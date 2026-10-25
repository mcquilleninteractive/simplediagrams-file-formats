# Refused: cell with 201 runs

**Rule broken:** A table cell has at most 200 runs, character runs and paragraph runs together. Here the first cell has 201 paragraph runs, one on each of its 201 letters.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** Yes for this case. All 201 runs are paragraph runs, and the schema limits each list of runs to 200. A cell with 120 runs of each kind passes the schema and is refused by the app.

**The app's message (English):** "The diagram contains a table cell with too many text-formatting runs (201)."

## In this folder

`cell-with-201-runs.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove the first cell's last paragraph run.
