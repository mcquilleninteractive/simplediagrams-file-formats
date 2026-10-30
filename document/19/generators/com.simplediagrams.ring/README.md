# `com.simplediagrams.ring`, version 1

A **ring** is a circle cut into sections, like a donut chart: each section is one part a person can
paint and label, and the band can be a ring of a given width or a solid disc. It shows how a whole is
shared out, or a set of things that belong together. First drawn by SimpleDiagrams 5.2.

Read [generated-shapes.md](../../generated-shapes.md) first for the generated record every reader reads.
This page gives what a ring version 1 record holds beyond it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `generator`, `generator_version` | `com.simplediagrams.ring`, `1` |
| Settings | `ring_width` (below) |
| Roles and counts | `section`, 1 to 12 parts |
| Slot on each part | `label` |
| Points each part owns | 1: the middle of its outer edge |
| The shape's own points | All of 0 to 7 |
| `own_text` | Absent: a ring has no text of its own, and its element's `text_content` is empty |
| `stacking` | `last_in_front` |
| `resizing` | `proportional` |
| Sequence | Clockwise from twelve o'clock |
| Incomplete form in format 19 | Yes |

## Settings

| Setting | Type | Values |
| --- | --- | --- |
| `ring_width` | number or word | A multiple of 10 from 10 to 10,000: how wide the band is, in points. Or the word `full`, a closed list: a solid disc cut into slices |

There are no rules beyond these (no check 3): every value the record allows can be drawn.

## Parts

Each section is one part with role `section`:

- **Its outline.** Within a band, a section is one loop: its outer arc clockwise, its end side in, the
  hole's arc back, and its start side out, four segments. In a solid disc it is three: from the centre
  out, the arc, and back to the centre. A ring of one section is the whole band: an outer circle and,
  unless the ring is full, a hole circle, each one arc of a whole turn from twelve o'clock.
- **Its slot**, `label`, holds the section's words.
- **Its point**, number 8 or more, sits at the middle of its outer edge, on the circle.

The shape's own points 0 to 7 lie on the circle at left, right, top and bottom (0, 1, 2, 3) and at the
four diagonals (4 top-left, 5 bottom-left, 6 top-right, 7 bottom-right).

## How the app draws it

**Informative.** The circle fills the smaller side of `made_at`, centred. The band is `ring_width`
wide, or the whole radius when that is wider, which draws as `full`. Sections share the circle equally,
the first starting at twelve o'clock, with a gap of up to 4 points between neighbours along the band's
middle, so each section's sides run straight out from the centre. A section's label is centred on the
band's middle at the section's middle angle, in a box twice as wide as tall, as large as fits inside
the section with 6 points to spare. The smallest frame the app draws for is 24 by 24 points. A new
ring from the app's tile is 200 by 200 points, one section, its band 40 points wide.

The [`generated-shapes`](../../examples/generated-shapes.simplediagram/) example holds a ring as its tile
places it, and [generated-shapes.md](../../generated-shapes.md#15-an-example) shows its record.
