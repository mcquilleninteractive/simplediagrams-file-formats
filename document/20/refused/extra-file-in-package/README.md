# Refused: extra file in package

**Rule broken:** The package folder holds only `manifest.json`, `document.json`, `assets/` and, for a converted legacy document, `legacy/`. Here it also holds `notes.txt`.

**Stated in:** [README.md](../../README.md#the-package-on-disk), The package on disk.

**Caught by the JSON Schemas:** No. JSON Schema describes the JSON files, not the folder.

**The app's message (English):** "The SimpleDiagrams document contains an invalid notes.txt file."

## In this folder

`extra-file-in-package.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Delete `notes.txt`.
