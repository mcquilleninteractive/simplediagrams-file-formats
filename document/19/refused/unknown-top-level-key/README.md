# Refused: unknown top level key

**Rule broken:** Unknown keys are refused at the top level of `document.json`. Here it has a `comment` key.

**Stated in:** [README.md](../../README.md#unknown-keys), Unknown keys.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-top-level-key.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `comment`. A document has no place for free-form notes at the top level.
