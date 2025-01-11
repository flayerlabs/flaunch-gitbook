# Progressive Bid Wall

## Introduction

This hook allows us to create a single sided liquidity position (Plunge Protection) that is placed 1 tick below spot price, using the ETH fees accumulated. After each deposit into the BidWall the position is rebalanced to ensure it remains 1 tick below spot. This spot will be determined by the tick value before the triggering swap.

After a swap is made against a Flaunch pool, if a sufficient fee threshold has been reached, we distribute fees across our expected recipients. The BidWall is, unless disabled by the creator, one of these recipients.

If the deposit passes a set threshold, the liquidity position held by the contract for the pool will be rebalanced:

* The existing position will withdraw any remaining ETH and any memecoin that was sold into it
* A new position will be created with the withdrawn ETH + the pending ETH that has been deposited at the tick directly below the current position
* The memecoin that was withdrawn is sent to the memecoin treasury

## Public Calls

### poolInfo

This structure mapping gets information for a pools BidWall.

```solidity
/**
 * Maps our poolId to the `PoolInfo` struct for bidWall data.
 */
function poolInfo(PoolId _poolId) public view returns (PoolInfo) {
  // ..
}
```

The structure of this returned information is below:

```solidity
/**
 * Stores the BidWall information for a specific pool.
 *
 * @member disabled If the BidWall is disabled for the pool
 * @member initialized If the BidWall has been initialized
 * @member tickLower The current lower tick of the BidWall
 * @member tickUpper The current upper tick of the BidWall
 * @member pendingETHFees The amount of ETH fees waiting to be put into the BidWall until threshold is crossed
 * @member cumulativeSwapFees The total amount of swap fees accumulated for the pool
 */
struct PoolInfo {
    bool disabled;
    bool initialized;
    int24 tickLower;
    int24 tickUpper;
    uint pendingETHFees;
    uint cumulativeSwapFees;
}
```

### isBidWallEnabled

Checks if the BidWall is currently enabled for the Pool. This can be updated by the memecoin creator by calling [setDisabledState](progressive-bid-wall.md#setdisabledstate).

```solidity
/**
 * Helper function that checks if a pool's BidWall is enabled or disabled.
 *
 * @param _poolId The pool ID to check
 *
 * @return bool Set to `true` if the hook is enabled, `false` if it is disabled
 */
function isBidWallEnabled(PoolId _poolId) public view returns (bool) {
  // ..
}
```

### position

Retrieves the current liquidity position held by the BidWall. The `amount0` and `amount1` values correlate to the PoolKey `token0` and `token1`. The `pendingEth` value shows the amount of ETH waiting to be added to the liquidity position when the threshold is next met.

```solidity
/**
 * Retrieves the current position held by the BidWall.
 *
 * @param _poolId The `PoolId` to check the {BidWall} position of
 *
 * @return amount0_ The {BidWall} token0 position
 * @return amount1_ The {BidWall} token1 position
 * @return pendingEth_ The amount of ETH pending to be depositted into the {BidWall}
 */
function position(PoolId _poolId) public view returns (uint amount0_, uint amount1_, uint pendingEth_) {
  // ..
}
```

### setDisabledState

The BidWall can be enabled or disabled via the `BidWall` by calling the `setDisabledState` function. When the BidWall is disabled, the pending and cumulitive fees will be reset to zero and any tokens held, as well as future tokens, will be sent to the Memecoin Treasury.

This can only be called by the owner of the memecoin ERC721, signifying ownership.

```solidity
/**
* Allows the BidWall to be enabled or disabled for the {PoolKey}.
*
* If disabled, future swap fees will be transferred directly to the respective
* {MemecoinTreasury} address instead of the BidWall.
*
* @dev This can only be called by the creator of the memecoin.
*
* @param _key The PoolKey that is being updated
* @param _disable If the BidWall is being disabled (true) or enabled (false)
*/
function setDisabledState(PoolKey memory _key, bool _disable) external {
  // ..
}
```

## Notes

* The BidWall will not receive tokens during the FairLaunch period, and will instead receive all fee allocation in a single transaction upon the Fair Launch period ending.
* The BidWall only receives ETH and not memecoin tokens, as these are first exchanged via the [Internal Swap Pool](internal-swap-pool.md).

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/bidwall/BidWall.sol" %}
Hook contract
{% endembed %}
