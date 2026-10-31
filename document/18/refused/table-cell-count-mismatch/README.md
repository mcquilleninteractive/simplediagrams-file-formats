# Refused: table cell count mismatch

**Rule broken:** A table has one row of `cells` per entry of `row_sizes` and one cell per entry of `column_sizes`. Here `row_sizes` has three entries and `cells` two rows.

**Stated in:** [elements.md](../../elements.md#127-table), Complex shapes: `table`.

**Caught by the JSON Schemas:** No. JSON Schema cannot compare the lengths of two arrays.

**The app's message (English):** "The shape 890CB3FA-71CA-55F8-A832-0F424FD90E6D is not a valid complex shape."

SimpleDiagrams 5.1 reports this as "Shape 890CB3FA-71CA-55F8-A832-0F424FD90E6D holds a container, swimlanes, table, callout or lifeline record that is not valid."

The identifier is the table's.

## In this folder

`table-cell-count-mismatch.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add the third row of cells, or remove one entry from `row_sizes`.
