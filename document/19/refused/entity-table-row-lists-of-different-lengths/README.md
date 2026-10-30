# Refused: entity table row lists of different lengths

**Rule broken:** `row_sizes`, `cells`, `row_ids`, `row_marks` and `row_point_ids` have one entry per row. Here `row_ids` lacks the last row's.

**Stated in:** [elements.md](../../elements.md#entity-tables), Entity tables.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`entity-table-row-lists-of-different-lengths.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add the last row's id back to `row_ids`.
