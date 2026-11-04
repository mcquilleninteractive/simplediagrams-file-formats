# Refused: entity table header with a mark

**Rule broken:** An entity table's header has no marks and no points. Here the header is marked `primary_key`.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape 65DE1AF9-D172-5EFC-9735-6D60B5CC6550 is not a valid complex shape."

## In this folder

`entity-table-header-with-a-mark.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the header's `marks`.
