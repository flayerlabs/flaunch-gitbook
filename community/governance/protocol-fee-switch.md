---
icon: toggle-off
---

# Protocol Fee Switch

## How the Fee Switch works

When disabled, the protocol fee share will just be distributed amongst the other fee beneficiaries (token creator, etc). When enabled, the fees will instead first be portioned off to capture an allocation for the protocol.

The fees will then be stored in the traditional `FeeEscrow` contract, ready to be claimed and utilised by governance.

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-29 at 15.14.07.png" alt=""><figcaption></figcaption></figure>

The fee calculation works like a waterfall, with each recipient capturing an amount before being passed to the next recipient.

<figure><img src="../../.gitbook/assets/carbon (17).png" alt=""><figcaption></figcaption></figure>

Once captured, the swap fee (1%) is distributed across 3 different recipients. One of these, the protocol fee, is optional.

<table><thead><tr><th width="129.5404052734375">Recipient</th><th>Notes</th></tr></thead><tbody><tr><td>Protocol</td><td>Optional fee set by proposal</td></tr><tr><td>Creator</td><td>Defined when the token is flaunched.</td></tr><tr><td>Bidwall</td><td>Defined when the token is flaunched. If the <code>BidWall</code> is not enabled for the token, then it will be directly sent to the <code>MemecoinTreasury</code>.<br><br>If the Memestream NFT has been burned, then this will be allocated to the protocol.</td></tr></tbody></table>

If we assume that a swap takes place for 100 eth and that the protocol fee switch was enabled and set to 5%, then this would result in the following fee distribution:

<table><thead><tr><th width="129.5404052734375">Recipient</th><th width="129.744140625">Amount</th><th>Calculation</th></tr></thead><tbody><tr><td>Swap Fee</td><td>1 eth</td><td><code>100 / 100 * 1</code></td></tr><tr><td>Protocol</td><td>0.0475 eth</td><td><code>(1 - 0.05) / 100 * 5</code></td></tr><tr><td>Creator</td><td>0.722 eth</td><td><code>(1 - 0.05 - 0.0475) / 100 * 80</code></td></tr><tr><td>Bidwall</td><td>0.1805 eth</td><td><code>1 - 0.05 - 0.0475 - 0.722</code></td></tr></tbody></table>

## How is the fee switch enabled?

Each Flaunch `PositionManager` contract has it's own `FeeDistributor` inheritance. This allows the protocol fee to be set explicitly on each `PositionManager`. The fee switch will be enabled when this value is not 0.

{% hint style="warning" %}
The protocol fee must range from between 0% and 10%.
{% endhint %}

The value is represented with 2 decimal places, so a value of `550` would represent a protocol fee of 5.5%, taken from the overall swap fee.

<figure><img src="../../.gitbook/assets/carbon (16).png" alt=""><figcaption></figcaption></figure>

To enable the fees, the governance would need to create a structured proposal that calls each PositionManager and makes the above call to set a protocol fee value.

## Where are these fees stored?

Fees allocated to the protocol will be stored in the same `FeeEscrow` contract in the same way that fees are allocated to creators. These can be claimed by, and only by, the `ProtocolFeeRecipient` at any time. This claim call will be callable by governance to fund any proposals made.

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>FeeEscrow</td><td><code>0x72e6f7948b1B1A343B477F39aAbd2E35E6D27dde</code></td></tr><tr><td>ProtocolFeeRecipient</td><td><code>0x1150c53eB4cE3aDE47808D1D1Ac9636b774eE079</code></td></tr></tbody></table>

{% hint style="warning" %}
There is a singular exception to this. The first PositionManager contract deployed for Flaunch set the protocol fee recipient as the **Flayer Foundation** multisig. More information regarding this can be found below.
{% endhint %}

## Claiming Fees for proposal use

To claim protocol fees, the `L2Owner` can call `claim` against the `ProtocolFeeRecipient` contract. This provides a simplified flow to call all fees. All fees, like any other fee allocation on Flaunch, are provided exclusively in native ETH.

### A caveat in fee claiming

Due to a contract upgrade during the start of the Flaunch project, if the first PositionManager (`PositionManager1`) enables protocol fees then they will be allocated immutably to the **Flayer Foundation** multisig.

{% hint style="info" %}
At time of writing there are **4755 flaunched tokens** on this `PositionManager1`.
{% endhint %}

For legal purposes, the foundation has **no** obligation to use these protocol fees for buybacks (but with obligations to be positive-sum for everything the Foundation builds). As the `PositionManager1` protocol fee recipient cannot be changed, any fees generated from them will therefore be true to this statement.

All future PositionManagers (post `PositionManager1`) _will_ be utilized at the discretion of $FLAY token holders to action on through onchain governance.

### Position Manager Addresses (Chain ID: 8453)

<table><thead><tr><th width="229.1016845703125">Contract</th><th>Address</th></tr></thead><tbody><tr><td>PositionManager1</td><td><code>0x6A53F8b799bE11a2A3264eF0bfF183dCB12d9571</code></td></tr><tr><td>PositionManager2</td><td><code>0xB4512bf57d50fbcb64a3adF8b17a79b2A204C18C</code></td></tr></tbody></table>

## Are the fees safe?

The ETH allocated to the protocol are securely stored onchain in our battle-tested `FeeEscrow` contracts.

This process has been extensively audited and shows no concerns.
