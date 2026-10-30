# Refused: generator name with capitals

**Rule broken:** Each label of a generator's name follows the slug rule: lower-case letters and digits in groups joined by single hyphens. Here the generator is named `com.example.Steps`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#2-the-record), The record.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape 6DC06D2D-2696-54FB-A45A-68B4DE56A9CF is not a valid generated shape."

## In this folder

`generator-name-with-capitals.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write the name in lower case: `com.example.steps`.
