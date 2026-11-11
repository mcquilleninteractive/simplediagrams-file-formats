# Refused: point owned by two parts

**Rule broken:** Each connection point numbered from 8 is owned by at most one part. Here the first two sections both list point 8.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`point-owned-by-two-parts.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Give the second section its own point, number 9.
