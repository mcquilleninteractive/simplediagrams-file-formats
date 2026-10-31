# Refused: unused shape data definition

**Rule broken:** Every entry of `shape_data_definitions` is used by at least one element. Here a second definition is used by none.

**Stated in:** [shape-data.md](../../shape-data.md), definitions.

**Caught by the JSON Schemas:** No. The check needs every element's `shape_data_definition_id`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unused-shape-data-definition.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the unused definition.
