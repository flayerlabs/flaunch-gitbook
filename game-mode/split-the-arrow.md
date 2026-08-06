---
icon: bullseye-arrow
---

# Split the Arrow

Sixty seconds, a longbow, and a target at thirty-five metres in gusting wind. Every point you score becomes ETH you are allowed to spend on the coin that is launching. Everyone in the round is shooting at the same time, and you can watch them do it.

This page walks through a round as a player sees it. For how the gate works underneath, see [Spend-Gated Launches](../developer-resources/spend-gate/README.md).

## The lobby

A round lives at a link. Open it and you are in the lobby with everyone else who followed it, watching the countdown to the window.

<figure><img src="../.gitbook/assets/game-mode-01-lobby.png" alt="The Split the Arrow lobby, showing the roster of players and the countdown to the launch window"><figcaption>Everyone who follows the link lands in the same room. The code in the address bar is the invite.</figcaption></figure>

The room code in the URL is a working invite — send it to someone and they join the round you are in, not whichever round happens to be current when they click. Leave and come back and you return to your own seat, with the same wind and the same score.

You cannot shoot yet. The window has not opened, and until it does the pool refuses every buy, including ours.

## Nocking, drawing, aiming

<figure><img src="../.gitbook/assets/game-mode-02-tutorial.png" alt="The tutorial card explaining the controls"><figcaption>The tutorial runs once and can be skipped.</figcaption></figure>

Four controls, and the whole game is in them:

| Control | What it does |
| --- | --- |
| **Space** | Nock an arrow |
| **Hold click** | Draw. Power builds for about 1.4 seconds to a full draw, and keeps going into overdraw |
| **Drag** | Aim |
| **Shift** | Hold your breath — freezes the sway for 2 seconds, once per shot |
| **Release** | Shoot |

Your aim sways while you hold a draw, and the longer you hold the worse it gets. Holding your breath buys you a still two seconds to place the shot, and you get one per arrow. Spending it early because the sway looked bad is the most common way to lose a bullseye.

## The wind

<figure><img src="../.gitbook/assets/game-mode-03-wind.png" alt="The aiming view with the range flags showing crosswind"><figcaption>The flags are the only wind indicator. Read them before you release.</figcaption></figure>

Wind is the skill in this game. It gusts on roughly a seven-second cycle and reverses direction at least once per round, and there is no readout for it — the flags down the range are what you have. A shot that would have been a bullseye in still air lands in the outer ring if you release into a gust you did not see building.

Your wind is **yours**. Every player gets their own seed, so the conditions you are shooting in are not the conditions the person above you on the leaderboard is shooting in. Same window, same board, same scoring; personal weather. This is deliberate: a shared wind could be solved once and the answer handed around.

## Scoring

<figure><img src="../.gitbook/assets/game-mode-04-rings.png" alt="Arrows landed on the target, showing the scoring rings"><figcaption>Four rings. The tightest one containing the arrow wins.</figcaption></figure>

| Ring | Points |
| --- | --- |
| Outer | 50 |
| Third | 150 |
| Second | 400 |
| Bullseye | 1,000 |

You have **32 arrows** per round with a short cooldown between them, so a round is not a spraying contest — at roughly one shot per second you will not run the budget out inside sixty seconds unless you are trying to.

## Splitting the arrow

<figure><img src="../.gitbook/assets/game-mode-05-split.png" alt="An arrow splitting a previously landed arrow on the target"><figcaption>The shot the game is named after.</figcaption></figure>

Land an arrow in one already stuck in the board and you split it — the Robin Hood shot. A split **doubles that shot's own ring score**, so splitting a bullseye is worth 2,000.

The first three splits of a round pay the bonus. After that an arrow still splits, still looks like it, and still scores its ring — it just stops doubling. The cap exists because a program that has solved the wind lands every arrow on the same point by default, and would otherwise collect a Robin Hood bonus thirty times in a round that no human collects twice.

## The Taxman

<figure><img src="../.gitbook/assets/game-mode-06-taxman.png" alt="The Taxman riding across the back of the range"><figcaption>He crosses once. Everyone in the round sees the same ride.</figcaption></figure>

Once per round, the Taxman gallops across the road behind the target. Unhorse him and you take **2,500 points** — two and a half bullseyes, and the biggest single swing available to you.

He is a genuine skill shot. He is further away than the target, he is moving, and his pace surges rather than holding steady, so leading him is guesswork unless you have watched him before. Unlike the wind, his crossing is **shared**: every player in the round rides the same one, at the same moment, which is what makes it something to shout about in the reactions bar.

Taking the shot costs you an arrow that was not going to score on the board. Missing him costs you nothing except the arrow.

## The leaderboard

<figure><img src="../.gitbook/assets/game-mode-07-leaderboard.png" alt="The live leaderboard of players in the round with reactions"><figcaption>Live, server-scored, and everybody can see it.</figcaption></figure>

Scores update live for everyone. There is no chat — the social channel is reactions, which are one tap and cannot be used to shill a different coin at a captive audience.

Every score on that board was calculated on the server. The game re-runs your shots from your inputs and scores what actually happened, so the board is a record of what people did rather than what their browsers claimed.

## Buying what you earned

<figure><img src="../.gitbook/assets/game-mode-08-buy.png" alt="The buy panel showing the ETH allowance earned from the round score"><figcaption>Points, converted. This is the whole reward.</figcaption></figure>

Your score is an ETH allowance, and the BUY button spends it. Press it and the server issues a signed authorization for that exact amount, your wallet submits the swap carrying it, and the pool checks the signature before letting the trade through.

Two limits apply. You cannot spend more than you earned, and you cannot spend more than the per-wallet cap the launch set — which is the same for everyone in the round no matter how well they shot. A very good round hits the cap; the cap is what stops Game Mode from being a game where the best player takes the float.

<figure><img src="../.gitbook/assets/game-mode-09-price.png" alt="The price chart with confirmed buys from across the round"><figcaption>One curve, moved by everyone.</figcaption></figure>

Every buy in the round moves the same price. Waiting to buy is a real decision: your allowance does not expire inside the window, but the price it buys at is being pushed up by everyone who bought already.

## The horn

<figure><img src="../.gitbook/assets/game-mode-10-results.png" alt="The end-of-round results card showing the final board and score"><figcaption>Your board, ready to share.</figcaption></figure>

The window closes, the round settles, and the gate lifts. The coin is now an ordinary Flaunch coin — anyone can buy it, no signature required, no game involved.

You keep your board. The results card renders your round as an image, and the best three shots of a round can be pulled out as a replay clip, re-rendered from the same numbers the server scored.

## Playing before you play

Practice rounds run the whole multiplayer experience — real roster, real scoring, real opponents — against a simulated economy. Nothing is earned and nothing is bought, so there is no wallet, nothing to sign, and no reason to be careful. It is the right place to learn what a headwind looks like.

{% hint style="warning" %}
Points earned in a practice round are not allowance. Only a live round against a real launch produces something you can spend.
{% endhint %}
