# Refused: flipped ring

**Rule broken:** Among complex shapes of the types the catalogue lists, only a callout may be flipped. Here a ring has `"flip_horizontal": true`.

**Stated in:** [elements.md](../../elements.md#126-the-elements-own-keys), The element's own keys.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape ED09E43E-D5A1-570A-9214-9AA998E6E89C is not a valid complex shape."

## In this folder

`flipped-ring.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `flip_horizontal` to `false`.
