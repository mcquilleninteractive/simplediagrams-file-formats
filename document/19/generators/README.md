# The generator catalogue

The format 19 core ([generated-shapes.md](../generated-shapes.md)) describes the generated record every
reader can read and draw, whatever drew it. This catalogue describes each **generator version** that
SimpleDiagrams knows: the shape it draws, what its record holds, and the rules a reader that knows it
holds the record to. A generator version listed here is a **known version** for a reader that
implements its entry; any other is an **unknown version**, read by the core alone.

**Names.** A generator's name is fully qualified, in reverse-DNS form
([generated-shapes.md](../generated-shapes.md#2-the-record)). The `com.simplediagrams.` namespace
belongs to this catalogue: a writer MUST NOT write a generator in it, or a version of one, that the
catalogue does not list, and writes only the setting words an entry lists. Another program MAY draw
shapes of its own generators in the complete form, named under its own domain, such as
`com.example.spiral`; they have no entry here, and every reader draws them from their saved parts. A
reader accepts what it does not know, from a later release or another program, by the core's rules.

## The entries

| Generator | Version | The shape | First drawn by | Incomplete form in format 19 | Page and schema |
| --- | --- | --- | --- | --- | --- |
| `com.simplediagrams.ring` | 1 | A ring cut into sections | SimpleDiagrams 5.2 | Yes | [page](com.simplediagrams.ring/README.md), [`1.schema.json`](com.simplediagrams.ring/1.schema.json) |
| `com.simplediagrams.flow` | 1 | A row of steps, such as chevrons | SimpleDiagrams 5.2 | Yes | [page](com.simplediagrams.flow/README.md), [`1.schema.json`](com.simplediagrams.flow/1.schema.json) |
| `com.simplediagrams.pyramid` | 1 | A pyramid or funnel cut into layers | SimpleDiagrams 5.2 | Yes | [page](com.simplediagrams.pyramid/README.md), [`1.schema.json`](com.simplediagrams.pyramid/1.schema.json) |
| `com.simplediagrams.cycle` | 1 | Steps round a circle | SimpleDiagrams 5.2 | Yes | [page](com.simplediagrams.cycle/README.md), [`1.schema.json`](com.simplediagrams.cycle/1.schema.json) |
| `com.simplediagrams.gauge` | 1 | A half dial with bands and a needle | SimpleDiagrams 5.2 | Yes | [page](com.simplediagrams.gauge/README.md), [`1.schema.json`](com.simplediagrams.gauge/1.schema.json) |

Each entry's folder is named after its generator's full name and holds its page, `README.md`, and one
schema for each version, `<version>.schema.json`.

"Incomplete form in format 19" says whether another program may write the entry's shapes without their
drawing, for the app to draw when it opens the document
([generated-shapes.md](../generated-shapes.md#12-the-incomplete-form)). That list is part of format 19
and frozen with it: an entry added later is never written in the incomplete form of format 19, and
in that form an entry's open settings take only the words listed for format 19.

## What an entry declares

Each entry gives, for its generator version:

- what the shape is, and how its parts make it up;
- its **settings**: each one's name, type, range and words, and whether its words are an open or a
  closed list ([generated-shapes.md](../generated-shapes.md#6-settings)). Every setting is required;
- its **roles**: the role of each part, in order, with how many parts of each a shape has;
- the **slot** on a part of each role, or none, and how many **points** a part of each role owns, in
  what order;
- which of the shape's own points, 0 to 7, it has;
- whether it has **own text**, and its `stacking` and `resizing`;
- its **rules**: what the settings must satisfy together (check 3 of
  [generated-shapes.md](../generated-shapes.md#10-the-three-checks));
- informatively, how the app draws it and the smallest frame it draws for.

A record of a known version MUST hold exactly what its entry declares and keep its rules; a reader that
knows the entry refuses one that does not, with "The shape … is not a valid generated shape." Saved
geometry is never compared with what the generator would draw.

## The schemas

Each entry has a JSON Schema, `<generator>/<version>.schema.json`, such as
`com.simplediagrams.ring/1.schema.json`. An element of `document.json` whose
`complex_shape` record names that generator and version, in either form, MUST pass it as well as
[`document.schema.json`](../schemas/document.schema.json). The core schema checks every generated
record alike; the entry's schema adds what the entry declares, as far as JSON Schema can. What it
cannot check, such as the order of a gauge's parts or a gauge's minimum below its maximum, is stated on
the entry's page.

## How the catalogue grows

A later SimpleDiagrams release, within document format 19, may:

- **add an entry**: a new generator, or a new version of a generator already here, with its own page
  and schema;
- **add words to an existing entry's open settings**: its page, the complete form of its schema and,
  where a new word needs one, its rules gain the word. The words its incomplete form takes stay those
  listed for format 19.

Nothing else in an entry ever changes, and the core never does. Any other change to a generator,
such as a new setting, role or closed word, is a new version and a new entry. So a released generator
version draws exactly what it drew, and a document that was valid stays valid. A reader that does not
know an entry, or a word, reads the shape as the core says for an unknown generator, version or open
word: it draws it from its saved parts and keeps its record exactly. Only the incomplete form refuses
what format 19 does not list. Each addition is recorded in the repository's CHANGELOG.
