# Refused: extra file in package

**Rule broken:** The package folder holds only `manifest.json`, `document.json`, `assets/` and, for a converted legacy document, `legacy/`. Here it also holds `notes.txt`.

**Stated in:** [README.md](../../README.md#the-package-on-disk), The package on disk.

**Caught by the JSON Schemas:** No. JSON Schema describes the JSON files, not the folder.

**The app's message (English):** "The SimpleDiagrams document contains an invalid notes.txt file."

## In this folder

`extra-file-in-package.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Even without `assets/`, the app refuses it for this fault, because it checks for unexpected names in the package before it looks for `assets/`; with an empty `assets/` folder added, it is refused the same way.

## To make it valid

Delete `notes.txt`.
