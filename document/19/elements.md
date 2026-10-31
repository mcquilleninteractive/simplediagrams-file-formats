# Elements

This page specifies the **elements** of document format 19: every shape on a page, the keys each one
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
14. [Open points](#14-open-points)

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
| `note` | A note drawn by the app in one of six looks, such as a sticky note or an index card | [11.3](#113-note) |
| `text` | Text on its own, with no shape behind it | [11.4](#114-text) |
| `symbol` | A library shape placed by reference: the document names the shape, not its artwork | [11.5](#115-symbol) |
| `editable_vector` | A shape whose artwork is stored in the document as native vector artwork (a `.sdvector` asset) | [11.6](#116-editable_vector) |
| `svg_artwork` | An SVG stored in the document and drawn as its author made it | [11.7](#117-svg_artwork) |
| `image` | A PNG or JPEG picture stored in the document | [11.8](#118-image) |
| `path` | A freehand or point-by-point line, or a closed outline | [11.9](#119-path) |
| `unresolved` | A placeholder for content from an imported legacy file that has no native equivalent | [11.10](#1110-unresolved) |
| `complex_shape` | A shape that does more than a basic shape: a container, swimlanes, a table, an entity table, a callout, a UML lifeline, a ring, a flow, a pyramid, a cycle or a gauge, drawn by the app from its settings | [12](#12-complex-shapes) |

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
0), or anywhere in a `complex_shape` record except inside its texts ([12.1](#121-the-structure)).
Everywhere else in an element (levels 1 to 3, and the style, text content, text layout, path, path node, symbol
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
| `note_style` | 1 | string | No | One of the six looks of [11.3](#113-note); `note` only | Which note look. Absent: sticky note. A reader draws a word it does not know as a sticky note ([11.3](#113-note)) |
| `preserves_aspect_ratio` | 1 | boolean | No | Default `false`; never `true` on a ring, flow, pyramid, cycle or gauge | Keep proportions when a person resizes the element ([section 9](#9-locking-and-aspect-ratio)) |
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
| `complex_shape` | Among the types the catalogue lists, only a callout may be flipped; on the others both MUST be `false`. A shape of a type or version the reader does not know keeps its flips as written ([12.6](#126-the-elements-own-keys)) |
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
  drawing a container wears (its `artwork` setting, [container](complex-shapes/container/README.md)), and within those only to
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
| `complex_shape` | Its body, a table's header row, and every part that has no fill of its own | Its outline, lane and cell lines, a lifeline's dashed line, and every part without a line of its own | Used | Ignored, except on a container that wears a drawing, where it applies to the drawing's line-coloured parts |

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
| `complex_shape` (every type) | `#FFFFFF` | `#000000` | 1 |
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

The text content record is lenient: an unknown key in it is ignored. A writer MUST NOT write
`background_hex`, a whole-text background colour that document format 19 does not have; a reader that
meets it, as it may in a document an earlier SimpleDiagrams saved, ignores it and draws no text
background. A highlight colour behind characters ([6.2](#62-character-style)) takes its place.

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
| `highlight_hex` | colour | No | A colour | A highlight drawn behind these characters, like a highlighter pen. Absent: none. `#RRGGBBAA` gives a see-through highlight |

The app's starting text is font size 15, weight `medium`, colour `#212529`, not italic, not
underlined, no `font_family`, no highlight, and `base_alignment` `center`.

**Highlight.** `highlight_hex` may appear in `base_style` and in any character run's `style`, in every
text: an element's label, a part's text, a table cell and a connector's
label. A writer leaves it out where there is no highlight. A highlight that is not a colour refuses the
document ("The diagram contains an invalid color value: …"). **Informative.** The app draws each
highlighted stretch as a band behind its characters on each line it covers, then the characters.

An unknown `weight` or alignment makes the app refuse the document with the general message for an
invalid `document.json`.

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
complex shape of a type without text of its own, such as a table, whose text is in its cells, MUST
keep its own `string` empty ([12.6](#126-the-elements-own-keys)). On
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

**Shapes that place their own points.** An entity table, a ring, a flow, a pyramid, a cycle and a gauge
list no connection points: the reader works them out from the shape as it reads the document, and again
whenever the shape changes ([12.6](#126-the-elements-own-keys)). Their numbers are fixed by the shape's
catalogue entry, and their places are where the app draws them, so a connector attached to one follows
it. Everything that resolves a point on another shape, a connector's end or a callout's locked tail,
uses those places.

**The standard eight.** The app gives every new element, except a `path`, a lifeline and a shape that
places its own points, these points:

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
attach along its line instead ([lifeline](complex-shapes/lifeline/README.md)). A shape that places its
own points has those its catalogue entry gives it, as above. A library shape MAY define its own points
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
library shape MAY ask for it; the app sets it from the library when the shape is placed. It MUST NOT be
`true` on a ring, a flow, a pyramid, a cycle or a gauge, whose catalogue entries say how they resize
([12.6](#126-the-elements-own-keys)).

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
| `flip_horizontal` or `flip_vertical` set to `true` | – | – | – | – | – | – | – | – | – | – | Callout, or a type or version the reader does not know; F for the other listed types |
| `preserves_aspect_ratio` set to `true` | – | – | – | – | – | – | – | – | – | – | F for a ring, flow, pyramid, cycle or gauge |
| `text_content.string` not empty | – | – | – | – | – | – | – | – | – | – | F for a type without text of its own: a table, entity table, ring, flow, pyramid or cycle |

Every other key of [2.2](#22-every-key) is required on every kind.

Example messages. A forbidden key: "…invalid text in unexpected symbol reference.", "…unexpected
embedded asset reference.", "…unexpected image properties.", "…unexpected note style.", "…unexpected
unresolved element source.", "…invalid number in unexpected element path.", "…unexpected element text
layout.". A missing required key: "…invalid text in symbol reference.", "…embedded asset reference.",
"…image properties.", "…invalid number in path node count.", "…invalid text in unresolved element
source.". A `complex_shape` record on another kind, a `complex_shape` element without one, a flipped
complex shape of a listed type other than a callout, or a table with its own text: "The shape … is not a valid complex
shape."

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

A note, drawn by the app in one of six looks chosen by `note_style`:

| `note_style` | Look (informative) |
| --- | --- |
| Absent, or `stickyNote` | A sticky note |
| `indexCard` | An index card with ruled lines |
| `tapedNote` | A scrap of paper held up by a strip of clear tape |
| `tag` | A luggage tag with a hole and a loop of string |
| `notebookPage` | A page torn from a ruled notebook, with punched holes and a margin |
| `pinnedNote` | Paper held by a grey push pin |

`note_style` on any other kind is refused. A writer MUST write one of these six words, or leave the key
out for a sticky note.

**A look the reader does not know.** The words form an open list: a later SimpleDiagrams may add looks
within document format 19. A reader that meets a word it does not know MUST NOT refuse the document for
it; it draws the note as a sticky note, with the note's own text, paint, frame and rotation. **Informative.**
The app does exactly that, does not keep the word, and saves `stickyNote` in its place. A word is a
JSON string; any other value refuses the document.

**Where the words go.** A note's `text_layout`, when present, places its words as on any shape
([section 7](#7-text-layout)). Without one, a sticky note and an index card set their words in the
ordinary inset; each of the other four looks sets them clear of its tape, hole, string, holes or pin,
and a notebook page starts them at the top, on its rules. Those decorations are mirrored with the
paper when the note is flipped, and so is that default place for the words, on each flipped axis; the
words themselves are never mirrored. A writer that flips a note whose `text_layout` it has written
SHOULD mirror that layout too, as the app does when a person flips one: `x` becomes `1 − x − width` on a
horizontal flip, and `y` becomes `1 − y − height` on a vertical one.

**Informative.** Every look is drawn inside the note's frame. The paper takes the note's fill and line,
and the rules of a ruled look its line colour; tape, string, pin, ring and margin take fixed colours of
their own. Ruled looks keep their rules a fixed distance apart in points and stop them short of the
bottom edge. The app may also draw a slight shadow; it is not stored. The look's exact artwork is the
app's own and may be refined in later releases without a format change.

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

A **basic shape** is a drawing and one text: a rectangle, a note, a library shape. A **complex shape**
is any shape that does more: it holds other shapes, is divided into lanes or cells, has sections a
person can paint and label one by one, or has a tail that can be locked to another shape. Its `kind`
is `complex_shape`, and its outer object (level 0) carries a `complex_shape` record.

Every complex shape is saved in **one structure** ([12.1](#121-the-structure)): its type, the version
of that type, its settings, the parts a person can touch, and, for a shape that holds other shapes on
its whole body, its members. **Nothing drawn is saved.** The file holds no outline, no position of a
part and no box the app works out for a text; the app draws every complex shape from its settings,
its parts and the element's frame, each time it is drawn. So a file cannot hold a drawing that
contradicts its settings, and a refinement to how a type is drawn reaches documents already saved.

What each type holds is declared by its entry in the [complex-shape catalogue](complex-shapes/README.md).
This section gives the structure every type shares and the rules that hold whatever the type. Document
format 19's catalogue has eleven types, each at version 1:

| `type` | What it is | Entry |
| --- | --- | --- |
| `container` | A frame with a title that holds other shapes | [container](complex-shapes/container/README.md) |
| `swimlanes` | One shape divided into parallel lanes, each holding shapes | [swimlanes](complex-shapes/swimlanes/README.md) |
| `table` | Rows and columns of text | [table](complex-shapes/table/README.md) |
| `entity_table` | One entity of a database: its name, then a row for each field | [entity_table](complex-shapes/entity_table/README.md) |
| `callout` | A speech bubble with an optional tail | [callout](complex-shapes/callout/README.md) |
| `lifeline` | A participant in a UML sequence diagram | [lifeline](complex-shapes/lifeline/README.md) |
| `ring` | A ring cut into sections | [ring](complex-shapes/ring/README.md) |
| `flow` | A row of steps, such as chevrons | [flow](complex-shapes/flow/README.md) |
| `pyramid` | A pyramid or funnel cut into layers | [pyramid](complex-shapes/pyramid/README.md) |
| `cycle` | Steps round a circle | [cycle](complex-shapes/cycle/README.md) |
| `gauge` | A half dial with bands and a needle | [gauge](complex-shapes/gauge/README.md) |

Only SimpleDiagrams defines complex shapes. A writer MUST write only the types and versions the
catalogue lists, each with exactly what its entry declares. It MUST NOT invent a type, a version, a
setting, a role or a word.

### 12.1 The structure

The record has these keys and no others:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `type` | string | Yes | A word ([12.4](#124-words)) | Which catalogue entry |
| `version` | whole number | Yes | 1 to 999 | Which version of the entry |
| `settings` | object | Yes | At most 16 entries; `{}` is valid ([12.2](#122-settings)) | The shape-wide values |
| `parts` | array of parts | Yes | At most 256; `[]` is valid ([12.3](#123-parts)) | The pieces a person can touch, in the order and roles the entry declares |
| `members` | array of members | For a type whose entry holds shapes on its whole body, and forbidden for every other | At most 1,000 ([12.5](#125-members)) | The shapes it holds, in order. `[]` when it holds none |
| `own_text_layout` | object | No; only on a type whose entry allows it | A placed text layout ([12.3](#123-parts)) | Where a person placed the shape's own text. Absent: where the type puts it |
| `hand_drawn` | boolean | No | Only `false` | `false`: the lines are drawn exactly. Absent: they are drawn with the app's hand-drawn pen. It is written only as `false` |

The record is **strict**, and so is every record inside it except its texts, which are ordinary text
records ([section 6](#6-text-content)): an unknown key refuses the document, `null` anywhere refuses it,
and a value of the wrong JSON type refuses it. Every list is read no further than one entry past its
limit, so a very long list costs little before it is refused.

**The structure never changes.** It is the same for every type and every version, and stays as it is
for the life of document format 19. A new kind of complex shape, or a new meaning for an existing one,
is a new entry in the catalogue or a new version of one ([12.7](#127-the-catalogue)).

**Informative: very large shapes are drawn with exact lines.** The hand-drawn pen's work grows with the
length of the lines it draws. So that a document asking for shapes far larger than a page cannot make
every repaint slow, the app draws a shape with exact lines, whatever `hand_drawn` says, when its lines
would need more of the pen's work than one shape may have, or when the complex shapes behind it on the
same page have already used what a page may have. Nothing is refused and nothing stored changes; only
shapes much larger than a page, or pages of very many of them, look less hand-drawn.

### 12.2 Settings

`settings` holds the shape-wide values a person chose, by name: how wide a ring's band is, which way a
flow runs, where a callout's tail points. Each name is a word ([12.4](#124-words)). Each value is one
of six kinds, and its JSON form says which, so a reader can tell the kinds apart without knowing the
type:

| Kind | Written as | Example |
| --- | --- | --- |
| Boolean | `true` or `false` | `"pointer": false` |
| Number | A finite JSON number | `"ring_width": 40` |
| Word | A JSON string that is a word ([12.4](#124-words)) | `"direction": "across"` |
| Point | An object of exactly `x` and `y`, finite numbers in diagram points, from an origin the entry states | `"tail_tip": {"x": -40, "y": 70}` |
| Attachment | An object of exactly `element_id`, an element's UUID, and `connection_point_id`, a whole number 0 or more | `"tail_attachment": {"element_id": "7E1A3C5F-9B2D-4E6A-8C0B-1D3F5A7C9E02", "connection_point_id": 1}` |
| Embedded asset | An object of exactly `asset_id`, an embedded asset's identifier: 64 lower-case hexadecimal digits | `"artwork": {"asset_id": "8c267a32bf4c6f6e89e1db2a05312d14531d6103096cd3a50e610539eb617fb7"}` |

Nothing else is a setting: `null`, a list, free text and any other object refuse the document. A number
is a number and a word is a word: `1` is not `true`, and `"40"` is not a word, because a word starts
with a letter.

**An attachment** fastens the shape to a numbered connection point of another element on the same page,
as a callout's locked tail does. It never names the shape itself and never an attach line
([12.10](#1210-rules-across-the-page)). **An embedded asset** names an asset the document holds, of the
kind the entry requires ([assets.md](assets.md)).

**Every setting is written.** A shape of a known type holds exactly the settings its entry declares,
each of the kind, range and words the entry gives. A writer MUST write every one, including those
whose value is the entry's default. Only a setting the entry marks optional may be left out, and then
its absence has the meaning the entry states: a callout without a tail, a container without artwork.

How many sections, steps, lanes or rows a shape has is never a setting: it is the number of its parts
of that role. And a record holds only what a person chose, never what the app was doing with it: no
chosen part and no open control is saved.

### 12.3 Parts

Each entry of `parts` is one piece of the shape a person can touch: a lane, a column or a row of a
table, a field of an entity table, a ring's section, a flow's step, a gauge's needle. Every part has a
`role`. Its other keys come from one closed set of ten, and the type's entry says, role by role, which
keys a part must hold and which it may; any other key of the ten refuses the document.

| Key | Type | Rules / bounds | Meaning |
| --- | --- | --- | --- |
| `id` | UUID | Unique among the shape's parts | The part's identity. Like the parts themselves, it is unique only within its shape: a copied shape keeps its parts' identifiers. The app makes it with the part and never changes or reuses it |
| `role` | string | A word; required on every part | What the part is, such as `lane` or `section` |
| `text` | text content | [Section 6](#6-text-content), and the limits its entry gives | The part's words: a lane's title, a section's label |
| `text_layout` | object | A placed text layout (below) | Where a person placed the part's words. Absent: where the type puts them |
| `style` | object | 1 to 5 channels (below) | The part's own paint. Absent: the shape's |
| `size` | number | Finite, 0.000001 to 1,000,000 | The part's share of the shape: a lane's thickness, a row's height, a column's width, as its part of the sum of all its siblings' sizes, not a length in points |
| `members` | array of members | At most 1,000 ([12.5](#125-members)) | The shapes the part holds, in order |
| `cells` | array of text content | At most 50 | The part's texts, one for each column |
| `marks` | array of words | At most 8, none repeated | Words marking the part, such as a field's `primary_key` |
| `point_ids` | array of whole numbers | At most 2, each 8 or more | The numbers of the connection points the part owns |

**Order.** The parts come in the order of the roles the entry declares, all the parts of one role
together, and each role has a number of parts within the range the entry gives. A gauge, for example,
has its bands and then its one needle. Within a role, the order of the parts is the shape's sequence:
reading order, moving from part to part with the Tab key, VoiceOver and the order of parts in exported
SVG all follow it.

**Points.** A part owns connection points only from 8 up, and lists them in `point_ids`; no number is
listed twice, by one part or by two. How many points a part of each role owns, and what each one is, is
the entry's to say: in a shape that places its own points ([12.6](#126-the-elements-own-keys)), points
0 to 7 are the shape's own and every point from 8 up belongs to one of its parts. A shape of a type or
version the reader does not know whose element lists connection points MUST list every point its parts
own among them.
**Informative.** A new part takes the smallest unused numbers from 8 up; removing a part removes its
points and releases the connectors attached to them where they were drawn, so a number used again
attaches nothing by accident.

**Part paint.** A part's `style` holds any of these five channels, at least one, and nothing else:

| Key | Type | Rules | Meaning |
| --- | --- | --- | --- |
| `fill_hex` | colour | A colour ([README.md](README.md#terms)) | The part's fill colour |
| `fill_texture` | string | A texture name ([13.10](#1310-textures)); only with `fill_hex` | The part's fill texture |
| `stroke_hex` | colour | A colour | The part's line colour |
| `stroke_texture` | string | A texture name; only with `stroke_hex` | The part's line texture |
| `line_width` | number | 0 to 10,000 | The part's line weight in points |

A missing channel follows the element's `style` ([section 5](#5-style)), with one rule for textures: a
colour and its texture go together.

| A part's `style` holds | Its fill is drawn with | Its line is drawn with |
| --- | --- | --- |
| No `fill_hex` | The shape's fill colour and fill texture | |
| `fill_hex` alone | The part's colour, flat | |
| `fill_hex` and `fill_texture` | The part's colour and texture | |
| No `stroke_hex` | | The shape's line colour and line texture |
| `stroke_hex` alone | | The part's colour, flat |
| `stroke_hex` and `stroke_texture` | | The part's colour and texture |

`line_width` stands alone: absent, the part has the shape's line weight. So a part keeps the shape's
texture under a colour of its own only by naming that texture too. An empty `style`, `{}`, and a
texture without its colour refuse the document. A part's lines are always solid.

**A placed text layout.** A part's `text_layout`, and the record's `own_text_layout`, record where a
person placed a text. Each has exactly these keys:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `frame` | object | Yes | Exactly `x`, `y`, `width` and `height`, finite fractions of the element's frame; `width` and `height` 0 or more | The region: from `x × frame.width` across and `y × frame.height` down from the frame's top-left corner, `width × frame.width` wide and `height × frame.height` tall |
| `position` | string | Yes | `top`, `middle` or `bottom` | Where the words sit in the region, as on any shape ([section 7](#7-text-layout)). `above` and `below` refuse the document |
| `rotation_degrees` | number | No | More than 0 and less than 360; written only when it is not 0 | The region's turn, clockwise about its centre, on top of the element's |

The region, turned as it is, lies inside the element's frame: each of its four corners within 0.01
points. It MAY run over any line, hole or other part. A layout is present only where a person placed
the text; without one, the text sits where its type puts it. **Informative.** When the number of a
shape's parts changes, and for the few settings that change every part, the app returns each placed
text to its type's place and stops saving its layout.

### 12.4 Words

Every word in a complex shape follows one spelling rule: its `type`, its setting names, a setting's
word value, its roles and its marks. A word is lower-case letters and digits, starting with a letter,
in groups joined by single underscores, and is at most 32 bytes:

```text
^[a-z][a-z0-9]*(_[a-z0-9]+)*$
```

`ring`, `entity_table`, `corner_tag` and `primary_key` are words. `Ring`, `rounded-rectangle`,
`two__parts`, `_tail`, `tail_` and `3d` are not, and refuse the document wherever they appear.

Words are machine words: they are stored exactly as written and never translated. Within a known type
and version every list of words is **closed**: a word its entry does not list refuses the document. A
new word for a setting or a mark is a new version of the entry ([12.7](#127-the-catalogue)).

### 12.5 Members

A shape that holds other shapes records them, in order, as a list of **members**: a container on its
whole body (the record's `members`), and swimlanes lane by lane (each lane part's `members`). A member
is an object of the same form a group member has, with exactly these two keys; any other refuses the
document:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `kind` | string | Yes | `element` or `group` | What the member is |
| `id` | UUID | Yes | An element or group on the **same page** | Which one |

A connector is never a member: a member of kind `connection` refuses the document. A member list MUST
NOT repeat an entry. A container holds at most 1,000 members, a lane at most 1,000, and all the lanes
of one swimlanes shape at most 2,000 together ([12.11](#1211-how-much-a-shape-and-a-page-hold)).
Members are references only: each member keeps its own position in page coordinates, and the reader
does not check that a member lies inside its holder's frame. The rules that involve other shapes on the
page are in [12.10](#1210-rules-across-the-page).

### 12.6 The element's own keys

A complex shape is an ordinary element in every other way: it has a frame, a rotation, a style, a
stacking position, a lock, connection points and shape data. In addition:

- **Text.** `text_content` is the shape's own text where its type has one: a container's title, the
  title across the top of swimlanes, a callout's words, a lifeline's name, a gauge's caption. A type
  without text of its own (a table, an entity table, a ring, a flow, a pyramid, a cycle) keeps its
  `text_content.string` empty, so that no hidden words travel in files, searches or exports. The
  entry places the shape's own text, so `text_layout` is forbidden on every complex shape; a type whose
  entry allows it records a place a person chose in `own_text_layout` instead.
- **Flips.** Among the types the catalogue lists, only a callout may be flipped: on every other
  listed type, `flip_horizontal` and `flip_vertical` MUST be `false`. A shape of a type or version the
  reader does not know keeps its flips as written, and a reader MUST NOT refuse a document for them
  ([12.8](#128-reading-a-complex-shape)).
- **Connection points.** An entity table, a ring, a flow, a pyramid, a cycle and a gauge **place their
  own points**: their element MUST list none, `"connection_points": []`, and the reader works the points
  out from the shape as it reads the document. Their numbers are fixed: points 0 to 7 as the entry
  gives them, and from 8 up the numbers the parts list. Each entry says where its points are. Every
  other complex shape lists its points as any element does ([section 8](#8-connection-points)).
- **Proportions.** `preserves_aspect_ratio` MUST NOT be `true` on a ring, a flow, a pyramid, a cycle or a
  gauge: each entry says how its shape resizes.
- **Style.** `style` gives the fill and line as for any shape ([section 5](#5-style)); a part's own
  `style` overrides it channel by channel ([12.3](#123-parts)).

### 12.7 The catalogue

What each type holds is not on this page. It is in the **complex-shape catalogue**,
[complex-shapes/](complex-shapes/README.md): one entry for each type and version SimpleDiagrams knows,
each with a page that explains the shape and a JSON Schema, `<version>.schema.json`. An entry declares
the shape's settings, with their kinds, ranges, words and defaults; its roles, in order, with their
counts and the keys each role's parts hold; whether it holds members on its whole body, has text of its
own, lets a person place that text, and places its own connection points; the rules that relate its
values to each other; and, informatively, how the app draws it.

**How the catalogue grows.** A later SimpleDiagrams release, within document format 19, may add a new
type, or a new version of a type already listed, each as a new entry with its own page and schema.
Nothing else changes: an entry, once published, never changes, its lists of words stay closed, and the
structure of [12.1](#121-the-structure) stays as it is. A new setting, role, key or word for an existing
type is a new version. So a document that was valid stays valid, and means what it meant. Each
addition is recorded in the repository's CHANGELOG.

### 12.8 Reading a complex shape

A reader holds every complex shape to the structure, [12.1](#121-the-structure) to
[12.6](#126-the-elements-own-keys), whether or not it knows the type. Then:

**A type and version the reader knows.** The record MUST hold exactly what the entry declares: every
setting it requires and no other, each of its kind, range and words; `members` exactly when the entry
holds members on its whole body; `own_text_layout` only where the entry allows it; parts in the
entry's roles and order, each role's count in its range; each part with every key its role requires
and no key its role does not allow; and each part's points as the entry declares. And it MUST keep the
entry's rules. An unknown key, role or word in a known type and version refuses the document: such a
record is never read as one from a later version to escape a check.

**A type, or a version of a type, the reader does not know.** The document opens: such a shape comes
from a later release of SimpleDiagrams, which can draw it. The reader keeps the record as it read it
and writes it back unchanged, except for the settling of its references below, and MUST NOT refuse it
for settings, roles, keys or words it does not know, or for its element's flips. It is still held to
the structure, to the rules of the element's own keys that hold for every type, and to the rules across
the page: its members are held as any holder's, so a shape that holds members counts among a page's
containers and swimlanes ([12.11](#1211-how-much-a-shape-and-a-page-hold)) and is never in a group;
its attachment settings follow the rules of a callout's locked tail
([12.10](#1210-rules-across-the-page)); and every embedded asset its settings name is one the document
holds. Its placed texts are not checked against its frame, since only its entry could say where they
belong.

**Informative: what the app does with one.** The app draws it as a labelled box in its frame, "Made with
a newer version of SimpleDiagrams." It can be moved, resized, turned, deleted, copied and locked, and
grouped unless it holds shapes, as no holder may be in a group; it cannot be painted, have its text
edited or have a part chosen. Its element's flips are kept as written. The app changes such a record
only to keep its references true, as it does a container's: a member that names a shape the person
deletes is dropped; a group that is ungrouped gives back its members in its place; members that a person
groups are replaced by their outermost group, where every shape of that group was already a member,
and otherwise leave the list; and an attachment setting whose shape or connection point is gone is
dropped. When
its element lists connection points, they are kept. When it lists none, the app gives it the standard
eight on its frame ([section 8](#8-connection-points)) and puts each point its parts list at the frame's
centre, so every connector attached to it still has a point to end on; it writes the list back empty.

### 12.9 Which message a fault gives

A fault in the **structure** of a complex shape ([12.1](#121-the-structure) to
[12.5](#125-members)) is found while the JSON is read, and gives the general message for an invalid
`document.json`: "The SimpleDiagrams document contains an invalid document.json file." That covers an
unknown key, a `null`, a value of the wrong kind, a word that breaks the spelling rule, a `version` out
of range, a list past its limit, a part identifier used twice, a mark repeated on one part, a point
numbered below 8 or listed twice, a part's paint without a channel or with a texture beside no colour, and a placed text set
`above` or `below`. It holds whether or not the reader knows the type.

Every other fault refuses the document with one message naming the shape: "The shape *id* is not a
valid complex shape.", *id* being the element's identifier. That covers a fault against the entry's
declarations, such as a missing or undeclared setting, a word the entry does not list, parts out of
order or past their counts, or a key a role does not have; a fault against the entry's rules; a fault
in the element's own keys ([12.6](#126-the-elements-own-keys)), such as a flip or listed points; a
member list that repeats an entry or names a connector; an embedded asset a shape of an unknown type
names that the document does not hold; and a broken rule across the page.

Five exceptions:

- a fault in the element's frame, position or rotation gives the message any element gives
  ([section 3](#3-identity-frame-rotation-and-flips)), such as "The diagram contains an invalid number
  in element width.";
- a fault in the shape's own text, a part's text or a cell gives the messages any text gives
  ([section 6](#6-text-content));
- a `text_layout` on the element gives "…invalid text in unexpected element text layout."
  ([section 10](#10-which-kind-may-carry-which-key));
- a member list longer than 1,000, and a shape or page holding more than it may, give the message of
  its limit ([12.11](#1211-how-much-a-shape-and-a-page-hold)), however long the list is;
- an embedded-asset setting of a known type that names no asset of the kind its entry requires gives
  the message of an asset reference ([assets.md](assets.md)), such as "…invalid text in container artwork reference.".

### 12.10 Rules across the page

These rules involve more than one shape and are checked with the rest of the page. A broken rule
refuses the document with the complex-shape message, naming the holder or the shape whose attachment
is broken. A **holder** here is a shape with a place to hold shapes: a container, swimlanes, or a shape
of a type the reader does not know that has `members` on its body or on a part.

1. Every member names an element or group on the **same page** as its holder, and never the holder
   itself.
2. Swimlanes are never a member of a holder.
3. An element or group is a member of at most one holder.
4. A group can be a member only as a whole, and only a group that is not itself inside another group:
   no element or group that is inside a group may also be a member on its own.
5. A holder MUST NOT be a member of any group. (A container holding it does that job.)
6. Holders nest at most **4** levels deep (a holder inside a holder inside a holder inside a holder),
   with no loops.
7. An attachment setting, such as a callout's locked tail, names a connection point that another
   element on the same page has. A callout's tail, as drawn, lies within ±1,000,000,000 on both axes
   ([callout](complex-shapes/callout/README.md)).

Nothing compares a complex shape, its members or a callout's tip with the page's stored `width` and
`height`; "on the page" means within the coordinate limits.

**Text budgets.**

| Budget | Limit | Refusal example |
| --- | --- | --- |
| One shape's text other than a table's: its own `text_content` and every part's `text` together | 1,000,000 bytes of UTF-8 and 100,000 runs | "…invalid text in text content." / "…too many text-formatting runs (*n*)." |
| Part texts and cells across the whole document | 100,000 | "The diagram contains too many lane titles and table cells (*n*)." |

A table's cells, and how much a page's complex shapes hold, have limits of their own
([12.11](#1211-how-much-a-shape-and-a-page-hold)).

**Informative: drawing order.** A holder is drawn, and clicked, just behind the lowest thing it holds: a
member, a member of a group it holds, a holder inside it, or a connector joined to one of these. Inner
holders are placed first, so a container inside another lands behind its own contents and the outer
one behind it. This drawn order is worked out every time and never stored; a writer cannot put a
container in front of its own contents.

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
| Characters in all the part texts and cells of one page's complex shapes together | 1,000,000 | "The diagram contains a page with too much text in its complex shapes (*n* characters)." |

*n* is the count the document holds, written as numbers are in the reader's language, for example
`1,001` in English. A shape's own `text_content` is held to the limits every element's text has
([section 6](#6-text-content)), not to these. An entity table's cells are a table's cells here. The
texts of a ring's sections, a flow's steps, a pyramid's layers, a cycle's steps and a gauge's bands have
a limit of their own, 500 characters each, given in their entries. When a document breaks more than one
limit, which message it gets is not specified.

### 12.12 An example

A ring of one section, as the app's Ring tile places it: 200 by 200 points, its band 40 points wide.
The section's text reads "Customers" (shortened here; it is a complete text record, [section 6](#6-text-content)).

```json
{
  "type": "ring",
  "version": 1,
  "settings": {"ring_width": 40},
  "parts": [
    {
      "id": "7DD1BEF2-4998-5B81-A60A-2A1CE5D1AA81",
      "role": "section",
      "text": "… Customers …",
      "point_ids": [8]
    }
  ]
}
```

That is the whole record: the app draws the band, the section's outline, where its text sits and where
its point lies from `ring_width`, the one section and the element's frame. The element beside it has
an empty `text_content` and `"connection_points": []`; as the document is read, the ring gets points 0
to 7 on its circle and point 8, the section's, at the middle of its outer edge
([ring](complex-shapes/ring/README.md)). The complete element is in the
[`complex-shapes`](examples/complex-shapes.simplediagram/) example, beside a flow, a pyramid, a cycle
and a gauge.

## 13. Enumerated values

Every value below is stored exactly as shown, and is case-sensitive. An unknown value makes the app
refuse the document, except in two open lists: `marker_tip`, which is read as absent, and `note_style`,
which is read as a sticky note ([13.2](#132-note_style)). A shape of a complex-shape type or version the
reader does not know is not refused but opened as a placeholder ([12.8](#128-reading-a-complex-shape)).

### 13.1 Element and complex shape values

| Where | Values |
| --- | --- |
| Element `kind` | `rectangle`, `ellipse`, `note`, `text`, `symbol`, `editable_vector`, `svg_artwork`, `image`, `path`, `unresolved`, `complex_shape` |
| `complex_shape.type` in document format 19's catalogue | `container`, `swimlanes`, `table`, `entity_table`, `callout`, `lifeline`, `ring`, `flow`, `pyramid`, `cycle`, `gauge` |
| Part `text_layout.position` and `own_text_layout.position` | `top`, `middle`, `bottom` |
| Member `kind` | `element`, `group` (`connection` is read but refused) |

The settings' words, the roles and the marks of each type are listed in its
[catalogue entry](complex-shapes/README.md).

### 13.2 `note_style`

| Value | Meaning |
| --- | --- |
| `stickyNote` | A sticky note (also the meaning when the key is absent) |
| `indexCard` | An index card with ruled lines |
| `tapedNote` | A scrap of paper held up by clear tape |
| `tag` | A luggage tag with a hole and string |
| `notebookPage` | A ruled notebook page with punched holes |
| `pinnedNote` | Paper held by a grey push pin |

An open list: a reader draws any other word as a sticky note ([11.3](#113-note)).

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

## 14. Open points

1. **Grapheme cluster boundaries across systems.** The rule is the one the app computes: it runs on
   macOS 15 and later and finds boundaries with the system's Unicode rules, which follow the Unicode
   version of that macOS release. For characters and emoji sequences added in recent Unicode versions,
   two systems can disagree about a boundary. A writer SHOULD keep runs to boundaries that are stable
   across Unicode versions: for example, not inside an emoji sequence added in a recent Unicode
   version.
2. **Drawing details** such as text measurement and wrapping, texture tile sizes, ending sizes, marker
   ink shapes and the hand-drawn pen's wobble are the app's own presentation. They do not affect
   whether a document is valid, and are not specified here. So is how each complex shape is drawn from
   its settings: a later release may refine it, and the refinement reaches documents already saved,
   without changing what any of them means.
