# Refused: newer format version

**Rule broken:** A reader refuses a document format newer than its own, after reading only `format` and `format_version`. Here `format_version` is 99.

**Stated in:** [README.md](../../README.md#newer-documents), Newer documents.

**Caught by the JSON Schemas:** Yes: `manifest.schema.json` allows only 19.

**The app's message (English):** "This document was saved by a newer version of SimpleDiagrams."

## In this folder

`newer-format-version.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Even without `assets/`, the app refuses it for this fault, because it checks for a newer format version before it looks for `assets/`; with an empty `assets/` folder added, it is refused the same way.

## To make it valid

Set `format_version` to 19.
