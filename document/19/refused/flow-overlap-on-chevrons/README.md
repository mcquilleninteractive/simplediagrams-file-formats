# Refused: flow overlap on chevrons

**Rule broken:** A flow's spacing is one its base shape allows: only rectangles, rounded rectangles and circles overlap. Here a chevron flow has `"spacing": "overlap"`.

**Stated in:** [generators/com.simplediagrams.flow/README.md](../../generators/com.simplediagrams.flow/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.flow/1.schema.json`.

**The app's message (English):** "The shape 1E0F06D5-21D1-5625-AE40-F0DECF715EFF is not a valid generated shape."

## In this folder

`flow-overlap-on-chevrons.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `spacing` to `gap` or `touch`.
