# Refused: entity table of 61 fields

**Rule broken:** An entity table has 1 to 60 fields below its header. Here it has 61.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape E4F8E1CE-0433-5961-9C8B-547FEB49C38E is not a valid complex shape."

## In this folder

`entity-table-of-61-fields.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the last field.
