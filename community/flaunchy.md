---
description: Flaunch's mascot and AI token launcher. Tweet him a name, he launches the coin.
icon: paw-simple
cover: ../.gitbook/assets/Flaunchy-portal.png
coverY: 0
---

# Flaunchy

Flaunchy is Flaunch's mascot and AI token launcher. Mention him on X with a token name and he launches a memecoin for you onchain, then replies with the contract address and a link to its flaunch.gg coin page. No wallet connect, no forms, no gas.

Talk to Flaunchy on Twitter/X: [x.com/flaunchybot](https://x.com/flaunchybot)

## How to launch

The whole flow is one tweet. Mention [@flaunchybot](https://x.com/flaunchybot) and tell him what to launch. A name is all he needs; everything else has a default.

```
💬 you: @flaunchybot launch Timer (TIME)
🐈‍⬛ flaunchy: gm. launching $TIME on base...
🐈‍⬛ flaunchy: $TIME is live: https://flaunch.gg/base/coins/0x1234...
```

You can also reply "flaunch it" under any post, and Flaunchy will spin a coin out of it:

```
💬 alice: someone should make a rocket coin 🚀
💬 you: @flaunchybot flaunch it
🐈‍⬛ flaunchy: $RCKT is live: https://flaunch.gg/base/coins/0x9abc...
```

### What Flaunchy needs from you

* **Name** - required. Used exactly as you write it.
* **Ticker** - optional. Leave it out and Flaunchy picks one (1 to 8 characters).
* **Image** - optional. Attach one and it becomes the coin's art. Otherwise the default Flaunch token image is used.
* **Everything else** - description, fee setup, and which chain to use are all handled automatically.

You don't need a wallet to launch. Flaunchy creates a claimable wallet tied to your X account, so your fees are waiting for you when you log in at flaunch.gg.

## What you get

* **Free to launch** - no cost, no gas, no wallet setup.
* **$10K starting market cap, straight to market** - there is no fair-launch period, so price discovery starts the moment the coin goes live.
* **80% of trading fees, paid in ETH** - streamed for the life of the coin and claimable at flaunch.gg by logging in with your X account.
* Coins launch on **Base** and **Robinhood Chain**. Flaunchy picks a live chain for you.

Every launch reply includes the coin's flaunch.gg link and its `$TICKER`, so you can trade and share it right away. Coin pages live at `flaunch.gg/{chain}/coins/<address>`, for example `flaunch.gg/base/coins/<address>`.

### About the fees

You keep 80% of your coin's trading fees, in ETH, for as long as it trades. Flaunchy keeps the other 20% for running the launch. If you would rather keep 100%, you can launch directly on flaunch.gg and configure the coin yourself; going through Flaunchy is the free, one-tweet path, and the cut is what keeps him running.

## Coming soon

* **Fee splitting** - split your 80% share across multiple wallets. Today the full creator share goes to the launcher.
* **Custom pairing** - launch a coin paired against a token other than ETH. Today every coin pairs against ETH.
