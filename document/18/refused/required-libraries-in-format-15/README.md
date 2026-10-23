# Refused: required libraries in format 15

**Rule broken:** `required_libraries` needs document format 16 or later; document formats 11 to 15 refuse it. Here a document format 15 package (`schema_version` 14) carries it.

**Stated in:** [libraries.md](../../libraries.md), required libraries; [README.md](../../README.md#what-an-older-document-format-refuses), What an older document format refuses.

**Caught by the JSON Schemas:** Indirectly. `manifest.schema.json` accepts only document format 18, so it refuses this package whatever it holds; no schema describes the older format's rule.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`required-libraries-in-format-15.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove `required_libraries`, or write document format 18.
