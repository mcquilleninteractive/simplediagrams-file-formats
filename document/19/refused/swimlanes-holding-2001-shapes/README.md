# Refused: swimlanes holding 2,001 shapes

**Rule broken:** All the lanes of one swimlanes shape hold at most 2,000 members together. Here the three lanes hold 1,000, 1,000 and 1 small squares: each lane is within its own limit, and the shape is one over.

**Stated in:** [elements.md](../../elements.md#1212-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** No. The check adds up every lane of the shape.

**The app's message (English):** "The diagram contains swimlanes holding too many shapes (2,001)."

## In this folder

`swimlanes-holding-2001-shapes.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove the third lane's one member.
