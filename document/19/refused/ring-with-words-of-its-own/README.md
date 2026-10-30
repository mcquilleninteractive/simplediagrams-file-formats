# Refused: ring with words of its own

**Rule broken:** A known generator version that declares no own text keeps the element's `text_content.string` empty. Here a ring version 1 has words of its own.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.ring/1.schema.json`.

**The app's message (English):** "The shape 2ACC1C70-3E40-5C42-85A8-35F6005427B9 is not a valid generated shape."

## In this folder

`ring-with-words-of-its-own.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Empty the element's text. A ring's words go in its sections' labels.
