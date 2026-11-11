# Refused: Gantt chart whose sections miss a task

**Rule broken:** The sections' sizes add up to the number of tasks. Here they add up to 6, and the chart has 5 tasks.

**Stated in:** [complex-shapes/gantt/README.md](../../complex-shapes/gantt/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 7C9B4067-8797-59BB-B0A9-13916B3A54CA is not a valid complex shape."

## In this folder

`gantt-section-sizes-not-adding-up.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set the second section's `size` to `3`.
