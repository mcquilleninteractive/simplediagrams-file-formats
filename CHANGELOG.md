# Changelog

Changes to the specifications in this repository. A released format's rules never change; entries for
it record clarifications of wording only.

## Document format 19

Draft, to be published with SimpleDiagrams 5.2.

- First publication of the document format 19 specification, written by SimpleDiagrams 5.2: the
  specification, JSON Schemas for `manifest.json`, `document.json` and vector artwork assets, a
  catalogue of the built-in shapes, accepted examples and refused examples.
- New in format 19: generated shapes (rings, flows, pyramids and funnels, cycles and gauges), saved as
  finished parts, and an incomplete form that other programs may write for the app to draw on opening
  (`generated-shapes.md`); entity tables; a `proportional` container layout; a highlight colour behind
  characters, `highlight_hex`; four more note looks, with a reader drawing a look it does not know as a
  sticky note; and the manifest's optional `app_version` and `app_build`.
- A generator catalogue (`generators/`): one page and JSON Schema for each generator version, beside
  the frozen core. Later releases may add entries, and words in an entry's open settings, within
  format 19; nothing else in an entry ever changes. Each such addition will be listed here.
- Removed in format 19: the text record's `background_hex`, which a reader ignores.
- Stricter in format 19: the innermost level of a page refuses anything in its left-over `elements`
  and `connections` lists.

## Document format 18

- First publication of the document format 18 specification, written by SimpleDiagrams 5.1.0 and later
  5.1 releases: the specification, JSON Schemas for `manifest.json`, `document.json` and vector artwork
  assets, a catalogue of the built-in shapes, accepted examples and refused examples.
- Limits on how much a complex shape and a page of them hold, added before first publication: a
  container or lane at most 1,000 members and a swimlanes shape 2,000; a lane title at most 1,000
  characters and 100 runs; a table cell at most 2,000 characters and 200 runs, and a whole table
  100,000 characters and 10,000 runs; a page at most 2,000 complex shapes, 500 containers and swimlanes,
  and 1,000,000 characters in its lane titles and cells (`elements.md` §12.11), with the schemas and one
  refused example for each. SimpleDiagrams 5.1.0 and 5.1.1 open documents over these limits;
  SimpleDiagrams 5.2 refuses them.
- The text content record's `background_hex` (`elements.md` §6.1), clarified before first
  publication: SimpleDiagrams 5.2 and later read it and ignore it, draw no text background and never
  write it again, as format 19 has no such key. Other readers may ignore it too.
- A note's `note_style` (`elements.md` §11.3 and §13.2), clarified before first publication:
  SimpleDiagrams 5.2 and later read it as an open list in every format version. A word they know is
  drawn as that look, and any other word as a sticky note, rather than refusing the document as
  SimpleDiagrams 5.1 does. A format 18 writer still writes only `stickyNote` or `indexCard`. Also
  noted: SimpleDiagrams 5.2 stops an index card's rules at least 10 points above its bottom edge.
