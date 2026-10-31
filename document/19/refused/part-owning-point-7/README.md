# Refused: part owning point 7

**Rule broken:** A part owns only connection points numbered from 8; points 0 to 7 are the shape's own. Here the section lists point 7.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-owning-point-7.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

List point 8 as the section's: `[8]`.
