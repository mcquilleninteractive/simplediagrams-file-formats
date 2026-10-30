# `com.simplediagrams.cycle`, version 1

A **cycle** is a set of steps arranged round a circle, each pointing to the next, for a process that
repeats: plan, do, check, act. Each step is one part a person can paint and label. First drawn by
SimpleDiagrams 5.2.

Read [generated-shapes.md](../../generated-shapes.md) first for the generated record every reader reads.
This page gives what a cycle version 1 record holds beyond it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `generator`, `generator_version` | `com.simplediagrams.cycle`, `1` |
| Settings | `ring_width`, `direction`, `arrow_style` (below) |
| Roles and counts | `step`, 3 to 8 parts |
| Slot on each part | `label` |
| Points each part owns | 1: the middle of its outer edge |
| The shape's own points | All of 0 to 7 |
| `own_text` | Absent: a cycle has no text of its own, and its element's `text_content` is empty |
| `stacking` | `last_in_front` |
| `resizing` | `proportional` |
| Sequence | In its direction, from twelve o'clock |
| Incomplete form in format 19 | Yes, with the words listed for format 19 below only |

## Settings

| Setting | Type | Values |
| --- | --- | --- |
| `ring_width` | number | A multiple of 10 from 10 to 10,000: how wide the band of steps is, in points. Unlike a ring's, it takes no word |
| `direction` | word, closed | `clockwise` or `anticlockwise` |
| `arrow_style` | word, open | `arrow` (each step a curved band ending in an arrowhead wider than the band) or `chevron` (each step as wide as the band, notched at its back and pointed at its front) |

The words above are those listed for format 19. The open setting `arrow_style` may gain words in a later
release, within version 1. A reader keeps a word it does not know; the incomplete form takes only the
words listed for format 19.

There are no rules beyond these (no check 3).

## Parts

Each step is one part with role `step`:

- **Its outline** is one loop of arcs and lines: seven segments for an arrow, six for a chevron.
- **Its slot**, `label`, holds the step's words.
- **Its point**, number 8 or more, sits at the middle of its outer edge.

The shape's own points 0 to 7 lie on the band's outer edge at left, right, top and bottom and at the
four diagonals, as a ring's do.

## How the app draws it

**Informative.** The circle fills the smaller side of `made_at`, centred. The band is `ring_width` wide,
or narrower where the circle is too small, so that the middle keeps a quarter of the radius empty. The
steps share the circle equally, the first centred at twelve o'clock, with small gaps between them; a
chevron's point fills the next step's notch. An `anticlockwise` cycle is the `clockwise` one mirrored
left to right. A step's label is centred on the band in a box twice as wide as tall, as large as fits
with 6 points to spare. The smallest frame the app draws for is 48 by 48 points. A new cycle from the
app's tile is 240 by 240 points: four arrows clockwise, the band 50 points wide.

The [`generated-shapes`](../../examples/generated-shapes.simplediagram/) example holds a cycle as its tile
places it.
