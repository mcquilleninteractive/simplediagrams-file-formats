# Refused: unknown element kind

**Rule broken:** An element's `kind` is one of eleven fixed values. Here it is `triangle`.

**Stated in:** [elements.md](../../elements.md#1-element-kinds), Element kinds.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-element-kind.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Use one of the eleven kinds, for example `rectangle`.
