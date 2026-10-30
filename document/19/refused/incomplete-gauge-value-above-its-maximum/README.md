# Refused: incomplete gauge value above its maximum

**Rule broken:** A gauge written in the incomplete form keeps the gauge's rules: its `value` lies from its `minimum` to its `maximum`. Here `value` is 101 and `maximum` is 100.

**Stated in:** [generated-shapes.md](../../generated-shapes.md#12-the-incomplete-form), The incomplete form.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape BD5B3C7D-2C09-572D-A0D3-A43AADA33091 is not a valid generated shape."

## In this folder

`incomplete-gauge-value-above-its-maximum.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `value` to a number from the minimum to the maximum.
