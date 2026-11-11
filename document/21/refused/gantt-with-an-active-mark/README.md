# Refused: Gantt task with an unknown mark

**Rule broken:** A task's marks are any of `milestone`, `done` and `crit`. Here the task Testing is marked `active`.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/gantt/1.schema.json`.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-with-an-active-mark.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the mark.
