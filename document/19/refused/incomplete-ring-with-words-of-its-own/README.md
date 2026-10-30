# Refused: incomplete ring with words of its own

**Rule broken:** A ring written in the incomplete form, like any ring version 1, keeps the element's `text_content.string` empty. Here the element's text is `Centre`.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape 94E1855F-85DA-57B1-B8DF-4073D3F85531 is not a valid generated shape."

## In this folder

`incomplete-ring-with-words-of-its-own.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Empty the element's text. A ring's words go in its sections' labels.
