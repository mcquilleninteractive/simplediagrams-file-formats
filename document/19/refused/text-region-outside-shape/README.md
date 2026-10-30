# Refused: text region outside shape

**Rule broken:** A text region must touch its shape: `x` from `-width` to 1 and `y` from `-height` to 1, as fractions of the frame. Here `x` is 1.5, so the region starts beyond the shape's right edge.

**Stated in:** [elements.md](../../elements.md#7-text-layout), Text layout.

**Caught by the JSON Schemas:** Yes for this value (`x` above 1). The lower bound depends on `width` and needs a program.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`text-region-outside-shape.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Make `x` at most 1, for example 0.5, so the region touches the shape.
