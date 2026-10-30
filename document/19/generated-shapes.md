# Generated shapes

This page specifies **generated shapes**, the `generated` type of complex shape in document format 19:
rings, flows of steps, pyramids and funnels, cycles and gauges. Read [README.md](README.md) first for the
conventions this page relies on, and [elements.md](elements.md#12-complex-shapes) for complex shapes in
general.

A generated shape's picture is worked out by code in the app, its **generator**, from a few
**settings**: how wide a ring's band is, which way a flow points. The record does not rely on that code
being present. It saves the finished drawing as **parts**, each a few closed outlines with an optional
text slot, so that any reader can draw, paint, label, move and resize the shape from the file alone. A
reader that has the generator can also redraw the shape when its settings change; one that does not
still shows exactly what the generator drew.

## Contents

1. [What a generated shape is](#1-what-a-generated-shape-is)
2. [The record](#2-the-record)
3. [Parts](#3-parts)
4. [Segments](#4-segments)
5. [Text areas](#5-text-areas)
6. [Settings](#6-settings)
7. [The one transform](#7-the-one-transform)
8. [Connection points](#8-connection-points)
9. [The element's other keys](#9-the-elements-other-keys)
10. [The three checks](#10-the-three-checks)
11. [The generator catalogue](#11-the-generator-catalogue)
12. [The incomplete form](#12-the-incomplete-form)
13. [Limits](#13-limits)
14. [Drawing](#14-drawing)
15. [An example](#15-an-example)
16. [Open points](#16-open-points)

## Terms used on this page

| Term | Meaning |
| --- | --- |
| **Generator** | The code that works a shape's drawing out from its settings, named by the record's `generator`, such as `com.simplediagrams.ring` |
| **Generator version** | Which version of that code, `generator_version`. A version's output, once released, never changes; a change is a new version |
| **Known version** | A generator and version the reader has the code for. SimpleDiagrams 5.2 knows the five generators of the [catalogue](generators/README.md), each at version 1 |
| **Unknown version** | Any other generator, or a version of a known generator that the reader does not have, such as one a later release adds |
| **Part** | One region of the shape a person can choose, paint and label: a ring's section, a flow's step, a gauge's needle |
| **`made_at`** | The width and height of the frame the parts were drawn for. Every position in the record is in this frame, in points, or in fractions of it |
| **Complete form** | The record with its drawing, as the app writes it ([section 2](#2-the-record)) |
| **Incomplete form** | A record without its drawing, which another program may write for the app to draw when it opens the document ([section 12](#12-the-incomplete-form)) |
| **Name** | A machine word matching `^[a-z][a-z0-9_]{0,31}$`: a lower-case letter, then up to 31 lower-case letters, digits or underscores. Roles, slots and settings are named this way |
| **Slug** | A word matching `^[a-z0-9]+(-[a-z0-9]+)*$`: groups of lower-case letters and digits joined by single hyphens. Setting words are written this way |
| **Generator name** | A fully qualified, reverse-DNS name: two or more slugs joined by single dots, `^[a-z0-9]+(-[a-z0-9]+)*(\.[a-z0-9]+(-[a-z0-9]+)*)+$`, 3 to 128 bytes, such as `com.simplediagrams.ring` or `com.example.spiral`. The `com.simplediagrams.` namespace is SimpleDiagrams' own [generator catalogue](generators/README.md); another program names its generators under its own domain |
| **Open list** | A list of words where a word the reader does not know is kept and written back, meaning "made by a newer version". In a **closed** list an unknown word refuses the document |

Messages the app shows when it refuses a document are quoted below in English, as examples only
([README.md](README.md#messages-quoted-in-this-specification)).

## 1. What a generated shape is

A generated shape is an element of kind `complex_shape` whose `complex_shape` record has `"type":
"generated"` ([elements.md](elements.md#1210-generated)). Its record comes in two forms:

- the **complete form**, which has `made_at` and the drawing of every part. The app writes only this
  form, and every reader can draw it;
- the **incomplete form**, which has no `made_at`: the generator, its settings and each part's role and
  words, but no drawing. Another program MAY write it for one of the five generators format 19 lists;
  the app draws it as it opens the document ([section 12](#12-the-incomplete-form)).

A generator's name is fully qualified ([Terms](#terms-used-on-this-page)). The `com.simplediagrams.`
namespace belongs to the [generator catalogue](generators/README.md): a writer MUST NOT write a name
and version in it that the catalogue does not list, and for a listed one MUST write only the setting
words its entry lists. Another program MAY write shapes of its own generators, named under its own
domain such as `com.example.spiral`, in the complete form; they have no catalogue entry, and every
reader draws them from their saved parts. A reader accepts a generator, version or open word it does
not know, from a later release or another program, by the rules of this page
([section 10](#10-the-three-checks)), so such a document still opens.

A writer that wants its shape to look exactly as the app draws it writes the incomplete form and lets
the app draw it. A writer MAY instead write the complete form, for a generator version the catalogue
lists, with a drawing of its own: the app never compares a saved drawing with what its generator would
draw, so any drawing that keeps the rules of this page and of the version's catalogue entry opens and
is drawn as saved, until a person changes a setting or resizes it and the app redraws it.

## 2. The record

The complete form. The record is strict: an unknown key at any level refuses the document, except in
the words of a text slot, which are an ordinary text record ([elements.md](elements.md#6-text-content)).
Every list is read no further than its limit: a longer one refuses the document while it is read, with
"The SimpleDiagrams document contains an invalid document.json file."

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `type` | string | Yes | `generated` | A generated shape |
| `hand_drawn` | boolean | No | Default `true` | As on every complex shape: `false` draws the lines exactly. A writer writes it only as `false` |
| `generator` | string | Yes | A generator name ([Terms](#terms-used-on-this-page)): reverse-DNS, 3 to 128 bytes | Which generator drew the shape. A writer writes a generator the catalogue lists; a reader accepts a name it does not know, keeps it and draws the shape from its parts |
| `generator_version` | whole number | Yes | 1 to 999 | Which version of the generator. A writer writes a version the catalogue lists; a reader accepts one it does not know |
| `resizing` | string | Yes | `free` or `proportional` | How a reader lets a person resize the shape: `free` in any proportion, `proportional` keeping the frame's proportions |
| `made_at` | object | Yes | Exactly `width` and `height`, each a number from 1 to 10,000,000 | The frame size, in points, the parts were drawn for |
| `stacking` | string | Yes | `last_in_front` or `first_in_front` | Which part is drawn in front, and chosen by a click, where two overlap: each part in front of those before it, or of those after it |
| `own_text` | object | No | A text area ([section 5](#5-text-areas)) | Where the element's own text is set. Absent: the shape shows no text of its own |
| `settings` | object | Yes | At most 16 entries ([section 6](#6-settings)) | The generator's inputs, by name. `{}` is valid for a generator that takes none |
| `parts` | array of parts | Yes | 1 to 24 | The finished drawing ([section 3](#3-parts)) |

**The order of `parts` is the shape's sequence.** Reading order, moving from part to part with the Tab
key, VoiceOver, and the order of parts in exported SVG all follow it, whatever `stacking` says about
which part is in front.

Faults of meaning in a generated record, such as a value out of range, a loop that does not close or a
point no part owns, refuse the document with "The shape *id* is not a valid generated shape.", *id*
being the element's identifier. A fault in the words of a text slot gives the messages any text gives
([elements.md](elements.md#6-text-content)).

## 3. Parts

Each entry of `parts` has exactly these keys.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique among the shape's parts | The part's identity. Like a lane's, it is unique only within its shape: a copied shape keeps its parts' identifiers. The app makes it with the part and never changes or reuses it |
| `role` | string | Yes | A name | What the part is, such as `section` or `needle`. A machine word, never translated |
| `loops` | array of loops | Yes | 1 to 4 loops; each loop 1 to 32 segments; at most 64 segments in the part | The part's outline: closed loops of segments ([section 4](#4-segments)), filled together by the even-odd rule, so a ring's section is an outer loop and an inner loop as its hole |
| `style` | object | No | At least one channel (below) | The part's own paint. Absent: the shape's |
| `texts` | array of text slots | Yes | 0 or 1 slot ([section 5](#5-text-areas)) | The part's words. `[]` for a part without words |
| `point_ids` | array of whole numbers | Yes | 0 to 2 numbers, each 8 or more ([section 8](#8-connection-points)) | The connection points the part owns. `[]` for none |

There is no other key. In particular a part has no `paint` key: every part is filled and outlined, and
a part that shows no fill has a fill colour with zero alpha.

Loops SHOULD NOT overlap except where one is a hole inside another: the even-odd rule would cut a hole
where two loops of one part overlap. Readers do not check this.

**Part paint.** A part's `style` holds any of these five channels and nothing else:

| Key | Type | Rules | Meaning |
| --- | --- | --- | --- |
| `fill_hex` | colour | A colour ([README.md](README.md#terms)) | The part's fill colour |
| `fill_texture` | string | A texture name ([elements.md](elements.md#1310-textures)); only with `fill_hex` | The part's fill texture |
| `stroke_hex` | colour | A colour | The part's line colour |
| `stroke_texture` | string | A texture name; only with `stroke_hex` | The part's line texture |
| `line_width` | number | 0 to 10,000 | The part's line weight in points |

A missing channel follows the element's `style` ([elements.md](elements.md#5-style)), with one rule for
textures: a colour and its texture go together.

| A part's `style` holds | Its fill is drawn with | Its line is drawn with |
| --- | --- | --- |
| No `fill_hex` | The shape's fill colour and fill texture | |
| `fill_hex` alone | The part's colour, flat | |
| `fill_hex` and `fill_texture` | The part's colour and texture | |
| No `stroke_hex` | | The shape's line colour and line texture |
| `stroke_hex` alone | | The part's colour, flat |
| `stroke_hex` and `stroke_texture` | | The part's colour and texture |

`line_width` stands alone: absent, the part has the shape's line weight. So a part keeps the shape's
texture under a colour of its own only by naming that texture too. An empty `style`, `{}`, a texture
without its colour, and an unknown texture refuse the document. A generated shape's lines are always
solid; there is no line pattern.

## 4. Segments

A loop is an array of segments. A segment is an array written in full, with no item left out: a
segment of any other length refuses the document while it is read.

| Segment | Form | Rules |
| --- | --- | --- |
| Line | `["L", x1, y1, x2, y2, wobble_from, wobble_share]`, exactly 7 items | At least 0.01 points long |
| Arc | `["A", cx, cy, radius, from_degrees, to_degrees, wobble_from, wobble_share]`, exactly 8 items | `radius` above 0 and at most 10,000,000; each angle from −720 to 720; the sweep, `to_degrees − from_degrees`, not 0 and at most 360 either way; at least 0.01 points long (`radius × |sweep| × π ÷ 180`) |

- Positions and radii are in points, in the `made_at` frame, with x growing to the right and y
  downwards.
- An arc's angles are degrees **clockwise** as seen on screen, measured from the direction of three
  o'clock. The arc runs from `from_degrees` to `to_degrees`; a sweep below 0 runs anticlockwise.
- `wobble_from` is `start` or `end`, and `wobble_share` a number from 0 to 1. Together they say how the
  hand-drawn pen wobbles along the segment: the end its wobble is measured from, and the segment's share
  of the full wobble. They change nothing when the shape is drawn exactly ([section 14](#14-drawing)).

**Joints and closure.** Within a loop, each segment starts where the one before it ends, and the last
ends where the first starts, each within 0.01 points.

**Inside the frame.** Both ends of every line, and the whole extent of every arc (its two ends and every
point at a multiple of 90 degrees that its sweep passes), lie inside the `made_at` frame, within 0.01
points.

A loop's area is not checked: a loop that encloses nothing is drawn as lines.

## 5. Text areas

A part's words, and the shape's own text, are each set in a **text area**: a rectangle in the `made_at`
frame with a vertical position and an optional turn.

**A text slot** is an entry of a part's `texts`, with exactly these keys:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `slot` | string | Yes | A name | Which of the part's slots this is, such as `label` |
| `text_layout` | object | Yes | Below | Where the words are set |
| `placed` | boolean | No | Only `true` | A person placed the area. Absent: the generator placed it |
| `text` | text content | Yes | As any text ([elements.md](elements.md#6-text-content)), and at most 500 characters | The words. Always written, even when empty, because an empty text still carries the style the next words take |

**`own_text`**, the shape's own text area, has exactly `text_layout` and, optionally, `placed`. Its
words are the element's `text_content`, held to the same 500 characters.

**`text_layout`** here has exactly these keys:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `frame` | object | Yes | Exactly `x`, `y`, `width` and `height`, fractions of `made_at`; `width` and `height` 0 or more | The area: from `x × made_at.width` across and `y × made_at.height` down, `width × made_at.width` wide and `height × made_at.height` tall |
| `position` | string | Yes | `top`, `middle` or `bottom` | Where the words sit in the area, as on any shape ([elements.md](elements.md#7-text-layout)). `above` and `below` are refused |
| `rotation_degrees` | number | No | At least 0 and less than 360; written only when it is not 0, and never as `null` | The area's turn, clockwise about its centre, on top of the element's |

The area, turned as it is, lies inside the `made_at` frame: each of its four corners within 0.01
points. This is the only bound on where an area lies. An area MAY run over any line, gap, hole or other
part; a generator's own areas lie inside their part, but a person may move one anywhere in the shape.

`placed` is written only as `true`. `false` and `null` refuse the document, so each meaning has one
spelling. **Informative.** When a person resizes the shape or changes a setting that keeps the number of
parts, the app keeps a placed area where it was relative to its part, still inside `made_at`; when the
number of parts changes, and for a few settings that change what every part is, every area returns to
the generator's place.

**One slot a part.** A part has at most one text slot: a second refuses the document while it is read.

**Characters.** The words of one text area are at most 500 characters (Unicode scalar values). The
words of a generated shape's text slots also count towards the document's and the page's budgets for
the text of complex shapes ([elements.md](elements.md#1212-how-much-a-shape-and-a-page-hold)).

## 6. Settings

`settings` is an object of at most 16 entries. Each name is a name ([Terms](#terms-used-on-this-page));
each value is one of:

- a JSON number, finite;
- `true` or `false`;
- a **word**: a string that is a slug of 1 to 32 bytes.

Nothing else: no `null`, list, object or free text. A number is a number and a word is a word: `"40"`
is a word, not the number 40, and `1` is a number, not `true`.

For a **known version**, the settings MUST be exactly those its generator declares, each present, each
of its declared type, in its declared range, and, for a word, one of its declared words; no setting has
a default ([the catalogue](generators/README.md)). A setting's words are either:

- **closed**: any other word refuses the document, as `diagonal` does for a flow's `direction`; or
- **open**: the catalogue entry may gain words in a later release, within the same version, as a
  base shape added to the flow later is still flow version 1. A writer writes only words the entry
  lists. A word the reader does not know means the shape was made by a later release; the reader
  keeps it, writes it back, and draws the shape from its parts. Only the rules that depend on that
  word are set aside ([section 10](#10-the-three-checks)).

For an **unknown version**, any settings that keep the form above are accepted and kept exactly as
read.

How many sections, steps or layers a shape has is never a setting: it is the number of its parts of that
role. A record holds what a person chose, never what the app was doing with it: no chosen part and no
open control is saved.

## 7. The one transform

Every position in the record is in the `made_at` frame, `w0` by `h0`. A reader places it in the
element's frame, `W` by `H`, with one transform, the same for outlines, text areas, the area a click
chooses a part by, and connection points:

```text
if any part of the shape holds an arc:
    s = min(W / w0, H / h0)
    T(x, y) = ((W - s × w0) / 2 + s × x,  (H - s × h0) / 2 + s × y)
    every radius is multiplied by s
otherwise:
    T(x, y) = (x × W / w0,  y × H / h0)
```

`T` gives a position measured from the frame's top-left corner, before rotation. The element then turns
about its frame's centre by `rotation_degrees`, as every element does
([elements.md](elements.md#3-identity-frame-rotation-and-flips)).

A shape with an arc is scaled evenly and centred, because an arc scaled unevenly is an ellipse the
hand-drawn pen cannot draw: a ring stretched by a reader that cannot redraw it stays round, centred in
its frame. A shape of lines alone is stretched to fill the frame. Line weights, the pen's wobble and
font sizes are never scaled; they stay in points.

**Informative.** The app redraws a shape of a known version whenever it is resized, a group's resize
included, so its `made_at` is then the frame itself, or the frame enlarged to the generator's smallest
frame on a side that is smaller, and `T` is the identity or a plain shrink. A reader that cannot redraw
a shape changes nothing in its record when it resizes it, so repeated resizes never drift.

## 8. Connection points

A generated shape's element keeps its `connection_points` in the form every element has
([elements.md](elements.md#8-connection-points)), with one difference of meaning: **`x` and `y` are
fractions of `made_at`**, not of the element's frame. A point's saved position is
`(x × w0, y × h0)` in the `made_at` frame; `T` places it in the frame, and the element's rotation turns
it. Everything that resolves a point does this: a connector's attached end, a callout's locked tail and
a reader's own drawing.

- Points 0 to 7 are the **shape's own**. A known version has exactly the ones its generator declares
  ([the catalogue](generators/README.md)); an unknown version may have any of them.
- Every point numbered 8 or more **belongs to exactly one part**, which lists it in its `point_ids`.
  A point a part lists MUST exist on the element, MUST NOT be listed by another part or twice, and a
  point numbered 8 or more that no part lists refuses the document.
- A part lists at most 2 points, so a shape has at most 8 + 2 × 24 = 56 points. The order within
  `point_ids` has the meaning the generator version declares, such as a flow step's back, then front.

**Informative.** The app writes the points again whenever it redraws the shape. A new part takes the
smallest unused numbers from 8 up, and removing a part removes its points and releases the connectors
attached to them where they were drawn, so a number used again attaches nothing by accident.

## 9. The element's other keys

A generated shape is an element like any other ([elements.md](elements.md#2-the-element-record)), with
these rules:

| Key | Rule |
| --- | --- |
| `text_content` | The shape's own text, set in `own_text`. For a known version without `own_text`, its `string` MUST be empty: no hidden words travel in files, searches or exports. An unknown version's text is kept as read and not drawn when the record has no `own_text` |
| `connection_points` | Fractions of `made_at` ([section 8](#8-connection-points)) |
| `rotation_degrees` | Allowed; the shape turns after `T` places it |
| `flip_horizontal`, `flip_vertical` | MUST be `false`: a generated shape is never flipped |
| `preserves_aspect_ratio` | MUST NOT be `true`; `resizing` is the shape's one resizing rule |
| `text_layout` | Forbidden, as on every complex shape |
| `style` | The shape's paint, which each part follows except where its own `style` says otherwise ([section 3](#3-parts)) |

## 10. The three checks

A reader holds every generated record to three checks, in order. A record that fails one refuses the
document; it is never treated as an unknown version to escape a check.

1. **Structure, for every record.** The keys, forms and limits of sections 2 to 9: closed keys,
   segments in full, loops that close, everything inside `made_at`, unique part identifiers, points each
   owned once, names, slugs and value types. A record of an unknown version meets this check only.
2. **The version's schema, for a known version.** Exactly the declared settings, each of its type and
   range; exactly the declared roles, in their declared order, each with a count in its range; exactly
   the declared slot on each part; the declared number of points on each part, and exactly the declared
   shape points among 0 to 7; `own_text` present exactly when declared; the declared `stacking` and
   `resizing`; and an empty `text_content` when the version declares no own text.
3. **The version's rules, for a known version.** The few rules that relate settings to each other, given
   with each generator in [the catalogue](generators/README.md).

A word in an open setting that the reader does not know sets aside only the rules that depend on that
word: a flow whose base shape is unknown still needs 2 to 12 steps, one `label` each and a `pointer`
that is `true` or `false`.

A `made_at` smaller than the generator's smallest frame is accepted: the smallest frame governs what a
generator draws, not what a reader reads. Geometry is never compared with what the generator would
draw.

## 11. The generator catalogue

What each generator version holds and allows is not part of this page. It is in the **generator
catalogue**, [generators/](generators/README.md): one entry for each generator version SimpleDiagrams
knows, each with a page that explains the shape, its settings and its parts, and a JSON Schema. The
catalogue published with format 19 holds the five generators SimpleDiagrams 5.2 draws, each at version
1: `com.simplediagrams.ring`, `com.simplediagrams.flow`, `com.simplediagrams.pyramid`,
`com.simplediagrams.cycle` and `com.simplediagrams.gauge`.

An entry declares a version's settings, roles and their counts, the slot and points of each role, its
own points among 0 to 7, `own_text`, `stacking`, `resizing` and its rules. A record of a version the
reader knows MUST hold exactly what the entry declares and keep its rules (checks 2 and 3,
[section 10](#10-the-three-checks)), and its element MUST pass the entry's schema as well as the core
schema.

**How the catalogue grows.** A later release may add a generator or a version to the catalogue, and an
existing entry may gain words in its open settings ([section 6](#6-settings)). Nothing else in an entry
ever changes, and nothing on this page does: any other change to a generator is a new version, a new
entry. A reader that does not know an entry, or a word, reads the shape as this page says for an
unknown generator, version or open word.

## 12. The incomplete form

A program that does not have a generator's code can still write its shape: it writes the record without
its drawing, and the app draws it while it opens the document. A record without `made_at` is in the
incomplete form; one with `made_at` is complete, and every part of it must then be complete too.

| Level | Keys allowed | Refused |
| --- | --- | --- |
| The record | `type`, `generator`, `generator_version`, `settings`, `parts`, and optionally `hand_drawn` | `made_at`, `resizing`, `stacking`, `own_text`, and any other key |
| A part | `role`, `texts`, and optionally `id` | `loops`, `style`, `point_ids`, and any other key |
| A text slot | `slot` and `text` | `text_layout`, `placed`, and any other key |

**Which shapes may be written this way.** Only the five generators the [catalogue](generators/README.md)
admits in this form for format 19, each at version 1, with the words their pages list for format 19. In
the incomplete form every setting is closed: any other word, such as a base shape a later release adds
to the flow's entry, refuses the document, and so does any other generator or version. Every app that writes
format 19 can draw exactly these, so a document written this way opens in each of them.

**What it must hold**, checked while the document is read, before the shape is drawn: exactly the
generator's settings, each of its type, range and words; exactly its roles in order, with counts in
range; exactly its slot on each part; a part `id`, where given, a UUID unique among the parts; and
`connection_points` that are `[]`, or only the shape's own points its generator declares among 0 to 7
(a pyramid only 2 and 3), so that a connector can be attached to them before the shape is drawn. Their
positions are replaced when it is. A record that breaks one of these refuses the document with "The
SimpleDiagrams document contains an invalid document.json file."

**What the drawn shape must keep**, checked once it is drawn, as every complete record is: the
generator's rules (check 3), such as a gauge's value from its minimum to its maximum; and
`text_content` as the complete form needs it, empty except for a gauge, whose caption it is. A record
that breaks one of these refuses the document with "The shape *id* is not a valid generated shape."

**What the app does when it opens it.** Before the document's window appears:

1. It draws the shape for the element's frame, enlarged to the generator's smallest frame on a side
   that is smaller, and makes that size `made_at`. The element's frame is kept: where `made_at` is
   larger, the one transform shrinks the drawing into it.
2. It keeps each part's role, order, words and given `id`, and gives a part without an `id` a new one.
3. It writes the loops, the text areas, `own_text`, `stacking`, `resizing` and the connection points:
   0 to 7 where the generator puts them, and each part's own from 8 up. A connector attached to one of
   0 to 7 keeps its point, which moves to where the generator puts it.
4. It treats the document as edited, so saving it stores the complete form. Locks do not stop this.

The app never writes the incomplete form.

An example of a three-step flow in the incomplete form (each `text` shortened here; each is a complete
text record):

```json
{
  "type": "generated",
  "generator": "flow",
  "generator_version": 1,
  "settings": {"base_shape": "chevron", "direction": "across", "spacing": "gap", "pointer": false},
  "parts": [
    {"role": "step", "texts": [{"slot": "label", "text": "… Plan …"}]},
    {"role": "step", "texts": [{"slot": "label", "text": "… Build …"}]},
    {"role": "step", "texts": [{"slot": "label", "text": "… Ship …"}]}
  ]
}
```

## 13. Limits

A record over any of these is refused. A list longer than its maximum is refused while it is read.

| What is limited | Limit |
| --- | --- |
| `generator` | A generator name: two or more slugs joined by single dots, 3 to 128 bytes |
| `generator_version` | 1 to 999 |
| `made_at` width and height | 1 to 10,000,000 |
| Settings | At most 16; each name a name; each word a slug of 1 to 32 bytes |
| Parts | 1 to 24, whatever the generator |
| Loops in one part | 1 to 4 |
| Segments | 1 to 32 in a loop; at most 64 in a part and 512 in the shape |
| Lines and arcs | At least 0.01 points long |
| Arc radius; arc angles; sweep | Above 0, at most 10,000,000; −720 to 720 degrees; not 0, at most 360 degrees either way |
| How far joints, closures and positions may miss | 0.01 points |
| A segment's `wobble_share` | 0 to 1 |
| A part's `line_width` | 0 to 10,000 |
| Text slots in one part | 0 or 1 |
| Characters in one text area | 500 |
| A text area's turn | At least 0, less than 360 degrees |
| Points one part owns | 0 to 2, each 8 or more |

**Every vocabulary.**

| Words | Syntax | Open or closed |
| --- | --- | --- |
| `generator` | Generator name | A writer writes one the catalogue lists, or one of its own under its own domain. A reader accepts any in the complete form; the incomplete form takes only the five the catalogue admits in it |
| `generator_version` | 1 to 999 | A writer writes one the catalogue lists. A reader accepts any in the complete form; the incomplete form takes only 1 |
| Part roles, slot names, setting names | Name | As a known version declares; any name for an unknown version |
| Setting words | Slug | Open or closed, setting by setting ([the catalogue](generators/README.md)); closed in the incomplete form |
| `resizing` | `free`, `proportional` | Closed |
| `stacking` | `last_in_front`, `first_in_front` | Closed |
| Segment tag | `L`, `A` | Closed |
| `wobble_from` | `start`, `end` | Closed |
| `text_layout.position` | `top`, `middle`, `bottom` | Closed |
| Part `style` channels | The five of [section 3](#3-parts) | Closed |
| Textures | The seventeen of [elements.md](elements.md#1310-textures) | Closed |

## 14. Drawing

**Informative.** How the app draws a generated shape, so that another reader can draw it alike. None of
this is a rule a document must keep.

- **Order.** Parts are painted back to front as `stacking` says, each its fill, then its line. The words
  of every slot are painted after all the parts, in the same order, so a label is never hidden by a
  neighbouring part; a click on overlapping words chooses the one in front.
- **The pen.** With `hand_drawn` absent or `true`, each segment is drawn with the app's hand-drawn pen,
  whose wobble is measured from the end `wobble_from` names and scaled by `wobble_share`. A shape whose
  lines would need more of the pen's work than one shape may have is drawn with exact lines instead, as
  with any complex shape ([elements.md](elements.md#121-the-record)); so is every part when `hand_drawn`
  is `false`. A part whose fill would need too much work to work out keeps its line and loses its fill.
- **Words.** Each text area is shrunk by half the line weight of its part (for the shape's own text, of
  the shape) on each side, but never past its centre, so words never sit on a line. Words wrap only
  between words: a word wider than its area stays whole and overflows evenly on both sides, and text
  taller than its area is drawn in full, centred on it.
- **SVG export.** Each part is one path, filled by the even-odd rule.

## 15. An example

A ring of one section, as the app's Ring tile places it: 200 by 200 points, the band 40 points wide.
The section's label reads "Customers". The label's frame is rounded here to four places; the
[`generated-shapes`](examples/generated-shapes.simplediagram/) example has the values in full.

```json
{
  "type": "generated",
  "generator": "ring",
  "generator_version": 1,
  "resizing": "proportional",
  "made_at": {"height": 200, "width": 200},
  "stacking": "last_in_front",
  "settings": {"ring_width": 40},
  "parts": [
    {
      "id": "DC3085BC-E5FE-5E93-9E1F-C7ED17803DB3",
      "role": "section",
      "loops": [
        [["A", 100, 100, 100, -90, 270, "start", 1]],
        [["A", 100, 100, 60, -90, 270, "start", 1]]
      ],
      "texts": [
        {
          "slot": "label",
          "text_layout": {
            "frame": {"x": 0.3964, "y": 0.8482, "width": 0.2073, "height": 0.1036},
            "position": "middle"
          },
          "text": {
            "base_alignment": "center",
            "base_style": {"color_hex": "#212529", "font_size": 15, "is_italic": false,
                           "is_underlined": false, "weight": "medium"},
            "character_runs": [], "paragraph_runs": [], "string": "Customers"
          }
        }
      ],
      "point_ids": [8]
    }
  ]
}
```

The section is one part of two loops: the outer circle and, inside it, the hole, each a single arc of
one whole turn starting at twelve o'clock. The element beside the record has `text_content` empty, and
`connection_points` with points 0 to 7 on the circle and point 8, the section's, at the middle of its
outer edge, `{"id": 8, "x": 0.5, "y": 1}`, a fraction of `made_at`. The complete element is in the
[`generated-shapes`](examples/generated-shapes.simplediagram/) example, with one shape of each of the
other generators.

## 16. Open points

1. **Drawing details.** The pen's wobble, text measurement and the exact artwork of each look are the
   app's own presentation and may be refined in later releases without a format change, as on every
   page. A generator version's saved output does not change: geometry a released version wrote is
   what that shape looks like in every reader.
