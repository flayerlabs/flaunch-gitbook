---
icon: image
---

# Token Images

When building on Flaunch, use the Flaunch image flow first.

The recommended paths are:

* API path: Upload a `base64Image` to the Flaunch image endpoint, then use the returned `ipfsHash` as `imageIpfs`
* SDK path: Pass `metadata.base64Image` into `flaunchIPFS(...)` and let the SDK handle the IPFS flow

This keeps the launch flow aligned with the Flaunch tools and avoids extra manual metadata work.

## API Path

The Web2 API expects images in two steps:

1. Send `base64Image` to `POST /api/v1/upload-image`
2. Use the returned `ipfsHash` as `imageIpfs` when calling `launch-memecoin`

The image upload endpoint validates the image and stores it on IPFS before launch.

{% content-ref url="../flaunch-memecoin-api.md" %}
[flaunch-memecoin-api.md](../flaunch-memecoin-api.md)
{% endcontent-ref %}

## SDK Path

The SDK path is simpler when you are already building in a TypeScript app or script.

Pass the image as `metadata.base64Image` inside `flaunchIPFS(...)`.

The SDK then uploads:

1. The image to IPFS
2. The token metadata JSON to IPFS
3. The launch transaction with the generated metadata URI

This is the recommended path when the app needs direct wallet-controlled launch behavior.

## What To Pass

Use one of these formats:

* A full data URL like `data:image/png;base64,...`
* A base64 string produced from a user-uploaded file

Recommended image formats:

* `jpg`
* `png`
* `webp`
* `gif`

## If You Already Have IPFS

If you already have an IPFS-hosted image, you can still use it.

For the API path, pass the image hash as `imageIpfs`.

For lower-level onchain flows, you can work from a prepared metadata URI, but the preferred developer path is still to use the Flaunch API or SDK upload flow first.

## Recommended Build Order

1. Start with the Flaunch upload flow
2. Confirm the returned IPFS hash or metadata URI
3. Launch the token

## Related Guides

{% content-ref url="flaunching-a-coin.md" %}
[flaunching-a-coin.md](flaunching-a-coin.md)
{% endcontent-ref %}

{% content-ref url="../../artificial-intelligence/vibe-guide.md" %}
[vibe-guide.md](../../artificial-intelligence/vibe-guide.md)
{% endcontent-ref %}
