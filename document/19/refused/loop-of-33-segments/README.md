# Refused: loop of 33 segments

**Rule broken:** A loop has at most 32 segments. Here the outer circle is drawn as 33 arcs.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#4-segments), Segments.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`loop-of-33-segments.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Draw the outer circle as one arc from −90 to 270 degrees.
