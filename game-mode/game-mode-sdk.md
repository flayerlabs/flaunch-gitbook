---
icon: gear-code
---

# Game Mode SDK

Build a game that other people's coin launches happen inside.

You are not launching a token, and your game does not get a coin. Coins are launched by creators on Flaunch, and a creator who launches in Game Mode picks a game to be the entrance: while the launch window is open, playing that game is the only way to earn the right to buy. Your game is the launch's distribution mechanism — a fairer and more fun one than an open pool, where the first block goes to whoever runs the fastest bot.

Players play, the game awards them points, and points become an allowance they can spend on the coin during the launch window. You write the game. Flaunch handles the wallet, the chain and the money.

### The two pieces

A Game Mode game has two parts, and only one of them is yours to worry about at the start:

* **The game** — a browser game, built to static files, zipped and uploaded at [flaunch.gg/game-mode/create](https://flaunch.gg/game-mode/create). Hosting is included — you upload the ZIP and we serve it, the way itch.io does. For the [Game Mode Hackathon](https://flaunch.gg/game-mode/hackathon), this is the whole submission.
* **The gate** — the server side of your game: it runs your game's rules, keeps score, and signs each player's spending allowance. This is the part that is unique to Game Mode, and **you do not need it to submit a game**: a game built on the SDK's `createMockRoom` runs a complete round in the browser with no server at all. The gate enters the picture when a real launch runs through your game — see [Running a server](#running-a-server).

If you have deployed to itch.io before: the ZIP upload is that. Everything about signers, gates and servers further down this page is the second piece, and it can wait.

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

### Already have a game? Port it

You do not have to start from the scaffold. If a browser game already exists — yours, or any permissively-licensed open-source game — the `port-game-mode` AI skill turns porting it into a checklist your coding agent can run:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill port-game-mode
```

The skill carries the whole route: licensing and provenance gates, a scoring economy bounded by the game's own rules, the room wiring for coin metadata, the live chart and buying, the launch framing players expect (tutorial, practice lobby, round timer with an end-of-round summary, leaderboard, the coin's art inside the game world), a headless QA harness that proves the loop, and the ZIP you upload. It is built on the same SDK surfaces documented on this page — nothing about a ported game is special once it ships.

{% hint style="info" %}
Building from scratch instead? The `build-game-mode` skill ships inside the `@flayerlabs/gamemode-cli` package and covers rules authoring, testing and the trust boundary in depth.
{% endhint %}

### How a game is structured

Your game is one file of pure functions called a rules module. It runs on the server, which is what stops a player editing their score in the browser.

```ts
import { defineGame } from '@flayerlabs/gamemode-spec'

export const rules = defineGame<Config, State, Event, Action, PublicView, PlayerView>({
  id: 'my-game',

  parseAction(input) { ... },              // reject anything malformed
  initRound(config, seed, window) { ... },  // the starting state
  decide(state, command) { ... },           // what happens, and who earns points
  evolve(state, event) { ... },             // apply one event
  publicView(state) { ... },                // what everyone sees
  playerView(state, player) { ... },        // what one player sees
  nextWakeAt(state) { ... },                // when the game needs waking
  rewardBounds(config) { ... },             // the most one player can earn
})
```

`defineGame` exists for type inference — it does nothing at runtime, and saves you naming six type arguments by hand everywhere the module is used.

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

const room = createMockRoom(rules, { config })

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

const room = createMockRoom(rules, { config, platform: { market: replayMarket(BUSY_LAUNCH) } })
```

`BUSY_LAUNCH` has a price spike in it on purpose. A chart that looks right on flat data and wrong on a spike is the usual result of building against nothing.

### Testing

Test the rules module directly. No server, no browser, no mocks. The spec package ships a round driver that runs your rules the way the server does — send commands, move time forward, read the result:

```ts
import { Round } from '@flayerlabs/gamemode-spec/round'

const round = Round.start(rules, config, 42, { opensAt: 1_000_000, closesAt: 1_300_000 })
round.send({ kind: 'action', player: '0xabc', action: { choice: 'a' }, at: 1_001_000 })
round.advanceTo(1_012_000) // time only moves when you say so

expect(round.pointsFor('0xabc')).toBe(100)
expect(round.playerView('0xabc').wasRight).toBe(true)
```

Because rules are pure, the same inputs always give the same answer. If a test passes here, it passes on the server. The scaffold's `test/rules.test.ts` starts you off, including a check that secrets stay out of `publicView`.

### Running a server

This is the second of [the two pieces](#the-two-pieces), and it is optional until a real launch runs through your game — a hackathon submission does not need it.

When a launch does go live, the game needs a gate: a small server that runs your rules, keeps score and signs the allowance a player spends. One gate serves one game, and it is your game's only server — the same process that runs the simulation is the one that signs.

To try one on your laptop:

```ts
import { createDemoGate } from '@flayerlabs/gamemode-gate'

const { app } = await createDemoGate({ game: rules, config })
await app.listen({ port: 4000 })
```

This is a real gate with a real database and real signatures. Only the chain is faked: purchases are recorded rather than submitted. It needs Postgres, and one Docker command matches its default connection string:

```bash
docker run -d --rm --name gamemode-pg -e POSTGRES_PASSWORD=gamemode \
  -e POSTGRES_DB=gamemode -p 55439:5432 postgres:16-alpine
```

(Or point `DATABASE_URL` at a Postgres you already have.)

For a live game, `startGate()` is the paved road: your rules module and an environment in, a verified, announced, running gate out.

```ts
import { startGate } from '@flayerlabs/gamemode-gate'
import { rules, config } from './src/game/rules.js'

const { port, signer } = await startGate(rules, config)
```

It reads its configuration from the environment (`CHAIN_ID`, `RPC_URL`, `SIGNER_PRIVATE_KEY`, `DATABASE_URL`, `SESSION_SECRET`, `SIGN_IN_DOMAIN`, `GATE_ORIGIN`, `ALLOWED_ORIGINS` — the full contract is in the [`@flayerlabs/gamemode-gate` README](https://www.npmjs.com/package/@flayerlabs/gamemode-gate) and the DEPLOY.md that ships in the package), refuses loudly on anything misconfigured, runs its own migrations, and serves the `/config` endpoint the launch form reads — a gate that boots is a gate a launch can actually be written against. The `signer` it prints is the address launches will trust; generate its key fresh and never share it, least of all with us.

One rule that catches people: `ALLOWED_ORIGINS` must name both browser callers — your hosted game's origin (`https://*.games.moongate.com`; the hostname changes on every upload, so use the wildcard) and the flaunch page origins, because the coin page itself calls your gate to adopt the round. Leave the page out and every launched coin fails on a CORS error.

Everything `startGate` assembles is also exported for wiring the gate yourself — `createGate`, `PayloadSigner`, `Discovery`, `Settlement`, `Sessions`, `Claims` and `migrate` — for the rare setup the paved road cannot express; the contract addresses `Discovery` verifies launches against are listed under [Spend-Gated Launches](../developer-resources/spend-gate/README.md). Two options worth knowing about:

* **`admit`** decides who the gate will talk to — one hook, consulted before a session is issued and before allowance is committed. `createTurnstileAdmit` ships in the gate package if Cloudflare Turnstile is your human check; anything else is a function returning ok or a refusal written for the player to read.
* **`config`** can be a function of the verified launch instead of a constant, resolved once per round — for a game whose rules differ from coin to coin.

### Bring your own multiplayer server

Everything above assumes your rules run inside the gate. A game with its own authoritative realtime server — custom netcode, region fleets, anything where a 100ms round trip is gameplay — keeps that server, and the platform does not host or relay its traffic. The integration stays light:

* Your gate uses `createGameServerGate()` instead of running rules: your server reports scores through an authenticated award route, and the gate still owns sessions, points, allowances and settlement. This path serves `/config` only when you pass `announce` — the launch form refuses a gate without it.
* The submission form's game server addresses field lists your server origins (up to four, exact https, no wildcards) — that list is what your hosted game's security policy permits, alongside your gate. The same list goes into `createGameServerGate()` as `gameServerOrigins`, and players prove their wallet to your server with a short-lived join ticket scoped to one of those exact origins.
* Region rotation belongs behind stable hostnames — the reviewed list is not meant to churn.

The full walkthrough — env semantics, the announce block, ticket verification, smoke tests — is the `run-a-game-server` AI skill:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill run-a-game-server
```

with the same material in the [`@flayerlabs/gamemode-gate` README](https://www.npmjs.com/package/@flayerlabs/gamemode-gate). Talk to us on [Discord](https://discord.gg/PcSmznqqqb) before building this tier — it is the newest path and we would rather walk it with you.

### Going live on Flaunch

1. Build your game to static files.
2. Upload them as a ZIP at [flaunch.gg/game-mode/create](https://flaunch.gg/game-mode/create). You get back a version that cannot change, so what was reviewed is what players get.
3. Games are reviewed, and the best join the official Game Mode library.
4. A creator launches a coin through your game. The launch names the gate's signer and a per-wallet spending cap, and your game appears on the coin's page on flaunch.gg.

Step 4 is not yours: the coin belongs to whoever launches it, and a library game can carry any number of launches. What you get from each one is a share of it — games in the official library earn 5% of the trading fees of every coin that launches through them.

If your game runs its own gate rather than the demo one, this is the point where it goes up somewhere public — [Running a server](#running-a-server) lists what that takes, and the submission form has a field for its address (plus, for a game with its own multiplayer servers, a field for their addresses — see [Bring your own multiplayer server](#bring-your-own-multiplayer-server)). Talk to us on [Discord](https://discord.gg/PcSmznqqqb) before you get here — we would rather help wire it than have you guess.

### Limits that protect players

The chain enforces a spending cap for each wallet on your pool. Your server cannot exceed it, whatever your rules say. If a perfect round would earn more than the cap allows, the gate refuses to start rather than showing players a reward they cannot spend.

Your signing key only ever affects your own launches.

### Packages

| Package                       | Use it for                                  |
| ----------------------------- | ------------------------------------------- |
| `@flayerlabs/gamemode-spec`   | the types both sides share, and the round driver your tests use |
| `@flayerlabs/gamemode-client` | browser code: the room, the mock, the embed |
| `@flayerlabs/gamemode-gate`   | the server                                  |
| `@flayerlabs/gamemode-cli`    | `gamemode new` and `gamemode check`         |

Your game's browser bundle uses `client` and `spec`. Your server uses `gate` and `spec`. Keeping them apart is what stops a database driver ending up in a browser.

### Commands

| Command                     | What it does                                    |
| --------------------------- | ----------------------------------------------- |
| `npx gamemode new <name>`   | create a game you can play immediately          |
| `npx gamemode check [file]` | check your rules are pure                       |
| `pnpm dev`                  | play it, with no server and no wallet           |
| `pnpm test`                 | check the rules are pure, then run your tests   |
| `pnpm typecheck`            | check the types                                 |
