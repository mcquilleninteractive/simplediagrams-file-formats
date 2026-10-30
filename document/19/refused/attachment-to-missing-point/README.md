# Refused: attachment to missing point

**Rule broken:** An attached connector end names a connection point the element has. Here the end attachment names point 9 on a rectangle with points 0 to 7.

**Stated in:** [connections.md](../../connections.md#attachments), Attachments.

**Caught by the JSON Schemas:** No. The check needs the target element's points.

**The app's message (English):** "Connection 602FF722-E8D6-5BE4-9350-E381781C87B7 refers to a missing element."

The identifier is the connector's.

## In this folder

`attachment-to-missing-point.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Attach the end to a point the rectangle has, for example 0, the middle of its left edge.
