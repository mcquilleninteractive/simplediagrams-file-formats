# Refused: entity table of 61 fields

**Rule broken:** An entity table has 1 to 60 fields below its header. Here it has 61, and so, with two points a field, 130 connection points, past the 128 an element may have. SimpleDiagrams 5.2 refuses it for its fields, while reading, with the message below.

**Stated in:** [elements.md](../../elements.md#entity-tables), Entity tables.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`entity-table-of-61-fields.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the last field, with its two points.
