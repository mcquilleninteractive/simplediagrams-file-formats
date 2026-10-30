# Refused: repeated part id

**Rule broken:** Each part's `id` is unique within its shape. Here the second section has the first section's `id`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#3-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape FCEC5E2E-4701-5CF3-8DE4-6A1F14F11A4A is not a valid generated shape."

## In this folder

`repeated-part-id.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the second section a UUID of its own.
