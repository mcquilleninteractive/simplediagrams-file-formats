# Refused: segment outside `made_at`

**Rule broken:** Every segment lies inside the `made_at` frame, within 0.01 points. Here the outer arc has radius 101 about the middle of a 200 by 200 frame, so it reaches one point past every edge.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#4-segments), Segments.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 23414DCA-541E-5E61-B900-D78480F19C64 is not a valid generated shape."

## In this folder

`segment-outside-made-at.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set the radius to 100.
