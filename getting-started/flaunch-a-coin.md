---
icon: coin-blank
---

# Launch a Token

Launching a token on Flaunch can be as simple or as configurable as you need.

At minimum, a launch only needs token metadata and a creator. From there, Flaunch lets you control fair launch settings, revenue allocation, scheduling, and treasury routing.

## Start With The Skill

If you are building this flow with an AI coding agent, start with the Flaunch skill first.

Install:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill flaunch
```

Then prompt the agent with the token creation path:

```text
Use flaunch: Launch a memecoin on Base with the fewest decisions possible.
```

The token creation skill defaults to the Web2 API fast path and only switches to the SDK path when direct launch control is needed.

## Fast Path

The fastest launch flow uses the default product settings:

* Token Name And Symbol
* Description And Image
* Creator Revenue Enabled
* Community Buybacks Enabled
* Standard Fair Launch Configuration

This is the best path when speed matters most.

## What You Can Configure

### Revenue split

When launching a token, the creator can choose how much of trading fee revenue they want to keep.

The default split is:

* Creator share: 80%
* Community buybacks: 20%

Anything not allocated to the creator is routed to [auto buybacks](../core-features/auto-buybacks.md).

{% hint style="info" %}
The creator share belongs to the holder of the revenue NFT. That can be a wallet, a multisig, a custom contract, or a group manager.
{% endhint %}

### Fee receivers

The creator side of revenue does not have to stay with a single wallet.

Flaunch supports:

* Direct Creator Ownership
* Multiple Fee Receivers
* Revenue Managers
* Groups And Treasury Managers

This makes it possible to launch for an individual, a team, or a product with custom revenue routing.

### Fair launch settings

Every token can start with a fixed-price fair launch.

The launch can define:

* How Much Supply Is Available During Fair Launch
* How Long The Fair Launch Lasts
* The Starting Market Cap
* Whether The Creator Prebuys Part Of The Fair Launch Supply
* Whether The Launch Starts Immediately Or At A Future Time

During the fair launch window, buyers trade at the same fixed price. When the window ends, the token moves into normal AMM trading.

### Sniper protection

Flaunch can apply bot-resistance during the fair launch period.

Using the [Flaunch app](https://flaunch.gg/), creators can enable CAPTCHA-based protection and per-wallet caps to support broader initial distribution.

{% hint style="success" %}
Sniper protection applies during the fair launch window, when launch quality matters most.
{% endhint %}

## Advanced Launches

Use advanced settings when the launch needs more direct control.

Common reasons:

* Launch At A Scheduled Time
* Set A Custom Market Cap
* Change Fair Launch Allocation
* Prebuy Part Of The Supply
* Route Revenue Into A Manager Or Group

## For Developers

If you are integrating Flaunch programmatically, use the SDK or API guides below.

{% content-ref url="../developer-resources/guides/flaunching-a-coin.md" %}
[flaunching-a-coin.md](../developer-resources/guides/flaunching-a-coin.md)
{% endcontent-ref %}

{% content-ref url="../developer-resources/flaunch-memecoin-api.md" %}
[flaunch-memecoin-api.md](../developer-resources/flaunch-memecoin-api.md)
{% endcontent-ref %}

{% content-ref url="../developer-resources/guides/token-images.md" %}
[token-images.md](../developer-resources/guides/token-images.md)
{% endcontent-ref %}

## Related Concepts

{% content-ref url="../core-features/fixed-price-fair-launch.md" %}
[fixed-price-fair-launch.md](../core-features/fixed-price-fair-launch.md)
{% endcontent-ref %}

{% content-ref url="../core-features/creator-revenue.md" %}
[creator-revenue.md](../core-features/creator-revenue.md)
{% endcontent-ref %}

{% content-ref url="launch-a-group.md" %}
[launch-a-group.md](launch-a-group.md)
{% endcontent-ref %}
