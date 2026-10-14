---
icon: code
---

# Build a Game

[Split the Arrow](split-the-arrow.md) is the first Game Mode game. It won't be the only one, and they won't all be ours: Game Mode is open to anyone's game, and the launch experience will be as unique as the game that gates it.

The gate underneath isn't archery-shaped. It's two pieces: a **signer** that decides how much a wallet has earned the right to spend, and a **fee calculator** that enforces that decision on chain. Nothing in either piece knows what a bow is. Any game that can look at a player and produce a number can drive it.

Note which direction this runs. A game developer never tokenizes their game. There is no coin for the game, and nothing for you to launch. Coins are launched by creators, who pick a game to be the entrance; your game is the launch's distribution mechanism. On an open pool the first block belongs to bots and snipers; inside a game, the first holders are the people who showed up and played. You build the room the launch happens in.

That opens up most of what "fair distribution" could mean:

* **Skill:** reflex, aim, timing. What Split the Arrow does.
* **Knowledge:** a trivia round for a community that actually knows its subject, gating the launch on getting the answers right.
* **Luck:** a raffle, for a project that would rather not reward the quickest thumbs.
* **Effort:** anything that costs a few minutes of genuine attention, which is already more than a sniper is willing to spend.

And "game" carries no blockchain qualifier. There is no requirement that a Game Mode game be about money, tokens, or chains. A real game in the Quake 3 sense qualifies exactly as well as a trivia round. Your game code never sees a wallet, a key, or a transaction.

## From idea to launch

You build a browser game. Flaunch runs everything else: the gate that keeps score and signs allowances, the signing key, the database, and the anti-abuse checks. There is no server for you to deploy.

1. **Build** your game with the [Game Mode SDK](game-mode-sdk.md) and play it locally. No server, wallet or chain needed.
2. **Run `pnpm build`.** It writes your game to `dist/`, plus a `gamemode.json` manifest and, for most games, a `gamemode.rules.js` file holding your scoring rules.
3. **Zip `dist/`** and upload it at [flaunch.gg/game-mode/dashboard](https://flaunch.gg/game-mode/dashboard) with a name, a category and a short description. Hosting is included.
4. **Test on Base Sepolia.** Your upload is playable there straight away, so you can run a real round on testnet before anyone reviews it.
5. **Submit for mainnet.** After review, your game opens on mainnet networks, and creators can launch coins through it.

Your scoring rules run inside Flaunch's gate, in a sandbox, so a player cannot edit their score in the browser. If your game already has its own authoritative server, for example real-time multiplayer, it keeps that server and reports scores to Flaunch's gate instead. The [SDK page](game-mode-sdk.md#games-with-their-own-server) covers both.

## Already have a game?

You don't need to build from zero. If a browser game already exists, including a permissively licensed open-source one, hand your AI coding agent the [`port-game-mode` skill](game-mode-sdk.md#already-have-a-game-port-it) and it will walk the whole port: the scoring economy, the coin surfaces, the launch framing, and the ZIP.

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill port-game-mode
```

## The hackathon

The [Game Mode Hackathon](https://flaunch.gg/game-mode/hackathon) takes submissions the same way: build with the SDK, zip the build, and upload it at [flaunch.gg/game-mode/dashboard](https://flaunch.gg/game-mode/dashboard).

The best submissions join the official Game Mode library, and library games earn **5% of the trading fees of every coin that launches through them**, for as long as creators keep choosing your game.

## Under the hood

The contracts are live on Ethereum, Arbitrum, Base and Robinhood chain, and documented in full under [Spend-Gated Launches](../developer-resources/spend-gate/README.md): the launch parameters, the signature format, and every way enforcement can refuse a swap. You do not need any of it to build or submit a game; it is there for the curious and for anyone integrating at the contract level.

{% hint style="success" %}
If you're building one, come and talk to us on [Discord](https://discord.gg/PcSmznqqqb). We play every submission, and we'd rather help you ship than have you guess.
{% endhint %}
