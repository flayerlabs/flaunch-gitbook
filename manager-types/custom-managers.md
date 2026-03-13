# Custom Managers

Custom managers let builders define treasury behavior outside the standard approved manager set.

## What A Custom Manager Is

A custom manager is a treasury-side contract that receives or controls the Flaunch revenue rights for a launched token.

Builders use custom managers when they need:

* Custom Fee Routing
* Custom Claim Logic
* Custom Escrow Or Treasury Behavior
* Product-Specific Policy After Launch

## How It Connects To Flaunch

Through a wrapper around `FlaunchZap` that enforces the manager path automatically.

The wrapper path is useful when the launch flow itself must guarantee the correct manager setup for every launch.

## Standard Managers vs Custom Managers vs Wrappers

### Standard managers

Use a standard manager when one of the existing manager types already matches the product.

This is the simplest path when you want a supported manager flow with less custom contract work.

Example: a launchpad that wants to use the existing RevenueManager so it can take a protocol fee without writing a new treasury contract.

### Custom managers

Use a custom manager when the treasury behavior itself is unique to the product.

This is the right path when the post-launch logic cannot be expressed through the existing manager set.

Custom managers will usually also need a wrapper around `FlaunchZap` so the intended manager setup is applied consistently during token creation.

Example: a product that wants fees to unlock over time, route to a game treasury, or follow custom creator payout rules that the standard managers do not support.

### Wrappers around `FlaunchZap`

Use a wrapper when the launch entrypoint itself needs to enforce a chosen manager policy for every launch.

This is not the first step for most builders. It is an advanced pattern on top of the custom manager model.

Example: a branded launchpad where every token launch must use the same custom manager and the team does not want integrators setting manager parameters manually.

## What A Wrapper Does

A wrapper around `FlaunchZap` can:

* Force The Intended Manager
* Set Default `permissions`
* Set Default `initializeData`
* Set Default `depositData`
* Ensure New Launches Bind To The Right Manager Path

This is useful when a product does not want users or integrators to configure manager parameters by hand.

## Related Docs

{% content-ref url="README.md" %}
[README.md](README.md)
{% endcontent-ref %}

{% content-ref url="../getting-started/launch-a-launchpad.md" %}
[launch-a-launchpad.md](../getting-started/launch-a-launchpad.md)
{% endcontent-ref %}

{% content-ref url="../artificial-intelligence/vibe-guide.md" %}
[vibe-guide.md](../artificial-intelligence/vibe-guide.md)
{% endcontent-ref %}

## Need Support Building Custom Manager?

Support is available in the [Flaunch Discord](https://discord.gg/QPxV8bMk) and [Telegram](https://t.me/flaunchgg/1).
