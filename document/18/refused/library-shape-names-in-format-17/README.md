# Refused: library shape names in format 17

**Rule broken:** `library_shape_names` needs document format 18; document formats 11 to 17 refuse it. Here a document format 17 package (`schema_version` 16) carries it.

**Stated in:** [libraries.md](../../libraries.md), library shape names; [README.md](../../README.md#what-an-older-document-format-refuses), What an older document format refuses.

**Caught by the JSON Schemas:** Indirectly. `manifest.schema.json` accepts only document format 18, so it refuses this package whatever it holds; no schema describes the older format's rule.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`library-shape-names-in-format-17.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `library_shape_names`, or write document format 18.
