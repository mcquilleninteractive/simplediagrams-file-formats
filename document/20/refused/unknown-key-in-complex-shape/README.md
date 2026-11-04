# Refused: unknown key in complex shape

**Rule broken:** A complex shape holds only `type`, `version`, `hand_drawn`, `settings`, `members`, `own_text_layout` and `parts`. Here a ring has a `colour` key.

**Stated in:** [elements.md](../../elements.md#121-the-structure), The structure.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-key-in-complex-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `colour`. A section's own colour goes in its part's `style`.
