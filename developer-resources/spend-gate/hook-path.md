---
icon: fishing-rod
---

# Enforcement in the V4 Hook

Flaunch's `PositionManager` is a Uniswap V4 hook. On every swap it calls the protocol's fee calculator, which — on a spend-gated pool — is `SpendGatedSignerFeeCalculator`, reached through the [dispatcher](README.md).

Two entry points matter:

* `determineSwapFee` runs in `beforeSwap` and, for this calculator, does nothing. The gate never alters the fee; it returns the base fee unchanged.
* `trackSwap` runs in `afterSwap` and is where all enforcement happens.

Enforcement lives in `afterSwap` because that is the only place the swap's **real** native input is knowable. A gate that checked an intended amount beforehand would be checking a promise.

## What `trackSwap` checks, in order

```solidity
function trackSwap(
    address _sender,          // who called PoolManager.swap, forwarded by the hook
    PoolKey calldata _poolKey,
    SwapParams calldata _params,
    BalanceDelta _delta,      // what the swap actually moved
    bytes calldata _hookData  // carries the signed authorization
) public nonReentrant;
```

1. **Caller holds `POSITION_MANAGER`.** In a dispatcher deployment that role belongs to the dispatcher. Anyone else reverts `CallerNotPositionManager`.
2. **Gate enabled?** If not, return immediately — nothing to enforce.
3. **Past `endsAt`?** Return. The gate has expired on its own and the pool is now ordinary.
4. **Per-pool signer zeroed?** Return. This is the settler's early off-switch.
5. **Decode and pre-check the authorization** — `deadline` not passed, `poolId` matches.
6. **Bind the buyer to the submitter.**
7. **Reject a consumed signature.**
8. **Recover the signer** and check it against the per-pool signer, or the protocol-wide trusted set.
9. **Refuse exact output.**
10. **Measure native spend** and enforce `maxSpendWei`.
11. **Accumulate against the wallet cap**, emit `SpendRecorded`, burn the signature.

Steps 2–4 are the three ways a gated pool stops being gated, and none of them need a transaction from the game server.

## Buyer binding

The signed message is public calldata. Without a binding, a front-runner could copy a victim's authorization out of the mempool, receive the tokens, consume the victim's allowance, and burn the signature.

```solidity
if (signedMessage.buyer != tx.origin) {
    if (!approvedRouters[_sender]) revert BuyerNotSubmitter(...);
    try IMsgSender(_sender).msgSender() returns (address reported) {
        if (reported != signedMessage.buyer) revert BuyerNotSubmitter(...);
    } catch { revert BuyerNotSubmitter(...); }
}
```

An EOA submitting its own transaction satisfies the binding through `tx.origin`. That alone would exclude smart-contract wallets and account abstraction entirely — under a 7702 relay or a 4337 bundler, `tx.origin` is the relayer, not the user.

So the binding is also satisfied by the `msgSender()` an **approved router** reports. Approval matters: any other contract could simply lie about its original caller. A front-runner replaying the message through an approved router is still refused, because the router honestly reports the front-runner.

{% hint style="info" %}
Approving a router is a statement that it truthfully reports the account that initiated the swap. On Robinhood chain the approved set is the canonical Universal Router and Flaunch's own `msgSender`-aware `PoolSwap`. The chain's older swap helper predates `msgSender()` and is deliberately **not** approved — a swap through it cannot be attributed to its sender, which is the property the whole gate hangs on.
{% endhint %}

## Measuring spend

```solidity
int128 nativeDelta = nativeIsZero ? _delta.amount0() : _delta.amount1();
if (nativeDelta < 0) ethSpent_ = uint(uint128(-nativeDelta));

// exact-input native swaps: also consider the specified amount, enforce the larger
if (nativeIsInput && _params.amountSpecified < 0) {
    uint specified = uint(-_params.amountSpecified);
    if (specified > ethSpent_) ethSpent_ = specified;
}
```

The primary measure is the swap's `BalanceDelta` — a negative native-side amount is what the swapper paid. The specified input is a backstop, and the **larger** of the two is enforced, so hook-side fills that adjust the visible delta can only ever make enforcement stricter.

### Why exact output is refused

