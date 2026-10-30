# Refused: generated shape of 25 parts

**Rule broken:** A generated shape has 1 to 24 parts, whatever its generator. Here a shape of an unknown generator has 25.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#13-limits), Limits.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`generated-shape-of-25-parts.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the last part.
