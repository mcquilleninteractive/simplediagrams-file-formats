# Refused: shape in two containers

**Rule broken:** A shape belongs to at most one container or lane. Here both containers list the note.

**Stated in:** [elements.md](../../elements.md#1210-rules-across-the-page), Complex shapes: rules across the page.

**Caught by the JSON Schemas:** No. The check spans every container on the page.

**The app's message (English):** "The shape 33D5BD73-2FFB-555A-B77C-9ECBC8FC0B96 is not a valid complex shape."

The identifier is the second container's (`Notes`), which already holds the note.

## In this folder

`shape-in-two-containers.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the note from the first container's `members`.
