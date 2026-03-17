# RevenueManager

If you operate an external protocol, there are a number of benefits that can come from integrating with Flaunch.

1. Create your own launchpad that gives you full control of the business model
2. Integrate your protocol's functionality into Flaunch's whitelisted treasury actions to build TVL
3. Build popular money games with the tokenized revenue streams and profit
4. Builders can take up to 100% of all fees generated, and the remaining will go to your token creator and [the community](https://docs.flaunch.gg/developer-resources/hooks/progressive-bid-wall).

The Flaunch protocol is built in a way that directly rewards the token creator though, which won't benefit an external protocol. To alleviate this, we can use an escrow contract that will capture fees as a middleware and give more granular control over fee distribution in any way wanted.

To achieve this, we created the `RevenueManager` implementation contract that is simple to integrate. Before making your call to flaunch a token you will first need to deploy an implementation of the Treasury Manager and then initialize the `RevenueManager` like below.

### Initialization <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

```solidity
/**
 * Parameters passed during manager initialization.
 *
 * @member protocolRecipient The recipient of protocol fees
 * @member protocolFee The fee that the external protocol will take (2dp)
 */
struct InitializeParams {
    address payable protocolRecipient;
    uint protocolFee;
}
```

### Deposit <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

```
No additional data required
```

### Public Calls

<details>

<summary>balances(address _recipient) returns (uint)</summary>

Allows the caller to check the balance of their position. This will check all of the creator's tokens, and if the protocol fee recipient calls this then it will also show the amount available for them to claim.

</details>

<details>

<summary>claim() returns (uint)</summary>

Allows a caller to make a claim against their whole balance, without any additional {FlaunchToken} logic being passed in the parameters.

</details>

<details>

<summary>claim(FlaunchToken[] calldata _flaunchToken) returns (uint)</summary>

Allows a creator to make claims against the fee revenues they have earned. This returns the amount of ETH claimed during the call.

If the `msg.sender` does not match the `creator` of any of the passed `FlaunchToken` , then the entire tx will revert.

</details>

<details>

<summary>creator(address _flaunch, uint _tokenId) returns (address)</summary>

The address of the user allocated as the creator of the token

</details>

<details>

<summary>creatorTotalClaimed(address _creator) returns (uint)</summary>

The total amount of ETH that a creator address has claimed

</details>

<details>

<summary>deposit(FlaunchToken calldata _flaunchToken, address _creator, bytes calldata _data)</summary>

Deposits a FlaunchToken into the manager.

</details>

<details>

<summary>getProtocolFee(uint _amount) returns (uint)</summary>

Calculates the protocol fee that will be taken from the amount passed in.

</details>

<details>

<summary>managerOwner() returns (address)</summary>

The owner of the manager that can make protected function calls.

</details>

<details>

<summary>protocolFee() returns (uint)</summary>

The fee that the external protocol will take (2dp)

</details>

<details>

<summary>protocolRecipient() returns (address)</summary>

The recipient address of the protocol revenue split

</details>

<details>

<summary>protocolTotalClaimed() returns (uint)</summary>

The total amount of ETH that the protocol has claimed

</details>

<details>

<summary>tokenPoolId(address _flaunch, uint _tokenId) returns (uint)</summary>

Maps a FlaunchToken to a PoolId for simple lookups

</details>

<details>

<summary>tokens(address _creator<em>)</em> returns (FlaunchToken[] memory flaunchTokens)</summary>

Returns an array of all FlaunchToken data assigned to the creator.

</details>

<details>

<summary>tokenTotalClaimed(address _flaunch, uint _tokenId) returns (uint)</summary>

The total amount of ETH that has been claimed for a specific token

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

<summary>setProtocolRecipient(address payable _protocolRecipient)</summary>

Allows the protocol recipient to be updated. This can allow a zero value that will bypass the protocol recipient taking a protocol fee during the claim.

</details>

<details>

<summary>transferManagerOwnership(address _newManagerOwner)</summary>

Transfers ownership of the contract to a new account (`newOwner`).

</details>

### Who is using the RevenueManager? <a href="#who-is-using-the-revenuemanager" id="who-is-using-the-revenuemanager"></a>

{% hint style="info" %}
We are currently working with a small number of launchpads to implement our RevenueManager. When they are released as public code repositories, we will share and document the implementations. Check back soon!
{% endhint %}

### ABI

{% file src="../.gitbook/assets/RevenueManager.json" %}
