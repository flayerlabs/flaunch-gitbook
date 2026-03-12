---
hidden: true
---

# ERC721OwnerFeeSplitManager

The `ERC721OwnerFeeSplitManager` allows revenue to be distributed across NFT token holders. Each NFT collection can be given a percentage (as small as 0.00001%) and token holders will be able

Multiple Flaunch stream tokens can be held inside the manager, and the creator will also receive a share of their revenue before it is distributed to the nominated addresses.

{% hint style="info" %}
Addresses that receive a fee split are immutable at point of manager creation.
{% endhint %}

### Initialization <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

```solidity
/**
 * Parameters passed during manager initialization.
 *
 * @member creatorShare The 5dp percentage share that a creator will earn from their token
 * @member recipientShares Revenue recipients and their share
 */
struct InitializeParams {
   uint creatorShare;
   RecipientShare[] recipientShares;
}

/**
 * Defines a revenue recipient and the share that they will receive.
 *
 * @member recipient The share recipient of revenue
 * @member share The 5dp percentage that the recipient will receive
 */
struct RecipientShare {
   address recipient;
   uint share;
}
```

### Deposit <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

```
No additional data required
```

### Public Read Calls

<details>

<summary>amountClaimed(address _recipient) returns (uint)</summary>

The amount claimed for the recipient

</details>

<details>

<summary>balances(address _recipient) returns (uint)</summary>

Allows the caller to check the balance of their position. This will check all of the creator's tokens and their fee split share.

</details>

<details>

<summary>creator(address _flaunch, uint _tokenId) returns (address)</summary>

The address of the user allocated as the creator of the token

</details>

<details>

<summary>creatorFees() returns (uint)</summary>

Gets the total amount of fees allocated to creators, including any fees that are pending against the manager. These pending fees will be claimed during the recipient claim flow.

</details>

<details>

<summary>creatorShare() returns (uint)</summary>

The percentage that a creator will receive before other recipients, accurate to 5 decimal places.

</details>

<details>

<summary>creatorTotalClaimed(address _creator) returns (uint)</summary>

Tracks the total claims for creators

</details>

<details>

<summary>feesCreator() returns (uint)</summary>

The total fees that have been claimed for creators

</details>

<details>

<summary>feesSplit() returns (uint)</summary>

The total fees that have been claimed for the split recipients

</details>

<details>

<summary>getCreatorFee(uint _amount) returns (uint)</summary>

Calculates the creator fee that will be taken from the amount passed in.

</details>

<details>

<summary>isValidRecipient(address _recipient, bytes memory _data) returns (bool)</summary>

Checks if the recipient has either been given a share at initialization or has any tokens that they created held in the manager.

</details>

<details>

<summary>managerFees() returns (uint)</summary>

Gets the total amount of fees held by the manager, including any fees that are pending against the manager. These pending fees will be claimed during the recipient claim flow.

</details>

<details>

<summary>managerFees() returns (uint)</summary>

Gets the total amount of fees held by the manager, including any fees that are pending against the manager. These pending fees will be claimed during the recipient claim flow.

</details>

<details>

<summary>managerOwner() returns (address)</summary>

The owner of the manager that can make protected function calls.

</details>

<details>

<summary>recipientShare(address _recipient, bytes memory _data) returns (uint)</summary>

Gets the percentage share that a recipient address is allocated from the whole of the revenue fees.

</details>

<details>

<summary>tokens(address _creator) returns (FlaunchToken[] memory flaunchTokens)</summary>

Returns an array of all FlaunchToken data assigned to the creator.

</details>

<details>

<summary>tokenTotalClaimed(address _flaunch, uint _tokenId) returns (uint)</summary>

The total amount of ETH that has been claimed for a specific token

</details>

### Public Write Calls

<details>

<summary>claim() returns (uint)</summary>

Allows for a claim call to be made without requiring any additional requirements for bytes to be passed, as these would always be unused for this FeeSplit Manager.

</details>

<details>

<summary>deposit(FlaunchToken calldata _flaunchToken, address _creator, bytes calldata _data)</summary>

Deposits a FlaunchToken into the manager.

</details>

<details>

<summary>transferRecipientShare(uint _newRecipient)</summary>

Allows the user to transfer their recipient share to another user.

The recipient share can only be transferred by the address that owns the recipient share. If the new recipient already has a recipient share, then these will be merged.

</details>

### Protected Calls

Only the owner of the manager can make these calls

Allows a protocol owner to make a claim, without any additional {FlaunchToken} logic being passed in the parameters. This returns the amount of ETH claimed during the call.

<details>

<summary>rescue(FlaunchToken calldata _flaunchToken, address _recipient)</summary>

Rescues the ERC721, extracting it from the manager and transferring it to a recipient.

</details>

<details>

<summary>setCreator(FlaunchToken calldata _flaunchToken, address payable _creator)</summary>

Allows the end-owner creator of the ERC721 to be updated by the intermediary platform. This will change the recipient of fees that are earned from the token externally and can be used for external validation of permissioned calls.

</details>

<details>

<summary>transferManagerOwnership(address _newManagerOwner)</summary>

Transfers ownership of the contract to a new account (`newOwner`).

</details>

### Who is using the AddressFeeSplitManager? <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

{% hint style="info" %}
We are currently working with a small number of launchpads to implement our AddressFeeSplitManager. When they are released as public code repositories, we will share and document the implementations. Check back soon!
{% endhint %}

### Contract Addresses

<table><thead><tr><th width="160.4202880859375">Chain</th><th>Implementation Address</th></tr></thead><tbody><tr><td>Base</td><td><em><code>coming soon</code></em></td></tr><tr><td>Base Sepolia</td><td><code>0x365EE5307C84b37CE3DC66E689e514219dA3968E</code></td></tr></tbody></table>

### ABI

{% file src="../.gitbook/assets/AddressFeeSplitManager (1).json" %}
