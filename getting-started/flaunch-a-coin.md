---
icon: coin-blank
---

# Launch a Token

Launching a token on Flaunch can be as simple or as configurable as you need.

At minimum, a launch only needs token metadata and a creator. From there, Flaunch lets you control fair launch settings, revenue allocation, scheduling, and treasury routing.

## Fast Path

The fastest launch flow uses the default product settings:

* token name and symbol
* description and image
* creator revenue enabled
* community buybacks enabled
* standard fair launch configuration

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

* direct creator ownership
* multiple fee receivers
* revenue managers
* groups and treasury managers

This makes it possible to launch for an individual, a team, or a product with custom revenue routing.

### Fair launch settings

Every token can start with a fixed-price fair launch.

The launch can define:

* how much supply is available during fair launch
* how long the fair launch lasts
* the starting market cap
* whether the creator prebuys part of the fair launch supply
* whether the launch starts immediately or at a future time

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

* launch at a scheduled time
* set a custom market cap
* change fair launch allocation
* prebuy part of the supply
* route revenue into a manager or group

## For Developers

If you are integrating Flaunch programmatically, use the SDK or API guides below.

{% content-ref url="../developer-resources/guides/flaunching-a-coin.md" %}
[flaunching-a-coin.md](../developer-resources/guides/flaunching-a-coin.md)
{% endcontent-ref %}

{% content-ref url="../developer-resources/flaunch-memecoin-api.md" %}
[flaunch-memecoin-api.md](../developer-resources/flaunch-memecoin-api.md)
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
