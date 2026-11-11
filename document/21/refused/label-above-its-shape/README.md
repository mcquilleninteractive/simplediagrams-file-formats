# Refused: label above its shape

**Rule broken:** A placed text's `position` is `top`, `middle` or `bottom`, never `above` or `below`. Here the section's label has `"position": "above"`.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`label-above-its-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `position` to `middle`.
