---
description: >-
  To reduce the friction of launching tokens we have released an API, allowing
  users to make standardised API calls to flaunch a token quickly and easily,
  without payment or even any web3 identity.
icon: plane
---

# Flaunch Create API

By making a series of API calls any website, end-user or API can create a Flaunch memecoin in seconds, with zero deployment fees. There is no requirement for a wallet to be connected and instead a user can Flaunch a token connected to their email address, X (Twitter), Farcaster account or Base wallet address.

The account connected to the flaunched token will be able to connect to Flaunch, or any external flow, using [Privy](https://www.privy.io/) to have access to their dev revs claim and offramp their fees into either crypto or fiat.

So whether you're extending your existing platform to benefit from token launches, or building a more generic plugin that an entire ecosystem can use, the Flaunch Memecoin API should have ample logic to support you.

## Example Integration

To demonstrate the power of this API, we have put together a landing page that will allow for fast flaunching across multiple recipient types.

{% embed url="https://just.flaunch.gg/" %}
https://just.flaunch.gg/
{% endembed %}

{% hint style="info" %}
Have you integrated the Flaunch Memecoin API? Get in touch and we'll feature it here!
{% endhint %}

## Available API Calls

Below we have documented the API calls that are available for the Memecoin API. These are also available as a Postman collection for quicker prototyping.

{% file src="../.gitbook/assets/Flaunch Memecoin API.postman_collection.json" %}
Download the Flaunch Memecoin API Postman Collection
{% endfile %}

### Base API URI

```
https://web2-api.flaunch.gg/livez
```

## Health Check

<mark style="color:green;">`GET`</mark> `/livez`

Checks if the API is running and returns the network configuration.

**Headers**

| Name         | Value              |
| ------------ | ------------------ |
| Content-Type | `application/json` |

**Response**

{% tabs %}
{% tab title="[200] Success Response" %}
```json
{
  "status": "ok",
  "serverWallet": "0x4d7c5DFceC78E011E16BFe9F701D857841bbDFbB",
  "network": "base-sepolia"
}
```
{% endtab %}
{% endtabs %}

## Image Upload

<mark style="color:green;">`POST`</mark> `/api/v1/upload-image`

Uploads and validates an image before creating a memecoin. Images are checked for appropriate content and stored on IPFS.

{% hint style="info" %}
**Rate Limit:** Maximum 4 image uploads per minute per IP address.
{% endhint %}

**Headers**

| Name         | Value              |
| ------------ | ------------------ |
| Content-Type | `application/json` |

**Body**

```json
{
  // Base64 Encoded Image String (https://www.base64-image.de/)
  "base64Image": "data:image/jpeg;base64,/9j/4AAQSkZJRgAB...FwySj0f/2Q=="
}
```

**Response**

{% tabs %}
{% tab title="[200] Success Response" %}
```json
{
  "success": true,
  "ipfsHash": "QmX7UbPKJ7Drci3y6p6E8oi5TpUiG7NH3qSzcohPX9Xkvo",
  "tokenURI": "ipfs://QmX7UbPKJ7Drci3y6p6E8oi5TpUiG7NH3qSzcohPX9Xkvo",
  "nsfwDetection": null
}
```
{% endtab %}

{% tab title="[400] Generic Error" %}
```json
{
  "success": false,
  "error": "Error message"
}
```
{% endtab %}

{% tab title="[400] NSFW Image Uploaded" %}
```json
{
  "success": false,
  "error": "Content flagged: Suggestive content",
  "nsfwDetection": {
    "isNSFW": true,
    "score": 0.92,
    "message": "Content flagged: Suggestive content",
    "details": []
  }
}
```
{% endtab %}
{% endtabs %}

## Launch Token

<mark style="color:green;">`POST`</mark> `/api/v1/{{ base | base-sepolia }}/launch-memecoin`

{% hint style="info" %}
Tokens can be launched on either `base` or `base-sepolia` based on the parameter passed in the API call.
{% endhint %}

Creates a new memecoin token on the selected network with the following default parameters:

* $10k starting market cap
* 60% of the total supply for fair launch
* 80% dev / 20% community split
* No protocol fees

After a token has been flaunched, a `jobId` will be returned in the response. This `jobId` signifies its position in a queue to be flaunched. It can then be referenced when querying the `launch-status` endpoint to retrieve the status of the token and subsequent deployed contract address of the token.

\
**Required Fields**

<table><thead><tr><th width="241.1312255859375">Name</th><th>Value</th></tr></thead><tbody><tr><td><code>name</code></td><td>Token name.</td></tr><tr><td><code>symbol</code></td><td>Token symbol (max 8 characters).</td></tr><tr><td><code>description</code></td><td>Token description.</td></tr><tr><td><code>imageIpfs</code></td><td>IPFS hash from the image upload endpoint.</td></tr></tbody></table>

\
**Creator Authentication Options (choose one as `creatorType`)**

<table><thead><tr><th width="243.6798095703125">Name</th><th>Value</th></tr></thead><tbody><tr><td><code>creatorAddress</code></td><td>Ethereum address to receive creator benefits.</td></tr><tr><td><code>creatorEmail</code></td><td>Email address to create a managed wallet via Privy.</td></tr><tr><td><code>creatorTwitterUsername</code></td><td>Twitter username for verification and wallet creation.</td></tr><tr><td><code>creatorFarcasterUsername</code></td><td>Farcaster username for verification and wallet creation.</td></tr></tbody></table>

If no creator authentication option is provided, the creator fees will go to the Flaunch.gg wallet.

{% hint style="info" %}
**Rate Limits:** Maximum 2 memecoin launches per minute per IP address
{% endhint %}

**Headers**

| Name         | Value              |
| ------------ | ------------------ |
| Content-Type | `application/json` |

**Body**

```json
{
  "name": "My Awesome Coin",
  "symbol": "MAC",
  "description": "This is a fun memecoin powered by Flaunch",
  "imageIpfs": "{{ ipfsHash }}",
  "websiteUrl": "https://example.com",
  "discordUrl": "https://discord.gg/example",
  "twitterUrl": "https://x.com/example",
  "telegramUrl": "https://t.me/example",
  "{{ creatorType }}": "0x498E93Bc04955fCBAC04BCF1a3BA792f01Dbaa96"
}
```

**Response**

{% tabs %}
{% tab title="[200] Success Response" %}
```json
{
  "success": true,
  "message": "Memecoin launch request queued",
  "jobId": "40",
  
  // Queue status will show how long the expected flaunch time should be. The
  // `estimatedWaitSeconds` can offset the initial "Check Launch Status" delay.
  "queueStatus": {
    "position": 0,
    "waitingJobs": 0,
    "activeJobs": 1,
    "estimatedWaitSeconds": 0
  },
  
  // Privy data will vary depending on the `creatorType` specified
  "privy": {
    "type": "wallet",
    "address": "0x498E93Bc04955fCBAC04BCF1a3BA792f01Dbaa96"
  }
}
```
{% endtab %}

{% tab title="[400] Missing Data" %}
```json
{
  "success": false,
  "error": "Missing required fields: name, symbol, description, imageIpfs"
}
```
{% endtab %}
{% endtabs %}

## Check Launch Status

<mark style="color:green;">`GET`</mark> `/api/v1/launch-status/{{ jobId }}`

Retrierves the launch status of a `jobId`.

**Headers**

| Name         | Value              |
| ------------ | ------------------ |
| Content-Type | `application/json` |

**Response**

{% tabs %}
{% tab title="[200]  Job Complete" %}
```json
{
  "success": true,
  "state": "completed",
  "queuePosition": 0,
  "estimatedWaitTime": 0,
  "transactionHash": "0x04e594ee46312eecee8827aa2fef777dd2a94d247b723662be3a6daabb300028",
  "collectionToken": {
    "address": "0x06199b4b69f815141C197D034698d9E67Cdd265b",
    "imageIpfs": "QmQX8pdLRGeNps92Ma4esew7cbnYuBxQK7xUvp5soe3Lec",
    "name": "My Awesome Coin",
    "symbol": "MAC",
    "tokenURI": "ipfs://QmSxzzNaeHbYZbPyFF29Z1PrhrsfNNMyFvebZeuLPrkzvA",
    "creator": "0x4d7c5DFceC78E011E16BFe9F701D857841bbDFbB"
  }
}
```
{% endtab %}

{% tab title="[200] Job Pending" %}
```json
{
  "success": true,
  "state": "waiting",
  "queuePosition": 2,
  "estimatedWaitTime": 120
}
```
{% endtab %}

{% tab title="[200] Job Active" %}
```json
{
  "success": true,
  "state": "active",
  "queuePosition": 0,
  "estimatedWaitTime": 0
}
```
{% endtab %}

{% tab title="[400] Job Unknown" %}
```json
{
  "success": false,
  "error": "Job not found"
}
```
{% endtab %}
{% endtabs %}
