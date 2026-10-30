# Refused: entity table of three columns

**Rule broken:** An entity table has exactly two columns: names and types. Here it has three.

**Stated in:** [elements.md](../../elements.md#entity-tables), Entity tables.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "Shape A2ACE886-4FFD-5F66-8659-94831223B906 holds a container, swimlanes, table, callout or lifeline record that is not valid."

## In this folder

`entity-table-of-three-columns.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the third column.
