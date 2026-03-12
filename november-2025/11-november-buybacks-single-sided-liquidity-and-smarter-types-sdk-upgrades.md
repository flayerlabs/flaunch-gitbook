# 11 November: Buybacks, Single-Sided Liquidity, and Smarter Types SDK Upgrades

## What’s New

* **BuyBack Manager System**\
  Deploy automated buyback managers via `deployBuyBackManager()` to route trading fees into Group Coin demand. Includes support for Base mainnet + Sepolia.
* **Single-Sided Coin Liquidity**\
  New `getSingleSidedCoinAddLiquidityCalls()` lets creators bootstrap liquidity from current price to ∞ using only their coin—no ETH needed. Position auto-sells into ETH as price rises.
* **Atomic Import + Liquidity**\
  `getImportAndSingleSidedCoinAddLiquidityCalls()` enables batched coin import and liquidity provisioning in a single transaction. Accepts either `initialMarketCapUSD` or `initialPriceUSD` for flexible pricing setups.
* **Enhanced TypeScript DX**\
  Added `Flatten<T>` utility type, overloads for liquidity methods, and refined param types (`ImportAndSingleSidedCoinAddLiquidityWithMarketCap`, etc.) for better IntelliSense and type safety.

## Fixes & Improvements

* **Math Safety**: Patched division-by-zero edge cases in v4 liquidity math (e.g., `maxLiquidityForAmount0Precise()`) for stability on uninitialized or degenerate pools.
* **Liquidity Param Flexibility**: Single-sided liquidity functions now support both pricing input styles—`initialMarketCapUSD`or `initialPriceUSD`.
