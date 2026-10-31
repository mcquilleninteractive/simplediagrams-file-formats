# Refused: ring without ring width

**Rule broken:** A known type has every setting its entry declares, defaults included. Here a ring has no `ring_width`.

**Stated in:** [complex-shapes/ring/README.md](../../complex-shapes/ring/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape 51084B03-0B07-58CB-BB2E-E51629BFBB3E is not a valid complex shape."

## In this folder

`ring-without-ring-width.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Add `"ring_width": 40`.
