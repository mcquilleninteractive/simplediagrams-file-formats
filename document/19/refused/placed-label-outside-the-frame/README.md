# Refused: placed label outside the frame

**Rule broken:** A placed text's region, turned as it is, lies inside the element's frame. Here the label starts at nine tenths of the frame's width and is a fifth as wide, so it runs past the right edge.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 428E4E45-416F-59CA-9D86-6DCF756CEEE1 is not a valid complex shape."

## In this folder

`placed-label-outside-the-frame.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Move the label back inside the frame, as it was: `"x": 0.3963735659199301`.
