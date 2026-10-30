# `com.simplediagrams.gauge`, version 1

A **gauge** is a half dial, like a speedometer: coloured bands from a minimum on the left, over the
top, to a maximum on the right, a needle pointing at a value, and a caption below. It shows where one
number stands in its range. Each band is a part a person can paint and label, and so is the needle.
First drawn by SimpleDiagrams 5.2.

Read [generated-shapes.md](../../generated-shapes.md) first for the generated record every reader reads.
This page gives what a gauge version 1 record holds beyond it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `generator`, `generator_version` | `com.simplediagrams.gauge`, `1` |
| Settings | `minimum`, `maximum`, `value`, `auto_rotate_labels` (below) |
| Roles and counts | `band`, 1 to 5 parts; then exactly one `needle`, last |
| Slot on each part | `label` on a band; none on the needle |
| Points each part owns | None |
| The shape's own points | All of 0 to 7 |
| `own_text` | Present: the caption, below the needle's hub. Its words are the element's `text_content` |
| `stacking` | `last_in_front`, so the needle lies over the bands |
| `resizing` | `proportional` |
| Sequence | Bands from the minimum to the maximum, then the needle |
| Incomplete form in format 19 | Yes |

## Settings

| Setting | Type | Values |
| --- | --- | --- |
| `minimum` | number | −1,000,000 to 1,000,000: the left end of the range |
| `maximum` | number | −1,000,000 to 1,000,000: the right end of the range |
| `value` | number | −1,000,000 to 1,000,000: where the needle points |
| `auto_rotate_labels` | `true` or `false` | Whether each band's label is turned to follow its band, or level |

## Rules

`minimum` is below `maximum`, and `value` lies from `minimum` to `maximum` inclusive. The bands come
first and the needle last. The schema cannot check either rule; a reader that knows this version
refuses a record that breaks one.

## Parts

- **Each band**, role `band`, is one loop of four segments: its outer arc clockwise, its end side in,
  its inner arc back, and its start side out. Its slot, `label`, holds the band's words.
- **The needle**, role `needle`, is one loop of three segments drawing the needle and its round hub
  together, so the even-odd fill never cuts a hole where they meet. It has no words and no points.

The shape's own points are 0 and 1 at the ends of the half circle, 2 at its top, 4 and 6 on the arc at
the upper diagonals, and 3, 5 and 7 along the bottom of the caption's room: middle, left and right.

## How the app draws it

**Informative.** The half circle and the caption's room below it, together 2 by 1.4 times the radius,
are as large as fit in `made_at`, centred. The bands are a quarter of the radius deep and share the
half circle equally, with small gaps between them. The needle points at the value's share of the range,
its tip in the middle of the bands. A band's label is centred on the band in a box twice as wide as
tall, as large as fits with 2 points to spare; with `auto_rotate_labels` it is turned to lie along the
band, never upside down, and the turn is saved in its `text_layout`. The caption's box runs from the
hub down to the bottom of the room. The smallest frame the app draws for is 40 by 28 points. A new
gauge from the app's tile is 200 by 140 points: three bands from 0 to 100, the needle at 50.

The [`gauges`](../../examples/gauges.simplediagram/) example holds gauges at both ends of their range and
one with level labels.
