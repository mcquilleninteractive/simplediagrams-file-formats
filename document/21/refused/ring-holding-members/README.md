# Refused: ring holding members

**Rule broken:** `members` belongs only to a type that holds shapes on its whole body, such as a container. Here a ring has `"members": []`.

**Stated in:** [complex-shapes/ring/README.md](../../complex-shapes/ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape C7D1491B-9A37-5FE4-BDDD-01C489C938E2 is not a valid complex shape."

## In this folder

`ring-holding-members.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `members`.
