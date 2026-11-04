# Refused: lane holding 1,001 shapes

**Rule broken:** A lane holds at most 1,000 members. Here the one lane of the swimlanes holds 1,001 small squares.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** Yes. The schema limits a member list to 1,000 entries.

**The app's message (English):** "The diagram contains a lane holding too many shapes (1,001)."

## In this folder

`lane-holding-1001-shapes.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove one member from the lane's `members`.
