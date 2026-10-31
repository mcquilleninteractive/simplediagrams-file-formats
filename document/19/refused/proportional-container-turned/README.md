# Refused: proportional container turned

**Rule broken:** A container whose layout is `automatic` or `proportional` is never turned: its rotation, reduced to one turn, is 0. Here a proportional container is turned 15 degrees.

**Stated in:** [complex-shapes/container/README.md](../../complex-shapes/container/README.md#rules), Rules.

**Caught by the JSON Schemas:** Yes: `complex-shapes/container/1.schema.json`.

**The app's message (English):** "The shape BA112C49-4889-52B5-8196-24C95E22F86B is not a valid complex shape."

## In this folder

`proportional-container-turned.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `rotation_degrees` to 0, or the layout to `free`.
