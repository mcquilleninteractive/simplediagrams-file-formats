# Refused: ring part of an unknown role

**Rule broken:** A known generator version holds only the roles it declares, in their order. Here a ring version 1 has a part whose role it does not declare.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape AC730CB7-F63C-584A-9D6D-90C8A893CD3B is not a valid generated shape."

## In this folder

`ring-part-of-an-unknown-role.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the part the role `section`, or remove it with its point.
