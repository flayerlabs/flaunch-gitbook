---
icon: sparkles
---

# What's new in 0.9.1 and 0.9.2

Both releases add to 0.9.0 without changing any route or field it shipped. A game on 0.9.0 keeps
working against today's gates: upgrade when you want what is below.

## 0.9.1: the coin's picture reaches your game

Released 2026-10-15.

- `room.launch.current().imageUrl` now carries the coin's picture. Before this it was always
  `null`, because the gate never read it and your game's page could not have loaded it anyway.
- The gate reads the coin's art from its metadata, checks it (PNG, JPEG, GIF or WebP, at most
  5 MiB and 4096 px a side, fetched only from public addresses), and serves the bytes at
  `GET /rounds/:id/coin-image`. The client fetches them and hands your game a `blob:` URL.
- The launch is published with `imageUrl: null` first and again once the picture is in. Subscribe
  to `room.launch` to catch it. See [Showing the coin](game-mode-sdk.md#showing-the-coin).

## 0.9.2: games that hold points, and allocation in play

- `gamemode.json` takes an optional `buying`: `"as-earned"` (the default) or `"on-cash-out"`. A
  game that holds points until the player acts in the game (a blimp to shoot, an extraction)
  declares `"on-cash-out"`, and flaunch.gg shows no Auto buy switch for it. Rules games set it from
  `src/game/config.ts` with `export const buying = 'on-cash-out'`.
- `room.economy.reportInPlay(units)` tells the page what your game is holding and has not handed to
  the gate yet. flaunch.gg draws it on the bar under the game as **In play**, beside "Ready to buy".
  It is never spendable. See
  [Games that hold points until the player cashes out](game-mode-sdk.md#games-that-hold-points-until-the-player-cashes-out).
- The economy frame between your game and the page now carries `earned`, `spent`, the round's
  `cap` and `inPlay` beside `available`. A malformed optional figure is dropped instead of
  rejecting the frame.

## Upgrade

Move all four packages together:

```sh
pnpm add @flayerlabs/gamemode-client@0.9.2 @flayerlabs/gamemode-spec@0.9.2
pnpm add -D @flayerlabs/gamemode-cli@0.9.2
```

Rebuild, zip and upload as before. A game that wants `buying` adds it to `gamemode.json` (or to
`src/game/config.ts` for a rules game) in the same upload.
