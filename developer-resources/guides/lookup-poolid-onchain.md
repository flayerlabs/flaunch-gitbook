---
icon: magnifying-glass-dollar
---

# Lookup PoolId Onchain

Events emitted by the Flaunch protocol will likely be indexed by the Uniswap V4 `PoolId` , as this is unique for the individual token deployment. This is beneficial for event monitoring and subgraph navigation, but if a query then needs to be made onchain to retrieve Pool information this can prove difficult.

To facilitate this lookup functionality, we implemented an `Indexer` subscriber to the [Notifier](../hooks/notifier.md) hook that captures initialized tokens and then stores various pieces of information that may be beneficial to third party developers, mapped to the `PoolId` as the key. This is mapped to:

```solidity
/*
 * @member flaunch The {Flaunch} contract that launched the token
 * @member memecoin The ERC20 memecoin address
 * @member memecoinTreasury The contract address for the memecoin treasury
 * @member tokenId The ERC721 {Flaunch} token created with the memecoin
 */
struct Index {
    address flaunch;
    address memecoin;
    address memecoinTreasury;
    uint tokenId;
}
```

### I have a PoolId and want to..

<details>

<summary>Find the PoolKey</summary>

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

As this is a one-way encoding, we cannot simply decode the `PoolId`. To achieve this lookup, we first need to [find the ERC20 memecoin address](lookup-poolid-onchain.md#find-the-erc20-memecoin-address) from the `PoolId` and then make a call against the linked `Flaunch` contract's `PositionManager` . This is quite a roundabout way of finding the required information, but through inheritance it's the most optimal route.

```solidity
IndexerSubscriber indexer = IndexerSubscriber(0x..);
(address flaunch, address memecoin,,) = indexer.poolIndex(POOL_ID);
PoolKey memory poolKey = IFlaunch(flaunch).positionManager().poolKey(memecoin);
```

</details>

<details>

<summary>Find the ERC20 memecoin address</summary>

Finding the ERC20 memecoin address of a `PoolId` can be beneficial to quickly determine a swap path for tokens or just confirm the token information that will be displayed. The memecoin will be stored directly in `Index` struct, so it can be quickly accessed.

```solidity
IndexerSubscriber indexer = IndexerSubscriber(0x..);
(, address memecoin,,) = indexer.poolIndex(POOL_ID);
```

{% hint style="info" %}
The ERC20 memecoin will always be paired with FLETH.
{% endhint %}

</details>

<details>

<summary>Find the ERC721 token that owns the PoolId</summary>

When a token is flaunched, an ERC721 token is created to prove ownership and the holder will receive the token dev revs. If you know the `PoolId` you can lookup the ERC721 token and subdequently the holder, `tokenUri` and other important variables.

```solidity
IndexerSubscriber indexer = IndexerSubscriber(0x..);
(address flaunch,,, uint tokenId) = indexer.poolIndex(POOL_ID);

address owner = IERC721(flaunch).ownerOf(tokenId);
```

</details>

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

<table><thead><tr><th width="182.1563720703125">Chain</th><th>Deployment Address</th></tr></thead><tbody><tr><td>Base</td><td><em>Coming soon</em></td></tr><tr><td>Base Sepolia</td><td><code>0x9709ca85be90fbe82bd4fc0bfa78f882c19fc211</code></td></tr></tbody></table>

