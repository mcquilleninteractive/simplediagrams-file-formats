# Refused: flow direction diagonal

**Rule broken:** A flow version 1's `direction` is `across` or `down`; no other word is allowed. Here `direction` is `diagonal`.

**Stated in:** [generators/com.simplediagrams.flow/README.md](../../generators/com.simplediagrams.flow/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.flow/1.schema.json`.

**The app's message (English):** "The shape 06A4F33E-98C1-5A96-B1F4-5A8CFF762BB5 is not a valid generated shape."

## In this folder

`flow-direction-diagonal.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `direction` to `across` or `down`.
