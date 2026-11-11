# Refused: entity table with a UML box

**Rule broken:** Only a `table` has `uml_box`; an `entity_table` has no settings. Here an entity table's settings hold `uml_box`.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape 1B7164FA-E563-5812-BFA8-84BA319AB4F0 is not a valid complex shape."

## In this folder

`entity-table-with-uml-box.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `uml_box`.
