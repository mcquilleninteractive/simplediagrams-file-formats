# Refused: element in innermost page list

**Rule broken:** The innermost level of a page keeps `elements` and `connections` as empty lists; the page's real elements are elsewhere. Here layer C's `elements` holds an element.

**Stated in:** [README.md](../../README.md#layer-c), Layer C.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`element-in-innermost-page-list.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the entry from the innermost `elements` list. If the page should hold it, put it in the page's own list ([A page, layer by layer](../../README.md#a-page-layer-by-layer)).
