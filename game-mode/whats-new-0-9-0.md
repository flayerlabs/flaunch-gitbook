---
icon: sparkles
---

# What's new in 0.9.0

Released 2026-10-13. From this release Flaunch runs the Game Mode gate for you: one gate per chain
serves every game. You ship a static build and a small manifest, `gamemode.json`, and upload it in
the developer dashboard at `https://flaunch.gg/game-mode/dashboard`. You no longer deploy a gate,
hold a signing key, run a database, list allowed origins or set up Turnstile. Games and game
servers built on 0.8.0 keep working: every gate route keeps its shape.

## What's new

- Flaunch runs the gate. Each chain has one platform gate that holds wallets, sessions, points and
  spend ceilings for every game on it.
- `gamemode bundle`. A new CLI command writes `gamemode.rules.js` (your rules) and `gamemode.json`
  (your manifest) into `dist/`. A new project's `pnpm build` runs it for you.
- Rules run inside Flaunch's gate. For a rules game, the gate runs your rules module in an isolated
  sandbox for every round.
- Awards clamp instead of failing. When a player's award would cross their ceiling, the gate pays
  what headroom is left and tells you how much landed (`credited`) and how much is left
  (`remainingPoints`). Your server can post raw scores.
- Anti-cheat is on for every game. Flaunch sets it up on the platform gate: a signed check that
  each wallet belongs to one Flaunch account, Turnstile at sign-in, limits on wallets per
  connection, and removals from a round with waiting periods that grow for repeat offenders.
- Tools for game servers. Your server can report scoring windows and input features, read a
  baseline of normal play for your game, and remove a player from a round. `createScoreRateMonitor`
  flags scoring that no honest player could manage.
- New projects are smaller. `gamemode new` no longer creates `src/gate.ts`, `.env.example` or
  `railpack.json`, and adds `src/game/config.ts`, where the round's config and length are stated
  once.

## If you ship a rules game

Your scoring is a pure `GameModule`, and the browser sends actions.

1. Upgrade `@flayerlabs/gamemode-spec`, `@flayerlabs/gamemode-client` and
   `@flayerlabs/gamemode-cli` to 0.9.0.
2. Add `src/game/config.ts` exporting `config` and `roundDurationMs`.
3. Make your build `vite build && gamemode bundle`.
4. Delete your gate: `src/gate.ts`, `.env.example`, `railpack.json` and the
   `@flayerlabs/gamemode-gate` dependency.
5. Run `pnpm build`, zip the contents of `dist/` and upload the ZIP.

The dashboard checks the manifest and shows any problem word for word. Your browser code does not
change, and players see no difference.

## If you run your own game server (REND-style)

Your server runs gameplay and decides scores. Keep it, and keep every call it makes to the gate.

1. Point your server at the platform gate for the chain instead of your own gate.
2. Use the award token from the developer dashboard as the bearer. Keep it on the server.
3. Add `gamemode.json` to your ZIP with `"tier": "server"`, `maxPointsPerPlayer`,
   `roundDurationMs` and `server.origins` (your server's HTTPS origins, at most four).
4. If your game reads its server from the gate's `/config`, read `gameServerOrigins` from
   `GET /rounds/:id` instead.
5. Delete your gate service, its database, its signing key, and its Turnstile, IP reputation and
   proxy settings.
6. Handle the new receipt: tell the player `credited`, treat `award.limit_exceeded` as "already at
   the ceiling", and retry `503 gate.unavailable` with the same event ID.

You can drop any code that tracked point caps: the gate clamps for you, including the smaller cap a
player's account may have.

## If you run your own gate

Running `@flayerlabs/gamemode-gate` yourself is still supported.

1. Upgrade all four packages to 0.9.0 together and redeploy.
2. The database migration runs once on start. `award_events` gains a nullable `credited` column.
3. Your game server sees the clamped award receipts described above.
4. IP blocks, boots and cooldowns behave as before on a gate that serves one game.
5. `PLATFORM_API_URL`, `PLATFORM_API_SECRET` and `PLATFORM_MAX_POINTS_PER_PLAYER` are only for the
   platform gate. You do not need them.

## Upgrade checklist

- [ ] All `@flayerlabs/gamemode-*` packages you use are on 0.9.0.
- [ ] Rules game: `src/game/config.ts` exists and `pnpm build` writes `dist/gamemode.json` and
  `dist/gamemode.rules.js`.
- [ ] Server game: `gamemode.json` is at the top of the ZIP with `"tier": "server"` and your
  server origins.
- [ ] Server game: your server uses the platform gate origin and the dashboard award token.
- [ ] Server game: you read `credited` from receipts and retry `503 gate.unavailable`.
- [ ] Your own gate, database, signing key and Turnstile keys are shut down, unless you
  deliberately run your own gate.
- [ ] The ZIP is uploaded at `https://flaunch.gg/game-mode/dashboard` and a test round plays end to
  end on Base Sepolia.

## Read more

* [Game Mode SDK](game-mode-sdk.md): the full walkthrough, the `gamemode.json` fields and the server-game path.
* [Build a game](build-a-game.md).
* Every `@flayerlabs/gamemode-*` package on npm ships `CHANGELOG.md` (every change in this release) and `MIGRATING.md` (the step-by-step checklist for each kind of game, with notes for porting a server game like REND). Open the package on npm, for example [@flayerlabs/gamemode-client](https://www.npmjs.com/package/@flayerlabs/gamemode-client?activeTab=code), or read them from `node_modules/@flayerlabs/gamemode-client/` after installing.
* If you still need to run your own gate, see [Advanced: running your own gate](game-mode-sdk.md#advanced-running-your-own-gate).
