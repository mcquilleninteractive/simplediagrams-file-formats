# JSON Schemas for document format 19

This folder holds three JSON Schemas (draft 2020-12) for SimpleDiagrams document format 19:

| File | Checks | `$id` |
| --- | --- | --- |
| [`manifest.schema.json`](manifest.schema.json) | `manifest.json` | `https://simplediagrams.com/schemas/document/19/manifest.schema.json` |
| [`document.schema.json`](document.schema.json) | `document.json` | `https://simplediagrams.com/schemas/document/19/document.schema.json` |
| [`vector-artwork.schema.json`](vector-artwork.schema.json) | Each `.sdvector` file in `assets/` (vector artwork formats 1 to 4) | `https://simplediagrams.com/schemas/document/19/vector-artwork.schema.json` |

Beside them, each entry of the [complex-shape catalogue](../complex-shapes/README.md) has its own
schema, `complex-shapes/<type>/<version>.schema.json`, such as
[`complex-shapes/ring/1.schema.json`](../complex-shapes/ring/1.schema.json). An element of
`document.json` whose `complex_shape` names that type and version MUST pass it as well as
`document.schema.json`. A program that validates a document validates `document.json` against
`document.schema.json`, then each such element against its entry's schema; an element of a type or
version the catalogue does not list has no entry schema, and the core schema alone applies to it. The
catalogue grows by adding entries; an existing entry's schema never changes.

The `$id` values are identifiers, not addresses: they are not promised to be served. Use the files in this
folder. The schemas are self-contained and do not refer to each other.

## What passing means

