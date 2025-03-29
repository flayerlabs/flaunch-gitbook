---
icon: magnifying-glass
---

# Subgraph Query Examples



{% hint style="info" %}
Subgraph queries will only return 1000 records at a time. If you require more than 1000 records you can use the `first:1000` and `skip:0` pattern to loop through requests.
{% endhint %}

## Protocol Data

### Protocol Totals

{% tabs %}
{% tab title="Query" %}
```graphql
{
  configs {
    collectionCount
    volumeETH
    totalUsers
    totalFeesETH
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "configs": [
      {
        "collectionCount": "3246",
        "volumeETH": "54932811355827392891895",
        "totalUsers": "43654",
        "totalFeesETH": "287662901856419610099"
      }
    ]
  }
}
```
{% endtab %}

{% tab title="cURL" %}
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"query": "{ configs { collectionCount volumeETH totalUsers totalFeesETH } }", "operationName": "Subgraphs", "variables": {}}' \
  https://gateway.thegraph.com/api/{api-key}/subgraphs/id/bbWLZuPrmoskDaU64xycxZFE6EvSkMQALKkDpsz5ifF
```
{% endtab %}
{% endtabs %}

* `collectionCount` : Number of tokens that have been launched on the Flaunch Protocol
* `volumeETH` : Total volume of ETH on the Flaunch Protocolin WEI.
* `totalUsers` : Number of unique wallets that have transacted on Flaunch (created or traded a token).
* `totalFeesEth` : The amount of fees generated for the protocol in WEI

## Collection Tokens (Memecoins)

### All Meme Coins

Return all the Meme coins that have been created on the Flaunch protocol.

{% tabs %}
{% tab title="Query" %}
{% code fullWidth="true" %}
```graphql
query getAllCollectionTokens {
  collectionTokens(first:1000,skip:0,orderBy:createdAt,orderDirection:asc){
    id
    name
    symbol
    baseURI
    createdAt
    creator {
      id
    }
    owner {
      id
    }
    marketCapETH
    volumeETH
    totalHolders
     fairLaunch {
      initialSupply
      active
      starts_at
      ends_at
    }
    pool {
      id
      startingMarketCap
      startingMarketCapETH
      flipped
      feeAllocation {
        creator
        community
      }
    }
  }
}
```
{% endcode %}
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "collectionTokens": [
      {
        "id": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
        "name": "Flaunchy",
        "symbol": "FLNCHY",
        "baseURI": "ipfs://QmScdsMTXWm3GUvzUwvQvYJLBQjtXwFjWvLgGvPBgipHmg",
        "createdAt": "1738332667",
        "creator": {
          "id": "0x37702e5b80b54c60e3328078cb710da0bde5e234"
        },
        "owner": {
          "id": "0x673a039f6a959fa9db65d16781e6defde30375d9"
        },
        "marketCapETH": "1166590447800000000000",
        "volumeETH": "16763537197633424202686",
        "totalHolders": "3318",
        "fairLaunch": {
          "initialSupply": "5000000000000000000000000000",
          "active": false,
          "starts_at": "1738335327",
          "ends_at": "1738335335"
        },
        "pool": {
          "id": "0xf8f4afa64c443ff00630d089205140814c9c0ce79ff293d05913a161fcc7ec4a",
          "startingMarketCap": "30000000000",
          "startingMarketCapETH": "8944652833553941000",
          "flipped": false,
          "feeAllocation": {
            "creator": 7000,
            "community": 3000
          }
        }
      },
      {
        "id": "0x65bb90a7a3c1a0f01a968b7a19ade9619984cf7e",
        "name": "QUEEFCOIN",
        "symbol": "QUEEF",
        "baseURI": "ipfs://QmYsW3jUpPm6L4pvqJJpdtRcBaRoBxM4ys6uLDsNiLdJdY",
        "createdAt": "1738334535",
        "creator": {
          "id": "0x7b0004501c9236c4bbe822aa5bfc031832d0df24"
        },
        "owner": {
          "id": "0x7b0004501c9236c4bbe822aa5bfc031832d0df24"
        },
        "marketCapETH": "15751009934250746704",
        "volumeETH": "218588969574305674564",
        "totalHolders": "3588",
        "fairLaunch": {
          "initialSupply": "7500000000000000000000000000",
          "active": false,
          "starts_at": "1738334535",
          "ends_at": "1738334549"
        },
        "pool": {
          "id": "0xb2565a6358b7af6417c04be1890b429dc3e6eeb93f1d33315756b5d32c59c0d3",
          "startingMarketCap": "30000000000",
          "startingMarketCapETH": "8931246581468615000",
          "flipped": false,
          "feeAllocation": {
            "creator": 3100,
            "community": 6900
          }
        }
      },
      ....
```
{% endtab %}

