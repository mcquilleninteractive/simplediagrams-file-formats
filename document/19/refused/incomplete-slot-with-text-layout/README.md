# Refused: incomplete slot with text layout

**Rule broken:** A slot in the incomplete form holds only `slot` and `text`. Here a step's slot has a `text_layout`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-slot-with-text-layout.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `text_layout`; the app places the label when it draws the shape.
