# Refused: flow base shape with a hyphen

**Rule broken:** Every word in a complex shape is lower-case letters and digits, starting with a letter, in groups joined by single underscores. Here a flow's `base_shape` is `rounded-rectangle`.

**Stated in:** [elements.md](../../elements.md#124-words), Words.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`flow-base-shape-with-a-hyphen.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write the word with an underscore: `rounded_rectangle`.
