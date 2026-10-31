# Refused: ring width not in tens

**Rule broken:** A ring's `ring_width` is a multiple of 10 from 10 to 10,000, or `full`. Here `ring_width` is 45.

**Stated in:** [complex-shapes/ring/README.md](../../complex-shapes/ring/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape E8B31034-F14D-5F69-A894-1604CD78C1CA is not a valid complex shape."

## In this folder

`ring-width-not-in-tens.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `ring_width` to 40 or 50.
