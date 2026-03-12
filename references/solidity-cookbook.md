---
icon: rectangle-terminal
---

# Solidity Cookbook

This page contains a Solidity-focused technical reference for developers integrating with the Flaunch protocol. All liquidity on Flaunch is built on top of **Uniswap V4**, and swaps are executed using the **Universal Router**.

In addition to swap examples, this reference covers how revenue from launched coins is allocated and claimed using the Flaunch **Escrow** mechanism. You'll also find utilities for **indexing pool metadata**, looking up PoolKeys from PoolIds, and retrieving token ownership data via the Flaunch **Indexer**.

The provided code examples are educational and **not audited or production-ready**. Developers are encouraged to use them as a starting point for understanding how to interact with the Flaunch stack but should conduct proper audits before deploying any derived code to mainnet.

{% hint style="warning" %}
Note that code has not been tested and is provided as-is, as a learning resource, and should not be used in production without proper audits and review.
{% endhint %}

<details>

<summary>Making a Swap</summary>

All of the Flaunch liquidity is powered by Uniswap V4, meaning that swaps can be easily executed using the [Uniswap V4 Universal Router](https://docs.uniswap.org/contracts/v4/guides/swap-routing).

All liquidity on Flaunch is created as FLETH : COIN, meaning that to make a swap between ETH and Token, we will need an intermediary step to either wrap or unwrap ETH <> FLETH.

### Swapping ETH to / from Token

In the following example we make a 2-step swap to convert ETH to FLETH to COIN.

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
    currency0: 0x0000000000000000000000000000000000000000, // ETH
    currency1: 0x000000000d564d5be76f7f0d28fe52605afc7cf8, // FLETH
    fee: 0,
    tickSpacing: 60,
    hooks: IHooks(0x9e433f32bb5481a9ca7dff5b3af74a7ed041a888)
});

