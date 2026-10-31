# Refused: callout attachment without its tip

**Rule broken:** A callout's `tail_attachment` comes with its `tail_tip`, which records where the tail was last drawn. Here the callout has an attachment and no tip.

**Stated in:** [complex-shapes/callout/README.md](../../complex-shapes/callout/README.md#settings), Settings.

**Caught by the JSON Schemas:** Yes: `complex-shapes/callout/1.schema.json`.

**The app's message (English):** "The shape 74D6AF51-28B0-5C10-8212-DEDFDD9F68B0 is not a valid complex shape."

## In this folder

`callout-attachment-without-its-tip.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Add the tip back: `"tail_tip": {"x": 220, "y": -10}`, or remove the attachment.
