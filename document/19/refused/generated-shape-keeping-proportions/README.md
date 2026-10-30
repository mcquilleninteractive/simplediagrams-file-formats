# Refused: generated shape keeping proportions

**Rule broken:** `preserves_aspect_ratio` is never `true` on a generated shape; `resizing` is its one resizing rule. Here the ring's element has `"preserves_aspect_ratio": true`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#9-the-elements-other-keys), The element's other keys.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape DA7E3D4D-2B91-50B7-8B94-38C770EC5719 is not a valid generated shape."

## In this folder

`generated-shape-keeping-proportions.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `preserves_aspect_ratio`.
