# Refused: collapsed rectangle

**Rule broken:** Only a note may be collapsed: `collapsed` set to `true` belongs only to `note`. Here a `rectangle` carries it.

**Stated in:** [elements.md](../../elements.md#10-which-kind-may-carry-which-key), Which kind may carry which key.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`collapsed-rectangle.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `collapsed`.
