---
icon: code
---

# Build a Game

[Split the Arrow](split-the-arrow.md) is the first Game Mode game. It should not be the only one, and it should not all be ours.

The gate underneath it is not archery-shaped. It is two pieces: a **signer** that decides how much ETH a wallet has earned the right to spend, and a **fee calculator** that enforces that decision on chain. Nothing in either piece knows what a bow is. Any game that can look at a player and produce a number can drive it.

That opens up most of what "fair distribution" could mean:

* **Skill** — reflex, aim, timing. What Split the Arrow does.
* **Knowledge** — a trivia round for a community that actually knows its subject, gating the launch on getting the answers right.
* **Luck** — a raffle, for a project that would rather not reward the quickest thumbs.
* **Effort** — anything that costs a few minutes of genuine attention, which is already more than a sniper is willing to spend.

## Where this stands

The contracts are live on Robinhood chain and documented in full under [Spend-Gated Launches](../developer-resources/spend-gate/README.md) — the launch parameters, the signature format, and every way enforcement can refuse a swap. Enough to build against today, if you are comfortable working directly with the contracts.

What is not shipped yet is the part that makes it pleasant:

* **An SDK**, so issuing a signed authorization is a function call rather than an EIP-712 implementation you have to get exactly right.
* **An MCP server**, so an agent can stand up a gated launch and run a round without a human assembling the transactions.

Both are in progress. Until they land, the honest summary is that building a Game Mode game means reading the two technical pages and wiring a signer yourself.

{% hint style="success" %}
If you are thinking about building one, come and talk to us on [Discord](https://discord.gg/PcSmznqqqb) before you start. We would rather shape the SDK around a game somebody actually wants to ship than guess at the surface.
{% endhint %}