**A document that passes these schemas can still be refused by the app.** JSON Schema checks one JSON
value at a time; it cannot follow an identifier to what it names, compare two values, look inside the
package folder or count bytes. Every rule of that kind is listed [below](#what-the-schemas-cannot-check),
with the page that states it. A program that writes documents MUST check those rules itself.

The other direction holds: everything these schemas refuse is either refused by the app or goes against
what this specification tells a writer to do. The [few cases of the second kind](#where-the-schemas-are-stricter-than-the-app)
are listed below.

## What the schemas check

- **Shape.** Every record, its required keys, the type of every value and every fixed list of values
  (element kinds, endings, textures, patterns, alignments and so on).
- **Strict and lenient records.** A record is closed (`additionalProperties: false`, so an unknown key
  fails) exactly where the app refuses unknown keys: the top level of `document.json`, the outer record
  of each page and of each element, every `complex_shape` record and every record inside it but its
  texts, every attachment, and every shape-data record. Entries of `required_libraries` and `library_shape_names` are closed too: the app
  leaves out an entry with an unknown key. Every other record is open, because the app ignores unknown
  keys there. The open records therefore do **not** catch a
  misspelt optional key, such as `drawable_order` for `drawableOrder`; compare key names against the field
  tables of the specification.
- **Which keys go with which kind.** On an element, the kind decides which of `symbol_reference`,
  `embedded_asset_id`, `image_content_mode`, `path`, `unresolved_content`, `note_style`, `text_layout`,
  `shows_image_background`, `shows_image_border` and `complex_shape` are required or forbidden. On a
  `complex_shape` record, each setting's value is one of the six kinds, told apart by its form. The
  background's `kind`, an attachment's
  two forms, a shape-data field's `type` (its `unit`, choices, bounds and default value) and a
  `.sdvector` file's `schema_version` (which of compositing groups, pinned parts and outlines it has) are
  checked the same way.
- **Bounds.** Numeric ranges, list lengths, string lengths, and patterns for UUIDs, SHA-256 digests,
  colours, dates, slugs, the words of complex shapes and library identifiers.
- **Fixed values.** `format_version` is 19 and `schema_version` is 18. Older formats are out of scope.
- **Rules about one element that JSON Schema can state.** A closed path has at least three nodes; a second control point needs a first; text with no
  characters has no runs; a shape with no shape-data definition has no values.
- **Complex shapes.** The structure every complex shape shares: its closed keys, the six kinds of
  setting value, the ten part keys and their forms, words that keep the spelling rule, part paint with
  a texture only beside its colour, placed texts `top`, `middle` or `bottom`, and every list's limit.
  Each catalogue entry's schema adds what its type and version declare: the settings with their kinds,
  ranges, closed words and which are required; the roles, their counts, and the keys each role's parts
  hold; whether `members` and `own_text_layout` are allowed; no flips except on a callout; an empty own
  text where the type has none;
  no connection points listed where the type places its own; and the type's rules where JSON Schema can
  state them, such as an `automatic` or `proportional` container not turned and a lifeline's frame tall
  enough for its head and line.

## How to read the schemas

- **Byte limits.** `maxLength` counts characters (Unicode scalar values), not bytes. Where the
  specification limits a string in bytes of UTF-8, the schema sets `maxLength` to the same number, which is
  a weaker check, and the description says so. A lane title's and a table cell's limits are in
  characters, so for those `maxLength` checks exactly what the specification says.
- **Optional keys.** Optional means the key is left out. These schemas refuse `null` for an optional key.
  The one key that is required and may be `null` is `shape_data_definition_id`.
- **Explicit default values** of optional keys, such as `"preserves_aspect_ratio": false`, are accepted,
  as the app accepts them, though a writer SHOULD leave them out. Inside a complex shape, every setting is
  written, defaults included, and `hand_drawn` is written only as `false`.
- **Regular expressions** are written for ECMA-262 with Unicode semantics. Checks on control characters,
  format characters, white space and invisible characters cover the Basic Multilingual Plane only, so a
  name made only of invisible characters above U+FFFF, for example, passes the schema and is refused by the
  app.
- **Numbers.** A whole-number field has type `integer`, which JSON Schema also satisfies with `1.0`; the
  specification requires whole numbers to be written without a decimal point.
- **Lower bounds just below a round number.** A lifeline's smallest height is checked as, for example,
  59.99999994 rather than 60, because the app allows a shortfall of one part in a billion.

## Before validating

These schemas see the JSON value your parser produces, so the parser must be as strict as the
specification:

- `NaN`, `Infinity` and `-Infinity` are not JSON. Some parsers accept them, and then they pass every
  numeric bound. Refuse them while parsing.
- A key MUST NOT appear twice in one object. Many parsers keep the last value; the app keeps the first.
  Refuse repeated keys while parsing.
- Both files MUST be UTF-8 ([How JSON is read and written](../README.md#how-json-is-read-and-written)).

## What the schemas cannot check

Each of these rules is stated on the page given. A document that breaks any of them is refused by the app
unless the page says otherwise.

### The package

| Rule | Stated in |
| --- | --- |
| The package is a folder holding exactly `manifest.json`, `document.json` and `assets/` (required, even when empty), and `legacy/` holding only `source.sdxml` exactly when `contains_legacy_source` is true; nothing else, including `.DS_Store` and `._` files | [The package on disk](../README.md#the-package-on-disk) |
| Size limits: `manifest.json` 65,536 bytes; `document.json` and `legacy/source.sdxml` 104,857,600 bytes; at most 10,000 assets, each at most 16,777,216 bytes and together at most 104,857,600 bytes | [Limits](../README.md#limits) |
| Asset file names: at most 256 bytes, not starting with `.`, with no `/` or `\` | [The package on disk](../README.md#the-package-on-disk) |
| `document.json`'s `schema_version` is the manifest's `format_version` minus one (each schema checks its own file only) | [Versions](../README.md#versions) |

### Identity and references

| Rule | Stated in |
| --- | --- |
| Every UUID in the shared set (document, pages, elements, connections, groups, guides, Quick Styles) is unique, compared without regard to letter case | [Identity rules](../README.md#identity-rules) |
| Identifiers unique within their own list: part ids within their complex shape, connection point numbers within their element, definition ids within `shape_data_definitions`, field ids within their definition, option ids within their field, asset ids within the inventory | [Identity rules](../README.md#identity-rules) |
| `selected_page_id` names one of the pages | [The document](../README.md#the-document) |
| `drawableOrder` names every element and connection on its page exactly once, and nothing else | [Drawing order](../README.md#drawing-order) |
| An attachment names an element on the same page, and one of its connection points or an attach line it offers (only a lifeline offers one, line `0`) | [Attachments](../connections.md#attachments) |
| A group has members on its own page only, each object is in at most one group, groups do not form loops, and nesting is at most 64 deep | [Membership rules](../connections.md#membership-rules) |
| A container or lane holds only elements and groups on its own page, never itself; each is held at most once; a group is held only whole and only when it is not inside another group; swimlanes are never held; a container or swimlanes is never in a group; holders nest at most 4 deep, without loops | [Rules across the page](../elements.md#1210-rules-across-the-page) |
| An attachment setting, such as a callout's `tail_attachment`, names a connection point of another element on the same page, and a callout's tail tip, where it is drawn, lies within ±1,000,000,000 | [`callout`](../complex-shapes/callout/README.md#rules) |
| An element's `embedded_asset_id`, a container's `artwork` and an image background's `asset_id` name an inventory entry of a kind that suits them | [Which assets may be referenced](../assets.md#which-assets-may-be-referenced) |
| `required_libraries` names no library twice (compared without regard to case); each `library_shape_names` entry names a shape the document uses, once, and its `library_readable_id` and `slug` derive the UUIDs it claims. The app leaves a faulty entry out rather than refusing the document | [libraries.md](../libraries.md) |

### Embedded assets

| Rule | Stated in |
| --- | --- |
| `embedded_assets` lists every file in `assets/` exactly once and nothing else | [The inventory](../assets.md#the-inventory) |
| An asset's `id` is the SHA-256 of its bytes, its `filename` is that id with the extension for its kind (the schema checks the form, not that the two agree), and `byte_count` is the file's size | [The inventory](../assets.md#the-inventory) |
| PNG and JPEG content: a single frame of the stated format, 1 to 16,384 pixels a side, at most 40,000,000 pixels | [PNG and JPEG images](../assets.md#png-and-jpeg-images) |
| SVG content: the whole safety profile | [SVG artwork](../assets.md#svg-artwork) |
| `.sdvector` content: `asset_id` equals the SHA-256 of the canonical encoding; path commands after the first are in a valid order; `dash_array` has an even number of entries; at most 250,000 path commands in all | [The vector artwork format (.sdvector)](../assets.md#the-vector-artwork-format-sdvector), [Content identity: asset_id](../assets.md#content-identity-asset_id) |
| `.sdvector` compositing groups: each run ends within `layers`; listed by first layer, a longer group never after a shorter one that starts at the same layer; nested or separate, never partly overlapping; at most 16 deep; `layer_count` values adding up to at most 250,000 | [Compositing groups](../assets.md#compositing-groups) |
| `.sdvector` pinned parts: each run ends within `layers`; listed in layer order; never overlapping | [Pinned parts](../assets.md#pinned-parts) |
| `.sdvector` outlines: `layer_index` names a layer; at most one outline per layer, listed in increasing `layer_index`; the layer is not in a pinned part and has no clip paths; `box` lies inside `source_view_box`; `corner_radius` is at most half the box's shorter side; `offsets` strictly increase and none exceeds `corner_radius`; `fold` is no longer than the box's shorter side; a tab is narrower than the box and at most half its height | [Outlines](../assets.md#outlines) |

### Text

| Rule | Stated in |
| --- | --- |
| Text runs are counted in UTF-16 code units, ascending and not overlapping, end within the text, and begin and end between whole user-perceived characters | [Runs and ranges](../elements.md#63-runs-and-ranges) |
| Character runs and paragraph runs together number at most 100,000 in one text | [Runs and ranges](../elements.md#63-runs-and-ranges) |
| Limits in bytes of UTF-8: document and page names 512; text 1,000,000; font family 256; library and item identifiers 256; Quick Style name 128; the three keys of `unresolved_content` 128, 512 and 1,000,000 | [Limits](../README.md#limits) |
| Control, format and invisible characters outside the Basic Multilingual Plane, in names, labels and font families | [elements.md](../elements.md), [shape-data.md](../shape-data.md), [Quick Styles](../README.md#quick-styles) |

### Sizes measured against other values

| Rule | Stated in |
| --- | --- |
| A text region touches its shape: `x` is at least minus its `width`, and `y` at least minus its `height` | [Text layout](../elements.md#7-text-layout) |
| A table's rows each have one cell for each column, at most 2,000 cells in all; a table's and an entity table's columns are at least 16 points wide and rows at least 12 points tall once laid out in the frame | [`table`](../complex-shapes/table/README.md#rules), [`entity_table`](../complex-shapes/entity_table/README.md#rules) |
| Swimlanes' lanes are at least 28 points thick once laid out, and the frame leaves room for the title strip | [`swimlanes`](../complex-shapes/swimlanes/README.md#rules) |
| A physical output layout, converted to points, has each side from 1 to 14,400 points and more than twice the margin (the schema checks each value's own range only: exactly for `points`, loosely for inches and millimetres, not at all for pixels; it never compares a side with the margin) | [Physical output layout](../README.md#physical-output-layout) |
| A shape-data number lies within its field's bounds, and the bounds allow at least one number | [shape-data.md](../shape-data.md) |

### Complex shapes

| Rule | Stated in |
| --- | --- |
| Part identifiers are unique among the shape's parts, without regard to letter case | [Parts](../elements.md#123-parts) |
| No connection point number is listed by two parts, or twice by one | [Parts](../elements.md#123-parts) |
| A placed text's region, turned as it is, lies inside the element's frame | [Parts](../elements.md#123-parts) |
| Words are measured in bytes; the schemas check characters | [Words](../elements.md#124-words) |
| A known type's parts come in the order of its roles, such as a gauge's bands before its needle | [Parts](../elements.md#123-parts), each type's entry |
| A gauge's minimum is below its maximum and its value between them | [`gauge`](../complex-shapes/gauge/README.md#rules) |
| An embedded-asset setting names an asset the document holds, of the kind its entry requires | [Settings](../elements.md#122-settings) |

### Totals across the document

| Rule | Stated in |
| --- | --- |
| At most 100,000 elements, 200,000 connections, 100,000 groups and 200,000 direct group members | [Limits](../README.md#limits) |
| At most 1,000,000 path nodes and connector points together | [Limits](../README.md#limits) |
| At most 100,000 part texts and cells together; one complex shape's own text and part texts together, except a table's, at most 1,000,000 bytes and 100,000 runs | [Rules across the page](../elements.md#1210-rules-across-the-page) |
| All the lanes of one swimlanes shape hold at most 2,000 members together (each lane's 1,000 is checked) | [How much a shape and a page hold](../elements.md#1211-how-much-a-shape-and-a-page-hold) |
| A lane title's runs (at most 100) and a cell's (at most 200) count character runs and paragraph runs together (the schema checks each list on its own) | [How much a shape and a page hold](../elements.md#1211-how-much-a-shape-and-a-page-hold) |
| All the cells of one table hold at most 100,000 characters and 10,000 runs | [How much a shape and a page hold](../elements.md#1211-how-much-a-shape-and-a-page-hold) |
| One page holds at most 2,000 complex shapes, 500 containers and swimlanes together, and 1,000,000 characters in its part texts and cells together | [How much a shape and a page hold](../elements.md#1211-how-much-a-shape-and-a-page-hold) |
| Shape data: every definition used by at least one element; each element's values cover every field of its definition exactly once; each value suits its field; a `choiceID` names one of the field's options; at most 100,000 fields across all definitions and 100,000 values across the document; at most 16 MiB of shape-data text | [shape-data.md](../shape-data.md) |

## Where the schemas are stricter than the app

In these cases the app opens a document that the schemas refuse. Each is a rule for writers.

| The schemas refuse | The app | The rule for writers |
| --- | --- | --- |
| `null` for an optional key | Reads `null` as absent for most optional keys (the exceptions, which it refuses, are in shape-data records: [How shape-data records are read](../shape-data.md#how-shape-data-records-are-read)) | Leave optional keys out ([Required keys, optional keys and `null`](../README.md#required-keys-optional-keys-and-null)) |
| A `note_style` other than the six looks | Draws the note as a sticky note | Write one of the six words ([`note`](../elements.md#113-note)) |
| A faulty entry in `required_libraries` or `library_shape_names` | Leaves the entry out and opens the document | Write every entry correctly ([libraries.md](../libraries.md)) |
| A `marker_tip` other than `bullet`, `chisel` or `brush` | Draws the stroke as an ordinary line | Use one of the three values ([`path`](../elements.md#119-path)) |
