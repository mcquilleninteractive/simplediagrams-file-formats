# Refused: incomplete with point 8

**Rule broken:** Before it is drawn, an incomplete shape has only points its generator declares among 0 to 7. Here the element has a point 8.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The SimpleDiagrams document contains an invalid document.json file."

## In this folder

`incomplete-with-point-8.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Remove point 8; the app gives each section its point when it draws the ring.
