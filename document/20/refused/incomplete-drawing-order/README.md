# Refused: incomplete drawing order

**Rule broken:** When a page has `drawableOrder`, it names every element and connector on the page exactly once. Here it leaves out the connector.

**Stated in:** [README.md](../../README.md#drawing-order), Drawing order.

**Caught by the JSON Schemas:** No. The check compares the list with the page's contents.

**The app's message (English):** "The diagram contains an invalid number in drawable order."

## In this folder

`incomplete-drawing-order.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Add the connector to `drawableOrder`, or remove `drawableOrder` altogether.