{% tab title="cURL" %}
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"query": "query getAllCollectionTokens { collectionTokens(first:1000,skip:0,orderBy:createdAt,orderDirection:asc){ id name symbol baseURI createdAt creator { id } owner { id } marketCapETH volumeETH totalHolders fairLaunch { initialSupply active starts_at ends_at } pool { id startingMarketCap startingMarketCapETH flipped feeAllocation { creator community } } } }", "operationName": "Subgraphs", "variables": {}}' \
  https://gateway.thegraph.com/api/{api-key}/subgraphs/id/bbWLZuPrmoskDaU64xycxZFE6EvSkMQALKkDpsz5ifF
```
{% endtab %}
{% endtabs %}

* `id` Meme Coin Contract Address
* `name` Meme Coin Name
* `symbol` Symbol for the memecoin, i.e. `FLNCHY`
* `baseURI` ipfs location for the metadata, which also contains the logoHash
* `createdAt` unixTimestamp for the time the coin was created
* `creator.id` address of the Meme Coin creator
* `owner.id` owner of the Meme Coin
* `marketCapETH` current market cap of the token in 1e18
* `volumeETH` total volume for the token in ETH
* `totalHolders` unique wallets that are currently holding the token
* `fairlaunch`
  * `initialSupply` - number of tokens available to buy during the fair launch period
  * `active` boolean as to whether the fair launch is in progress or has finished
  * `starts_at` unixTimestamp when users are able to start buying the token in fair launch
  * `ends_at` unixTimestamp when the fair launch closes. Note that the fair launch also ends once the `initialsupply` has sold out.
* `pool.id` uniSwap V4 pool ID.
  * `startingMarketCap` the USD starting marketCap value in 1e6
  * `startingMarketCapETH` the starting ETH market cap in 1e18, based around the ETH/USD value at the time.
  * `flipped` boolean value defining the pool token order. `false` means `token0` is ETH, `true` means `token1` is ETH
  * `feeAllocation`
    * `creator` percentage of fees going to the creator of the meme coin. `7000` = 70%
    * `community` percentage of fees going towards buybacks (community) `3000` = 30%

### All Holders for Meme coin

Return all the user addresses and balances for a specific collection token

{% tabs %}
{% tab title="Query" %}
```graphql
query CollectionTokenDetails {
  collectionToken(id:"0x1c93d155bd388241f9ab5df500d69eb529ce9583") {
    holdings(orderBy: balance orderDirection: desc) {
      id
      user {
        id
      }
      balance
    }
  }
}
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "collectionToken": {
      "holdings": [
        {
          "id": "0xdad9cab0f2c6191b171a1c6200d673304d7904e71c93d155bd388241f9ab5df500d69eb529ce9583",
          "user": {
            "id": "0xdad9cab0f2c6191b171a1c6200d673304d7904e7"
          },
          "balance": "4500000000000000000000000000"
        },
        {
          "id": "0x52be6c55f7ba1d388619f173cc3ace4bdf3aff871c93d155bd388241f9ab5df500d69eb529ce9583",
          "user": {
            "id": "0x52be6c55f7ba1d388619f173cc3ace4bdf3aff87"
          },
          "balance": "3150498445585845619816041600"
        },
        {
          "id": "0x4e33d53fe81c6ad1bae49c1202c6e8ae869736ce1c93d155bd388241f9ab5df500d69eb529ce9583",
          "user": {
            "id": "0x4e33d53fe81c6ad1bae49c1202c6e8ae869736ce"
          },
          "balance": "2621193127874927602762249188"
        },
        {
          "id": "0xb396e2490a505104c310e2e8c799a391480f68281c93d155bd388241f9ab5df500d69eb529ce9583",
          "user": {
            "id": "0xb396e2490a505104c310e2e8c799a391480f6828"
          },
          "balance": "2564659661933479019416657446"
        },
        {
          "id": "0xa8969f0811f8b4aedcda2cc85c68539976ce8e671c93d155bd388241f9ab5df500d69eb529ce9583",
          "user": {
            "id": "0xa8969f0811f8b4aedcda2cc85c68539976ce8e67"
          },
          "balance": "2215368711154279402078250413"
        },
...
```
{% endtab %}

{% tab title="cURL" %}
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"query": "query CollectionTokenDetails { collectionToken(id:"0x1c93d155bd388241f9ab5df500d69eb529ce9583") { holdings(orderBy: balance orderDirection: desc) { id user { id } balance } } }", "operationName": "Subgraphs", "variables": {}}' \

    https://gateway.thegraph.com/api/{api-key}/subgraphs/id/bbWLZuPrmoskDaU64xycxZFE6EvSkMQALKkDpsz5ifF
```
{% endtab %}
{% endtabs %}



## Users

### All Holdings for a User&#x20;

{% tabs %}
{% tab title="Query" %}
```graphql
query AllTokenHoldingsByUser {
  collectionTokenHoldings(
    orderDirection:desc
    where: {
      user: "0x4eAc46c2472b32dc7158110825A7443D35a90168"
    })
    {
      collectionToken {
        id
        symbol
      }
      balance
            user {
       				 id
     			 }
      }
  }
```
{% endtab %}

{% tab title="Response" %}
```json
{
  "data": {
    "collectionTokenHoldings": [
      {
        "balance": "42297698462229262031992423",
        "collectionToken": {
          "id": "0x09ecca7f51e8d083a74e198276f6ee2c030b34fc",
          "symbol": "LAIN"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "25949698344772658299511043",
        "collectionToken": {
          "id": "0x13f3b3a88355c8e8612584a6f00be4510b3df8ac",
          "symbol": "STHPARK"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "14490157913007908912040652",
        "collectionToken": {
          "id": "0x175cd9ffd8142661a14267d6b9f1301b4c1abb18",
          "symbol": "TAMARA X"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19969327498849791758420906",
        "collectionToken": {
          "id": "0x17f4dd00f04dfa8e007897a55c24b1a992911bfb",
          "symbol": "CARLOTA"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19039197038587248460972805",
        "collectionToken": {
          "id": "0x1a5e0bc802502ddae5b251de6b373876cc48fbef",
          "symbol": "MBG"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "777048523788583370860033",
        "collectionToken": {
          "id": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
          "symbol": "FLNCHY"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1000000000000000000000000",
        "collectionToken": {
          "id": "0x1d3b37770e302f3c85c0aa63fde8422284169b3f",
          "symbol": "SLOL"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19951364087004250615700305",
        "collectionToken": {
          "id": "0x1f57042c2efe1a92beba591e01f07ac4d5b5a286",
          "symbol": "JESSE"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19965334232349979439033305",
        "collectionToken": {
          "id": "0x20abc69e5b17daf7fa19e4e21fc5b297b5c7bb3f",
          "symbol": "BLOBS"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "18772641321637807131724369",
        "collectionToken": {
          "id": "0x2273945c59b01bf00ecc4fc1adfbf2b84b9277a0",
          "symbol": "RIDDLER"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "21970839958294306451011419",
        "collectionToken": {
          "id": "0x261c81ed2b868e33804c0bfb5232b0f4904fd191",
          "symbol": "LADDER"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1894424338294721337557869",
        "collectionToken": {
          "id": "0x350ece607af350926bd88bc97909d0350a3f70b4",
          "symbol": "PATDOG"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "109849631834064942804733742",
        "collectionToken": {
          "id": "0x373ae658f37638961ea8fdbd4e6a60d345b07bdf",
          "symbol": "FELON"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19949369150089241691531153",
        "collectionToken": {
          "id": "0x379faaa5c6e8fe36537dbbc8d66a0900a88b425a",
          "symbol": "ELON"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "43514239822175853717175223",
        "collectionToken": {
          "id": "0x3b3dae806493f6cc96da31fd7c84ffcc18a0fcaf",
          "symbol": "MERLION"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "21425760323336186606218837",
        "collectionToken": {
          "id": "0x475a2d9b9246a00f8d426ef50937f6e9f798ea90",
          "symbol": "PWEASE"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "18565427527499026851381999",
        "collectionToken": {
          "id": "0x4dd044a6be5dd14f3800bf59e4dc6e5350e20c00",
          "symbol": "VKDN"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1000000000000000000000000",
        "collectionToken": {
          "id": "0x58f887491115a0108a62b4d25a89a6847dd5af6e",
          "symbol": "FEFE"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "552218767015279214542687921",
        "collectionToken": {
          "id": "0x5ac8b8543353b169dcf93af135285f3a5884ef57",
          "symbol": "TIMMAY"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19967345528427567290744297",
        "collectionToken": {
          "id": "0x5d84dc8571a8965cd08e3a5a7a735340babf84d7",
          "symbol": "Frens"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "100001000000000000000000",
        "collectionToken": {
          "id": "0x65bb90a7a3c1a0f01a968b7a19ade9619984cf7e",
          "symbol": "QUEEF"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "3834317370111770647701743",
        "collectionToken": {
          "id": "0x6ec2637b5d0c06e8f22904d423c7e7eeb4dacb02",
          "symbol": "FUCKELON"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19951364087004250615700305",
        "collectionToken": {
          "id": "0x71c75ba1da6be9535a5fb7f15b2c562a3d50939b",
          "symbol": "PIGGY"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1905572995450623296032686",
        "collectionToken": {
          "id": "0x75725132ed064eeb952c4874418d00d3f31f57a5",
          "symbol": "MUBARAK"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "23514050005954179308758335",
        "collectionToken": {
          "id": "0x819cad67bba6d03d8e37a8e3bf9de5b380cf2600",
          "symbol": "NOS"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19153331504902435596875576",
        "collectionToken": {
          "id": "0x88dd7e4c184c8f8e0a50984b23181a1ab1b56afc",
          "symbol": "KEK"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "396413800844469450555450",
        "collectionToken": {
          "id": "0x8f5a590be0b27feb5bffaab82bbf31d3c7efe222",
          "symbol": "CONCERN"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "104617923228511263968932646",
        "collectionToken": {
          "id": "0x91267a9d74378af437c2f8a97781e074185f800b",
          "symbol": "JJJ"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1914339513490995731682571",
        "collectionToken": {
          "id": "0x95ce601bba06b0da91e9a3416a854eaf4ecb4985",
          "symbol": "SCIENCE"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "7305275282590877667421458",
        "collectionToken": {
          "id": "0x981f95ec4d4a42f1916062250574adb2e7e53752",
          "symbol": "IMPOSSIBLE"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "3000000000000000000000",
        "collectionToken": {
          "id": "0xa08d1d19468ed5a98b4164dcbeddea2ee4644dfa",
          "symbol": "QBT3"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "18984066102169692397792278",
        "collectionToken": {
          "id": "0xa5d8ebe4e2785cde4e1712a91addc02a73fb2d4a",
          "symbol": "PADDY"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "55204380793109093191855101",
        "collectionToken": {
          "id": "0xac262d923358ee3692de538de47bbe0b89fc5ab0",
          "symbol": "RATWELL"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1897457691606421342801808",
        "collectionToken": {
          "id": "0xb2cfb7c1082b570372f6caf5cdf47af4590c8faa",
          "symbol": "FWNCY"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19760756900253676525474001",
        "collectionToken": {
          "id": "0xb5652652d95498227b040b2c518681bcc546092b",
          "symbol": "MCJBL"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "21111009707317567772262185",
        "collectionToken": {
          "id": "0xb936ab8b05808aac75b57ca8df0b14f0eea1c1ab",
          "symbol": "COCORO"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "1894424338294721337557869",
        "collectionToken": {
          "id": "0xc29294941f2aac05900da4810be07f3f91ed999b",
          "symbol": "FBTC"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "28078463525736530461692668",
        "collectionToken": {
          "id": "0xc3bef7897eb925f71154b786fc4a2684dad998cb",
          "symbol": "ZACH"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "14422849793946682799468540",
        "collectionToken": {
          "id": "0xddf317a59409927f68bd836533f09a15b1bb0172",
          "symbol": "AVATARAI"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "20213834084440654435405271",
        "collectionToken": {
          "id": "0xe280a0f59be0e07e078becdf21a4bc2c22387886",
          "symbol": "LFG"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "708513927269802406432514",
        "collectionToken": {
          "id": "0xef752e28ee2d89275633deead03ae050a8d7f911",
          "symbol": "MPSSBL"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "39532737279933877962103663",
        "collectionToken": {
          "id": "0xf222734ec1f8e6d07fda618eeeaaf01c0c23c7cc",
          "symbol": "CTG"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "8734134185577696202293245",
        "collectionToken": {
          "id": "0xf2d9d3950e386404aec321ea28f80a67cbd807ff",
          "symbol": "BROCCOLI"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "220036327802138446227626385",
        "collectionToken": {
          "id": "0xf7df1867f3eb9bd7f564150bc04e5aa4a1066064",
          "symbol": "TRUTH"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      },
      {
        "balance": "19072242208449900859495080",
        "collectionToken": {
          "id": "0xf85670d74401784744d7c71b6b01f3e39583231c",
          "symbol": "STAR"
        },
        "user": {
          "id": "0x4eac46c2472b32dc7158110825a7443d35a90168"
        }
      }
    ]
  }
}
```
{% endtab %}

{% tab title="cURL" %}
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer {api-key}" \
  -d '{"query": "{ collectionTokenHoldings( orderDirection:desc where: { user: "0x4eAc46c2472b32dc7158110825A7443D35a90168" }) { collectionToken { id symbol } balance user { id } } }", "operationName": "Subgraphs", "variables": {}}' \
  https://gateway.thegraph.com/api/subgraphs/id/bbWLZuPrmoskDaU64xycxZFE6EvSkMQALKkDpsz5ifF
```
{% endtab %}
{% endtabs %}

