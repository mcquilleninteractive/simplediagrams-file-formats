# `com.simplediagrams.pyramid`, version 1

A **pyramid** is a triangle cut across into layers, often used for a hierarchy from the few at the top
to the many at the base. In its other form, a **funnel**, it narrows from a wide top to a spout, as a
sales funnel does. Each layer is one part a person can paint and label. First drawn by SimpleDiagrams
5.2.

Read [generated-shapes.md](../../generated-shapes.md) first for the generated record every reader reads.
This page gives what a pyramid version 1 record holds beyond it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `generator`, `generator_version` | `com.simplediagrams.pyramid`, `1` (for both forms) |
| Settings | `form` (below) |
| Roles and counts | `layer`, 2 to 8 parts |
| Slot on each part | `label` |
| Points each part owns | 2: its left side, then its right side |
| The shape's own points | 2 and 3 only: the top middle and the bottom middle |
| `own_text` | Absent: a pyramid has no text of its own, and its element's `text_content` is empty |
| `stacking` | `last_in_front` |
| `resizing` | `free` |
| Sequence | Top to bottom |
| Incomplete form in format 19 | Yes, with the words listed for format 19 below only |

## Settings

| Setting | Type | Values |
| --- | --- | --- |
| `form` | word, open | `pyramid` (a triangle, its apex at the top) or `funnel` (wide at the top, narrowing to a spout a fifth as wide) |

The words above are those listed for format 19. The open setting `form` may gain words in a later
release, within version 1. A reader keeps a word it does not know; the incomplete form takes only the
words listed for format 19.

There are no rules beyond these (no check 3).

## Parts

Each layer is one part with role `layer`:

- **Its outline** is one loop of lines, clockwise from its top-left corner: top edge, right side,
  bottom edge, left side. A pyramid's top layer has no top edge and is a triangle of three lines.
- **Its slot**, `label`, holds the layer's words.
- **Its points**, two numbers of 8 or more, are the middles of its left and right sides, in that
  order.

The shape's own point 2 is the top middle (a pyramid's apex, a funnel's rim) and 3 the bottom middle.
It has no other point among 0 to 7.

## How the app draws it

**Informative.** One figure fills `made_at`, symmetric about its middle, and is cut across into layers
of equal height parted by 4-point gaps (less when the layers are thin). A layer's label sits in the
largest upright box inside it, less a 6-point margin. The smallest frame the app draws for is 24 points
wide and 12 tall for each layer. A new pyramid from the app's tile is 240 by 200 points, three layers.

The [`generated-shapes`](../../examples/generated-shapes.simplediagram/) example holds a pyramid as its
tile places it.
