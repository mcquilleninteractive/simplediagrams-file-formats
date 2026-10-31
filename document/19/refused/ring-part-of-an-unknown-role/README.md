# Refused: ring part of an unknown role

**Rule broken:** A known type holds only the roles its entry declares, in their order. Here a ring's part has the role `slice`.

**Stated in:** [complex-shapes/ring/README.md](../../complex-shapes/ring/README.md#parts), Parts.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape AC730CB7-F63C-584A-9D6D-90C8A893CD3B is not a valid complex shape."

## In this folder

`ring-part-of-an-unknown-role.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the part the role `section`.
