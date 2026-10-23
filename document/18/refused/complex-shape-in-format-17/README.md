# Refused: complex shape in format 17

**Rule broken:** Complex shapes need document format 18. Here a container is written in a document format 17 package (`format_version` 17, `schema_version` 16).

**Stated in:** [elements.md](../../elements.md#14-reading-older-document-formats), Reading older document formats.

**Caught by the JSON Schemas:** Indirectly. `manifest.schema.json` accepts only document format 18, so it refuses this package whatever it holds; no schema describes the older format's rule.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`complex-shape-in-format-17.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write document format 18: `format_version` 18 and `schema_version` 17.
