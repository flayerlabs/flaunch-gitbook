# Flaunch & Buy

Flaunches a memecoin and makes a token buy from the Fair Launch supply in the same transaction. This ensures that the token buy cannot be frontrun by bots, and gives the creator a share of their fair launch memecoin supply.

```solidity
/**
 * Flaunches a new token and premines some tokens for the creator in exchange for ETH.
 *
 * @return memecoin_ The created ERC20 token address
 * @return ethSpent_ The amount of ETH spent during the premine
 */
function flaunch(PositionManager.FlaunchParams calldata _params) external payable returns (address memecoin_, uint ethSpent_) {
  //  ..
}
```

The `FlaunchParams` passed are the same as a typical `PositionManager.flaunch` call:

```solidity
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
 * @member initialPriceParams The encoded parameters for the Initial Price logic
 * @member feeCalculatorParams The encoded parameters for the fee calculator
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

This call does not bypass the buy price and requires additional ETH to be sent to the zap in order to execute the transaction.

#### Notes

* Our current implementation uses the `initialPriceParams` to define the initial market cap. This can be set by passing the USDC token value of the desired market cap. For example, `5000e6`would give a starting market cap of `$5000`. **The market cap must be >= `1000e6`.**
* The `feeCalculatorParams` bytes are not used in current calculator implementations. For this reason, this can be left as bytes. This can be referenced as `abi.encode('')`
* The `premineAmount` parameter is passed in the `PositionManager` flaunch call, but it won't actually be actioned unless this zap is called instead. The `premineAmount` attribute only allocates the buy that is then filled when called via the zap.

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/zaps/FlaunchPremineZap.sol" %}
Zap contract
{% endembed %}

