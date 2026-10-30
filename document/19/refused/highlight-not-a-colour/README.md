# Refused: highlight not a colour

**Rule broken:** A character style's `highlight_hex` is a colour, `#RRGGBB` or `#RRGGBBAA`. Here it is `#FF0`.

**Stated in:** [elements.md](../../elements.md#62-character-style), Character style.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid color value: #FF0."

## In this folder

`highlight-not-a-colour.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write the colour in full: `#FFFF00`.
