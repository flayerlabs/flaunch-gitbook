---
description: Build a token launch experience on top of Flaunch.
icon: rocket-launch
---

# Build a Launchpad

Flaunch can power a launchpad in two ways.

1. Use the existing Flaunch launch flow and build your own product, onboarding, and brand on top of it.
2. Add custom onchain policy with a manager or wrapper when your launchpad needs rules that standard Flaunch launches do not enforce.

For most teams, the first path is the right one.

## Choose the Right Path

### Basic launchpad

Use the basic path when you want to launch tokens through Flaunch without writing new protocol contracts.

This path is best for:

* standard token launch flows
* creator launchpads
* branded consumer apps
* backend-assisted launch flows
* products that want to launch into an existing revenue split or treasury manager

You have two ways to build it:

* **Web2 API:** the fastest path for a backend-driven launch flow
* **SDK:** the best path when your app needs direct reads, writes, parsing, and more control over the user experience

### Advanced launchpad

Use the advanced path when the launchpad itself needs custom onchain behavior.

This path is best for:

* custom treasury behavior at launch
* project-specific permissioning
* launchpads that must route every launch through a custom manager
* wrapper contracts around `FlaunchZap`

## Fastest Path: Web2 API

Use the API when you want the smallest integration surface.

The launch flow is:

1. Upload the token image
2. Submit the launch job
3. Poll for launch status

This is the best choice when your product needs a clean backend-assisted flow with fewer moving parts.

{% content-ref url="../developer-resources/flaunch-memecoin-api.md" %}
[flaunch-memecoin-api.md](../developer-resources/flaunch-memecoin-api.md)
{% endcontent-ref %}

## Most Flexible Path: SDK

Use the SDK when you want your app to control the launch experience directly.

The SDK supports:

* direct token launches
* fee split launches
* revenue manager launches
* launching into an existing group or treasury manager
* parsing launch results from transaction logs
* trading, liquidity, and import flows around the launch

At the protocol layer, a launch is centered around the `FlaunchParams` fields used by the position manager, including:

* token name and symbol
* metadata URI
* fair launch allocation and duration
* creator address
* creator fee allocation
* optional scheduled launch time
* initial pricing parameters

{% content-ref url="../developer-resources/guides/flaunching-a-coin.md" %}
[flaunching-a-coin.md](../developer-resources/guides/flaunching-a-coin.md)
{% endcontent-ref %}

## When You Need a Manager

Managers are used when revenue rights or treasury behavior need to be handled by another contract.

Common examples:

* a revenue-sharing launchpad
* a group or staking-based launchpad
* a split manager for multiple recipients
* a custom treasury contract controlled by your product

Flaunch already supports this model directly. A launchpad can:

* deploy a manager first, then launch into it
* launch a token and create a new manager in the same flow
* add existing Flaunch positions into a manager later

{% content-ref url="../developer-resources/treasury-managers/README.md" %}
[README.md](../developer-resources/treasury-managers/README.md)
{% endcontent-ref %}

## Recommended Build Order

1. Start with the default token launch flow.
2. Add fee splits or manager support if your product needs custom ownership or revenue routing.
3. Move to a custom manager or zap wrapper only when your launchpad needs hard onchain policy.

This keeps the launchpad simple at first while preserving a clear upgrade path as the product expands.

## Related Guides

{% content-ref url="flaunch-a-coin.md" %}
[flaunch-a-coin.md](flaunch-a-coin.md)
{% endcontent-ref %}

{% content-ref url="launch-a-group.md" %}
[launch-a-group.md](launch-a-group.md)
{% endcontent-ref %}

{% content-ref url="../developer-resources/quick-start.md" %}
[quick-start.md](../developer-resources/quick-start.md)
{% endcontent-ref %}
