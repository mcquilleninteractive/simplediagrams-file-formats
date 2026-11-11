# Refused: asset byte count wrong

**Rule broken:** An inventory entry's `byte_count` equals the size of its file. Here it is one byte too many.

**Stated in:** [assets.md](../../assets.md), the asset inventory.

**Caught by the JSON Schemas:** No. The check needs the file.

**The app's message (English):** "The SimpleDiagrams document contains an invalid assets file."

## In this folder

`asset-byte-count-wrong.simplediagram/` is a complete document that breaks only this rule.

## To make it valid

Set `byte_count` to the file's size.
