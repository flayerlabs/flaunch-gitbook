---
hidden: true
icon: people-roof
---

# Treasury Managers



The core functionality of Flaunch allows creators and degens to quickly launch tokens and share it with the blockchain in a managed, fair and sustainable way. But what if the creator wants more control over how their token is managed?

To help facilitate this we have put together a Treasury Manager Factory and Implementation flow that allows the token to be held in external ownership to expand it's functionality. By taking control of the flaunch ERC721, the Treasury Manager is designed to utilise the revenue of the token on behalf of the depositor.

This can lead to exciting protocols in-and-of itself, allowing the entirety of the Flaunch process to be taken behind the scenes of another protocol and just aid to facilitate faster and secure building.

{% hint style="info" %}
Some treasury managers will be able to hold multiple tokenIds, whilst some may only be able to hold one. This will be documented on each individual manager and should be taken into consideration when planning integrations.
{% endhint %}

Utilising a Treasury Manager is simple. When a token is flaunched, in the same transaction we can deploy an approved Treasury Manager implementation and then initialize it with our newly flaunched token. It's as simple as that! The Treasury Manager will now own the token on the creator's behalf and manage it from there.

```solidity
// 1. Flaunch the token, keeping the creator as this contract
address memecoin = PositionManager.flaunch(
  PositionManager.FlaunchParams({
    creator: address(this),
    ...
  })
);

// Get the tokenId of the memecoin that was just flaunched
uint tokenId = flaunch.tokenId(memecoin);

// 2. Deploy the treasury manager via the TreasuryManagerFactory. This will
// use an approved implementation to create a unique version of the manager.
TreasuryManagerFactory factory = TreasuryManagerFactory(0x123);
address payable _manager = factory.deployManager(managerImplementation);

// Approve the ERC721 to be transferred by the new manager
flaunch.approve(_manager, tokenId);

// 3. Initialize our manager. This will transfer the token from this address
// to the deployed treasury manager contract. Each treasury manager will
// expect different data to be passed inside the `_data` parameter and will
// usually be defined by their own internal InitializeParams struct.
ITreasuryManager(_manager).initialize({
  _tokenId: tokenId,      // The Flaunch tokenId that will be transferred
  _owner: address(this),  // Make this contract the owner of the manager
  _data: abi.encode(...)  // Passes variables to configure the manager
});
```

This can be simplified by calling our **CreateWithManagerZap** that will automatically deploy a manager implementation and transfer it to the newly deployed manager in a single call.

```solidity
/**
 * Flaunches a new token and premines some tokens for the creator in exchange
 * for ETH. The manager receives the flaunch creator NFT.
 * 
 * @dev If the manager is an approved implementation, then it's instance
 * will be deployed. Otherwise the flaunch token will be transferred directly
 * to the manager.
 *
 * @param _params The parameters for the flaunch
 * @param _manager The manager implementation to use
 * @param _managerInitData The data to initialize the manager with
 *
 * @return memecoin_ The created ERC20 token address
 * @return ethSpent_ The amount of ETH spent during the premine
 * @return deployedManager_ The address of the manager that was deployed
 */
function flaunch(
  PositionManager.FlaunchParams calldata _params,
  address _manager,
  bytes calldata _managerInitData
) external payable returns (
  address memecoin_,
  uint ethSpent_,
  address deployedManager_
);
```

### Approved Managers

To help protect creators and also to ensure our frontend interface correctly handles all managers, we only allow **approved** treasury managers to be deployed via our official Treasury Manager Factory. Before being approved, both our internal team and an external auditor will ensure that it conforms to the levels of security we would expect.

{% content-ref url="revenuemanager.md" %}
[revenuemanager.md](revenuemanager.md)
{% endcontent-ref %}

{% hint style="info" %}
Have an idea for a Treasury Manager? We have are putting together a builders fund to help! Get in touch on [our Discord](https://discord.gg/flaunch) to find out more!
{% endhint %}

Of course, if you have bespoke protocol logic you want to apply, you can work with contracts outside of our Treasury Manager ecosystem, but in those instances we can't speak to the safety and efficacy of those contracts.

### Deployment Addresses

Our `TreasuryManagerFactory` can be found at the following addresses:

<table><thead><tr><th width="158">Chain</th><th>Address</th></tr></thead><tbody><tr><td>Base</td><td><em>Coming soon</em></td></tr><tr><td>Base Sepolia</td><td><code>0x98afe08a4a0abc7019ed70e58b08229e1a680683</code></td></tr></tbody></table>

Our `CreateWithManagerZap` can be found at the following addresses:

<table><thead><tr><th width="158">Chain</th><th>Address</th></tr></thead><tbody><tr><td>Base</td><td><em>Coming soon</em></td></tr><tr><td>Base Sepolia</td><td><em>Coming soon</em></td></tr></tbody></table>

### Source Code

{% embed url="https://github.com/flayerlabs/flaunchgg-contracts/blob/dev/src/contracts/treasury/managers/TreasuryManagerFactory.sol" %}
TreasuryManagerFactory.sol
{% endembed %}

{% embed url="https://github.com/flayerlabs/flaunchgg-contracts/blob/dev/src/contracts/zaps/CreateWithManagerZap.sol" %}
CreateWithManagerZap.sol
{% endembed %}
