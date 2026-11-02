# Embedded assets

Artwork and bitmap images that are not shipped inside the app are stored in the document as files in its
`assets/` folder. Each file is named after the SHA-256 digest of its own bytes. `document.json` lists
every one of them in `embedded_assets`, and the list and the folder must agree exactly. Elements,
containers and page backgrounds refer to an asset by that digest.

This page defines the asset folder and its inventory, the rules each kind of asset must meet, and the
**vector artwork format** (`.sdvector`), a JSON format of its own with its own version number.
Conventions and limits are in [README.md](README.md).

On this page:

- [Asset kinds](#asset-kinds)
- [The inventory](#the-inventory)
- [Which assets may be referenced](#which-assets-may-be-referenced)
- [PNG and JPEG images](#png-and-jpeg-images)
- [SVG artwork](#svg-artwork)
- [The vector artwork format (.sdvector)](#the-vector-artwork-format-sdvector)
- [Content identity: asset_id](#content-identity-asset_id)
- [Test vectors](#test-vectors)
- [Open points](#open-points)

## Asset kinds

| `kind` | File extension | What the bytes are | Rules |
| --- | --- | --- | --- |
| `editable_vector` | `sdvector` | Vector artwork in the vector artwork format: JSON layers of paths whose paint can follow the placed shape's colours | [The vector artwork format](#the-vector-artwork-format-sdvector) |
| `svg_artwork` | `svg` | An SVG file, stored and drawn exactly as its author made it | [SVG artwork](#svg-artwork) |
| `raster_png` | `png` | A PNG image | [PNG and JPEG images](#png-and-jpeg-images) |
| `raster_jpeg` | `jpg` | A JPEG image | [PNG and JPEG images](#png-and-jpeg-images) |

**Two digests, two jobs.** Every asset is identified by the SHA-256 digest of its file's bytes; that is
its inventory `id` and its file name. An `editable_vector` asset also carries, inside the file, an
`asset_id`: the digest of a canonical encoding of what the artwork means
([Content identity](#content-identity-asset_id)). The two are different values and are checked
separately.

## The inventory

The top-level key `embedded_assets` of `document.json` is required. It is an array with one entry per
file in `assets/`, and `[]` when the folder is empty. A writer always writes the folder, even empty; a
reader treats a package without it as having an empty one, so `[]` with no folder opens and any entry
with no folder is refused ([Package rules](README.md#package-rules)).

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | string | Yes | Exactly 64 lower-case hexadecimal digits: the SHA-256 digest of the file's bytes | The asset's identity. Elements, containers and backgrounds refer to it |
| `kind` | string | Yes | `editable_vector`, `svg_artwork`, `raster_png` or `raster_jpeg` | What the bytes are |
| `filename` | string | Yes | Exactly `<id>.<extension>`, the extension fixed by `kind` ([Asset kinds](#asset-kinds)) | The file's name in `assets/` |
| `byte_count` | whole number | Yes | Equal to the file's size in bytes | A check that the right file is present |

A writer MUST meet every one of these rules:

1. Every file in `assets/` is listed exactly once, and every listed file is there. `assets/` holds the
   listed files and nothing else: no hidden files (a name starting with `.`, such as `.DS_Store` or
   `.gitkeep`), no folders, and no symbolic links.
2. No two entries share an `id` or a `filename`.
3. `id` is the SHA-256 digest of the file's exact bytes, written as 64 lower-case hexadecimal digits.
   The app computes the digest again every time a document opens.
4. The bytes are valid for their `kind`, as this page defines.
5. At most 10,000 assets; each file at most 16 MiB (16,777,216 bytes); all together at most 100 MiB
   (104,857,600 bytes).

The inventory entries are lenient records: the app ignores keys it does not know. Entries may be in any
order. **Informative.** The app writes them sorted by `id`.

```json
{
  "embedded_assets": [
    {
      "id": "fcb99190ee9e0b7a7ddfdb70fc7d4da3e029fddbb3ce1c387a47c80a522e9ade",
      "kind": "editable_vector",
      "filename": "fcb99190ee9e0b7a7ddfdb70fc7d4da3e029fddbb3ce1c387a47c80a522e9ade.sdvector",
      "byte_count": 582
    }
  ]
}
```

(That entry describes the file of [test vector 1](#test-vector-1-vector-artwork-format-1), written
exactly as given there.)

### What the app refuses

| Fault | Message (English example) |
| --- | --- |
| An unlisted file, a missing file, a hidden file, a repeated entry, a `filename` or `byte_count` that does not match, an `id` that is not the file's digest, an unknown `kind`, or bytes not valid for their kind | "The SimpleDiagrams document contains an invalid assets file." |
| A folder or a link inside `assets/`, or one file over 16 MiB | "The SimpleDiagrams document contains an invalid assets/*name* file." |
| All assets together over 100 MiB | "The assets file is too large (*n* bytes)." |

**Why content addressing.** Identical artwork is stored once however many shapes use it; an asset's
name proves what it contains; and the app can carry an unchanged asset into the next save without
rewriting it, because the name says whether the bytes are still the same.

## Which assets may be referenced

An asset is **reachable** when something on a page names its `id`. Each user accepts only certain
kinds:

| User | Key | Asset kinds it may name |
| --- | --- | --- |
| An `editable_vector` element | `embedded_asset_id` ([elements.md](elements.md)) | `editable_vector` |
| An `svg_artwork` element | `embedded_asset_id` | `svg_artwork` |
| An `image` element | `embedded_asset_id` | `raster_png` or `raster_jpeg` |
| A container (a `complex_shape` of type `container`) | Its `artwork` setting, `{"asset_id": …}` ([container](complex-shapes/container/README.md)) | `editable_vector` |
| A page with an image background | the background's `asset_id` ([README.md, Background](README.md#background)) | `raster_png` or `raster_jpeg` |

A reference MUST name an asset in the inventory, of a kind its user accepts. Otherwise the document is
refused with "The diagram contains invalid text in embedded asset reference." (or "…page background
asset reference.", or "…container artwork reference.").

Many users MAY name the same asset. An unreachable asset is allowed in a file. **Informative.** The app
drops unreachable assets the next time it saves.


## PNG and JPEG images

A `raster_png` or `raster_jpeg` asset MUST meet these rules:

- The bytes are a PNG (for `raster_png`) or a JPEG (for `raster_jpeg`). The format is identified from the
  bytes, not from the file name.
- Exactly one frame. An animated image is refused.
- Width and height each from 1 to 16,384 pixels, and at most 40,000,000 pixels in all.
- At most 16 MiB.

**Informative.** When the app opens a document it reads only each image's header, so a document full of
images does not have to decode every bitmap image to open. When a person imports a bitmap image, the app
re-encodes it as PNG or JPEG, which removes camera and location metadata. A program writing a document
SHOULD do the same when the bitmap image may carry private metadata.

## SVG artwork

An `svg_artwork` asset holds the exact bytes of an SVG file. The app stores it unchanged and draws it
with the macOS SVG renderer, so it MUST pass a safety profile, which the app checks every time a document
opens. The profile refuses *active* or externally loaded content, not unfamiliar markup: an element
that is not forbidden below is allowed, whether or not the app understands it.

### Structure

- The file MUST be well-formed XML, encoded as UTF-8, with no NUL byte.
- The root element MUST be `svg`. Element names are compared by local name, ignoring letter case and any
  namespace prefix.
- The file MUST NOT contain a document type declaration (`<!DOCTYPE`), an entity declaration
  (`<!ENTITY`), a notation, or a processing instruction. An XML declaration (`<?xml version="1.0"?>`) is
  allowed. Comments are allowed.
- CDATA sections MAY appear only directly inside a `style` element.

### Size limits

| What | Limit |
| --- | --- |
| The whole file | 16 MiB |
| Elements | 100,000 |
| Nesting depth | 128 |
| Attributes on one element | 128 |
| An element or attribute name | 256 bytes |
| Attribute values | 16 MiB |
| Text and CDATA together | 4 MiB |
| One text node | 2 MiB |
| One `style` element's text | 2 MiB |

### Forbidden elements

None of these elements may appear, under any prefix or letter case: `a`, `animate`, `animateColor`,
`animateMotion`, `animateTransform`, `audio`, `canvas`, `discard`, `embed`, `foreignObject`, `iframe`,
`object`, `script`, `set`, `video`.

These filter primitives are also forbidden, because their drawing cost has no safe bound:
`feConvolveMatrix`, `feDisplacementMap`, `feImage`, `feMorphology`, `feTile`, `feTurbulence`. An
`feGaussianBlur` with a `stdDeviation` MUST give one or two numbers (separated by white space or a
comma), each from 0 to 100.

### Attributes and references

- No attribute whose local name starts with `on` (event handlers), and no attribute whose local name is
  `base`.
- No attribute value may contain, in any letter case, `javascript:`, `vbscript:`, `data:`, `file:`,
  `http:`, `https:`, or a backslash. Two exceptions: namespace declarations (`xmlns` and
  `xmlns:prefix`) are not checked, and bitmap images in the form described under
  [Bitmap images inside SVG artwork](#bitmap-images-inside-svg-artwork) are allowed.
- An `href` or `src` attribute (under any prefix, so `xlink:href` counts) MUST be a **local reference**:
  `#` followed by 1 to 256 bytes, each character a Unicode letter, mark or number, or one of `.` `_` `:`
  `-`. So `#a%20b` and `#a b` are refused.
- Every `url(...)` in any attribute value MUST hold a local reference, optionally in quotes.
- A `style` element's text MUST NOT contain `@`, a backslash, or any of `javascript:`, `data:`, `file:`,
  `http:` or `https:`, and every `url(...)` in it MUST hold a local reference.
- Local references MUST NOT form a loop, whether through `use` or through paint servers (such as
  gradients) that inherit from one another. A `use` MUST name exactly one existing element, and the
  drawing it expands to MUST stay within the element and depth limits above. A gradient or filter
  reference that names nothing is allowed.

### Bitmap images inside SVG artwork

An SVG MAY carry small PNG bitmap images (textures) in one form only, chosen so that it cannot hold camera
or location metadata:

- only in an `image` element's `href` attribute (under any prefix, so `xlink:href` works; not `src`);
- the value starts with `data:image/png;base64,` (letter case ignored), followed by base64 with no white
  space or line breaks;
- at most 4 bitmap images in one SVG, each from 1 to 1,024 pixels a side, together at most 1,048,576 pixels
  and 4,259,840 bytes of PNG data after base64 decoding (4 MiB plus 64 KiB);
- each PNG is: the PNG signature; an `IHDR` chunk; then, in any order, at most one each of `sRGB`
  (exactly 1 byte of data), `gAMA` (exactly 4) and `cHRM` (exactly 32); then one or more consecutive
  `IDAT` chunks; then an empty `IEND` chunk, with nothing after it. No other chunk is allowed;
- bit depth 8; colour type 0, 2, 4 or 6 (no palette); compression, filter and interlace methods all 0;
- the joined `IDAT` data is one complete zlib stream that inflates to exactly
  height × (1 + width × samples per pixel) bytes, where samples per pixel is 1, 3, 2 or 4 for colour
  types 0, 2, 4 and 6;
- and the result is a valid one-frame PNG under the rules of [PNG and JPEG images](#png-and-jpeg-images).

JPEG images, SVG images and images in any other form are refused.

### Size of the drawing

The drawing's natural size comes from the root element's `viewBox` when it holds four finite numbers;
failing that, from absolute `width` and `height` attributes (in `px`, `pt`, `pc`, `in`, `cm`, `mm` or
`q`, or no unit); failing that, 300 by 150. The size is in CSS pixels: `viewBox` numbers and lengths in
`px` or with no unit are taken as they are, and other units are converted at 96 pixels to the inch (so
`1in` is 96, `1pt` is 4/3 and `1cm` is 96 ÷ 2.54). A `viewBox` of four numbers whose width or height
lies outside 0.000001 to 10,000,000 refuses the asset. The artwork is stretched to its element's
frame.

**Informative.** When a person imports an SVG, the app also applies a drawing-cost budget and may
simplify the file. That budget is deliberately not applied when a document opens, so a saved document
never stops opening because of it. A program writing SVG artwork SHOULD keep it simple; the app may
draw very complex artwork slowly.

### What the app refuses

Any breach refuses the whole document with "The SimpleDiagrams document contains an invalid assets
file."

## The vector artwork format (.sdvector)

An `editable_vector` asset is a JSON file describing artwork as ordered **layers** of paths, each with a
fill, a line (stroke) or both. Some paints are **linked**: instead of a colour of their own, they take
the placed shape's fill or line colour, which is how one piece of artwork is recoloured per shape.

The format has its own identifier, `com.simplediagrams.vector-asset`, and its own version number, the
**vector artwork format version** (the file's `schema_version`), independent of the document format.

### Versions of the vector artwork format

| Version | Adds |
| --- | --- |
| 1 | A flat list of layers |
| 2 | Compositing groups |
| 3 | Pinned parts |
| 4 | Outlines, with or without pinned parts |

A document format 19 document MAY contain assets of any of the four versions.

Each asset MUST use the **lowest** version that can carry it: 4 when it has outlines; otherwise 3 when
it has pinned parts; otherwise 2 when it has compositing groups; otherwise 1. Concretely:

| Version | `compositing_groups` | `pins` | `outlines` |
| --- | --- | --- | --- |
| 1 | absent | absent | absent |
| 2 | present, not empty | absent | absent |
| 3 | optional | present, not empty | absent |
| 4 | optional | optional | present, not empty |

The app reads an empty list (or `null`) as an absent key where the table says "absent"; a writer SHOULD
leave such a key out.

**Informative.** In the app, only the shapes shipped inside it carry versions 3 and 4; such artwork
reaches a document when a person makes a shipped shape editable. Shape libraries that people download
or make themselves are held to versions 1 and 2.

### Reading rules

The vector artwork reader is **lenient**, unlike the strict records of `document.json`. The app:

- ignores keys it does not know, at any level;
- reads `null` as an absent optional key;
- reads a whole-number field written as `1.0` or `1e2` as that whole number (but refuses `1.5`);
- uses the first of two repeated keys.

A writer MUST NOT rely on any of this. It MUST write only the keys this page defines, each key once, and
MUST leave optional keys out rather than write `null`. Unknown keys do not change `asset_id`, which
is computed from the values the reader keeps, but they stay in the file's bytes and so in its file name.

The file MAY use any white space and key order. **Informative.** The app writes it as compact JSON with
keys sorted and `/` not escaped.

### Top level

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `format` | string | Yes | Exactly `com.simplediagrams.vector-asset` | Identifies the format |
| `schema_version` | whole number | Yes | 1 to 4, the lowest that can carry the artwork ([Versions](#versions-of-the-vector-artwork-format)) | The vector artwork format version |
| `asset_id` | string | Yes | 64 lower-case hexadecimal digits, equal to the artwork's content identity ([Content identity](#content-identity-asset_id)) | The artwork's identity, independent of where it came from |
| `source_view_box` | rectangle | Yes | `x`, `y` within ±1,000,000,000; `width`, `height` from 0.000001 to 10,000,000 | The coordinate box that path coordinates are expressed in |
| `intrinsic_size` | object `{width, height}`, diagram points | Yes | Each from 0.000001 to 10,000,000 | The artwork's natural size |
| `source_sha256` | string | No | 64 lower-case hexadecimal digits | The digest of the file the artwork was converted from, kept for provenance only |
| `layers` | array of layers | Yes | 1 to 50,000 | The layers, drawn in order: the first is at the back |
| `compositing_groups` | array | Version 2: yes. Versions 3 and 4: optional. Version 1: absent | 1 to 4,096 when present | Opacity applied to runs of layers as a unit ([Compositing groups](#compositing-groups)) |
| `pins` | array | Version 3: yes. Version 4: optional. Versions 1 and 2: absent | 1 to 1,024 when present | Runs of layers that keep their drawn size ([Pinned parts](#pinned-parts)) |
| `outlines` | array | Version 4: yes. Versions 1 to 3: absent | 1 to 64 when present | Layers redrawn for the frame they are placed in ([Outlines](#outlines)) |

The whole file MUST be at most 16 MiB.

### How the artwork fits its frame

Path coordinates are in `source_view_box` space. Everything except pinned parts and outlines is
stretched to the element's frame, scaling x and y independently:

```text
frame x = frame.x + (x - view_box.x) * frame.width  / view_box.width
frame y = frame.y + (y - view_box.y) * frame.height / view_box.height
```

The element's flips and rotation are then applied ([elements.md](elements.md#3-identity-frame-rotation-and-flips)). Line widths and dash
lengths are **not** stretched: they are in diagram points whatever the frame's size. `intrinsic_size` is
the artwork's natural size in diagram points; the element's frame, not this value, decides the size it
is drawn at.

### A layer

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `path` | object `{"commands": [...]}` | Yes | At least one command ([Path commands](#path-commands)) | The layer's shape |
| `fill` | object | No | [Fill](#fill-stroke-and-paint). Absent: no fill | How the inside is painted |
| `stroke` | object | No | [Stroke](#fill-stroke-and-paint). Absent: no line | How the outline is drawn |
| `clip_paths` | array of clip paths | Yes | 0 to 16. `[]` for none | Shapes the layer is clipped to |
| `opacity` | number | Yes | 0 to 1 | The layer's overall opacity |

A layer MUST have a `fill`, a `stroke`, or both.

A **clip path** is `{"path": {"commands": [...]}, "rule": "nonzero" | "even_odd"}`, both keys required.
The layer is drawn only where it falls inside every one of its clip paths.

### Path commands

Each command is a JSON array: a one-letter operation followed by its coordinates, in view-box units.

| Command | Meaning | Numbers after the letter |
| --- | --- | --- |
| `["M", x, y]` | Start a new sub-path at a point | 2 |
| `["L", x, y]` | Straight line to a point | 2 |
| `["Q", cx, cy, x, y]` | Quadratic curve: control point, then end point | 4 |
| `["C", c1x, c1y, c2x, c2y, x, y]` | Cubic curve: first control point, second control point, end point | 6 |
| `["Z"]` | Close the current sub-path | 0 |

Rules:

- Each command has exactly the numbers shown, no more and no fewer.
- The first command of a path is `M`. `L`, `Q`, `C` and `Z` need an open sub-path: after `Z`, an `M` is
  needed before the next drawing command. Several `M`s in a row are allowed.
- At least one command draws something; `Z` counts, so `M x y Z` is a valid path.
- Every coordinate is within ±1,000,000,000.
- All the commands of an asset, in layers and clip paths together, number at most 250,000.

### Fill, stroke and paint

`fill` (all keys required):

| Key | Type | Rules / bounds | Meaning |
| --- | --- | --- | --- |
| `paint` | paint | Below | Where the colour comes from |
| `rule` | string | `nonzero` or `even_odd` | How overlapping parts of the path decide what is inside |
| `opacity` | number | 0 to 1 | Fill opacity |

`stroke` (all keys required):

| Key | Type | Rules / bounds | Meaning |
| --- | --- | --- | --- |
| `paint` | paint | Below | Where the colour comes from |
| `width` | number, diagram points | 0 to 10,000 | Line weight. Replaced by the placed element's line width when the paint is `linked_diagram_stroke` |
| `line_cap` | string | `butt`, `round` or `square` | How open ends are finished |
| `line_join` | string | `miter`, `round` or `bevel` | How corners are joined |
| `miter_limit` | number | 1 to 10,000 | When a sharp mitred corner is cut off |
| `dash_array` | array of numbers, diagram points | 0 to 1,024 entries, an even number of them, each 0 to 10,000, not all zero. `[]` for a solid line | Dash and gap lengths |
| `dash_offset` | number, diagram points | Within ±1,000,000,000 | Where the dash pattern starts |
| `opacity` | number | 0 to 1 | Line opacity |

`paint`:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `kind` | string | Yes | `authored`, `linked_diagram_fill` or `linked_diagram_stroke` | Where the colour comes from |
| `color_hex_rgba` | string | With `authored` only; MUST be absent for the linked kinds | Exactly `#RRGGBBAA`: nine characters, **upper-case** hexadecimal digits, alpha last | The colour |

What each paint kind uses from the element that shows the artwork (its `style`, [elements.md](elements.md#5-style)):

| `kind` | Colour | Texture | Line weight of a stroke |
| --- | --- | --- | --- |
| `authored` | `color_hex_rgba` | none | The stroke's own `width` |
| `linked_diagram_fill` | The element's `fill_hex` | The element's `fill_texture` | The stroke's own `width` |
| `linked_diagram_stroke` | The element's `stroke_hex` | The element's `stroke_texture` | The element's `line_width` |

The kind says which of the element's colours to use, not whether the layer is a fill or a line. A solid
region painted `linked_diagram_stroke` is filled with the shape's line colour; that is how the dark
"ink" parts of a drawing follow the line colour while the light parts follow the fill colour. A linked
paint stores no fallback colour: the placed element's style is authoritative. An element's
`stroke_pattern_override` changes the dashes only of `linked_diagram_stroke` lines
([elements.md](elements.md)).

### Compositing groups

A compositing group applies one opacity to a run of consecutive layers drawn together, so that overlaps
inside the run do not show through each other.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `first_layer_index` | whole number | Yes | 0 or more | The run's first layer, counting from 0 |
| `layer_count` | whole number | Yes | Above 0; the run MUST end within `layers` | How many layers the run holds |
| `opacity` | number | Yes | 0 to 1 | The opacity applied to the run as a whole |

Rules:

- Groups are listed in order of `first_layer_index`. Where two start at the same layer, a longer one
  never follows a shorter one (outermost first; equal lengths are allowed).
- Two groups are either nested or separate, never partly overlapping.
- Groups nest at most 16 deep.
- The `layer_count` values add up to at most 250,000.

### Pinned parts

A **pinned part** is a run of consecutive layers that keeps the size it was drawn at, and its distance
from one spot of the frame, while the rest of the artwork stretches: for example, a task's
small icon in its corner, or the plus sign on a collapsed sub-process. Vector artwork formats 3 and 4 only.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `first_layer_index` | whole number | Yes | An index into `layers` | The part's first layer |
| `layer_count` | whole number | Yes | At least 1; the part MUST end within `layers` | How many layers, from the first, make the part |
| `anchor` | string | Yes | `top-left`, `top`, `top-right`, `left`, `center`, `right`, `bottom-left`, `bottom`, `bottom-right` | The spot of the frame the part keeps its distance from |

Pinned parts are listed in layer order and never overlap.

**Where a pinned layer is drawn.** With view box origin `(vx, vy)` and size `(vw, vh)`, intrinsic size
`(iw, ih)`, frame size `(W, H)`, and the anchor's spot `(ax, ay)` as fractions of the frame (0, 0.5 or 1
on each axis: `top-left` is `(0, 0)`, `center` is `(0.5, 0.5)`, `bottom-right` is `(1, 1)`), a point `p`
of a pinned layer is placed, relative to the frame's top-left corner and before flips and rotation, at:

```text
k = min(1, W / iw, H / ih)
x = ax * W + (p.x - (vx + ax * vw)) * (iw / vw) * k
y = ay * H + (p.y - (vy + ay * vh)) * (ih / vh) * k
```

So the part keeps its drawn size when the frame is at least the intrinsic size, and shrinks evenly, by
the smaller of the two shares, when the frame is smaller. At exactly the intrinsic size this equals the
stretching mapping, so a shape at its natural size looks the same whether or not parts are pinned. Line
widths are not scaled.

### Outlines

An **outline** marks a layer that the reader draws afresh for the frame it is placed in, instead of
stretching it: its corner radius, its distance from each edge of the frame and its line keep the size
they were drawn at, and only its straight sides grow. Vector artwork format 4 only.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `layer_index` | whole number | Yes | An index into `layers` | The layer the outline draws |
| `kind` | string | Yes | `hand_drawn_box`, `folded_corner_box`, `folded_corner_crease`, `tabbed_box`, `tabbed_box_divider` | What it draws (below) |
| `box` | rectangle, view-box units | Yes | Positive width and height; inside `source_view_box`, with a tolerance of 1e-9 × the larger of 1, the view box's width and its height | The box at the drawn size |
| `corner_radius` | number, view-box units | Yes | 0 to half the box's smaller side | How round its corners are |
| `offsets` | array of numbers, view-box units | Yes | 1 to 4 entries, strictly increasing, each from 0 to `corner_radius` | Distances inwards at which the layer draws the box: `[0]` for the box itself |
| `fold` | number, view-box units | The folded-corner kinds only | Above 0 and no longer than the box's shorter side | The length of each side of the cut corner |
| `tab` | object `{width, height}`, view-box units | The tabbed kinds only | Width above 0 and less than the box's width; height above 0 and at most half the box's height | The tab's size at the drawn size |

| `kind` | What it draws | Measures it takes |
| --- | --- | --- |
| `hand_drawn_box` | A rounded box | `corner_radius` and `offsets`; no `fold`, no `tab` |
| `folded_corner_box` | A box with its top-right corner cut off, as on a UML note | `corner_radius` 0, `offsets` `[0]`, `fold`; no `tab` |
| `folded_corner_crease` | The crease inside a `folded_corner_box` of the same box: an open line down from where the cut starts, then across to where it ends | As `folded_corner_box` |
| `tabbed_box` | A box with a tab on the left of its top edge, as on a UML package; the box includes the tab | `corner_radius` 0, `offsets` `[0]`, `tab`; no `fold` |
| `tabbed_box_divider` | The line under the tab of a `tabbed_box` of the same box, open at both ends | As `tabbed_box` |

Rules:

- At most one outline per layer; outlines are listed in increasing `layer_index`.
- An outline's layer MUST NOT belong to a pinned part, and MUST have no clip paths.
- An outline that breaks a rule makes the asset invalid, and so refuses the document.

The layer keeps its own path, as drawn at the artwork's intrinsic size, and its own paint. A reader that
does not redraw outlines MAY draw that path stretched like any other layer; the result is the right
drawing at the natural size.

**Informative.** The app draws an outline with its own hand-drawn pen, whose wobble is worked out from
the box and cannot be reproduced from this specification. When the frame is smaller than the intrinsic
size, the outline shrinks evenly by the same share as a pinned part. A fold keeps its drawn size; a tab's
width keeps its share of the box's width, and its height grows only slowly as the box grows.

### What the app refuses

An `editable_vector` asset that is not valid JSON, breaks any rule above, uses a vector artwork format
the document format does not allow, or whose `asset_id` does not match its content refuses the document
with "The SimpleDiagrams document contains an invalid assets file."

## Content identity: asset_id

`asset_id` is the SHA-256 digest of a **canonical encoding** of the artwork's meaning. It leaves out
`asset_id` itself and `source_sha256`, so the same artwork converted from two different files has the
same `asset_id`. A writer MUST compute it exactly as follows; the app refuses an asset whose `asset_id`
differs ("The vector asset identity does not match its canonical content." is the underlying reason).

1. **Start from the values, not the text.** Take the values a reader keeps from the file: numbers as
   64-bit floating-point values (whole-number fields as integers), and only the keys this page defines.
   Unknown keys are not part of the identity.
2. **Build one object** with the keys `format`, `schema_version`, `source_view_box`, `intrinsic_size` and
   `layers`. Add `compositing_groups`, `pins` and `outlines` only when they hold at least one entry.
3. **Leave out every optional key that has no value**: a layer's `fill` or `stroke`, a paint's
   `color_hex_rgba`, an outline's `fold` or `tab`. Keep keys that are always present, even when their
   value is empty, such as `"clip_paths": []` and `"dash_array": []`.
4. **Write it as JSON with no white space at all.** Within every object, sort the keys by comparing
   their characters' Unicode code points (for these keys, which are all lower-case ASCII letters and
   `_`, that is plain byte order: `clip_paths` < `fill` < `opacity` < `path` < `stroke`). Arrays keep
   their order. Do not escape `/`. Strings are written with JSON's required escapes only; every string
   in a valid asset is ASCII.
5. **Write each number** as follows. Every number in a valid asset has a magnitude of at most
   1,000,000,000, and these rules cover exactly that range:
   - a field defined as a whole number (`schema_version`, `first_layer_index`, `layer_count`,
     `layer_index`): as a plain integer;
   - zero: `0`, and negative zero `-0`;
   - any other number of magnitude at least 0.0001: the shortest decimal number that reads back as
     exactly the same 64-bit value, in plain notation, with no trailing `.0` on a whole value (`2`,
     `0.1`, `39.5`, `0.30000000000000004`, `48.916000000000004`, `1000000000`);
   - a non-zero number of magnitude below 0.0001: the shortest decimal number that reads back as
     exactly the same 64-bit value, in exponent notation: its significand, `e`, a sign, and an exponent
     of at least two figures (`1e-05`, `2.5e-07`, `1e-07`, `1e-300`).
6. **Hash.** `asset_id` is the SHA-256 digest of the UTF-8 bytes of that text, as 64 lower-case
   hexadecimal digits.

**Informative.** These number rules match Python's `repr` of a float with any trailing `.0` removed.
Python's standard JSON encoder, with sorted keys and the separators `,` and `:`, produces the canonical
text, except that it writes whole-valued floats as `1.0` and `-0.0`, where the canonical text has `1`
and `-0`.

The file's bytes need not be canonical. The file name and inventory `id` are the digest of the file's
actual bytes, whatever its white space and key order; only `asset_id` depends on the canonical form.

## Test vectors

Each vector below gives an asset as the app writes it, the canonical text behind its `asset_id`, and
both digests. A file with these exact bytes and no trailing newline has the stated file digest. All
three were checked with the app's own vector artwork reader and with an independent implementation of
the rule above.

### Test vector 1: vector artwork format 1

A triangle whose inside follows the element's fill colour and whose line follows its line colour and
weight.

The file, as the app writes it (582 bytes):

```json
{"asset_id":"f7ae028aaf78e64e6b830548efd5074775b16e6e3deb7754927be2dfb48cf2e8","format":"com.simplediagrams.vector-asset","intrinsic_size":{"height":80,"width":100},"layers":[{"clip_paths":[],"fill":{"opacity":1,"paint":{"kind":"linked_diagram_fill"},"rule":"nonzero"},"opacity":1,"path":{"commands":[["M",50,0],["L",100,80],["L",0,80],["Z"]]},"stroke":{"dash_array":[],"dash_offset":0,"line_cap":"round","line_join":"round","miter_limit":4,"opacity":1,"paint":{"kind":"linked_diagram_stroke"},"width":2}}],"schema_version":1,"source_view_box":{"height":80,"width":100,"x":0,"y":0}}
```

The canonical text (the same, without `asset_id`):

```json
{"format":"com.simplediagrams.vector-asset","intrinsic_size":{"height":80,"width":100},"layers":[{"clip_paths":[],"fill":{"opacity":1,"paint":{"kind":"linked_diagram_fill"},"rule":"nonzero"},"opacity":1,"path":{"commands":[["M",50,0],["L",100,80],["L",0,80],["Z"]]},"stroke":{"dash_array":[],"dash_offset":0,"line_cap":"round","line_join":"round","miter_limit":4,"opacity":1,"paint":{"kind":"linked_diagram_stroke"},"width":2}}],"schema_version":1,"source_view_box":{"height":80,"width":100,"x":0,"y":0}}
```

| Value | Result |
| --- | --- |
| `asset_id` (SHA-256 of the canonical text) | `f7ae028aaf78e64e6b830548efd5074775b16e6e3deb7754927be2dfb48cf2e8` |
| File digest: inventory `id` and file name stem | `fcb99190ee9e0b7a7ddfdb70fc7d4da3e029fddbb3ce1c387a47c80a522e9ade` |

The same artwork written with indentation, or with `"width": 100.0`, has the same `asset_id` but a
different file digest.

### Test vector 2: vector artwork format 2

Two layers in one compositing group: a translucent square with an authored colour and even-odd fill,
and a dashed authored curve. It exercises non-integer numbers, the shortest round-trip form
(`0.30000000000000004`), the exponent form (`1e-05`), a `source_sha256` (left out of the identity), and
layers without a `stroke` or without a `fill`.

The file, as the app writes it (905 bytes):

```json
{"asset_id":"d1c35e7549acffae2a00b1a9fa12303cbf8fd9d4a796067c1cadad90eeb2c6a7","compositing_groups":[{"first_layer_index":0,"layer_count":2,"opacity":0.5}],"format":"com.simplediagrams.vector-asset","intrinsic_size":{"height":40,"width":40},"layers":[{"clip_paths":[],"fill":{"opacity":0.5,"paint":{"color_hex_rgba":"#1F6FEB80","kind":"authored"},"rule":"even_odd"},"opacity":1,"path":{"commands":[["M",0,0],["L",40,0],["L",40,40],["L",0,40],["Z"]]}},{"clip_paths":[],"opacity":0.75,"path":{"commands":[["M",0.30000000000000004,20],["C",10,0,30,0,39.5,20]]},"stroke":{"dash_array":[6,3.5],"dash_offset":1e-05,"line_cap":"butt","line_join":"miter","miter_limit":10,"opacity":1,"paint":{"color_hex_rgba":"#000000FF","kind":"authored"},"width":1.5}}],"schema_version":2,"source_sha256":"0000000000000000000000000000000000000000000000000000000000000000","source_view_box":{"height":40,"width":40,"x":0,"y":0}}
```

The canonical text:

```json
{"compositing_groups":[{"first_layer_index":0,"layer_count":2,"opacity":0.5}],"format":"com.simplediagrams.vector-asset","intrinsic_size":{"height":40,"width":40},"layers":[{"clip_paths":[],"fill":{"opacity":0.5,"paint":{"color_hex_rgba":"#1F6FEB80","kind":"authored"},"rule":"even_odd"},"opacity":1,"path":{"commands":[["M",0,0],["L",40,0],["L",40,40],["L",0,40],["Z"]]}},{"clip_paths":[],"opacity":0.75,"path":{"commands":[["M",0.30000000000000004,20],["C",10,0,30,0,39.5,20]]},"stroke":{"dash_array":[6,3.5],"dash_offset":1e-05,"line_cap":"butt","line_join":"miter","miter_limit":10,"opacity":1,"paint":{"color_hex_rgba":"#000000FF","kind":"authored"},"width":1.5}}],"schema_version":2,"source_view_box":{"height":40,"width":40,"x":0,"y":0}}
```

| Value | Result |
| --- | --- |
| `asset_id` | `d1c35e7549acffae2a00b1a9fa12303cbf8fd9d4a796067c1cadad90eeb2c6a7` |
| File digest | `048d546435ace91efcd55497319f70839f3dda9e9a808e21a4217ac508173a5b` |

### Test vector 3: vector artwork format 4

A made-up note shape: a box with its top-right corner folded, drawn as two outlines (the box and its
crease), and a small authored square pinned to the bottom-right corner. It exercises `outlines`,
`pins`, a `fold`, the key order inside outlines and pinned parts, and an authored paint beside linked
ones.

The file, as the app writes it (1,419 bytes):

```json
{"asset_id":"ad16ee3757d3cdb618e754d219460142efc78a923e3f79549006dfd4cb921f0e","format":"com.simplediagrams.vector-asset","intrinsic_size":{"height":80,"width":120},"layers":[{"clip_paths":[],"fill":{"opacity":1,"paint":{"kind":"linked_diagram_fill"},"rule":"nonzero"},"opacity":1,"path":{"commands":[["M",0,0],["L",104,0],["L",120,16],["L",120,80],["L",0,80],["Z"]]},"stroke":{"dash_array":[],"dash_offset":0,"line_cap":"round","line_join":"round","miter_limit":4,"opacity":1,"paint":{"kind":"linked_diagram_stroke"},"width":1.5}},{"clip_paths":[],"opacity":1,"path":{"commands":[["M",104,0],["L",104,16],["L",120,16]]},"stroke":{"dash_array":[],"dash_offset":0,"line_cap":"round","line_join":"round","miter_limit":4,"opacity":1,"paint":{"kind":"linked_diagram_stroke"},"width":1.5}},{"clip_paths":[],"fill":{"opacity":1,"paint":{"color_hex_rgba":"#2F9E44FF","kind":"authored"},"rule":"nonzero"},"opacity":0.8,"path":{"commands":[["M",100,60],["L",112,60],["L",112,72],["L",100,72],["Z"]]}}],"outlines":[{"box":{"height":80,"width":120,"x":0,"y":0},"corner_radius":0,"fold":16,"kind":"folded_corner_box","layer_index":0,"offsets":[0]},{"box":{"height":80,"width":120,"x":0,"y":0},"corner_radius":0,"fold":16,"kind":"folded_corner_crease","layer_index":1,"offsets":[0]}],"pins":[{"anchor":"bottom-right","first_layer_index":2,"layer_count":1}],"schema_version":4,"source_view_box":{"height":80,"width":120,"x":0,"y":0}}
```

The canonical text:

```json
{"format":"com.simplediagrams.vector-asset","intrinsic_size":{"height":80,"width":120},"layers":[{"clip_paths":[],"fill":{"opacity":1,"paint":{"kind":"linked_diagram_fill"},"rule":"nonzero"},"opacity":1,"path":{"commands":[["M",0,0],["L",104,0],["L",120,16],["L",120,80],["L",0,80],["Z"]]},"stroke":{"dash_array":[],"dash_offset":0,"line_cap":"round","line_join":"round","miter_limit":4,"opacity":1,"paint":{"kind":"linked_diagram_stroke"},"width":1.5}},{"clip_paths":[],"opacity":1,"path":{"commands":[["M",104,0],["L",104,16],["L",120,16]]},"stroke":{"dash_array":[],"dash_offset":0,"line_cap":"round","line_join":"round","miter_limit":4,"opacity":1,"paint":{"kind":"linked_diagram_stroke"},"width":1.5}},{"clip_paths":[],"fill":{"opacity":1,"paint":{"color_hex_rgba":"#2F9E44FF","kind":"authored"},"rule":"nonzero"},"opacity":0.8,"path":{"commands":[["M",100,60],["L",112,60],["L",112,72],["L",100,72],["Z"]]}}],"outlines":[{"box":{"height":80,"width":120,"x":0,"y":0},"corner_radius":0,"fold":16,"kind":"folded_corner_box","layer_index":0,"offsets":[0]},{"box":{"height":80,"width":120,"x":0,"y":0},"corner_radius":0,"fold":16,"kind":"folded_corner_crease","layer_index":1,"offsets":[0]}],"pins":[{"anchor":"bottom-right","first_layer_index":2,"layer_count":1}],"schema_version":4,"source_view_box":{"height":80,"width":120,"x":0,"y":0}}
```

| Value | Result |
| --- | --- |
| `asset_id` | `ad16ee3757d3cdb618e754d219460142efc78a923e3f79549006dfd4cb921f0e` |
| File digest | `38b3180a7a4977d37aee9d214fedb08a6eb060d929bd1d47e4eef295e0f63477` |

**Informative.** The same rule reproduces the `asset_id` of every vector artwork file shipped inside
SimpleDiagrams 5.1.1 and 5.2, in vector artwork formats 1, 3 and 4.

## Open points

1. **Number spelling across systems.** The rule is the one the app computes: it runs on macOS 15 and
   later and writes the canonical text with the system's JSON encoder, whose number spelling is
   described above. The test vectors are the reference values. A writer SHOULD check its output against
   them.
