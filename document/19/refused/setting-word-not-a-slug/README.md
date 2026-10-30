# Refused: setting word not a slug

**Rule broken:** A setting's word follows the slug rule: lower-case letters and digits in groups joined by single hyphens, at most 32 bytes. Here a setting holds `Soft Edge`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#6-settings), Settings.

**Caught by the JSON Schemas:** Yes: `document.schema.json`.

**The app's message (English):** "The shape 1CB8FEA4-862F-5D1E-A940-161F618E01B4 is not a valid generated shape."

## In this folder

`setting-word-not-a-slug.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Write the word as a slug: `soft`.
