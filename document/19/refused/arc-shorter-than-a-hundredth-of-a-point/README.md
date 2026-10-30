# Refused: arc shorter than a hundredth of a point

**Rule broken:** Every arc is at least 0.01 points long. Here the outer loop ends with an arc of radius 0.001 that turns a quarter, about 0.0016 points long.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#4-segments), Segments.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 2CAA2270-56AD-575D-BB6D-92F83398CBF1 is not a valid generated shape."

## In this folder

`arc-shorter-than-a-hundredth-of-a-point.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the short arc. A loop that closes needs nothing to join its last segment to its first.
