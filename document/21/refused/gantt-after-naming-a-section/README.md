# Refused: Gantt task following a section

**Rule broken:** A task's `after` names another task, never a section. Here the task Prototype follows the section Plan.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-after-naming-a-section.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Name the task it follows, Sketches, by its `id`.
