# Refused: gauge needle with a label

**Rule broken:** A part holds only the keys its role declares: a gauge's needle has no text. Here the needle has one.

**Stated in:** [complex-shapes/gauge/README.md](../../complex-shapes/gauge/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/gauge/1.schema.json`.

**The app's message (English):** "The shape 5C579624-BB64-59D0-8EAD-B60AA9DB45DD is not a valid complex shape."

## In this folder

`gauge-needle-with-a-label.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the needle's `text`.
