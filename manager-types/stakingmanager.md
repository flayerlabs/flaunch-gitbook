# StakingManager

The `StakingManager` allows an ERC20 token to be staked by users and distributes pro-rata ETH rewards from Flaunch stream to staking users.&#x20;

### Initialization <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

```solidity
/**
 * Parameters passed during manager initialization.
 *
 * @member stakingToken The address of the token to be staked
 * @member minEscrowDuration The minimum duration that the creator's NFT is locked for
 * @member minStakeDuration The minimum duration that the user's tokens are locked for
 * @member creatorShare The share that a creator will earn from their token
 * @member ownerShare The share that the manager owner will earn from their token
 */
struct InitializeParams {
    address stakingToken;
    uint minEscrowDuration;
    uint minStakeDuration;
    uint creatorShare;
    uint ownerShare;
}
```

### Deposit <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

```
No additional data required
```

### Public Calls

<details>

<summary>stake(uint amount)</summary>

Allows a caller to stake the defined ERC20 token.

</details>

<details>

<summary>unstake(uint amount)</summary>

Allows a caller to unstake the defined ERC20 token and claim any pending ETH fees

Before unstaking, the function validates that the user’s stake is no longer time-locked and that they have sufficient balance.

</details>

<details>

<summary>claim() returns (uint)</summary>

Allows a caller to withdraw their pending ETH rewards earned from staking.

It calculates all owed balances, updates internal snapshots, transfers ETH to the caller, and returns the total amount claimed.

</details>

<details>

<summary>balances(address user) returns(uint balance)</summary>

Allows the caller to check the total claimable ETH balance for a given address.

</details>

<details>

<summary>getUserStakeInfo(address <em>user) returns (uint amount</em>_, uint timelockUntil_, uint pendingETHRewards_)</summary>

Allows the caller to view detailed staking data for any user, including the amount of tokens currently staked, the timestamp when the stake unlocks, and the pending ETH rewards accrued so far.

</details>

### Creator Calls

<details>

<summary>escrowWithdraw(FlaunchToken calldata _flaunchToken)</summary>

Allows the token creator to withdraw their ERC721 after the escrow lock period has passed by verifying ownership and time-based unlock conditions.

</details>

<details>

<summary>extendEscrowDuration(FlaunchToken calldata _flaunchToken, uint _extendBy)</summary>

Allows the token creator to increase the escrow lock period by a specified duration, delaying when the ERC721 can be withdrawn.

</details>

### Who is using the StakingManager? <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

{% hint style="info" %}
The StakingManager is designed for protocols launching ecosystem tokens that want to offer staking yield, DAOs looking to distribute ETH rewards to stakers or contributors, meme or gamefi projects aiming to gamify attention and trading activity, and artist collectives or collaborative economies that want to coordinate shared upside through ETH-based rewards.
{% endhint %}
