---
description: Install the Flaunch AI skills and start building quickly.
icon: sparkles
cover: ../.gitbook/assets/Flaunchy-AI.png
coverY: 0
---

# AI Skills Quick Start

The Flaunch skills repository gives AI coding agents a structured way to build on Flaunch.

It is designed to route the agent to the smallest skill that matches the job, whether the goal is launching a token, building a launchpad, or creating a manager-based flow.

## Install The Flaunch Skill

For a single install target, install the top-level `flaunch` skill:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill flaunch
```

That installs the umbrella router skill, which then routes the agent to the right deeper skill for the task.

{% hint style="info" %}
If the installer only supports direct folder URLs, use the `flaunch/` folder rather than the repository root.
{% endhint %}

## What The Skill Helps With

The installed skill can guide an AI coding agent through:

* Launching A Token
* Building A Launchpad
* Choosing Between The API, SDK, And Manager Paths
* Working With Revenue, Staking, Or Buyback Managers

## Quick Start

{% stepper %}
{% step %}
### Install the skill

Install the umbrella `flaunch` skill so the agent can route itself to the right path.
{% endstep %}

{% step %}
### Pick the goal

The two most common outcomes are:

* Launch A Token
* Build A Launchpad
{% endstep %}

{% step %}
### Prompt the agent clearly

Use a direct prompt that includes the desired outcome.

{% code overflow="wrap" %}
```
Use flaunch: Help me choose whether this launchpad should use the API, SDK, or a custom manager.
```
{% endcode %}
{% endstep %}

{% step %}
### Follow the suggested path

For basic launch flows, the skill will usually route toward the API or SDK.

For custom treasury behavior, the skill will route toward the manager path.
{% endstep %}
{% endstepper %}

## Example Prompts

* `Use flaunch: Launch a memecoin on Base with the fastest possible path.`
* `Use flaunch: Help me choose whether this launchpad should use the API, SDK, or a custom manager.`
* `Use flaunch: Build a TypeScript launch flow with @flaunch/sdk on Base Sepolia.`
* `Use flaunch: Create a revenue manager through the Web2 API.`

## Build Paths

### Basic Path

Use the basic path when the goal is to:

* Launch A Token With Minimal Parameters
* Build A Standard Launchpad On Top Of The Existing API Or SDK
* Ship Quickly Without Custom Treasury Contracts

### Advanced Path

Use the advanced path when the product needs:

* Custom Treasury Or Revenue Behavior After Launch
* Buyback, Staking, Or Project-Specific Payout Logic
* Manager-Bound Launch Flows
* Wrapper Zaps That Enforce Manager Defaults During Launch

## FAQ

<details>

<summary>Do I need to install every skill separately?</summary>

No. The main install target is the umbrella `flaunch` skill. It routes the agent to the right deeper skill for the task.

</details>

<details>

<summary>What should I install for token creation?</summary>

Start with the umbrella skill. It will usually route to `skills/basic/token-creation/SKILL.md` for the fastest path.

</details>

<details>

<summary>What should I install for launchpad work?</summary>

Start with the umbrella skill. It will usually route to `skills/basic/token-launchpad/SKILL.md`, then into the API, SDK, or manager path as needed.

</details>

## Continue Reading

{% content-ref url="skill-files.md" %}
[skill-files.md](skill-files.md)
{% endcontent-ref %}

{% content-ref url="../getting-started/launch-a-launchpad.md" %}
[launch-a-launchpad.md](../getting-started/launch-a-launchpad.md)
{% endcontent-ref %}

## Vibe Showcase

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><a href="https://fleets.wtf">fleets.wtf</a></td><td>Take a picture, tokenize and earn revenue from trading.</td><td><a href="../.gitbook/assets/Screenshot 2025-05-02 at 11.56.34.png">Screenshot 2025-05-02 at 11.56.34.png</a></td></tr></tbody></table>
