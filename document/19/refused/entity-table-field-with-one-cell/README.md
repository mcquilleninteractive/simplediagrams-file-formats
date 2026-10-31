# Refused: entity table field with one cell

**Rule broken:** Each of an entity table's rows has two cells: a name and a type. Here the last field has only its name.

**Stated in:** [complex-shapes/entity_table/README.md](../../complex-shapes/entity_table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/entity_table/1.schema.json`.

**The app's message (English):** "The shape 0CAE4603-1CBB-5B5E-A8AF-53D6EAD1A8E4 is not a valid complex shape."

## In this folder

`entity-table-field-with-one-cell.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the field its type cell back.
