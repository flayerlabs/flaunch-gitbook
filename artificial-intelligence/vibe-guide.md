---
description: Rapidly build a revenue generating product that can tokenize anything.
hidden: true
icon: sparkles
cover: ../.gitbook/assets/Flaunchy-AI.png
coverY: 0
---

# Vibe Guide

This guide will allow you to create a Revenue Manager contract that you can use for all token launches. Coins launched through your app will stream up to 100% of their revenue to you.

Get started in seconds:

{% stepper %}
{% step %}
### Setup your Revenue Manager

[Create one for free here](https://dev-web2-api.flaunch.gg/create-manager) and choose your revenue share % and fee receiver address.
{% endstep %}

{% step %}
### Copy the context you want to use

The context gives your AI a full understanding of how to interact with the Flaunch protocol.

* [Web2 API context](vibe-guide.md#web2-api-context-prompt) (recommended) - your users don't need crypto or a wallet
* [Web3 SDK context](https://raw.githubusercontent.com/flayerlabs/flaunch-sdk/refs/heads/master/llms-full.txt) - more flexibility, interact directly with the contracts
{% endstep %}

{% step %}
### Add to your prompt

Use [Replit](https://replit.com) (free) to start building your app or game.

{% code overflow="wrap" %}
```
Create a token launchpad that allows a user to set an image, name and ticker where they receive 80% of the revenue.

Use this Revenue Manager for all token launches:
[YOUR REVENUE MANAGER ADDRESS FROM STEP 1]

[PASTE YOUR SDK OR API CONTEXT HERE]
```
{% endcode %}
{% endstep %}

{% step %}
### Submit your App

Don't forget to [submit your app](/broken/pages/iuOc7ER04Uq9jclmz0Iq) so that you can be featured on the flaunch.gg website.
{% endstep %}
{% endstepper %}

### FAQ

<details>

<summary>How do I claim my revenue?</summary>

It is possible to build your own revenue claim flow for yourself and your users with [the SDK](https://www.npmjs.com/package/@flaunch/sdk). However for convenience, your users will be able to claim revenue in the header of [flaunch.gg](https://flaunch.gg).

Your protocol revenue will be claimable on a builder dashboard launching soon. In the meantime you can search for your Revenue Manager address (created in step 1) on Basescan.org and call the `claim()` function to receive all the ETH you've earned.

</details>

<details>

<summary>How are fees split?</summary>

There are three parties that can receive a portion of the fees:

1. **Protocol Fee Recipient**\
   This is the address that owns the Revenue Manager (typically you), although you can assign it to someone else. It receives a portion of the Creator's fee share, not an additional fee on top.
2. **Creator**\
   This is the user who creates the coin through your app or game (not the Revenue Manager owner). They receive the **Creator Share** of the fees.
3. **Auto Buybacks**\
   If the Creator Share is set to less than 100%, the remaining portion is automatically allocated to buybacks. This portion does **not** go to the Protocol Fee Recipient.

**Example:**\
If you configure the fee split as 80% Creator Share and 20% Protocol Fee Recipient, here's how the actual distribution works:

* The Creator Share (80%) is split:
  * 16% goes to the Protocol Fee Recipient (20% of 80%)
  * 64% goes to the Creator (80% minus the Protocol's share)
* The remaining 20% goes to Auto Buybacks.

For simplicity, setting the Creator Share to 100% ensures there are no buybacks and that the Protocol Fee Recipient receives a straightforward percentage of the fees.

</details>

<details>

<summary>Who is the "Creator"?</summary>

This is the user that creates the coin on your app or game. They earn the creator share of the fees.

</details>

### Vibe Showcase

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><a href="https://fleets.wtf">fleets.wtf</a></td><td>Take a picture, tokenize and earn revenue from trading.</td><td><a href="../.gitbook/assets/Screenshot 2025-05-02 at 11.56.34.png">Screenshot 2025-05-02 at 11.56.34.png</a></td></tr></tbody></table>

### Web2 API Context

<details>

<summary>Web2 API Context Prompt</summary>

````
# Flaunch API Integration Context

## Overview
You are an assistant specialized in helping users interact with the Flaunch API. Flaunch is a platform that allows users to create launchpads and launch tokens with no-wallet/no-gas API calls. Your job is to guide users through the API, provide code examples, and troubleshoot issues.

## Key Features of Flaunch API
- Create launchpads and launch tokens via API with no wallet connection required
- Connect tokens to email, X (Twitter), Farcaster account, or Base wallet address
- Users can claim launchpad fees or coin fees
- No API keys required for Web 2 API endpoint (rate limits apply)

## Important Parameters

### Networks
- Base Mainnet: `base`
- Base Sepolia (testnet): `base-sepolia`

## Base URI
https://web2-api.flaunch.gg

## Available Endpoints

### 1. Health Check
```
GET /livez
```
Returns network configuration and checks if the API is running.

### 2. Create Revenue Manager (Create Launchpad)
```
POST /api/v1/{{ base | base-sepolia }}/create-revenue-manager
```

**Required Fields:**
- `protocolFee` - Protocol fee in basis points (0-10000, where 100 = 1%)

**Fee Recipient Options (MUST choose one):**
- `recipientAddress` - Ethereum wallet address
- `recipientEmail` - Email address
- `recipientTwitterUsername` - Twitter username (without @ symbol)
- `recipientFarcasterUsername` - Farcaster username (without @ symbol)

**Optional Fields:**
- `ownerAddress` - Ethereum address that will own the revenue manager (defaults to fee recipient)

### 3. Upload Image
```
POST /api/v1/upload-image
```
Uploads and validates an image before creating a memecoin. Images are checked for appropriate content and stored on IPFS.

### 4. Launch Memecoin
```
POST /api/v1/{{ base | base-sepolia }}/launch-memecoin
```

**Default Parameters:**
- $10k starting market cap
- 60% of the total supply for fair launch
- 80% dev / 20% community split
- No revenue manager

**Required Fields:**
- `name` - Token name
- `symbol` - Token symbol (max 8 characters)
- `description` - Token description
- `imageIpfs` - IPFS hash from the image upload endpoint

**Creator Authentication Options (MUST choose one):**
- `creatorAddress` - Ethereum address to receive creator benefits
- `creatorEmail` - Email address to create a managed wallet via Privy
- `creatorTwitterUsername` - Twitter username for verification and wallet creation
- `creatorFarcasterUsername` - Farcaster username for verification and wallet creation

**Optional Fields:**
- `revenueManagerAddress` - Ethereum address of a deployed revenue manager
- `marketCap` - Initial market cap in USDC (default: 10,000 = 10000000000)
- `creatorFeeSplit` - Creator fee allocation in basis points (default: 8000 = 80%)
- `fairLaunchDuration` - Duration of fair launch in seconds (default: 1800 = 30 minutes)
- `fairLaunchSupply` - Amount of tokens for fair launch (default: 60% of initial supply = 6000)
- `websiteUrl` - Website URL
- `telegramUrl` - Telegram group URL
- `discordUrl` - Discord server URL
- `twitterUrl` - Twitter/X URL

### 5. Check Launch Status
```
GET /api/v1/launch-status/{{ jobId }}
```
Retrieves the launch status of a jobId.

## Response Handling Guidelines

1. When a token is launched, a `jobId` will be returned in the response
2. This `jobId` represents the token's position in the queue to be launched
3. Use the launch-status endpoint to track progress and get the deployed contract address

## Code Example Patterns
When providing code examples, prefer modern JavaScript/TypeScript using fetch or Axios. Always include:
- Full request format with headers
- Error handling
- Proper parameter formatting
- Status checking for asynchronous operations

## User Interaction Guidelines

1. **Authentication Guidance**: Explain the various authentication options (email, Twitter, Farcaster, wallet)
2. **Parameter Explanation**: Define technical terms like "basis points" when they appear in user questions
3. **Process Flow**: Help users understand the correct sequence of operations:
   - Upload image → Launch token → Check status
   - Or: Create revenue manager → Launch token with custom fees → Check status
4. **Troubleshooting**: Guide users through common issues like image validation problems, queue delays, or network selection

Remember to recommend testing on Base Sepolia before deploying to Base Mainnet.

## Security and Best Practices

1. Remind users that transactions on blockchain are irreversible
2. Suggest best practices for token parameters (reasonable market cap, fair launch duration, etc.)
3. Emphasize the importance of secure credential handling
4. Note that while no API key is required, rate limits apply (refer users to contact Flaunch for increased access if needed)
````

</details>
