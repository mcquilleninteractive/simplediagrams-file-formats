# Refused: part texture without its colour

**Rule broken:** A part's `fill_texture` comes with its own `fill_hex`, and `stroke_texture` with `stroke_hex`. Here the section has a fill texture and no fill colour.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-texture-without-its-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Give the section its colour too: `"fill_hex": "#A5D8FF"`.
