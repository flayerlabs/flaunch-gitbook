# Fair Launch

## Introduction

The Fair Launch hook allows tokens that have just been flaunched to be purchased at a consistent token price before it is affected by liquidity supply.

This creates a time window right after the token is launched that keeps the token at the same price in a single tick position. Fees earned from this are kept within the position and cannot be sold into until the fair launch window has finished.

Once the FairLaunch period has ended, the ETH raised and the remaining tokens areboth deployed into a Uniswap position to facilitate ongoing transactions and create a price discovery.

{% hint style="info" %}
The Fair Launch period runs for 30 minutes.
{% endhint %}

When we are filling from our Fair Launch position, we will always be buying tokens with ETH. The amount specified that is passed in, however, could be positive or negative.

The positive / negative flag will require us to calculate the amount the user will get in a different way. Positive: How much ETH it costs to get amount. Negative: How many tokens I can get for amount.

The amount requested **can** exceed the Fair Launch position, but we will additionally have to call `_closeFairLaunchPosition` to facilitate it during this call. This will provide additional liquidity before the swap actually takes place.

If the user has requested more tokens than are available in the fair launch, then we need to strip back the amount that we can fulfill.

```solidity
if (tokensOut > info.supply) {
    // Calculate the percentage of tokensOut relative to the threshold and reduce the `ethIn`
    // value by the same amount. There may be some slight accuracy loss, but it's all good.
    uint percentage = info.supply * 1e18 / tokensOut;
    ethIn = (ethIn * percentage) / 1e18;

    // Update our `tokensOut` to the supply limit
    tokensOut = info.supply;
}
```

The Fair Launch window can end in one of two ways:

1. The Fair Launch runs out of time, without tokens selling out during the 30 minutes allocation
2. The Fair Supply token supply has been purchased to point of exhaustion

When the FairLaunch position ends, it creates two Uniswap V4 positions:

* A wide range memecoin position from just above current tick to the max value
* A single tick ETH position just below the current tick

## Public Calls

### inFairLaunchWindow

Checks if the PoolKey is within the fair launch window.

```solidity
/**
 * Checks if the {PoolKey} is within the fair launch window.
 *
 * @param _poolId The ID of the PoolKey
 *
 * @return If the {PoolKey} is within the fair launch window
 */
function inFairLaunchWindow(PoolId _poolId) public view returns (bool) {
  // ..
}
```

### fairLaunchInfo

Call the `FairLaunchInfo` struct for a pool, which provides information about the Fair Launch activity of a pool. After the Fair Launch has finished, the struct data will still be present for historical purposes, but the `closed` boolean will be marked `true` and if the period ended early, then the `endsAt` will have also been updated to reflect the actual time that the period closed.

```solidity
/**
 * Helper function to call the FairLaunchInfo struct for a pool.
 *
 * @param _poolId The ID of the PoolKey
 *
 * @return The FairLaunchInfo for the pool
 */
function fairLaunchInfo(PoolId _poolId) public view returns (FairLaunchInfo memory) {
  // ..
}
```

The `FairLaunchInfo` struct is:

```solidity
/**
 * Holds FairLaunch information for a Pool.
 *
 * @member endsAt The unix timestamp that the FairLaunch window ends
 * @member initialTick The tick that the FairLaunch position was created at
 * @member revenue The amount of revenue earned by the FairLaunch position
 * @member supply The amount of supply in the FairLaunch
 * @member closed If the FairLaunch has been closed
 */
struct FairLaunchInfo {
    uint endsAt;
    int24 initialTick;
    uint revenue;
    uint supply;
    bool closed;
}
```

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/hooks/FairLaunch.sol" %}

