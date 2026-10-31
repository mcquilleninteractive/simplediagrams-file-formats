# Refused: newer shape with a word breaking the rule

**Rule broken:** A shape of a type the reader does not know still keeps the structure: every word in it follows the spelling rule. Here a setting of a `matrix` shape holds `Soft Edge`.

**Stated in:** [elements.md](../../elements.md#124-words), Words.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`newer-shape-with-a-word-breaking-the-rule.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Write the word as a word: `soft_edge`.
