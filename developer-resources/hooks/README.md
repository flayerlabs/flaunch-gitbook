---
hidden: true
icon: fishing-rod
---

# Hooks

## Introduction <a href="#core-hook-functions" id="core-hook-functions"></a>

Flaunch extensively uses Uniswap V4 hooks in the `PositionManager` to allow for custom logic to be actioned during any swap and interaction against our pools.

> Uniswap V4 introduces Hooks, a system that allows developers to customize and extend the behavior of liquidity pools.
>
> Hooks are external smart contracts that can be attached to individual pools. Every pool can have one hook but a hook can serve an infinite amount of pools to intercept and modify the execution flow at specific points during pool-related actions.\
>
>
> ### Key Concepts[​](https://docs.uniswap.org/contracts/v4/concepts/hooks#key-concepts) <a href="#key-concepts" id="key-concepts"></a>
>
> * Each liquidity pool in Uniswap V4 can have its own hook contract attached to it. Hooks are optional for Uniswap V4 pools.
> * The hook contract is specified when creating a new pool in the `PoolManager.initialize` function.
> * Having pool-specific hooks allows for fine-grained control and customization of individual pools.

## Position Manager

The PositionManager is a Uniswap V4 hook that controls the user journey from token creation, to fair launch, to ongoing swaps.

### Public Functions

{% hint style="info" %}
This does not included public functions found in the hooks implemented; these can be found under this subpage.
{% endhint %}

#### flaunch

Creates a new ERC20 memecoin token creating and an ERC721 that signifies ownership of the flaunched collection. The token is then initialized into a UV4 pool.

```solidity
/**
 * Creates a new ERC20 memecoin token creating and an ERC721 that signifies ownership
 * of the flaunched collection. The token is then initialized into a UV4 pool.
 *
 * The FairLaunch period will start in this call, as soon as the pool is initialized.
 *
 * @return memecoin_ The created ERC20 token address
 */
function flaunch(FlaunchParams calldata _params) external payable returns (address memecoin_) {
  // ..
}
```

#### poolKey

Returns the PoolKey mapped to the token address. If none is set then a zero value will be returned for the fields.

```solidity
/**
 * Returns the PoolKey mapped to the token address. If none is set then a zero value
 * will be returned for the fields.
 *
 * @dev The easiest way to check for an empty response is `tickSpacing = 0`
 *
 * @param _token The address of the ERC20 token
 *
 * @return The corresponding {PoolKey} for the token
 */
function poolKey(address _token) external view returns (PoolKey memory) {
  // ..
}
```

#### getFlaunchingFee

Gets the ETH fee that must be paid to flaunch a token.

```solidity
/**
 * Gets the ETH fee that must be paid to flaunch a token.
 *
 * @return The ETH value of the fee
 */
function getFlaunchingFee() public view returns (uint) {
  // ..
}
```

#### getFlaunchingMarketCap

Gets the ETH market cap for a new token that will be flaunched.

```solidity
/**
 * Gets the ETH market cap for a new token that will be flaunched.
 *
 * @return The ETH market cap value
 */
function getFlaunchingMarketCap() public view returns (uint) {
  // ..
}
```

### Structs

```solidity
/**
 * Defines our constructor parameters.
 *
 * @member nativeToken The native ETH equivalent token used by protocol
 * @member poolManager The Uniswap V4 {PoolManager} contract
 * @member feeDistribution The default fee distribution configuration
 * @member initialPrice Set initial price calculator address
 * @member protocolOwner The EOA that will be the initial owner
 * @member protocolFeeRecipient The recipient EOA of all
 * @member flayGovernance The $FLAY token governance address
 * @member feeExemptions The default global FeeExemption values
 */
struct ConstructorParams {
    address nativeToken;
    IPoolManager poolManager;
    FeeDistribution feeDistribution;
    IInitialPrice initialPrice;
    address protocolOwner;
    address protocolFeeRecipient;
    address flayGovernance;
    FeeExemptions feeExemptions;
}

/**
 * If the creator requests a premine amount of tokens, then these will be cast
 * to this structure.
 *
 * @member amountSpecified The amount of tokens requested to buy as creator
 * @member blockNumber The block that the premine is created and allocated
 */
struct PoolPremineInfo {
    int amountSpecified;
    uint blockNumber;
}

/**
 * Parameters required when flaunching a new token.
 *
 * @member name Name of the token
 * @member symbol Symbol of the token
 * @member tokenUri The generated ERC721 token URI
 * @member initialTokenFairLaunch The amount of tokens to add as single sided fair launch liquidity
 * @member premineAmount The amount of tokens that the creator will buy themselves
 * @member creator The address that will receive the ERC721 ownership and premined ERC20 tokens
 * @member creatorFeeAllocation The percentage of fees the creators wants to take from the BidWall
 * @member flaunchAt The timestamp at which the token will launch
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
}
```

### Which Hooks are Used

This breaks down the functionality included on each Uniswap V4 hook call.

**`beforeInitialize`**

* We prevent external contracts from initializing a pool with this hook contract.

**`afterInitialize`**

* Emit PoolState update to Notifier subscribers and subgraph

**`beforeAddLiquidity`**

* If in fair launch window, we need to prevent liquidity being added by external parties

**`afterAddLiquidity`**

* Emit PoolState update to Notifier subscribers and subgraph

**`beforeRemoveLiquidity`**

* If in fair launch window, we need to prevent liquidity being removed by external parties

**`afterRemoveLiquidity`**

* Emit PoolState update to Notifier subscribers and subgraph

**`beforeSwap`**

* Check if the token is scheduled to be flaunched and only allow a swap to take place if there is a premine call available
* If the FairLaunch window has ended, but our position is still open, then we need to close the position
* We attempt to fill the swap request from our FairLaunch position. If the swap parameters surpass the FairLaunch position, or the window has closed since the last swap, then this call will also close the position and create our new range.
* We want to see if we have any token1 fee tokens that we can use to fill the swap before it hits the Uniswap pool. This prevents the pool from being affected and reduced gas costs. This also allows us to benefit from the Uniswap routing infrastructure. This frontruns Uniswap to sell undesired token amounts from our fees into desired tokens ahead of our fee distribution. This acts as a partial orderbook to remove impact against our pool.

**`afterSwap`**

* Captures fees from the swap to either distribute or send to ISP
* Once a swap has been made, we distribute fees to our LPs and emit our price update event
* If we have a feeCalculator, then we want to track the swap data for any dynamic calculations
* Emit PoolState update to Notifier subscribers and subgraph

**`beforeDonate`**

* Not used

**`afterDonate`**

* Emit PoolState update to Notifier subscribers and subgraph

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/PositionManager.sol" %}
PositionManager.sol
{% endembed %}

