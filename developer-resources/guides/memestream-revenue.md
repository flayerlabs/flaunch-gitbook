---
icon: calculator
---

# Memestream revenue

In an effort to simplify the user claiming experience of revenue, as well as to improve security and save gas during transactions under the hood, Flaunch uses an Escrow contract to store flETH that is assigned to a user from all of their memestream revenues.

Revenue from memestreams are allocated and are claimable per-user, as opposed to per-memestream or per-user-per-memestream. This is beneficial from a protocol perspective, but external protocols may find it hard to track and query fees onchain.

When fees are allocated we assign them to the owner of the memestream at the time of allocation. Note, however, that we do emit an event that defines the `PoolId` that has generated the revenue.

```solidity
/// Maps a user to an ETH equivalent token balance available in escrow
mapping (address _recipient => uint _amount) public balances;

/**
 * Allows a deposit to be made against a user. The amount is stored within the
 * escrow contract to be claimed later.
 *
 * @param _poolId The PoolId that the deposit came from
 * @param _recipient The recipient of the transferred token
 * @param _amount The amount of the token to be transferred
 */
function _allocateFees(PoolId _poolId, address _recipient, uint _amount) internal {
    // If we don't have fees to allocate, exit early
    if (_amount == 0) return;

    // Ensure we aren't trying to allocate fees to a zero address
    if (_recipient == address(0)) revert RecipientZeroAddress();

    balances[_recipient] += _amount;
    emit Deposit(_poolId, _recipient, nativeToken, _amount);
}
```

***

If you are wanting to query the amount that an individual memestream has earned, that it would be recommended to take one of two approaches.

### Approach 1: Subgraph Queries

{% hint style="warning" %}
**This approach is currently a WIP and will not currently work**
{% endhint %}

Although this can't be queried directly onchain (without using a platform like [**Chainlink**](https://chain.link/)) it is possible to pull through a memestream's lifetime fees via [**The Graph**](https://thegraph.com/).

```graphql
{
  collectionFees(where: {id: 0x...}) {
    lifetimeFees
  }
}
```

{% hint style="info" %}
[https://g.flayerlabs.xyz/flaunch/base-mainnet/graphql](https://g.flayerlabs.xyz/flaunch/base-mainnet/graphql)
{% endhint %}

### Approach 2: Move memestream ERC721 to escrow

If you want to be able to query the revenue of the memestream onchain, then it would be possible to create an escrow contract that holds the memestream ERC721 and when a claim is made it can update a public variable that can be queried.

The following code shows an example of an escrow contract that will allow the memestream to be held and record the claim logic for subsequent onchain calls for any memestreams held inside it.

{% hint style="warning" %}
Note that code has not been tested and is provided as-is, as a learning resource, and should not be used in production without proper audits and review.
{% endhint %}

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

import {Flaunch} from '@flaunch/Flaunch.sol';


/**
 * Allows a {Flaunch} ERC721 memestream to be held in escrow to monitor claim amounts.
 */
contract PublicTreasuryEscrow {

    error NotOriginalOwner();

    /// ERC721 Flaunch contract address
    Flaunch public immutable flaunch;

    /// Map tokenIds to the original owner that sent it
    mapping (uint _tokenId => address _owner) public originalOwner;

    /// Map earnings from the memestream whilst it is held in the escrow
    mapping (uint _tokenId => uint _amount) public earnings;

    /**
     * Sets up the contract with the initial required contract addresses.
     *
     * @param _flaunch The {Flaunch} ERC721 contract address
     */
    constructor (address _flaunch) {
        flaunch = Flaunch(_flaunch);
    }

    /**
     * Escrow an ERC721 token by transferring it to this contract and recording the original
     * owner.
     *
     * @param _tokenId The ID of the token to escrow
     */
    function initialize(uint _tokenId) public {
        // Set the original owner
        originalOwner[_tokenId] = msg.sender;

        // Transfer the token from the msg.sender to the contract
        flaunch.transferFrom(msg.sender, address(this), _tokenId);
    }

    /**
     * Reclaims the escrowed token by the original owner. Calls the `claim` function before
     * transferring the token back.
     */
    function reclaim(uint _tokenId) public {
        // Confirm that the original owner is making this call
        if (originalOwner[_tokenId] != msg.sender) {
            revert NotOriginalOwner();
        }

        // Call claim before transferring back
        claim();

        // Clear the original owner record after reclaiming
        delete originalOwner[_tokenId];

        // Transfer the token back to the original owner
        flaunch.transferFrom(address(this), msg.sender, _tokenId);
    }

    /**
     * Calls `withdrawFees` against the {PositionManager} to claim any fees allocated to this
     * escrow contract. We pull in the fees to this escrow contract to track it and then send
     * it on to the original owner.
     */
    function claim() public {
        uint startBalance = payable(address(this)).balance;

        // Claim our fees
        flaunch.positionManager().withdrawFees(address(this), true);

        // Track the increased ETH
        uint ethGain = payable(address(this)).balance - startBalance;

        // Send on the fees to the original owner
        if (ethGain != 0) {
            earnings[_tokenId] += ethGain;
        }
    }

    /**
     * Allows the contract to receive ETH when withdrawn from the flETH token.
     */
    receive () external payable {}

}
```
