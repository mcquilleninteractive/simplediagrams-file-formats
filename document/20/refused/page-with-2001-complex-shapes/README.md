# Refused: page with 2,001 complex shapes

**Rule broken:** A page holds at most 2,000 complex shapes, of every type. Here the page holds 2,001 lifelines.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** No. The check counts every element on the page.

**The app's message (English):** "The diagram contains a page with too many complex shapes (2,001)."

## In this folder

`page-with-2001-complex-shapes.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove one lifeline from the page's `elements`.
