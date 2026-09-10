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

Game Mode runs on **Robinhood chain (4663)** and **Base (8453)**, with a test stack on **Base Sepolia (84532)**. Each chain's current stack is the Flaunch v1.3 generation, which measures spend in the pool's own paired token: ETH, or any token the chain's `PairedTokenRegistry` has approved.

<table><thead><tr><th width="280">Contract</th><th>Robinhood (4663)</th><th>Base (8453)</th><th>Base Sepolia (84532)</th></tr></thead><tbody><tr><td><code>PositionManager</code></td><td><code>0x588C683EcC450F8b2aAdb13D7f63792b840425DC</code></td><td><code>0x588C683EcC450F8b2aAdb13D7f63792b840425DC</code></td><td><code>0x8D346f24278C5CD786309161aAC0fC2bbe4c25dc</code></td></tr><tr><td><code>FeeCalculatorDispatcher</code></td><td><code>0xe3fDDf48E305dAB9A74BFa5b9858Db1aAc1D80F8</code></td><td><code>0xdbc2f399bbac8cd766f20c9b917a9a6ecad5bc4b</code></td><td><code>0xd381f8ea57df43c57cfe6e5b19a0a4700396f28c</code></td></tr><tr><td><code>SpendGatedSignerFeeCalculator</code></td><td><code>0xB246b270bB05d9Fa76c4456408ce3e8600d916bf</code></td><td><code>0xd8e46a2ca31915d9b76cc8e6b365b7ed46b77b01</code></td><td><code>0x54cdcf0bcbc3a33f470e07134c10582f93058a32</code></td></tr><tr><td><code>PoolSwap</code> (approved router, reports <code>msgSender()</code>)</td><td><code>0x8476ED156f731335ECA8Cc8A8eE759330ee4A91f</code></td><td><code>0x1B8065a099AdcD7aa7c5e241e3596B56ec98bA5a</code></td><td><code>0xf0f388a31a1745a5e2378b812ed51525f70595be</code></td></tr><tr><td><code>PairedTokenRegistry</code></td><td><code>0xC3F4E72DE4D37988F12C101b0766Fd8462F6Faf9</code></td><td><code>0x26958422636655b5a4eCE23a062e2EB61332c6da</code></td><td><code>0x23cb441d18CA75c6a14964B06806dF668d45A1C6</code></td></tr></tbody></table>

The `PositionManager` is a CREATE3 deploy, which is why Robinhood and Base share its address; every other contract is chain-specific. The dispatcher is installed on the `PositionManager`, with the chain's `StaticFeeCalculator` as its fallback — every non-game launch passes through untouched. The `PositionManager` names its registry on chain (`pairedTokenRegistry()`), so a gate discovers the approved pairings and their price calculators without configuration.

{% hint style="info" %}
Robinhood's first-generation stack (`PositionManager` `0x5Cf8e499…`, dispatcher `0x04cDDed8…`, calculator `0xc65fC67F…`, `PoolSwap` `0xb45e89f4…`) predates paired tokens and keeps serving the coins launched on it; coins never migrate. New launches use the table above.
{% endhint %}

## Limitations worth knowing before you build

* **Spend is measured in the pool's paired token.** On the current stack that is ETH or any token the chain's `PairedTokenRegistry` approves, and every signed amount is in that token's base units — `maxSpendWei` keeps its name for signature compatibility but is not always wei. Only the retired first-generation Robinhood calculator assumed an ETH-paired pool and refused anything else with `InvalidPoolKey`.
* **Exact-output swaps are refused.** They cannot be measured safely — see [the enforcement page](hook-path.md) for why a zero-measured buy would otherwise be repeatable.
* **One gate, one signer, one pool.** There is no notion of multiple concurrent signers per pool; the per-pool signer overrides the protocol-wide trusted set entirely.
