---
icon: square-dollar
---

# Setting a swap referrer

When a swap is made on Flaunch, a portion of the swap fee is earmarked for referrer fees before being divided between the remaining allocations.

{% hint style="info" %}
If no referrer is set, then the allocation is instead divided between other sources.
{% endhint %}

By default, all fees in Flaunch are distributed as ETH. However, for referrers they receive their referral fee in the unspecified token (ETH or Token).

This referrer fee is taken before any other fee and is given regardless of token type. The protocol is designed this way as when tokens are sent to the Internal Swap Protocol, it becomes unfeasible to map and maintain referrer allocations.

For example, if a swap buys 10 tokens using ETH, then the fee would be taken in ETH as the ETH was unspecified. Alternatively, if the swap is 0.1 ETH for tokens, then the fee would be in tokens as that is unspecified.

### Direct Swap Calls

When making a swap call to our hook, which will swap (FLETH for, or from, memecoin) we will determine the optional referrer from the `hookData` that is passed. This `hookData` contains optional bytes passed during any hook call.

This referrer is the only `hookData` that we parse in our `beforeSwap` call, meaning that when calling the **PoolManager.swap** function the following can be passed to assign the referrer:

```solidity
BalanceDelta delta = poolManager.swap({
    key: POOL_KEY,
    params: SWAP_PARAMS,
    hookData: abi.encode(address(referrer))
});
```

### Using the Universal Router

Swaps can also be implemented using the [Uniswap V4 Universal Router](https://docs.uniswap.org/contracts/v4/guides/swap-routing). As the documentation on the Uniswap page is extensive, it would be better to read through that approach.

### Claiming Referrer Fees

Referral fees will be distributed in one of two ways:

1. If the `ReferralEscrow` contract is not set on the protocol, then it will instead just be directly transferred to the referrer.
2. If a `ReferralEscrow` contract has been set up, then the tokens will be assigned by escrow to the referrer to claim.

```solidity
// If we don't have referral escrow, send direct to user. We use an unsafe transfer so that
// invalid addresses don't prevent the process.
if (address(referralEscrow) == address(0)) {
    _swapFeeCurrency.transfer(referrer, referrerFee_);
    emit ReferrerFeePaid(poolId, referrer, Currency.unwrap(_swapFeeCurrency), referrerFee_);
}
// Transfer referrer fees to the escrow contract that they can claim or swap for later
else {
    _swapFeeCurrency.transfer(address(referralEscrow), referrerFee_);
    referralEscrow.assignTokens(poolId, referrer, Currency.unwrap(_swapFeeCurrency), referrerFee_);
}
```

{% hint style="warning" %}
The **ReferralEscrow contract is currently enabled on the protocol**
{% endhint %}

### **Referral Escrow Contract Addresses**

<table><thead><tr><th width="182.1563720703125">Chain</th><th>Deployment Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0xBD39c7Be6D98BD1a3e4Ad482baF99d738947fE55</code></td></tr><tr><td>Base Sepolia</td><td><code>0x0651cadC51b6a13CB3465C134A22154a2b633779</code></td></tr></tbody></table>

The claim process for referral fees is called by passing an array of token addresses to claim, and the recipient of the claim. Tokens can only be claimed from the allocation of the `msg.sender`.

```solidity
/**
 * Function for a user to claim tokens across multiple token addresses.
 *
 * @param _tokens The tokens to be claimed by the caller
 */
function claimTokens(address[] calldata _tokens, address payable _recipient);
```

If there is no allocation for a provided token address then it will just be skipped and the call will not revert.

If the user holds flETH in their referral fees, then when this is claimed it will automatically be unwrapped into ETH and transferred to the `_recipient`.
