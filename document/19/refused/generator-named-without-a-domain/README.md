# Refused: generator named without a domain

**Rule broken:** A generator's name is fully qualified: at least two labels joined by dots, such as `com.simplediagrams.ring` or `com.example.steps`. Here the generator is named `steps`, a bare word.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#2-the-record), The record.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape 7F12EB32-C8D6-5FF8-9DC0-EF20C4FB76F5 is not a valid generated shape."

## In this folder

`generator-named-without-a-domain.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Name the generator under its writer's domain: `com.example.steps`.
