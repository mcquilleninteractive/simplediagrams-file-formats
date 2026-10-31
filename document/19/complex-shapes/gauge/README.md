# `gauge`, version 1

A **gauge** is a half dial, like a speedometer: coloured bands from a minimum on the left, over the
top, to a maximum on the right, a needle pointing at a value, and a caption below. It shows where one
number stands in its range. Each band is a part a person can paint and label, and so is the needle,
which takes paint but no words. First drawn by SimpleDiagrams 5.2.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a gauge version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `gauge`, `1` |
| Settings | `minimum`, `maximum`, `value`, `auto_rotate_labels` (below) |
| Roles and counts | `band`, 1 to 5 parts, from the minimum to the maximum; then `needle`, exactly 1 |
| `members` | Not allowed |
| Own text | The caption, the element's `text_content`, at most 500 characters |
| `own_text_layout` | Allowed: where a person placed the caption. Absent: below the needle's hub |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `minimum` | number | −1,000,000 to 1,000,000: the left end of the range | 0 |
| `maximum` | number | −1,000,000 to 1,000,000: the right end of the range | 100 |
| `value` | number | −1,000,000 to 1,000,000: where the needle points | 50 |
| `auto_rotate_labels` | boolean | Whether each band's words are turned to follow their band, or level | `true` |

## Parts

All the bands come first, then the needle.

| Role | Keys | Rules |
| --- | --- | --- |
| `band` | `id`, `role` and `text` required; `text_layout` and `style` optional | `text`: the band's words, at most 500 characters, written even when empty |
| `needle` | `id` and `role` required; `style` optional | No words and no points |

`id`s are unique among the shape's parts. A band's `text_layout` is a placed text layout and a part's
`style` is part paint ([elements.md](../../elements.md#123-parts)). No part owns a point.

## Rules

`minimum` is below `maximum`, and `value` lies from `minimum` to `maximum` inclusive. The schema cannot
check this rule, or that the needle comes last.

## Connection points

A gauge places its own points: its element lists none, `"connection_points": []`, and the reader works
them out from the shape as it reads the document. They are 0 and 1 at the ends of the half circle, 2 at
its top, 4 and 6 on the arc at the upper diagonals, and 3, 5 and 7 along the bottom of the caption's
room: middle, left and right. Its parts own none.

## How the app draws it

**Informative.** The half circle and the caption's room below it, together 2 by 1.4 times the radius,
are as large as fit in the frame, centred; a person resizes a gauge keeping its proportions. The bands
are a quarter of the radius deep and share the half circle equally, with small gaps between them. The
needle, drawn in front of the bands with its round hub, points at the value's share of the range, its
tip in the middle of the bands. A band's words are centred on the band in a box twice as wide as tall,
as large as fits with 2 points to spare; with `auto_rotate_labels` they are turned to lie along the
band, never upside down. The caption's box runs from the hub down to the bottom of the room. The
smallest frame the app draws a gauge for is 40 by 28 points; a smaller frame shows the smallest gauge
shrunk to fit. A new gauge from the app's tile is 200 by 140 points: three bands from 0 to 100, the
needle at 50.

## An example

A gauge of three bands with the caption "Load" in its element's `text_content`. The texts are shortened
here; each is a complete text record.

```json
{
  "type": "gauge",
  "version": 1,
  "settings": {"minimum": 0, "maximum": 100, "value": 50, "auto_rotate_labels": true},
  "parts": [
    {"id": "D4135FB1-02E6-4735-83F4-8FA0B1C2D3EE", "role": "band", "text": "… Low …"},
    {"id": "E52460C2-13F7-4846-94A5-90B1C2D3E4FF", "role": "band", "text": "… Normal …"},
    {"id": "F63571D3-2408-4957-85B6-A1C2D3E4F500", "role": "band", "text": "… High …"},
    {"id": "07468204-3519-4A68-96C7-B2D3E4F50611", "role": "needle"}
  ]
}
```

The [`gauges`](../../examples/gauges.simplediagram/) example holds gauges at both ends of their range and
one with level labels.
