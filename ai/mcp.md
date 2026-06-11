---
icon: brain
---

# Flaunch MCP

The Flaunch MCP server lets AI agents launch, discover, and trade Flaunch tokens on Base. It exposes the Flaunch launch flow, IPFS uploads, and coin discovery as [Model Context Protocol](https://modelcontextprotocol.io) tools, and pairs with [Base MCP](https://mcp.base.org) for wallet signing and swaps.

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

### Running on Base

Flaunch MCP is designed to run alongside [Base MCP](https://mcp.base.org). Flaunch prepares everything — metadata, IPFS uploads, unsigned launch calldata, swap inputs — while Base MCP owns the wallet: account onboarding, transaction approval, and submission. The agent never signs or broadcasts through Flaunch.

All production launches and swaps target the `base` chain. `base-sepolia` is available for the Web2 launch path only — do not use Base MCP `swap` with `base-sepolia` addresses.

#### Launching a token

1. Run Base MCP onboarding first.
2. If the user did not provide a `creatorAddress`, call Base MCP `get_wallets` and use their Base Account address.
3. Collect token details and upload the image with `upload_image` if it is not already an IPFS CID.
4. Show the final launch details and ask the user to confirm.
5. Call `prepare_base_launch` and verify the response has `tool: "send_calls"` and `input.chain: "base"`.
6. Ask for explicit confirmation, then call Base MCP `send_calls` with the returned `input`.
7. Surface the approval URL as "Approve Transaction" and wait for the user to act.
8. Call Base MCP `get_request_status` once after the user acts and report the confirmed result.

Do not submit launches silently, and do not report success until Base MCP request status confirms completion.

#### Discovering and buying coins

1. Call `get_new_coins` for fresh launches or `get_market_cap_coins` for top coins.
2. Show a compact list: symbol, name, contract address, and market data when present.
3. Do not auto-buy. Ask the user which token and what amount.
4. On explicit confirmation, call `swap_guidance` and pass its `input` to Base MCP `swap` with a funding asset such as `ETH` or `USDC`.
5. Surface the approval URL and poll request status only after the user acts.

#### Swapping by contract address

Tokens can be traded directly by Base contract address — no launch flow required. For buys, set `fromAsset` to `ETH` or `USDC` and `toAsset` to the token address; for sells, reverse them. If an address came directly from the user, describe it as a user-provided Base token address — do not claim it is a Flaunch token unless Flaunch discovery verified it. If Base MCP `swap` cannot quote or route the token, report that it is not currently swappable rather than improvising raw calldata.

#### Risks

* **Low liquidity** — newly launched memecoins can have thin liquidity, volatile prices, and user-supplied metadata. Never present a discovery result as endorsed or vetted.
* **Slippage** — swaps on new tokens can fill materially worse than quoted or fail to route. Do not auto-raise slippage or retry failed swaps without explicit user confirmation.
* **Irreversible** — once the user approves a `send_calls` launch or `swap`, the onchain result cannot be undone. Always confirm chain, token details, creator address, and amount before any write action.

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
