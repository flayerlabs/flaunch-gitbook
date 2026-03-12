# Get Started

The core functionality of Flaunch allows any user to quickly launch tokens on the blockchain in a managed, fair and sustainable way. But what if builders could harness this infrastructure for their own token launches while retaining 100% of the revenue generated?

To help facilitate this we have put together a Token Manager Factory and Implementation flow that allows tokens to be held in external ownership to expand its functionality. By taking control of the flaunch ERC721 (full revenue rights), the Token Manager is designed to utilise the revenue of the token on behalf of the depositor.

This can lead to exciting protocols in-and-of itself, allowing the entire value chain of the Flaunch tokenization protocol to be monetized in whatever way builders see fit.

{% hint style="info" %}
Some treasury managers will be able to hold multiple tokenIds, whilst some may only be able to hold one. This will be documented on each individual manager and should be taken into consideration when planning integrations.
{% endhint %}

Utilising a Token Manager is simple. When a token is flaunched, either in the same transaction or retrospectively we can deposit it into a Token Manager implementation. It's as simple as that! The Token Manager will now own the token on the creator's behalf and manage it from there.
