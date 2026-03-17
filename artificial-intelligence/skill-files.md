---
description: Understand how the Flaunch skill repository is organized.
icon: folder-tree
---

# Understanding Skill Files

The Flaunch skills repository is organized to help AI coding agents choose the right path quickly.

At the top level, Flaunch supports two primary outcomes:

* Launch A Token
* Build A Launchpad

Those outcomes can be solved through a lighter path or a more advanced path depending on how much control the product needs after launch.

## The Three Skill Groups

The repository is organized into three groups:

* `skills/basic`: Fast paths for common tasks
* `skills/core`: Source-of-truth guidance for the main integration surfaces
* `skills/advanced`: Custom contract and wrapper flows

## What Each Group Is For

### Basic

Use the basic skills when you want the shortest route to a finished result.

Common examples:

* Launch A Token With Minimal Inputs
* Scope A Standard Launchpad
* Choose Between The API And SDK

### Core

Use the core skills when the agent needs full guidance for one Flaunch surface.

Core skills cover:

* The Web2 API
* The SDK
* Manager integrations

### Advanced

Use the advanced skills when the product needs custom protocol behavior.

Advanced skills cover:

* Custom manager implementation
* Wrapper zaps
* Project-specific treasury policy

## Recommended Reading Order

1. Start with the umbrella `flaunch` skill.
2. Let it route to the smallest matching skill.
3. Move into `core` or `advanced` only when the product requires more control.

## Key Skill Files

The main files to know are:

* `flaunch/SKILL.md`
* `skills/basic/token-creation/SKILL.md`
* `skills/basic/token-launchpad/SKILL.md`
* `skills/core/api/SKILL.md`
* `skills/core/sdk/SKILL.md`
* `skills/core/manager/SKILL.md`
* `skills/advanced/manager-builder/SKILL.md`
* `skills/advanced/manager-zap-wrapper/SKILL.md`

## When To Use Which Skill

Use `token-creation` when the goal is to launch a token with the fewest decisions.

Use `token-launchpad` when the goal is to build a launchpad and the first question is whether the API, SDK, or a manager path is required.

Use the manager skills when the launchpad needs custom treasury or payout behavior after launch.

## Continue Reading

{% content-ref url="vibe-guide.md" %}
[vibe-guide.md](vibe-guide.md)
{% endcontent-ref %}

{% content-ref url="../getting-started/launch-a-launchpad.md" %}
[launch-a-launchpad.md](../getting-started/launch-a-launchpad.md)
{% endcontent-ref %}
