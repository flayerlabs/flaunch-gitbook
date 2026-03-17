---
icon: right-left
---

# Making a swap

All of the Flaunch liquidity is powered by Uniswap V4, meaning that swaps can be easily executed using the [Uniswap V4 Universal Router](https://docs.uniswap.org/contracts/v4/guides/swap-routing).

All liquidity on Flaunch is created as FLETH : MEME, meaning that to make a swap between ETH and Token, we will need an intermediary step to either wrap or unwrap ETH <> FLETH.

### Swapping ETH to / from Token

In the following example we make a 2-step swap to convert ETH to FLETH to MEME.

{% hint style="warning" %}
Note that code has not been tested and is provided as-is, as a learning resource, and should not be used in production without proper audits and review.
{% endhint %}

```solidity
// Encode the Universal Router command
bytes memory commands = abi.encodePacked(uint8(Commands.V4_SWAP));
bytes[] memory inputs = new bytes[](1);

// Encode V4Router actions
bytes memory actions = abi.encodePacked(
    uint8(Actions.SWAP_EXACT_IN_SINGLE),
    uint8(Actions.SWAP_EXACT_IN_SINGLE),
    uint8(Actions.SETTLE_ALL),
    uint8(Actions.TAKE_ALL)
);

// Set the amount being spent
int amountIn = -1e18;

// Set the minimum amount of tokens to receive
uint minAmountOut = 0;

// Set an optional referrer address
address referrer = address(0);

// Define the PoolKey for ETH/FLETH
PoolKey memory ethPoolKey = PoolKey({
    currency0: ETH_ADDRESS,
    currency1: FLETH_ADDRESS,
    fee: 0,
    tickSpacing: 60,
    hooks: IHooks(POOL_MANAGER_HOOK_ADDRESS)
});

// Define the PoolKey for FLETH/MEME
PoolKey memory memePoolKey = PoolKey({
    currency0: FLETH_ADDRESS,
    currency1: TOKEN,
    fee: 0,
    tickSpacing: 60,
    hooks: IHooks(POSITION_MANAGER_HOOK_ADDRESS)
});

// Prepare parameters for each action
bytes[] memory params = new bytes[](4);

// Param: Swapping ETH to FLETH
params[0] = abi.encode(
    IV4Router.ExactInputSingleParams({
        poolKey: ethPoolKey,
        zeroForOne: true,
        amountIn: amountIn,
        amountOutMinimum: minAmountOut,
        sqrtPriceLimitX96: TickMath.MAX_TICK - 1,
        hookData: bytes('')
    })
);

// Param: Swapping FLETH to MEME
params[1] = abi.encode(
    IV4Router.ExactInputSingleParams({
        poolKey: key,
        zeroForOne: true,
        amountIn: amountIn,
        amountOutMinimum: minAmountOut,
        sqrtPriceLimitX96: TickMath.MAX_TICK - 1,
        hookData: abi.encode(referrer)
    })
);

// Param: Settling spent ETH
params[2] = abi.encode(key.currency0, amountIn);

// Param: Confirming expected min MEME received
params[3] = abi.encode(key.currency1, minAmountOut);

// Combine actions and params into inputs
inputs[0] = abi.encode(actions, params);

// Execute the swap
router.execute(commands, inputs, block.timestamp);
```
