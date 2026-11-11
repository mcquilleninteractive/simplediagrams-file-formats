# Refused: gauge needle before its bands

**Rule broken:** A known type's parts come in the order of its roles: a gauge's bands, then its needle. Here the needle comes first.

**Stated in:** [complex-shapes/gauge/README.md](../../complex-shapes/gauge/README.md#parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape D6EA4981-18A3-5DB9-B7A5-F43AB8BB5E0A is not a valid complex shape."

## In this folder

`gauge-needle-before-its-bands.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Move the needle to the end of `parts`.
