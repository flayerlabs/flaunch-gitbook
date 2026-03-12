---
icon: rectangle-terminal
---

# RESTful Data API

## Flaunch V1 API Documentation

{% hint style="info" %}
If you are looking to upload images, create revenue managers, or create tokens, [check out the API section](api.md).
{% endhint %}

### Overview

The Flaunch V1 API provides comprehensive access to token data, trading information, and holder analytics across supported blockchain networks. All endpoints are publicly accessible (no authentication required) and return JSON responses with consistent formatting.

**Base URL**: `https://dev-api.flayerlabs.xyz`

### Supported Networks

The API supports multiple blockchain networks through the `:chain` parameter:

* `base` - Base Mainnet
* `base-sepolia` - Base Testnet

### Global Features

#### Pagination

Most list endpoints support pagination with these query parameters:

* `limit` - Number of items to return (default: 50, max: 100)
* `offset` - Number of items to skip (default: 0)

#### Caching

All endpoints implement intelligent caching with appropriate cache headers for optimal performance.

#### Error Handling

The API returns standard HTTP status codes:

* `200` - Success
* `400` - Bad Request (invalid parameters)
* `404` - Not Found (token/data not found)
* `500` - Internal Server Error

***

### Endpoints

#### 1. Get All Tokens

**Endpoint**: `GET /v1/:chain/tokens`

Returns a paginated list of all tokens sorted by market capitalization (highest first).

**Parameters**

* `chain` (path) - Network identifier (required)
* `limit` (query) - Items per page (optional, default: 50, max: 100)
* `offset` (query) - Items to skip (optional, default: 0)
* `manager` (query) - Filter for tokens with this manager (optional)
* `ownerAddress` (query) - Filter for tokens with this owner (optional)

**Example Request**

```bash
curl "https://dev-api.flayerlabs.xyz/v1/base/tokens?limit=10&offset=0"
```

**Example Response**

```json
{
  "data": [
    {
      "tokenAddress": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
      "symbol": "FLOKI",
      "name": "Floki Inu",
      "marketCapETH": "1234.567890123456789",
      "createdAt": 1703980800,
      "fairLaunchActive": true,
      "image": "https://example.com/floki.png",
      "description": "Community-driven meme token"
    }
  ],
  "pagination": {
    "limit": 10,
    "offset": 0
  },
  "meta": {
    "network": "mainnet",
    "timestamp": 1703980800
  }
}
```

***

#### 2. Get New Tokens

**Endpoint**: `GET /v1/:chain/tokens/new`

Returns a paginated list of recently created tokens sorted by creation time (newest first).

**Parameters**

* `chain` (path) - Network identifier (required)
* `limit` (query) - Items per page (optional, default: 50, max: 100)
* `offset` (query) - Items to skip (optional, default: 0)
* `manager` (query) - Filter for tokens with this manager (optional)
* `ownerAddress` (query) - Filter for tokens with this owner (optional)

**Example Request**

```bash
curl "https://dev-api.flayerlabs.xyz/v1/base/tokens/new?limit=5"
```

**Example Response**

```json
{
  "data": [
    {
      "tokenAddress": "0x9876543210abcdef9876543210abcdef98765432",
      "symbol": "NEWMEME",
      "name": "New Meme Token",
      "marketCapETH": "0.123456789",
      "createdAt": 1703980800,
      "isActive": true,
      "image": "https://example.com/newmeme.png",
      "description": "Brand new meme token just launched"
    }
  ],
  "pagination": {
    "limit": 5,
    "offset": 0
  },
  "meta": {
    "network": "mainnet",
    "timestamp": 1703980800
  }
}
```

***

#### 3. Get Token Basic Details

**Endpoint**: `GET /v1/:chain/tokens/:tokenAddress`

Returns basic information about a specific token including metadata and social links.

**Parameters**

* `chain` (path) - Network identifier (required)
* `tokenAddress` (path) - Token contract address (required)

**Example Request**

```bash
curl "https://dev-api.flayerlabs.xyz/v1/base/tokens/0x1c93d155bd388241f9ab5df500d69eb529ce9583"
```

**Example Response**

```json
{
  "tokenAddress": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
  "symbol": "FLOKI",
  "name": "Floki Inu",
  "marketCapETH": "1234.567890123456789",
  "createdAt": 1703980800,
  "image": "https://example.com/floki.png",
  "description": "Community-driven meme token with strong fundamentals",
  "socials": {
    "website": "https://floki.com",
    "twitter": "https://twitter.com/floki",
    "telegram": "https://t.me/floki",
    "discord": "https://discord.gg/floki",
    "farcaster": "https://warpcast.com/floki"
  },
  "meta": {
    "network": "mainnet",
    "timestamp": 1703980800
  }
}
```

***

#### 4. Get Token Full Details

**Endpoint**: `GET /v1/:chain/tokens/:tokenAddress/details`

Returns comprehensive token information including trading data, bid wall information, and detailed metrics.

**Parameters**

* `chain` (path) - Network identifier (required)
* `tokenAddress` (path) - Token contract address (required)

**Example Request**

```bash
curl "https://dev-api.flayerlabs.xyz/v1/base/tokens/0x1c93d155bd388241f9ab5df500d69eb529ce9583/details"
```

**Example Response**

