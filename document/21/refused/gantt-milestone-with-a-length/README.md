# Refused: Gantt milestone with a length

**Rule broken:** A task holds `size` exactly when it is not marked `milestone`. Here the milestone Launch has a `size`.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-milestone-with-a-length.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove its `size`.
