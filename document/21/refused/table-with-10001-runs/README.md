# Refused: table with 10,001 runs

**Rule broken:** All the cells of one table have at most 10,000 runs together. Here a table of one column has fifty cells of 200 paragraph runs each and one of a single run.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** No. The check adds up every cell of the table.

**The app's message (English):** "The diagram contains a table with too many text-formatting runs (10,001)."

## In this folder

`table-with-10001-runs.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove the last cell's paragraph run.
