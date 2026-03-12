# Import Your Existing Token

To facilitate the support of importing tokens, we have introduced an approved middleware that allows for token to be imported if it is deemed valid by _**any**_ of our verifiers. These verifiers use onchain calls to to check token implementations and validate them.

```solidity
TokenImporter.initialize({
  memecoin: 0x123,           // The address of the ERC20 token being bridged in
  lpFeeAllocation: 80_00,    // The percentage of fees the LPs will take from the BidWall (2dp)
  initialMarketCap: 10_000e6 // The initial market cap of the token in USD
});
```

When importing your token you should configure the parameters to match your individual token requirements. The code comments against the function outline the logic that each parameter relates to.

{% hint style="warning" %}
If the token implementation is not deemed valid by any verifier, then the `flaunch` call will revert.
{% endhint %}

### Deployment addresses

Our bridging contract is deployed to the following addresses, with verifiers attached to them.

<table><thead><tr><th width="200.03277587890625">Chain</th><th>Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre class="language-yaml"><code class="lang-yaml">0x8dc3b85e1dc1c846ebf3971179a751896842e5dc
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0xb4a535b9d35851972736495cc52fbfdacf32e5dc
</code></pre></td></tr></tbody></table>

### Supported verifiers

We currently have the following verifiers running against the creator bridge.

| ClankerWorldVerifier | `0xf6ddfcb093be0cd0c015590cb6c5127d9ff2a20b` | Same |
| -------------------- | -------------------------------------------- | ---- |
| Doppler Verifier     | `0xedd66b080b8e9425c39d349a3fb69f480580f993` | Same |
| Paragraph Verifier   | `0x2E744436E35BC346777288B8dae2da23FD67e542` | New  |
| Solana Verifier      | `0xba28aC1540893a34476c24B2c4Fa32e0506C9055` | New  |
| Virtuals Verifier    | `0x06a089fa231aca48d2aa77365123ad9aca43d3a4` | Same |
| WhitelistVerifier    | `0x7a04367563a65db574d6b7d084fdbcf4a570c5a6` | Same |
| ZoraVerifier         | `0x656047fd43d2c3a121f2ef859d7171d7dd59f8b9` |      |

<details>

<summary>Clanker.world</summary>

Any tokens launched on clanker.world are able to be imported into Flaunch.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0xf6ddfcb093be0cd0c015590cb6c5127d9ff2a20b
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0x2874f9a30348acaaad55d74b0bec9c18f04b471a
</code></pre></td></tr></tbody></table>



</details>

<details>

<summary>Doppler</summary>

Any tokens launched on Doppler are able to be imported into Flaunch.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0xedd66b080b8e9425c39d349a3fb69f480580f993
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0x6428b5C4da36ecB070aBdcB5E1939244A3cC7fb5
</code></pre></td></tr></tbody></table>

</details>

<details>

<summary>Paragraph</summary>

Any tokens launched on Doppler are able to be imported into Flaunch.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0x2E744436E35BC346777288B8dae2da23FD67e542
</code></pre></td></tr><tr><td>Base Sepolia</td><td>n/a</td></tr></tbody></table>

</details>

<details>

<summary>Solana</summary>

Allows users to deploy CrossChain token for their solana coin, when authorized by a trusted signer.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0xba28aC1540893a34476c24B2c4Fa32e0506C9055
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0x47226918E518f205584bd75Bf81E0b532B0B3Ea7
</code></pre></td></tr></tbody></table>

**The trusted signer addresses**

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0x90664E887e4E98cf281F4C244A9957544134E358
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0x32aaadf8c9dd0de0e5b65d6cd84f786ef76c7a84
</code></pre></td></tr></tbody></table>

</details>

<details>

<summary>Virtuals</summary>

Any tokens launched on Virtuals are able to be imported into Flaunch.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0x06a089fa231aca48d2aa77365123ad9aca43d3a4
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0x6582d2bc6a7eba3b40bdf46b3868fc7ec2ff96ec
</code></pre></td></tr></tbody></table>

</details>

<details>

<summary>Zora.co</summary>

Any tokens launched on zora.co are able to be imported into Flaunch.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0x656047fd43d2c3a121f2ef859d7171d7dd59f8b9
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0x05a5763e9199b88bb591c6b112d0424b2cd7a99e
</code></pre></td></tr></tbody></table>

</details>

<details>

<summary>Custom</summary>

Whitelisted custom tokens can be imported to Flaunch.

<table><thead><tr><th width="149.940673828125">Chain</th><th width="460.043701171875">Contract Address</th></tr></thead><tbody><tr><td>Base</td><td><pre><code>0x7a04367563a65db574d6b7d084fdbcf4a570c5a6
</code></pre></td></tr><tr><td>Base Sepolia</td><td><pre><code>0xfde5b79e3e2814edd5f91e8694ae400954d9cfaa
</code></pre></td></tr></tbody></table>

</details>

### Add your protocol

If you'd like to add a custom verifier to either a protocol that you run, or just a token that you own, then reach out to us in [Discord](https://discord.gg/flaunch) and we will review the contract.
