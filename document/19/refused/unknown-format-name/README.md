# Refused: unknown format name

**Rule broken:** `format` in `manifest.json` must be exactly `com.simplediagrams.document`. Here it is `com.example.diagram`.

**Stated in:** [README.md](../../README.md#the-manifest), The manifest.

**Caught by the JSON Schemas:** Yes: `manifest.schema.json`.

**The app's message (English):** "The document format com.example.diagram is not supported."

## In this folder

`unknown-format-name.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `format` to `com.simplediagrams.document`.
