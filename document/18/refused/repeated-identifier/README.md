# Refused: repeated identifier

**Rule broken:** Document, page, element, connector, group, guide and Quick Style identifiers share one namespace: no two may be equal. Here the rectangle has the page's identifier.

**Stated in:** [README.md](../../README.md#identity-rules), Identity rules.

**Caught by the JSON Schemas:** No. Uniqueness across the document needs a program.

**The app's message (English):** "The diagram repeats identifier 748E6ED8-9628-5613-B3ED-54285F38B73B."

The identifier is the page's, which the rectangle repeats.

## In this folder

`repeated-identifier.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the rectangle an identifier of its own.
