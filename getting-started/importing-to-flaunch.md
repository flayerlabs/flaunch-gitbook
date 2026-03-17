---
icon: web-awesome
---

# Importing to Flaunch

Flaunching a token brings a range of ecosystem benefits, including automated buybacks, ETH yield without impacting the token, and more. Each traditional flaunch mints a supply of 100B fresh ERC20 tokens and enters a fresh price discovery phase, usually through a Fair Launch window.

But if you launched your token on another protocol, you can still get access to the post-launch benefits that our optimised Uniswap V4 pools offer. If you have an existing ERC20 token, then you'll likely be eligible to bridge your token into Flaunch and benefit from our core protocol features, whilst retaining all fees earned from your token through Flaunch.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FZ7N2KDzpDSvxEq8jqwXq%2Fuploads%2FO99oTcNHpKI6Yc3WhPlB%2FImport-Tokens.mp4?alt=media&token=a72564a6-b072-4dca-be35-5ed7139d109b" %}

### Supported External Protocols

If you launched your ERC20 through one of these existing launchpads and you are the owner, then you can directly initialize a Flaunch pool with that token. Any token holders can then add single- or double-sided liquidity against the new Uniswap V4 pool.

<details>

<summary>Clanker</summary>

If you are the onchain `admin` of the `ClankerToken` then you can import your token without any additional verification.

</details>

<details>

<summary>Doppler</summary>

If you are the onchain `integrator` of the `DopplerAirlock` token's `AssetData`, then you can import your token without any additional verification.

</details>

<details>

<summary>Virtuals</summary>

If you are an onchain admin of a `AgentToken` (determined by `owner`) on the token contract, then you can import your token without any additional verification.

</details>

<details>

<summary>Zora</summary>

If you are an onchain admin of a `ZoraCoin` (determined by `isOwner`) on the coin contract, then you can import your token without any additional verification.

</details>

As the creator, you will receive the [ERC721 memestream](../core-features/royalty-nft.md) to prove ownership and drive revenue.

To import your token, you can use our frontend UI here:

{% embed url="https://flaunch.gg/import" %}

<figure><img src="../.gitbook/assets/Screenshot 2025-10-22 at 16.18.57.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
To calculate the "Current Coin Price" then you can take the `totalSupply` response from the ERC20 and divide it by the market cap value.\
\
For example, if the `totalSupply()` is `1,000,000,000` and your market cap is `$50,000` , then the Current Coin Price should be:

```
50,000 USDC / 1,000,000,000 Total Supply = 0.00005
```

You do not need to factor in decimal accuracies in either the USDC coin or the coin being imported
{% endhint %}

#### Migrating ERC20 Tokens Not Listed Above

If you have an ERC20 token from a protocol not listed above, we created a whitelist approach to allow us to approve specific tokens to be imported by specific EOA addresses.

As the tokens bridged will have non-flaunch ERC20 implementation logic, we strive to prevent malicious ERC20 contracts from entwining into our platform. For this reason, we run a vetting process before a token can be migrated into Flaunch:

1. **Are you the owner** - Only the project team or owner can migrate the token into Flaunch.
2. **Contract Review** - Our protocol team will review your ERC20 token to ensure there are no malicious exploits present, and then all Flaunch functionality will gel smoothly.

{% hint style="success" %}
If you are interested in migrating your existing ERC20 to the Flaunch ecosystem please reach out on [Discord](https://discord.gg/flaunch).
{% endhint %}

If your token currently resides on a chain other than Base, we can assist you in bridging the token from other chains, including Solana.

### What Flaunch Unlocks for Existing Tokens

When a token is bridged into Flaunch:

* An ERC721 will be minted that shows ownership of the memestream
  * All ETH yield will be sent to the owner of this ERC721
  * Logic and functionality can be expanded with [Treasury Managers](../developer-resources/treasury-managers/)
* Unlike a traditional Flaunch, no ERC20 will be created nor minted. An initial ETH price will be set for your token and then an optional liquidity position can be created in the same transaction
  * Other parties can also create additional liquidity position, but fees will still be directed back solely to the ERC721 memestream owner and the [BidWall](../developer-resources/hooks/progressive-bid-wall.md). If you want to reward other LPs, then a TreasuryManager can be set up to facilitate this as a management strategy
* With your new token, you will have a [Progressive Bid Wall](../developer-resources/hooks/progressive-bid-wall.md) that will facilitate buy positions from any community rev that you assign
* All revenue will be distributed to the memestream owner solely in ETH, preserving your token floor and not requiring you to dump on your holders
* The security and gas optimisations from being built on top of Flaunch and Uniswap V4

### What Next?

Once your token has been imported, you will now have a Flaunch token, but with no liquidity available to swap against. You can then add liquidity by going to the Add Liquidity URL and selecting your new token:

{% embed url="https://flaunch.gg/add-liquidity" %}

<figure><img src="../.gitbook/assets/Screenshot 2025-10-22 at 16.25.13.png" alt=""><figcaption></figcaption></figure>

We have some helpers that will assist in setting a full range or concentrated liquidity position.

#### Liquidity Recommendations

The liquidity you add is completely up to you and the project that you are wanting to launch; however, we would propose the following suggestions:

* Ideally adding full range liquidity to support both sides of the swap. If you **are** adding concentrated liquidity try to at least add some ETH to your position, ideally no less than 25% of the corresponding token value.
* Supply sufficient liquidity to be picked up by routers and to support initial price discovery swaps when your listing appears.
* The more liquidity added, the better!
