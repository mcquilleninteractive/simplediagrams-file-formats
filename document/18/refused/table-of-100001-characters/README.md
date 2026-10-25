# Refused: table of 100,001 characters

**Rule broken:** All the cells of one table hold at most 100,000 characters together. Here a table of one column has fifty cells of 2,000 letters and one of a single letter: every cell is within its own limit, and the table is one over.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** No. The check adds up every cell of the table.

**The app's message (English):** "The diagram contains a table with too much text (100,001 characters)."

## In this folder

`table-of-100001-characters.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Empty the last cell's `string`.
