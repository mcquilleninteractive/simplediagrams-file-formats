# Refused: part with two text slots

**Rule broken:** A part holds at most one text slot. Here the section has two, `label` and `note`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#5-text-areas), Text areas.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-with-two-text-slots.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the `note` slot.
