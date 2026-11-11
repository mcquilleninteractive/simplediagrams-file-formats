# Refused: Gantt chart ending after 2199

**Rule broken:** Every task's start and end, as worked out, lie within 1900-01-01 to 2199-12-31. Here Research starts on 2199-12-28 and its tasks run past the end of 2199.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-ending-after-2199.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Move Research's `start` back, such as to `2199-11-01`.
