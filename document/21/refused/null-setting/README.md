# Refused: null setting

**Rule broken:** A setting's value is never `null`. Here a container's `layout` is `null`.

**Stated in:** [elements.md](../../elements.md#122-settings), Settings.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`null-setting.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Write the layout: `"layout": "free"`.
