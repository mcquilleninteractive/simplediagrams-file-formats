# Refused: character run splits character

**Rule broken:** Both ends of a run fall between whole characters as a person sees them. The text is `Cafe` followed by U+0301 COMBINING ACUTE ACCENT, which shows as one `é`; the run covers the first 4 units and so ends between the `e` and its accent.

**Stated in:** [elements.md](../../elements.md#63-runs-and-ranges), Runs and ranges.

**Caught by the JSON Schemas:** No. The check needs Unicode character boundaries.

**The app's message (English):** "The diagram contains invalid text in character run."

## In this folder

`character-run-splits-character.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Make the run cover all 5 units, so it ends after the accent.
