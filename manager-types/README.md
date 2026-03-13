# Managers

Managers control what happens to value after launch.

They sit on the treasury side of Flaunch and define how revenue rights are held, routed, claimed, or shared. This is what makes Flaunch useful not just for token launches, but also for launchpads, group products, and custom treasury systems.

## What A Manager Does

A manager can be used to:

* Route Revenue To One Or More Recipients
* Control Claim And Payout Behavior
* Support Revenue, Staking, Or Buyback-Style Flows
* Apply Project-Specific Treasury Logic After Launch

## When To Use A Manager

Use a manager when the product needs more than a standard launch.

Common cases:

* A Launchpad That Takes A Share Of Revenue
* A Group That Routes Value To Stakers Or Buybacks
* A Team Treasury With Multiple Recipients
* A Project That Needs Custom Post-Launch Ownership Logic

## Manager Types

Flaunch currently documents the following manager types:

{% content-ref url="revenuemanager.md" %}
[revenuemanager.md](revenuemanager.md)
{% endcontent-ref %}

{% content-ref url="addressfeesplitmanager.md" %}
[addressfeesplitmanager.md](addressfeesplitmanager.md)
{% endcontent-ref %}

{% content-ref url="stakingmanager.md" %}
[stakingmanager.md](stakingmanager.md)
{% endcontent-ref %}

{% content-ref url="custom-managers.md" %}
[custom-managers.md](custom-managers.md)
{% endcontent-ref %}

## Which Manager Should I Use?

### Revenue Manager

Use this when a product wants to take a protocol share while still routing the creator share correctly.

### Address Fee Split Manager

Use this when revenue should be split across multiple wallets or recipients.

### Staking Manager

Use this when value should flow toward stakers of an ecosystem token.

### Custom Managers

Use this when the project needs treasury behavior that is not covered by the standard manager set.

## Creating Your Own Manager

If you need bespoke logic, you can build your own manager and deposit tokens into it directly.

If you want to share it with the community, [get in touch](https://x.com/buildonflaunch). Flaunch can help fund an audit and approve the manager on the Token Manager Factory.
