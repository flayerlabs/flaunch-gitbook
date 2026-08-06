---
icon: shield-halved
---

# Spend-Gated Launches

A spend-gated launch is a Flaunch pool that refuses swaps unless they carry a signature from a nominated off-chain signer, and that signature names the maximum ETH the swap may spend. It is the mechanism behind [Game Mode](../../game-mode/README.md), and it is general: anything that can decide how much a given wallet should be allowed to spend can drive it.

This page covers the architecture. The two pages beside it go deeper on [the signer](signer.md) and on [enforcement inside the V4 hook](hook-path.md).

## The problem: one calculator slot

Flaunch's `PositionManager` has exactly one `feeCalculator`, set by the protocol owner. Every pool routes through it. That is fine while there is one fee policy, and blocking the moment you want a gated launch sitting alongside ordinary ones.

`FeeCalculatorDispatcher` occupies that single slot and routes per pool:

```
                                    ┌─ StaticFeeCalculator      (every ordinary pool)
PositionManager ─> FeeCalculatorDispatcher ─┤
                                    └─ SpendGatedSignerFeeCalculator  (gated pools)
```

A launch opts into a sub-calculator by prefixing its `feeCalculatorParams` with a routing marker:

```solidity
bytes32 constant ROUTING_PREFIX = keccak256('flaunch.dispatcher.route.v1');

feeCalculatorParams = abi.encode(ROUTING_PREFIX, address subCalculator, bytes subParams);
```

A launch without the prefix routes to the default calculator and behaves exactly as it did before the dispatcher existed. Pools configured before the dispatcher was installed keep working too — the dispatcher does not forward `setFlaunchParams` to the default calculator, which is safe precisely because that default is stateless.

Only calculators the dispatcher's owner has registered can be routed to.

## The flow

```
player ──────> game server ──────> signed SpendAuthorization
                                            │
                                            ▼
player's wallet ─── swap(…, hookData) ──> PositionManager
                                            │  afterSwap
                                            ▼
                                    FeeCalculatorDispatcher
                                            │  routed by poolId
                                            ▼
                          SpendGatedSignerFeeCalculator.trackSwap
                                    │
                                    ├─ recover signer, check against the pool's signer
                                    ├─ bind the authorization to its submitter
                                    ├─ measure real ETH input against maxSpendWei
                                    ├─ accumulate against the per-wallet cap
                                    └─ burn the signature
```

Enforcement runs in `afterSwap`, so it measures what the swap actually did rather than what it claimed it would do.

## Configuring the gate at launch

Gate settings are written once per pool, at flaunch time, through `setFlaunchParams`. Two encodings are accepted:

{% tabs %}
{% tab title="Tagged (192 bytes)" %}
The full form. This is the only one that can enable an enforcing gate.

```solidity
abi.encode(
    GATE_PARAMS_TAG,   // keccak256('flaunch.spendGate.params')
    bool   enabled,
    uint   walletCapWei,
    address signer,    // authorizes swaps for this pool
    address settler,   // may rotate or retire the signer
    uint   endsAt      // the gate expires here, on chain
)
```

Naming the signer at launch is what lets a working gate be installed in a **single transaction** — otherwise the signer arrives in a follow-up call, and until it lands nobody can produce a signature the pool accepts.
{% endtab %}

{% tab title="Short (64 bytes)" %}
```solidity
abi.encode(bool enabled, uint walletCapWei)
```

Kept decodable for existing callers. It cannot express an expiry, and an enforcing gate must have one, so in practice this form launches ungated.
{% endtab %}
{% endtabs %}

Anything else reverts with `UnrecognisedGateParams`.

{% hint style="warning" %}
The tag is not decoration. `feeCalculatorParams` is caller-controlled, and length alone is not a discriminator — any other encoding that happened to be six words long would be reinterpreted field for field. A dynamic type sitting in the signer's position decodes its ABI offset as an address, which installs a permanent per-pool signer that nobody holds the key to.
{% endhint %}

`setFlaunchParams` is a launch-time hook and runs at most once per pool. A second call reverts with `GateAlreadyConfigured`, which bounds the damage a compromised `POSITION_MANAGER` role could do to pools that have not launched yet.

