# Refused: ring listing connection points

**Rule broken:** A type that places its own connection points lists none on its element: they are worked out as the document is read. Here a ring lists the standard eight.

**Stated in:** [complex-shapes/ring/README.md](../../complex-shapes/ring/README.md#connection-points), Connection points.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape DC94A470-21E6-536E-A58E-E20C7B86D0F4 is not a valid complex shape."

## In this folder

`ring-listing-connection-points.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Write `"connection_points": []`.
