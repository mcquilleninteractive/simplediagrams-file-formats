# Refused: page with 1,000,001 characters in its tables

**Rule broken:** The lane titles and table cells of one page hold at most 1,000,000 characters together. Here the page has ten tables of 100,000 letters each and an eleventh of one letter: every cell and every table is within its own limit, and the page is one over.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** No. The check adds up every complex shape on the page.

**The app's message (English):** "The diagram contains a page with too much text in its complex shapes (1,000,001 characters)."

## In this folder

`page-with-1000001-characters-in-tables.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Empty the eleventh table's one cell.
