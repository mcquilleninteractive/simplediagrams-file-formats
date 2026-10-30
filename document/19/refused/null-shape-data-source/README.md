# Refused: null shape data source

**Rule broken:** A definition's `source` is left out when there is none; it is never written as `null`. Here it is `null`.

**Stated in:** [shape-data.md](../../shape-data.md), definitions.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`null-shape-data-source.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Leave `source` out.
