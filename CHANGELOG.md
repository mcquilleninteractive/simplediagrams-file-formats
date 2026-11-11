# Changelog

Changes to the specifications in this repository. A released format's rules never change; entries for
it record clarifications of wording only.

## Document format 21

Draft, to be published with SimpleDiagrams 5.4.

- First draft of the document format 21 specification, written by SimpleDiagrams 5.4: format 20's
  pages, schemas, catalogue, examples and refused examples, changed only where format 21 differs.
- New in format 21: a paragraph run may hold `list`, a `kind` (`bullet` or `number`) and a `level` from
  1 to 3, which makes its paragraphs the items of a list, in any text: an element's, a connector's
  labels, a lane's title, a table cell or a complex shape's part (`elements.md`, Lists). Numbers are
  not stored; they follow the order of the items.
- New in format 21: a note may hold `collapsed`, set to `true`, which shows it as a small icon of its
  look while it keeps its expanded frame (`elements.md`, Note). Only a note may be collapsed.
- A document claiming format 20 or lower that holds a `list` or `collapsed` set to `true` is refused.
  One accepted example (`lists-and-collapsed-notes`) and three refused examples (`list-at-level-4`,
  `list-of-an-unknown-kind` and `collapsed-rectangle`) show them.
- `note_style` gains three looks, `documentation`, `textAnnotation` and `umlNote`. The list of looks
  is open in every format, so this needs no change of a reader: one that does not know a look draws it
  as a sticky note.
- In the complex-shape catalogue, a `table` gains the setting `uml_box`, which kind of UML box it
  stands for (`none`, `class`, `interface` or `enumeration`), written by every table in format 21, and
  a `ring` holds 1 to 20 sections rather than 1 to 12. A format 20 document holding either is refused.
  The `ring-of-13-sections` refused example becomes `ring-of-21-sections`, and three refused examples
  (`table-with-an-unknown-uml-box`, `table-without-uml-box` and `entity-table-with-uml-box`) show
  `uml_box`.
- New in format 21: a complex shape's part may hold two more keys, `start`, the day it begins, written
  `YYYY-MM-DD`, and `after`, 1 to 8 other parts of the same shape it follows, named by `id`
  (`elements.md`, Parts). The structure's closed set of part keys grows from ten to twelve, once,
  with format 21 itself. Two refused examples (`part-start-not-a-date` and
  `part-after-naming-no-part`) show the structure's rules.
- New in format 21: the complex-shape catalogue gains a type, the `gantt`, first drawn by
  SimpleDiagrams 5.4 (`complex-shapes/gantt/`): a time chart of 1 to 60 tasks in 1 to 20 sections,
  each task starting on a day or when another ends, lasting 1 to 3,650 days or marked a milestone,
  with an optional count of weekdays only (`skip_weekends`). A document claiming format 20 or lower
  that holds one is refused. One accepted example (`gantt-charts`) and fourteen refused examples, one
  for each of its limits and rules, show it. The pages that list the catalogue's types, the shapes
  that place their own points and the shapes that never keep their proportions now name it.
- Every document SimpleDiagrams 5.4 saves is format 21, so it needs 5.4 or later to open.

## Document format 20

Published with SimpleDiagrams 5.3.

- The document format 20 specification, written by SimpleDiagrams 5.3: format 19's
  pages, schemas, catalogue, examples and refused examples, changed only where format 20 differs.
- New in format 20: a connector may carry a label beside each end, `start_label` and `end_label` on
  the connection's outer record, each placed by its `distance` from that end and its `normal_offset`
  to one side, its text at most 200 scalars (`connections.md`, Labels beside the ends). A document
  claiming format 19 or lower that holds either key is refused. One accepted example
  (`connector-end-labels`) and one refused example (`end-label-at-a-negative-distance`) show them.
- New in format 20: a `ring` section may hold a `size`, its share of the circle as a percentage, a
  whole number from 1 to 100, so a ring is a true pie or donut chart (`complex-shapes/ring/`). A ring's
  shares add up to at most 100, and under 100 the rest of the circle is left empty. Sections without
  one share equally what the others leave, so a ring that holds none draws its equal sections as in
  format 19, gaps included; once any section holds one, neighbouring slices touch.