```json
{
  "tokenAddress": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
  "price": {
    "marketCapETH": "1234.567890123456789",
    "priceChange24h": "12.45",
    "startingMarketCapETH": "100.0"
  },
  "volume": {
    "volume24h": "456.789"
  },
  "trading": {
    "bidWallBalance": "1000.0",
    "bidWallLifetime": "3600",
    "bidWallRemaining": "800.0",
    "bidWallRemainingPercentage": 80.0,
    "buybackBalance": "1000.0",
    "buybackProgress": 20.0
  },
  "status": {
    "fairLaunchActive": true,
    "createdAt": 1703980800,
    "owner": "0xowner123..."
  },
  "socials": {
    "website": "https://floki.com",
    "twitter": "https://twitter.com/floki",
    "telegram": "https://t.me/floki",
    "discord": "https://discord.gg/floki",
    "farcaster": "https://warpcast.com/floki"
  },
  "meta": {
    "network": "mainnet",
    "timestamp": 1703980800
  }
}
```

***

#### 5. Get Token Price Data

**Endpoint**: `GET /v1/:chain/tokens/:tokenAddress/price`

Returns detailed price information with historical time series data across multiple timeframes.

**Parameters**

* `chain` (path) - Network identifier (required)
* `tokenAddress` (path) - Token contract address (required)

**Example Request**

```bash
curl "https://dev-api.flayerlabs.xyz/v1/base/tokens/0x1c93d155bd388241f9ab5df500d69eb529ce9583/price"
```

**Example Response**

```json
{
  "tokenAddress": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
  "price": {
    "current": "1234.567890123456789",
    "marketCapETH": "1234.567890123456789",
    "priceETH": "0.001234567",
    "priceChange24h": "12.45",
    "priceChange24hPercentage": 12.45,
    "allTimeHigh": "2000.0",
    "allTimeLow": "50.0",
    "startingMarketCapETH": "100.0"
  },
  "volume": {
    "volume24h": "456.789",
    "volume7d": "3200.123"
  },
  "trading": {
    "bidWallBalance": "1000.0",
    "bidWallLifetime": "3600",
    "bidWallRemaining": "800.0",
    "bidWallRemainingPercentage": 80.0,
    "buybackBalance": "1000.0",
    "buybackProgress": 20.0
  },
  "priceHistory": {
    "daily": [
      {
        "timestamp": 1703980800,
        "date": 19722,
        "open": "1200.0",
        "high": "1300.0",
        "low": "1100.0",
        "close": "1234.5",
        "volume": "100.5",
        "marketCap": "1234.5",
        "price": "0.001234"
      }
    ],
    "hourly": [
      {
        "timestamp": 1703980800,
        "open": "1230.0",
        "high": "1240.0",
        "low": "1220.0",
        "close": "1234.5",
        "volume": "10.5",
        "marketCap": "1234.5",
        "price": "0.001234"
      }
    ],
    "minutely": [
      {
        "timestamp": 1703980800,
        "open": "1233.0",
        "high": "1235.0",
        "low": "1232.0",
        "close": "1234.5",
        "volume": "1.5",
        "marketCap": "1234.5",
        "price": "0.001234"
      }
    ],
    "secondly": [
      {
        "timestamp": 1703980800,
        "open": "1234.0",
        "high": "1235.0",
        "low": "1233.0",
        "close": "1234.5",
        "volume": "0.1",
        "marketCap": "1234.5",
        "price": "0.001234"
      }
    ]
  },
  "status": {
    "isActive": true,
    "createdAt": 1703980800,
    "owner": "0xowner123..."
  },
  "meta": {
    "network": "mainnet",
    "timestamp": 1703980800,
    "timeRanges": {
      "dailyStart": 1703376000,
      "hourlyStart": 1703894400,
      "minutelyStart": 1703970000,
      "secondlyStart": 1703980500
    }
  }
}
```

***

#### 6. Get Token Holders

**Endpoint**: `GET /v1/:chain/tokens/:tokenAddress/holders`

Returns a paginated list of token holders sorted by balance (highest first).

**Parameters**

* `chain` (path) - Network identifier (required)
* `tokenAddress` (path) - Token contract address (required)
* `limit` (query) - Items per page (optional, default: 50, max: 100)
* `offset` (query) - Items to skip (optional, default: 0)

**Example Request**

```bash
curl "https://dev-api.flayerlabs.xyz/v1/base/tokens/0x1c93d155bd388241f9ab5df500d69eb529ce9583/holders?limit=10&offset=0"
```

**Example Response**

```json
{
  "tokenAddress": "0x1c93d155bd388241f9ab5df500d69eb529ce9583",
  "totalHolders": "3162",
  "holders": [
    {
      "id": "0x498581ff718922c3f8e6a244956af099b2652b2b",
      "balance": "16544363767544050103679622889"
    },
    {
      "id": "0x742d35cc6354c7deae7a5d3f2f4c2f8b8a8b9e6c",
      "balance": "8272181883772025051839811444"
    },
    {
      "id": "0x1a2b3c4d5e6f7890abcdef1234567890abcdef12",
      "balance": "4136090941886012525919905722"
    }
  ]
}
```

***

### Rate Limits

The API implements reasonable rate limiting to ensure fair usage:

* No authentication required for public endpoints
* Implement reasonable delays between requests (100-200ms recommended)
* Bulk operations should use pagination rather than rapid sequential requests

***

### Support and Resources

* **API Issues**: Report any API issues or bugs through Discord
* **Rate Limiting**: There are no limits, but don't abuse the opportunity otherwise people won't be able to have nice things. Contact the team on Discord if you will be using higher limits for production applications
* **Feature Requests**: Suggest new endpoints or data points you'd like to see added

This documentation provides everything needed to integrate with the FlayerLabs Flaunch V1 API. The examples are production-ready and include proper error handling, caching, and rate limiting considerations.
