# Networks, review and availability

Upload one browser game and register a Gate URL for each supported Flaunch network. A gate process serves one game on one chain. Deploy another process with that chain's RPC, contracts and signer configuration to add a network; isolate its database namespace too. The browser game receives its launch network from Flaunch and must use the gate in that launch context.

## Submit your networks

1. Build your game and deploy its gates using the Game Mode SDK.
2. Open [Submit a game](https://flaunch.gg/game-mode/create), enable the gate option, and add each network and its exact public HTTPS Gate URL.
3. Upload the ZIP with `index.html` at its root. Flaunch reads each gate's configuration and records the chain, signer and contracts for review.
4. Staff review the build and network configuration. The library shows the approved game only on networks with fresh successful readiness checks. Chain icons show its available networks.

The hosted build's allowed connections are immutable. Adding a new gate origin requires uploading the ZIP again with every network that the replacement supports. A configuration change using origins already permitted by that build can be submitted from its management page without uploading the bytes again. Both changes create a pending revision.

Your previous approved revision remains available while a replacement is reviewed. Rejection of the replacement does not remove it. Coins already launched through a game keep their exact build and gate registration, keyed by chain and coin; approving another revision does not replace their registration.

## Configure readiness

Use an SDK release that includes signed gate readiness. `startGate()` installs it automatically. When assembling `createGate()` or `createGameServerGate()` yourself, pass:

```ts
import { createGateReadiness } from '@flayerlabs/gamemode-gate'

const readiness = createGateReadiness({
  origin: 'https://your-gate.example.com',
  chainId,
  positionManager,
  signer,
  pool,
  client,
})
// Pass `readiness` to your gate constructor.
```

Use the same claim signer, database pool and RPC client as the running game. Do not provide a separate test signer. Set connection and RPC timeouts in your deployment.

Flaunch checks `/health`, `/config` and `/ready?nonce=0x<64 hex characters>`. The readiness endpoint first checks the database and RPC chain/head, then signs a proof that expires after 30 seconds. The proof binds a fresh nonce, origin, chain and contracts to the reviewed signer. Its separate EIP-712 domain cannot authorize a purchase. A process answering `/health` alone is unverified.

## Understand availability

The monitor runs every minute. A failed probe hides the affected network immediately; an observation older than 150 seconds also stops qualifying. Three consecutive failures create a staff Slack alert. Two successful probes restore availability automatically, unless staff paused the deployment or its identity changed. Other healthy networks stay available.

Your management page shows the pending and approved network configurations, last check and failure reason. Fix database or RPC outages and allow two checks for recovery. If you rotated a signer or changed a contract, submit the new configuration for review. A healthy endpoint never overrides a manual staff pause.

Creators browse games on their selected launch network. Choosing another network requires confirmation before their launch selection changes. Flaunch checks eligibility and readiness again before the launch transaction. A temporary outage can therefore prevent a launch even if the game appeared in the library a moment earlier.

## Staff operations

[Flaunch administration](https://admin.flayer.io/flaunch) lists submissions and approved revisions, with build previews and per-network diagnostics. Flaunch staff access uses the platform's existing product grants and MFA. Editors can propose configurations; publishers can review, feature and pause games. The backend owns these decisions, audit records and availability; the admin application is its interface.

Health alerts use a webhook bound to Slack `#product-game-mode`. They include the game, chain, reason and admin link. Alerts are queued durably and retried; a Slack outage does not undo a submission or restore an unhealthy game. Webhook delivery is at least once, so a timeout after Slack accepts a message can produce a duplicate.

Operators should apply the backend migration, configure the admin API and webhook, and enable monitoring in observation mode before enabling the new picker. Upgrade existing gates and submit their network configurations first. Older gates do not become verified merely because they previously passed a liveness check. Existing coin registrations need a verified historical backfill where they predate immutable pins; do not guess their original build from the game's current slug.
