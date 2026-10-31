# Refused: entity table header with a mark

**Rule broken:** An entity table's header has no marks and no points. Here the header is marked `primary_key`.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape 65DE1AF9-D172-5EFC-9735-6D60B5CC6550 is not a valid complex shape."

## In this folder

`entity-table-header-with-a-mark.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the header's `marks`.
