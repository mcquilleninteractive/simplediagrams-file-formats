# Refused: ring label in the wrong slot

**Rule broken:** A known generator version names each part's slot as it declares. Here a ring section's slot is not `label`.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape 332E1132-063A-56AE-A603-AA290BCBAD11 is not a valid generated shape."

## In this folder

`ring-label-in-the-wrong-slot.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Name the slot `label`.
