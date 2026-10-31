# Refused: cell of 2,001 characters

**Rule broken:** A table cell holds at most 2,000 characters. Here the table's first cell is 2,001 letters.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** Yes. The schema limits a cell's `string` to 2,000 characters.

**The app's message (English):** "The diagram contains a table cell that is too long (2,001 characters)."

## In this folder

`cell-of-2001-characters.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove one letter from the first cell's `string`.
