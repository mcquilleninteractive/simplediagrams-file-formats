# Refused: audience group size not whole

**Rule broken:** An audience group's `size` is a whole number of seats from 1 to 1,000. Here the second group's `size` is 30.5.

**Stated in:** [complex-shapes/audience/README.md](../../complex-shapes/audience/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/audience/1.schema.json`.

**The app's message (English):** "The shape DAECF345-CC7B-54A5-824B-8692C9BD5B0B is not a valid complex shape."

## In this folder

`audience-group-size-not-whole.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set the second group's `size` to a whole number, such as `30`.
