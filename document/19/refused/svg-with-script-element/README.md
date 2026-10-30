# Refused: svg with script element

**Rule broken:** SVG artwork must pass the safety profile, which refuses active content such as a `script` element even when it is empty. This file holds an empty `<script/>` element and nothing that runs.

**Stated in:** [assets.md](../../assets.md), SVG artwork.

**Caught by the JSON Schemas:** No. JSON Schema does not read asset files.

**The app's message (English):** "The SimpleDiagrams document contains an invalid assets file."

## In this folder

`svg-with-script-element.simplediagram/` is a complete document that breaks only this rule.

## To make it valid

Remove the `script` element. The file name and inventory entry change with the bytes.
