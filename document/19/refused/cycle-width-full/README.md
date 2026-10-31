# Refused: cycle width full

**Rule broken:** A cycle's `ring_width` is a number, a multiple of 10 from 10 to 10,000; unlike a ring's, it takes no word. Here `ring_width` is `full`.

**Stated in:** [complex-shapes/cycle/README.md](../../complex-shapes/cycle/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/cycle/1.schema.json`.

**The app's message (English):** "The shape 7AF2E03B-D891-58D1-87BE-4AE65D082343 is not a valid complex shape."

## In this folder

`cycle-width-full.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `ring_width` to a number, such as 50.
