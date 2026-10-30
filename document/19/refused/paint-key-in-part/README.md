# Refused: paint key in part

**Rule broken:** A part holds only `id`, `role`, `loops`, `style`, `texts` and `point_ids`; there is no `paint` key. Here the section has `"paint": "band"`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#3-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`paint-key-in-part.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `paint`. Every part is filled and outlined; a part without a visible fill has a fill colour with zero alpha.
