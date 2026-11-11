# Refused: missing shape data value

**Rule broken:** An element has exactly one value for every field of its definition. Here the definition has two fields and the element one value.

**Stated in:** [shape-data.md](../../shape-data.md), an element's values.

**Caught by the JSON Schemas:** No. The check compares two lists.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`missing-shape-data-value.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Add a value for the `Flow` field. An `{"type": "empty"}` value is enough.
