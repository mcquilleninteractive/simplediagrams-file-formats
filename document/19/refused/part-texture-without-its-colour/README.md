# Refused: part texture without its colour

**Rule broken:** A part's `fill_texture` comes with its own `fill_hex`, and `stroke_texture` with `stroke_hex`. Here the section has a fill texture and no fill colour.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-texture-without-its-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the section its colour too: `"fill_hex": "#A5D8FF"`.
