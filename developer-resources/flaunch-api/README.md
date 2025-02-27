---
description: Access any Flaunch data through our Subgraphs
icon: clipboard-question
---

# Flaunch API

## The Flaunch Subgraph

Flaunch uses multiple subgraphs for indexing and organizing data from the Flaunch smart contracts. These subgraphs are hosted on The Graph decentralised service and can be used to query Uniswap data.

### Versions and Production Endpoints <a href="#versions-and-production-endpoints" id="versions-and-production-endpoints"></a>

Each version of Uniswap has its own dedicated subgraph, and governance contracts have a dedicated subgraph as well.

Each subgraph has a dedicated endpoint for querying data, as well as a page on [The Graph explorer](https://thegraph.com/explorer) that exposes the schema and available fields to query.

**v3 (Mainnet)**

* [Subgraph](https://thegraph.com/explorer/subgraphs/5zvR82QoaXYFyDEKLZ9t6v9adgnptxYpKpSbxtgVENFV?view=Query\&chain=arbitrum-one)
* Graphql Endpoint: `https://gateway.thegraph.com/api/<YOUR_API_KEY_HERE>/subgraphs/id/5zvR82QoaXYFyDEKLZ9t6v9adgnptxYpKpSbxtgVENFV`
* Code: [https://github.com/Uniswap/v3-subgraph](https://github.com/Uniswap/v3-subgraph)

**V2 (Mainnet)**

* [Subgraph](https://thegraph.com/explorer/subgraphs/A3Np3RQbaBA6oKJgiwDJeo5T3zrYfGHPWFYayMwtNDum?view=Query\&chain=arbitrum-one)
* Graphql Endpoint: `https://gateway.thegraph.com/api/<YOUR_API_KEY_HERE>/subgraphs/id/A3Np3RQbaBA6oKJgiwDJeo5T3zrYfGHPWFYayMwtNDum`
* Code: [https://github.com/Uniswap/v2-subgraph](https://github.com/Uniswap/v2-subgraph)