- New in format 20: the complex-shape catalogue gains a type, the `audience`, first drawn by
  SimpleDiagrams 5.3 (`complex-shapes/audience/`): a half-circle arch of seats drawn as dots, divided
  into 1 to 20 groups, each a whole number of seats and an optional paint of its own, with the total
  drawn in the middle or hidden (`show_total`). Its groups hold at most 1,000 seats in all. A document
  claiming format 19 or lower that holds an audience is refused. One accepted example (`audiences`)
  and three refused examples (`audience-of-21-groups`, `audience-group-size-not-whole` and
  `audience-of-1001-seats`) show it. The pages that list the catalogue's types, the shapes that place
  their own points and the shapes that never keep their proportions now name it.
- Every document SimpleDiagrams 5.3 saves is format 20, so it needs 5.3 or later to open.
- A complex shape's `version` is kept as earlier documents wrote it, 1 for every type, and is no longer
  raised: what a type holds changes only with a new document format version, and the pages and the
  catalogue's schemas now say so.
- The built-in shape catalogue lists every shape 5.3 bundles, including Biz's Trophy. Format 19's
  catalogue stays as 5.2 published it.
- Clarification, 10 November 2026: `elements.md` now records that SimpleDiagrams 5.4 and later refuse
  a format 20 document whose paragraph run holds `list`, or whose note holds `collapsed` set to `true`,
  which format 21 brought in, instead of ignoring them as unknown keys. A writer never writes them in
  format 20, so no document written to this specification is affected.

## Document format 19

Published with SimpleDiagrams 5.2.

- First publication of the document format 19 specification, written by SimpleDiagrams 5.2: the
  specification, JSON Schemas for `manifest.json`, `document.json` and vector artwork assets, a
  catalogue of the built-in shapes, accepted examples and refused examples.
- New in format 19: one structure for every complex shape (`elements.md` §12), in which a shape saves
  only its type, version, settings, parts and members, and the app draws it from them; a complex-shape
  catalogue (`complex-shapes/`) with one page and JSON Schema for each type and version; the entity
  table as a type of its own; rings, flows, pyramids and funnels, cycles and gauges; a `proportional`
  container layout; a highlight colour behind characters, `highlight_hex`; four more note looks, with a
  reader drawing a look it does not know as a sticky note; and the manifest's optional `app_version`
  and `app_build`.
- Containers, swimlanes, tables, callouts and lifelines, which format 18 saved with records of their
  own, are saved in format 19 in the one complex-shape structure, every setting written.
- The complex-shape catalogue may gain entries, new types and new versions, within format 19; an entry
  never changes once published, and its words are a closed list. A reader that does not know an entry
  opens the document and shows that shape as a placeholder. Each addition will be listed here.
- Clarification, 4 November 2026: `connections.md` now records that SimpleDiagrams 5.3 and later
  refuse a format 19 document whose connection holds `start_label` or `end_label`, the labels beside a
  connector's ends that format 20 brought in, instead of ignoring them as unknown keys. A writer never
  writes them in format 19, so no document written to this specification is affected.
- Clarification of wording, November 2026: "picture" now reads "bitmap image" throughout, because it could
  be taken to mean other artwork, and a task's pinned part is described as a small icon. What the format
  allows and refuses is unchanged.
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
- The message for a complex shape that breaks a rule across the page (`connections.md`, and the refused
  cases `container-in-group`, `shape-in-two-containers` and `table-cell-count-mismatch`), clarified
  before first publication: SimpleDiagrams 5.2 and later report "The shape *id* is not a valid complex
  shape."; SimpleDiagrams 5.1 reports "Shape *id* holds a container, swimlanes, table, callout or
  lifeline record that is not valid."
- A package without its `assets/` folder (`README.md`, the package on disk, and the refused index's
  described-only faults), clarified before first publication: SimpleDiagrams 5.2 and later open a
  package that lacks only its empty `assets/` folder, reading it as empty, and refuse one whose
  inventory lists assets with "The SimpleDiagrams document contains an invalid assets file.";
  SimpleDiagrams 5.1 refuses any package without `assets/`. A writer still writes the folder.
