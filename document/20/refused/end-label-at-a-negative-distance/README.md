# Refused: end label at a negative distance

**Rule broken:** A label beside an end has a `distance` from 0 to 10,000,000 and a `normal_offset` within ±1,000,000,000. Here the start label's `distance` is -1.

**Stated in:** [connections.md](../../connections.md#labels-beside-the-ends), Labels beside the ends.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid number in connector end label distance."

## In this folder

`end-label-at-a-negative-distance.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set the start label's `distance` to 0 or more, such as `4`.
