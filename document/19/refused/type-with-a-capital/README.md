# Refused: type with a capital

**Rule broken:** A complex shape's `type` is a word: lower-case letters and digits, starting with a letter, in groups joined by single underscores. Here it is `Ring`.

**Stated in:** [elements.md](../../elements.md#124-words), Words.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`type-with-a-capital.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Write the type in lower case: `ring`.
