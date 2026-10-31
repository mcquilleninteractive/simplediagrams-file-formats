# Refused: three digit colour

**Rule broken:** A colour is `#RRGGBB` or `#RRGGBBAA`. Here the fill is `#FFF`.

**Stated in:** [README.md](../../README.md#terms), Terms (colour).

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid color value: #FFF."

## In this folder

`three-digit-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Write the colour in full: `#FFFFFF`.
