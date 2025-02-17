---
icon: chart-pyramid
---

# Fee Distribution

This works in a waterfall approach, with a percentage taking a share before passing the potential allocation on to the next. This means that the percentages listed don't need to equal 100% as they instead take a percentage from the amount passed to it.

{% hint style="info" %}
**Fee Waterfall:** Swap Fee >> Referrer >> Protocol >> Creator >> Bid Wall
{% endhint %}

{% stepper %}
{% step %}
### Swap Fee

When a swap is made, a 1% swap fee is taken.

Fees captured in memecoin will be sent to the Internal Swap Pool that will frontrun Uniswap in future swaps to instead use that pool. This will convert memecoin into flETH which is then distributed to through the fees.

Fees captured in flETH (either from the swap or via the Internal Swap Pool's output) will be distributed in the following waterfall approach.
{% endstep %}

{% step %}
### Referrer Fee

If a referrer is specified during the swap, then an amount will be taken from the swap fee. This fee will be sent to a ReferrerEscrow, or will be sent directly to the referrer depending on the protocol state.
{% endstep %}

{% step %}
### Protocol Fee

The protocol fee is a value set between 0% - 10%, which can be updated via an onchain governance call by [$FLAY](https://dexscreener.com/base/0xf1a7000000950c7ad8aff13118bb7ab561a448ee) token holders.

{% hint style="success" %}
This is currently set at 0%, so all fees will be passed on from this point to Dev Revs and the Bid Wall.
{% endhint %}
{% endstep %}

{% step %}
### Dev Revs / Creator

The dev revs / creator fees set up by the memestream creator at point of flaunching determines the amount of flETH that is allocated directly to the memestream owner. This can be claimed at any time via an onchain call.
{% endstep %}

{% step %}
### Community / Bid Wall

All remaining fees will be sent to the community by passing it to the BidWall.
{% endstep %}
{% endstepper %}

### What if the Bid Wall is disabled?

If the memestream owner disables the Bid Wall, funds will instead be sent to the Memecoin Treasury. This means that the ETH fees allocated to the Community will be sent to the Memecoin Treasury and the memestream owner can choose a Treasury Action to run perform against the fees collected.

### How are fees assigned to a pool?

Fees are defined to provide the first 3 steps of the waterfall. The remaining 2 steps are defined per memecoin by the dev / community split decided by the creator.

```solidity
/**
 * @member swapFee The amount of the transaction taken as fee
 * @member referrer The percentage that the referrer will receive
 * @member protocol The percentage that the protocol will receive
 * @member active If a FeeDistribution struct has been set for the mapping
 */
struct FeeDistribution {
    uint24 swapFee;
    uint24 referrer;
    uint24 protocol;
    bool active;
}
```

Fees are assigned globally across the Flaunch protocol, but there is the possibility for fees to be set per specific pool. For this reason, the `FeeDistribution` struct should be found by querying the `PositionManager.getPoolFeeDistribution` function and passing in the `PoolId` of the memecoin.

```solidity
/**
 * Gets the distribution for a pool by checking to see if a pool has it's own FeeDistribution. If
 * it does then this is used, but if it isn't then it will fallback on the global FeeDistribution.
 *
 * @param _poolId The PoolId being updated
 *
 * @return feeDistribution_ The FeeDistribution applied to the pool
 */
function getPoolFeeDistribution(PoolId _poolId) public view returns (FeeDistribution memory feeDistribution_) {
    feeDistribution_ = (poolFeeDistribution[_poolId].active) ? poolFeeDistribution[_poolId] : feeDistribution;
}
```

### What happens if the Memestream ERC721 is burned?

If the memestream owner burns their ERC721, then the dev revs share will instead be allocated to the Bid Wall. However, if the Bid Wall is disabled when the memestream ERC721 is burned, then the treasury would not have someone to trigger it's actions and the fees will instead be sent to the protocol.

For this reason, it is important that the Bid Wall is enabled before the memestream owner burns their ERC721.
