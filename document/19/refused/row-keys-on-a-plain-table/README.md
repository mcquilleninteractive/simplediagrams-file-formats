# Refused: row keys on a plain table

**Rule broken:** `row_ids`, `row_marks` and `row_point_ids` belong to an entity table, with `"variant": "entity"`. Here a table without `variant` has them.

**Stated in:** [elements.md](../../elements.md#entity-tables), Entity tables.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`row-keys-on-a-plain-table.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add `"variant": "entity"`, or remove the three row keys.
