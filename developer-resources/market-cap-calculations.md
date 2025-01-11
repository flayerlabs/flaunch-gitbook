---
icon: face-tongue-money
---

# Market Cap Calculations

When a new token is being flaunched, an initial market cap will be calculated for the pool created during the flow. This market cap determines the cost per token (CPT) by finding an ETH price to set against the initial token supply of 100B (`1e29`).

Although this contract reference can be set in the PositionManager through an Ownable call of `setInitialPrice`, the protocol currently uses the [`MarketCappedPrice`](https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/price/MarketCappedPrice.sol) implementation.

<figure><img src="../.gitbook/assets/Screenshot 2024-12-05 at 00.21.13.png" alt=""><figcaption><p>Example of a market cap calculation at 10,000 USDC</p></figcaption></figure>

When the token is flaunched, we make an onchain call to the primary Uniswap V4 `ETH:USDC` that has the most liquidity to find an ETH equivalency of a set USDC value. At point of protocol deployment, this value is equal to 10,000 USDC.

