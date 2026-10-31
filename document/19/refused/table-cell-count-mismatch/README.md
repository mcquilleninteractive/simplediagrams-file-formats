# Refused: table cell count mismatch

**Rule broken:** Each row of a table has one cell for each column. Here the table has two columns and its last row one cell.

**Stated in:** [complex-shapes/table/README.md](../../complex-shapes/table/README.md#rules), Rules.

**Caught by the JSON Schemas:** No. JSON Schema cannot compare the lengths of two lists.

**The app's message (English):** "The shape 293EB0A1-5C9A-54CC-83C1-9F35497B8A03 is not a valid complex shape."

## In this folder

`table-cell-count-mismatch.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the last row its second cell back.
