# Refused: gauge needle before its bands

**Rule broken:** A known generator version lists its parts' roles in their declared order: a gauge's bands, then its needle. Here the needle comes first.

**Stated in:** [generators/com.simplediagrams.gauge/README.md](../../generators/com.simplediagrams.gauge/README.md#the-record), The record.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape D6EA4981-18A3-5DB9-B7A5-F43AB8BB5E0A is not a valid generated shape."

## In this folder

`gauge-needle-before-its-bands.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Move the needle to the end of `parts`.
