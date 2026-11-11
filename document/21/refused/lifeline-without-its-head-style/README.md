# Refused: lifeline without its head style

**Rule broken:** A known type has every setting its entry declares, defaults included. Here a lifeline has no `head_style`.

**Stated in:** [complex-shapes/lifeline/README.md](../../complex-shapes/lifeline/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/lifeline/1.schema.json`.

**The app's message (English):** "The shape 76708739-93A7-5EAE-B02B-AD122C0A31B2 is not a valid complex shape."

## In this folder

`lifeline-without-its-head-style.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Add `"head_style": "box"`.
