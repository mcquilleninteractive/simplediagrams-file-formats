# Refused: flow pointer as number

**Rule broken:** A flow's `pointer` is `true` or `false`, never a number. Here `pointer` is `0`.

**Stated in:** [generators/com.simplediagrams.flow/README.md](../../generators/com.simplediagrams.flow/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.flow/1.schema.json`.

**The app's message (English):** "The shape C22DA3CC-A8C3-57A8-A978-B6E5A137A083 is not a valid generated shape."

## In this folder

`flow-pointer-as-number.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `pointer` to `false`.
