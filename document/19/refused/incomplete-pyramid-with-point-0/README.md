# Refused: incomplete pyramid with point 0

**Rule broken:** Before it is drawn, an incomplete shape has only points its generator declares among 0 to 7: a pyramid only 2 and 3. Here a pyramid has point 0.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-pyramid-with-point-0.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove point 0.
