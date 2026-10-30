# Refused: connection in innermost page list

**Rule broken:** The innermost level of a page keeps `elements` and `connections` as empty lists; the page's real connections are elsewhere. Here layer C's `connections` holds a connector.

**Stated in:** [README.md](../../README.md#layer-c), Layer C.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`connection-in-innermost-page-list.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove the entry from the innermost `connections` list. If the page should hold it, put it in the page's own list ([A page, layer by layer](../../README.md#a-page-layer-by-layer)).
