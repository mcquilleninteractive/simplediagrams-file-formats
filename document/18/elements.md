# Elements

This page specifies the **elements** of document format 18: every shape on a page, the keys each one
carries, and the rules the app checks when it opens a document. Read [README.md](README.md) first for
the conventions this page relies on (colours, identifiers, diagram points, page coordinates, how JSON
is read) and for the page record that holds the elements.

An **element** is one shape on a page: a rectangle, a shape from a library, a picture, a freehand
stroke, a container and so on. Connectors are not elements; they are described in
[connections.md](connections.md), and groups in the same file.

## Contents

1. [Element kinds](#1-element-kinds)
2. [The element record](#2-the-element-record)
3. [Identity, frame, rotation and flips](#3-identity-frame-rotation-and-flips)
4. [Stacking](#4-stacking)
5. [Style](#5-style)
6. [Text content](#6-text-content)
7. [Text layout](#7-text-layout)
8. [Connection points](#8-connection-points)
9. [Locking and aspect ratio](#9-locking-and-aspect-ratio)
10. [Which kind may carry which key](#10-which-kind-may-carry-which-key)
11. [Each kind](#11-each-kind)
12. [Complex shapes](#12-complex-shapes)
13. [Enumerated values](#13-enumerated-values)
14. [Reading older document formats](#14-reading-older-document-formats)
15. [Open points](#15-open-points)

## Terms used on this page

| Term | Meaning |
| --- | --- |
| **Diagram point** | The unit of every position and size. On printed or exported output one diagram point is 1/72 inch |
| **Page coordinates** | Positions on a page. The origin is the top-left; x grows to the right and y grows **downwards**. Coordinates may be negative |
| **Frame** | The rectangle an element occupies *before* rotation: `x`, `y` (its top-left corner in page coordinates), `width`, `height` |
| **Fraction of the frame** | A number where 0 is the frame's left (or top) edge and 1 its right (or bottom) edge. Connection points and text regions are stored this way, so they keep their place when a shape is resized |
| **Degrees** | Angles are in degrees, **clockwise** as seen on screen (because y grows downwards) |
| **Colour** | A string `#RRGGBB` or `#RRGGBBAA` (alpha last), hexadecimal digits in either letter case. `#00000000` is fully transparent. Three-digit colours, colour names and `none` are refused |
| **UUID** | An identifier written as 8-4-4-4-12 hexadecimal digits. The app writes upper case and reads either case |
| **Whole number** | A JSON number with no fractional part. Writers MUST write it without a decimal point, fraction or exponent ([README.md](README.md#numbers)) |
| **Text content** | The rich-text record of [section 6](#6-text-content) |

Messages the app shows when it refuses a document are quoted below in English, as examples only. They
are translated, and programs MUST NOT match on their text (see [README.md](README.md)).

## 1. Element kinds

Every element has a `kind`. There are exactly eleven kinds, and an unknown `kind` makes the app
refuse the document.

| `kind` | What it is | Details |
| --- | --- | --- |
| `rectangle` | A rounded rectangle drawn by the app | [11.1](#111-rectangle) |
| `ellipse` | The ellipse that fits the frame, drawn by the app | [11.2](#112-ellipse) |
| `note` | A sticky note or an index card drawn by the app | [11.3](#113-note) |
| `text` | Text on its own, with no shape behind it | [11.4](#114-text) |
| `symbol` | A library shape placed by reference: the document names the shape, not its artwork | [11.5](#115-symbol) |
| `editable_vector` | A shape whose artwork is stored in the document as native vector artwork (a `.sdvector` asset) | [11.6](#116-editable_vector) |
| `svg_artwork` | An SVG stored in the document and drawn as its author made it | [11.7](#117-svg_artwork) |
| `image` | A PNG or JPEG picture stored in the document | [11.8](#118-image) |
| `path` | A freehand or point-by-point line, or a closed outline | [11.9](#119-path) |
| `unresolved` | A placeholder for content from an imported legacy file that has no native equivalent | [11.10](#1110-unresolved) |
| `complex_shape` | A container, swimlanes, a table, a callout or a UML lifeline, drawn by the app from a few stored values | [12](#12-complex-shapes) |

An element never contains another element. Containers and swimlanes *refer* to other elements by
identifier ([section 12](#12-complex-shapes)); they do not nest them.

## 2. The element record

### 2.1 How the record nests on disk

An element is a JSON object in its page's `elements` array (see [README.md](README.md) for the page
record). The keys an element carries are one set, but on disk they sit at **four levels**: the outer
object holds a `content` object, which holds another `content` object, which holds a third. This
nesting is real JSON nesting, and a writer MUST put each key at its level. It carries no meaning of
its own.

```text
element                                   level 0 (outer object)
├─ shape_data_definition_id               required key; null or a UUID
├─ shape_data_values                      required
├─ complex_shape                          complex_shape elements only
└─ content                                level 1
   ├─ flip_horizontal, flip_vertical      required
   ├─ text_layout                         optional
   ├─ note_style                          optional; note only
   ├─ preserves_aspect_ratio              optional
   ├─ shows_image_background              optional; image only
   ├─ shows_image_border                  optional; image only
   └─ content                             level 2
      ├─ is_locked                        required
      └─ content                          level 3
         ├─ id, kind                      required
         ├─ frame, rotation_degrees       required
         ├─ z_index                       required
         ├─ text_content                  required
         ├─ style                         required
         ├─ connection_points             required
         ├─ symbol_reference              symbol only
         ├─ embedded_asset_id             editable_vector, svg_artwork, image only
         ├─ image_content_mode            image only
         ├─ path                          path only
         └─ unresolved_content            unresolved only
```

**Unknown keys.** The app refuses a document that has an unknown key on the **outer object** (level
0), in a `complex_shape` record, in a swimlanes lane, or in a callout's `tail_attachment`. Everywhere
else in an element (levels 1 to 3, and the style, text content, text layout, path, path node, symbol
reference, connection point and member records inside them) an unknown key is ignored and is gone the
next time the app saves the document. A misspelt optional key at those levels is therefore silently
lost, not refused. Writers MUST NOT write keys this specification does not define.

**Absent and `null`.** For an optional key, JSON `null` is read as if the key were absent. Writers
MUST leave an optional key out rather than write `null`. One key is different:
`shape_data_definition_id` MUST be present on every element, and is `null` for an element with no
shape data.

### 2.2 Every key

The table lists every key of an element, with the level it sits at. Section numbers point to the
detailed rules.

| Key | Level | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- | --- |
| `shape_data_definition_id` | 0 | UUID or `null` | Yes, the key MUST be present | `null`, or the `id` of an entry in the document's `shape_data_definitions` | Which set of shape data fields this element carries ([shape-data.md](shape-data.md)) |
| `shape_data_values` | 0 | array | Yes | At most 128 entries; `[]` when `shape_data_definition_id` is `null`; MUST NOT be `null` | This element's shape data values ([shape-data.md](shape-data.md)) |
| `complex_shape` | 0 | object | If `kind` is `complex_shape`; forbidden otherwise | [Section 12](#12-complex-shapes) | Which complex shape this is and how it is set up |
| `content` | 0 | object | Yes | | Level 1 |
| `flip_horizontal` | 1 | boolean | Yes | See [section 3](#3-identity-frame-rotation-and-flips) | Mirror the artwork left to right |
| `flip_vertical` | 1 | boolean | Yes | See [section 3](#3-identity-frame-rotation-and-flips) | Mirror the artwork top to bottom |
| `text_layout` | 1 | object | No | Forbidden on `path`, `unresolved`, `svg_artwork`, `image`, `complex_shape` | Where the label sits ([section 7](#7-text-layout)). Absent: the default inset region, label centred |
| `note_style` | 1 | string | No | `stickyNote` or `indexCard`; `note` only | Which note appearance. Absent: sticky note |
| `preserves_aspect_ratio` | 1 | boolean | No | Default `false` | Keep proportions when a person resizes the element ([section 9](#9-locking-and-aspect-ratio)) |
| `shows_image_background` | 1 | boolean | No | Default `false`; `image` only | Fill the frame behind the picture |
| `shows_image_border` | 1 | boolean | No | Default `false`; `image` only | Outline the frame |
| `content` | 1 | object | Yes | | Level 2 |
| `is_locked` | 2 | boolean | Yes | | A person locked this element ([section 9](#9-locking-and-aspect-ratio)) |
| `content` | 2 | object | Yes | | Level 3 |
| `id` | 3 | UUID | Yes | Unique in the whole document (see [README.md](README.md) for identity rules) | The element's permanent identity. Connectors, groups, containers and the drawing order refer to it |
| `kind` | 3 | string | Yes | One of the eleven kinds | What the element is |
| `frame` | 3 | rectangle | Yes | `x`, `y` finite, within ±1,000,000,000; `width`, `height` greater than 0 and at most 10,000,000 | Where the element is and how big, before rotation |
| `rotation_degrees` | 3 | number, degrees | Yes | Finite, within ±1,000,000,000 | Clockwise turn about the frame's centre |
| `z_index` | 3 | whole number | Yes | A 64-bit signed integer | Stacking depth under the older stacking rule ([section 4](#4-stacking)) |
| `text_content` | 3 | text content | Yes | [Section 6](#6-text-content) | The element's text (its label), required even when empty |
| `style` | 3 | object | Yes | [Section 5](#5-style) | Fill and line |
| `connection_points` | 3 | array | Yes | 0 to 128 entries | Where connector ends can attach ([section 8](#8-connection-points)). `[]` is valid |
| `symbol_reference` | 3 | object | If `kind` is `symbol`; forbidden otherwise | [11.5](#115-symbol) | Which library shape |
| `embedded_asset_id` | 3 | string | If `kind` is `editable_vector`, `svg_artwork` or `image`; forbidden otherwise | Names an embedded asset of the matching kind | Which file in `assets/` holds the artwork |
| `image_content_mode` | 3 | string | If `kind` is `image`; forbidden otherwise | `fit`, `fill`, `stretch` | How the picture fills the frame |
| `path` | 3 | object | If `kind` is `path`; forbidden otherwise | [11.9](#119-path) | The line's points |
| `unresolved_content` | 3 | object | If `kind` is `unresolved`; forbidden otherwise | [11.10](#1110-unresolved) | The retained legacy fragment |

A **rectangle** in this format is an object with four required numbers, `x`, `y`, `width`, `height`.
A **point** is an object with two required numbers, `x` and `y`.

### 2.3 An element as it sits on disk

A plain rectangle with a one-word label, as the app writes it. (The app writes compact JSON with keys
sorted; layout and key order do not matter to a reader.)

```json
{
  "content": {
    "content": {
      "content": {
        "connection_points": [
          {"id": 0, "x": 0, "y": 0.5}, {"id": 1, "x": 1, "y": 0.5},
          {"id": 2, "x": 0.5, "y": 0}, {"id": 3, "x": 0.5, "y": 1},
          {"id": 4, "x": 0, "y": 0}, {"id": 5, "x": 0, "y": 1},
          {"id": 6, "x": 1, "y": 0}, {"id": 7, "x": 1, "y": 1}
        ],
        "frame": {"x": 100, "y": 80, "width": 160, "height": 80},
        "id": "1D9B6C0E-3F2A-4B7C-9E1D-2A4C6E8F0B13",
        "kind": "rectangle",
        "rotation_degrees": 0,
        "style": {"fill_hex": "#D8ECFF", "line_width": 2, "stroke_hex": "#343A40"},
        "text_content": {
          "base_alignment": "center",
          "base_style": {
            "color_hex": "#212529", "font_size": 15, "is_italic": false,
            "is_underlined": false, "weight": "medium"
          },
          "character_runs": [],
          "paragraph_runs": [],
          "string": "Start"
        },
        "z_index": 1
      },
      "is_locked": false
    },
    "flip_horizontal": false,
    "flip_vertical": false
  },
  "shape_data_definition_id": null,
  "shape_data_values": []
}
```

## 3. Identity, frame, rotation and flips

**Identity.** `id` is the element's permanent identity. It MUST be unique across the whole document:
it may not equal the identifier of the document, any page, Quick Style, guide, other element,
connector or group. Otherwise the app refuses the document ("The diagram repeats identifier …").

**Frame.** `frame.x` and `frame.y` are the frame's top-left corner in page coordinates; `width` and
`height` extend right and down. Both sizes MUST be greater than zero, so a perfectly thin element
cannot exist. The app stretches a shape's artwork to whatever frame the element has; it does not
size library shapes for the writer.

| Field | Bounds | Refusal example |
| --- | --- | --- |
| `frame.x`, `frame.y` | Finite, within ±1,000,000,000 | "The diagram contains an invalid number in element x." |
| `frame.width`, `frame.height` | Greater than 0, at most 10,000,000 | "…invalid number in element width." |
| `rotation_degrees` | Finite, within ±1,000,000,000 | "…invalid number in element rotation." |

**Rotation.** The stored frame is always the *unrotated* rectangle. The element turns clockwise by
`rotation_degrees` about the centre of its frame. For a point at offset `(dx, dy)` from the centre,
the turned offset is:

```text
a   = rotation_degrees × π / 180
dx' = dx · cos(a) − dy · sin(a)
dy' = dx · sin(a) + dy · cos(a)
```

With y growing downwards this is a clockwise turn. The value need not lie between 0 and 360. (Other
angles in the format, such as a label's own turn, MUST be at least 0 and less than 360.)

**Flips.** `flip_horizontal` and `flip_vertical` mirror the element's *artwork* about the centre of
its frame, inside the rotation: the artwork is mirrored first, then turned. Flips never mirror the
text: the label is drawn upright and readable. Connection points keep their numbers and stored
positions, and are drawn mirrored with the artwork ([section 8](#8-connection-points)). A `path`
element's nodes are drawn mirrored too.

| Kind | Effect of the flips |
| --- | --- |
| `text`, `unresolved` | No artwork is mirrored. Connection points are still placed mirrored, as on any element. Writers SHOULD write `false` |
| `complex_shape` | Only a callout may be flipped. On any other complex shape both MUST be `false` ([section 12](#12-complex-shapes)) |
| Every other kind | The artwork is mirrored |

## 4. Stacking

`z_index` orders elements front to back under the page's older stacking rule: when the page has no
explicit drawing order (`drawableOrder`), all connectors are drawn first, behind every element, and
then elements in ascending `z_index`; two elements with the same `z_index` are ordered by their
identifiers compared as upper-case text. When the page has an explicit drawing order, that list alone
decides and `z_index` is not used for drawing, although every element MUST still carry it. The page
record and the drawing order are specified in [README.md](README.md).

`z_index` values need not be unique or consecutive. The app gives a new element one more than the
highest `z_index` on its page. The format allows any 64-bit signed integer; writers SHOULD keep values
within ±9,007,199,254,740,991 (2⁵³ − 1) so that readers using double-precision numbers keep them
exact.

The order of elements in the page's `elements` array does not affect drawing.

**Informative.** Containers and swimlanes are always drawn just behind the lowest thing they hold,
whatever the stored order says ([12.10](#1210-rules-across-the-page)).

## 5. Style

Every element has a `style` record: fill colour, line colour and line weight. Opacity has no key of its
own; it is the alpha part of each colour.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `fill_hex` | colour | Yes | A colour | Fill colour, and through its alpha the fill's opacity |
| `stroke_hex` | colour | Yes | A colour | Line colour and opacity |
| `line_width` | number, diagram points | Yes | Finite, 0 to 10,000 | Line weight. `0` draws no line |
| `fill_texture` | string | No | A texture name ([13.10](#1310-textures)) | A texture, tinted with the fill colour, drawn in place of the flat fill |
| `stroke_texture` | string | No | A texture name | The same for the line |
| `stroke_pattern_override` | string | No | `solid`, `dashed`, `dotted` | A line pattern for a library shape's artwork, replacing the artwork's own |

The keys `fill_hex`, `stroke_hex` and `line_width` are required on every kind, even where they are
not drawn. Refusals: "The diagram contains an invalid color value: …", "…invalid number in element
line width." An unknown texture name or `stroke_pattern_override` value makes the app refuse the
document with the general message for an invalid `document.json`.

How the values behave:

- **Opacity.** `#D8ECFF` is opaque; `#D8ECFF80` is the same colour at about half opacity;
  `#00000000` is fully transparent and is how "no fill" or "no line" is written. The colour digits
  are kept when the alpha is `00`, so a reader MUST preserve them.
- **Line weight does not scale.** `line_width` is always in diagram points. Resizing an element does
  not thicken or thin its line.
- **Textures** are a fixed set shipped with the app, named by the identifiers in
  [13.10](#1310-textures). They are tiled in page coordinates and clipped to the shape, so resizing
  a shape does not stretch them.
- **`stroke_pattern_override`** applies only to `symbol` and `editable_vector` elements and to the
  drawing a container wears (`artwork_asset_id`, [12.5](#125-container)), and within those only to
  the parts of the artwork that follow the element's line colour. A `path` has its own pattern
  (`path.stroke_pattern`). On every other kind the key is accepted and ignored.

**Informative: which style values each kind uses.**

| Kind | `fill_hex` | `stroke_hex`, `line_width` | Textures | `stroke_pattern_override` |
| --- | --- | --- | --- | --- |
| `rectangle`, `ellipse`, `note` | Fills the shape | Outline (always solid) | Used | Ignored |
| `text` | Not drawn | Not drawn | Not drawn | Ignored |
| `symbol`, `editable_vector` | Colours the parts of the artwork that follow the fill | Colour and weight of the parts that follow the line | Used on those parts | Used |
| `svg_artwork` | Not used: the SVG keeps its own paint | Not used | Not used | Ignored |
| `image` | The panel behind the picture, when `shows_image_background` is `true` | The border, when `shows_image_border` is `true` | Used for those | Ignored |
| `path` | Fills a closed path | The line | Used (a stroke texture turns marker ink off) | Ignored |
| `unresolved` | Fills the placeholder box | Its dashed outline | Not used | Ignored |
| `complex_shape` | Its body, and a table's header row | Its outline, lane and cell lines, a lifeline's dashed line | Used | Ignored, except on a container that wears a drawing, where it applies to the drawing's line-coloured parts |

A container that wears a drawing paints it as an `editable_vector` is painted: the drawing's fill- and
line-coloured parts take the container's `fill_hex`, `stroke_hex` and `line_width`.

**The app's starting values.** These are the styles the app gives a new element of each kind. They are
a good choice for a writer with no other preference.

| Kind | `fill_hex` | `stroke_hex` | `line_width` |
| --- | --- | --- | --- |
| `rectangle` | `#D8ECFF` | `#343A40` | 2 |
| `ellipse` | `#DDF5E3` | `#343A40` | 2 |
| `note` | `#FFF1A8` | `#8A7636` | 2 |
| `text` | `#00000000` | `#00000000` | 0 |
| `symbol`, `editable_vector` | `#FFFFFF` | `#000000` | 1 |
| `complex_shape` (all five types) | `#FFFFFF` | `#000000` | 1 |
| `svg_artwork`, `image` | `#00000000` | `#00000000` | 0 |
| `path` | `#00000000` | `#343A40` | 2 |
| `unresolved` | `#F2F2F2` | `#8E8E93` | 2 |

Library shapes and complex shapes start with a one-point line because library artwork is drawn for
it; two points clogs small lettering and hatching. A shape placed from a library MAY start with paint
the library gives it instead (see [libraries.md](libraries.md)).

## 6. Text content

Every element owns one piece of rich text in `text_content`: a rectangle's label, a `text` element's
words, a container's title. The same record is used for a connector's label
([connections.md](connections.md)), a swimlanes lane's title and a table cell
([section 12](#12-complex-shapes)).

The record is a closed list of formatting. There are no links, attachments, pictures, HTML or RTF, and
no way to add them.

### 6.1 The text content record

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `string` | string | Yes | At most 1,000,000 bytes of UTF-8; no NUL character. MAY be empty | The text. Line breaks are ordinary newline characters |
| `base_style` | character style | Yes | [6.2](#62-character-style) | The appearance of all text not covered by a character run |
| `character_runs` | array of character runs | Yes | [6.3](#63-runs-and-ranges) | Stretches of text with a different character style. `[]` for plain text |
| `base_alignment` | string | Yes | `leading`, `center`, `trailing`, `justified` | Alignment of every paragraph not covered by a paragraph run. `leading` is drawn left-aligned and `trailing` right-aligned |
| `paragraph_runs` | array of paragraph runs | Yes | [6.3](#63-runs-and-ranges) | Stretches of text with a different alignment. Usually `[]` |
| `background_hex` | colour | No | A colour | A filled rectangle behind the text block. Absent: nothing behind the text |

Character runs and paragraph runs together MUST number at most 100,000 in one text content ("The
diagram contains too many text-formatting runs (*n*).").

### 6.2 Character style

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `font_family` | string | No | At most 256 bytes of UTF-8; no NUL; not empty or only white space; no control or format characters | A font name. Absent: the system font. If the Mac has no font of that name, the system font is drawn and the name is kept |
| `font_size` | number, diagram points | Yes | Finite, 1 to 720 | Font size. It does not change when the element is resized |
| `weight` | string | Yes | `regular`, `medium`, `bold` | Font weight |
| `is_italic` | boolean | Yes | | Italic |
| `is_underlined` | boolean | Yes | | Underline |
| `color_hex` | colour | Yes | A colour | Text colour |

The app's starting text is font size 15, weight `medium`, colour `#212529`, not italic, not
underlined, no `font_family`, and `base_alignment` `center`. An unknown `weight` or alignment makes the
app refuse the document with the general message for an invalid `document.json`.

### 6.3 Runs and ranges

A **character run** is an object `{"range": …, "style": …}`, where `style` is a *complete* character
style: every required key, not only those that differ from `base_style`. A **paragraph run** is an
object `{"range": …, "alignment": …}`, with `alignment` one of the four alignment values.

A **range**:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `location` | whole number | Yes | 0 or more | Where the run starts, counted in **UTF-16 code units** from the start of `string` |
| `length` | whole number | Yes | Greater than 0 | How many UTF-16 code units the run covers |

Ranges are counted in UTF-16 code units even though the file is UTF-8: a character outside the Basic
Multilingual Plane, such as most emoji, counts as two units.

The rules, checked separately for the character runs and for the paragraph runs of one text content:

1. Runs are in ascending order and do not overlap: each starts at or after the end of the one before.
2. Each run has a `length` greater than 0 and ends at or before the end of `string`.
3. Both ends of every run fall on a **grapheme cluster boundary**: between two characters as a person
   sees them (an extended grapheme cluster in the sense of Unicode Standard Annex #29). A run MUST NOT
   start or end inside a surrogate pair, inside an emoji sequence, between a base letter and a
   combining mark, or between the two characters of `\r\n`.

A broken rule makes the app refuse the document ("The diagram contains invalid text in character
run.", or "…paragraph run.").

**Example.** In the string `Go 👍🏽 now`, `G`, `o` and the space are units 0 to 2; the thumbs-up with
its skin-tone modifier is one grapheme cluster of four units (3 to 6); the space is unit 7 and `now`
is units 8 to 10. A run `{"location": 8, "length": 3}` covers `now`. A run starting at 3 and of length
4 covers the emoji. A run starting at 4 (inside the surrogate pair) or at 5 (between the thumbs-up and
its modifier) is refused.

A writer that cannot find grapheme cluster boundaries reliably SHOULD leave both run arrays empty,
which is always valid, or use runs only on text where every character is a single ASCII unit.

### 6.4 Which kinds show their text

Every element's text is drawn when `string` is not empty, whatever its kind; flips never mirror it. A
table's own `string` MUST be empty, because its text is in its cells ([12.7](#127-table)). On
`path`, `unresolved`, `svg_artwork` and `image` the label always sits in the default place, because
those kinds may not have a `text_layout`. A complex shape places its own text where its type puts it.

## 7. Text layout

`text_layout` says where an element's label sits. The label is laid out in a **text region**, a
rectangle given in fractions of the element's frame; the text wraps to the region's width, and
`position` decides where it sits vertically.

`text_layout` is allowed only on `rectangle`, `ellipse`, `note`, `text`, `symbol` and
`editable_vector`. On any other kind the app refuses the document ("The diagram contains invalid text
in unexpected element text layout.").

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `frame` | rectangle, fractions of the element's frame | Yes | All four finite. `width` and `height` from 0.001 to 8. `x` from −`width` to 1 and `y` from −`height` to 1 | The text region |
| `position` | string | Yes | `above`, `top`, `middle`, `bottom`, `below` | Where the text block sits vertically |
| `rotation_degrees` | number, degrees | No | Default `0`. Finite, at least 0 and less than 360 | The label's turn *relative to its element* |

A layout value out of range, or an unknown `position`, makes the app refuse the document with the
general message for an invalid `document.json`.

**The region.** For an element of size `W` by `H`, measured from the frame's top-left corner and
before rotation:

```text
region left  = frame.x × W        region width  = frame.width  × W
region top   = frame.y × H        region height = frame.height × H
```

The region may extend past the element on any side, so long as some part of it still touches the
element: that is what the bounds on `x` and `y` express. A region may be up to eight times as wide
and as tall as the element. The region scales with the element; the font size does not.

**When `text_layout` is absent**, the region is the frame inset by 10 points on the left and right and
8 points at the top and bottom (each inset reduced so the region keeps at least one point of width or
height on a very small element), `position` is `middle`, and the label is not turned.

**Position.**

| `position` | Where the text block goes |
| --- | --- |
| `top` | Its top edge on the region's top edge |
| `middle` | Centred vertically in the region |
| `bottom` | Its bottom edge on the region's bottom edge |
| `above` | Wholly above the element: its bottom edge 5 points above the frame's top edge |
| `below` | Wholly below the element: its top edge 5 points below the frame's bottom edge |

For `above` and `below`, the region's `y` and `height` play no part, but its `x` and `width` still
decide where the text sits horizontally and the width it wraps to. A caption wider than its shape and
centred under it is a region with a negative `x` and a `width` greater than 1, for example
`{"x": -0.5, "y": 1, "width": 2, "height": 0.4}` with `position` `below`.

Text is not clipped to its region: a label taller than its region overflows it.

**Rotation.** The label turns with its element. The layout's `rotation_degrees` is an extra turn on
top of the element's, about the centre of the text region: the label is drawn at the element's
`rotation_degrees` plus the layout's.

**Informative.** How text is measured and wrapped depends on the fonts of the Mac that draws it. A
reader on another platform may place lines slightly differently.

## 8. Connection points

A **connection point** is a numbered place on an element where a connector end, or a callout's tail,
can be attached. A connector stores the point's *number*, not its position, so a point can move and
the connector follows. How connectors attach is specified in [connections.md](connections.md).

Each entry of `connection_points`:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | whole number | Yes | 0 or more; unique within this element | The point's number. Connectors refer to it |
| `x` | number, fraction of the frame | Yes | Finite, 0 to 1 | 0 is the frame's left edge, 1 its right edge |
| `y` | number, fraction of the frame | Yes | Finite, 0 to 1 | 0 is the frame's top edge, 1 its bottom edge |

`x` and `y` sit directly in the entry, not in a nested object.

An element MUST have at most 128 connection points. Refusals: "The diagram contains an invalid number in
element connection point count." (more than 128), "…element connection point identifier." (a negative
number), "…duplicate connection point identifier.", "…element connection point." (a position outside 0
to 1).

**Where a point is drawn.** Mirror with the flips, scale by the frame, then turn with the element:

```text
fx = flip_horizontal ? 1 − x : x
fy = flip_vertical   ? 1 − y : y
px = frame.x + fx × frame.width
py = frame.y + fy × frame.height
then turn (px, py) about the frame's centre by rotation_degrees (section 3)
```

`x` and `y` are always the point's place on the *unflipped* element. Flipping an element never changes
a point's stored position or number.

**The standard eight.** The app gives every new element, except a `path` and a lifeline, these points:

| `id` | Place | `x` | `y` |
| --- | --- | --- | --- |
| 0 | Middle of the left edge | 0 | 0.5 |
| 1 | Middle of the right edge | 1 | 0.5 |
| 2 | Middle of the top edge | 0.5 | 0 |
| 3 | Middle of the bottom edge | 0.5 | 1 |
| 4 | Top-left corner | 0 | 0 |
| 5 | Bottom-left corner | 0 | 1 |
| 6 | Top-right corner | 1 | 0 |
| 7 | Bottom-right corner | 1 | 1 |

A `path` made by the app has no points. A lifeline made by the app has point `2` alone; connectors
attach along its line instead ([12.9](#129-lifeline)). A library shape MAY define its own points
(see [libraries.md](libraries.md)).

**Other points.** An element MAY have any points, with any numbers, anywhere on or inside its frame.
The numbers need not start at 0 or be consecutive. An empty list is valid and means no connector can
attach to the element.

A point has no stored direction. **Informative:** a right-angle connector leaves a point square to the
side of the frame the point is nearest to.

## 9. Locking and aspect ratio

**`is_locked`** records that a person locked this element. A locked element cannot be moved, resized,
restyled or deleted in the app until it is unlocked. Only the element's own lock is stored. Whether an
element is *effectively* locked also depends on the groups it is in (see
[connections.md](connections.md) for groups); that is worked out when needed and never written into
the element. A writer MUST NOT set `is_locked` to record a group's lock.

**`preserves_aspect_ratio`**, when `true`, makes the app keep the element's proportions when a person
resizes it, as if Shift were held. It does not require the frame to have any particular proportion. A
library shape MAY ask for it; the app sets it from the library when the shape is placed.

## 10. Which kind may carry which key

`R` = required, `F` = forbidden (the app refuses the document), `–` = optional.

| Key | `rectangle` | `ellipse` | `note` | `text` | `symbol` | `editable_vector` | `svg_artwork` | `image` | `path` | `unresolved` | `complex_shape` |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `complex_shape` (level 0) | F | F | F | F | F | F | F | F | F | F | R |
| `text_layout` | – | – | – | – | – | – | F | F | F | F | F |
| `note_style` | F | F | – | F | F | F | F | F | F | F | F |
| `shows_image_background`, `shows_image_border` | F | F | F | F | F | F | F | – | F | F | F |
| `symbol_reference` | F | F | F | F | R | F | F | F | F | F | F |
| `embedded_asset_id` | F | F | F | F | F | R | R | R | F | F | F |
| `image_content_mode` | F | F | F | F | F | F | F | R | F | F | F |
| `path` | F | F | F | F | F | F | F | F | R | F | F |
| `unresolved_content` | F | F | F | F | F | F | F | F | F | R | F |
| `flip_horizontal` or `flip_vertical` set to `true` | – | – | – | – | – | – | – | – | – | – | Callout only; F for the other types |
| `text_content.string` not empty | – | – | – | – | – | – | – | – | – | – | F for a table |

Every other key of [2.2](#22-every-key) is required on every kind.

Example messages. A forbidden key: "…invalid text in unexpected symbol reference.", "…unexpected
embedded asset reference.", "…unexpected image properties.", "…unexpected note style.", "…unexpected
unresolved element source.", "…invalid number in unexpected element path.", "…unexpected element text
layout.". A missing required key: "…invalid text in symbol reference.", "…embedded asset reference.",
"…image properties.", "…invalid number in path node count.", "…invalid text in unresolved element
source.". A `complex_shape` record on another kind, a `complex_shape` element without one, a flipped
complex shape other than a callout, or a table with its own text: "Shape … holds a container,
swimlanes, table, callout or lifeline record that is not valid."

Some of these faults are found while the JSON is read, and give only the general message for an
invalid `document.json`: `shows_image_background` or `shows_image_border` on a kind other than
`image`, and any unknown enumerated value ([section 13](#13-enumerated-values)).

## 11. Each kind

### 11.1 `rectangle`

A rectangle filled with `fill_hex` (or `fill_texture`) and outlined with `stroke_hex` at `line_width`.
It needs no key beyond the common ones. **Informative:** the app rounds its corners to a fixed radius of
8 points, which is not stored and cannot be changed.

### 11.2 `ellipse`

The ellipse that fits the frame, painted like a rectangle. It needs no key beyond the common ones.

### 11.3 `note`

An annotation with one of two looks, chosen by `note_style`:

| `note_style` | Look |
| --- | --- |
| Absent, or `stickyNote` | A sticky note |
| `indexCard` | An index card with ruled lines |

`note_style` on any other kind is refused. **Informative:** the app draws a sticky note as a
rectangle with corners rounded to 3 points, and an index card as a square-cornered card with a rule
every 20 points from the top, the first one in a fixed red (`#CC5B4F`, one point wide) and the rest in
the note's line colour and weight.

### 11.4 `text`

Text alone: nothing is drawn but `text_content`. `style` is still required; the app writes a
transparent fill, a transparent line and a `line_width` of 0. The frame is the text's area. A `text`
element MAY have a `text_layout` and connection points like any other shape.

### 11.5 `symbol`

A shape from a library, placed **by reference**: the document stores which shape it is, not its
artwork. The app draws its built-in libraries' shapes this way. How a reference is resolved, which
libraries the app ships, and how a writer may refer to a published library's shape are specified in
[libraries.md](libraries.md).

`symbol_reference`:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `library_id` | string | Yes | 1 to 256 bytes of UTF-8; no NUL | The library's identifier, for example `com.simplediagrams.shapeLibrary.basic` |
| `item_id` | string | Yes | 1 to 256 bytes of UTF-8; no NUL | The shape's identifier within that library. Case-sensitive |
| `asset_version` | whole number | Yes | Greater than 0 | Which version of the shape's artwork |

Refusals: an identifier longer than 256 bytes or holding NUL gives "…invalid text in symbol library
identifier." (or "…symbol item identifier."); an empty identifier, or an `asset_version` of 0 or less,
gives "…invalid text in symbol reference.".

A reference the app cannot draw does **not** make the document invalid. The app opens the document,
draws the shape as a placeholder box and keeps the reference unchanged when it saves.

The reference supplies the artwork and nothing else. The element MUST carry everything else itself:
its frame, style, connection points and any text layout. The artwork is stretched to the frame. Its
parts that follow the fill or the line take the element's `fill_hex`, `stroke_hex` and `line_width`.

### 11.6 `editable_vector`

A shape whose artwork is stored in the document as native vector artwork: a `.sdvector` file in
`assets/`. It is what a shape from a downloaded or personal library becomes when placed, and what an
SVG becomes when imported as an editable shape.

`embedded_asset_id` MUST name an embedded asset of kind `editable_vector`. Many elements MAY name the
same asset; the artwork is stored once. The artwork is stretched to the frame, and its parts that
follow the fill or the line take the element's style; parts with their own fixed paint are unaffected.
The vector artwork format, how it fits the frame, and the parts that keep their size are specified in
[assets.md](assets.md).

The element does not record which library it came from. If it carries shape data, the data's
definition may (see [shape-data.md](shape-data.md)).

### 11.7 `svg_artwork`

An SVG stored in the document and drawn as its author made it, stretched to the frame.
`embedded_asset_id` MUST name an embedded asset of kind `svg_artwork`. The element's `style` is not
applied to the artwork, and the element may not have a `text_layout`. The SVG must pass the app's
safety rules to be stored at all; see [assets.md](assets.md).

### 11.8 `image`

A PNG or JPEG picture. `embedded_asset_id` MUST name an embedded asset of kind `raster_png` or
`raster_jpeg` (see [assets.md](assets.md)). An `image` may not have a `text_layout`.

| Key | Level | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- | --- |
| `image_content_mode` | 3 | string | Yes | `fit`, `fill`, `stretch` | How the picture fills the frame |
| `shows_image_background` | 1 | boolean | No | Default `false` | Fill the frame behind the picture with `style.fill_hex` |
| `shows_image_border` | 1 | boolean | No | Default `false` | Outline the frame with `style.stroke_hex` at `style.line_width` |

| `image_content_mode` | Result |
| --- | --- |
| `fit` | The whole picture, as large as fits, centred; the frame may show empty bands |
| `fill` | The picture covers the frame, centred; what overflows is clipped to the frame |
| `stretch` | The picture is distorted to fill the frame exactly |

### 11.9 `path`

A line or closed outline made of points: a pencil stroke, a marker stroke, or a shape drawn point by
point.

`path`:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `nodes` | array of nodes | Yes | At least 2; at least 3 when `is_closed` is `true` | The line's points, in order |
| `is_closed` | boolean | Yes | | Join the last node back to the first, and fill the outline |
| `is_smoothed` | boolean | Yes | | Draw as a pencil curve (below) |
| `stroke_pattern` | string | Yes | `solid`, `dashed`, `dotted` | Line pattern |
| `start_ending` | string | Yes | A line ending ([13.9](#139-line-endings)) | Decoration at the first node. Not drawn on a closed path |
| `end_ending` | string | Yes | A line ending | Decoration at the last node. Not drawn on a closed path |
| `marker_tip` | string | No | `bullet`, `chisel`, `brush`; an unknown value is read as absent | The marker tip the stroke was drawn with. Absent: an ordinary line |

A node:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `point` | point, diagram points relative to the frame's top-left corner | Yes | Each coordinate finite, within ±1,000,000,000 | Where the line goes |
| `kind` | string | Yes | `straight` or `curve` | A corner, or a smooth bend through this node |
| `control_point` | point, same coordinates | No | As `point` | The first curve control of the segment *arriving at* this node |
| `second_control_point` | point, same coordinates | No | As `point`; only with `control_point` | The second control, making the arriving segment a cubic curve |

**Node coordinates are in diagram points, local to the frame**, not fractions: a node at `(0, 0)` is at
the frame's top-left corner. This differs from connection points and text regions. When a person
resizes a path, the app rewrites its node coordinates in proportion.

Paths count against a document-wide limit of 1,000,000 points, counted together with connector points
("The diagram contains too many path points (*n*).").

**The frame.** The reader does not require nodes to lie inside the frame; a node outside it is valid
and drawn where it is. A writer SHOULD make the frame the bounding box of the nodes grown by half the
line width on every side, and at least one line width in each direction, as the app does for a
freehand stroke. That is how a perfectly horizontal stroke still has a height greater than zero.

**How the nodes become a line.** The first rule that fits applies:

1. **No node has a control point, and at least one node has `kind` `curve`.** The nodes are joined as
   a direct connector's points are ([connections.md](connections.md)): a `straight` node is a corner,
   and a run of consecutive `curve` nodes is one smooth curve through them.
2. **No node has a control point, `is_smoothed` is `true`, and there are more than two nodes.** The
   pencil rule: interior nodes are *control* points, not places the line visits. For nodes P0 to Pn,
   the line is a quadratic curve from P0, pulled towards P1, to the midpoint of P1 and P2; then, for
   each later interior node Pj, a quadratic curve from the midpoint of Pj−1 and Pj, pulled towards Pj,
   to the midpoint of Pj and Pj+1; and last a straight piece from the final midpoint to Pn.
3. **Otherwise** each node after the first is reached by the segment its own keys describe: a straight
   line with no control point, a quadratic curve with `control_point` only, a cubic curve with both.

On a closed path, the first node's control points describe the closing segment from the last node back
to the first.

**Marker ink.** When `marker_tip` is present, the path is open and `style.stroke_texture` is absent,
the stroke is drawn as translucent marker ink shaped by the tip, in `style.stroke_hex` at
`style.line_width`. Only the tip is stored. Adding a stroke texture replaces the ink.

| `marker_tip` | Shape of the stroke (informative) |
| --- | --- |
| `bullet` | Round: the same width in every direction |
| `chisel` | Flat and slanted: broad across the slant, thin along it |
| `brush` | Swells after it lands and thins to a point as it lifts |

Refusals: "…invalid number in path node count.", "…closed path node count.", "…path point x." (or y),
"…path control point x.", "…path second control point x.", "…missing first cubic control." (a
`second_control_point` without a `control_point`). An unknown node `kind`, `stroke_pattern` or ending
gives the general message for an invalid `document.json`.

### 11.10 `unresolved`

A placeholder for content from an imported legacy file that the app could not convert. A program
writing new diagrams has no reason to create one, and SHOULD keep one it reads unchanged.

`unresolved_content` (note that its keys are camel case):

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `sourceFormat` | string | Yes | At most 128 bytes of UTF-8; no NUL | Where the fragment came from. The importer writes `sdxml` |
| `typeIdentifier` | string | Yes | At most 512 bytes of UTF-8; no NUL | The legacy element's type name |
| `sourceFragment` | string | Yes | At most 1,000,000 bytes of UTF-8; no NUL | The original markup of the element, kept as inert text |

The fragment is never interpreted; it is kept so that a later conversion can use it. **Informative:**
the app draws the element as a box with 5-point rounded corners and a dashed outline, filled and
stroked from `style`.

## 12. Complex shapes

A **complex shape** is drawn by the app from a few stored values, not from stored artwork. Its `kind`
is `complex_shape`, and its outer object (level 0) carries a `complex_shape` record naming the type and
holding the values that type needs. The outline, title band, lane strips, cell lines, tail and dashed
line are all worked out from those values and the element's frame.

There are five types:

| `type` | What it is |
| --- | --- |
| `container` | A frame with a title that holds other shapes |
| `swimlanes` | One shape divided into parallel lanes, each holding shapes |
| `table` | Rows and columns of text |
| `callout` | A speech bubble with an optional tail |
| `lifeline` | A participant in a UML sequence diagram |

### 12.1 The record

The record is strict. `type` is read first; after it, only `type`, `hand_drawn` and that type's own keys
are allowed. A key another type uses, an unknown key, or an unknown `type` makes the app refuse the
document.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `type` | string | Yes | `container`, `swimlanes`, `table`, `callout`, `lifeline` | Which complex shape |
| `hand_drawn` | boolean | No | Default `true` | `true`: lines are drawn with the app's hand-drawn pen. `false`: drawn exactly. The app writes the key only when it is `false` |

**Informative: very large shapes are drawn with exact lines.** The hand-drawn pen's work grows with the
length of the lines it draws. So that a document asking for shapes far larger than a page cannot make
every repaint slow, the app draws a shape with exact lines, whatever `hand_drawn` says, when its lines
would need more of the pen's work than one shape may have, or when the complex shapes behind it on the
same page have already used what a page may have. Nothing is refused and nothing stored changes; only
shapes much larger than a page, or pages of very many of them, look less hand-drawn.

The keys of each type follow in [12.5](#125-container) to [12.9](#129-lifeline).

### 12.2 The element's own keys

A complex shape is an ordinary element in every other way: it has a frame, a rotation, a style, a
stacking position, a lock, connection points and shape data. In addition:

- **Text.** `text_content` is the shape's own text: a container's title, the title across the top of
  swimlanes, a callout's words, a lifeline's name. A table's `text_content.string` MUST be empty,
  because its text is in its cells. The shape puts its text in a fixed place, so `text_layout` is
  forbidden.
- **Flips.** Only a callout may be flipped. On every other type `flip_horizontal` and `flip_vertical`
  MUST be `false`.
- **Style.** `style` gives the fill and line as for any shape ([section 5](#5-style)).

### 12.3 Members

A container and each lane of swimlanes record what they hold, in order, as a list of **members**.
A member is an object of the same form a group member has:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `kind` | string | Yes | `element` or `group` | What the member is |
| `id` | UUID | Yes | An element or group on the **same page** | Which one |

A connector is never a member: a member of kind `connection` makes the app refuse the document. A
member list MUST NOT repeat an entry. A container holds at most 1,000 members, a lane at most 1,000,
and all the lanes of one swimlanes shape at most 2,000 together ([12.11](#1211-how-much-a-shape-and-a-page-hold)).
Members are references only;
each member keeps its own position in page coordinates, and the reader does not check that a member
lies inside its holder's frame. The rules that involve other shapes on the page are in
[12.10](#1210-rules-across-the-page).

### 12.4 Which message a fault gives

Faults in the record's *form* are found while the JSON is read and give the general message for an
invalid `document.json`: an unknown type, key or enumerated value; a missing required key
(`direction`, `lanes`, `row_sizes`, `column_sizes`, `cells`, or a lane's key); a lane or member `id`
that is not a UUID; a member `kind` other than `element`, `group` or `connection`; and a list longer
than its maximum (more than 50 lanes, 200 rows, 50 columns or 50 cells in a row). A member list
longer than 1,000 is not one of these: it gets the message of
[12.11](#1211-how-much-a-shape-and-a-page-hold), however long it is. A reader stops reading one
past 2,001 entries, so a very long list costs little before it is refused.

Faults in its *meaning* are found afterwards and give "Shape … holds a container, swimlanes, table,
callout or lifeline record that is not valid.": no lanes, a share out of range, a cell count that does
not match, more than 2,000 cells, strip or band sizes out of range, a forbidden flip or rotation, a
frame below a smallest size, a tail rule, or a member rule.

Three exceptions: a fault in the shape's own text, a lane title or a cell gives the text messages of
[section 6](#6-text-content); an `artwork_asset_id` that does not name an `editable_vector` asset
gives "…invalid text in container artwork reference."; and a shape or page holding more than it may
gives the message of its limit ([12.11](#1211-how-much-a-shape-and-a-page-hold)).

### 12.5 `container`

A frame with a title that holds other shapes.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `members` | array of members | No | Default `[]`. At most 1,000; no repeats; no connectors | What the container holds, in order. The app always writes the key, even when empty |
| `layout` | string | No | Default `free`. `free` or `automatic` | `free`: members stay where they are put. `automatic`: the members are meant to sit in order, left to right and then down (see below). The app writes the key only for `automatic` |
| `artwork_asset_id` | string | No | MUST name an embedded asset of kind `editable_vector` | A drawing the container wears in place of its plain box, stretched to its frame. Absent: the app's own rounded box |
| `title_style` | string | No | Default `band`. `band` or `corner_tag` | `band`: the title is drawn along the top. `corner_tag`: the title is drawn in a tag in the top-left corner, sized to the title, as a UML frame names itself. The app writes the key only for `corner_tag` |

An `automatic` container MUST NOT be turned: its `rotation_degrees`, reduced to one turn, MUST be 0
(so 0, 360 and −720 are all accepted).

A new container made by the app has an empty title whose `base_alignment` is `leading`.

**What `automatic` does in SimpleDiagrams 5.1.** The app keeps and draws an `automatic` container and
its members exactly as stored: it does not move the members or resize the container on its own, either
when the document opens or when it is edited. Apart from the rotation rule above, `automatic` changes
nothing in how the document is read or drawn. Members are drawn where they are stored.

**The arrangement.** The RECOMMENDED placement for an automatic container's members, and its height, is
this arrangement. Members keep their sizes and are placed in
list order, each in a slot as large as its upright bounding box: from 12 points inside the left edge
and 32 points below the top, 12 points apart, wrapping to a new row before crossing 12 points inside
the right edge. Each row is as tall as its tallest slot, and members are centred on their row's middle
line. The container's height is the title band, the rows and 12 points of padding at the bottom.

**Informative: the corner tag.** The tag's size is never stored; the app sizes it to the title each
time it is drawn.

Example record:

```json
{
  "type": "container",
  "members": [{"kind": "element", "id": "3B9E1F2A-6C4D-4E8F-A0B2-C4D6E8F0A1B3"}],
  "title_style": "corner_tag"
}
```

### 12.6 `swimlanes`

One shape divided into parallel lanes. Each lane has a title strip and its own members, and a title
band across the top of the whole shape holds the shape's own title.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `direction` | string | Yes | `horizontal` or `vertical` | `horizontal`: lanes are rows, their title strips down the left side. `vertical`: lanes are columns, their title strips across the top of each lane |
| `lanes` | array of lanes | Yes | 1 to 50 | The lanes, from the top (horizontal) or from the left (vertical) |
| `title_strip_size` | number, diagram points | No | Default `28`. Finite, 28 to 10,000 | How thick every lane's title strip is. The app writes the key only when it is not 28 |
| `title_band_size` | number, diagram points | No | Default `28`. Finite, 28 to 10,000 | How tall the band across the top is. The app writes the key only when it is not 28 |

A **lane** has exactly these four keys. Any other key makes the app refuse the document.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique among this shape's lanes | The lane's identity. It is unique only within its shape, not in the document: a copied shape keeps its lanes' identifiers |
| `title` | text content | Yes | As any text content ([section 6](#6-text-content)), and at most 1,000 characters and 100 runs | The lane's title |
| `size` | number | Yes | Finite, 0.000001 to 1,000,000 | The lane's thickness as a **share**: its part of the sum of all the lanes' sizes, not a length in points |
| `members` | array of members | Yes | As a container's: at most 1,000. MUST be present even when empty | What the lane holds, in order. All the lanes of one shape hold at most 2,000 together |

**Laying out the lanes.** The title band is `title_band_size` points tall across the top of the frame.
The lanes share what is left of the frame between them, in proportion to their `size` values:

| `direction` | Each lane's frame, before rotation | Smallest sizes the frame MUST allow |
| --- | --- | --- |
| `horizontal` | Full frame width; height = (frame height − title band) × `size` ÷ sum of sizes; stacked top to bottom below the band | Frame width at least `title_strip_size`; every lane at least 28 points tall |
| `vertical` | Full height below the band; width = frame width × `size` ÷ sum of sizes; side by side from the left | Every lane at least 28 points wide; frame height minus the title band at least `title_strip_size` |

A frame too small for its lanes makes the app refuse the document with the complex shape message
([12.4](#124-which-message-a-fault-gives)). The comparison allows a relative shortfall of 10⁻⁹, so a
size worked out from shares that lands a rounding error below its smallest still passes.

A new swimlanes shape made by the app has three horizontal lanes of `size` 1, titled "Lane 1" to
"Lane 3" in the language of the Mac that made it.

Example record:

```json
{
  "type": "swimlanes",
  "direction": "horizontal",
  "lanes": [
    {
      "id": "5C0A7E21-94B3-4D6F-8A1E-0B2C3D4E5F60",
      "title": {
        "base_alignment": "center",
        "base_style": {"color_hex": "#212529", "font_size": 15, "is_italic": false,
                       "is_underlined": false, "weight": "medium"},
        "character_runs": [], "paragraph_runs": [], "string": "Sales"
      },
      "size": 1,
      "members": []
    }
  ]
}
```

### 12.7 `table`

Rows and columns with text in each cell.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `row_sizes` | array of numbers | Yes | 1 to 200 entries, each finite, 0.000001 to 1,000,000 | Each row's height as a share of the table's height |
| `column_sizes` | array of numbers | Yes | 1 to 50 entries, each as above | Each column's width as a share of the table's width |
| `cells` | array of rows, each an array of text content | Yes | Exactly one row per entry of `row_sizes`, and in every row exactly one cell per entry of `column_sizes`; at most 2,000 cells in all. Each cell at most 2,000 characters and 200 runs; all the cells together at most 100,000 characters and 10,000 runs | Each cell's text, row by row ([section 6](#6-text-content)) |
| `header_row` | boolean | No | Default `true` | `true`: the first row is drawn as a header, its text bold on a band of the fill colour. It changes only how the row is drawn, never the stored text. The app writes the key only when it is `false` |

A row's height is the frame height × its share ÷ the sum of `row_sizes`; a column's width likewise
from the frame width. With the frame as stored, no column may be narrower than 16 points and no row
shorter than 12 points (with the same 10⁻⁹ allowance as lanes).

The table element's own `text_content.string` MUST be empty.

A new table made by the app has three rows and three columns, all shares 1, and empty cells.

Example record of a table with two rows and two columns, the first column twice as wide as the
second. Each cell is a complete text content record; the second row's cells are left empty.

```json
{
  "type": "table",
  "row_sizes": [1, 1],
  "column_sizes": [2, 1],
  "cells": [
    [
      {"base_alignment": "center", "character_runs": [], "paragraph_runs": [], "string": "Item",
       "base_style": {"color_hex": "#212529", "font_size": 15, "is_italic": false,
                      "is_underlined": false, "weight": "medium"}},
      {"base_alignment": "center", "character_runs": [], "paragraph_runs": [], "string": "Qty",
       "base_style": {"color_hex": "#212529", "font_size": 15, "is_italic": false,
                      "is_underlined": false, "weight": "medium"}}
    ],
    [
      {"base_alignment": "center", "character_runs": [], "paragraph_runs": [], "string": "",
       "base_style": {"color_hex": "#212529", "font_size": 15, "is_italic": false,
                      "is_underlined": false, "weight": "medium"}},
      {"base_alignment": "center", "character_runs": [], "paragraph_runs": [], "string": "",
       "base_style": {"color_hex": "#212529", "font_size": 15, "is_italic": false,
                      "is_underlined": false, "weight": "medium"}}
    ]
  ]
}
```

### 12.8 `callout`

A speech bubble with an optional tail.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `tail_tip` | point, diagram points | No | Finite; and once placed on the page (below), each coordinate within ±1,000,000,000 | Where the tail points, measured from the centre of the frame along the bubble's own axes, before rotation and flipping. Absent: no tail |
| `tail_attachment` | object | No | Only with `tail_tip`; see below | Locks the tail to a numbered connection point on another shape |

**Placing the tip on the page.** Take the point `(width / 2 + tail_tip.x, height / 2 + tail_tip.y)`
relative to the frame's top-left corner; mirror it about the frame's centre for each flip that is
`true`; then turn it about the frame's centre by `rotation_degrees`. A free tail therefore moves and
turns with its bubble.

**A locked tail.** `tail_attachment` is an attachment record of the form a connector end uses (see
[connections.md](connections.md)), and refuses unknown keys in the same way. For a callout it MUST be
the numbered-point form:

```json
{"element_id": "7E1A3C5F-9B2D-4E6A-8C0B-1D3F5A7C9E02", "connection_point_id": 1}
```

- It MUST name a connection point that an element on the **same page** has.
- It MUST NOT name the callout itself.
- It MUST NOT use the attach-line form (`attach_line_id` and `distance`).
- `tail_tip` MUST be present as well. It records where the tail was last drawn, and is used again if
  the lock is removed.

A locked tail is drawn to its target's connection point, wherever that shape now is. That drawn tip,
expressed in the bubble's own axes and placed back on the page, MUST also lie within ±1,000,000,000 on
both axes.

**Informative.** A tip that lies inside the bubble's frame, or on its edge, draws no tail; it is kept,
so moving the bubble or the tip outward shows the tail again. A new callout made by the app has
`tail_tip` `{"x": -40, "y": 70}`.

### 12.9 `lifeline`

A participant in a UML sequence diagram: a head holding its name, and a dashed line down from the
bottom centre of the head to the bottom of the frame.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `head_style` | string | No | Default `box`. `box` or `actor` | `box`: the name in a box. `actor`: a stick figure with the name below it. The app writes the key only for `actor` |
| `ends_in_destroy_mark` | boolean | No | Default `false` | `true`: the line ends in UML's destroy mark, an X centred on the line's end. The app writes the key only when it is `true` |

**Geometry.** The head spans the full width of the frame at its top and is 40 points tall for `box`,
72 points for `actor`. The line runs from the bottom centre of the head (`x` = 0.5 of the frame) down
to the bottom of the frame, or, with a destroy mark, to 12 points above the bottom, where the centre
of the 24-point mark is.

**Smallest height.** The frame's height MUST be at least the head's height plus 20 points of line, plus
12 more with a destroy mark:

| `head_style` | Without destroy mark | With destroy mark |
| --- | --- | --- |
| `box` | 60 | 72 |
| `actor` | 92 | 104 |

**The attach line.** A lifeline offers connectors one **attach line**, number `0`: its dashed line,
measured from its start at the head. A connector end may be attached anywhere along it; how such an
attachment is stored is specified in [connections.md](connections.md). A lifeline offers the attach
line whatever its `connection_points` are. A callout's tail cannot lock to the attach line, but it
may lock to one of the lifeline's numbered connection points, such as point `2` below.

A new lifeline made by the app has one connection point, number `2`, at the top centre of its head.

Example record:

```json
{"type": "lifeline", "head_style": "actor"}
```

### 12.10 Rules across the page

These rules involve more than one shape and are checked with the rest of the page. A broken rule makes
the app refuse the document with "Shape … holds a container, swimlanes, table, callout or lifeline
record that is not valid.", naming the holder or callout.

1. Every member names an element or group on the **same page** as its holder, and never the holder
   itself.
2. Swimlanes are never a member of a container or lane.
3. An element or group is a member of at most one container or lane.
4. A group can be a member only as a whole, and only a group that is not itself inside another group:
   no element or group that is inside a group may also be a member on its own.
5. A container or swimlanes MUST NOT be a member of any group. (A container holding it does that job.)
6. Containers and swimlanes nest at most **4** levels deep (a holder inside a holder inside a holder
   inside a holder), with no loops.
7. A callout's locked tail names a connection point its target has, on the same page, and the tail's
   drawn tip lies within ±1,000,000,000 on both axes ([12.8](#128-callout)).

Nothing compares a complex shape, its members or a callout's tip with the page's stored `width` and
`height`; "on the page" means within the coordinate limits.

**Text budgets.**

| Budget | Limit | Refusal example |
| --- | --- | --- |
| One swimlanes shape's text: its own `text_content` and every lane title together | 1,000,000 bytes of UTF-8 and 100,000 runs | "…invalid text in text content." / "…too many text-formatting runs (*n*)." |
| Lane titles and table cells across the whole document | 100,000 | "The diagram contains too many lane titles and table cells (*n*)." |

A table's cells, and how much a page's complex shapes hold, have limits of their own
([12.11](#1211-how-much-a-shape-and-a-page-hold)).

**Informative: drawing order.** A container or swimlanes is drawn, and clicked, just behind the lowest
thing it holds: a member, a member of a group it holds, a container inside it, or a connector joined
to one of these. Inner holders are placed first, so a container inside another lands behind its own
contents and the outer one behind it. This drawn order is worked out every time and never stored; a
writer cannot put a container in front of its own contents.

### 12.11 How much a shape and a page hold

These limits keep a document that one person makes from hanging or exhausting the Mac of another who
opens it. They are set far above any diagram drawn by hand. A document over any of them is refused as
a whole, with the message shown; nothing is cut to fit.

**Characters** here are scalars ([Terms](README.md#terms)), Unicode scalar values, so `é` written as
one code point counts once however many bytes it takes. **Runs** are the character runs
and paragraph runs of one text content together ([6.3](#63-runs-and-ranges)).

| What is limited | Limit | Message (English) |
| --- | --- | --- |
| Members of one container | 1,000 | "The diagram contains a container holding too many shapes (*n*)." |
| Members of one lane | 1,000 | "The diagram contains a lane holding too many shapes (*n*)." |
| Members of all the lanes of one swimlanes shape together | 2,000 | "The diagram contains swimlanes holding too many shapes (*n*)." |
| One lane title's characters | 1,000 | "The diagram contains a lane title that is too long (*n* characters)." |
| One lane title's runs | 100 | "The diagram contains a lane title with too many text-formatting runs (*n*)." |
| One table cell's characters | 2,000 | "The diagram contains a table cell that is too long (*n* characters)." |
| One table cell's runs | 200 | "The diagram contains a table cell with too many text-formatting runs (*n*)." |
| All the cells of one table: characters | 100,000 | "The diagram contains a table with too much text (*n* characters)." |
| All the cells of one table: runs | 10,000 | "The diagram contains a table with too many text-formatting runs (*n*)." |
| Complex shapes on one page, of every type | 2,000 | "The diagram contains a page with too many complex shapes (*n*)." |
| Containers and swimlanes on one page together | 500 | "The diagram contains a page with too many containers and swimlanes (*n*)." |
| Characters in all the lane titles and table cells on one page together | 1,000,000 | "The diagram contains a page with too much text in its complex shapes (*n* characters)." |

*n* is the count the document holds, written as numbers are in the reader's language, for example
`1,001` in English. A shape's own
`text_content` is held to the limits every element's text has ([section 6](#6-text-content)), not to
these. When a document breaks more than one limit, which message it gets is not specified.

## 13. Enumerated values

Every value below is stored exactly as shown, and is case-sensitive. An unknown value makes the app
refuse the document, except `marker_tip`, which is read as absent.

### 13.1 Element and complex shape values

| Where | Values |
| --- | --- |
| Element `kind` | `rectangle`, `ellipse`, `note`, `text`, `symbol`, `editable_vector`, `svg_artwork`, `image`, `path`, `unresolved`, `complex_shape` |
| `complex_shape.type` | `container`, `swimlanes`, `table`, `callout`, `lifeline` |
| Container `layout` | `free`, `automatic` |
| Container `title_style` | `band`, `corner_tag` |
| Swimlanes `direction` | `horizontal`, `vertical` |
| Lifeline `head_style` | `box`, `actor` |
| Container and lane member `kind` | `element`, `group` (`connection` is read but refused) |

### 13.2 `note_style`

| Value | Meaning |
| --- | --- |
| `stickyNote` | A sticky note (also the meaning when the key is absent) |
| `indexCard` | An index card with ruled lines |

### 13.3 `image_content_mode`

`fit`, `fill`, `stretch` ([11.8](#118-image)).

### 13.4 Font `weight`

`regular`, `medium`, `bold`.

### 13.5 Alignment

`base_alignment` and a paragraph run's `alignment`: `leading`, `center`, `trailing`, `justified`.

### 13.6 `text_layout.position`

`above`, `top`, `middle`, `bottom`, `below` ([section 7](#7-text-layout)).

### 13.7 Line patterns

`path.stroke_pattern` and `style.stroke_pattern_override`: `solid`, `dashed`, `dotted`.

### 13.8 Path node `kind` and `marker_tip`

| Where | Values |
| --- | --- |
| Path node `kind` | `straight`, `curve` |
| `marker_tip` | `bullet`, `chisel`, `brush` |

### 13.9 Line endings

`start_ending` and `end_ending` on a `path`. Connectors use the same list
([connections.md](connections.md)). The second column is the name the app shows.

| Stored value | Shown as | Stored value | Shown as |
| --- | --- | --- | --- |
| `none` | None | `one` | One |
| `stop` | Stop (a bar across the end) | `oneAndOnlyOne` | One and Only One |
| `arrow` | Arrow (open) | `zeroOrOne` | Zero or One |
| `solidArrow` | Hollow Arrow | `oneOrMany` | One or Many |
| `circle` | Circle | `zeroOrMany` | Zero or Many |
| `solidCircle` | Filled Circle | `filledArrow` | Filled Arrow |
| `openDiamond` | Diamond (outlined) | `slash` | Slash |
| `diamond` | Filled Diamond | `circlePlus` | Circle Plus |
| `halfCircle` | Half Circle | `cross` | Cross |
| `crowFoot` | Crow Foot | `halfArrow` | Half Arrow |

Note that `solidArrow` is drawn hollow, `openDiamond` is the outlined diamond and `diamond` the filled
one, and `stop` is not "nothing": a plain end is `none`.

### 13.10 Textures

`fill_texture` and `stroke_texture`, stored exactly as shown, including the misspelling in the seventh
value:

| | | |
| --- | --- | --- |
| `Horizontal` | `Vertical` | `Grid` |
| `SingleHatch` | `TripleHatch` | `PencilFill` |
| `PencilThinHoriztonalHatch` | `PencilThinSquareHatch` | `PencilThinCrossHatch` |
| `PencilThinDiagonalHatch` | `WatercolorFill` | `Grain` |
| `PencilVerticalHatch` | `PencilTriangleHatch` | `PencilRandomHatch` |
| `PencilHorizontal` | `Chalk` | |

## 14. Reading older document formats

Writers MUST write document format 18. The app also opens documents of every earlier format; their
element records are laid out differently and are not specified here (see [README.md](README.md)).
Two element rules are specific to format 18, and a reader that also accepts older documents needs
them:

- **Complex shapes** exist only in document format 18. An older document that holds a `complex_shape`
  record is refused.
- **Text regions outside their shape.** Before document format 18 a text region's `x` and `y` had to
  lie from 0 to 1; an older document whose region starts left of or above its shape is refused as
  damaged.

## 15. Open points

1. **Grapheme cluster boundaries across systems.** The rule is the one the app computes: it runs on
   macOS 15 and later and finds boundaries with the system's Unicode rules, which follow the Unicode
   version of that macOS release. For characters and emoji sequences added in recent Unicode versions,
   two systems can disagree about a boundary. A writer SHOULD keep runs to boundaries that are stable
   across Unicode versions: for example, not inside an emoji sequence added in a recent Unicode
   version.
2. **Drawing details** such as text measurement and wrapping, texture tile sizes, ending sizes, marker
   ink shapes and the hand-drawn pen's wobble are the app's own presentation. They do not affect
   whether a document is valid, and are not specified here.
