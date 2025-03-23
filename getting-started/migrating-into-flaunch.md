---
icon: web-awesome
---

# Migrating into Flaunch

Flaunching a token brings a range of ecosystem benefits, including automated buybacks, ETH yield without impacting the token, and more. But each traditional flaunch mints a supply of 100B fresh ERC20 tokens and enters a fresh price discovery phase, usually through a Fair Launch window.

If you have an existing ERC20 token, then you'll be eligible to bridge your token into Flaunch and benefit from our core protocol features, whilst retaining all fees earned from your token through Flaunch.

### How to Onboard Your Existing ERC20

As the tokens bridged will have non-flaunch ERC20 implementation logic, we strive to prevent malicious ERC20 contracts from entwining into our platform. For this reason, we run a vetting process before a token can be migrated into Flaunch.

1. **Are you the owner** - Only the project team or owner can migrate the token into Flaunch.
2. **Vibe Check** - Does the project suit the Flaunch vibe?
3. **Contract Review** - Our protocol team will review your ERC20 token to ensure there are no malicious exploits present, and then all Flaunch functionality will gel smoothly.

{% hint style="success" %}
If you are interested in migrating your existing ERC20 to the Flaunch ecosystem, **we are currently accepting applications**. Please reach out via any of our social networks to start up conversations.
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
