# `callout`, version 1

A **callout** is a speech bubble with an optional tail. The tail may point anywhere, or be locked to a
numbered connection point of another shape, so it follows that shape. First drawn by SimpleDiagrams
5.1.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a callout version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `callout`, `1` |
| Settings | Optionally `tail_tip` and `tail_attachment` (below) |
| Roles | None: `parts` is `[]` |
| `members` | Not allowed |
| Own text | The callout's words, the element's `text_content` |
| `own_text_layout` | Not allowed |
| Connection points | The element's own, listed as on any element ([elements.md](../../elements.md#8-connection-points)) |
| Flips | Allowed: the only complex shape that may be flipped |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `tail_tip` | point | Where the tail points, in diagram points from the centre of the frame along the bubble's own axes, before rotation and flipping. Optional: absent, the callout has no tail | Absent |
| `tail_attachment` | attachment | Locks the tail to a numbered connection point of another element on the same page. Optional, and only with `tail_tip` | Absent |

**Placing the tip on the page.** Take the point `(width / 2 + tail_tip.x, height / 2 + tail_tip.y)`
relative to the frame's top-left corner; mirror it about the frame's centre for each flip that is
`true`; then turn it about the frame's centre by `rotation_degrees`. A free tail therefore moves and
turns with its bubble.

**A locked tail.** A tail with `tail_attachment` is drawn to its target's connection point, wherever
that shape now is. `tail_tip` records where the tail was last drawn, and is used again if the lock is
removed.

## Parts

A callout has no parts.

## Rules

- `tail_attachment` comes only with `tail_tip`.
- `tail_attachment` names a connection point that an element on the **same page** has, never the
  callout itself ([elements.md](../../elements.md#1210-rules-across-the-page)).
- The tip, placed on the page, lies within ±1,000,000,000 on both axes. For a locked tail, the drawn tip,
  expressed in the bubble's own axes and placed back on the page, does too.

The schema checks only the first rule.

## Connection points

The callout's points are the element's own, numbered and placed as on any element. A new callout made
by the app has the standard eight.

## How the app draws it

**Informative.** A rounded bubble in the shape's fill and line, with its words inside, and a tail from
its edge to the tip. A tip that lies inside the bubble's frame, or on its edge, draws no tail; it is
kept, so moving the bubble or the tip outward shows the tail again. A new callout made by the app has
`tail_tip` `{"x": -40, "y": 70}`.

## An example

```json
{
  "type": "callout",
  "version": 1,
  "settings": {
    "tail_tip": {"x": -120, "y": 60},
    "tail_attachment": {"element_id": "7E1A3C5F-9B2D-4E6A-8C0B-1D3F5A7C9E02", "connection_point_id": 6}
  },
  "parts": []
}
```

The [`callout`](../../examples/callout.simplediagram/) example holds a callout whose tail is locked to
a rectangle's point, and a flipped callout with a free tail.
