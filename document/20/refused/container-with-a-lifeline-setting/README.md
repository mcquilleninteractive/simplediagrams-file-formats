# Refused: container with a lifeline setting

**Rule broken:** A known type holds only the settings its entry declares. Here a container has `head_style`, a lifeline's setting.

**Stated in:** [complex-shapes/container/README.md](../../complex-shapes/container/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/container/1.schema.json`.

**The app's message (English):** "The shape F13808BE-BE9C-5763-B822-AF83F522B2B7 is not a valid complex shape."

## In this folder

`container-with-a-lifeline-setting.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `head_style`.
