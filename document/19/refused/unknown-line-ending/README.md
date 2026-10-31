# Refused: unknown line ending

**Rule broken:** A connector's `start_ending` and `end_ending` are names from a fixed list. Here `end_ending` is `arrowhead`; the arrow is `arrow`.

**Stated in:** [connections.md](../../connections.md#line-endings), Line endings.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-line-ending.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Use `arrow`.
