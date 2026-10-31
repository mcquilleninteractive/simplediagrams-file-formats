# Refused: legacy source claimed but missing

**Rule broken:** `contains_legacy_source` must agree with the package: `true` requires `legacy/source.sdxml`. Here it is `true` and there is no `legacy/` folder.

**Stated in:** [README.md](../../README.md#the-manifest), The manifest.

**Caught by the JSON Schemas:** No. Whether `legacy/source.sdxml` exists is a fact about the folder.

**The app's message (English):** "The SimpleDiagrams document is missing legacy/source.sdxml."

## In this folder

`legacy-source-claimed-but-missing.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set `contains_legacy_source` to `false`. A program writing a new document never creates `legacy/`.
