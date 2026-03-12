---
description: >-
  The first set of updates were pushed this week of new features and tweaks
  based on user feedback.
---

# 14 February: Meme Manager, Swap Permit, Memecoin Preview, Performance fixes

## Meme Manager

The new updates to the Meme Manager now allows holders of the Meme token to control how the community fees are spent.

* Enable Auto Buy Backs
* Burn Tokens
* Market Buy Tokens (when automated buys are disabled)

<div><figure><img src="../.gitbook/assets/Screenshot 2025-02-17 at 15.55.35.png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/Screenshot 2025-02-17 at 15.55.44.png" alt=""><figcaption></figcaption></figure></div>

You can also now more easily transfer the ownership of the Memestream using ENS or a full wallet address.

Remember, once you transfer the ownership the new holder will begin to earn the Fee Receiver portion of the fees and also have control over how the Community earnings are managed.

<figure><img src="../.gitbook/assets/Read only.png" alt=""><figcaption></figcaption></figure>

## Latest Rewards

The homepage now celebrates the creator/meme coin owners by sharing the latest claims on the homepage.

{% embed url="https://youtu.be/q-ICSdyXk4g" %}

It's worth restating that all of the rewards are earned as part o the 1% swap fee, and zero tokens need to be sold for the creator to earn their rewards.



<details>

<summary>Bug Fixes</summary>

* **Fix Warpcast URL**: perviously defaulting links back to the `base-sepolia` endpoint, now chain specific.
* **Round down fair launch supply**: 99.65% was previously showing at 100% sold.&#x20;
* **TX history not showing on coin pages**: only a single page history was showing on some coins
* **Privy Wallect Connect**: Privy was showing a wallet connect warning and preventing the connection.
* **Setting Premine to 0.01 error**: no longer throws an error when set to tiny amounts

</details>

<details>

<summary>New Features</summary>

* **Update Sepolia Base contracts**: Sepolia site and subgraphs now using the lastest contracts
* **API updates for gated chat**: Only allow users that hold token supply to participate on coin chat.
* **Add meme treasury activity**: Activity feed now also shows any activity through the meme treasury, along with swaps, claims, and new token launches.
* **API endpoint for schedule launches**: Allows users to be able to schedule their token launches in the future. Next steps will be adding the feature to the existing token creation UI.
* **Homepage UI updates**: fees claim animation, just launched replaced by revenues generated, content updatse.



</details>

