# Refused: unknown format name

**Rule broken:** `format` in `manifest.json` must be exactly `com.simplediagrams.document`. Here it is `com.example.diagram`.

**Stated in:** [README.md](../../README.md#the-manifest), The manifest.

**Caught by the JSON Schemas:** Yes: `manifest.schema.json`.

**The app's message (English):** "The document format com.example.diagram is not supported."

## In this folder

`unknown-format-name.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `format` to `com.simplediagrams.document`.
