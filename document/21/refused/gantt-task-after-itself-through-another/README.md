# Refused: Gantt tasks following each other

**Rule broken:** Following `after` from task to task never comes back to where it began. Here Sketches follows Prototype, which follows Sketches.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-task-after-itself-through-another.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the `after` of Sketches.
