# Refused: ring resizing free

**Rule broken:** A known generator version has the `resizing` it declares. Here a ring version 1 has `"resizing": "free"`; a ring declares `proportional`.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape 21FF7184-A50F-5DDE-A124-4127E09E4E24 is not a valid generated shape."

## In this folder

`ring-resizing-free.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `resizing` to `proportional`.
