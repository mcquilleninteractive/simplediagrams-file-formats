# Refused: incomplete version 2

**Rule broken:** The incomplete form names one of the five generators format 19 lists, at version 1. Here `generator_version` is 2.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-version-2.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `generator_version` to 1.
