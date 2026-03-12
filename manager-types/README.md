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

{% content-ref url="erc721ownerfeesplitmanager.md" %}
[erc721ownerfeesplitmanager.md](erc721ownerfeesplitmanager.md)
{% endcontent-ref %}

## Which Manager Should I Use?

### Revenue Manager

Use this when a product wants to take a protocol share while still routing the creator share correctly.

### Address Fee Split Manager

Use this when revenue should be split across multiple wallets or recipients.

### Staking Manager

Use this when value should flow toward stakers of an ecosystem token.

### ERC721 Owner Fee Split Manager

Use this when revenue should be distributed based on NFT ownership.

## Factory Addresses

<table><thead><tr><th width="149.55841064453125">Chain</th><th>Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0x48af8b28DDC5e5A86c4906212fc35Fa808CA8763</code></td></tr><tr><td>Base Sepolia</td><td><code>0xd2f3c6185e06925dcbe794c6574315b2202e9ccd</code></td></tr></tbody></table>

## Creating Your Own Manager

If you need bespoke logic, you can build your own manager and deposit tokens into it directly.

If you want to share it with the community, [get in touch](https://x.com/buildonflaunch). Flaunch can help fund an audit and approve the manager on the Token Manager Factory.
