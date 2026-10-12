---
icon: gear-code
---

# Game Mode SDK

Build a game that other people's coin launches happen inside.

You are not launching a token, and your game does not get a coin. Coins are launched by creators on Flaunch, and a creator who launches in Game Mode picks a game to be the entrance: while the launch window is open, playing that game is the only way to earn the right to buy. Your game is the launch's distribution mechanism. It is fairer and more fun than an open pool, where the first block goes to whoever runs the fastest bot.

Players play, the game awards them points, and points become an allowance they can spend on the coin during the launch window. You write the game. Flaunch handles the wallet, the chain, the money and the server that keeps score.

{% hint style="info" %}
This page describes SDK 0.9.0. If you built a game on 0.8.0 or earlier, read [What's new in 0.9.0](whats-new-0-9-0.md) first.
{% endhint %}

## How shipping works

From SDK 0.9.0, Flaunch runs one Game Mode gate per chain, and every game on that chain plays through it. You ship a static build and a small manifest. You do not run a gate, hold a signing key, run a database, declare origins or set up Turnstile.

1. **Build** your game with the SDK and play it locally against a mock room.
2. **Run `pnpm build`.** It builds the game into `dist/` and writes `gamemode.json` (and, for a rules game, `gamemode.rules.js`) beside `index.html`.
3. **Zip `dist/`** and **upload** it at [flaunch.gg/game-mode/dashboard](https://flaunch.gg/game-mode/dashboard).
4. **Test it on Base Sepolia.** An upload is playable there straight away.
5. **Submit for mainnet.** Mainnet networks open after review.

Your scoring runs in one of two places, and `gamemode.json` says which:

* **Rules games** (`"tier": "rules"`). Your rules module runs inside Flaunch's gate, in a sandbox. The browser sends actions, and the gate is the only thing that credits points. Most games are this kind.
* **Server games** (`"tier": "server"`). Your own server owns gameplay, for example real-time multiplayer with its own netcode. It keeps running where it runs today and posts raw scores to Flaunch's gate with an award token issued by the dashboard. See [Games with their own server](#games-with-their-own-server).

## What you need

* Node 20 or later
* pnpm
* a code editor, or an AI coding agent

You do not need to know anything about blockchains. Your game never sees a wallet, a key or a transaction.

## Make a game

1. Create a project you can play straight away:

   ```bash
   pnpm dlx @flayerlabs/gamemode-cli new my-game
   cd my-game
   pnpm install
   ```
2. Open `src/game/rules.ts` and write your game. Put the round's config and length in `src/game/config.ts`.
3. Run `pnpm dev` to play it in a browser. No server, no wallet, no chain.
4. Run `pnpm test` to check it still works.
5. Run `pnpm build` when you are ready to upload. See [Ship it](#ship-it).

The scaffold gives you a complete working game with passing tests. Change it rather than starting from an empty file.

To add the SDK to an existing project instead:

```bash
npm install @flayerlabs/gamemode-client @flayerlabs/gamemode-spec
npm install --save-dev @flayerlabs/gamemode-cli
```

## Already have a game? Port it

You do not have to start from the scaffold. If a browser game already exists, yours or any permissively licensed open-source game, the `port-game-mode` AI skill turns porting it into a checklist your coding agent can run:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill port-game-mode
```

A port is the one case with a license requirement: the upstream game must carry a permissive open-source license, because that is what gives you the right to redistribute a modified version of someone else's work. Porting your own game has no such requirement. See [Your game stays yours](#your-game-stays-yours).

The skill carries the whole route: licensing and provenance checks, a scoring economy bounded by the game's own rules, the room wiring for coin metadata, the live chart and buying, the launch framing players expect (tutorial, practice lobby, round timer with an end-of-round summary, leaderboard, the coin's art inside the game world), a headless QA harness that proves the loop, and the ZIP you upload. It is built on the same SDK surfaces documented on this page, so nothing about a ported game is special once it ships.

{% hint style="info" %}
Building from scratch instead? The `build-game-mode` skill ships inside the `@flayerlabs/gamemode-cli` package and covers rules authoring, testing and the trust boundary in depth.
{% endhint %}

## How a game is structured

Your game's scoring is one file of pure functions called a rules module. It runs inside Flaunch's gate, which is what stops a player editing their score in the browser.

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

`defineGame` exists for type inference. It does nothing at runtime, and saves you naming six type arguments by hand everywhere the module is used.

### Deciding and evolving

`decide` answers "what should happen?" and `evolve` answers "what does the state look like afterwards?". Keeping them apart is what lets the gate replay a round exactly, and lets you test a game without running a server.

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

### Rules must be pure

Your rules module must not use:

* `Date.now()` or any clock
* `Math.random()`
* network calls, files, or databases
* variables that change between calls

Time arrives on every command as `command.at`. Randomness comes from the `seed` given to `initRound`. Run `pnpm check` (or `npx gamemode check`) and it will tell you if you have broken this. `gamemode bundle` runs the same check on your rules file and on every local file it imports, and refuses to build an impure module.

This matters for two reasons. The gate replays rounds, and a rules module that reads the clock gives a different answer on replay. And the gate runs one copy of your module for every round of your game at once, so state kept between calls would leak from one round into another.

### Keeping answers secret

`publicView` is sent to everyone, including spectators. If a value must stay hidden, leave it out of the type. Do not return it and hide it in the interface: a quiz that puts the correct answer in `publicView` has already leaked it, whatever the screen shows.

## What your game talks to

Your browser code talks to one object, the room.

```ts
import { createMockRoom } from '@flayerlabs/gamemode-client'

const room = createMockRoom(rules, { config })

room.subscribe(({ publicView, playerView }) => render(publicView, playerView))
await room.send({ kind: 'answer', choice: 'a' })
room.now()
```

On Flaunch, the scaffold swaps the mock for the real room: `connectHost()` reads the launch context the coin page hands the game, and `joinRoom()` joins the round on the gate it names. Your render code does not change.

The room also carries everything the platform provides:

| Capability        | What it gives you                                                |
| ----------------- | ---------------------------------------------------------------- |
| `room.launch`     | coin, pool and the round's timings                               |
| `room.connection` | `connecting`, `connected`, `reconnecting`, `superseded`, `ended` |
| `room.economy`    | what a player earned, has spent, and has available               |
| `room.market`     | the coin's price history, trades and current market cap          |
| `room.identity`   | names and avatars for wallet addresses                           |
| `room.presence`   | how many players are connected                                   |
| `room.social`     | reactions                                                        |

Every `subscribe` gives you the current value immediately, then updates.

Use `room.now()` for anything a player sees counting down. It corrects for a wrong clock on their device. `Date.now()` in a game is a bug: two players with different device clocks will see different games.

### Buying

```ts
const { available } = room.economy.current()
const result = await room.economy.buy(available)
```

Your game asks. The page around it builds the transaction and asks the player to approve it. Your game never constructs a transaction and cannot, which is why a bug in your game cannot lose anyone money. A player who closes the wallet prompt loses nothing and can try again at once.

### Where market data comes from

`room.market` carries the coin's price history and its current market cap in USD, and the source is the **coin page**, not the gate. The gate knows the round's ledger (who earned what, who spent what) and nothing at all about the coin's price. So the page pushes its own live feed into the game: the same feed drawing the chart beside you, which is why the two never disagree on a number.

For a rules game, `joinRoom` wires this for you. For a server game, `joinEconomy` needs telling once. Miss it and `market.status` stays `unavailable` for the whole round:

```ts
const embedded = await connectHost()

const gameMode = await joinEconomy({
  gateUrl: embedded.context.gateUrl,
  roundId: embedded.context.roundId,
  host: embedded.host,
  platform: { market: embedded.market },
})
```

`market.status` is the cue for what you are looking at: `unavailable` means the page has no feed and the honest thing to draw is an empty chart, not a spinner that never resolves.

### Building a chart with no chain

On your laptop there is no page and no chain, so the chart would be empty. Use the replay fixture instead:

```ts
import { createMockRoom, replayMarket, BUSY_LAUNCH } from '@flayerlabs/gamemode-client'

const room = createMockRoom(rules, { config, platform: { market: replayMarket(BUSY_LAUNCH) } })
```

`BUSY_LAUNCH` has a price spike in it on purpose. A chart that looks right on flat data and wrong on a spike is the usual result of building against nothing.

One thing the fixture will not catch: show `marketCapUsd` as it arrives. It is already the coin's current market cap, so scaling it by the round's price move counts that move twice, and puts a number in your game that contradicts the one on the page.

## Testing

Test the rules module directly. No server, no browser, no mocks. The spec package ships a round driver that runs your rules the way the gate does: send commands, move time forward, read the result.

```ts
import { Round } from '@flayerlabs/gamemode-spec/round'

const round = Round.start(rules, config, 42, { opensAt: 1_000_000, closesAt: 1_300_000 })
round.send({ kind: 'action', player: '0xabc', action: { choice: 'a' }, at: 1_001_000 })
round.advanceTo(1_012_000) // time only moves when you say so

expect(round.pointsFor('0xabc')).toBe(100)
expect(round.playerView('0xabc').wasRight).toBe(true)
```

Because rules are pure, the same inputs always give the same answer. If a test passes here, it passes on the gate. The scaffold's `test/rules.test.ts` starts you off, including a check that secrets stay out of `publicView`.

## Ship it

### Build

```bash
pnpm build
```

In a scaffolded project this runs `vite build && gamemode bundle`. The game is built into `dist/`, and `gamemode bundle` adds two files beside `index.html`:

* `gamemode.rules.js`: your rules module bundled as a classic script (an IIFE) that the gate runs in a sandbox. It is at most 2 MiB, so keep assets and UI code out of it.
* `gamemode.json`: the manifest. `gamemode bundle` writes it from `src/game/config.ts` (which exports `config` and `roundDurationMs`) and from your module's `rewardBounds(config)`.

If you do not use the scaffold, run `npx gamemode bundle` after your own build. It reads `src/game/rules.ts` and `src/game/config.ts` and writes to `dist/`.

### `gamemode.json`

The manifest sits at the root of the ZIP, next to `index.html`. Flaunch validates it on upload and shows any refusal to you word for word.

A rules game:

```json
{
  "tier": "rules",
  "maxPointsPerPlayer": 1000,
  "roundDurationMs": 180000,
  "rules": { "module": "gamemode.rules.js", "config": { "targets": 12 }, "maxActionsPerMinute": 600 }
}
```

A server game:

```json
{
  "tier": "server",
  "maxPointsPerPlayer": 1000,
  "roundDurationMs": 180000,
  "server": { "origins": ["https://coop.quiz.example"] }
}
```

<table><thead><tr><th width="250">Field</th><th>Meaning</th></tr></thead><tbody><tr><td><code>tier</code></td><td>Required. <code>"rules"</code> or <code>"server"</code>.</td></tr><tr><td><code>maxPointsPerPlayer</code></td><td>Required. A positive whole number: the most points one player can earn in a round. A rules module's own <code>rewardBounds</code> can lower the round's ceiling but never raise it past this value.</td></tr><tr><td><code>roundDurationMs</code></td><td>Required. How long a launch's round lasts, in milliseconds. At least 60000 (one minute) and at most seven days.</td></tr><tr><td><code>rules.module</code></td><td>Rules games only. The name of the rules file at the ZIP root, at most 2 MiB. It must be a classic script that assigns your <code>GameModule</code> to <code>globalThis.gamemodeRules</code>, and must pass <code>gamemode check</code>. An ES module is refused. <code>gamemode bundle</code> writes <code>gamemode.rules.js</code> in this form.</td></tr><tr><td><code>rules.config</code></td><td>Rules games only, optional. A JSON object handed to your rules as their config.</td></tr><tr><td><code>rules.maxActionsPerMinute</code></td><td>Rules games only, optional. A positive whole number: the highest action rate per player your game expects.</td></tr><tr><td><code>server.origins</code></td><td>Server games only, optional. Up to 4 exact HTTPS origins your browser game connects to, such as your realtime server. No wildcards, paths or plain HTTP. Flaunch's gate is always allowed and is never declared.</td></tr></tbody></table>

A rules game has no `server` block and a server game has no `rules` block. Any other field is refused.

### Zip and upload

Zip the **contents** of `dist/`, so `index.html` is at the top level of the archive. Zipping the `dist` folder itself also works, as long as the archive holds that one folder and `index.html` is directly inside it. `gamemode.json` and the rules file are read from the same place.

Upload the ZIP at [flaunch.gg/game-mode/dashboard](https://flaunch.gg/game-mode/dashboard) with a name, a category and a short description. Hosting is included: you upload the ZIP and Flaunch serves it, the way itch.io does.

Upload limits:

* 20 MiB for the compressed ZIP
* 50 MiB unpacked
* `index.html` at the top level, or inside a single top-level folder

macOS `__MACOSX/` folders and `.DS_Store` files are dropped before these checks. Large engine exports (an uncompressed Godot `.pck` or a Unity `Build/*.data`) are the builds most likely to hit the limits, so compress assets before you zip.

Each upload becomes a build that cannot change, so what was reviewed is what players get.

### Test on Base Sepolia

An upload is playable on Base Sepolia straight away, before any review. Use it to run a real round end to end: the coin page, the wallet, buying, the leaderboard and settlement, on testnet funds. Fix what you find, rebuild, and upload again.

### Submit for mainnet

Mainnet networks open after Flaunch reviews the build. The best games join the official Game Mode library, and a library game earns 5% of the trading fees of every coin that launches through it.

Once a revision is approved, your game has a stable play URL, `https://<your-game-slug>.games.moongate.com/`, that always serves the approved build. Draft uploads do not move it. Each individual upload also has its own address, which changes on every upload because it is derived from the build's contents.

Uploading a new build creates a new revision for review. Your previous approved revision stays live while the new one is reviewed, and a rejection does not remove it. Coins already launched through your game keep the exact build they launched with.

A creator then launches a coin through your game and it appears on the coin's page on flaunch.gg. The coin belongs to whoever launches it, and a library game can carry any number of launches.

## Games with their own server

A game with its own authoritative realtime server (custom netcode, region fleets, anything where a 100ms round trip is gameplay) keeps that server. Flaunch does not host or relay its traffic, and you still do not run a gate. Flaunch's gate owns wallet sessions, points, allowances and settlement; your server decides scores and reports them.

1. **Declare the server in `gamemode.json`.** Set `"tier": "server"` and list your server's exact HTTPS origins in `server.origins` (up to 4). That list is what your hosted game's security policy lets the browser reach. Put region rotation behind stable hostnames, because the list is fixed for each build.
2. **Get an award token from the dashboard.** The token has the form `gmt_<slug>_<64 hex characters>` and is shown once. Keep it on your server only. Rotating it issues a new one. Start sending the new token at once: your first request with it retires the old one, and the old one stops working within about 90 seconds in any case.
3. **Connect the browser.** Use `joinEconomy()` instead of `joinRoom()`. It signs the player in to Flaunch's gate and gives you `gameMode.joinTicket(origin)`, a short-lived proof of the player's wallet scoped to one of your declared origins. Send it as the first message on your socket, never in a URL.
4. **Check the ticket on your server.** Post it to the gate's `POST /internal/rounds/:id/tickets/consume` with your award token as the bearer. The gate checks it and accepts each ticket once. If you verify locally with `verifyPlayerJoinTicket`, pass your award token; it also accepts tickets Flaunch's gate signs.
5. **Post raw scores.** Send `POST /internal/rounds/:id/awards` with `{ eventId, player, points }` and your award token as the bearer. Post the score your server computed. The gate clamps each award to the player's ceiling and the receipt says how much was `credited`, so your server never tracks caps. Retry with the same `eventId` after a timeout; it is never paid twice.

Your browser code receives the gate's URL for the round as `embedded.context.gateUrl`. Your server calls the same gate. Treat `503 { "refuse": "gate.unavailable" }` as retryable.

The full walkthrough (ticket handling, award errors, score baselines and boots) is in the `run-a-game-server` AI skill and in the SDK's guides:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill run-a-game-server
```

Talk to us on [Discord](https://discord.gg/PcSmznqqqb) before building this tier. We would rather walk it with you.

## Limits that protect players

Flaunch's gate enforces these for every game, so you do not build them:

* **Point caps.** No player earns more than the round's `maxPointsPerPlayer`. Awards past it are clamped, not refused.
* **One wallet per account.** Every player signs in with a Flaunch player note that ties their game wallet to one account. An account's share of the cap follows it, whichever wallet it brings.
* **Boots and cooldowns.** A server game can boot a player from a round through the gate. A booted account gets no further tickets, awards or buys in that round, whichever wallet it brings. Repeat boots within 30 days escalate to a cooldown of a day, then a week, and cooldowns expire on their own.
* **Spending caps on chain.** The pool enforces a per-wallet spending cap and a signed spend ceiling on every buy. Your game cannot exceed them, whatever its rules say.

## Your game stays yours

You do not have to open-source a game you wrote to put it on Flaunch, and we do not ask for ownership of it. Your code, your art and your IP stay yours.

What we need is narrow: permission to host and serve your game inside Game Mode, and to show its name, tagline and cover art in the library and on the coin pages where it runs. Hosting a game means copying it to our servers and serving it to players, so some permission is unavoidable, but it stops there. It is not exclusive, gives no right to modify the game beyond the packaging needed to serve it, and makes no claim on anything you do with the game anywhere else.

One consequence worth knowing before you submit, because it cannot be undone afterwards: **a launch is permanent.** Each ZIP becomes a build that cannot change, and a coin launched through it embeds that exact build on its page forever. If you later withdraw your game from the library, we stop offering it for new launches, but coins already launched through it keep working, which means we keep serving that build to their players. Withdrawing is not deletion.

The open-source requirement you may have seen applies only to [porting someone else's game](#already-have-a-game-port-it). A permissive license there is how a port proves it has the right to redistribute the original, alongside the provenance file naming the upstream repo, author and commit. It has nothing to do with a game of your own.

{% hint style="info" %}
This section describes the position in plain terms; [Games You Submit](../terms/terms-of-service.md#games-you-submit) in the Terms of Service governs. If your game needs something different (a specific attribution, a license you are already bound by, an asset pack with its own terms), raise it on [Discord](https://discord.gg/PcSmznqqqb) before you submit rather than after.
{% endhint %}

## Linking out of your game

Your game runs inside a sandboxed frame on the coin page. It can open your site, Discord or X in a new tab: `target="_blank"` on a link, or `window.open` inside the click handler. Browsers refuse openers that run without a user gesture. The new tab is a normal page, not sandboxed. What the frame cannot do is move the coin page itself or submit forms to it: `window.top`, `location.assign()` and `target="_top"` are blocked, by design.

## Packages

| Package                       | Use it for                                                       |
| ----------------------------- | ---------------------------------------------------------------- |
| `@flayerlabs/gamemode-spec`   | the types both sides share, and the round driver your tests use  |
| `@flayerlabs/gamemode-client` | browser code: the room, the mock, the embed                      |
| `@flayerlabs/gamemode-cli`    | `gamemode new`, `gamemode check` and `gamemode bundle`           |
| `@flayerlabs/gamemode-gate`   | only for running your own gate, or verifying join tickets locally on a game server |

Your game's browser bundle uses `client` and `spec`. Keep all four packages at the same version, and rebuild and re-upload after upgrading them.

## Commands

| Command                     | What it does                                                         |
| --------------------------- | -------------------------------------------------------------------- |
| `gamemode new <name>`       | create a game you can play immediately                               |
| `gamemode check [file]`     | check your rules are pure                                            |
| `gamemode bundle`           | write `dist/gamemode.rules.js` and `dist/gamemode.json` for upload   |
| `pnpm dev`                  | play it, with no server and no wallet                                |
| `pnpm test`                 | check the rules are pure, then run your tests                        |
| `pnpm typecheck`            | check the types                                                      |
| `pnpm build`                | build the game and the rules bundle; zip `dist/` and upload it       |

## Advanced: running your own gate

Before 0.9.0, every game that hosted launches ran its own gate: a server with a signing key, a Postgres database, allowed origins and optional Turnstile, registered per network in the dashboard. That path still exists. Games already shipped on 0.8.0 keep working, and a ZIP without a `gamemode.json` is still treated as a game with its own gate.

You should not need it for a new game. If you do (for example, a launcher outside Flaunch that wants its own signer), the [`@flayerlabs/gamemode-gate` package](https://www.npmjs.com/package/@flayerlabs/gamemode-gate) covers `startGate()`, `createGameServerGate()`, the environment, admission and Turnstile in its README and `DEPLOY.md`. A self-run gate must sign against the version 2 spend-gated calculator for its chain, listed under [Spend-Gated Launches](../developer-resources/spend-gate/README.md#deployments).

To move a 0.8.0 game onto Flaunch's gate, follow [What's new in 0.9.0](whats-new-0-9-0.md) and the `MIGRATING.md` that ships in every `@flayerlabs/gamemode-*` package on npm.
