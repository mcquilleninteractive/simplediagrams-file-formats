# Refused: incomplete part with point ids

**Rule broken:** A part in the incomplete form holds only `role`, `texts` and, optionally, `id`. Here a layer has `point_ids`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-part-with-point-ids.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `point_ids`; the app numbers the parts' points when it draws the shape.
