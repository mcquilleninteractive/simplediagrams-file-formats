# Refused: incomplete unknown generator

**Rule broken:** The incomplete form names one of the five generators format 19 lists, at version 1. Here it names `com.example.spiral`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-unknown-generator.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Name `com.simplediagrams.ring`, or write the shape in the complete form.
