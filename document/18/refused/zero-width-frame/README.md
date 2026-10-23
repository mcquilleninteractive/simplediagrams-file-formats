# Refused: zero width frame

**Rule broken:** An element's frame has a width and height above 0. Here the rectangle's width is 0.

**Stated in:** [elements.md](../../elements.md#3-identity-frame-rotation-and-flips), Identity, frame, rotation and flips.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid number in element width."

## In this folder

`zero-width-frame.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the frame a width above 0.
