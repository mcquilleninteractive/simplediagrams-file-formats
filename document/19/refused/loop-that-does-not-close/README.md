# Refused: loop that does not close

**Rule broken:** Each loop closes: every segment starts where the one before it ends, and the last ends where the first starts, within 0.01 points. Here the hole's arc stops at 260 degrees, short of where it started.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#4-segments), Segments.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape EA4459C8-537B-51CD-B5FF-1C6E5AA2A827 is not a valid generated shape."

## In this folder

`loop-that-does-not-close.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

End the arc at 270 degrees, where it began a turn earlier.
