# Refused: arc sweeping 720 degrees

**Rule broken:** An arc sweeps at most 360 degrees either way. Here the hole's arc runs from −90 to 630 degrees, two whole turns, ending where it starts.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#4-segments), Segments.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 18F5F351-AB54-53A9-9D0A-5D26CDCFE57A is not a valid generated shape."

## In this folder

`arc-sweeping-720-degrees.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Draw the hole as one turn: from −90 to 270 degrees.
