# Refused: three digit colour

**Rule broken:** A colour is `#RRGGBB` or `#RRGGBBAA`. Here the fill is `#FFF`.

**Stated in:** [README.md](../../README.md#terms), Terms (colour).

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid color value: #FFF."

## In this folder

`three-digit-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write the colour in full: `#FFFFFF`.
