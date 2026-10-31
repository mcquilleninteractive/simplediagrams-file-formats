# `cycle`, version 1

A **cycle** is a set of steps arranged round a circle, each pointing to the next, for a process that
repeats: plan, do, check, act. Each step is one part a person can paint and label. First drawn by
SimpleDiagrams 5.2.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a cycle version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `cycle`, `1` |
| Settings | `ring_width`, `direction`, `arrow_style` (below) |
| Roles and counts | `step`, 3 to 8 parts, in its direction from twelve o'clock |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty |
| `own_text_layout` | Not allowed |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `ring_width` | number | A multiple of 10 from 10 to 10,000: how wide the band of steps is, in points. Unlike a ring's, it takes no word | 50 |
| `direction` | word | `clockwise` or `anticlockwise` | `clockwise` |
| `arrow_style` | word | `arrow` (each step a curved band ending in an arrowhead wider than the band) or `chevron` (each step as wide as the band, notched at its back and pointed at its front) | `arrow` |

## Parts

Each step is one part with role `step`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The step's identity |
| `role` | Yes | `step` | |
| `text` | Yes | At most 500 characters | The step's words, written even when empty |
| `text_layout` | No | A placed text layout ([elements.md](../../elements.md#123-parts)) | Where a person placed the words |
| `style` | No | Part paint ([elements.md](../../elements.md#123-parts)) | The step's own paint |
| `point_ids` | Yes | Exactly one number, 8 or more | The step's point |

## Rules

There are no rules beyond these: every value the record allows can be drawn.

## Connection points

A cycle places its own points: its element lists none, `"connection_points": []`, and the reader works
them out from the shape as it reads the document.

- Points 0 to 7 lie on the band's outer edge, at left, right, top and bottom (0, 1, 2, 3) and at the
  four diagonals (4 top-left, 5 bottom-left, 6 top-right, 7 bottom-right), as a ring's do.
- Each step's point sits at the middle of its outer edge.

## How the app draws it

**Informative.** The circle fills the smaller side of the frame, centred; a person resizes a cycle
keeping its proportions. The band is `ring_width` wide, or narrower where the circle is too small, so
that the middle keeps a quarter of the radius empty. The steps share the circle equally, the first
centred at twelve o'clock, with small gaps between them; a chevron's point fills the next step's notch,
each later step drawn in front of the one before. An `anticlockwise` cycle is the `clockwise` one
mirrored left to right. A step's words are centred on the band in a box twice as wide as tall, as large
as fits with 6 points to spare. The smallest frame the app draws a cycle for is 48 by 48 points; a
smaller frame shows the smallest cycle shrunk to fit. A new cycle from the app's tile is 240 by 240
points: four arrows clockwise, the band 50 points wide.

## An example

A cycle of four arrows. The texts are shortened here; each is a complete text record.

```json
{
  "type": "cycle",
  "version": 1,
  "settings": {"ring_width": 50, "direction": "clockwise", "arrow_style": "arrow"},
  "parts": [
    {"id": "90DF1B7D-CEA2-43F1-8FB0-4B6C7D8E9FAA", "role": "step", "text": "… Plan …", "point_ids": [8]},
    {"id": "A1E02C8E-DFB3-4402-90C1-5C7D8E9FA0BB", "role": "step", "text": "… Do …", "point_ids": [9]},
    {"id": "B2F13D9F-E0C4-4513-81D2-6D8E9FA0B1CC", "role": "step", "text": "… Check …", "point_ids": [10]},
    {"id": "C3024EA0-F1D5-4624-92E3-7E9FA0B1C2DD", "role": "step", "text": "… Act …", "point_ids": [11]}
  ]
}
```

The [`complex-shapes`](../../examples/complex-shapes.simplediagram/) example holds a cycle as its tile
places it.
