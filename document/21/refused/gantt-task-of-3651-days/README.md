# Refused: Gantt task of 3,651 days

**Rule broken:** A task's `size` is a whole number of days from 1 to 3,650. Here the task Research is 3,651 days long.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/gantt/1.schema.json`.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-task-of-3651-days.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set its `size` to 3,650 or less.