## Three roles, and what each one cannot do

<table><thead><tr><th width="150">Role</th><th>Can</th><th>Cannot</th></tr></thead><tbody><tr><td><strong>Creator</strong><br>(holds the Flaunch ERC-721)</td><td>Choose the window, the cap, the signer and the settler at launch</td><td>Touch the signer afterwards — not even to retire it</td></tr><tr><td><strong>Signer</strong></td><td>Issue authorizations that the pool accepts</td><td>Change any pool setting, or authorize past <code>endsAt</code></td></tr><tr><td><strong>Settler</strong></td><td>Rotate the signer, or zero it to open trading early</td><td>Extend the window, or move the cap</td></tr></tbody></table>

The creator's exclusion is the surprising one, and it is deliberate. In Game Mode the launching **player** is the memecoin's creator — that is what makes the round theirs. Zeroing the signer is how that player would buy their own fair launch ungated, for as much as they liked, while everyone else was still earning allowance a point at a time. Nothing downstream would catch it: an ungated swap is indistinguishable from an authorized one to a watcher polling `Swap`.

Nor could the power be pinned to the launching player even if you wanted to. `IMemecoin.creator()` reads the ERC-721 owner live, so the role transfers with a secondary sale of the NFT — it was sellable mid-round to someone the round had never met.

## Expiry is the guarantee

An enforcing gate **must** carry an `endsAt`, and it must be within `MAX_GATE_DURATION` (30 days) of launch. Past that timestamp the calculator stops enforcing outright: no signature demanded, no spend tracked, the pool is an ordinary pool.

This exists because neither the launcher nor the signer is vetted, and they are routinely the same party. `flaunch()` is permissionless and `feeCalculatorParams` is caller-supplied, so a launch can name itself as both its own signer and its own settler. Since the gate enforces on sells as well as buys, such a launcher would otherwise hold a permanent veto over every holder's exit — issue buy authorizations, then simply stop signing, and the position is trapped for as long as the key stays silent.

The expiry makes the exit a property of the pool. It needs no transaction from the launcher, the settler, or the contract owner.

A launch that names a signer but no settler is refused outright (`SettlerRequired`), because the signer *is* the gate and the settler is the only party who can lift it early. One reverted flaunch is a much cheaper failure than a coin nobody can open, discovered when a round tries to settle and cannot.

## Deployments

Game Mode runs on **Robinhood chain (4663)**.

<table><thead><tr><th width="280">Contract</th><th>Address</th></tr></thead><tbody><tr><td><code>FeeCalculatorDispatcher</code></td><td><code>0x04cDDed83da53cD32E7fcD0242b21e38C279d613</code></td></tr><tr><td><code>SpendGatedSignerFeeCalculator</code></td><td><code>0xc65fC67Fa953869dF97ab2DBa96fA58F2bDC9891</code></td></tr><tr><td><code>PositionManager</code></td><td><code>0x5Cf8e499C7c466C7E2cf127BDF129F57151E65Dc</code></td></tr><tr><td><code>PoolSwap</code> (reports <code>msgSender()</code>)</td><td><code>0xb45e89f435CD33F759755Df193024310BEe7C35A</code></td></tr><tr><td><code>UniversalRouter</code></td><td><code>0x8876789976dEcBfCbBbe364623C63652db8C0904</code></td></tr></tbody></table>

The dispatcher is installed on the `PositionManager`, with the chain's `StaticFeeCalculator` as its fallback — every non-game launch passes through untouched.

{% hint style="info" %}
**Base Sepolia (84532)** carries a self-owned test stack of the same contracts. **Base mainnet (8453)** does not have the spend gate deployed.
{% endhint %}

## Limitations worth knowing before you build

* **The gate assumes an ETH-paired pool.** Spend is measured on the native side of the pool key, and a swap against a pool paired with anything else is refused with `InvalidPoolKey`.
* **Exact-output swaps are refused.** They cannot be measured safely — see [the enforcement page](hook-path.md) for why a zero-measured buy would otherwise be repeatable.
* **One gate, one signer, one pool.** There is no notion of multiple concurrent signers per pool; the per-pool signer overrides the protocol-wide trusted set entirely.
