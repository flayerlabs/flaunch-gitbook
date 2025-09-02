---
description: >-
  The Flaunch protocol has an option for a protocol fee to be enabled. When
  enabled, a percentage of swap fees are managed and used through Tally
  proposals via $FLAY holder voting.
icon: person-sign
---

# Proposals (Tally.xyz)

## Introduction

$FLAY token holders can propose, vote on and execute onchain proposals through governance. We use [Tally.xyz](https://www.tally.xyz/gov/flayer) to manage our governance proposals and execution.

{% hint style="info" %}
Our $FLAY token implements the [ERC20Votes](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v5.0/contracts/token/ERC20/extensions/ERC20Votes.sol) standard to power our governance.
{% endhint %}

The token governance has two key areas of control in the Flaunch protocol:

1. Control of enabling and disabling a protocol fee
2. Utilizing ETH earned from protocol fees

## Governance Resources

{% content-ref url="proposals-and-voting.md" %}
[proposals-and-voting.md](proposals-and-voting.md)
{% endcontent-ref %}

{% content-ref url="protocol-fee-switch.md" %}
[protocol-fee-switch.md](protocol-fee-switch.md)
{% endcontent-ref %}

## Contract Addresses

#### Ethereum Mainnet (Chain ID: 1)

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>Governor</td><td><code>0x8BA5eA8c8b1Aafe9dbcb7a36737AcfAd6afa5D38</code></td></tr><tr><td>Token</td><td><code>0xF1A7000000950C7ad8Aff13118Bb7aB561A448ee</code></td></tr><tr><td>Timelock</td><td><code>0x6c4c0CD7E0E5eeFfbd77AAfe1820d3b9B1ef27b0</code></td></tr></tbody></table>

#### Base Mainnet (Chain ID: 8453)

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>L2Owner</td><td><code>0x000000000Bb63D5c070d0D5791517886a4d8C545</code></td></tr></tbody></table>
