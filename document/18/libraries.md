# Library shapes, required libraries and shape names

A **shape library** is a named collection of ready-made shapes. A document can use a library shape in
two ways:

- **By reference.** A `symbol` element names the shape by its library, its identifier in that library,
  and an artwork version. The artwork is not stored in the document; the reader must have the library
  ([elements.md](elements.md#115-symbol) defines the `symbol` element).
- **Embedded.** The shape's artwork is copied into the document as an embedded asset, and the element is
  an `editable_vector`, `svg_artwork` or `image` ([assets.md](assets.md)). The document is then complete
  without the library. Which library it came from is recorded, if at all, only in its shape-data
  definition's `source` ([shape-data.md](shape-data.md#the-link-to-a-library-shape)).

This page defines how a reference names a library shape, how the identifiers of published libraries are
derived from their names, the letter-case rules, the two optional top-level lists `required_libraries`
and `library_shape_names`, and, as informative notes, what the app does with references when it opens a
document. Conventions and limits are in [README.md](README.md).

On this page:

- [Kinds of library](#kinds-of-library)
- [Library shape references](#library-shape-references)
- [Built-in libraries](#built-in-libraries)
- [Placing a built-in shape](#placing-a-built-in-shape)
- [Name-based UUIDs](#name-based-uuids)
- [Letter case](#letter-case)
- [What the app does when it opens a document](#what-the-app-does-when-it-opens-a-document)
- [Required libraries](#required-libraries)
- [Library shape names](#library-shape-names)
- [Open points](#open-points)

## Kinds of library

| Kind | What it is | Its identifier in a reference (`library_id`) | Its qualified name |
| --- | --- | --- | --- |
| **Built-in** | Shipped inside the app | A reverse-DNS name, such as `com.simplediagrams.shapeLibrary.basic` | The same name |
| **Published** | Downloadable from the SimpleDiagrams website and installed on a Mac | A UUID derived from its qualified name ([Name-based UUIDs](#name-based-uuids)) | `com.simplediagrams.shapeLibrary.` followed by one or more ASCII letters and digits; at most 255 bytes in all |
| **Personal** | Made by a person in the app, or imported | A UUID chosen when the library was made | `com.simplediagrams.local.` followed by the library's UUID in lower case |

A library's **qualified name** is its readable, permanent name. With a shape's **slug**
([Library shape names](#library-shape-names)), it gives every library shape a readable identity beside
its identifiers.

Each built-in library and its shapes also have a published UUID, derived from their names in the same
way as a published library's.

## Library shape references

A `symbol` element's `symbol_reference` names one library shape:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `library_id` | string | Yes | 1 to 256 bytes; no NUL | The library: a built-in library's name, or a library's UUID |
| `item_id` | string | Yes | 1 to 256 bytes; no NUL | The shape within that library: a built-in shape's identifier, or a shape's UUID |
| `asset_version` | whole number | Yes | Above 0 | The version of the shape's artwork |

Many elements may use the same reference.

**Asset versions only grow.** When a library changes a shape's artwork, the shape gets a new asset
version; the old version keeps its old artwork. So a document never silently resolves to different
geometry from the one it was made with. A reference to a version a reader does not have is not
resolved to a nearby version. This rule holds for built-in and downloadable shapes. The one known
exception is the Basic library's `heptagon`, whose artwork changed in SimpleDiagrams 5.1.1 without its
asset version changing; a later release is expected to give it a new asset version.

**The reference gives only the artwork.** Everything else about the element (its frame, rotation,
paint, connection points, text and text layout) is the element's own and MUST be written out
([elements.md](elements.md)).

A reference MAY name:

- **a built-in shape**, by its built-in `library_id` and `item_id`, exactly as the app spells them
  ([Built-in libraries](#built-in-libraries));
- **a published library's shape**, by the library's UUID and the shape's UUID. Such a reference is a
  valid element; whether a reader can draw it depends on whether it has that library installed
  ([What the app does](#what-the-app-does-when-it-opens-a-document)). A writer that does this SHOULD name
  the library in [`required_libraries`](#required-libraries), so that a reader who lacks it can be told
  which library to get;
- **a personal library's shape**, by the library's UUID and the shape's UUID. Only a Mac that has that
  library installed can draw it.

A `source` in shape data uses the same three values under the camel-case keys `libraryID`, `itemID` and
`assetVersion` ([shape-data.md](shape-data.md#the-link-to-a-library-shape)).

## Built-in libraries

The built-in libraries of SimpleDiagrams 5.1.0 and 5.1.1, all at asset version 1:

| `library_id` | Name in the app | Shapes |
| --- | --- | --- |
| `com.simplediagrams.shapeLibrary.basic` | Basic | 62 |
| `com.simplediagrams.shapeLibrary.basicClean` | Basic (Clean) | 45 |
| `com.simplediagrams.shapeLibrary.3DBasicHatch` | 3D Basic (Hatch) | 74 |
| `com.simplediagrams.shapeLibrary.wordBubbles` | Word Bubbles | 50 |
| `com.simplediagrams.shapeLibrary.biz` | Biz | 83 |
| `com.simplediagrams.shapeLibrary.uiFlow` | UI Flow | 123 |
| `com.simplediagrams.shapeLibrary.bpmn` | BPMN | 89 |
| `com.simplediagrams.shapeLibrary.uml` | UML | 8 |

Built-in library and item identifiers are permanent: a later release does not rename them. Item
identifiers are case-sensitive and not always consistent between libraries: for example, `Square` in
Basic but `square` in 3D Basic (Hatch). Later releases may add libraries, shapes and asset versions.

**Informative.** A document that references a built-in shape added in a later release opens in an earlier
release with that shape drawn as a placeholder ([below](#what-the-app-does-when-it-opens-a-document)).

## Placing a built-in shape

[`built-in-shapes.json`](built-in-shapes.json), in this folder, lists every built-in shape of
SimpleDiagrams 5.1.0 and 5.1.1 with what a writer needs to place it by reference. For each library it
gives `library_id` and `display_name`; for each shape:

| Key | Meaning | Where it goes in the document |
| --- | --- | --- |
| `item_id` | The shape's identifier in its library, case-sensitive | `symbol_reference.item_id` |
| `slug` | The shape's readable name in its library | `slug` of its [`library_shape_names`](#library-shape-names) entry |
| `display_name` | The shape's English name in the app | Nowhere; for people choosing a shape |
| `asset_version` | The artwork version | `symbol_reference.asset_version` |
| `default_width`, `default_height` | The size, in diagram points, at which the app places the shape | A starting size for `frame`; any size is valid |
| `connection_points` | The shape's connection points, in the form an element stores them. For a shape with no points of its own, the file lists the app's standard eight | The element's `connection_points` |
| `text_region` | Present on some shapes: where the shape's label goes, as fractions of the frame | `text_layout.frame` |
| `text_position` | Present on some shapes that have a `text_region`. Absent: `middle` | `text_layout.position` |
| `resizing` | Present on one shape. **Informative:** how the app lets a person resize it (`width_only`: only its width changes). Absent: freely | Nowhere; it is not stored in a document |

To place a built-in shape as the app does:

1. Write a `symbol` element whose `symbol_reference` holds the library's `library_id` and the shape's
   `item_id` and `asset_version`, exactly as spelt in the file ([elements.md](elements.md#115-symbol)).
2. Give it a `frame`, for example at `default_width` by `default_height`.
3. Copy `connection_points` into the element.
4. If the shape has a `text_region`, write `text_layout` with that region as its `frame` and
   `text_position` (or `middle`) as its `position`. Otherwise leave `text_layout` out.
5. Optionally, as the app does, add an entry to `library_shape_names` with the same `library_id` and
   `item_id` and the shape's `slug`.

The element's other keys (style, text and so on) are written as for any element.

The file describes the values of 5.1.1. In 5.1.0, the 3D Basic (Hatch) and Word Bubbles shapes that
have a `text_region` in the file had none; a document that gives them the region opens in both
releases.

## Name-based UUIDs

The UUID of a published library, and of each of its shapes, is calculated from readable names, so one
name always gives one identity. The same method gives the built-in libraries' published UUIDs.

To compute the UUID for a name:

1. Form the string: `com.simplediagrams.published-library/` followed by the name in the table below.
2. Take the SHA-256 digest of its UTF-8 bytes and keep the first 16 bytes, numbered 0 to 15.
3. Set byte 6 to `(byte6 & 0x0F) | 0x80` and byte 8 to `(byte8 & 0x3F) | 0x80`.
4. Read the 16 bytes, in order, as a UUID. (Step 3 marks it as a version 8 UUID, RFC 9562.)

| Identity | Name appended in step 1 |
| --- | --- |
| A library | its qualified name, for example `com.simplediagrams.shapeLibrary.basic` |
| A shape | the library's qualified name, `/`, and the shape's slug |
| A shape-data field of a published library | the library's qualified name, `/field/`, and the field's key |
| A choice option of such a field | the library's qualified name, `/field/`, the field's key, `/choice/`, and the choice's key |

Slugs, field keys and choice keys are lower-case ASCII letters and digits, in runs joined by single
hyphens ([Library shape names](#library-shape-names)), so a derived name is never ambiguous. A field or
choice key is at most 64 bytes.

Test vectors:

| Name appended | UUID |
| --- | --- |
| `com.simplediagrams.shapeLibrary.basic` | `657227F7-7B3E-86B4-BB6F-79190E1592B3` |
| `com.simplediagrams.shapeLibrary.basic/square` | `43585F7D-3420-8BA8-BFF0-8962EA373A62` |
| `com.simplediagrams.shapeLibrary.basic/cylinder` | `D80A971E-FE62-8B0F-8714-8BECF88B1120` |
| `com.simplediagrams.shapeLibrary.exampleValves` | `3401CCC1-C395-8D98-B2AA-66C8E29B520E` |
| `com.simplediagrams.shapeLibrary.exampleValves/gate-valve` | `D946AE6C-A941-87D0-97F9-440184375CA6` |
| `com.simplediagrams.shapeLibrary.exampleValves/field/pressure-rating` | `AF218B98-5D4C-8664-92C3-0997529F4673` |
| `com.simplediagrams.shapeLibrary.exampleValves/field/status/choice/open` | `92A70936-5C42-8AA2-AA96-9CE645C4A88C` |

The first three are the published identities of the built-in Basic library and two of its shapes. The
rest use a made-up library, `exampleValves`, that is used again in the examples below.

A UUID is not proof of origin: anyone can compute one from a name. The derivation lets a name and a UUID
be checked against each other, nothing more.

## Letter case

| Comparison | Letter case | Asset version |
| --- | --- | --- |
| A reference to a built-in shape, against the app's own catalogue | **Exact**: `library_id` and `item_id` must match the built-in spelling, case included | Must be equal |
| A reference to a published or personal library's shape, against an installed library | Ignored | Must be equal |
| A built-in shape referenced by its published UUIDs | Ignored (UUIDs are compared as values) | Must be equal |
| `required_libraries` entries, against each other and against references | Ignored | Not involved |
| `library_shape_names` entries, against each other and against references and `source` values | Ignored | Ignored |
| A shape-data `source`, against a library shape | Ignored | Ignored |

A writer SHOULD spell a built-in `library_id` and `item_id` exactly as the app does. UUIDs MAY be written
in either case. **Informative.** The app writes a downloaded or personal library's UUIDs in a shape-data
`source` in lower case.

## What the app does when it opens a document

**Informative.** This section describes the app, not the format. A writer needs it to predict what a
person will see, and to know that the app may rewrite a document as it opens it.

**A reference the app can draw from its built-in libraries** is drawn with that artwork. The lookup is
exact: same library, same item, same asset version, same letter case. There is no "nearest version" and
no fallback to a shape with a similar name.

**A reference to a published or personal library that is installed on the Mac**, at the same asset
version, is turned into the element a person would have got by dragging that shape from the library,
before the document's window appears:

- Only a `symbol` the built-in libraries cannot draw is considered. It resolves only to installed
  artwork with the same library, shape and asset version, compared ignoring letter case.
- The element becomes the kind the library places (usually `editable_vector`; otherwise `svg_artwork` or
  `image`), with the artwork embedded and listed in `embedded_assets`. It takes the library's connection
  points, aspect-ratio rule and starting paint.
- It keeps its identifier, frame, rotation, flips, stacking order, text, lock and connectors. An
  `editable_vector` keeps a text layout it already has, and otherwise takes the library's. Shape data it
  already has is kept; a shape without any gets the library's fields.
- A connector end attached to a connection point the library's shape does not have is released where it
  was drawn.
- Locks do not prevent any of this.

**A built-in shape referenced by its published UUIDs** is rewritten to the app's own `library_id` and
`item_id`, when the app has that asset version. Only the reference changes.

After either change, the app treats the document as edited, and macOS saves it in place, in document
format 18, without the person choosing Save.

**A reference the app cannot draw** does not stop the document opening. The shape is drawn as a faint
box with a dashed outline and a question mark; its identifiers are not shown. A notice in the document
window explains why:

- for a shape of a library that ships inside SimpleDiagrams (a `library_id` that is not a UUID and
  begins `com.simplediagrams.`, or the published UUIDs of a built-in shape the app knows, at an asset
  version it does not have): "Some shapes in this diagram need a newer version of SimpleDiagrams.";
- for a shape of any other library, it names the library using the `display_name` from
  [`required_libraries`](#required-libraries), as plain text, and, when `library_id` is a UUID, offers a
  link to the library's page built from that UUID alone:
  `https://simplediagrams.com/shape-libraries/id/<library UUID in lower case>/`. A library not named in
  `required_libraries` is described without a name and without a link.

The reference is kept when the document is saved, so opening it on a Mac that has the shape shows it
again.

**`required_libraries` after resolution.** When the app resolves shapes from an installed library, it
removes that library's `required_libraries` entry once no reference to it remains. It never removes an
entry for a library it resolved nothing from, because a writer may name a library it did not use.

## Required libraries

`required_libraries` is an optional top-level key of `document.json`: an advisory list of the
libraries the document's writer says it needs, each with a name a person can recognize. Nothing is drawn,
placed or refused because of it. Its one use is to name a missing library to the person who opens the
document. A writer that places shapes from a published library by reference SHOULD include it, because
the app has no network access and cannot look up what a library identifier belongs to.

An array of:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `library_id` | string | Yes | 1 to 256 bytes of ASCII letters, digits, `.`, `-` and `_`, beginning and ending with a letter or digit | The identifier shapes use in `symbol_reference.library_id`: a published library's UUID, or a built-in library's name |
| `display_name` | string | Yes | Not blank: something remains after white space and line breaks are removed from both ends, where the zero-width space (U+200B) counts as white space but the joiners U+200C and U+200D do not; at most 200 scalars; no control characters, line or paragraph separators (U+2028, U+2029), or text-direction controls (U+061C, U+200E, U+200F, U+202A to U+202E, U+2066 to U+2069). Joiners used by emoji and some scripts are allowed | The library's name, for display only |
| `minimum_revision` | whole number | No | 1 to 1,000,000 | The lowest revision of the library that holds every shape the document uses, when the writer knows it |

A writer MUST meet these rules:

- At most 64 entries, and no library named twice (ignoring letter case).
- Every entry meets the table above.
- The list holds no address. **Informative.** Any link the app shows for a library is built from
  `library_id`, so a document cannot send its reader to a site of its choosing.

The list need not match the page: a library MAY be named and not used, or used and not named. When the
list would be empty, a writer SHOULD leave the key out; the app reads an empty list as no list.

**How the app reads it.** Leniently, so a faulty list never stops a document opening:

- a value that is not a list counts as no list;
- only the first 256 entries are examined;
- an entry with an unknown key, a missing key, a value of the wrong type, or a value out of bounds is
  left out;
- an entry naming a library already named is left out;
- the first 64 sound entries are kept.

**Informative.** The app carries the list unchanged through editing and saving, except for the trimming
described in [What the app does](#what-the-app-does-when-it-opens-a-document). It refuses to save a list
that breaks the rules, but it never makes one.

```json
{
  "required_libraries": [
    {"library_id": "3401CCC1-C395-8D98-B2AA-66C8E29B520E", "display_name": "Example Valves", "minimum_revision": 1}
  ]
}
```

## Library shape names

`library_shape_names` is an optional top-level key of `document.json`: one table giving each library
shape the document uses its readable name, its **slug**, and, where the shape's reference spells its
library only as a UUID, the library's qualified name. With it, a shape keeps its readable identity on a
Mac that lacks its library, and other programs can name it.

**A name never resolves anything.** Shapes are still found and drawn by their identifiers, in a
`symbol_reference` or a shape-data `source`. The worst a wrong name could do is mislabel a shape, and the
rules below stop even that where they can. One entry names every copy of a shape, whatever its asset
version.

An array of:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `library_id` | string | Yes | 1 to 256 bytes; no NUL | The library identifier exactly as the document's references spell it: a built-in library's name, or a UUID |
| `item_id` | string | Yes | 1 to 256 bytes; no NUL | The shape identifier exactly as the references spell it |
| `slug` | string | Yes | A slug (below) | The shape's readable name within its library |
| `library_readable_id` | string | No | Only when `library_id` is a UUID; MUST be that library's qualified name (below) | The library's qualified name |

A **slug** is one or more runs of lower-case ASCII letters (`a`–`z`) and digits (`0`–`9`), joined by
single hyphens, at most 128 bytes: `square`, `gate-valve`, `3d-cube`. It does not begin or end with a
hyphen. Each shape's slug is unique within its library and is given by the library.

`library_readable_id` MUST be one of:

- the library's **local name**, exactly `com.simplediagrams.local.` followed by the library's UUID
  (`library_id`) in lower case; or
- the library's **published name**, `com.simplediagrams.shapeLibrary.` followed by one or more ASCII
  letters and digits, at most 255 bytes in all, from which `library_id` is derived
  ([Name-based UUIDs](#name-based-uuids)). With a published name, `item_id` MUST also be a UUID, and it
  MUST be the UUID derived from that name and the entry's `slug`.

A writer MUST meet these rules:

- At most 10,000 entries.
- Each entry names a shape the document uses: some element's `symbol_reference`, or some shape-data
  definition's `source`, has the same `library_id` and `item_id` (ignoring letter case).
- No shape is named twice (ignoring letter case).
- Each entry meets the table above.

When there is no entry, a writer SHOULD leave the key out.

**How the app reads it.** Leniently, so a faulty table never stops a document opening:

- a value that is not a list counts as no list;
- only the first 10,000 entries are examined;
- an entry with an unknown key, a missing key, a value of the wrong type, an identifier outside 1 to 256
  bytes or holding NUL, or a `slug` that is not a slug, is left out;
- a `library_readable_id` that is neither the library's local name nor a published name that derives
  `library_id` is dropped from its entry, and the rest of the entry is kept;
- an entry with a published `library_readable_id`, whose `item_id` is not a UUID or is not the UUID its
  `slug` derives, is left out;
- an entry that names no shape the document uses, or a shape already named, is left out.

**Informative.** The app writes the table only for shapes in use, and only when there is at least one.

```json
{
  "library_shape_names": [
    {"library_id": "com.simplediagrams.shapeLibrary.basic", "item_id": "Square", "slug": "square"},
    {"library_id": "3401CCC1-C395-8D98-B2AA-66C8E29B520E", "item_id": "D946AE6C-A941-87D0-97F9-440184375CA6", "slug": "gate-valve", "library_readable_id": "com.simplediagrams.shapeLibrary.exampleValves"}
  ]
}
```

The second entry names a shape placed by reference to the made-up published library `exampleValves`:

```json
{"library_id": "3401CCC1-C395-8D98-B2AA-66C8E29B520E", "item_id": "D946AE6C-A941-87D0-97F9-440184375CA6", "asset_version": 1}
```

## Open points

None.
