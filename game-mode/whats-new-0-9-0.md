---
icon: sparkles
---

# What's new in 0.9.0

Game Mode SDK 0.9.0 moves the server side of every game onto Flaunch. Flaunch now runs one Game Mode gate per chain, and every game on that chain plays through it.

## For new games

You ship a static build and a manifest. You no longer run a gate, hold a signing key, run a database, declare allowed origins or set up Turnstile.

* `pnpm build` in a scaffolded project now runs `gamemode bundle`, which writes `gamemode.json` and, for a rules game, `gamemode.rules.js` into `dist/` beside `index.html`.
* Zip `dist/` and upload it at [flaunch.gg/game-mode/dashboard](https://flaunch.gg/game-mode/dashboard). It is playable on Base Sepolia straight away; mainnets open after review.
* Rules games: your rules module runs inside Flaunch's gate, in a sandbox.
* Server games: your own server keeps owning gameplay and posts raw scores to Flaunch's gate with an award token issued by the dashboard. The gate clamps each award to the player's ceiling, so your server no longer tracks caps.
* Point caps, one-wallet-per-account player notes, boots and cooldowns are enforced by Flaunch's gate for every game.

The full walkthrough is on the [Game Mode SDK](game-mode-sdk.md#ship-it) page.

## For games already on 0.8.0

0.8.0 games keep working. A build uploaded without a `gamemode.json` is still treated as a game with its own gate.

To move onto Flaunch's gate:

* **Rules game:** upgrade the SDK packages to 0.9.0, run `pnpm build` (or add `gamemode bundle` after your own build), and upload the new ZIP. `connectHost()` and `joinRoom()` are unchanged; the gate URL the game is handed is now Flaunch's.
* **Server game:** add a `gamemode.json` with `"tier": "server"`, point your server's `/internal/*` calls at Flaunch's gate, switch the bearer to the award token from the dashboard, and retire your own gate. Treat `503 gate.unavailable` as retryable.

The SDK repository has the details:

* [MIGRATING.md](https://github.com/flayerlabs/gamemode-sdk/blob/main/MIGRATING.md): the steps for each kind of game
* [CHANGELOG.md](https://github.com/flayerlabs/gamemode-sdk/blob/main/CHANGELOG.md): every change in the release

If you still need to run your own gate, see [Advanced: running your own gate](game-mode-sdk.md#advanced-running-your-own-gate).
