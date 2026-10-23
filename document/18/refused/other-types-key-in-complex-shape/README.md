# Refused: other types key in complex shape

**Rule broken:** A `complex_shape` record may hold only `type`, `hand_drawn` and the keys of its own type. Here a container carries `head_style`, a lifeline key.

**Stated in:** [elements.md](../../elements.md#121-the-record), Complex shapes: the record.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`other-types-key-in-complex-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `head_style` from the container.
