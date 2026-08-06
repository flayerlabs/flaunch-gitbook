---
icon: file-signature
---

# The Trusted Signer

The signer is the off-chain half of a spend-gated launch. It decides how much ETH a wallet may spend against a pool, and says so in an EIP-712 signature the [calculator](hook-path.md) verifies on every swap.

Nothing about the signer is Flaunch-specific. It is a key, a rule for deciding allowance, and an endpoint that hands out signatures. In Game Mode the rule is "how well did you shoot"; it could as easily be "did you answer the question", "were you on the allowlist", or "did you win the raffle".

## The authorization

```solidity
bytes32 constant SPEND_AUTHORIZATION_TYPEHASH = keccak256(
    'SpendAuthorization(address buyer,bytes32 poolId,uint256 deadline,uint256 maxSpendWei,uint256 nonce)'
);
```

<table><thead><tr><th width="170">Field</th><th>Meaning</th></tr></thead><tbody><tr><td><code>buyer</code></td><td>The wallet this authorization belongs to. Binding it is what stops a front-runner copying the message out of public calldata.</td></tr><tr><td><code>poolId</code></td><td>The pool it is valid against. A signature for one pool is worthless on another.</td></tr><tr><td><code>deadline</code></td><td>Expiry timestamp. Keep it short — it is the window in which a leaked signature is useful.</td></tr><tr><td><code>maxSpendWei</code></td><td>The maximum native input the swap may consume. Measured against the swap's real ETH input.</td></tr><tr><td><code>nonce</code></td><td>Distinguishes two otherwise-identical authorizations, so a retried buy is not mistaken for a replay of the first.</td></tr></tbody></table>

The EIP-712 domain is:

```typescript
const domain = {
  name: 'FlaunchSpendGate',
  version: '1',
  chainId,             // the chain the pool is on
  verifyingContract,   // the SpendGatedSignerFeeCalculator
};
```

{% hint style="danger" %}
**Do not reimplement the digest.** Derive it from the contract:

```solidity
function hashSpendAuthorization(
    address _buyer, bytes32 _poolId, uint _deadline, uint _maxSpendWei, uint _nonce
) public view returns (bytes32 digest_);
```

That function is public for exactly this reason. A domain mismatch is otherwise a silent `InvalidSigner` at swap time — a failure that surfaces in a user's wallet, minutes and one gas payment away from the signing bug that caused it.
{% endhint %}

### Why the domain matters

Earlier revisions signed an EIP-191 personal-sign over `keccak256(abi.encodePacked(...))`. That preimage committed to the five fields and nothing else — no chain id, no verifying contract.

Flaunch deploys deterministically across chains, so the same `poolId` genuinely recurs on more than one of them. One signing key shared between two environments therefore made an authorization issued for one valid on the other. The EIP-712 domain closes it by construction.

## Signing

```typescript
import { privateKeyToAccount } from 'viem/accounts';

const SPEND_AUTHORIZATION_TYPES = {
  SpendAuthorization: [
    { name: 'buyer', type: 'address' },
    { name: 'poolId', type: 'bytes32' },
    { name: 'deadline', type: 'uint256' },
    { name: 'maxSpendWei', type: 'uint256' },
    { name: 'nonce', type: 'uint256' },
  ],
} as const;

const account = privateKeyToAccount(SIGNER_PRIVATE_KEY);

const signature = await account.signTypedData({
  domain,
  types: SPEND_AUTHORIZATION_TYPES,
  primaryType: 'SpendAuthorization',
  message: { buyer, poolId, deadline, maxSpendWei, nonce },
});
```

### Nonces

The chain only needs each nonce to be **unique** — it feeds the digest so two otherwise-identical claims stay distinct. Replay protection is the contract's consumed-signature ledger, not the nonce, and ordering carries no meaning.

A time-major layout keeps nonces unique across process restarts with no persistence at all:

```typescript
let seq = 0n;
function nextNonce(): bigint {
  seq = (seq + 1n) & 0xfffffn;
  return (BigInt(Date.now()) << 20n) | seq;
}
```

## Encoding the swap

The authorization travels as Uniswap V4 `hookData`, behind the referrer field Flaunch already puts there:

```solidity
abi.encode(
    address referrer,
    SignedMessage {
        address buyer;
        bytes32 poolId;
        uint256 deadline;
        uint256 maxSpendWei;
        uint256 nonce;
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
        { name: 'maxSpendWei', type: 'uint256' },
        { name: 'nonce', type: 'uint256' },
        { name: 'signature', type: 'bytes' },
      ],
    },
  ],
  [referrer, { buyer, poolId, deadline, maxSpendWei, nonce, signature }],
);
```

## Installing the signer

Two routes, and they differ in how many transactions they cost:

1. **At launch**, by naming `signer` in the tagged gate params. The gate is live the moment the pool exists. This is the single-transaction path and the one Game Mode uses.
2. **Afterwards**, via `setTrustedPoolKeySigner(poolKey, signer)` — callable only by the pool's **settler** or the contract owner. Until it lands, nobody can produce a signature the pool accepts.

There is also a protocol-wide trusted signer set (`addTrustedSigner`), administered by the contract owner, used by pools that do not nominate their own. A per-pool signer overrides it entirely.

Zeroing the per-pool signer is the gate's off-switch: enforcement stops and the pool trades openly. That is how a round settles early.

## Running one in production

**Clamp what you issue to what the chain will accept.** Read `walletSpentWei[poolId][buyer]` and cap each authorization at `walletCapWei − walletSpentWei`. Otherwise you will hand out allowance the gate refuses with `WalletCapExceeded` — users earning spend they cannot use, which reads to them as a broken product rather than a cap working as designed.

**Keep deadlines short.** The signature is public calldata the moment it is submitted. The buyer binding means a copy is useless to anyone else, but a short deadline bounds the damage if your own key or your own issuance logic misbehaves.

**Persist issuance.** If your server forgets which authorizations it has already handed out, a restart re-offers the same allowance — and now two live signatures exist against one balance. The contract's replay ledger stops the *same* signature being used twice; it does nothing about two distinct signatures you never meant to issue.

**One key, one chain.** The domain binds `chainId` and `verifyingContract`, so a key used on two deployments is safe in a way it was not before. Sharing state between them is still not: pool ids, addresses and spend records mean nothing outside their own chain.

**Reconcile.** The calculator emits `SpendRecorded(poolId, buyer, ethSpent)` on every enforced swap, including sells, where the amount is zero. Comparing that stream against what you issued is the only way to notice a divergence between your ledger and the chain's.
