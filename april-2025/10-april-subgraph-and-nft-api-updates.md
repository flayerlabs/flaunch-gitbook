# 10 April: Subgraph and NFT API updates

## Subgraph updates

A series of updates have been made to the subgraph endpoints.

* `positionManager` address is now available on the `pool` . This allows integrations to target the right `positionManager` when executing trades on the pools based on the current token.
* `collection` now includes the `contract` to differentiate
* `dayData` was incorrectly counting ETH Volume. Previously it was summing the ETH and the token value in ETH for each trade, effectively doubling the volume when looking at aggregated day data.
* `0x0000` and Uniswap pools are now captured as holders for the token. This allows the flaunch UI to show the amount of tokens burned and held in the LP position. If you are using this for “holder counts” you may want to `holderCount = holderCount - 2`

## NFT API Updated to support new contract

NFT details have been updated from [https://api.flaunch.gg/token/{tokenId}](https://api.flaunch.gg/token/%7BtokenId%7D) to [https://api.flaunch.gg/token/{contractAddress}/{tokenId}](https://api.flaunch.gg/token/%7BcontractAddress%7D/%7BtokenId%7D) to accommodate the new 721 contract launch. Redirects have been included for the original endpoints so no breaking changes when hitting the previous endpoints.
