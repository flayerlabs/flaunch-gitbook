# Game Mode SDK

Build a game that players earn the right to buy a token in.

A Game Mode attaches to a Flaunch launch. Players play, the game awards them points, and points become an allowance they can spend on the coin during the launch window. You write the game. Flaunch handles the wallet, the chain and the money.

### What you need

* Node 20 or later
* pnpm
* Docker, only if you want to run a server locally
* a code editor, or an AI coding agent

You do not need to know anything about blockchains. Your game never sees a wallet, a key or a transaction.

### Install

```bash
npm install @flayerlabs/gamemode-client @flayerlabs/gamemode-spec
npm install --save-dev @flayerlabs/gamemode-cli
```

Add `@flayerlabs/gamemode-gate` as well if you are running your own server.

### Make a game in 4 steps

1. Run `npx gamemode new my-game` to create a project you can play straight away.
2. Open `src/game/rules.ts` and write your game.
3. Run `pnpm dev` to play it in a browser. No server, no wallet, no chain.
4. Run `pnpm test` to check it still works.

The scaffold gives you a complete working game with passing tests. Change it rather than starting from an empty file.

### How a game is structured

Your game is one file of pure functions called a rules module. It runs on the server, which is what stops a player editing their score in the browser.

```ts
export const game: GameModule<Config, State, Event, Action, PublicView, PlayerView> = {
  id: 'my-game',

  parseAction(input) { ... },              // reject anything malformed
  initRound(config, seed, window) { ... },  // the starting state
  decide(state, command) { ... },           // what happens, and who earns points
  evolve(state, event) { ... },             // apply one event
  publicView(state) { ... },                // what everyone sees
  playerView(state, player) { ... },        // what one player sees
  nextWakeAt(state) { ... },                // when the game needs waking
  rewardBounds(config) { ... },             // the most one player can earn
}
```

#### Deciding and evolving

`decide` answers "what should happen?" and `evolve` answers "what does the state look like afterwards?". Keeping them apart is what lets the server replay a round exactly, and lets you test a game without running a server.

`decide` returns events, and optionally awards:

```ts
decide(state, command) {
  if (command.kind !== 'action') return { events: [] }
  if (state.phase !== 'open') return { refuse: 'not-open' }

  return {
    events: [{ type: 'answered', player: command.player, choice: command.action.choice }],
    awards: [{ player: command.player, points: 100 }],
  }
}
```

You do not have to award points at the moment a player acts. A quiz can accept answers during a question and pay everyone at the reveal. Both use the same shape.

#### Rules must be pure

Your rules module must not use:

* `Date.now()` or any clock
* `Math.random()`
* network calls, files, or databases
* variables that change between calls

Time arrives on every command as `command.at`. Randomness comes from the `seed` given to `initRound`. Run `npx gamemode check` and it will tell you if you have broken this.

This matters because the server replays rounds. A rules module that reads the clock gives a different answer on replay, and a game that disagrees with itself cannot pay anyone reliably.

#### Keeping answers secret

`publicView` is sent to everyone, including spectators. If a value must stay hidden, leave it out of the type. Do not return it and hide it in the interface — a quiz that puts the correct answer in `publicView` has already leaked it, whatever the screen shows.

### What your game talks to

Your browser code talks to one object, the room.

```ts
import { createMockRoom } from '@flayerlabs/gamemode-client'

const room = createMockRoom(game, { config })

room.subscribe(({ publicView, playerView }) => render(publicView, playerView))
await room.send({ kind: 'answer', choice: 'a' })
room.now()
```

The room also carries everything the platform provides:

| Capability        | What it gives you                                                |
| ----------------- | ---------------------------------------------------------------- |
| `room.launch`     | coin, pool and the round's timings                               |
| `room.connection` | `connecting`, `connected`, `reconnecting`, `superseded`, `ended` |
| `room.economy`    | what a player earned, holds, and buy progress                    |
| `room.market`     | price history and trades                                         |
| `room.identity`   | names and avatars for wallet addresses                           |
| `room.presence`   | how many players are connected                                   |
| `room.social`     | reactions                                                        |

Every `subscribe` gives you the current value immediately, then updates.

Use `room.now()` for anything a player sees counting down. It corrects for a wrong clock on their device. `Date.now()` in a game is a bug: two players in different timezones will see different games.

#### Buying

```ts
const result = await room.economy.buy(maxSpendWei)
```

Your game asks. The page around it builds the transaction and asks the player to approve it. Your game never constructs a transaction and cannot, which is why a bug in your game cannot lose anyone money.

#### Building a chart with no chain

Market data comes from Flaunch. On your laptop there is none, so the chart would be empty. Use the replay fixture instead:

```ts
import { createMockRoom, replayMarket, BUSY_LAUNCH } from '@flayerlabs/gamemode-client'

const room = createMockRoom(game, { config, platform: { market: replayMarket(BUSY_LAUNCH) } })
```

`BUSY_LAUNCH` has a price spike in it on purpose. A chart that looks right on flat data and wrong on a spike is the usual result of building against nothing.

### Testing

Test the rules module directly. No server, no browser, no mocks.

```ts
const state = game.initRound(config, 1, window)
const decision = game.decide(state, { kind: 'action', player: '0xabc', action, at: 1_000 })

expect(decision).toEqual({
  events: [{ type: 'answered', player: '0xabc', choice: 'a' }],
  awards: [{ player: '0xabc', points: 100 }],
})
```

Because rules are pure, the same inputs always give the same answer. If a test passes here, it passes on the server.

### Running a server

Your game needs a gate: a small server that runs your rules, keeps score and signs the allowance a player spends.

To try one on your laptop:

```ts
import { createDemoGate } from '@flayerlabs/gamemode-gate'

const { app } = await createDemoGate({ game, config })
await app.listen({ port: 4000 })
```

This is a real gate with a real database and real signatures. Only the chain is faked: purchases are recorded rather than submitted. It needs Postgres, which the scaffold can start for you with Docker.

For a live game you run the same gate with your own signing key, a Postgres database and an RPC endpoint, on a public address with HTTPS and WebSockets.

### Going live on Flaunch

1. Build your game to static files.
2. Upload them as a ZIP. You get back a version that cannot change, so what was reviewed is what players get.
3. Run your gate somewhere public.
4. Launch your coin, naming your gate's signer and a per-wallet spending cap.
5. Your game appears on the coin's page on flaunch.gg.

Games are reviewed before they appear. Contact the Flaunch team to start that.

### Limits that protect players

The chain enforces a spending cap for each wallet on your pool. Your server cannot exceed it, whatever your rules say. If a perfect round would earn more than the cap allows, the gate refuses to start rather than showing players a reward they cannot spend.

Your signing key only ever affects your own launches.

### Packages

| Package                       | Use it for                                  |
| ----------------------------- | ------------------------------------------- |
| `@flayerlabs/gamemode-spec`   | the types both sides share                  |
| `@flayerlabs/gamemode-client` | browser code: the room, the mock, the embed |
| `@flayerlabs/gamemode-gate`   | the server                                  |
| `@flayerlabs/gamemode-cli`    | `gamemode new` and `gamemode check`         |

Your game's browser bundle uses `client` and `spec`. Your server uses `gate` and `spec`. Keeping them apart is what stops a database driver ending up in a browser.

### Commands

| Command                     | What it does                           |
| --------------------------- | -------------------------------------- |
| `npx gamemode new <name>`   | create a game you can play immediately |
| `npx gamemode check [file]` | check your rules are pure              |
| `pnpm dev`                  | play it, with no server and no wallet  |
| `pnpm test`                 | run your tests                         |
