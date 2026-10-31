# Refused: cycle direction sideways

**Rule broken:** A cycle's `direction` is `clockwise` or `anticlockwise`. Here `direction` is `sideways`.

**Stated in:** [complex-shapes/cycle/README.md](../../complex-shapes/cycle/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/cycle/1.schema.json`.

**The app's message (English):** "The shape F2C04E1E-C147-5923-8833-5563EE997B54 is not a valid complex shape."

## In this folder

`cycle-direction-sideways.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `direction` to `clockwise` or `anticlockwise`.
