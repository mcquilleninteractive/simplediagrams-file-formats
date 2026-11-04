# Refused: paint key in part

**Rule broken:** A part holds only the ten part keys: `id`, `role`, `text`, `text_layout`, `style`, `size`, `members`, `cells`, `marks` and `point_ids`. Here the section has `"paint": "band"`.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`paint-key-in-part.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove `paint`. A part's own colours go in its `style`.