// Define the PoolKey for FLETH/COIN
PoolKey memory memePoolKey = PoolKey({
    currency0: 0x000000000d564d5be76f7f0d28fe52605afc7cf8, // FLETH
    currency1: TOKEN,
    fee: 0,
    tickSpacing: 60,
    hooks: IHooks(0x51Bba15255406Cfe7099a42183302640ba7dAFDC)
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

// Param: Swapping FLETH to COIN
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

// Param: Confirming expected min Coin received
params[3] = abi.encode(key.currency1, minAmountOut);

// Combine actions and params into inputs
inputs[0] = abi.encode(actions, params);

// Execute the swap
router.execute(commands, inputs, block.timestamp);
```

</details>

<details>

<summary>Claiming Revenue</summary>

In an effort to simplify the user claiming experience of revenue, as well as to improve security and save gas during transactions under the hood, Flaunch uses an Escrow contract to store flETH that is assigned to a user from all of their coin revenues.

Revenue from coins are allocated and are claimable per-user, as opposed to per-coin or per-user-per-coin. This is beneficial from a protocol perspective, but external protocols may find it hard to track and query fees onchain.

When fees are allocated we assign them to the owner of the coin at the time of allocation, and we also track the total amount of fees that a `PoolId` has generated.  These attributes are tracked both onchain in the `FeeEscrow` contract, and in the subgraph.

```solidity
/// Maps a user to an ETH equivalent token balance available in escrow
mapping (address _recipient => uint _amount) public balances;

/// Maps the total fees that a PoolId has accrued
mapping (PoolId _poolId => uint _amount) public totalFeesAllocated;
```

***

If you are wanting to query the amount that an individual coin has earned, that it would be recommended to take one of two approaches.

### Approach 1: Subgraph Queries

Although this can't be queried directly onchain (without using a platform like [**Chainlink**](https://chain.link/)) it is possible to pull through a coin's lifetime fees via [**The Graph**](https://thegraph.com/).

```graphql
{
  collectionFees(where: {id: 0x...}) {
    lifetimeFees
  }
}
```

{% hint style="info" %}
You can check out our subgraphs below for both mainnet and sepolia:

[https://g.flayerlabs.xyz/flaunch/base-mainnet/graphql](https://g.flayerlabs.xyz/flaunch/base-mainnet/graphql)

[https://g.flayerlabs.xyz/flaunch/base-sepolia/graphql](https://g.flayerlabs.xyz/flaunch/base-sepolia/graphql)
{% endhint %}

### Approach 2: Check the onchain FeeEscrow mapping

If you want to be able to query the revenue of the coin onchain, then the mapping in the `FeeEscrow` contract can be used

```solidity
// Get the PoolId of an ERC721
PoolId poolId = FLAUNCH.poolId(ERC721_TOKEN_ID);

// Get the total fees claimed
uint startTotalFeesClaimed = FEE_ESCROW.totalFeesAllocated(poolId);

// Make our claim to retrieve fees
FEE_ESCROW.withdrawFees(address(this), true);

// Find the new pool fees claimed
uint newTotalFeesClaimed = FEE_ESCROW.totalFeesAllocated(poolId);

// Calculate the change in fees
uint feeChange = newTotalFeesClaimed - startTotalFeesClaimed;
```

</details>

<details>

<summary>Lookup PoolId</summary>

Events emitted by the Flaunch protocol will likely be indexed by the Uniswap V4 `PoolId` , as this is unique for the individual token deployment. This is beneficial for event monitoring and subgraph navigation, but if a query then needs to be made onchain to retrieve Pool information this can prove difficult.

To facilitate this lookup functionality, we implemented an `Indexer` subscriber to the **Notifier** hook that captures initialized tokens and then stores various pieces of information that may be beneficial to third party developers, mapped to the `PoolId` as the key. This is mapped to:

```solidity
/*
 * @member flaunch The {Flaunch} contract that launched the token
 * @member memecoin The ERC20 coin address
 * @member memecoinTreasury The contract address for the coin treasury
 * @member tokenId The ERC721 {Flaunch} token created with the coin
 */
struct Index {
    address flaunch;
    address memecoin;
    address memecoinTreasury;
    uint tokenId;
}
```

### I have a PoolId and want to...

**Find the Pool Key**

In the Uniswap V4 ecosystem, the `PoolId` is calculated by hashing the `PoolKey` struct:

```solidity
/// @notice Returns value equal to keccak256(abi.encode(poolKey))
function toId(PoolKey memory poolKey) internal pure returns (PoolId poolId) {
  assembly ("memory-safe") {
    // 0xa0 represents the total size of the poolKey struct (5 slots of 32 bytes)
    poolId := keccak256(poolKey, 0xa0)
  }
}
```

As this is a one-way encoding, we cannot simply decode the `PoolId`. To achieve this lookup, we first need to [find the ERC20 coin address](solidity-cookbook.md#find-the-erc20-memecoin-address) from the `PoolId` and then make a call against the linked `Flaunch` contract's `PositionManager` . This is quite a roundabout way of finding the required information, but through inheritance it's the most optimal route.

```solidity
IndexerSubscriber indexer = IndexerSubscriber(0x..);
(address flaunch, address memecoin,,) = indexer.poolIndex(POOL_ID);
PoolKey memory poolKey = IFlaunch(flaunch).positionManager().poolKey(memecoin);
```

**Find the ERC20 coin address**

Finding the ERC20 coin address of a `PoolId` can be beneficial to quickly determine a swap path for tokens or just confirm the token information that will be displayed. The coin will be stored directly in `Index` struct, so it can be quickly accessed.

```solidity
IndexerSubscriber indexer = IndexerSubscriber(0x..);
(, address memecoin,,) = indexer.poolIndex(POOL_ID);
```

{% hint style="info" %}
The ERC20 coin will always be paired with FLETH.
{% endhint %}

**Find the ERC721 token that owns the PoolId**

When a token is flaunched, an ERC721 token is created to prove ownership and the holder will receive the token dev revs. If you know the `PoolId` you can lookup the ERC721 token and subdequently the holder, `tokenUri` and other important variables.

```solidity
IndexerSubscriber indexer = IndexerSubscriber(0x..);
(address flaunch,,, uint tokenId) = indexer.poolIndex(POOL_ID);

address owner = IERC721(flaunch).ownerOf(tokenId);
```

### Indexing a Legacy Token

If your token was deployed before the indexed was registered, then it can be added by making a public call to the `addIndex` function on the Indexer. The data is validated internally to confirm that the mapping is legitimate before storing.

```solidity
/**
 * For tokens that were flaunched before this Notifier was put in place, we allow the
 * information to be back-filled. The data is validated before being written and will
 * revert if it is deemed invalid.
 *
 * @param _poolId Array of PoolIds
 * @param _flaunch Array of the flaunch contracts for each tokenId
 * @param _tokenId Array of tokenIds
 */
function addIndex(PoolId[] calldata _poolId, address[] calldata _flaunch, uint[] calldata _tokenId) external;
```

### Deployed Indexer Addresses

<table><thead><tr><th width="182.1563720703125">Chain</th><th>Deployment Address</th></tr></thead><tbody><tr><td>Base</td><td><em>Coming soon</em></td></tr><tr><td>Base Sepolia</td><td><code>0x9fa6c64c5fe954f7da1832dc190bbabc0c234989</code></td></tr></tbody></table>

</details>

### What is FLETH?

Flaunch introduces its own wrapped ETH equivalent token called **FLETH**, and all token liquidity is paired as **FLETH : COIN**. This means standard ETH ↔ COIN swaps require a two-step conversion process through FLETH.
