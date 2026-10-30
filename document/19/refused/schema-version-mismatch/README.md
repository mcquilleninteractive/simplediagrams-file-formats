# Refused: schema version mismatch

**Rule broken:** `schema_version` in `document.json` must be one less than `format_version` in `manifest.json`: 18 for document format 19. Here it is 19.

**Stated in:** [README.md](../../README.md#versions), Versions.

**Caught by the JSON Schemas:** Yes: `document.schema.json` requires 18.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`schema-version-mismatch.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `schema_version` to 18.
