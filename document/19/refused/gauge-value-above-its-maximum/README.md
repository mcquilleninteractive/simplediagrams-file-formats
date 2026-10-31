# Refused: gauge value above its maximum

**Rule broken:** A gauge's `minimum` is below its `maximum`, and its `value` lies from the minimum to the maximum. Here `value` is 101 and `maximum` is 100.

**Stated in:** [complex-shapes/gauge/README.md](../../complex-shapes/gauge/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 80FECA04-02BA-5931-9869-3F32F7A52B22 is not a valid complex shape."

## In this folder

`gauge-value-above-its-maximum.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Set `value` to a number from 0 to 100.
