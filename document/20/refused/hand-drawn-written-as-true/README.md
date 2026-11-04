# Refused: hand drawn written as true

**Rule broken:** `hand_drawn` is written only as `false`; a shape drawn with the hand-drawn pen leaves the key out. Here a ring has `"hand_drawn": true`.

**Stated in:** [elements.md](../../elements.md#121-the-structure), The structure.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`hand-drawn-written-as-true.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `hand_drawn`.
