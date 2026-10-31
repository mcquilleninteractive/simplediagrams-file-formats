# SimpleDiagrams document format 19

> **Draft.** Document format 19 is the format SimpleDiagrams 5.2 writes. Until SimpleDiagrams 5.2 is
> released and this repository is tagged `document-19`, this specification is a draft and may still
> change. From that tag on, its rules are final.

A SimpleDiagrams document is a file whose name ends in `.simplediagram`. It holds one or more pages of
shapes, connectors, groups and text, together with any artwork and images those pages use. This
specification describes document format 19, the current format, closely enough that a program can
read such a document, or write one that SimpleDiagrams for macOS (in this specification, **the app**)
opens. It is a draft until SimpleDiagrams 5.2 is released.

This page is the entry point. It covers the conventions every page relies on, the package on disk, the
manifest, the document and its pages, identity rules, every limit, versions, and a checklist for
writing a document from a program. The other pages describe the records that sit inside a page.

## Contents

| Page | What it covers |
| --- | --- |
| [README.md](README.md) (this page) | Conventions, the package on disk, the manifest, the document and its pages, identity rules, every limit, versions, writing a document |
| [elements.md](elements.md) | Every element kind, style, text, text layout, connection points, and the one structure every complex shape is saved in |
| [complex-shapes/](complex-shapes/README.md) | The complex-shape catalogue: one page and schema for each type and version, such as `ring` version 1 |
| [connections.md](connections.md) | Connectors, how their ends attach, routing, labels; groups |
| [assets.md](assets.md) | Embedded assets, the asset inventory, SVG and raster rules, the vector artwork format (`.sdvector`) |
| [shape-data.md](shape-data.md) | Shape data: definitions, fields and values |
| [libraries.md](libraries.md) | Library shapes placed by reference, required libraries, library shape names |
| [built-in-shapes.json](built-in-shapes.json) | The built-in shapes a document can place by reference ([libraries.md](libraries.md#placing-a-built-in-shape)) |
| [schemas/](schemas/) | JSON Schemas for `manifest.json`, `document.json` and `.sdvector` assets |
| [examples/](examples/README.md) | Complete documents the app opens |
| [refused/](refused/README.md) | Documents the app refuses, each breaking one named rule |

On this page:

- [Conformance](#conformance)
- [Messages quoted in this specification](#messages-quoted-in-this-specification)
- [Terms](#terms)
- [How JSON is read and written](#how-json-is-read-and-written)
- [The package on disk](#the-package-on-disk)
- [The manifest](#the-manifest)
- [The document](#the-document)
- [Pages](#pages)
- [Grid](#grid)
- [Background](#background)
- [Export frame, physical output layout and background reference frame](#export-frame-physical-output-layout-and-background-reference-frame)
- [Drawing order](#drawing-order)
- [Guides](#guides)
- [Quick Styles](#quick-styles)
- [Identity rules](#identity-rules)
- [Limits](#limits)
- [Versions](#versions)
- [Writing a document from a program](#writing-a-document-from-a-program)
- [Open points](#open-points)

## Conformance

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT",
"RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this specification are to be interpreted as
described in BCP 14 (RFC 2119 and RFC 8174) when, and only when, they appear in all capitals, as shown
here.

A **writer** is any program that creates or changes a `.simplediagram` document. A **reader** is any
program that reads one. A document **conforms** to document format 19 when it meets every MUST and MUST
NOT addressed to writers on every page of this folder. The app opens every conforming document.

Where this specification says what the app does with input that does not conform, it describes the
app's behaviour so that a writer can understand a refusal. Another reader MAY be more lenient, but a
writer MUST NOT rely on any leniency this specification does not promise.

Sections marked **Informative** explain behaviour of the app that a writer does not need in order to
produce a valid document. Everything else is normative.

## Messages quoted in this specification

When the app refuses a document it shows a message. This specification quotes those messages in
English, as examples, so that a person who meets one can find the rule behind it. The app shows them
translated into the Mac's language where a translation exists, and the field names inside them are
translated too.

Programs MUST NOT match on the text of a message. A reader of its own need not produce the same
messages, or any message at all, and MAY accept documents the app refuses.

## Terms

| Term | Meaning |
| --- | --- |
| **Document package** | A folder that macOS Finder shows as one file. A `.simplediagram` document is a document package, not a zip archive. "Package" in this specification always means that folder, never a version number |
| **Document format version** | The whole number in `manifest.json` under `format_version`. This specification describes document format 19 |
| **Document schema version** | The whole number in `document.json` under `schema_version`. It is always one less than the document format version: 18 for document format 19 |
| **Vector artwork format version** | The `schema_version` inside a `.sdvector` asset, from 1 to 4 ([assets.md](assets.md)). It is independent of the other two |
| **Element** | One shape on a page: a rectangle, a library shape, an image, a freehand line and so on ([elements.md](elements.md)) |
| **Connection** (connector) | A line whose ends can be attached to elements. It is stored apart from elements ([connections.md](connections.md)) |
| **Group** | A set of elements, connections and smaller groups that move together ([connections.md](connections.md)) |
| **Diagram point** | The unit of every position and size. On printed or exported output one diagram point is 1/72 inch |
| **Page coordinates** | Positions on a page, in diagram points. The origin is the top-left; **x grows to the right and y grows downwards**. The page is unbounded: coordinates may be negative, and nothing is clipped to the page's stored width and height |
| **Rectangle** | A JSON object with four required numbers: `x`, `y` (the top-left corner), `width`, `height` |
| **Point** | A JSON object with two required numbers: `x`, `y` |
| **Frame** | The rectangle an element occupies before rotation |
| **Fraction of the frame** | A number where 0 is the frame's left or top edge and 1 its right or bottom edge. Connection points and text regions are stored this way so that they keep their place when a shape is resized |
| **Degrees** | Angles are in degrees, **clockwise** as seen on screen (because y grows downwards). An element turns about the centre of its frame |
| **Colour** | A string `#RRGGBB` or `#RRGGBBAA` (alpha last), hexadecimal digits in either letter case. Fully transparent is `#00000000`. Three-digit colours, colour names and `none` are not colours |
| **UUID** | An identifier written as 32 hexadecimal digits in the groups 8-4-4-4-12, for example `6F1D2C3A-8B4E-4A7D-9C21-5E0F3A1B2C4D`. Either letter case reads; see [Identity rules](#identity-rules) |
| **Bytes** | When a limit is given in bytes, it counts the bytes of the value's UTF-8 encoding |
| **Scalars** | Unicode scalar values. For most text this is close to the number of characters |

## How JSON is read and written

`manifest.json` and `document.json` are JSON texts as defined by RFC 8259. These rules apply to both,
and to every record on every page of this folder.

### Encoding, white space and key order

- A writer MUST encode both files as UTF-8 and SHOULD NOT write a byte-order mark.
- Key order and white space carry no meaning. A writer MAY indent its output.
- **Informative.** The app writes compact JSON, with keys sorted alphabetically and `/` not escaped, so
  that saving an unchanged document produces the same bytes. It also happens to read UTF-8 with a
  byte-order mark, and UTF-16 with one; that is not promised.

### Required keys, optional keys and `null`

- A key marked **required** MUST be present.
- A key marked **optional** MAY be left out. A writer MUST leave out an optional key it has no value
  for, and MUST NOT write it as `null`. The schemas refuse `null` for optional keys.
- A reader SHOULD treat `null` for an optional key as if the key were absent: the app reads most of
  them that way, and never writes `null` itself. The exceptions, where the app refuses `null`, are in
  shape-data records, listed in [shape-data.md](shape-data.md#how-shape-data-records-are-read), and
  everywhere in a complex shape's record ([elements.md](elements.md#121-the-structure)).
- One key is required and may be `null`: `shape_data_definition_id`, on every element
  ([elements.md](elements.md)). It MUST be present.
- A required list MUST be present and MUST NOT be `null`, even when it is empty. Write `[]`.

### Numbers

- Every number MUST be finite. (JSON has no `NaN` or `Infinity`; a number too large for a 64-bit
  floating-point value is refused.)
- A field this specification calls a **whole number** (for example `format_version`,
  `schema_version`, `revision`, `z_index`, connection point numbers, text ranges) MUST be written as a
  JSON integer, with no decimal point, fraction or exponent. The app refuses a fraction such as `1.5`
  in a whole-number field. It currently reads `1.0` as `1`, but a writer MUST NOT rely on that.
- Every other number MAY have a fraction.

### Repeated keys

A writer MUST NOT write the same key twice in one object. (The app currently uses the first value of a
repeated key; another reader may use the last, or refuse the document.)

### Key spelling

Most keys are `snake_case`. A few are `camelCase`, for historical reasons, and MUST be written exactly
as given:

- on a page: `physicalOutputLayout` (and `marginPoints` inside it), `backgroundReferenceFrame`,
  `drawableOrder` ([Pages](#pages));
- on an `unresolved` element: the keys inside `unresolved_content` ([elements.md](elements.md));
- inside every shape-data record ([shape-data.md](shape-data.md)).

Each field table in this folder gives every key exactly as it appears on disk. A key spelt differently
is a different key: `drawable_order` is not `drawableOrder`.

### Unknown keys

A writer MUST NOT write a key this specification does not define for that record.

What the app does with an unknown key depends on the record. Some records are **strict**: an unknown key
makes the app refuse the whole document. The rest are **lenient**: the app ignores the key, and the
value is lost the next time the app saves.

| Record | An unknown key |
| --- | --- |
| The top level of `document.json` | Refuses the document |
| A page's outer record ([Pages](#pages)) | Refuses the document |
| An element's outer record ([elements.md](elements.md)) | Refuses the document |
| A `complex_shape` record and every record inside it but its texts ([elements.md](elements.md#121-the-structure)) | Refuses the document |
| A connector end's attachment, and a callout's tail attachment ([connections.md](connections.md), [elements.md](elements.md)) | Refuses the document |
| Every shape-data record ([shape-data.md](shape-data.md)) | Refuses the document |
| An entry of `required_libraries` or `library_shape_names` ([libraries.md](libraries.md)) | Drops that one entry; the rest of the document opens |
| Inside a `.sdvector` asset ([assets.md](assets.md)) | Ignored; the asset's bytes are kept unchanged |
| Every other record: the manifest, page layers A to C, the grid and its appearance, the background, the physical output layout, drawing-order entries, guides, Quick Styles, element layers 1 to 3, styles, text, text layouts (except inside a complex shape, where they are strict), paths, connection points, connections, groups and the asset inventory | Ignored, and lost at the next save |

A text record's `background_hex`, which document format 19 does not have, is one such ignored key: an
earlier SimpleDiagrams wrote it, and a reader that meets it ignores it
([elements.md](elements.md#61-the-text-content-record)).

Because a misspelt optional key in a lenient record is ignored rather than refused, a writer SHOULD
check its key names against the field tables in this folder. The schemas in [schemas/](schemas/) are
open wherever the app is lenient, so they do not catch such a misspelling either. A misspelt *required*
key is always refused, because the real key is then missing.

### How the app reports a fault

**Informative.** The app reports two broad sorts of fault differently:

- A fault in the **form** of `document.json` (not JSON, a required key missing, a value of the wrong
  type, an unknown value in a fixed list such as an element `kind`, an unknown key in a strict record)
  gives one message that does not say where: "The SimpleDiagrams document contains an invalid
  document.json file."
- A fault in the **meaning** of a well-formed document (a repeated identifier, a colour that is not a
  colour, a connector attached to a shape that is not there) gives a specific message, such as "The
  diagram repeats identifier …" or "The diagram contains an invalid number in grid spacing."
- A fault in the meaning of a complex shape gives that shape's own message, naming it: "The shape … is
  not a valid complex shape." A fault in the structure every complex shape shares, such as an unknown
  key or a word that breaks the spelling rule, is a fault of form and gives the general message
  ([elements.md](elements.md#129-which-message-a-fault-gives)).

Some limits are enforced while the file is read, so exceeding them gives the general message rather
than a specific one. The [Limits](#limits) table marks them.

## The package on disk

A document is a folder whose name ends in `.simplediagram`, holding two JSON files and an `assets`
folder:

```text
Order flow.simplediagram/        the document package
  manifest.json                  required: what kind of file this is, and its version
  document.json                  required: the whole diagram
  assets/                        required, even when empty
    <sha256>.sdvector            native vector artwork
    <sha256>.svg                 SVG artwork
    <sha256>.png                 a PNG image
    <sha256>.jpg                 a JPEG image
  legacy/                        only in a document converted from an older .sdxml file
    source.sdxml                 the original file, kept unchanged
```

- The file name extension is `.simplediagram`. On macOS the type identifier is
  `com.simplediagrams.document`, declared as a package, so Finder shows the folder as one document.
- There is no thumbnail, preview or metadata file. A writer MUST NOT add one.

### Members

| Member | Required | Size limit | Purpose |
| --- | --- | --- | --- |
| `manifest.json` | Yes | 64 KiB (65,536 bytes) | Names the format and its version, so a reader can decide how to read the rest before reading it ([The manifest](#the-manifest)) |
| `document.json` | Yes | 100 MiB (104,857,600 bytes) | Every page, element, connection and group, and the inventory of `assets/` ([The document](#the-document)) |
| `assets/` | Yes, as a folder, even when empty | 10,000 files; 16 MiB (16,777,216 bytes) each; 100 MiB in total | The bytes of embedded artwork and images. Each file is named after the SHA-256 digest of its own contents and listed in the document's inventory ([assets.md](assets.md)) |
| `legacy/` | Only when the manifest says so | One file, `source.sdxml`, at most 100 MiB | The original file of a document the app converted from its older `.sdxml` format. A writer creating a new document MUST NOT create it |

### Package rules

A writer MUST produce a package that meets all of these:

1. The package is a folder, not a symbolic link, and not a zip or other archive.
2. It contains `manifest.json` and `document.json` as regular files and `assets` as a folder.
3. It contains nothing else, except a `legacy` folder when the manifest's `contains_legacy_source` is
   `true`. In particular it MUST NOT contain `.DS_Store`, AppleDouble `._` files, `.gitkeep` or any other
   file, at the top level or in `assets/`.
4. No member is a symbolic link, and each member is the right kind of thing (a file where a file is
   expected, a folder where a folder is expected).
5. Each file is within its size limit.
6. `assets/` contains exactly the files listed in the document's `embedded_assets` inventory, no more
   and no fewer, each a regular file whose name does not start with `.`, contains no `/` or `\`, and is
   at most 256 bytes long ([assets.md](assets.md)).
7. `legacy/`, when present, contains exactly one file, `source.sdxml`, and the manifest's
   `contains_legacy_source` is `true`. When `contains_legacy_source` is `true`, `legacy/source.sdxml`
   exists.

A document is treated as untrusted input, so the exact inventory is enforced: it prevents content that
would be ignored, ambiguous or substituted.

**Informative: how good documents get broken.** Four everyday things add or remove a member and make a
document unreadable:

- Finder writes `.DS_Store` inside the package after someone uses **Show Package Contents**.
- Git and some archive tools drop empty folders, removing an empty `assets/`. (This is why the
  [examples](examples/README.md) in this repository are stored without an empty `assets/` folder; add
  one before opening them.)
- A file-sync tool adds its own bookkeeping file.
- Copying the package to a volume without extended-attribute support (FAT, exFAT, some network
  shares) makes macOS add a `._name` file beside each member.

### What the app refuses at the package level

| Situation | Example message |
| --- | --- |
| The path is not a folder, is a symbolic link, cannot be listed, or holds more than four members | "The SimpleDiagrams document is not a valid document package." |
| A member with any other name is present, such as `.DS_Store` or `._document.json` | "The SimpleDiagrams document contains an invalid *name* file." |
| `manifest.json`, `document.json` or `assets` is missing | "The SimpleDiagrams document is missing *name*." |
| A member is the wrong kind of thing, is a link, or cannot be read | "The SimpleDiagrams document contains an invalid *name* file." |
| `manifest.json`, `document.json` or `legacy/source.sdxml` is over its size limit | "The SimpleDiagrams document contains an invalid manifest.json file." (or the file concerned) |
| One asset over 16 MiB, or an entry in `assets/` that is a folder or a link | "The SimpleDiagrams document contains an invalid assets/*file name* file." |
| All assets together over 100 MiB | "The assets file is too large (*n* bytes)." |
| A file in `assets/` whose name starts with `.`, contains `/` or `\`, or is longer than 256 bytes | "The SimpleDiagrams document contains an invalid assets file." |
| `assets/` and the inventory disagree | "The SimpleDiagrams document contains an invalid assets file." |
| `legacy/` holds anything other than exactly `source.sdxml` | "The SimpleDiagrams document contains an invalid legacy file." |
| `legacy/` exists but the manifest says there is no legacy source | "The SimpleDiagrams document contains an invalid manifest.json file." |
| The manifest says there is a legacy source but `legacy/source.sdxml` is missing | "The SimpleDiagrams document is missing legacy/source.sdxml." |

The app first checks that the path is a folder it can open. Then, before any other check in this table,
it reads `format` and `format_version` from `manifest.json` (when that is a readable file within its size
limit) and refuses a newer document with a request to update ([Newer documents](#newer-documents)).

## The manifest

`manifest.json` is one JSON object with three required keys and two optional ones. It exists so that a
reader can check the format and version before parsing a document that may be large.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `format` | string | Yes | Exactly `com.simplediagrams.document` | Says this is a SimpleDiagrams document |
| `format_version` | whole number | Yes | 19 for this specification | The document format version |
| `contains_legacy_source` | boolean | Yes | `true` if and only if `legacy/source.sdxml` exists | Whether the package keeps the original file of a document converted from `.sdxml`. A new document has `false` |
| `app_version` | string | No | 1 to 32 bytes of UTF-8 | The version of the program that saved the document, such as `5.2.0` |
| `app_build` | string | No | 1 to 32 bytes of UTF-8 | The build of the program that saved the document, such as `45` |

**`app_version` and `app_build`** record which program saved the document, so that a report of a
document that looks different in two releases can be explained. They are for people only:

- SimpleDiagrams writes both every time it saves, naming its own version and build. A document saved
  again by another release of the app therefore differs in these two lines even when nothing else
  changed.
- Another writer MAY leave them out. If it writes them, they name that program's own version and
  build, never a SimpleDiagrams version. They hold nothing else: no edition, machine,
  operating system or user.
- A reader MUST NOT decide anything by them. **Informative.** The app never reads them: a missing,
  malformed or over-long value is ignored, as any unknown key in the manifest is.

The whole file is at most 64 KiB. The manifest is a lenient record: the app ignores unknown keys in it,
but a writer MUST NOT write any other key. A manifest that is not valid JSON, or lacks one of the three
required keys, gives "The SimpleDiagrams document contains an invalid manifest.json file." A `format`
other than `com.simplediagrams.document` gives "The document format *x* is not supported."

A writer creating a document MUST write this manifest (white space and key order aside), with
`app_version` and `app_build` added or not:

```json
{"format": "com.simplediagrams.document", "format_version": 19, "contains_legacy_source": false}
```

## The document

`document.json` is one JSON object. Its top level is a strict record: an unknown key refuses the
document.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique in the document's shared namespace ([Identity rules](#identity-rules)) | The document's own identity |
| `schema_version` | whole number | Yes | Exactly 18 | The document schema version. It MUST equal `format_version` minus one |
| `revision` | whole number | Yes | 0 to 1,000,000,000 | A counter the app raises by one for each committed edit. A new document SHOULD use `0` |
| `name` | string | Yes | At most 512 bytes; no NUL character; may be empty | A name for the document. The app keeps it unchanged but shows the file's name on disk instead. A writer SHOULD use a short readable name |
| `selected_page_id` | UUID | Yes | The `id` of one of the pages | The page shown when the document opens |
| `pages` | array of pages | Yes | 1 to 100 | The pages, in order ([Pages](#pages)) |
| `quick_styles` | array | Yes | 0 to 32 | The document's Quick Styles ([Quick Styles](#quick-styles)). `[]` is valid |
| `embedded_assets` | array | Yes | 0 to 10,000; MUST match the files in `assets/` exactly | The asset inventory ([assets.md](assets.md)). `[]` when `assets/` is empty |
| `shape_data_definitions` | array | Yes | Every entry MUST be used by at least one element | The shared table of shape-data definitions ([shape-data.md](shape-data.md)). `[]` when no element has shape data |
| `required_libraries` | array | No | See [libraries.md](libraries.md) | Libraries the writer says the document needs. The app writes the key only when the list is not empty |
| `library_shape_names` | array | No | See [libraries.md](libraries.md) | A readable name for each library shape the document uses. The app writes the key only when the list is not empty |

Refusals at this level:

- `schema_version` other than 18 in a document format 19 file: "invalid document.json".
- A negative `revision`: "The diagram contains an invalid number in document revision." Above one
  billion: "The document editing history is too large to open safely."
- `name` too long or containing NUL: "The diagram contains invalid text in document name."
- Zero pages: "The diagram contains an invalid number of pages (0)." More than 100 pages is refused while
  reading, with the general "invalid document.json" message.
- More than 32 Quick Styles: "The diagram contains too many Quick Styles (*n*)."
- A `selected_page_id` that names no page: "The selected page … does not exist."

A skeleton, with the page left out (see [Pages](#pages)):

```json
{
  "id": "6F1D2C3A-8B4E-4A7D-9C21-5E0F3A1B2C4D",
  "schema_version": 18,
  "revision": 0,
  "name": "Order flow",
  "selected_page_id": "0B7E9A12-3C4D-4E5F-8A6B-7C8D9E0F1A2B",
  "pages": [ "… one page object …" ],
  "quick_styles": [],
  "embedded_assets": [],
  "shape_data_definitions": []
}
```

## Pages

A page holds its elements, connections and groups, a grid, a background, and some optional settings for
output and alignment.

### Why a page is nested

A page record is several records wrapped inside one another, each under the key `content`. Each time
the format needed new page fields, the older record was kept unchanged and wrapped in a new one with the
new keys beside it. This keeps the reading code for older formats working, at the cost of depth.

Two consequences matter to a writer:

- The real data sits at different depths. The page's `id` is three `content` levels down; its elements
  are at the top.
- Some arrays appear at more than one level, left over from the level that used to own them. Only one
  copy of each is real. The others MUST be present and MUST be empty, and the app refuses a document
  with anything in them.

### A page, layer by layer

```text
page                                        outer record (strict)
├─ elements: [ element, ... ]               THE PAGE'S ELEMENTS (elements.md)
└─ content                                  layer A
   ├─ groups: [ group, ... ]                THE PAGE'S GROUPS (connections.md)
   ├─ background: { ... }                   required
   ├─ elements: []                          required, MUST be empty
   ├─ physicalOutputLayout                  optional
   ├─ backgroundReferenceFrame              optional
   ├─ drawableOrder                         optional
   ├─ guides                                optional
   └─ content                               layer B
      ├─ connections: [ connection, ... ]   THE PAGE'S CONNECTIONS (connections.md)
      ├─ elements: []                       required, MUST be empty
      ├─ groups: []                         required, MUST be empty
      └─ content                            layer C: the page itself
         ├─ id, name, width, height         required
         ├─ grid: { ... }                   required
         ├─ export_frame                    optional
         ├─ elements: []                    required, MUST be empty
         └─ connections: []                 required, MUST be empty
```

The app refuses content in all five left-over arrays, in layers A, B and C, with "The SimpleDiagrams
document contains an invalid document.json file." A writer that puts its elements or connectors one
level too deep is told so at once, rather than getting an empty page.

#### Outer record

Strict: an unknown key refuses the document.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `content` | object | Yes | | Layer A |
| `elements` | array of elements | Yes | At most 100,000 on one page (refused while reading); 100,000 across the document | The page's elements ([elements.md](elements.md)). Their order in the array does not decide drawing order ([Drawing order](#drawing-order)) |

#### Layer A

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `content` | object | Yes | | Layer B |
| `background` | object | Yes | | What is drawn behind the page ([Background](#background)) |
| `elements` | array | Yes | MUST be `[]` | Left over from an older format |
| `groups` | array of groups | Yes | 100,000 groups across the document | The page's groups ([connections.md](connections.md)) |
| `physicalOutputLayout` | object | No | See [below](#physical-output-layout) | A fixed paper or pixel size for output. Absent: output is sized to the artwork |
| `backgroundReferenceFrame` | rectangle | No | `x`, `y` within ±1,000,000,000; `width`, `height` above 0 and at most 10,000,000 | Where a background image is anchored ([Background](#background)) |
| `drawableOrder` | array | No | MUST list every element and connection on the page exactly once | Back-to-front drawing order ([Drawing order](#drawing-order)). Absent: the default order applies |
| `guides` | array of guides | No | At most 1,000 | Ruler guides ([Guides](#guides)). The app leaves the key out when there are none |

#### Layer B

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `content` | object | Yes | | Layer C |
| `connections` | array of connections | Yes | 200,000 across the document | The page's connectors ([connections.md](connections.md)) |
| `elements` | array | Yes | MUST be `[]` | Left over from an older format |
| `groups` | array | Yes | MUST be `[]` | Left over from an older format |

#### Layer C

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique in the document's shared namespace | The page's identity |
| `name` | string | Yes | At most 512 bytes; no NUL; may be empty | The name shown in the app's list of pages |
| `width` | number, diagram points | Yes | Above 0, at most 10,000,000 | A reference size only (see below). The app uses 2,000 for a new page |
| `height` | number, diagram points | Yes | Above 0, at most 10,000,000 | A reference size only. The app uses 1,200 for a new page |
| `grid` | object | Yes | | The page's grid ([Grid](#grid)) |
| `export_frame` | rectangle | No | `x`, `y` within ±1,000,000,000; `width`, `height` above 0 and at most 10,000,000 | An authored output region ([below](#export-frame)) |
| `elements` | array | Yes | MUST be `[]` | Left over from an older format |
| `connections` | array | Yes | MUST be `[]` | Left over from an older format |

**About `width` and `height`.** They do not bound the page: elements may lie anywhere, and neither these
values nor the export frame clip anything. They have one effect: when a page has a background image and
no `backgroundReferenceFrame`, the image is placed in the rectangle from `(0, 0)` to
`(width, height)`.

#### An empty page

The smallest valid page, with no elements, connections or groups:

```json
{
  "content": {
    "content": {
      "content": {
        "id": "0B7E9A12-3C4D-4E5F-8A6B-7C8D9E0F1A2B",
        "name": "Page 1",
        "width": 2000,
        "height": 1200,
        "grid": {"spacing_x": 20, "spacing_y": 20, "origin": {"x": 0, "y": 0}},
        "elements": [],
        "connections": []
      },
      "connections": [],
      "elements": [],
      "groups": []
    },
    "background": {"kind": "none"},
    "elements": [],
    "groups": []
  },
  "elements": []
}
```

## Grid

The grid has two parts that do different jobs. Spacing and origin decide where shapes snap. Visibility
and appearance decide only what is drawn; changing them never changes snapping.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `spacing_x` | number, diagram points | Yes | 4 to 10,000 | Width of one grid cell |
| `spacing_y` | number, diagram points | Yes | 4 to 10,000 | Height of one grid cell |
| `origin` | point | Yes | Each coordinate within ±1,000,000,000 | A page position that lies on a grid crossing. It shifts the whole lattice |
| `visible` | boolean | No | | Whether the grid is drawn. Absent: `true` |
| `appearance` | object | No | | How the grid is drawn. Absent: the standard look (below) |

The app writes `visible` and `appearance` every time. A page without them opens with the grid shown in
the standard look.

Keys of `appearance`. When `appearance` is present, every key except `color` is required. The "standard
look" column gives the values used when `appearance` is absent.

| Key | Type | Required | Rules / bounds | Standard look | Meaning |
| --- | --- | --- | --- | --- | --- |
| `minor_line` | string | Yes | `none`, `solid`, `dashed`, `dotted` | `solid` | Lines on every cell boundary |
| `major_line` | string | Yes | `none`, `solid`, `dashed`, `dotted` | `none` | Lines on every *N*th cell boundary, replacing the minor line there |
| `major_interval` | whole number | Yes | 2 to 20 | `5` | *N*: how many cells lie between major lines |
| `marker` | string | Yes | `none`, `dot`, `cross` | `none` | A small mark where lines cross |
| `marker_placement` | string | Yes | `every`, `major` | `major` | `every`: at each cell corner. `major`: only where two major lines would cross, whether or not major lines are drawn |
| `color` | string | No | Exactly `#RRGGBB`; no alpha | absent | The grid colour. Absent: the app picks a colour to suit the background. Alpha is refused because `strength` is the grid's only opacity |
| `strength` | number | Yes | 0.02 to 1 | `0.10` | Opacity of the minor lines. Major lines are drawn at twice this and markers at four times, each capped at 1 |

Refusals: spacing out of range gives "The diagram contains an invalid number in grid spacing."; likewise
"grid origin", "grid major interval" and "grid strength". A colour with alpha, or not a colour, gives
"The diagram contains an invalid color value: *x*." An unknown line, marker or placement name gives
"invalid document.json".

## Background

`background` is always present and has a `kind`. Which other keys are allowed depends on the kind, and
a wrong combination is refused.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `kind` | string | Yes | `none`, `solid`, `image` | Which sort of background |
| `color_hex` | colour | For `solid`; MUST be absent otherwise | A colour | The fill colour |
| `asset_id` | string | For `image`; MUST be absent otherwise | The `id` of an embedded asset of kind `raster_png` or `raster_jpeg` ([assets.md](assets.md)) | The image |
| `layout` | string | For `image`; MUST be absent otherwise | `actual_size`, `tile`, `stretch` | How the image is placed |
| `tint` | object | Optional for `image`; MUST be absent otherwise | | A colour wash over the image |

`tint` has two required keys: `color_hex` (a colour) and `opacity` (a number from 0 to 1).

The image is anchored to a *reference rectangle*: the page's `backgroundReferenceFrame` when it has one,
otherwise the rectangle from `(0, 0)` to the page's `(width, height)`.

| `layout` | Result |
| --- | --- |
| `actual_size` | One copy at the image's own pixel size, its top-left at the reference rectangle's top-left corner |
| `tile` | Repeated without scaling, the tiling starting from the reference rectangle's top-left corner |
| `stretch` | One copy scaled to fill the reference rectangle |

The three valid forms:

```json
[
  {"kind": "none"},
  {"kind": "solid", "color_hex": "#FFF9DB"},
  {"kind": "image", "asset_id": "9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08", "layout": "tile", "tint": {"color_hex": "#FFFFFF", "opacity": 0.4}}
]
```

Refusals: a wrong combination of keys, or an unknown `kind` or `layout`, gives "invalid document.json".
An `asset_id` that names no embedded PNG or JPEG gives "The diagram contains invalid text in page
background asset reference." A tint opacity outside 0 to 1 gives "The diagram contains an invalid number
in page background tint opacity."

A background never counts towards the page's content bounds.

## Export frame, physical output layout and background reference frame

These three optional values concern output and placement, not the drawing itself.

### Export frame

`export_frame` (layer C) is a rectangle in page coordinates. The app offers it as one of the regions
that can be exported. Absent, the page has no authored export region.

### Background reference frame

`backgroundReferenceFrame` (layer A, camel case) is a rectangle in page coordinates that anchors a
background image ([Background](#background)).

### Physical output layout

`physicalOutputLayout` (layer A, camel case) gives the page a fixed output size, such as A4. Absent,
output is sized to the artwork. When the object is present, all six keys are required.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `width` | number, in `unit` | Yes | Above 0 | One side of the output page |
| `height` | number, in `unit` | Yes | Above 0 | The other side |
| `unit` | string | Yes | `millimeters`, `inches`, `points`, `pixels` | The unit of `width` and `height` |
| `dpi` | number | Yes | 1 to 2,400 | Pixels per inch. Used to convert when `unit` is `pixels`; MUST be in range whatever the unit |
| `marginPoints` | number, diagram points | Yes | 0 to 720 | The margin on every side |
| `landscape` | boolean | Yes | | `true`: the longer side is horizontal. `false`: the longer side is vertical. Which of `width` and `height` is larger does not matter |

One more rule ties them together. Converted to points (millimetres × 72 ÷ 25.4; inches × 72; points
unchanged; pixels × 72 ÷ `dpi`), each side MUST be from 1 to 14,400 points and MUST be more than twice
`marginPoints`. Otherwise the app refuses the document with "Choose a finite, positive output size with
margins smaller than the page and DPI between 1 and 2,400."

## Drawing order

A page can state what is drawn on top of what in one of two ways. A writer chooses one.

**The default order, used when `drawableOrder` is absent.** All connections are drawn first, in their
array order, so they sit behind every element. Then elements are drawn in ascending `z_index` (higher is
nearer the front). Two elements with the same `z_index` are ordered by their identifiers, compared as
upper-case UUID text. In this mode a connector is never in front of an ordinary shape.

**The explicit order, used when `drawableOrder` is present.** It is one list, back to front, of every
element and connection on the page. Each entry is:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `kind` | string | Yes | `element` or `connection` | What the entry refers to |
| `id` | UUID | Yes | The `id` of an element or connection on this page, of that kind | Which one |

The list MUST name every element and connection on the page exactly once: no omissions, no repeats, no
identifiers from elsewhere. Otherwise the app refuses the document with "The diagram contains an invalid
number in drawable order." When the list is present, `z_index` is not used for drawing, though every
element still requires it. Groups do not appear in the list; only things that are drawn do.

**Informative.** A new page made by the app has no `drawableOrder`. The app adds one when a person uses a
stacking command or pastes, and from then on keeps it complete.

**Containers and swimlanes.** In both modes, a container or swimlanes element is drawn, and hit-tested,
just behind the lowest-ordered thing it holds, so that it never hides its own contents. Nothing is
written back: the stored order is unchanged. A writer therefore cannot put a container in front of its
own contents, and a connector joined to a shape inside a container is drawn in front of that container
even in the default order. See [elements.md](elements.md) for what a container holds.

## Guides

A guide is a ruler line a person drags out to align things. It belongs to the page but is not drawn
content: it is not in the drawing order, and copying content does not copy it.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique in the document's shared namespace | The guide's identity |
| `axis` | string | Yes | `horizontal` or `vertical` | A horizontal guide is a line of constant y; a vertical guide is a line of constant x |
| `position` | number, diagram points | Yes | Within ±1,000,000,000 | The y of a horizontal guide, or the x of a vertical one, in page coordinates |

At most 1,000 guides on one page ("The diagram contains an invalid number in manual guides.").

## Quick Styles

A Quick Style is a named preset of fill and line that a person applies to a shape with one click. Quick
Styles belong to the document.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique in the document's shared namespace | The preset's identity |
| `name` | string | Yes | 1 to 128 bytes; no leading or trailing white space; no control or format characters | The name shown |
| `style` | object | Yes | | The paint, below |

Keys of `style`:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `fill_hex` | colour | Yes | A colour | Fill colour |
| `stroke_hex` | colour | Yes | A colour | Line colour |
| `line_width` | number, diagram points | Yes | **1 to 100** (narrower than an element's 0 to 10,000) | Line weight |
| `fill_texture` | string | No | A texture name ([elements.md](elements.md)) | Fill texture. Absent: none |
| `stroke_texture` | string | No | A texture name | Line texture. Absent: none |
| `stroke_pattern` | string | No | `solid`, `dashed`, `dotted` | Line pattern. Absent: applying the preset leaves an element's line pattern unchanged |

The array may be empty. **Informative.** A new document made by the app carries six presets, named in
the language of the Mac that made it (Outline, Paper, Note, Sky, Mint and Sketch in English), with the
fixed identifiers `51554943-4B53-4000-8000-000000000001` to `51554943-4B53-4000-8000-000000000006`. A
writer MAY include presets with these identifiers or any others; the names are ordinary text.

Refusals: "The diagram contains invalid text in Quick Style name."; "The diagram contains an invalid
number in Quick Style line width."; an unknown texture or pattern name gives "invalid document.json".

## Identity rules

Most things in a document are identified by a UUID, and those UUIDs share one namespace: no two may be
equal anywhere in the document. A few other things use numbers or names with narrower rules.

| Identifier | Form | Must be unique within | Notes |
| --- | --- | --- | --- |
| Document `id` | UUID | The shared namespace | |
| Page `id` | UUID | The shared namespace | A page may not share an identifier with any element, guide, Quick Style and so on, not only with other pages |
| Element `id` | UUID | The shared namespace (all pages) | [elements.md](elements.md) |
| Connection `id` | UUID | The shared namespace (all pages) | [connections.md](connections.md) |
| Group `id` | UUID | The shared namespace (all pages) | [connections.md](connections.md) |
| Guide `id` | UUID | The shared namespace (all pages) | |
| Quick Style `id` | UUID | The shared namespace | |
| Shape-data definition `id` | UUID | `shape_data_definitions` | A separate namespace, not compared with the shared one ([shape-data.md](shape-data.md)) |
| Shape-data field `id` | UUID | Its definition | Normally the same in every definition derived from the same library field |
| Choice option `id` | UUID | Its field | |
| Part `id` | UUID | Its complex shape | A separate namespace: two complex shapes may share part identifiers, as a copied shape keeps its parts' ([elements.md](elements.md#123-parts)) |
| Connection point `id` | whole number, 0 or more | Its element | Connectors refer to it ([elements.md](elements.md)) |
| Attach line number | whole number | Its element | Fixed per kind of shape ([connections.md](connections.md)) |
| Embedded asset `id` | 64 lower-case hexadecimal digits | `embedded_assets` | The SHA-256 digest of the file's bytes, so it cannot be chosen ([assets.md](assets.md)) |
| `.sdvector` `asset_id` | 64 lower-case hexadecimal digits | | Derived from the artwork's canonical form ([assets.md](assets.md)) |
| `required_libraries` entry | `library_id` | The list, ignoring letter case | [libraries.md](libraries.md) |
| `library_shape_names` entry | `library_id` and `item_id` | The list, ignoring letter case | [libraries.md](libraries.md) |

A repeated identifier in the shared namespace refuses the document with "The diagram repeats identifier
*id*."

**Letter case.** UUIDs are compared as values, so `6f1d2c3a-…` and `6F1D2C3A-…` are the same identifier
(and therefore collide). The app writes upper case; a writer SHOULD too. Library and item identifiers
have their own case rules ([libraries.md](libraries.md)).

**Permanence.** The app keeps element, connection, group, page and guide identifiers from one save to
the next; they are what connectors, groups, the drawing order and shape data hold on to. A writer that
edits an existing document SHOULD keep them too. (Pasting or duplicating content gives the copies new
identifiers.) A writer creating a document MUST generate a fresh UUID for each; random (version 4) UUIDs
are suitable.

**Fixed names are permanent.** Every enumerated value in this specification (element kinds, line
endings, textures, patterns and the rest) is stored exactly as listed and never renamed, even where the
name is misspelt or no longer matches what the app calls it. A writer MUST write them exactly.

## Limits

The limits are generous for real diagrams. They exist so that a damaged or hostile file cannot exhaust
memory or time before anything is drawn. A writer MUST stay within every limit.

"Document" means a total across all pages. "±*n*" means from −*n* to *n* inclusive. Ranges are
inclusive unless stated. A limit marked **R** is enforced while the file is read, so exceeding it gives
the general "invalid document.json" message rather than a specific one.

### Package and document

| What is limited | Limit | Defined in |
| --- | --- | --- |
| Members of the package folder | 4 at most (`manifest.json`, `document.json`, `assets`, `legacy`) | [The package on disk](#the-package-on-disk) |
| `manifest.json` size | 64 KiB (65,536 bytes) | [The package on disk](#the-package-on-disk) |
| `document.json` size | 100 MiB (104,857,600 bytes) | [The package on disk](#the-package-on-disk) |
| `legacy/source.sdxml` size | 100 MiB | [The package on disk](#the-package-on-disk) |
| Members of `legacy/` | exactly one, `source.sdxml` | [The package on disk](#the-package-on-disk) |
| Files in `assets/` | 10,000 | [assets.md](assets.md) |
| One asset's size | 16 MiB (16,777,216 bytes) | [assets.md](assets.md) |
| All assets together | 100 MiB | [assets.md](assets.md) |
| Asset file name | at most 256 bytes; no leading `.`; no `/` or `\` | [assets.md](assets.md) |
| `revision` | 0 to 1,000,000,000 | [The document](#the-document) |
| Pages | 1 to 100 (above 100: **R**) | [The document](#the-document) |
| Document name, page name | at most 512 bytes; no NUL | [The document](#the-document), [Pages](#pages) |
| Quick Styles | 32 | [Quick Styles](#quick-styles) |
| Quick Style name | 1 to 128 bytes; no leading or trailing white space; no control or format characters | [Quick Styles](#quick-styles) |
| Quick Style line width | 1 to 100 | [Quick Styles](#quick-styles) |
| Elements | 100,000 in the document; above 100,000 on one page: **R** | [elements.md](elements.md) |
| Connections | 200,000 in the document | [connections.md](connections.md) |
| Groups | 100,000 in the document | [connections.md](connections.md) |
| Direct group members | 200,000 in the document | [connections.md](connections.md) |
| Members of one group | at least 2 | [connections.md](connections.md) |
| Group nesting depth | 64 | [connections.md](connections.md) |
| Path nodes plus connector points | 1,000,000 in the document | [elements.md](elements.md), [connections.md](connections.md) |

### Geometry, colour and page settings

| What is limited | Limit | Defined in |
| --- | --- | --- |
| Any position or coordinate (frames, points, path nodes, guides, grid origin, label offsets) | ±1,000,000,000 | [Terms](#terms), each record's page |
| Any size (frames, page width and height, export and reference frames) | above 0, at most 10,000,000 | each record's page |
| Element rotation | ±1,000,000,000 degrees | [elements.md](elements.md) |
| Text region, group and connector routing rotations | at least 0 and less than 360 | [elements.md](elements.md), [connections.md](connections.md) |
| Element and connector line width | 0 to 10,000 | [elements.md](elements.md), [connections.md](connections.md) |
| Colours | `#RRGGBB` or `#RRGGBBAA` | [Terms](#terms) |
| Grid colour | `#RRGGBB` only | [Grid](#grid) |
| Grid spacing | 4 to 10,000 on each axis | [Grid](#grid) |
| Grid major interval | 2 to 20 | [Grid](#grid) |
| Grid strength | 0.02 to 1 | [Grid](#grid) |
| Background tint opacity | 0 to 1 | [Background](#background) |
| Guides on one page | 1,000 | [Guides](#guides) |
| Physical output DPI | 1 to 2,400 | [Physical output layout](#physical-output-layout) |
| Physical output margin | 0 to 720 points | [Physical output layout](#physical-output-layout) |
| Physical output side, after conversion | 1 to 14,400 points, and more than twice the margin | [Physical output layout](#physical-output-layout) |

### Elements and text

| What is limited | Limit | Defined in |
| --- | --- | --- |
| Text `string`; `unresolved_content` source fragment | 1,000,000 bytes; no NUL | [elements.md](elements.md) |
| Character runs plus paragraph runs in one text | 100,000 | [elements.md](elements.md) |
| Font family | 1 to 256 bytes; not blank; no control or format characters | [elements.md](elements.md) |
| Font size | 1 to 720 points | [elements.md](elements.md) |
| Text region origin (`text_layout.frame.x`, `y`), as fractions of the frame | `x` from −`width` to 1, `y` from −`height` to 1, where `width` and `height` are the region's own (**R**) | [elements.md](elements.md) |
| Text region size, as fractions of the frame | 0.001 to 8 (**R**) | [elements.md](elements.md) |
| Nodes in one path | at least 2; at least 3 if closed | [elements.md](elements.md) |
| Connection points on one element | 0 to 128 | [elements.md](elements.md) |
| Connection point number | 0 or more; unique in its element | [elements.md](elements.md) |
| Connection point position | 0 to 1 on each axis | [elements.md](elements.md) |
| `symbol_reference` identifiers | 1 to 256 bytes; asset version above 0 | [elements.md](elements.md), [libraries.md](libraries.md) |
| `unresolved_content` source format; type identifier | 128 bytes; 512 bytes | [elements.md](elements.md) |

### Complex shapes

Every list in a `complex_shape` record is read no further than one entry past its limit. The limits of
the structure every complex shape shares, from `version` to a placed text's turn below, are enforced
while reading (**R**). How much a shape or a page holds gives a message naming the limit
([elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold)); the others give the complex-shape
message ([elements.md](elements.md#129-which-message-a-fault-gives)). Each type's own
limits, such as how many lanes, rows or sections it has, are in its entry in the
[complex-shape catalogue](complex-shapes/README.md).

| What is limited | Limit | Defined in |
| --- | --- | --- |
| `version` | 1 to 999 | [elements.md](elements.md#121-the-structure) |
| Settings in one shape | 16 | [elements.md](elements.md#122-settings) |
| Parts in one shape, whatever its type | 256 | [elements.md](elements.md#121-the-structure) |
| A word: a type, setting name, setting word, role or mark | 32 bytes, matching `^[a-z][a-z0-9]*(_[a-z0-9]+)*$` | [elements.md](elements.md#124-words) |
| Cells in one part; marks on one part; points one part owns | 50; 8, none repeated; 2, each 8 or more | [elements.md](elements.md#123-parts) |
| A part's share (`size`) | 0.000001 to 1,000,000 | [elements.md](elements.md#123-parts) |
| A part's line width | 0 to 10,000 | [elements.md](elements.md#123-parts) |
| A placed text's turn | More than 0 and less than 360 degrees | [elements.md](elements.md#123-parts) |
| Members of one container or lane | 1,000; none repeated | [elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold) |
| Members of all the lanes of one swimlanes shape | 2,000 | [elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold) |
| Containers and swimlanes nested inside one another | 4 deep | [elements.md](elements.md#1210-rules-across-the-page) |
| Part texts and cells | 100,000 in the document | [elements.md](elements.md#1210-rules-across-the-page) |
| One lane title | 1,000 scalars and 100 runs | [elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold) |
| One table cell; all the cells of one table | 2,000 scalars and 200 runs; 100,000 scalars and 10,000 runs | [elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold) |
| The text of a ring's section, a flow's or cycle's step, a pyramid's layer, a gauge's band or caption | 500 scalars | The type's entry in the [catalogue](complex-shapes/README.md) |
| Complex shapes on one page; containers and swimlanes on one page | 2,000; 500 | [elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold) |
| Part texts and cells on one page | 1,000,000 scalars together | [elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold) |

### Connections

| What is limited | Limit | Defined in |
| --- | --- | --- |
| Points in one connector | at least 2 | [connections.md](connections.md) |
| Connector label position | 0 to 1 | [connections.md](connections.md) |
| Attachment `distance` along an attach line | 0 to 10,000,000 | [connections.md](connections.md) |

### Libraries

| What is limited | Limit | Defined in |
| --- | --- | --- |
| `library_shape_names` entries | at most 10,000; a reader examines only the first 10,000 | [libraries.md](libraries.md#library-shape-names) |
| Library shape name identifiers | 1 to 256 bytes; no NUL | [libraries.md](libraries.md) |
| Shape slug | at most 128 bytes | [libraries.md](libraries.md) |
| `required_libraries` entries examined; kept | the first 256; at most 64 | [libraries.md](libraries.md) |
| Required library identifier | 1 to 256 bytes | [libraries.md](libraries.md) |
| Required library display name | at most 200 scalars | [libraries.md](libraries.md) |
| Required library minimum revision | 1 to 1,000,000 | [libraries.md](libraries.md) |

### Shape data

| What is limited | Limit | Defined in |
| --- | --- | --- |
| Definitions in `shape_data_definitions` | 100,000 (**R**) | [shape-data.md](shape-data.md) |
| Fields across all definitions | 100,000 (**R**) | [shape-data.md](shape-data.md) |
| Fields in one definition | 128 (**R**) | [shape-data.md](shape-data.md) |
| Values on one element | 128 (**R**) | [shape-data.md](shape-data.md) |
| Values | 100,000 in the document (**R**) | [shape-data.md](shape-data.md) |
| Shape-data text | 16 MiB in the document (**R**) | [shape-data.md](shape-data.md) |
| Field name | 1 to 120 scalars | [shape-data.md](shape-data.md) |
| Field unit | at most 64 scalars; number fields only | [shape-data.md](shape-data.md) |
| Choice options in one field; option label | 1 to 128; 1 to 256 scalars | [shape-data.md](shape-data.md) |
| Stored text value | at most 4,096 scalars | [shape-data.md](shape-data.md) |
| `source` identifiers | 1 to 256 bytes; asset version above 0 | [shape-data.md](shape-data.md) |

### Embedded assets

| What is limited | Limit | Defined in |
| --- | --- | --- |
| PNG or JPEG image: each side; pixels; size | 1 to 16,384 pixels; 40,000,000 pixels; 16 MiB | [assets.md](assets.md) |
| SVG artwork size | 16 MiB | [assets.md](assets.md) |
| SVG elements; nesting depth | 100,000; 128 | [assets.md](assets.md) |
| SVG attributes on one element | 128 | [assets.md](assets.md) |
| SVG attribute values | 16 MiB | [assets.md](assets.md) |
| SVG text and CDATA; one text node; one `<style>` block | 4 MiB; 2 MiB; 2 MiB | [assets.md](assets.md) |
| SVG element and attribute names | 256 bytes | [assets.md](assets.md) |
| SVG local reference (`#id`) | 1 to 256 bytes after `#` | [assets.md](assets.md) |
| SVG `feGaussianBlur` `stdDeviation` | one or two numbers, each 0 to 100 | [assets.md](assets.md) |
| SVG view box or size | 0.000001 to 10,000,000 | [assets.md](assets.md) |
| PNG textures inside one SVG | 4 images; 1,024 pixels a side; 1,048,576 pixels and 4 MiB + 64 KiB in total | [assets.md](assets.md) |
| `.sdvector` size | 16 MiB | [assets.md](assets.md) |
| `.sdvector` layers | 1 to 50,000 | [assets.md](assets.md) |
| `.sdvector` path commands, counting every layer's path and every clip path | 250,000 | [assets.md](assets.md#path-commands) |
| `.sdvector` clip paths on one layer | 16 | [assets.md](assets.md) |
| `.sdvector` dash entries | at most 1,024, an even number | [assets.md](assets.md) |
| `.sdvector` coordinates | ±1,000,000,000 | [assets.md](assets.md) |
| `.sdvector` view box and intrinsic size | 0.000001 to 10,000,000 | [assets.md](assets.md) |
| `.sdvector` stroke width; miter limit; dash lengths | 0 to 10,000; 1 to 10,000; 0 to 10,000 | [assets.md](assets.md) |
| `.sdvector` compositing groups; depth; layer visits | 4,096; 16; 250,000 | [assets.md](assets.md) |
| `.sdvector` opacities | 0 to 1 | [assets.md](assets.md) |
| `.sdvector` pinned parts; outlines; copies of one outline | 1,024; 64; 1 to 4 | [assets.md](assets.md) |

## Versions

### Document format 19

Document format 19 is the format written by SimpleDiagrams 5.2. Its manifest has `format_version` 19,
and its `document.json` has `schema_version` 18.

A writer MUST write document format 19 with document schema version 18. Any other pair is refused: for
example, 19 in both places, or format 19 with schema 17.

The app always saves in document format 19, whatever the document contains. So a document saved by the
app needs SimpleDiagrams 5.2 or later to open, even if it uses nothing new.

### The complex-shape catalogue

What each type of complex shape holds is not in the core of this specification but in the
[complex-shape catalogue](complex-shapes/README.md): one entry for each type and version, such as
`ring` version 1. Only SimpleDiagrams defines complex shapes; a writer MUST write only the types and
versions the catalogue lists. Within document format 19, a later SimpleDiagrams release may add a type,
or a new version of one, as a new entry. Nothing else ever changes: an entry never changes, its words
stay a closed list, and the structure every complex shape shares stays as it is, so a document that was
valid stays valid. A reader that does not know a type or version opens the document, shows that shape as
a placeholder and keeps its record exactly as read ([elements.md](elements.md#128-reading-a-complex-shape)).

### Older documents

This specification describes document format 19 only. A writer MUST write document format 19. The app
also opens documents saved by earlier versions of SimpleDiagrams, and saves them as document format 19.

### Newer documents

The app refuses a document whose `format_version` is higher than the newest it knows, with "This
document was saved by a newer version of SimpleDiagrams." followed by "Update SimpleDiagrams to the
latest version, then open the document again." It never opens a newer document and silently drops what
it does not understand.

The app makes this check first. Once it has opened the package folder, and before it examines anything
else in it, it reads only `format` and `format_version` from `manifest.json`; if they name this format at
a newer version, it asks the person to update, even if the rest of the package would not be valid under
its own rules. Every document format keeps `manifest.json` at the top of the package, with these two
keys unchanged in name and meaning, so that this check always works.

A reader SHOULD do the same: read `format` and `format_version` first, and refuse a version it does not
implement rather than read the document partially.

### What the app may change when it opens a document

**Informative.** Opening a document normally changes nothing on disk. There is one exception. When a
document places shapes from a downloaded library installed on this Mac, or names a built-in shape by its
published identifiers, the app fills in those shapes as it opens the document
([libraries.md](libraries.md)). It then treats the document as edited, and macOS saves it in place. A writer that keeps
its own copy of a document SHOULD expect the file to change after the app opens it.

## Writing a document from a program

Build the identities and geometry first, then the records that refer to them, then the asset inventory,
and check everything before writing the package. The [examples](examples/README.md) show complete
documents built this way.

### Checklist

1. **Use document format 19.** Write `format_version` 19 in the manifest and `schema_version` 18 in
   `document.json` ([Versions](#versions)).
2. **Make every identifier.** Generate a fresh UUID for the document and for each page, element,
   connector, group, guide and Quick Style. Keep them all distinct ([Identity rules](#identity-rules)).
3. **Lay out the elements.** Decide each element's frame in page coordinates (origin top-left, y
   downwards). Give each element the connection points connectors need, numbered and placed as
   fractions of the frame ([elements.md](elements.md)).
4. **Fill in each element** for its kind: style, text, and the kind-specific keys it requires; leave out
   the keys its kind forbids. Write `shape_data_definition_id` (as `null` if the element has no shape
   data) and `shape_data_values` on every element. For a complex shape, write its `complex_shape`
   record: the type and version, every setting its catalogue entry declares, defaults included, and
   its parts, with what each container or lane holds. Write no drawing: the app draws the shape from
   these ([elements.md](elements.md#12-complex-shapes), [complex-shapes/](complex-shapes/README.md)).
5. **Add connectors.** For each attached end, compute where the connection point lies on the page and
   store that position as the end point, as well as the attachment ([connections.md](connections.md)).
6. **Add groups.** Each group has at least two members, all on its own page, and no member is in two
   groups. A container or swimlanes element MUST NOT be in a group ([connections.md](connections.md)).
7. **Decide the drawing order.** Either rely on `z_index` (connectors behind everything), or write a
   complete `drawableOrder` naming every element and connection on the page exactly once
   ([Drawing order](#drawing-order)).
8. **Build the shape-data table** from the definitions your elements use, and give each such element one
   value per field ([shape-data.md](shape-data.md)). If no element has shape data, write
   `"shape_data_definitions": []`.
9. **Write the assets.** Name each file after the lower-case hexadecimal SHA-256 digest of its bytes,
   with the extension `.sdvector`, `.svg`, `.png` or `.jpg`, and list it in `embedded_assets` with its
   exact size ([assets.md](assets.md)). If there are none, write `"embedded_assets": []`.
10. **Write the page records,** with every left-over array present and empty
    ([A page, layer by layer](#a-page-layer-by-layer)), and a grid and background on every page.
11. **Write `document.json`,** with `selected_page_id` naming one of the pages, `quick_styles` present
    (`[]` is fine), and no keys this specification does not define.
12. **Write the package:** the folder, `manifest.json` (with `contains_legacy_source` `false`),
    `document.json`, and `assets/` even when it is empty. Nothing else.
13. **Check the result.** Validate both JSON files against the [schemas](schemas/), then check the rules
    a schema cannot express against the list below.

### What the app refuses

Each of these stops the document opening. The [refused](refused/README.md) folder has one example of
most of them.

- A `schema_version` that is not `format_version` minus one.
- Any extra member in the package folder or in `assets/`, including `.DS_Store`, an AppleDouble `._`
  file, or a `.gitkeep` placed to keep an empty `assets/` folder in a repository. Create `assets/` when
  writing instead.
- A missing `assets/` folder.
- An unknown key in a strict record ([Unknown keys](#unknown-keys)).
- A missing required key anywhere, including `shape_data_definition_id` and the empty left-over arrays
  inside pages.
- Content in a left-over array that must be empty.
- An unknown value in a fixed list: an element `kind`, a line ending, texture, pattern, font weight,
  alignment, text position, routing name, background kind, or a word in a complex shape of a known type
  and version. (`note_style`, `marker_tip`, and the type or version of a complex shape are read
  differently; see
  [What the app does with content it does not understand](#what-the-app-does-with-content-it-does-not-understand).)
- A fraction in a whole-number field.
- A repeated UUID anywhere in the shared namespace.
- A colour that is not `#RRGGBB` or `#RRGGBBAA`, such as `#FFF` or `none`; a grid colour with alpha.
- A frame, page size or region with a width or height of zero or less.
- A connector with fewer than two points, or attached to an element on another page, to a connection
  point number the element does not have, or to an attach line it does not offer.
- A complex shape that breaks the structure of [elements.md](elements.md#12-complex-shapes): an
  unknown key, a `null`, a value of the wrong kind, a word that breaks the spelling rule, a point
  numbered below 8 or owned twice, a list past its limit.
- A complex shape of a known type and version that breaks its catalogue entry: a missing or undeclared
  setting, a word the entry does not list, parts out of order or with counts out of range, a key a role
  does not allow, a rule such as a gauge's value outside its range, or connection points listed on a
  shape that places its own.
- A holder whose members break the rules across the page: a member on another page or in two holders,
  a container or swimlanes in a group; or a container, lane, cell, table or page holding more than it
  may ([elements.md](elements.md#1211-how-much-a-shape-and-a-page-hold)).
- A group with fewer than two members, a member in two groups, or a group that contains itself.
- A `drawableOrder` that misses, repeats or invents anything.
- A kind-specific key on the wrong kind of element, or a required one missing.
- Text runs that overlap, run backwards, have zero length, or split a character.
- A shape-data definition that no element uses; values that do not cover every field exactly once;
  `null` written for `source`, `minimum` or `maximum`.
- An asset whose file name, size or digest does not match its inventory entry, or a file in `assets/`
  that is not listed.
- A `.sdvector` asset whose `asset_id` does not match its canonical form.
- Any value outside a limit in [Limits](#limits).

### What the app does with content it does not understand

**Informative.** These do not stop the document opening.

| Situation | What happens |
| --- | --- |
| An unknown key in a lenient record | Ignored, and lost at the next save |
| An unknown key inside a `.sdvector` asset | Ignored; the asset's bytes are kept unchanged |
| An unknown `marker_tip` value | Read as no tip ([elements.md](elements.md)) |
| An unknown `note_style` value | Drawn as a sticky note, and saved as `stickyNote` ([elements.md](elements.md#113-note)) |
| `background_hex` in a text record | Ignored; no text background is drawn ([elements.md](elements.md#61-the-text-content-record)) |
| A complex shape of a type or version the app does not know | Drawn as a labelled placeholder box, "Made with a newer version of SimpleDiagrams."; it can be moved, resized, turned, deleted, copied, grouped and locked, and its record and its element's flips are written back unchanged ([elements.md](elements.md#128-reading-a-complex-shape)) |
| A `symbol_reference` the app has no artwork for | Opens as a dashed placeholder box, with a notice naming the missing library or asking for a newer SimpleDiagrams; kept on save ([libraries.md](libraries.md)) |
| A faulty entry in `required_libraries` or `library_shape_names` | That entry is left out; the rest opens ([libraries.md](libraries.md)) |
| `routes_around_shapes` on a direct connector | Ignored ([connections.md](connections.md)) |
| An embedded asset that nothing refers to | Accepted, then dropped at the next save |
| A hand-adjusted right-angle route whose segments are not square | Accepted; the automatic route is drawn instead, and the stored points are kept ([connections.md](connections.md)) |
| An `unresolved` element | Opens as a dashed placeholder box ([elements.md](elements.md)) |

## Open points

1. **JSON leniencies.** The app runs on macOS 15 and later and reads JSON with the system's JSON
   reader. What this page says it currently does with `null` for optional keys, `1.0` in a
   whole-number field, repeated keys, and a byte-order mark or UTF-16 is that reader's behaviour, which
   this specification does not promise. This is why each is stated as something a writer MUST NOT rely
   on.
