# Refused: flow direction diagonal

**Rule broken:** A flow's `direction` is `across` or `down`; a known type's words are a closed list. Here `direction` is `diagonal`.

**Stated in:** [complex-shapes/flow/README.md](../../complex-shapes/flow/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/flow/1.schema.json`.

**The app's message (English):** "The shape 06A4F33E-98C1-5A96-B1F4-5A8CFF762BB5 is not a valid complex shape."

## In this folder

`flow-direction-diagonal.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `direction` to `across` or `down`.
