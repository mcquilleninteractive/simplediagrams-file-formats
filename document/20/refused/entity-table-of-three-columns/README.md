# Refused: entity table of three columns

**Rule broken:** An entity table has exactly two columns: names and types. Here it has three.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape A2ACE886-4FFD-5F66-8659-94831223B906 is not a valid complex shape."

## In this folder

`entity-table-of-three-columns.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the third column, and each row's third cell.
