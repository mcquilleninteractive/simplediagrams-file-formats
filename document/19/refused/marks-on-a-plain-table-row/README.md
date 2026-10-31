# Refused: marks on a plain table row

**Rule broken:** Only an entity table's fields have marks; a table's rows hold only `size` and `cells`. Here a table's row has `"marks": ["primary_key"]`.

**Stated in:** [complex-shapes/table/README.md](../../complex-shapes/table/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/table/1.schema.json`.

**The app's message (English):** "The shape B519CB0B-A927-5380-A2B8-496E67CFD67B is not a valid complex shape."

## In this folder

`marks-on-a-plain-table-row.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `marks`, or make the shape an `entity_table`.
