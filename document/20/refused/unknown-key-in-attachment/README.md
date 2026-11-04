# Refused: unknown key in attachment

**Rule broken:** A connector attachment has exactly `element_id` and either `connection_point_id`, or `attach_line_id` with `distance`. Here the start attachment also has `side`.

**Stated in:** [connections.md](../../connections.md#attachments), Attachments.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`unknown-key-in-attachment.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `side`. Where the end sits follows from the connection point.
