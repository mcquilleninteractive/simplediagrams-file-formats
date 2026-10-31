# Refused: entity table with an unknown mark

**Rule broken:** A field's marks are words its entry lists: `primary_key`, `foreign_key`, `unique`, `indexed` and `nullable`. Here a field is marked `archived_after`.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape 70D55D45-B56F-5F05-A125-EC82F8B1EC86 is not a valid complex shape."

## In this folder

`entity-table-with-an-unknown-mark.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `archived_after`.
