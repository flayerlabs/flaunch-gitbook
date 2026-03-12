---
icon: wand-magic-sparkles
---

# Custom Managers

Custom managers let builders define treasury behavior outside the standard approved manager set.

You do not need an approved manager just to build locally, integrate, or launch through your own custom flow.

Approval only matters when a manager needs to be surfaced through the official factory and official supported manager flows.

## What A Custom Manager Is

A custom manager is a treasury-side contract that receives or controls the Flaunch revenue rights for a launched token.

Builders use custom managers when they need:

* Custom Fee Routing
* Custom Claim Logic
* Custom Escrow Or Treasury Behavior
* Product-Specific Policy After Launch

## How It Connects To Flaunch

A custom manager can connect to Flaunch in two common ways:

1. Through a manager-aware launch flow that passes manager data during launch
2. Through a wrapper around `FlaunchZap` that enforces the manager path automatically

The wrapper path is useful when the launch flow itself must guarantee the correct manager setup for every launch.

## Standard Managers vs Custom Managers vs Wrappers

### Standard managers

Use a standard manager when one of the existing manager types already matches the product.

This is the simplest path when you want a supported manager flow with less custom contract work.

### Custom managers

Use a custom manager when the treasury behavior itself is unique to the product.

This is the right path when the post-launch logic cannot be expressed through the existing manager set.

### Wrappers around `FlaunchZap`

Use a wrapper when the launch entrypoint itself needs to enforce a chosen manager policy for every launch.

This is not the first step for most builders. It is an advanced pattern on top of the custom manager model.

## What A Wrapper Does

A wrapper around `FlaunchZap` can:

* Force The Intended Manager
* Set Default `permissions`
* Set Default `initializeData`
* Set Default `depositData`
* Ensure New Launches Bind To The Right Manager Path

This is useful when a product does not want users or integrators to configure manager parameters by hand.

## Standard Pattern

The wrapper skill describes the usual advanced flow:

1. Deploy the custom manager implementation
2. Approve it in `TreasuryManagerFactory`
3. Deploy a wrapper around `FlaunchZap`
4. Force the manager-related defaults inside the wrapper
5. Launch through the wrapper
6. Confirm the launched token is bound to the intended manager

## Approval And Official Support

Approval is only required when a manager should be deployed through the official factory or treated as part of the official supported manager set.

If you are building your own custom integration path, approval is not required just to develop or use the manager.

## When To Use It

Use this page when the project needs:

* A Custom Treasury Contract
* A Manager-Aware Launch Flow
* A Clear Path Between Standard And Custom Manager Behavior
* A Wrapper That Enforces Manager Defaults At Launch

## Why It Is Advanced

The wrapper path is advanced because it depends on:

* Manager Internals
* Launch Orchestration
* Post-Launch Manager Initialization Or Deposit Logic
* Manager-Specific Testing

It should only be used when the launch flow itself needs to enforce the manager behavior.

## Required Validation

The wrapper skill calls for tests that prove:

* The Happy Path Binds The Intended Manager
* Invalid Params Or Unauthorized Configurations Fail
* Post-Launch Init Or Deposit Logic Works
* The Payout And Accounting Policy Is Actually Enforced

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
