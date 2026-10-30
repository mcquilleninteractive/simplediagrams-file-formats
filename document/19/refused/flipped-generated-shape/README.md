# Refused: flipped generated shape

**Rule broken:** A generated shape is never flipped. Here the ring has `"flip_horizontal": true`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#9-the-elements-other-keys), The element's other keys.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape 2448DAE7-AFD8-57F1-9121-73399F327F61 is not a valid generated shape."

## In this folder

`flipped-generated-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `flip_horizontal` to `false`.
