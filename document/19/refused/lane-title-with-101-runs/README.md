# Refused: lane title with 101 runs

**Rule broken:** A lane title has at most 100 runs, character runs and paragraph runs together. Here the title has 101 paragraph runs, one on each of its 101 letters.

**Stated in:** [elements.md](../../elements.md#1212-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** Yes for this case. All 101 runs are paragraph runs, and the schema limits each list of runs to 100. A title with 60 runs of each kind passes the schema and is refused by the app.

**The app's message (English):** "The diagram contains a lane title with too many text-formatting runs (101)."

## In this folder

`lane-title-with-101-runs.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove the last paragraph run.
