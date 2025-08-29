---
icon: hand
---

# Proposals and Voting

## How can I take part in governance?

You can take part in governance by just holding $FLAY token and heading over to [Tally.xyz](https://www.tally.xyz/gov/flayer) to vote on any existing or upcoming proposals. If you hold sufficient $FLAY token, you can also create proposals to be voted on.

{% hint style="warning" %}
Delegation and voting must be undertaken on the Ethereum Mainnet. If you need to bridge your $FLAY tokens from Base to Ethereum Mainnet, then we [recommend using Superbridge](https://superbridge.app/?fromChainId=8453\&toChainId=1\&tokenAddress=0xF1A7000000950C7ad8Aff13118Bb7aB561A448ee). Please note that this takes 7 days.
{% endhint %}

## How do I create a proposal?

Creating a proposal is relatively simple. We have outlined a 5 step flow to creating your proposal. You can also pick up some useful approaches by reviewing existing, successful proposals.\


1. View the [current Flayer proposals](https://www.tally.xyz/gov/flayer/proposals) and click to create a new proposal.
2. Ensure that at least 1 million $FLAY is delegated to your address in order to submit a proposal, or find a delegate who has enough delegated $FLAY to meet the proposal threshold to propose on your behalf.
3. Create your proposal. The Tally interface can help to create a series of structured calls to be executed. If writing the calldata yourself, please review the logic with a qualified community member prior to posting the proposal. We would also recommend ensuring that `ProtocolFeeRecipient.claim` is the first call in your proposal to ensure that the full amount of ETH is available to execute with.
4. Once the proposal has been submitted a two day voting delay will start. After the voting delay is finished, a six day voting period begins.
5. If the proposal passes successfully (with a 40 million vote quorum) then a two day timelock will follow before the proposed code is executed.

{% hint style="info" %}
For more information about Tally and their platform, check out their [documentation](https://docs.tally.xyz/).
{% endhint %}

## What does the proposal need to include?

Your proposal should include:\


* The reasoning behind your proposal
* The expectation and justification of cost, or outgoing tokens, from your transaction(s)
* The expected benefits to the $FLAY community
* You may also want to include your personal background and experience in your proposal

## What should the proposal be?

As a $FLAY holder, it would be our recommendation that any proposal should have the intention of benefitting the Flayer ecosystem in the longterm. Any proposals that don't align with this have a high likelihood of being voted down.

## How long does a proposal last?

When a proposal threshold is reached, the voting period will start. This will allow for a total of 6 days to vote on the proposal. If the proposal reaches a quorum and passes, then there will be a 2 day grace window until the proposal can be executed.

<table><thead><tr><th width="213.06768798828125">Key</th><th>Value</th></tr></thead><tbody><tr><td>Proposal Threshold</td><td>1 million</td></tr><tr><td>Quorum Needed</td><td>40 million</td></tr><tr><td>Proposal Delay</td><td>2 days</td></tr><tr><td>Voting Period</td><td>6 days</td></tr></tbody></table>

{% hint style="info" %}
Please note, it takes 7 days for $FLAY tokens to bridge from Base to Ethereum Mainnet. It is recommended that tokens are held on Ethereum Mainnet in advance, rather than as a reactive response to a proposal.
{% endhint %}

## What happens when a proposal is executed?

1. Proposals and votes are made on Ethereum Mainnet through delegation of $FLAY token.
2. If proposal passes, the `TimelockController` on Ethereum Mainnet stores the executable calldata set by the proposal.
3. After the timelock of 2 days has passed, the execution can be called on the Ethereum Mainnet `TimelockController`. This executes the proposal arbritrary calldata against the `L2Owner` contract on Base Mainnet.

## How are fees raised for proposals?

Fees are earned from an optional protocol fee switch. More information about how this works, and how it can be enabled, can be found in the resource below.

{% content-ref url="protocol-fee-switch.md" %}
[protocol-fee-switch.md](protocol-fee-switch.md)
{% endcontent-ref %}
