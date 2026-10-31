# Refused: asset setting written as plain text

**Rule broken:** An embedded-asset setting is written as a labelled record, `{"asset_id": …}`, never as plain text. Here a container's `artwork` is the text `"cccccccc…"`.

**Stated in:** [elements.md](../../elements.md#122-settings), Settings.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`asset-setting-written-as-plain-text.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `artwork`, or write it as `{"asset_id": "…"}` naming an editable vector asset the document holds.
