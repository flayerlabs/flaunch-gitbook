# 12 December: Solana Imports, Custom Token Supply, and Smarter Liquidity SDK Upgrades

## What’s New

* **Cross-Chain Import: Solana Support**\
  Added Solana verifier integration for token imports. You can now verify Solana-based tokens via `Verifier.SOLANA` with support on Base mainnet and Sepolia.
* **Token ID Lookup for Memecoins**\
  Use `getFlaunchTokenIdForMemecoin()` to fetch the Flaunch NFT token ID and contract for any memecoin address.
* **Custom Token Supply for Imports**\
  All import and liquidity methods now support a `tokenSupply` param. Useful for tokens with non-standard decimals or fixed supply configs. Passed through to `getInitializeParams()` and used in tick and liquidity math.
* **Pool Event Parsing**\
  `getPoolCreatedFromTx()` extracts memecoin address, token ID, and config from a transaction hash by decoding the `PoolCreated` event.
* **Optional Slippage Control**\
  Added `slippagePercent` (default: 0.05%) across all liquidity types and methods. Customize the slippage buffer used during liquidity operations for finer execution control.

## Fixes & Improvements

* **ClankerWorld Support**:\
  Updated `ClankerWorldVerifierAddress` to support multiple token deployments.
* **Token Importer Contract Upgrade**:\
  New `TokenImporterAddress` deployed on Base mainnet with updated ABI and support for custom `_totalSupply` via a 5-parameter `initialize()` overload.
