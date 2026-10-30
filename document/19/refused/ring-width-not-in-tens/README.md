# Refused: ring width not in tens

**Rule broken:** A ring's `ring_width` is a multiple of 10 from 10 to 10,000, or `full`. Here `ring_width` is 45.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape E8B31034-F14D-5F69-A894-1604CD78C1CA is not a valid generated shape."

## In this folder

`ring-width-not-in-tens.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `ring_width` to 40 or 50.
