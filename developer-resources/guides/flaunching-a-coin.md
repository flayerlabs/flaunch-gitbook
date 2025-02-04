---
icon: up
---

# Flaunching a Coin

Launching a token should comprise of three steps. Firstly we will need to upload a coin image to IPFS, followed by uploading the token metadata to IPFS. Once we have our data available, we can make the onchain call to flaunch the token.

### 1. Upload image

When uploading an image, it is recommended to use `jpg`, `png`, `webp` or `gif`. These can be uploaded directly to an IPFS protocol such as [Pinata](https://docs.ipfs.tech/quickstart/publish/) either through their [SDK](https://docs.pinata.cloud/quickstart) or through their manual control panel.

Once the image is uploaded, you will need to note the `CID`, also sometimes known as the `IPFS Hash`, for the next step.

### 2. Upload JSON metadata

With our image, we will now need to upload a JSON metadata structure to IPFS that defines some additional information about the coin. This metadata is picked up by the frontend to provide information about social networks linked to the coin, as well as a human readable description.

```json
{
  "name": "Token Name",
  "description": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc efficitur ut orci.",
  "image": "ipfs://ImageCID",
  "external_link": "https://flaunch.gg",
  "discordUrl": "https://discord.gg/flaunchgg",
  "twitterUrl": "https://x.com/flaunchgg",
  "telegramUrl": "https://t.me/flaunchgg",
  "collaborators": []
}
```

Again, take note of the `CID`(`IPFS Hash`) of the uploaded JSON metadata for step 3.

### 3. Create the onchain call data

When flaunching a coin we _can_ make a call directly to the [**PositionManager**](https://github.com/flayerlabs/flaunchgg-contracts/blob/main/src/contracts/PositionManager.sol#L221) contract, but it is recommended to use the [**FlaunchPremineZap**](https://github.com/flayerlabs/flaunchgg-contracts/blob/main/src/contracts/zaps/FlaunchPremineZap.sol#L70) which adds additional logic and functionality on top of the default function call.

As you can likely garner from the name of the zap, it allows the caller to premine their own token before other users can frontrun it. If this logic is not required, then you can call **PositionManager** directly with the same parameters instead and save a small amount of gas.

```solidity
/**
 * Parameters required when flaunching a new token.
 */
struct FlaunchParams {
    string name;
    string symbol;
    string tokenUri;
    uint initialTokenFairLaunch;
    uint premineAmount;
    address creator;
    uint24 creatorFeeAllocation;
    uint flaunchAt;
    bytes initialPriceParams;
    bytes feeCalculatorParams;
}
```

Below is a breakdown of each parameter that is passed, as well as some additional detail in constructing them and examples where applicable.

<details>

<summary>name</summary>

The name of the token that will be used for the ERC20.

_**Example:**_

```
Flaunchy
```

</details>

<details>

<summary>symbol</summary>

The symbol of the token that will be used for the ERC20.

It is recommended to keep this value betweene 3-6 characters for maximum DEX support.

_**Example:**_

```
FLNCHY
```

</details>

<details>

<summary>tokenUri</summary>

The IPFS URI of the CID created in step 2 of this process. If this is badly formatted or invalid, then information will be missing and the token may be hidden on the platform entirely.

_**Example:**_

```
ipfs://QmScdsMTXWm3GUvzUwvQvYJLBQjtXwFjWvLgGvPBgipHmg
```

</details>

<details>

<summary>initialTokenFairLaunch</summary>

The amount of tokens to add as single sided fair launch liquidity. This does not require any additional ETH to be sent.

This value must be between 0% (`0`) - 69% (`69e27`).

_**Example for 50% of total supply:**_

```
50e27
```

</details>

<details>

<summary>premineAmount</summary>

The amount of tokens that the `msg.sender`will buy and the `creator` will receive. This will be quoted and charged at the swap price and will require an ETH value to be passed with the call.

This value must be less than, or equal to, the value in `initialTokenFairLaunch`.

_**Example for 20% of total supply:**_

```
20e27
```

</details>

<details>

<summary>creator</summary>

The address that will receive the ERC721 ownership and premined ERC20 tokens. The `msg.sender`will still make all ETH payments for this call.

An address must be provided. If a zero address is provided then the call will revert.

_**Example:**_

```
0x498E93Bc04955fCBAC04BCF1a3BA792f01Dbaa96
```

</details>

<details>

<summary>creatorFeeAllocation</summary>

The percentage of fees the creators wants to take from the BidWall. This is denoted on the frontend by showing a split between "Dev" and "Community". This allocation refers to the share that the "Dev" will receive. This value shows a percentage accurate to 2 decimal places.

This value cannot be greater than 100% (`100_00`).

_**Example for 60.25% dev revs:**_

```
6025
```

</details>

<details>

<summary>flaunchAt</summary>

If the token wants to be scheduled to launch at a future time, a unix timestamp can be set to define when it will launch.

Tokens will not be able to be swapped until this time is reached. If an `initialTokenFairLaunch` allocation has been set, then the fair launch window won't start until this timestamp is reached.

This value cannot be greater than `+30 days`time from the current `block.timestamp`.

_**Example:**_

```
1739534400
```

</details>

<details>

<summary>initialPriceParams</summary>

The encoded parameters for the **InitialPrice** logic. This bytes data is passed to the current pricing calculator to allow for the currently active contract to handle the market cap calculations for the flaunching process.

The current data required on this endpoint is a USDC value that will represent the marketcap that the coin is created at. This will define the coin value.

At a protocol level this allows for extendability, and as such means that the expected values for this call may change. It should handle fallback values gracefully, but this cannot be guaranteed.

This value must be greater than, or equal to, `1000e6`.

_**Example of setting a $5,000 market cap:**_

```solidity
abi.encode(5000e6)
```

_**Example of setting a $75,000 market cap:**_

```solidity
abi.encode(75000e6)
```

</details>

<details>

<summary>feeCalculatorParams</summary>

The encoded parameters for the **FeeCalculator**. This bytes data is passed to the current fee calculator to allow for the currently active contract to handle the swap fee calculations after the flaunching process.

At a protocol level this allows for extendability, and as such means that the expected values for this call may change. It should handle fallback values gracefully, but this cannot be guaranteed.

_**This is not currently implemented, so a blank value can be passed:**_

```
0x0
```

</details>

### 4. Determine the flaunching fee

There are two function calls that should be made to determine the ETH fee that will be required to flaunch the token. There is a flaunching fee that is 0.1% of the market cap set, and any token premine costs. The latter is optional, but the first will always be required.

#### Find the Flaunching fee

Firstly, the 0.1% of the market cap value can be retrieved by passing the `initialPriceParams` attribute from the FlaunchParams to get back an ETH value.

```solidity
uint flaunchingFee = PositionManager.getFlaunchingFee(
    FlaunchParams.initialPriceParams
)
```

#### **Find the Premine fee**

Passing the number of tokens that you are wanting to purchase, as well as the `initialPriceParams` we can calculate the cost to premine the tokens. We can provide slippage here, though it shouldn't be required as it will only fall within the fair launch amount.

```solidity
uint premineFee = FlaunchPremineZap.calculateFee(
    FlaunchParams.premineAmount,
    0, // The slippage percentage with 2 decimal places
    FlaunchParams.initialPriceParams
)
```

_**Note: This can be overpaid and the sender will receive a refund afterwards of unspent ETH.**_

### 5. Flaunch!

Now that we have our fees and combined parameters we can make our call to flaunch our token!

```solidity
(address memecoin, uint ethSpent) = premineZap.flaunch{value: flaunchingFee + premineFee}(
    PositionManager.FlaunchParams({
        name: 'Example Memecoin',
        symbol: 'EXMPL',
        tokenUri: 'ipfs://QmScdsMTXWm3GUvzUwvQvYJLBQjtXwFjWvLgGvPBgipHmg',
        initialTokenFairLaunch: 10e27,  // 10% fair launch allocation
        premineAmount: 5e27,  // 5% premine tokens
        creator: address(this),
        creatorFeeAllocation: 20_00,  // 20% dev revs
        flaunchAt: block.timestamp + 24 hours,  // Launch in 24 hours
        initialPriceParams: abi.encode(75_000e6),  // $75,000 mcap
        feeCalculatorParams: abi.encode(0)
    })
);
```
