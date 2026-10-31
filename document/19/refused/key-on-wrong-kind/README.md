# Refused: key on wrong kind

**Rule broken:** Kind-specific keys belong only to their kind: `symbol_reference` only to `symbol`. Here a `rectangle` carries one.

**Stated in:** [elements.md](../../elements.md#10-which-kind-may-carry-which-key), Which kind may carry which key.

**Caught by the JSON Schemas:** Yes: `document.schema.json` ties these keys to `kind`.

**The app's message (English):** "The diagram contains invalid text in unexpected symbol reference."

## In this folder

`key-on-wrong-kind.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `symbol_reference`, or change `kind` to `symbol` to place the library shape it names.
