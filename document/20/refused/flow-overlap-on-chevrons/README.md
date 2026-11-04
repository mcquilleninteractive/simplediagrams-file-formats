# Refused: flow overlap on chevrons

**Rule broken:** A flow's spacing is one its base shape allows: only rectangles, rounded rectangles and circles overlap. Here a chevron flow has `"spacing": "overlap"`.

**Stated in:** [complex-shapes/flow/README.md](../../complex-shapes/flow/README.md#rules), Rules.

**Caught by the JSON Schemas:** Yes: `complex-shapes/flow/1.schema.json`.

**The app's message (English):** "The shape 1E0F06D5-21D1-5625-AE40-F0DECF715EFF is not a valid complex shape."

## In this folder

`flow-overlap-on-chevrons.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `spacing` to `gap` or `touch`.
