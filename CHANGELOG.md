# Changelog

Changes to the specifications in this repository. A released format's rules never change; entries for
it record clarifications of wording only.

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
