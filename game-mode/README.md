---
icon: gamepad-modern
---

# Game Mode

A fair launch is meant to give everyone the same shot at a coin. In practice the first block goes to whoever has the best infrastructure. Bots watch the mempool, land their buys in the same second the pool opens, and sell into the people who arrived moments later. Technically nothing stopped you from buying; practically the good price was gone before you saw the coin.

Game Mode replaces that race with a **window**. For as long as the window is open, the only way to buy the coin is to earn the right to, by playing a game. Nobody buys before the window opens, and nobody buys more than they earned. When the window closes, the coin trades like any other Flaunch coin.

## How a round works

1. **A coin launches with a window.** The creator schedules the launch and picks the game that will guard it. Until the window opens, every swap against the pool reverts, and that's the pool's own rule rather than something the game server has to enforce.
2. **Players play.** Everyone in the round shares one window and one leaderboard. Scoring happens on the server, which re-runs every move from the player's inputs instead of trusting the browser.
3. **Points become spending power.** Your score converts to an ETH allowance. Score more, buy more, up to a per-wallet cap that applies to everyone equally.
4. **You buy what you earned.** Claiming your allowance produces a signed authorization for exactly that amount. The pool checks the signature and the amount on every swap.
5. **The window closes and the gate lifts.** The coin is now an ordinary coin, trading permissionlessly.

## What you can rely on

These hold because the pool enforces them, not because the game server is well behaved:

* **No buying before the window.** The launch's opening time is written into the pool, so there's no early access for anyone to be given or to buy.
* **No buying more than you earned.** Every authorization names a maximum spend, and the pool measures the swap's real ETH input against it.
* **No buying more than your share.** A per-wallet cap is set at launch and enforced cumulatively, so a player who wins ten rounds of allowance still can't exceed it.
* **The coin reaches the open market on time.** The gate carries an expiry written into the pool at launch. Past it the gate stops enforcing on its own, with no transaction needed from the game server, the creator, or Flaunch.

That last one is the important one. Without an expiry, a coin would need our servers to still be alive to ever trade freely, and holders would be waiting on us instead of on a clock.

{% hint style="info" %}
Game Mode is live on Robinhood chain. The technical details — the signing service, the contracts, and how enforcement works inside the Uniswap V4 hook — are documented under [Spend-Gated Launches](../developer-resources/spend-gate/README.md).
{% endhint %}

## What it changes for creators

Game Mode changes what buyers compete over. Instead of latency, it's skill, knowledge or luck, and instead of a few automated wallets clearing the fair launch in one block, you get a room full of people who spent five minutes earning a position. That's a slower launch and a better holder list, which is the trade a creator is making when they turn it on.

## The games

**[Split the Arrow](split-the-arrow.md)** is the first, and it's a real game rather than a buy button with a puzzle bolted to it: 3D archery with wind you have to read, a shot budget, and a leaderboard full of people you can watch shooting. It's playable now.

More are coming from us, but the version of Game Mode we're most interested in is the one where the game isn't ours at all. A trivia round for a knowledge community, a reflex test, a raffle for a project that would rather gate on luck than skill. Anything that can score a player can issue allowance.

See **[Build a Game](build-a-game.md)** for where that stands.
