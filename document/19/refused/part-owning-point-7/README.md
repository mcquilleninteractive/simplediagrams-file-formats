# Refused: part owning point 7

**Rule broken:** A part owns only connection points numbered from 8; points 0 to 7 are the shape's own. Here the section lists point 7.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-owning-point-7.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

List point 8 as the section's: `[8]`.
