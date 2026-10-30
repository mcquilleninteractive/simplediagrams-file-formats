# Refused: point owned by two parts

**Rule broken:** Each point numbered from 8 is listed by exactly one part. Here the first two sections both list point 8.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#8-connection-points), Connection points.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape FE1C9B48-6F97-55AE-8FA9-9A2A5609A542 is not a valid generated shape."

## In this folder

`point-owned-by-two-parts.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the second section its own point, number 9, and list that.
