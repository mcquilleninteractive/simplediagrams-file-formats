# Refused: unknown key in generated record

**Rule broken:** A generated record holds only the keys the format lists. Here it has a `colour` key.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#2-the-record), The record.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-key-in-generated-record.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `colour`. A part's own colour goes in its `style`.
