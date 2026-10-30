# Refused: ring without point 0

**Rule broken:** A known generator version has exactly its declared points among 0 to 7. Here a ring version 1 lacks point 0.

**Stated in:** [generators/com.simplediagrams.ring/README.md](../../generators/com.simplediagrams.ring/README.md#the-record), The record.

**Caught by the JSON Schemas:** No.

**The app's message (English):** "The shape 62026231-2F0D-56E1-A36B-24D5A9BEB89F is not a valid generated shape."

## In this folder

`ring-without-point-0.simplediagram/` is a complete document that breaks only this rule. It is stored without the empty `assets/` folder every document needs, because Git does not keep empty folders. Create an empty `assets/` folder inside it before opening it, or the document is refused for that instead.

## To make it valid

Add point 0 where the ring puts it, at the left of its circle: `{"id": 0, "x": 0, "y": 0.5}`.
