# Refused: lane title of 1,001 characters

**Rule broken:** A lane title holds at most 1,000 characters. Here the one lane's title is 1,001 letters.

**Stated in:** [elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold), Complex shapes: how much a shape and a page hold.

**Caught by the JSON Schemas:** Yes. The schema limits a lane title's `string` to 1,000 characters.

**The app's message (English):** "The diagram contains a lane title that is too long (1,001 characters)."

## In this folder

`lane-title-of-1001-characters.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead. To keep it small, its JSON is written without spaces or line breaks, and its identifiers are numbered in the order it first uses them.

## To make it valid

Remove one letter from the title's `string`.
