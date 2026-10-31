# Refused: container in group

**Rule broken:** A container or swimlanes is never a member of a group. Here the page's one group holds both of its containers, `Web tier` and `Notes`. Both containers break the same rule; nothing else is wrong.

**Stated in:** [elements.md](../../elements.md#1210-rules-across-the-page), Complex shapes: rules across the page.

**Caught by the JSON Schemas:** No. The check spans groups and elements.

**The app's message (English):** "The shape 991246F5-D565-5AAC-B093-C922F44392DF is not a valid complex shape."

The identifier is that of the first container, `Web tier`.

## In this folder

`container-in-group.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the group. A container already holds its members; it is never grouped.
