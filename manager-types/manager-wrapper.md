---
icon: wand-magic-sparkles
---

# Manager Wrapper

A manager wrapper is a custom launch entrypoint that always routes launches through a chosen manager policy.

Instead of asking every caller to set manager parameters correctly by hand, the wrapper sets those defaults for them and sends the launch through `FlaunchZap`.

## What A Manager Wrapper Does

A manager wrapper can:

* Force The Intended Manager
* Set Default `permissions`
* Set Default `initializeData`
* Set Default `depositData`
* Ensure New Launches Bind To The Right Manager Path

This is useful when the launch flow itself must enforce the treasury behavior.

## Standard Pattern

The wrapper skill describes the usual flow:

1. Deploy the custom manager implementation
2. Approve it in `TreasuryManagerFactory`
3. Deploy a wrapper around `FlaunchZap`
4. Force the manager-related defaults inside the wrapper
5. Launch through the wrapper
6. Confirm the launched token is bound to the intended manager

## When To Use It

Use a manager wrapper when:

* The Launchpad Must Always Route Through One Manager Policy
* The Product Needs Fixed Treasury Defaults At Launch
* The Team Wants To Prevent Incorrect Manual Manager Setup

## Why It Is Advanced

This path is advanced because it depends on:

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
