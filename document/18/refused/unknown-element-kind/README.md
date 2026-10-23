# Refused: unknown element kind

**Rule broken:** An element's `kind` is one of eleven fixed values. Here it is `triangle`.

**Stated in:** [elements.md](../../elements.md#1-element-kinds), Element kinds.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-element-kind.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Use one of the eleven kinds, for example `rectangle`.
