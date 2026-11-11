# Refused: part following no part

**Rule broken:** Each entry of a part's `after` is the `id` of another part of the same shape. Here the task Prototype follows an identifier no part of the chart has.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-after-naming-no-part.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Name the task it follows, Sketches, by its `id`.
