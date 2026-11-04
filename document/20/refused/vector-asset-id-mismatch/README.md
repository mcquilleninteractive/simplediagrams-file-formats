# Refused: vector asset id mismatch

**Rule broken:** A `.sdvector` file's `asset_id` is the SHA-256 of the canonical encoding of its artwork. Here it is 64 zeros. The file name is still the correct digest of the file's bytes.

**Stated in:** [assets.md](../../assets.md), the vector artwork format, `asset_id`.

**Caught by the JSON Schemas:** No. `vector-artwork.schema.json` checks only the form of `asset_id` (64 lower-case hexadecimal digits), which this file meets; it cannot compute the value.

**The app's message (English):** "The SimpleDiagrams document contains an invalid assets file."

## In this folder

`vector-asset-id-mismatch.simplediagram/` is a complete document that breaks only this rule.

## To make it valid

Set `asset_id` to the SHA-256 of the artwork's canonical encoding. The file name and inventory entry change with the bytes.
