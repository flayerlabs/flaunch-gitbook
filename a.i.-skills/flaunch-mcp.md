---
description: >-
  The Flaunch MCP server lets AI agents launch, discover, and trade Flaunch
  tokens on Base. It exposes the Flaunch launch flow, IPFS uploads, and coin
  discovery as Model Context Protocol tools, and pair
icon: brain
---

# Flaunch MCP

### Endpoint

The server is hosted at:

```
https://mcp.flaunch.gg
```

It uses the **Streamable HTTP** transport. No API key is required. Clients that do not support remote MCP can use the REST routes instead.

### Connecting a Client

#### Claude Code

```bash
claude mcp add --transport http flaunch https://mcp.flaunch.gg
```

#### Cursor / generic MCP config

```json
{
  "mcpServers": {
    "flaunch": {
      "url": "https://mcp.flaunch.gg"
    }
  }
}
```

For wallet-native launches and swaps, also connect Base MCP — the Flaunch tools return inputs shaped for Base MCP's `send_calls` and `swap` tools.

### What Agents Can Do

* Prepare a wallet-native Flaunch token launch for Base MCP `send_calls`.
* Launch through the Flaunch Web2 API and poll the returned `jobId`.
* Upload token images and metadata to IPFS through Flaunch.
* Resolve a completed Web2 launch job into a deployed token contract address.
* Build Base MCP `swap` input for a deployed token contract address.
* Discover newest Flaunch coins and market-cap-ranked Flaunch coins on Base.

### Recommended Launch Flow

Use `prepare_base_launch` for new launches where the user approves the transaction in their own wallet.

1. Collect the launch payload:

```json
{
  "name": "Fuzzy Cat",
  "symbol": "FCAT",
  "description": "A community memecoin.",
  "imageIpfs": "Qm...",
  "creatorAddress": "0x..."
}
```

2. If the user provides an image file instead of an IPFS CID, call `upload_image` first and use the returned `ipfsHash`.
3. Call `prepare_base_launch`.
4. Show the final payload and the returned transaction target to the user for confirmation.
5. Pass the returned `input` directly to Base MCP `send_calls`.

The response shape is:

```json
{
  "supported": true,
  "chain": "base",
  "tool": "send_calls",
  "input": {
    "chain": "base",
    "calls": [
      {
        "to": "0x...",
        "value": "0x0",
        "data": "0x..."
      }
    ]
  },
  "tokenUri": "ipfs://...",
  "metadataIpfsHash": "Qm...",
  "note": "Pass input directly to Base MCP send_calls..."
}
```

`prepare_base_launch` only prepares unsigned calldata — it never submits the transaction. The user must approve it in their wallet.

### Tools

| Tool                   | Purpose                                                                          |
| ---------------------- | -------------------------------------------------------------------------------- |
| `prepare_base_launch`  | Prepare unsigned Flaunch launch calldata for Base MCP `send_calls`.              |
| `launch_memecoin`      | Create a server-side launch job through the Flaunch Web2 API. Returns a `jobId`. |
| `get_launch_status`    | Poll a Web2 launch job by `jobId`.                                               |
| `resolve_token`        | Resolve a completed Web2 launch job into a deployed token contract address.      |
| `upload_image`         | Upload and moderate a token image; returns an IPFS hash.                         |
| `upload_metadata`      | Upload token metadata to IPFS.                                                   |
| `swap_guidance`        | Build Base MCP `swap` input for buying or selling a deployed token.              |
| `get_new_coins`        | List the newest Flaunch coin launches on Base.                                   |
| `get_market_cap_coins` | List Base Flaunch coins sorted by market cap.                                    |
| `get_top_coins`        | Explicit discovery query (`sort`: `new` or `marketCap`).                         |
| `health_check`         | Check upstream Flaunch API health.                                               |

#### `prepare_base_launch`

Input:

