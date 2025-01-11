# Notifier

## Introduction

After any pool action, our `PositionManager` will make 2 notification calls.

## External Contract Notifications

An Ownable call to our [`Notifier`](https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/hooks/Notifier.sol) contract allows external contracts to subscribe to pool state updates onchain, receiving realtime pool data updates.

The extract below is taken from the `ISubscriber` interface contract.&#x20;

```solidity
/**
 * Interface that a Subscriber contract should implement to receive
 * updates from the Flaunch {PositionManager}.
 */
interface ISubscriber {

    function subscribe(bytes memory data) external returns (bool);
    function unsubscribe() external;
    function notify(PoolId _poolId) external;

}
```

When the subscription is initialised the subscriber can validate and only if a `true` response is returned will the subscription be made. Pool updates will subsequently send a notification to each subscribed contract sending on the `PoolId` to all subscribed recipients. External calls can then be made if required, though gas considerations should always be made.

The contract can subsequently be unsubscribed without any required confirmation, and even if the contract becomes compromised and reverts during the unsubscription this is caught and handled gracefully.

## Subgraph Event Emission

Our subgraph will be notified of the closing tick price of the pool, as well as the remaining pool liquidity, allowing frontend integrations to provide realtime context of pool state.

{% embed url="https://github.com/flayerlabs/flaunch-contracts/blob/main/src/contracts/hooks/Notifier.sol" %}
Hook code
{% endembed %}

