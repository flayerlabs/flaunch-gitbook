---
description: >-
  The Flaunch protocol has an option for a protocol fee to be enabled. When
  enabled, a percentage of swap fees are managed and used through Tally
  proposals via $FLAY holder voting.
hidden: true
noIndex: true
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
2. Managing token assets earned from fees

## How can I take part in governance?

You can take part in governance by just holding $FLAY token and heading over to [Tally.xyz](https://www.tally.xyz/gov/flayer) to vote on any existing or upcoming proposals. If you hold sufficient $FLAY token, you can also create proposals to be voted on.

{% hint style="warning" %}
Delegation and voting must be undertaken on the Ethereum Mainnet. If you need to bridge your $FLAY tokens from Base to Ethereum Mainnet, then we [recommend using Superbridge](https://superbridge.app/?fromChainId=8453\&toChainId=1\&tokenAddress=0xF1A7000000950C7ad8Aff13118Bb7aB561A448ee).
{% endhint %}

## Creating A Proposal

### How do I create a proposal?

Creating a proposal is relatively simple. We have outlined a 5 step flow to creating your proposal. You can also pick up some useful approaches by reviewing existing, successful proposals.\


1. View the [current Flayer proposals](https://www.tally.xyz/gov/flayer/proposals) and click to create a new proposal.
2. Ensure that at least 1 million $FLAY is delegated to your address in order to submit a proposal, or find a delegate who has enough delegated $FLAY to meet the proposal threshold to propose on your behalf.
3. Create your proposal. The Tally interface can help to create a series of structured calls to be executed. If writing the calldata yourself, please review the logic with a qualified community member prior to posting the proposal. We would also recommend ensuring that `ProtocolFeeRecipient.claim` is the first call in your proposal to ensure that the full amount of ETH is available to execute with.
4. Once the proposal has been submitted a two day voting delay will start. After the voting delay is finished, a six day voting period begins.
5. If the proposal passes successfully (with a 40 million vote quorum) then a two day timelock will follow before the proposed code is executed.

{% hint style="info" %}
For more information about Tally and their platform, check out their [documentation](https://docs.tally.xyz/).
{% endhint %}

### What does the proposal need to include?

Your proposal should include:\


* The reasoning behind your proposal
* The expectation and justification of cost, or outgoing tokens, from your transaction(s)
* The expected benefits to the $FLAY community
* You may also want to include your personal background and experience in your proposal

### What should the proposal be?

As a $FLAY holder, it would be our recommendation that any proposal should have the intention of benefitting the Flayer ecosystem in the longterm. Any proposals that don't align with this have a high likelihood of being voted down.

### What happens when a proposal is executed?

1. Proposals and votes are made on Ethereum Mainnet through delegation of $FLAY token.
2. If proposal passes, the `TimelockController` on Ethereum Mainnet stores the executable calldata set by the proposal.
3. After the timelock of 2 days has passed, the execution can be called on the Ethereum Mainnet `TimelockController`. This executes the proposal arbritrary calldata against the `L2Owner` contract on Base Mainnet.

## How the Fee Switch works

When disabled, the protocol fee share will just be distributed amongst the other fee beneficiaries (token creator, etc). When enabled, the fees will instead first be portioned off to capture an allocation for the protocol.

The fees will then be stored in the traditional `FeeEscrow` contract, ready to be claimed and utilised by governance.

<figure><img src="../.gitbook/assets/Screenshot 2025-08-26 at 12.45.50.png" alt=""><figcaption></figcaption></figure>

The fee calculation works like a waterfall, with each recipient capturing an amount before being passed to the next recipient.

<figure><img src="../.gitbook/assets/carbon (17).png" alt=""><figcaption></figcaption></figure>

The fee priority is:

> Swap Fee -> Referrer -> Protocol -> Creator -> BidWall

For example, imagine we have a fee distribution of:

<table><thead><tr><th width="129.5404052734375">Recipient</th><th width="129.744140625">Amount</th><th>Notes</th></tr></thead><tbody><tr><td>Swap Fee</td><td>1%</td><td>This is a global value</td></tr><tr><td>Referrer</td><td>5%</td><td>If a referrer has been passed into a swap, then they will receive an optional percentage of the swap. If no referrer is set, then this allocation is bypassed.</td></tr><tr><td>Protocol</td><td>5%</td><td></td></tr><tr><td>Creator</td><td>80%</td><td>Defined when the token is flaunched.</td></tr><tr><td>Bidwall</td><td>20%</td><td>Defined when the token is flaunched. If the <code>BidWall</code> is not enabled for the token, then it will be directly sent to the <code>MemecoinTreasury</code>.<br><br>If the Memestream NFT has been burned, then this will be allocated to the protocol.</td></tr></tbody></table>

If we assume that a swap takes place for 100 eth, then this would result in the following fee distribution:

<table><thead><tr><th width="129.5404052734375">Recipient</th><th width="129.744140625">Amount</th><th>Calculation</th></tr></thead><tbody><tr><td>Swap Fee</td><td>1 eth</td><td>100 / 100 * 1</td></tr><tr><td>Referrer</td><td>0.05 eth</td><td>1 / 100 * 5</td></tr><tr><td>Protocol</td><td>0.0475 eth</td><td>(1 - 0.05) / 100 * 5</td></tr><tr><td>Creator</td><td>0.722 eth</td><td>(1 - 0.05 - 0.0475) / 100 * 80</td></tr><tr><td>Bidwall</td><td>0.1805 eth</td><td>1 - 0.05 - 0.0475 - 0.722</td></tr></tbody></table>

### How is the fee switch enabled?

Each Flaunch `PositionManager` contract has it's own `FeeDistributor` inheritance. This allows the protocol fee to be set explicitly on each `PositionManager`. The fee switch will be enabled when this value is not 0, and is valid when is set to a value `<= 1000` (note: this is the `MAX_PROTOCOL_ALLOCATION` value).

The value is represented with 2 decimal places, so a value of `550` would represent a protocol fee of 5.5%, taken from the overall swap fee.

<figure><img src="../.gitbook/assets/carbon (16).png" alt=""><figcaption></figcaption></figure>

To enable the fees, the governance would need to create a structured proposal that calls each PositionManager and makes the above call to set a protocol fee value.

### Where are these fees stored?

Fees allocated to the protocol will be stored in the same `FeeEscrow` contract in the same way that fees are allocated to creators. These can be claimed by, and only by, the `ProtocolFeeRecipient` at any time. This claim call will be callable by governance to fund any proposals made.

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>FeeEscrow</td><td><code>0x72e6f7948b1B1A343B477F39aAbd2E35E6D27dde</code></td></tr><tr><td>ProtocolFeeRecipient</td><td><code>0x1150c53eB4cE3aDE47808D1D1Ac9636b774eE079</code></td></tr></tbody></table>

{% hint style="warning" %}
There is a singular exception to this. The first PositionManager contract deployed for Flaunch set the protocol fee recipient as the **Flayer Foundation** multisig. More information regarding this can be found below.
{% endhint %}

### Claiming Fees for proposal use

To claim protocol fees, the `L2Owner` can call `claim` against the `ProtocolFeeRecipient` contract. This provides a simplified flow to call all fees. All fees, like any other fee allocation on Flaunch, are provided exclusively in native ETH.

Due to a contract upgrade during the start of the Flaunch project, if the first PositionManager (`PositionManager1`) enables protocol fees then they will be allocated immutably to the **Flayer Foundation** multisig. At time of writing there are 4755 flaunched tokens on this PositionManager.

For legal purposes, the foundation has **no** obligation to use these protocol fees for buybacks (but with obligations to be positive-sum for everything the Foundation builds).

All future PositionManagers (post `PositionManager1`) _will_ be utilized at the discretion of $FLAY token holders to action on through onchain governance.

#### Position Manager Addresses (Chain ID: 8453)

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>PositionManager1</td><td><code>0x6A53F8b799bE11a2A3264eF0bfF183dCB12d9571</code></td></tr><tr><td>PositionManager2</td><td><code>0xB4512bf57d50fbcb64a3adF8b17a79b2A204C18C</code></td></tr></tbody></table>

### Are the fees safe?

The ETH allocated to the protocol are securely stored onchain in our battle-tested `FeeEscrow` contracts.

This process has been extensively audited and shows no concerns.

## Contract Addresses

#### Ethereum Mainnet (Chain ID: 1)

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>Governor</td><td><code>0x8BA5eA8c8b1Aafe9dbcb7a36737AcfAd6afa5D38</code></td></tr><tr><td>Token</td><td><code>0xF1A7000000950C7ad8Aff13118Bb7aB561A448ee</code></td></tr><tr><td>Timelock</td><td><code>0x6c4c0CD7E0E5eeFfbd77AAfe1820d3b9B1ef27b0</code></td></tr></tbody></table>

#### Base Mainnet (Chain ID: 8453)

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>L2Owner</td><td><code>0x000000000Bb63D5c070d0D5791517886a4d8C545</code></td></tr></tbody></table>
