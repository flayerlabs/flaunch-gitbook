---
icon: coin-blank
---

# Launch a Coin

Launching a coin takes 30 seconds and is free!

Other than setting a name, symbol and description, Flaunch offers a suite of tools that go far beyond what is possible elsewhere...

### Custom fee split

When launching a coin the Creator can choose to earn anything between 0% and 100% of the trading fees. Whatever the Creator doesn't take is sent to [automated buybacks](../core-features/auto-buybacks.md).

The default settings are:

* Creator share: 80%
* Auto buybacks: 20%

{% hint style="info" %}
The creator share is paid out to the holder of the revenue stream NFT which could be a user, a multi-sig, a custom contract or a Group.
{% endhint %}

### Fee receivers

By default the Creator share is paid out to the user that launches the coin, however there is a lot more flexibility available.

**Multiple Fee Receivers**

Using the [Flaunch app](https://flaunch.gg/) it's possible to set an unlimited number of fee receivers for a given coin. These receivers can be wallet addresses, ENS or Basename and even X and Farcaster handles. This makes it possible to "airdrop" or sell fee receiver rights to users that may never have used crypto.

**Groups**

{% hint style="info" %}
Groups are not yet live. [Follow on X for the latest](https://x.com/flaunchgg).
{% endhint %}

Coins can share their trading fees with Groups, and Groups can contain an unlimited set of coins. It is possible to launch a coin into an existing Group, or you can [create a new Group](launch-a-group.md) and launch a coin into one that you own.

The trading fees from coins inside Groups are rewarded in two ways:

1. Staking rewards

Users that stake the "Group coin" receive the trading fees.

2. Buybacks

The "Group coin" is bought back with the trading fees.

**Sniper Protection**

Flaunch has created proprietary technology that allows web2 verification of a user to be embedded in the Flaunch AMM.

Enabling Sniper Protection when launching a coin via the [Flaunch app](https://flaunch.gg/) requires the user to validate a CAPTCHA before being able to swap during the Fair Launch period (5 minutes by default).

This allows the Creator to leverage the power of hardened web2 anti-bot/proof of humanity tooling to create a better initial distribution of the coin.

{% hint style="success" %}
A "per wallet cap" can also be set to facilitate a broader distribution amongst users at launch.
{% endhint %}

**Scheduling, Fair Launch supply and more**

Under "Advanced Flaunch" there are more settings a Creator can choose from:

* Prebuy (purchase tokens at the fair launch price)
* Starting market cap (min $1,000)
* Scheduling for launch at a future date
* Fair launch supply (how much is available during fair launch)

### Fair Launch Period

The Fair Launch period on Flaunch is simple. For a given duration (default: 5 minutes) the supply available (default: 10%) is available to purchase at a _fixed price_. This means that any buyer during this period can also sell at the _same price_, removing the price risk of early entry.

Once the Fair Launch period ends (after the duration finishes or the fair launch supply is sold) the liquidity moves into a standard xyk AMM price curve from the starting price to infinity.

The Sniper Protection mentioned above is only applied during the Fair Launch period.
