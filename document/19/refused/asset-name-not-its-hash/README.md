# Refused: asset name not its hash

**Rule broken:** An asset's `id`, and so its file name, is the SHA-256 of the file's bytes. Here the picture is stored under the digest of a different picture, and the inventory and the element use that name consistently.

**Stated in:** [assets.md](../../assets.md), the asset inventory.

**Caught by the JSON Schemas:** No. The check needs the file's bytes.

**The app's message (English):** "The SimpleDiagrams document contains an invalid assets file."

## In this folder

`asset-name-not-its-hash.simplediagram/` is a complete document that breaks only this rule.

## To make it valid

Rename the file to the SHA-256 of its bytes, and use that digest as the asset's `id`, `filename` and the element's `embedded_asset_id`.
