---
icon: shield-quartered
---

# Auto Buybacks

Flaunch introduces a new Uniswap V4 hook called the "Progressive Bid Wall" (PBW) that uses the trading fees generated by the coin to protect its own price from dropping.

{% hint style="info" %}
Whatever the [dev doesn't take](creator-revenue.md) in fees goes to the PBW. For instance, if the dev share was 20%, then 80% would go to the PBW.
{% endhint %}

A new PBW is created for every 0.1 ETH of trading fees it receives. This places a 0.1 ETH limit order immediately below spot, reducing the impact of any selling.

<figure><img src="../.gitbook/assets/image (22).png" alt="" width="375"><figcaption><p>The PBW follows the price up as it increases</p></figcaption></figure>

If the price continues moving upwards, the PBW follows with more and more ETH being added to its size as each 0.1 ETH threshold is met.

### Turning off Progressive Bid Walls

[Memestream](revenue-stream-nft.md) owners can disable their coin's Progressive Bid Wall to allow the ETH to accumulate in the coin's treasury. This ETH can be used for approved actions starting with Full Stack Churchills that market buy the token.
