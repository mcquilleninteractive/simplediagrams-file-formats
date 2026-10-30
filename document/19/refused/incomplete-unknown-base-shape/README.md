# Refused: incomplete unknown base shape

**Rule broken:** In the incomplete form, every setting holds a word the generator version knows in format 19; an open setting's newer words are not allowed. Here a flow's `base_shape` is `parallelogram`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.flow/1.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-unknown-base-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Choose one of the seven base shapes, such as `rectangle`.
