# Refused: cycle direction sideways

**Rule broken:** A cycle version 1's `direction` is `clockwise` or `anticlockwise`; no other word is allowed. Here `direction` is `sideways`.

**Stated in:** [generators/com.simplediagrams.cycle/README.md](../../generators/com.simplediagrams.cycle/README.md#the-record), The record.

**Caught by the JSON Schemas:** Yes: `generators/com.simplediagrams.cycle/1.schema.json`.

**The app's message (English):** "The shape F2C04E1E-C147-5923-8833-5563EE997B54 is not a valid generated shape."

## In this folder

`cycle-direction-sideways.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `direction` to `clockwise` or `anticlockwise`.
