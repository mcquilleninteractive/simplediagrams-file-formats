# Refused: part point missing

**Rule broken:** Every point a part lists is among the element's `connection_points`. Here the section lists point 8 and the element has no point 8.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#8-connection-points), Connection points.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape FC14EE41-E784-5933-845B-F15CADB8046A is not a valid generated shape."

## In this folder

`part-point-missing.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add point 8 back to the element's `connection_points`: `{"id": 8, "x": 0.5, "y": 1}`.
