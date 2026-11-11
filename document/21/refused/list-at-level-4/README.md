# Refused: list at level 4

**Rule broken:** A list item's `level` is a whole number from 1 to 3. Here the bulleted item "Weight over 2 kg" has `level` 4.

**Stated in:** [elements.md](../../elements.md#65-lists), Lists.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid number in paragraph run."

## In this folder

`list-at-level-4.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set its `level` to 3 or less, such as `3`.
