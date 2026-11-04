# Refused: newer shape of 257 parts

**Rule broken:** A complex shape has at most 256 parts, whatever its type. Here a shape of a type the reader does not know has 257.

**Stated in:** [elements.md](../../elements.md#121-the-structure), The structure.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`newer-shape-of-257-parts.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the last part.
