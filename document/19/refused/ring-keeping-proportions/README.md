# Refused: ring keeping proportions

**Rule broken:** `preserves_aspect_ratio` is never `true` on a ring, flow, pyramid, cycle or gauge; its entry says how it resizes. Here a ring's element has `"preserves_aspect_ratio": true`.

**Stated in:** [complex-shapes/ring/README.md](../../complex-shapes/ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `complex-shapes/ring/1.schema.json`.

**The app's message (English):** "The shape DB593480-750C-5B1B-819C-C18A80F44400 is not a valid complex shape."

## In this folder

`ring-keeping-proportions.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `preserves_aspect_ratio`.
