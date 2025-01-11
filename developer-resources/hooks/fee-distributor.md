# Fee Distributor

## Introduction

Flaunch implements a waterfall approach to fee distribution, with each subsequent recipient being able to take a percentage of the remainder passed down to them from the previous recipient.

The hierarchy of our fee waterfall is as follows:

1. Swap Fee
2. Referrer Fee
3. Protocol Fee
4. Creator Fee
   1. This will be bypassed if ownership of the Memecoin ERC721 has been burned
5. BidWall Deposit
   1. This will become the Memecoin Treasury if the creator disables the BidWall

Fees are only distributed in ETH via the Fee Distributor, as all memecoin fees will be sent directly into the [Internal Swap Pool](internal-swap-pool.md) to be converted into ETH. There is only one exception to this statement:

1. Referrer fees will be distributed to the referrer either directly or via an escrow contract, as tracking of allocation through the Internal Swap Pool would result in unjustifiable gas spend and logic complexity.

ETH fees distributed to creators are allocated to an escrow interface on the PositionManager that allows the recipient to withdraw fees collected from across **all** of their pools in a single transaction.

## Public Calls

### balances

Maps the amount of ETH that an address has available to claim.

```solidity
/**
 * Maps a user to an ETH equivalent token balance available in escrow.
 */
function balances(address _recipient) public view returns (uint amount_) {
  // ..
}
```

### withdrawFees

Allows fees to be withdrawn from escrowed fee positions. Fees are claimed from the `msg.sender` and sent to the `_recipient` .

```solidity
/**
 * Allows fees to be withdrawn from escrowed fee positions.
 *
 * @param _recipient The recipient of the holder's withdraw
 * @param _unwrap If we want to unwrap the balance from flETH into ETH
 */
function withdrawFees(address _recipient, bool _unwrap) public {
  // ..
}
```

### feeCalculator

The Fee Calculator used to calculate swap fees after the Fair Launch period.

```solidity
function feeCalculator() public view returns (IFeeCalculator) {
  // ..
}
```

### fairLaunchFeeCalculator

The Fee Calculator used to calculate swap fees during the Fair Launch period.

```solidity
function fairLaunchFeeCalculator() public view returns (IFeeCalculator) {
  // ..
}
```

### flayGovernance

The address of the $FLAY token's governance. This value is immutable.

```solidity
function flayGovernance() public view returns (address) {
  // ..
}
```

### getPoolFeeDistribution

Gets the distribution for a pool by checking to see if a pool has it's own FeeDistribution. If it does then this is used, but if it isn't then it will fallback on the global FeeDistribution.

```solidity
/**
 * Gets the distribution for a pool by checking to see if a pool has it's own
 * FeeDistribution. If it does then this is used, but if it isn't then it will
 * fallback on the global FeeDistribution.
 *
 * @param _poolId The PoolId being updated
 *
 * @return feeDistribution_ The FeeDistribution applied to the pool
 */
function getPoolFeeDistribution(PoolId _poolId) public view returns (FeeDistribution memory feeDistribution_) {
  // ..
}
```

### getFeeCalculator

Gets the Fee Calculator contract that should be used based on which are set, and if the pool is currently in FairLaunch or not.

{% hint style="info" %}
It is possible for a different calculator to be used for both Fair Launch and post-Fair Launch trading.
{% endhint %}

Fee Calculators are set by the contract owner to allow for calculations to determine the swap fee. These can simply return a static amount (e.g. 1%) or can implement more advanced logic such a trading volume and volatility.

```solidity
/**
 * Gets the {IFeeCalculator} contract that should be used based on which are set,
 * and if the pool is currently in FairLaunch or not.
 *
 * @dev This could return a zero address if no fee calculators have been set
 *
 * @param _isFairLaunch If the pool is currently in FairLaunch
 *
 * @return IFeeCalculator The IFeeCalculator to use
 */
function getFeeCalculator(bool _isFairLaunch) public view returns (IFeeCalculator) {
  // ..
}
```

## Fee Percentage Assignment

Top level fee percentages can be set globally and per-pool via an Ownable call:

```solidity
    /**
     * Stores the percentages of fee distribution.
     *
     * @dev This works in a waterfall approach, with a percentage taking a
     * share before passing the potential allocation on to the next. This
     * means that the percentages listed don't need to equal 100%.
     *
     * @member swapFee The amount of the transaction taken as fee
     * @member referrer The percentage that the referrer will receive
     * @member protocol The percentage that the protocol will receive
     * [..]
     */
    struct FeeDistribution {
        uint24 swapFee;
        uint24 referrer;
        uint24 protocol;
        // [..]
    }
```

The creator fee is determined by the amount set during token flaunching, and the only way this value can be modified is if the creator burns their ERC721 token, effectively renouncing ownership and passing all fees on to the BidWall or Memecoin Treasury (depending on token configuration).

### Example

_Note: These percentages are not representitive of true protocol values_

Assuming that we have a swap of 1000 $MEME -> 1 ETH, in which ETH is the unspecified token and $MEME is the specified token.

| Taker           | Percentage | Amount     |
| --------------- | ---------- | ---------- |
| Swap Fee        | 1%         | 0.01 ETH   |
| Referrer Fee    | 5%         | 0.0005 ETH |
| Protocol Fee    | 0%         | 0 ETH      |
| Creator Fee     | 20%        | 0.0019 ETH |
| BidWall Deposit | 100%       | 0.0079 ETH |

We can modify this example to reinforce that the sum of the takes do not need to add up to 100%.

| Taker           | Percentage | Amount     |
| --------------- | ---------- | ---------- |
| Swap Fee        | 2%         | 0.02 ETH   |
| Referrer Fee    | 10%        | 0.002 ETH  |
| Protocol Fee    | 25%        | 0.0045 ETH |
| Creator Fee     | 80%        | 0.0108 ETH |
| BidWall Deposit | 100%       | 0.0027 ETH |

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/hooks/FeeDistributor.sol#L4" %}
Hook code
{% endembed %}

