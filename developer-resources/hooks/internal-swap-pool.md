# Internal Swap Pool

## Introduction

The Internal Swap Pool (ISP) hook allows for fees to be accumulated that will subsequently be [distributed to recipients](fee-distributor.md). The magic of this hook comes in the fact that we convert all donated fees to one of the tokens through intercepting swaps using `beforeSwap`.

This hook depends on a desired token (the one that we want to distributed) and undesired token (the one that we don't want to distribute) pairing in pools. In our case the desired token is (fl)ETH and the undesired token is the flaunched memecoin.

When a swap takes place in Uniswap V4, the fee is always taken in the unspecified token. So if I wanted to spend 1 ETH to buy as many memecoin as possible, then the fee would be taken from the memecoin.

The ISP captures and holds any undesired token fees (the memecoin) and then frontruns any subsequent pool swaps to fill from it’s own internal position before hitting Uniswap to request the remainder. This converts memecoin into ETH, which is only then distributed to fee recipients.

The Internal Swap Pool logic is implemented in the `beforeSwap` hook, which then alters the `BeforeSwapDelta` returned in the hook to Uniswap. This informs Uniswap V4 that we have already facilitated part, or all, of the swap required and reduces the amount swapped on Uniswap.

{% code title="PositionManager.sol" %}
```solidity
 /**
  * We want to see if we have any token1 fee tokens that we can use to fill the swap
  * before it hits the Uniswap pool. This prevents the pool from being affected and 
  * reduced gas costs. This also allows us to benefit from the Uniswap routing 
  * infrastructure.
  *
  * This frontruns Uniswap to sell undesired token amounts from our fees into desired
  * tokens  ahead of our fee distribution. This acts as a partial orderbook to remove
  * impact against our pool.
  */

 (uint tokenIn, uint tokenOut) = _internalSwap(poolManager, _key, _params, nativeToken == Currency.unwrap(_key.currency0));
 if (tokenIn + tokenOut != 0) {
   // Update our hook delta to reduce the upcoming swap amount to show that we have
   // already spent some of the ETH and received some of the underlying ERC20.
   BeforeSwapDelta internalBeforeSwapDelta = _params.amountSpecified >= 0
     ? toBeforeSwapDelta(-tokenOut.toInt128(), tokenIn.toInt128())
     : toBeforeSwapDelta(tokenIn.toInt128(), -tokenOut.toInt128());
         
     // ..
 }
```
{% endcode %}

## Public Calls

### poolFees

Provides the Claimable Fees for a pool key.

```solidity
/**
* Provides the {ClaimableFees} for a pool key.
*
* @param _poolKey The PoolKey to check
*
* @return The {ClaimableFees} for the PoolKey
*/
function poolFees(PoolKey memory _poolKey) public view returns (ClaimableFees memory) {
  // ..
}
```

The ClaimableFees struct is defined as:

```solidity
/**
 * Contains amounts for both the currency0 and currency1 values of a UV4 Pool.
 */
struct ClaimableFees {
    uint amount0;
    uint amount1;
}
```

## Key Benefits

* Recipients don’t have to swap the token against the pool, reducing detrimental tick movement. We reduce price movement on the actual pool, sometimes negating it entirely if our pool holds the full swap value.
* Recipients don’t need to dump tokens or perform any other actions to receive ETH, which would negatively impact the memecoin price.

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/hooks/InternalSwapPool.sol#L4" %}
Hook code
{% endembed %}
