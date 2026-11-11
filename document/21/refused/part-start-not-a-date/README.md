# Refused: part start that is not a day

**Rule broken:** A part's `start` names a real day, written `YYYY-MM-DD`. Here it is `2026-02-30`, a day February does not have.

**Stated in:** [elements.md](../../elements.md#123-parts), Parts.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`part-start-not-a-date.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Set it to a real day, such as `2026-11-09`.
