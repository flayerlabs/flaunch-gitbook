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

* Standard Token Launch Flows
* Creator Launchpads
* Branded Consumer Apps
* Backend-Assisted Launch Flows
* Products That Want To Launch Into An Existing Revenue Split Or Treasury Manager

You have two ways to build it:

* **Web2 API:** the fastest path for a backend-driven launch flow
* **SDK:** the best path when your app needs direct reads, writes, parsing, and more control over the user experience

### Advanced launchpad

Use the advanced path when the launchpad itself needs custom onchain behavior.

This path is best for:

* Custom Treasury Behavior At Launch
* Project-Specific Permissioning
* Launchpads That Must Route Every Launch Through A Custom Manager
* Wrapper Contracts Around `FlaunchZap`

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

* Direct Token Launches
* Fee Split Launches
* Revenue Manager Launches
* Launching Into An Existing Group Or Treasury Manager
* Parsing Launch Results From Transaction Logs
* Trading, Liquidity, And Import Flows Around The Launch

At the protocol layer, a launch is centered around the `FlaunchParams` fields used by the position manager, including:

* Token Name And Symbol
* Metadata URI
* Fair Launch Allocation And Duration
* Creator Address
* Creator Fee Allocation
* Optional Scheduled Launch Time
* Initial Pricing Parameters

{% content-ref url="../developer-resources/guides/flaunching-a-coin.md" %}
[flaunching-a-coin.md](../developer-resources/guides/flaunching-a-coin.md)
{% endcontent-ref %}

## When You Need a Manager

Managers are used when revenue rights or treasury behavior need to be handled by another contract.

Common examples:

* A Revenue-Sharing Launchpad
* A Group Or Staking-Based Launchpad
* A Split Manager For Multiple Recipients
* A Custom Treasury Contract Controlled By Your Product

Flaunch already supports this model directly. A launchpad can:

* Deploy A Manager First, Then Launch Into It
* Launch A Token And Create A New Manager In The Same Flow
* Add Existing Flaunch Positions Into A Manager Later

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
