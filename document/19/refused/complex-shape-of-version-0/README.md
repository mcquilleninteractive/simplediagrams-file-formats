# Refused: complex shape of version 0

**Rule broken:** A complex shape's `version` is a whole number from 1 to 999. Here `version` is 0.

**Stated in:** [elements.md](../../elements.md#121-the-structure), The structure.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`complex-shape-of-version-0.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `version` to 1.
