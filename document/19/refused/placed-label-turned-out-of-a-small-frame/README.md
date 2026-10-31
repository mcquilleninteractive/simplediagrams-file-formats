# Refused: placed label turned out of a small frame

**Rule broken:** A placed text's region, turned as it is, lies inside the element's frame, whatever the shape's size, even below the smallest frame its type is drawn for. Here a ring 10 by 16 points has a label 4 by 1.28 points turned upright, so one corner reaches 0.56 points above the frame.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 47503CAF-46A1-551F-A4D8-1BCB12234B38 is not a valid complex shape."

## In this folder

`placed-label-turned-out-of-a-small-frame.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Leave the label level: remove `rotation_degrees`.
