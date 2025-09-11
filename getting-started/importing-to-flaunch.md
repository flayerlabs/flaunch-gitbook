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

<table><thead><tr><th width="212.9293212890625">Chain</th><th width="459.8172607421875">Verifier Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0xf6ddfcb093be0cd0c015590cb6c5127d9ff2a20b</code></td></tr><tr><td>Base Sepolia</td><td><code>0x2874f9a30348acaaad55d74b0bec9c18f04b471a</code></td></tr></tbody></table>

</details>

<details>

<summary>Doppler</summary>

If you are the onchain `integrator` of the `DopplerAirlock` token's `AssetData`, then you can import your token without any additional verification.

<table><thead><tr><th width="212.9293212890625">Chain</th><th width="459.8172607421875">Verifier Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0xedd66b080b8e9425c39d349a3fb69f480580f993</code></td></tr><tr><td>Base Sepolia</td><td><code>0x6428b5C4da36ecB070aBdcB5E1939244A3cC7fb5</code></td></tr></tbody></table>

</details>

<details>

<summary>Virtuals</summary>

If you are an onchain admin of a `AgentToken` (determined by `owner`) on the token contract, then you can import your token without any additional verification.

<table><thead><tr><th width="212.9293212890625">Chain</th><th width="459.8172607421875">Verifier Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0x06a089fa231aca48d2aa77365123ad9aca43d3a4</code></td></tr><tr><td>Base Sepolia</td><td><code>0x6582d2bc6a7eba3b40bdf46b3868fc7ec2ff96ec</code></td></tr></tbody></table>

</details>

<details>

<summary>Zora</summary>

If you are an onchain admin of a `ZoraCoin` (determined by `isOwner`) on the coin contract, then you can import your token without any additional verification.

<table><thead><tr><th width="212.9293212890625">Chain</th><th width="459.8172607421875">Verifier Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0x656047fd43d2c3a121f2ef859d7171d7dd59f8b9</code></td></tr><tr><td>Base Sepolia</td><td><code>0x05a5763e9199b88bb591c6b112d0424b2cd7a99e</code></td></tr></tbody></table>

</details>

As the creator, you will receive the [ERC721 memestream](../core-features/royalty-nft.md) to prove ownership and drive revenue.

To import your token, you can use our frontend UI here:

{% embed url="https://flaunch.gg/import" %}

You can also view the verified `TokenImporter` contracts below:

<table><thead><tr><th width="230.3734130859375">Chain</th><th>Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0xb47af90ae61bc916ea4b4bacffae4570e7435842</code></td></tr><tr><td>Base Sepolia</td><td><code>0xb4955a396ef07c124a59164c30d63e96a103a98b</code></td></tr></tbody></table>

### Migrating ERC20 Tokens Not Listed Above

Importing tokens from Zora, Clanker, Doppler or Virtuals is permissionless. Get started on the link below.

{% embed url="https://flaunch.gg/import" %}

As the tokens bridged will have non-flaunch ERC20 implementation logic, we strive to prevent malicious ERC20 contracts from entwining into our platform. For this reason, we created a whitelist approach for unknown tokens and run a vetting process before a token can be migrated into Flaunch.

1. **Are you the owner** - Only the project team or owner can migrate the token into Flaunch.
2. **Contract Review** - Our protocol team will review your ERC20 token to ensure there are no malicious exploits present, and then all Flaunch functionality will gel smoothly.

{% hint style="success" %}
If you are interested in migrating your existing ERC20 to the Flaunch ecosystem please reach out on [Discord](https://discord.gg/flaunch).
{% endhint %}

If your token currently resides on a chain other than Base, we can assist you in bridging the token from other chains, including Solana.

#### Whitelist Contract Addresses

<table><thead><tr><th width="230.3734130859375">Chain</th><th>Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><code>0x7a04367563a65db574d6b7d084fdbcf4a570c5a6</code></td></tr><tr><td>Base Sepolia</td><td><code>0xfde5b79e3e2814edd5f91e8694ae400954d9cfaa</code></td></tr></tbody></table>

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
