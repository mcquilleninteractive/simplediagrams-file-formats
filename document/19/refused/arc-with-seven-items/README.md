# Refused: arc with seven items

**Rule broken:** An arc segment is written in full: exactly eight items. Here the outer arc leaves out its last item, its share of the wobble.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#4-segments), Segments.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`arc-with-seven-items.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add the wobble share back as the arc's eighth item: `1`.
