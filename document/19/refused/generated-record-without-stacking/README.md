# Refused: generated record without stacking

**Rule broken:** `stacking` is required in the complete form. Here it is missing.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#2-the-record), The record.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`generated-record-without-stacking.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add `"stacking": "last_in_front"`, as a ring declares.
