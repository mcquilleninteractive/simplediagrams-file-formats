# Refused: table without its UML box

**Rule broken:** A table in document format 21 writes its `uml_box`. Here its settings hold only `header_row`.

**Stated in:** [complex-shapes/table/README.md](../../complex-shapes/table/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/table/1.schema.json`.

**The app's message (English):** "The shape 890CB3FA-71CA-55F8-A832-0F424FD90E6D is not a valid complex shape."

## In this folder

`table-without-uml-box.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Add `"uml_box": "none"` to its settings.
