# Refused: flow pointer as number

**Rule broken:** A flow's `pointer` is `true` or `false`; a setting holds a value of the kind its entry declares. Here `pointer` is `0`.

**Stated in:** [complex-shapes/flow/README.md](../../complex-shapes/flow/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/flow/1.schema.json`.

**The app's message (English):** "The shape C22DA3CC-A8C3-57A8-A978-B6E5A137A083 is not a valid complex shape."

## In this folder

`flow-pointer-as-number.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `pointer` to `false`.
