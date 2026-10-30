# Refused: incomplete drawing order

**Rule broken:** When a page has `drawableOrder`, it names every element and connector on the page exactly once. Here it leaves out the connector.

**Stated in:** [README.md](../../README.md#drawing-order), Drawing order.

**Caught by the JSON Schemas:** No. The check compares the list with the page's contents.

**The app's message (English):** "The diagram contains an invalid number in drawable order."

## In this folder

`incomplete-drawing-order.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add the connector to `drawableOrder`, or remove `drawableOrder` altogether.
