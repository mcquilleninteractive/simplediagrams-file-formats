# Refused: character run past end

**Rule broken:** A character run ends at or before the end of the text, counted in UTF-16 code units. Here the text `Hello` has 5 units and the run covers units 3 to 7.

**Stated in:** [elements.md](../../elements.md#63-runs-and-ranges), Runs and ranges.

**Caught by the JSON Schemas:** No. The check needs the length of the string.

**The app's message (English):** "The diagram contains invalid text in character run."

## In this folder

`character-run-past-end.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Make the run end at or before unit 5, for example `length` 2.
