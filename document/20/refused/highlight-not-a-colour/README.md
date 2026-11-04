# Refused: highlight not a colour

**Rule broken:** A character style's `highlight_hex` is a colour, `#RRGGBB` or `#RRGGBBAA`. Here it is `#FF0`.

**Stated in:** [elements.md](../../elements.md#62-character-style), Character style.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid color value: #FF0."

## In this folder

`highlight-not-a-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Write the colour in full: `#FFFF00`.