The delta `afterSwap` receives is already net of any `beforeSwapDelta`, and Flaunch's `InternalSwapPool` fills buys out of accrued memecoin inventory through exactly that mechanism. Native taken for such a fill never appears in the delta, and the `amountSpecified` backstop only covers exact input.

An exact-output buy that inventory could cover outright would therefore measure as **zero spend**. Zero passes any `maxSpendWei` and never reaches `walletSpentWei`, so neither cap could see it — and the buy would be repeatable with every fresh authorization.

Refusing exact output is the safe direction. The gate exists to bound native input, and every buy path a gated round actually uses is exact-input native.

## The revert matrix

<table><thead><tr><th width="270">Revert</th><th>Cause</th></tr></thead><tbody><tr><td><code>TokenNotFlaunched</code></td><td>A buy before the window opens. Raised by the <code>PositionManager</code> from the launch's <code>flaunchAt</code>, so it binds the game server exactly as hard as it binds a player.</td></tr><tr><td><code>CallerNotPositionManager</code></td><td>Something other than the PositionManager (or the dispatcher acting for it) tried to report a swap.</td></tr><tr><td><code>DeadlineExpired</code></td><td>The authorization is past its <code>deadline</code>.</td></tr><tr><td><code>InvalidPoolKey</code></td><td>The authorization was signed for a different pool. Also what a non-ETH-paired pool hits.</td></tr><tr><td><code>BuyerNotSubmitter</code></td><td>Someone other than the signed buyer submitted it, via an unapproved router or a mismatched <code>msgSender()</code>.</td></tr><tr><td><code>SignatureAlreadyUsed</code></td><td>The digest has been consumed.</td></tr><tr><td><code>InvalidSigner</code></td><td>Recovered signer is not the pool's signer, nor in the trusted set. Very often a domain mismatch rather than a wrong key — see <a href="signer.md">the signer page</a>.</td></tr><tr><td><code>ExactOutputNotSupported</code></td><td><code>amountSpecified &#x3E; 0</code>. Unmeasurable, so refused.</td></tr><tr><td><code>SpendCapExceeded</code></td><td>The swap's real native input exceeded the signed <code>maxSpendWei</code>.</td></tr><tr><td><code>WalletCapExceeded</code></td><td>Cumulative spend for this wallet on this pool passed <code>walletCapWei</code>.</td></tr><tr><td><code>UnrecognisedGateParams</code></td><td>Launch params were neither the tagged 192-byte form nor the 64-byte short form.</td></tr><tr><td><code>SettlerRequired</code></td><td>An enforcing gate named a signer but no settler — nobody could ever lift it.</td></tr><tr><td><code>GateExpiryRequired</code> / <code>GateExpiryTooDistant</code></td><td>An enforcing gate had no <code>endsAt</code>, or one beyond <code>MAX_GATE_DURATION</code>.</td></tr><tr><td><code>GateAlreadyConfigured</code></td><td>A second attempt to write gate params for a pool.</td></tr></tbody></table>

## Recording spend

```solidity
uint walletSpent = walletSpentWei[poolId][signedMessage.buyer] + ethSpent;
if (settings.walletCapWei != 0 && walletSpent > settings.walletCapWei) {
    revert WalletCapExceeded(walletSpent, settings.walletCapWei);
}

walletSpentWei[poolId][signedMessage.buyer] = walletSpent;
emit SpendRecorded(poolId, signedMessage.buyer, ethSpent);
_usedSignatures[messageHash] = true;
```

The write and the event are unconditional, **including when the amount is zero**. A zero is a sell — the swapper receives native — which legitimately consumes an authorization without spending anything. Emitting it anyway keeps the on-chain record complete for off-chain reconciliation; guarding on a non-zero amount meant a consumed signature could leave no trace at all.

## Two endings

A gated pool becomes an ordinary pool in one of two ways, and the difference matters:

* **The settler zeroes the signer.** Immediate, and requires a transaction from a live key. This is how a round opens trading early.
* **`endsAt` passes.** Automatic, on chain, requiring nothing from anyone. This is the guarantee that survives the game server vanishing mid-round.

Design for the second and treat the first as an optimisation. A gate whose only exit needs the gatekeeper to come back is not a gate.
