# Refused: zero pages

**Rule broken:** A document has 1 to 100 pages. Here `pages` is empty, so `selected_page_id` necessarily names no page either; the app reports the page count.

**Stated in:** [README.md](../../README.md#the-document), The document.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The diagram contains an invalid number of pages (0)."

## In this folder

`zero-pages.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Give the document at least one page, and name it in `selected_page_id`.
