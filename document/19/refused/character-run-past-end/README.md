# Refused: character run past end

**Rule broken:** A character run ends at or before the end of the text, counted in UTF-16 code units. Here the text `Hello` has 5 units and the run covers units 3 to 7.

**Stated in:** [elements.md](../../elements.md#63-runs-and-ranges), Runs and ranges.

**Caught by the JSON Schemas:** No. The check needs the length of the string.

**The app's message (English):** "The diagram contains invalid text in character run."

## In this folder

`character-run-past-end.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Make the run end at or before unit 5, for example `length` 2.
