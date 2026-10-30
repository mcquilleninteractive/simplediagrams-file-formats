# Refused: ring without ring width

**Rule broken:** A known generator version has every setting it declares; none has a default. Here a ring version 1 has no `ring_width`.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape 51084B03-0B07-58CB-BB2E-E51629BFBB3E is not a valid generated shape."

## In this folder

`ring-without-ring-width.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add `"ring_width": 40`.
