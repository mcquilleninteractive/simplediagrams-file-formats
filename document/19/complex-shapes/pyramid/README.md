# `pyramid`, version 1

A **pyramid** is a triangle cut across into layers, for a hierarchy from its base to its apex; as a
**funnel**, wide at the top and narrowing to a spout, it shows a whole narrowing down in stages. Each
layer is one part a person can paint and label. First drawn by SimpleDiagrams 5.2.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a pyramid version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `pyramid`, `1` |
| Settings | `form` (below) |
| Roles and counts | `layer`, 2 to 8 parts, top to bottom |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty |
| `own_text_layout` | Not allowed |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `form` | word | `pyramid` (a triangle, its apex at the top) or `funnel` (wide at the top, narrowing to a spout a fifth as wide) | `pyramid` |

## Parts

Each layer is one part with role `layer`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The layer's identity |
| `role` | Yes | `layer` | |
| `text` | Yes | At most 500 characters | The layer's words, written even when empty |
| `text_layout` | No | A placed text layout ([elements.md](../../elements.md#123-parts)) | Where a person placed the words |
| `style` | No | Part paint ([elements.md](../../elements.md#123-parts)) | The layer's own paint |
| `point_ids` | Yes | Exactly two numbers, each 8 or more | The middles of the layer's left side, then its right side |

## Rules

There are no rules beyond these: every value the record allows can be drawn.

## Connection points

A pyramid places its own points: its element lists none, `"connection_points": []`, and the reader
works them out from the shape as it reads the document.

- Among 0 to 7 it has only two: 2, the top middle (a pyramid's apex, a funnel's rim), and 3, the bottom
  middle.
- Each layer's two points are the middles of its left side and its right side, in that order.

## How the app draws it

**Informative.** One figure fills the frame, symmetric about its middle, and is cut across into layers
of equal height parted by 4-point gaps (less when the layers are thin); a person resizes a pyramid
freely. A pyramid's top layer is a triangle. A layer's words sit in the largest upright box inside it,
less a 6-point margin. The smallest frame the app draws a pyramid for is 24 points wide and 12 tall for
each layer; a smaller frame shows the smallest pyramid shrunk to fit. A new pyramid from the app's tile
is 240 by 200 points, three layers.

## An example

A pyramid of three layers. The texts are shortened here; each is a complete text record.

```json
{
  "type": "pyramid",
  "version": 1,
  "settings": {"form": "pyramid"},
  "parts": [
    {"id": "6DAC8E4A-9B7F-40CE-9C8D-1E3F4A5B6C77", "role": "layer", "text": "… Vision …", "point_ids": [8, 9]},
    {"id": "7EBD9F5B-AC80-41DF-8D9E-2F4A5B6C7D88", "role": "layer", "text": "… Goals …", "point_ids": [10, 11]},
    {"id": "8FCE0A6C-BD91-42E0-9EAF-3A5B6C7D8E99", "role": "layer", "text": "… Tasks …", "point_ids": [12, 13]}
  ]
}
```

The [`complex-shapes`](../../examples/complex-shapes.simplediagram/) example holds a pyramid as its tile
places it, and [`complex-shape-placement`](../../examples/complex-shape-placement.simplediagram/) one in a
wide, short frame.
