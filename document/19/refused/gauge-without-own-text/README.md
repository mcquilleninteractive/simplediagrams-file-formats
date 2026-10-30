# Refused: gauge without own text

**Rule broken:** A known generator version has `own_text` exactly when it declares it: a gauge does, for its caption. Here a gauge has no `own_text`.

**Stated in:** [generators/com.simplediagrams.gauge/README.md](../../generators/com.simplediagrams.gauge/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.gauge/1.schema.json`.

**The app's message (English):** "The shape F092C13C-5A54-53CF-89D5-56669C9A6B24 is not a valid generated shape."

## In this folder

`gauge-without-own-text.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add `own_text` with its text area, below the hub.
