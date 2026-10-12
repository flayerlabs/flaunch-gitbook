---
icon: file-signature
---

# The Trusted Signer

The signer is the offchain half of a spend-gated launch. It decides how much a wallet may have spent against a pool in total, and says so in an EIP-712 signature the [calculator](hook-path.md) verifies on every swap.

This page describes version 2 of `SpendGatedSignerFeeCalculator`, the cumulative spend ceiling model that Game Mode SDK 0.7.0 and later sign against. Version 1 signed a per-purchase `maxSpendWei` with a nonce; its pools keep working, but no current gate signs for it.

In Game Mode from SDK 0.9.0, the signer is Flaunch's platform gate for the chain. Game developers do not run one. The rest of this page is for anyone building their own signer against the contracts.

Nothing about the signer is Flaunch-specific. It is a key, a rule for deciding allowance, and an endpoint that hands out signatures. In Game Mode the rule is "how well did you shoot"; it could as easily be "did you answer the question", "were you on the allowlist", or "did you win the raffle".

## The authorization

```solidity
bytes32 constant SPEND_AUTHORIZATION_TYPEHASH = keccak256(
    'SpendAuthorization(address buyer,bytes32 poolId,uint256 deadline,uint256 spendCeilingWei)'
);
```

<table><thead><tr><th width="170">Field</th><th>Meaning</th></tr></thead><tbody><tr><td><code>buyer</code></td><td>The wallet this authorization belongs to. Binding it is what stops a front-runner copying the message out of public calldata.</td></tr><tr><td><code>poolId</code></td><td>The pool it is valid against. A signature for one pool is worthless on another.</td></tr><tr><td><code>deadline</code></td><td>Expiry timestamp. Game Mode sets it to the round's books close, the end of the settlement tail after the round.</td></tr><tr><td><code>spendCeilingWei</code></td><td>The most the buyer may have spent on this pool in total, including this swap. The calculator refuses any swap that would take the buyer's <code>walletSpentWei</code> past it. Game Mode signs <code>min(earned, walletCap)</code> at the moment of issue.</td></tr></tbody></table>

The EIP-712 domain is:

```typescript
const domain = {
  name: 'FlaunchSpendGate',
  version: '2',
  chainId,             // the chain the pool is on
  verifyingContract,   // the SpendGatedSignerFeeCalculator
};
```

{% hint style="danger" %}
**Do not reimplement the digest.** Derive it from the contract:

```solidity
function hashSpendAuthorization(
    address _buyer, bytes32 _poolId, uint _deadline, uint _spendCeilingWei
) public view returns (bytes32 digest_);
```

That function is public for exactly this reason. A domain mismatch is otherwise a silent `InvalidSigner` at swap time — a failure that surfaces in a user's wallet, minutes and one gas payment away from the signing bug that caused it.
{% endhint %}

### Why the domain matters

Earlier revisions signed an EIP-191 personal-sign over `keccak256(abi.encodePacked(...))`. That preimage committed to the signed fields and nothing else: no chain id, no verifying contract.

Flaunch deploys deterministically across chains, so the same `poolId` genuinely recurs on more than one of them. One signing key shared between two environments therefore made an authorization issued for one valid on the other. The EIP-712 domain closes it by construction.

## Signing

```typescript
import { privateKeyToAccount } from 'viem/accounts';

const SPEND_AUTHORIZATION_TYPES = {
  SpendAuthorization: [
    { name: 'buyer', type: 'address' },
    { name: 'poolId', type: 'bytes32' },
    { name: 'deadline', type: 'uint256' },
    { name: 'spendCeilingWei', type: 'uint256' },
  ],
} as const;

const account = privateKeyToAccount(SIGNER_PRIVATE_KEY);

const signature = await account.signTypedData({
  domain,
  types: SPEND_AUTHORIZATION_TYPES,
  primaryType: 'SpendAuthorization',
  message: { buyer, poolId, deadline, spendCeilingWei },
});
```

### Why one signature can be reused

The same signature is valid for repeated buys until its deadline, by design. The calculator keeps a running total, `walletSpentWei[poolId][buyer]`, and refuses any swap that would take it past the signed ceiling (`CeilingExceeded`). Two authorizations with the same ceiling therefore cannot add up to more than the ceiling, so there is no nonce, no consumed-signature ledger, and nothing to hold back while a wallet prompt is open. A declined or failed buy changes nothing; the next one starts from the same position.

Ceilings only rise as a player earns, so a later authorization never needs to take back an earlier one.

## Encoding the swap

The authorization travels as Uniswap V4 `hookData`, behind the referrer field Flaunch already puts there. Game Mode requires that referrer to be the zero address.

```solidity
abi.encode(
    address referrer,
    SignedMessage {
        address buyer;
        bytes32 poolId;
        uint256 deadline;
        uint256 spendCeilingWei;
        bytes   signature;
    }
)
```

```typescript
const hookData = encodeAbiParameters(
  [
    { type: 'address' },
    {
      type: 'tuple',
      components: [
        { name: 'buyer', type: 'address' },
        { name: 'poolId', type: 'bytes32' },
        { name: 'deadline', type: 'uint256' },
        { name: 'spendCeilingWei', type: 'uint256' },
        { name: 'signature', type: 'bytes' },
      ],
    },
  ],
  [zeroAddress, { buyer, poolId, deadline, spendCeilingWei, signature }],
);
```

## Installing the signer

Two routes, and they differ in how many transactions they cost:

1. **At launch**, by naming `signer` in the tagged gate params. The gate is live the moment the pool exists. This is the single-transaction path and the one Game Mode uses.
2. **Afterwards**, via `setTrustedPoolKeySigner(poolKey, signer)` — callable only by the pool's **settler** or the contract owner. Until it lands, nobody can produce a signature the pool accepts.

There is also a protocol-wide trusted signer set (`addTrustedSigner`), administered by the contract owner, used by pools that do not nominate their own. A per-pool signer overrides it entirely.

Zeroing the per-pool signer is the gate's off-switch: enforcement stops and the pool trades openly. That is how a round settles early.

## Running one in production

**Clamp the ceiling to what the chain will accept.** Sign `min(earned, walletCapWei)`, never more. A ceiling above the pool's `walletCapWei` hands out allowance the gate refuses with `WalletCapExceeded`, which reads to users as a broken product rather than a cap working as designed. What a player can still spend is `ceiling - walletSpentWei[poolId][buyer]`.

**The deadline can be long.** Ceilings only rise, so a long-lived signature is never more generous than the next one you would issue. Game Mode uses the round's books close. The answer to a leaked signer key is rotation (`setTrustedPoolKeySigner`), not a short lifetime.

**Read spend from the chain.** The calculator's `walletSpentWei[poolId][buyer]` is the whole story of what a wallet has spent. Settle against it at a confirmed block rather than inferring spend from browser messages or individual swap events. The calculator also emits `SpendRecorded(poolId, buyer, ethSpent)` when it records spend, if you want the stream as well.

**One key, one chain.** The domain binds `chainId` and `verifyingContract`, so a signature for one deployment is meaningless on another. Sharing state between chains is still a mistake: pool ids, addresses and spend records mean nothing outside their own chain.
