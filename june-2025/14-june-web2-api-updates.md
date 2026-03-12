---
description: >-
  Introducing new fee management capabilities that make revenue sharing more
  flexible and user-friendly.
---

# 14 June: Web2 API Updates

### What's New

A new endpoint for the [Create a Fee Split Manager](https://docs.flaunch.gg/references/api#create-a-fee-split-manager-launchpad) which allows you to create a reuseable contract to receive fees to multiple addresses, and an update to the existing [Launch Token endpoint](https://docs.flaunch.gg/references/api#launch-token) allowing you to pass an existing manager or an array of addresses to create a manager on the fly.

#### New Fee Split Recipients

Fee split managers can now be created to share the revenue between multiple wallets. Pass an array of addresses, or an array of objects with email/twitter/farcaster.

#### Custom Fee Split Percentages

Either pass no split percentages to have the API calculate an even split between addresses, or specify an ahoc split between them (precision up to 5 decimal places)

#### Multi-Format Fee Recipients

The fee split manager now supports three flexible input formats:&#x20;

**Legacy Format (Still Supported)**

```
{
  "treasuryType": "fee_split_manager",
  "feeRecipients": [
    "0x1234567890123456789012345678901234567890",
    "0x0987654321098765432109876543210987654321"
  ]
}
// Result: 50% each
```

**New Enhanced Format**

```
{
  "treasuryType": "fee_split_manager", 
  "feeRecipients": [
    { "type": "email", "id": "alice@example.com" },
    { "type": "twitter", "id": "bob_crypto" },
    { "type": "farcaster", "id": "charlie.eth" },
    { "type": "wallet", "id": "0x1234...7890" }
  ]
}
// Result: 25% each
```

```
{
  "feeRecipients": [
    { "type": "email", "id": "dev@team.com", "split": "6000000" },
    { "type": "twitter", "id": "marketing_lead", "split": "2500000" },
    { "type": "wallet", "id": "0x...", "split": "1500000" }
  ]
}
// Result: 60%, 25%, 15% respectively
```

**Supported Account Types:**

* **Email:** Any valid email address,
* **Twitter:** Twitter username (with or without @),
* **Farcaster:** Farcaster username or .eth domain,
* **Wallet:** Ethereum wallet address (0x format)

**Split Format:** Percentages are specified in "micro-percentages" where:

* `"10000000"` = 100.00000%,
* `"5000000"` = 50.00000%,
* `"3333333"` = 33.33333%,

#### Validation Rules

The system enforces intelligent validation:

* **Mixed Splits Not Allowed:** If ANY recipient has a custom split, ALL recipients must have splits
* **Perfect Sum Required:** Custom splits must sum to exactly 100.00000% (10,000,000 micro-percentage units)
* **Recipient Limit:** Maximum of 100 fee split recipients
* **Account Resolution:** All social accounts are automatically resolved to wallet addresses via Privy.

### Benefits

#### For Project Creators

* **Simplified Collaboration:** Invite team members using their social accounts,
* **Flexible Revenue Sharing:** Customize splits based on contribution levels,
* **Reduced Friction:** No need to collect wallet addresses upfront,
* **Professional Management:** Automated resolution and validation,

#### For Recipients,

* **Easy Onboarding:** Receive revenue shares using existing social accounts,
* **Transparent Distribution:** Clear visibility into split percentages,
* **Automatic Setup:** Wallet addresses resolved automatically via Privy,

#### For Developers

* **Backward Compatibility:** Existing integrations continue to work,
* **Better Error Handling:** Detailed validation messages for troubleshooting,
