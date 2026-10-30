# Refused: part texture without its colour

**Rule broken:** A part's `fill_texture` comes with its own `fill_hex`, and `stroke_texture` with `stroke_hex`. Here the section has a fill texture and no fill colour.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#3-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape A7EBB321-9A0C-59AB-A37F-7B0A97FBCB8F is not a valid generated shape."

## In this folder

`part-texture-without-its-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the section its colour too: `"fill_hex": "#A5D8FF"`.
