# Implementation

Initializing a Token Manager is simple. We have an approved, audited collection of managers available to deploy. This example will run through the deployment and initialization of a [RevenueManager](manager-types/revenuemanager.md), and subsequent deposit of flaunched tokens.

### 1. Initialize Manager

```solidity
// Contract addresses are valid for Base Sepolia
TreasuryManagerFactory factory = TreasuryManagerFactory(0xd2f3c6185e06925dcbe794c6574315b2202e9ccd);
address manager = factory.deployAndInitializeManager({
  _managerImplementation: 0x1216c723853dac0449c01d01d6e529d751d9c0c8,  // RevenueManager
  _owner: address(this),
  _data: abi.encode(
    RevenueManager.InitializeParams({
      protocolRecipient: payable(address(this)),
      protocolFee: 1000  // 10%
    })
  )
});
```

#### Fields

<details>

<summary>_managerImplementation</summary>

The address of a manager implementation that has been approved in our factory. These will be included on each Manager page in our [Manager Types](manager-types/) section.

</details>

<details>

<summary>_owner</summary>

The address approved to make authenticated calls to the manager once deployed.

</details>

<details>

<summary>_data</summary>

Any additional data required by the Token Manager implementation. These will be included on each Manager page in our [Manager Types](manager-types/) section.

</details>

### 2. Deposit Token(s)

Once your Token Manager is initialized, you can deposit Flaunch tokens into it.

```solidity
revenueManager.deposit({
    _flaunchToken: ITreasuryManager.FlaunchToken({
        flaunch: flaunch,
        tokenId: tokenId
    }),
    _creator: creator,
    _data: abi.encode('')
});
```

#### Fields

<details>

<summary>_flaunchToken</summary>

The FlaunchToken object, containing the `Flaunch` ERC721 contract address and the tokenId that is being transferred in.

</details>

<details>

<summary>_creator</summary>

The address of the wallet that flaunched the token.

</details>

<details>

<summary>_data</summary>

Any additional data required by the Token Manager implementation. These will be included on each Manager page in our [Manager Types](manager-types/) section.

</details>

### 3. Flaunch Tokens Directly to Manager

Rather than depositing an existing Flaunch ERC721, it's possible to transfer it directly to the manager when flaunching. When flaunching via the Flaunch Zap, the caller can specify `TreasuryManagerParams` in the call.

```solidity
/**
 * If the manager is an approved implementation, then it's instance will be deployed.
 * Otherwise the flaunch token will be transferred directly to the manager.
 *
 * @param manager The manager implementation to use
 * @param initializeData The data to initialize the manager with
 * @param depositData The data to deposit to the manager with
 */
struct TreasuryManagerParams {
   address manager;
   bytes initializeData;
   bytes depositData;
}
```

This will result in the flaunched token being sent directly to the manager; for a simplified example:

```solidity
(address memecoin_,, address deployedManager_) = flaunchZap.flaunch({
  _flaunchParams: PositionManager.FlaunchParams({
    // ..
    creator: YOUR_END_CREATOR,
  }),
  // ..
  _treasuryManagerParams: FlaunchZap.TreasuryManagerParams({
    manager: 0x1216c723853dac0449c01d01d6e529d751d9c0c8,  // RevenueManager
    initializeData: abi.encode(
      RevenueManager.InitializeParams({
        protocolRecipient: payable(address(this)),
        protocolFee: 1000  // 10%
      })
    ),
    depositData: abi.encode('')
  })
});
```

### 4. ???

### 5. Profit!
