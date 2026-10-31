# Refused: container with a lifeline setting

**Rule broken:** A known type holds only the settings its entry declares. Here a container has `head_style`, a lifeline's setting.

**Stated in:** [complex-shapes/container/README.md](../../complex-shapes/container/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/container/1.schema.json`.

**The app's message (English):** "The shape F13808BE-BE9C-5763-B822-AF83F522B2B7 is not a valid complex shape."

## In this folder

`container-with-a-lifeline-setting.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `head_style`.
