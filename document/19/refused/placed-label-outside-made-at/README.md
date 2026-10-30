# Refused: placed label outside `made_at`

**Rule broken:** A text area, turned as it is, lies inside the `made_at` frame, within 0.01 points, whether or not a person placed it. Here a placed label starts at nine tenths of the frame's width and is a fifth as wide, so it runs past the right edge.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#5-text-areas), Text areas.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 9474FD36-B073-510F-8D75-9D9F60BCC420 is not a valid generated shape."

## In this folder

`placed-label-outside-made-at.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Move the label back inside the frame: `"x": 0.75`.
