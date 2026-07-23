---
description: The AI agent that launches memecoins from a single message on X and Farcaster.
icon: paw-simple
cover: ../.gitbook/assets/Flaunchy-portal.png
coverY: 0
---

# Flaunchy

Flaunchy is the Flaunch AI agent. Tell it what you want in plain language and it launches a coin onchain for you, sets up the fee split, and replies with the contract address and a link to the coin page. No wallet connect, no forms, no deploy scripts.

Talk to Flaunchy where you already post:

* Twitter/X: [x.com/flaunchybot](https://x.com/flaunchybot)
* Farcaster: [farcaster.com/flaunchy](https://farcaster.xyz/flaunchy)

Flaunchy launches on **Base** and on the **Robinhood chain**. Every launch uses the same fair-launch mechanics as the rest of Flaunch, so creator revenue, auto buybacks, and sniper protection all apply out of the box.

## Launch Types

#### 1. Fast Launch

**Name and ticker (image optional)**

```
💬 User: @Flaunchy launch Timer (TIME)
🐈‍⬛ Flaunchy: gmeow! Timer ($TIME) ready to launch on base. launching now...
🐈‍⬛ Flaunchy: Timer ($TIME) launched on base! check it: https://flaunch.gg/base/coins/0x1234...
CA: 0x1234...
```

**Other examples:**

* `Flaunchy create SpaceToken with ticker SPACE`
* `@flaunchybot launch a token called Moonshot`
* `hey Flaunchy, launch DOGE token`

***

#### 2. Standard Launch

**Set custom fee splits or receivers**

```
💬 User: @Flaunchy launch Timer (TIME) with 50% fees to vitalik.eth
🐈‍⬛ Flaunchy: henlo! Timer ($TIME) with custom fee setup. launching...
🐈‍⬛ Flaunchy: Timer ($TIME) launched! check: https://flaunch.gg/base/coins/0x5678...
CA: 0x5678...
```

**Other examples:**

* `Flaunchy create SpaceToken, send 25% fees to alice.eth`
* `launch Moonshot with 60% fee share to 0x1234...`
* `@flaunchybot launch DOGE, receiver is bob.eth, 40% fees`

***

#### 3. FlaunchIt Launch

**Reply to someone's post to create a themed token**

```
💬 Alice: I wish there was a rocket token 🚀
💬 Bob: @Flaunchy flaunch it
🐈‍⬛ Flaunchy: gmeow! Rocket ($RCKT) flaunched on base! check it: https://flaunch.gg/base/coins/0x9abc...
CA: 0x9abc...
```

**How it works:**

* Flaunchy auto-generates the token from the original post
* Poster gets 60% of fees
* You get 30% for replying
* Flaunchy keeps 10%

**Other examples:**

* `Reply to meme: Flaunchy flaunch it!`
* `Reply to image: @flaunchybot flaunch it with this vibe`

***

#### 4. Multi-Receiver Launch

**Split fees between multiple people**

```
💬 User: @Flaunchy launch TeamToken for alice.eth, bob.eth, charlie.eth
🐈‍⬛ Flaunchy: yo! TeamToken with 3 receivers. need a ticker
💬 User: TEAM
🐈‍⬛ Flaunchy: launching TeamToken ($TEAM) with multi-receiver split...
🐈‍⬛ Flaunchy: TeamToken ($TEAM) deployed! check: https://flaunch.gg/base/coins/0xdef0...
CA: 0xdef0...
```

**Custom percentages:**

```
💬 User: launch ProjectToken for alice.eth (40%), bob.eth (30%), charlie.eth (20%)
🐈‍⬛ Flaunchy: ProjectToken with custom splits launching...
```

**Other examples:**

* `Flaunchy create SpaceToken with receivers: @user1, @user2, vitalik.eth`
* `@flaunchybot launch DOGE for alice.eth, bob.eth (equal split)`

***

## Tips

* Give Flaunchy a name and a ticker and it can go straight away. Everything else is optional.
* Attach an image to set the coin's icon, or let Flaunchy generate one from the theme of the post.
* If you leave out a detail it needs, like a ticker for a multi-receiver launch, Flaunchy will ask before it launches.
* Once a coin is live, open the link Flaunchy replies with to trade, add socials, and track revenue on the coin page.
