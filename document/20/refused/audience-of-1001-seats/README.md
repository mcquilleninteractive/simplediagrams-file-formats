# Refused: audience of 1,001 seats

**Rule broken:** An audience's groups hold at most 1,000 seats in all. Here its two groups hold 1,000 and 1 seats, 1,001 in all.

**Stated in:** [complex-shapes/audience/README.md](../../complex-shapes/audience/README.md#rules), Rules.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape B523273C-04AC-5129-9692-756F97B014A7 is not a valid complex shape."

## In this folder

`audience-of-1001-seats.simplediagram/` is a complete document that breaks only this rule. It is stored without its empty `assets/` folder, because Git does not keep empty folders; a reader treats a missing empty `assets/` folder as empty.

## To make it valid

Remove the second group, or give the first 999 seats.
