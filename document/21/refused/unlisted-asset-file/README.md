# Refused: unlisted asset file

**Rule broken:** `assets/` holds exactly the files listed in `embedded_assets`. Here it holds a second bitmap image that the inventory does not list.

**Stated in:** [assets.md](../../assets.md), the asset inventory.

**Caught by the JSON Schemas:** No. JSON Schema cannot see the folder.

**The app's message (English):** "The SimpleDiagrams document contains an invalid assets file."

## In this folder

`unlisted-asset-file.simplediagram/` is a complete document that breaks only this rule.

## To make it valid

Delete the unlisted file, or add an inventory entry for it.
