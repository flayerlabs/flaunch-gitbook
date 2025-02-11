---
icon: lightbulb-on
---

# Creating Treasury Actions

Treasury Actions are approved contracts that allow for interactions with a memecoin's treasury. This treasury receives flETH from the Internal Swap Pool and, if the BidWall is disabled, will additionally receive flETH from swaps.

The protocol has a small trade off between the freedom for creators to do what they want with their treasury, whilst also striving to ensure that all interactions are secure to protect both holders and erroneous creator calls.

Treasury actions are extremely simple to create, but equally powerful. During the length of the transaction, the action receives full allowance for both flETH and memecoin tokens from the treasury. These approvals are then revoked after the action transaction.

{% hint style="info" %}
If your treasury action wants to interact with external protocols using native ETH, then the flETH in the treasury will first need to be withdrawn. This can be done using the [`flETH.withdraw()`](https://basescan.org/token/0x000000000d564d5be76f7f0d28fe52605afc7cf8#writeContract#F16) function call.
{% endhint %}

The following steps should be follwed to ensure that created hooks can be integrated into Flaunch seamlessly.

{% stepper %}
{% step %}
### Get Inspired

Your idea can be something that you want to develop for your own treasury, or you think it may benefit a range of projects. Anything that can benefit holders and projects would be well received.

If you're looking for some inspiration, check out the [#builders channel](https://discord.com/channels/1254802493821550684/1333741740670193724) on the Flaunch Discord!
{% endstep %}

{% step %}
### Branch and Code

A fork can be made from our [public repository](https://github.com/flayerlabs/flaunchgg-contracts). All Treasury Actions can be found in the `contracts/treasury/actions/` folder in the codebase, and all newly developed actions should live there too.

Each Treasury Action should inherit the `ITreasuryAction` interface. This will define a required tracking event that **must** be emitted, as well as a required function call that will handle the custom treasury execution logic.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

import {PoolKey} from '@uniswap/v4-core/src/types/PoolKey.sol';

interface ITreasuryAction {

    event ActionExecuted(PoolKey _poolKey, int _token0, int _token1);

    function execute(PoolKey memory _poolKey, bytes memory _data) external;

}
```

#### Registering the `ActionExecuted` event

The `ActionExecuted` event must be emitted during every execution function call, regardless of if balances will have changed. This allows our subgraph and frontend to receive a proper history of all actions that have taken place.

The `PoolKey` should just be the `PoolKey` that is passed into the `execute` function. This can just remain the same.

The `token0` and `token1` integer values will need to represent the balance change to the treasury invoked by the action for `PoolKey.currency0` and `PoolKey.currency1` respectively. For example, if we are doing a buyback in which the treasury spends 10 `currency0` and received 5 `currency1` then the event would be emitted as:

```solidity
emit ActionExecuted(_poolKey, -10e18, 5e18);
```

#### Executing action logic

When a Memecoin Treasury calls an action to be executed, it is routed through the [MemecoinTreasury](https://github.com/flayerlabs/flaunchgg-contracts/blob/main/src/contracts/treasury/MemecoinTreasury.sol#L68), which is a contract unique to each memecoin and only callable by the memecoin ERC721 owner.

This temporarily approves both PoolKey tokens and executes the action by calling the `execute` function. This function is called with the `PoolKey` and optional `bytes` data that can contain additional information required to process the action.

```solidity
function execute(PoolKey memory _poolKey, bytes memory _data) external;
```

#### Using additional Bytes logic

If additional information is required to make the treasury action function (such as slippage, merkle data or recipient arrays) then this can be provided by the `bytes memory _data`. This is passed by a frontend process, or onchain, to allow the treasury action to have additional context within it's call.

{% hint style="warning" %}
If additional `bytes`data is required, then please include this explicitly in your Pull Request to ensure that our frontend can incorporate the action parameters correctly. This should be provided with as much information as possible including field names, types, value boundaries, etc.&#x20;
{% endhint %}

#### Code examples

There is a number of production-utilised actions in the code repository, but the `BlankAction` contract is a useful starting point.

{% code title="treasury/actions/Blank.sol" fullWidth="false" %}
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

import {PoolKey} from '@uniswap/v4-core/src/types/PoolKey.sol';

import {ITreasuryAction} from '@flaunch-interfaces/ITreasuryAction.sol';


/**
 * Does nothing.
 *
 * @dev This is just used for testing purposes.
 */
contract BlankAction is ITreasuryAction {

    function execute(PoolKey memory _poolKey, bytes memory) external override {
        emit ActionExecuted(_poolKey, 0, 0);
    }

}
```
{% endcode %}
{% endstep %}

{% step %}
### Test Suite creation

Each treasury action should have a corresponding [Foundry test suite](https://book.getfoundry.sh/forge/tests). The more comprehensive the test suite, the better. PRs without an attached test suite will be rejected.
{% endstep %}

{% step %}
### PR Created

Once the action and test suite are ready, a [Pull Request can be created](https://github.com/flayerlabs/flaunchgg-contracts/pulls) against the Flaunch public code respository. PRs can be created, but an approved repository maintainer must approve the request before it can be merged into `main`.

No deployment script is required as this will be performed by the Flaunch protocol team.
{% endstep %}

{% step %}
### Internal Team Review

The pull request will then be reviewed by the Flaunch protocol team. Changes may be requested or actioned directly by the team before merging.
{% endstep %}

{% step %}
### Third Party Audit

At the discretion of Flaunch, we may work with an external party to provide a thorough security audit of the action. It would be beneficial for the developer to make themselves available for ongoing communication with the auditing team where possible.
{% endstep %}

{% step %}
### Treasury Action Deployment

Once ready, the treasury action contract will be deployed to Base Sepolia and tested. It will then be deployed to Base.
{% endstep %}

{% step %}
### Action Approval

The action will then be approved against the `TreasuryActionManager` to enable the new treasury action for usage on the protocol!
{% endstep %}
{% endstepper %}

### Frequently Asked Questions

<details>

<summary>Can my treasury action be upgradable?</summary>

We will not approve actions that are proxy contracts or upgradable contracts. This can have serious security ramifications, even if ownership is held by our internal team.

As actions can be both approved and unapproved, there is no perceived requirement for these kinds of contracts.

</details>

<details>

<summary>Can I have a private treasury action?</summary>

We prefer to allow all projects to have access to the same actions, but if there is a truly unique use case, then it is possible to revert for non-expected treasury addresses as each treasury has a unique contract address.

If you think that you have an idea that would warrant this, please reach out to us first before starting development.

</details>
