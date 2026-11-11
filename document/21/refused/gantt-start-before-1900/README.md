# Refused: Gantt task starting before 1900

**Rule broken:** A task's `start` is a day from 1900-01-01 to 2199-12-31. Here Research starts on 1899-12-31.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/gantt/1.schema.json`.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-start-before-1900.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set its `start` to 1900-01-01 or later.