```json
{
  "chain": "base",
  "name": "Fuzzy Cat",
  "symbol": "FCAT",
  "description": "A community memecoin.",
  "imageIpfs": "Qm...",
  "creatorAddress": "0x...",
  "websiteUrl": "https://example.com",
  "telegramUrl": "https://t.me/example",
  "discordUrl": "https://discord.gg/example",
  "twitterUrl": "https://x.com/example"
}
```

Required fields are `name`, `symbol`, `description`, `imageIpfs`, and `creatorAddress`. Social fields are optional.

Internally this uploads metadata through the Flaunch Web2 API, then uses `@flaunch/sdk` calldata mode to build the launch call. SDK launch defaults are kept server-side.

#### `launch_memecoin`

Create a server-side Flaunch launch job through the Web2 API. Use this only when the product flow explicitly wants the Web2 launch path — it returns a `jobId`, not wallet calldata.

#### `get_launch_status`

```json
{ "jobId": "..." }
```

#### `resolve_token`

```json
{ "jobId": "..." }
```

If the job has not completed, the tool returns a structured `token_not_deployed` error.

#### `upload_image`

```json
{ "base64Image": "data:image/png;base64,..." }
```

Use the returned `ipfsHash` as `imageIpfs` in launch preparation.

#### `upload_metadata`

```json
{
  "name": "Fuzzy Cat",
  "symbol": "FCAT",
  "description": "A community memecoin.",
  "imageIpfs": "Qm...",
  "websiteUrl": "https://example.com"
}
```

#### `swap_guidance`

Buy example:

```json
{
  "tokenAddress": "0x...",
  "direction": "buy",
  "amount": "0.01",
  "asset": "ETH",
  "chain": "base"
}
```

Response:

```json
{
  "chain": "base",
  "tool": "swap",
  "input": {
    "chain": "base",
    "fromAsset": "ETH",
    "toAsset": "0x...",
    "amount": "0.01"
  }
}
```

Pass the returned `input` to Base MCP `swap`.

### Resources

Agents can read these static MCP resources:

* `flaunch://chains` — supported chains and feature flags.
* `flaunch://schema/launch` — launch payload summary.
* `flaunch://docs/capabilities` — concise tool capability notes.

### Prompts

The server exposes reusable prompts for common workflows:

* `launch-simple-token`
* `buy-token-by-address`
* `discover-coins`
* `inspect-launch`

### REST Routes

For clients that cannot use MCP, the same functionality is available over REST:

```
GET  /livez
GET  /v1/flaunch/livez
POST /v1/upload-image
POST /v1/upload-metadata
POST /v1/base/launch/prepare
POST /v1/{base|base-sepolia}/launch-memecoin
GET  /v1/launch-status/:jobId
GET  /v1/launch-status/:jobId/token
GET  /v1/swap-guidance
GET  /v1/base/coins/new
GET  /v1/base/coins/market-cap
GET  /v1/base/coins/top?sort=new|marketCap
```

Example native launch preparation over REST:

```bash
curl -s https://mcp.flaunch.gg/v1/base/launch/prepare \
  -H 'content-type: application/json' \
  -d '{
    "name": "Fuzzy Cat",
    "symbol": "FCAT",
    "description": "A community memecoin.",
    "imageIpfs": "Qm...",
    "creatorAddress": "0x1111111111111111111111111111111111111111"
  }'
```

### Agent Rules

Agents integrating this server should follow these rules:

* Never submit launches silently. Show the final launch payload and ask for explicit user confirmation before using `send_calls`.
* Prefer `prepare_base_launch` for wallet-native launches.
* Use `launch_memecoin` only for the server-side Web2 job flow.
* Use `resolve_token` before attempting a swap from a Web2 `jobId`.
* For swaps by contract address, call `swap_guidance` and pass its `input` to Base MCP `swap`.
* Treat `prepare_base_launch.input` as unsigned transaction data — the user must approve it in their wallet.
