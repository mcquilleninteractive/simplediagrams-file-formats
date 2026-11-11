# Refused: null shape data source

**Rule broken:** A definition's `source` is left out when there is none; it is never written as `null`. Here it is `null`.

**Stated in:** [shape-data.md](../../shape-data.md), definitions.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`null-shape-data-source.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Leave `source` out.
