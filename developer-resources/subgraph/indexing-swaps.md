---
icon: retweet
---

# Indexing Swaps

If you are reading Flaunch swap data, you would need to do a little more than just read from the Uniswap V4 events.

Our PositionManager implement additional [fair launch](../hooks/fair-launch.md) and [internal swap](../hooks/internal-swap-pool.md) mechanisms that occurs before the standard Uniswap swap flow. This internal swap is used to handle fee conversions and distributions within the protocol and Fair Launch is used to handle swaps before committing liquidity to Uniswap.

When indexing these swaps, we need to account for both the standard Uniswap swap metrics and the internal swap components, which are emitted through the [PoolSwap](https://github.com/flayerlabs/flaunchgg-contracts/blob/ddd44bde2053d109a0685d918d41bb72ece5c7cc/src/contracts/PositionManager.sol#L988C1-L993C11) event.

```solidity
event PoolSwap(
  PoolId indexed poolId, 
  int flAmount0, int flAmount1,   // Fair Launch stage amounts
  int flFee0, int flFee1,         // Fair Launch stage fees
  int ispAmount0, int ispAmount1, // Internal Swap stage amounts
  int ispFee0, int ispFee1,       // Internal Swap stage fees
  int uniAmount0, int uniAmount1, // amounts returned with Uniswap delta
  int uniFee0, int uniFee1        // Uniswap fees
);
```

Each swap mechanism includes the amount swapped and the fee taken at each point. A few points of note:

* If the value is negative then this indicates that the user has sent this amount into the swap
* If the value is positive then this means that the user has received this amount in the swap
* The `0`/ `1` suffix refers to `token0` and `token1` of the PoolKey respectively

<table><thead><tr><th width="250.417236328125">Variable Name</th><th>Definition</th></tr></thead><tbody><tr><td><code>poolId</code></td><td>The V4 PoolId that has been swapped against</td></tr><tr><td><code>flAmount0</code></td><td>Fair Launch <code>token0</code> change</td></tr><tr><td><code>flAmount1</code></td><td>Fair Launch <code>token1</code> change</td></tr><tr><td><code>flFee0</code></td><td>Fair Launch <code>token0</code> fee taken</td></tr><tr><td><code>flFee1</code></td><td>Fair Launch <code>token1</code> fee taken</td></tr><tr><td><code>ispAmount0</code></td><td>Internal Swap Pool <code>token0</code> change</td></tr><tr><td><code>ispAmount1</code></td><td>Internal Swap Pool <code>token1</code> change</td></tr><tr><td><code>ispFee0</code></td><td>Internal Swap Pool <code>token0</code> fee taken</td></tr><tr><td><code>ispFee1</code></td><td>Internal Swap Pool <code>token1</code> fee taken</td></tr><tr><td><code>uniAmount0</code></td><td>Uniswap Pool <code>token0</code> change</td></tr><tr><td><code>uniAmount1</code></td><td>Uniswap Pool <code>token1</code> change</td></tr><tr><td><code>uniFee0</code></td><td>Uniswap Pool <code>token0</code> fee taken</td></tr><tr><td><code>uniFee1</code></td><td>Uniswap Pool <code>token1</code> fee taken</td></tr></tbody></table>

Therefore, additional indexing needs to be added:

1. Internal swap amounts to be calculated: `amount = flAmount + ispAmount + uniAmount;`
2. Internal swap fees to be calculated: `fees = flFee + ispFee + uniFee;`&#x20;

These can then be summed up for `token0` and `token1` to get a complete picture of the swap.

***

### Uniswap Native Hooks

Following the announcement of a standards Uniswap V4 hook data standards, we have upgraded our protocol to incorporate the proposed `HookSwap` and `HookFee` events into future deployments of our `PositionManager`. Unfortunately, these standards were announced after our initial launch, and as such are not included in our core `PositionManager` , which needs to implement data tracking as above.&#x20;

{% embed url="https://uniswapfoundation.mirror.xyz/KGKMZ2Gbc_I8IqySVUMrEenZxPnVnH9-Qe4BlN1qn0g" %}
Read about the UV4 data standard
{% endembed %}

> You can track emitted events, but to get a complete picture, you must monitor both:
>
> * Swap events from the v4 core contract.
> * HookSwap events from delta-returning hook contracts (e.g., custom curves). Relying only on Swap events will miss volume facilitated by delta-returning hooks.

Both events will still be emitted, as our core `PoolSwap` event provides more detailed information regarding which mechanism contributed to the swap in more granular detail. But for a simplified, standardised approach that will work out the box, we have the following events defined:

#### HookSwap

{% hint style="warning" %}
We do not take LP fees, so `hookLPfeeAmount0` and `hookLPfeeAmount1` will always be zero values.

If you are looking for swap fees taken by the protocol for internal mechanisms and creator / community distributions, then you should digest the `HookFee` event.
{% endhint %}

```solidity
event HookSwap(
    bytes32 indexed id,       // v4 pool id
    address indexed sender,   // router of the swap
    int128 amount0,
    int128 amount1,
    uint128 hookLPfeeAmount0,
    uint128 hookLPfeeAmount1
);
```

#### HookFee

Provides information on fees taken from the swaps.

```solidity
event HookFee(
    bytes32 indexed id,       // v4 pool id
    address indexed sender,   // router of the swap
    uint128 feeAmount0,
    uint128 feeAmount1
);
```
