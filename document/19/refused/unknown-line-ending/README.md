# Refused: unknown line ending

**Rule broken:** A connector's `start_ending` and `end_ending` are names from a fixed list. Here `end_ending` is `arrowhead`; the arrow is `arrow`.

**Stated in:** [connections.md](../../connections.md#line-endings), Line endings.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-line-ending.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Use `arrow`.
