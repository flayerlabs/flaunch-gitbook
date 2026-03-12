# Flaunch

Flaunch is a token launch protocol for creators, communities, and developers.

It gives users a fast way to launch tokens with fixed-price fair launch mechanics, creator revenue in ETH, and configurable treasury behavior. It also gives builders a clean foundation for launchpads, revenue products, and group-based token economies.

## Install The Flaunch Skill

For a single install target, install the top-level `flaunch` skill:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill flaunch
```

That installs the umbrella router skill, which then routes the agent to the right deeper skill for the task.

## What Flaunch Does

Flaunch combines token creation, launch mechanics, and ongoing fee routing in one system.

When a token is launched on Flaunch, the launch can include:

* A Fixed-Price Fair Launch Window
* Creator Revenue Paid In ETH
* Automatic Community Buybacks
* Optional Scheduling For A Future Launch Time
* Optional Treasury Manager Or Group Integration

This lets a single token act as both a launch event and a long-term onchain revenue primitive.

## Getting Started

{% content-ref url="getting-started/why-flaunch.md" %}
[why-flaunch.md](getting-started/why-flaunch.md)
{% endcontent-ref %}

{% content-ref url="getting-started/flaunch-a-coin.md" %}
[flaunch-a-coin.md](getting-started/flaunch-a-coin.md)
{% endcontent-ref %}

{% content-ref url="getting-started/launch-a-launchpad.md" %}
[launch-a-launchpad.md](getting-started/launch-a-launchpad.md)
{% endcontent-ref %}

{% content-ref url="getting-started/launch-a-group.md" %}
[launch-a-group.md](getting-started/launch-a-group.md)
{% endcontent-ref %}

## Who These Docs Are For

These docs are organized for three common goals:

* Understanding What Makes Flaunch Different
* Launching A Token Quickly
* Building A Launchpad Or Treasury Product On Top Of The Protocol

## Product Story

Flaunch is designed around a simple idea: token launches should not end at minting.

A Flaunch token can begin with a structured launch, generate ongoing fees, and route those fees to creators, communities, or external managers. That makes Flaunch useful both for token creators who want a better default launch and for developers who want programmable revenue infrastructure.

## Key Concepts

{% content-ref url="core-features/creator-revenue.md" %}
[creator-revenue.md](core-features/creator-revenue.md)
{% endcontent-ref %}

{% content-ref url="core-features/fixed-price-fair-launch.md" %}
[fixed-price-fair-launch.md](core-features/fixed-price-fair-launch.md)
{% endcontent-ref %}

{% content-ref url="core-features/auto-buybacks.md" %}
[auto-buybacks.md](core-features/auto-buybacks.md)
{% endcontent-ref %}
