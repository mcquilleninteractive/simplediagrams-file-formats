# Refused: null setting

**Rule broken:** A setting's value is never `null`. Here a container's `layout` is `null`.

**Stated in:** [elements.md](../../elements.md#122-settings), Settings.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`null-setting.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write the layout: `"layout": "free"`.
