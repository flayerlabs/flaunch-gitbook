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
* Porting An Existing Open-Source Game Into Game Mode

The main manager-development skill is `skills/core/manager/SKILL.md`.

Use the wrapper-zap skill only when the launch flow itself must enforce a manager policy.

## Port A Game Into Game Mode

If the goal is to take an existing open-source browser game and attach it to a token launch as a [Game Mode](../game-mode/README.md), install the porting skill directly:

```bash
npx skills add https://github.com/flayerlabs/flaunch-skills --skill port-game-mode
```

It walks the agent through the whole port: the licensing and provenance gates, a claims-bounded scoring economy, the coin metadata / chart / buy surfaces, the launch framing players expect (tutorial, practice lobby, round timer, leaderboard), a headless QA harness, and the ZIP you upload at [flaunch.gg/game-mode/create](https://flaunch.gg/game-mode/create).

For building a Game Mode from scratch, the `build-game-mode` skill ships inside the `@flayerlabs/gamemode-cli` npm package — see the [Game Mode SDK](../game-mode/game-mode-sdk.md).

## Quick Start

{% stepper %}
{% step %}
#### Install the skill

Install the umbrella `flaunch` skill so the agent can route itself to the right path.
{% endstep %}

{% step %}
#### Pick the goal

The two most common outcomes are:

* Launch A Token
* Build A Launchpad
{% endstep %}

{% step %}
#### Prompt the agent clearly

Use a direct prompt that includes the desired outcome.

{% code overflow="wrap" %}
```
Use flaunch: Help me choose whether this launchpad should use the API, SDK, or a custom manager.
```
{% endcode %}
{% endstep %}

{% step %}
#### Follow the suggested path

For basic launch flows, the skill will usually route toward the API or SDK.

For custom treasury behavior, the skill will route toward the manager path.
{% endstep %}
{% endstepper %}

## Example Prompts

* `Use flaunch: Launch a memecoin on Base with the fastest possible path.`
* `Use flaunch: Help me choose whether this launchpad should use the API, SDK, or a custom manager.`
* `Use flaunch: Build a TypeScript launch flow with @flaunch/sdk on Base Sepolia.`
* `Use flaunch: Create a revenue manager through the Web2 API.`
* `Use port-game-mode: Port this open-source browser game into a Flaunch Game Mode.`

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

{% content-ref url="../manager-types/custom-managers.md" %}
[custom-managers.md](../manager-types/custom-managers.md)
{% endcontent-ref %}
