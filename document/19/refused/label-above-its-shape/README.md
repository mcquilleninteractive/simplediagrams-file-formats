# Refused: label above its shape

**Rule broken:** A text area of a generated shape is placed `top`, `middle` or `bottom`, never `above` or `below`. Here the label's `position` is `above`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#5-text-areas), Text areas.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape FCA10BA5-1291-558C-87C7-7C411BF0C672 is not a valid generated shape."

## In this folder

`label-above-its-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `position` to `middle`.
