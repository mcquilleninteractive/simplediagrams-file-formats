# Refused: hand drawn written as true

**Rule broken:** `hand_drawn` is written only as `false`; a shape drawn with the hand-drawn pen leaves the key out. Here a ring has `"hand_drawn": true`.

**Stated in:** [elements.md](../../elements.md#121-the-structure), The structure.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`hand-drawn-written-as-true.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `hand_drawn`.
