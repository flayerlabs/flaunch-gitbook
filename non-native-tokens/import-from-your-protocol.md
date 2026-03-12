# Import From Your Protocol

Protocols that are supported by the `AnyPositionManager` can call the `flaunch` function on the contract directly to launch the token. The Solidity code to do this is extremely simple:

```solidity
anyPositionManager.flaunch(
    AnyPositionManager.FlaunchParams({
        memecoin: 0x123,                            // The address of the ERC20 token being migrated in
        creator: address(this),                     // The address that will receive the ERC721 position
        creatorFeeAllocation: 50_00,                // The percentage of fees the LPs will take from the BidWall (2dp)
        initialPriceParams: abi.encode('10_000e6'), // The initial market cap of the token in USD
        feeCalculatorParams: abi.encode('')         // Deprecated
    })
);
```

When importing your token you should configure the parameters to match your individual token. The code comments against the function outline the logic that each parameter relates to.

{% hint style="warning" %}
If the `msg.sender` has not been approved as a valid caller, then the `flaunch` call will revert.
{% endhint %}

### Deployment addresses

<table><thead><tr><th width="200.03277587890625">Chain</th><th>Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0x8DC3b85e1dc1C846ebf3971179a751896842e5dC
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0xb4a535b9d35851972736495cc52fbfdacf32e5dc
</code></pre></td></tr></tbody></table>

### Add your protocol

If you'd like to add migration integrations to either a protocol that you run, or just a token that you own, then reach out to us in [Discord](https://discord.gg/flaunch) and we will review the contract.
