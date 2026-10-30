# Refused: label of 501 characters

**Rule broken:** A text area holds at most 500 characters. Here the label has 501.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#5-text-areas), Text areas.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape CFE12687-3650-5507-82C9-CC689A142E2F is not a valid generated shape."

## In this folder

`label-of-501-characters.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Shorten the label to 500 characters or fewer.
