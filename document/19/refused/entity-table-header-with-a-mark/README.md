# Refused: entity table header with a mark

**Rule broken:** An entity table's header row has no marks and no points. Here the header row is marked `primary_key`.

**Stated in:** [elements.md](../../elements.md#entity-tables), Entity tables.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "Shape 65DE1AF9-D172-5EFC-9735-6D60B5CC6550 holds a container, swimlanes, table, callout or lifeline record that is not valid."

## In this folder

`entity-table-header-with-a-mark.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Empty the header's marks: `[]`.
