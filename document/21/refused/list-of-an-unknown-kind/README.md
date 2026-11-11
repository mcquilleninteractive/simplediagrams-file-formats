# Refused: list of an unknown kind

**Rule broken:** A list item's `kind` is `bullet` or `number`. Here the first item's `kind` is `ordered`.

**Stated in:** [elements.md](../../elements.md#65-lists), Lists.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`list-of-an-unknown-kind.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set its `kind` to `number`, for a numbered list.
