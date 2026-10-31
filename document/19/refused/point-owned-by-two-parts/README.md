# Refused: point owned by two parts

**Rule broken:** Each connection point numbered from 8 is owned by at most one part. Here the first two sections both list point 8.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`point-owned-by-two-parts.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the second section its own point, number 9.
