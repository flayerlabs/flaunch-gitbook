---
icon: gamepad-modern
---

# Game Mode

A fair launch is supposed to give everyone the same shot at a coin. In practice the first block belongs to whoever has the best infrastructure. Bots watch the mempool, land their buys in the same second the pool opens, and sell into the people who arrived a moment later. The launch was fair in the sense that nobody was excluded, and unfair in every sense that matters.

Game Mode replaces the race with a **window**. For as long as the window is open, the only way to buy the coin is to earn the right to — by playing a game. Nobody can buy early, including us. Nobody can buy more than they earned. When the window closes the coin trades like any other Flaunch coin.

The gate is not a speed bump. It is the entire entrance.

## How a round works

1. **A coin launches with a window.** The creator schedules the launch and names the game that will guard it. Until the window opens, every swap against the pool reverts — this is enforced by the pool itself, not by the game server.
2. **Players play.** Everyone in the round shares one window and one leaderboard. Scoring happens on the server, which re-simulates every move rather than believing the client.
3. **Points become spending power.** Your score converts to an ETH allowance. Earn more, buy more — up to a per-wallet cap that applies to everyone equally.
4. **You buy what you earned.** Claiming your allowance produces a signed authorization for exactly that amount. The pool checks the signature and the amount on every swap.
5. **The window closes and the gate lifts.** The coin is now an ordinary coin, trading permissionlessly.

## What you can rely on

Game Mode is worth playing only if the rules bind the operator as tightly as they bind the players. They do, and they do it on chain:

* **Nobody buys before the window.** The launch's opening time is the pool's own rule. A player bypassing the game, the game server itself, and the creator are all refused identically.
* **Nobody buys more than they earned.** Every authorization names a maximum spend, and the pool measures the swap's real ETH input against it.
* **Nobody buys more than their share.** A per-wallet cap is set at launch and enforced cumulatively, so a player who wins ten rounds of allowance still cannot exceed it.
* **The coin reaches the open market on time, whatever happens to us.** The gate carries an expiry written into the pool at launch. Past it the gate stops enforcing on its own — no transaction from the game server, the creator, or Flaunch. If our servers vanish mid-round, the coin still opens.

That last one is the load-bearing guarantee. A gate that depends on the gatekeeper coming back is not a gate, it is a hostage situation.

{% hint style="info" %}
Game Mode is live on Robinhood chain. The technical details — the signing service, the contracts, and how enforcement works inside the Uniswap V4 hook — are documented under [Spend-Gated Launches](../developer-resources/spend-gate/README.md).
{% endhint %}

## Game Mode and Sniper Protection

Flaunch already offers [Sniper Protection](../core-features/sniper-protection.md), which puts a CAPTCHA in front of the fair launch period. The two solve adjacent problems and can be described in one line each:

* **Sniper Protection** asks *are you a person?*
* **Game Mode** asks *what did you do to deserve this?*

Sniper Protection raises the cost of automation. Game Mode changes what is being competed over: not latency, but skill, knowledge, or luck. A creator who wants a broad, engaged initial distribution — holders who spent five minutes earning their position rather than five milliseconds — is reaching for the second one.

## The games

**[Split the Arrow](split-the-arrow.md)** is the first, and it is a real game rather than a puzzle wrapped around a buy button: 3D archery with wind you have to read, a shot budget, and a leaderboard full of people you can see shooting. It is playable now.

More are coming from us. But the interesting version of Game Mode is the one where the game is not ours at all — a trivia round for a knowledge community, a reflex test, a raffle for a project that would rather gate on luck than skill. Anything that can score a player can issue allowance.

See **[Build a Game](build-a-game.md)** for where that stands.
