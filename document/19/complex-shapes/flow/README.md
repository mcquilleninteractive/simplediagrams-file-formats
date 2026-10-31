# `flow`, version 1

A **flow** is a row of steps that shows a process in order: chevrons pointing the way, boxes, circles
or other shapes, laid across the page or down it. Each step is one part a person can paint and label.
First drawn by SimpleDiagrams 5.2.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a flow version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `flow`, `1` |
| Settings | `base_shape`, `direction`, `spacing`, `pointer` (below) |
| Roles and counts | `step`, 2 to 12 parts, first step to last |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty |
| `own_text_layout` | Not allowed |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `base_shape` | word | The shape every step takes: `chevron` (a notched back and a pointed front), `arrow_box` (a box with a pointed front), `block_arrow` (a shaft with an arrowhead), `rectangle`, `rounded_rectangle`, `circle`, `hexagon` | `chevron` |
| `direction` | word | `across` (left to right) or `down` (top to bottom) | `across` |
| `spacing` | word | How neighbouring steps meet: `gap`, `touch` or `overlap` | `gap` |
| `pointer` | boolean | Whether each step but the last has a small point at its front | `false` |

## Parts

Each step is one part with role `step`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The step's identity |
| `role` | Yes | `step` | |
| `text` | Yes | At most 500 characters | The step's words, written even when empty |
| `text_layout` | No | A placed text layout ([elements.md](../../elements.md#123-parts)) | Where a person placed the words |
| `style` | No | Part paint ([elements.md](../../elements.md#123-parts)) | The step's own paint |
| `point_ids` | Yes | Exactly two numbers, each 8 or more | The step's back, then its front |

## Rules

A base shape allows its spacing and pointer: only `rectangle`, `rounded_rectangle` and `circle` allow a
`spacing` of `overlap` and a `pointer` of `true`. A `chevron`, `arrow_box`, `block_arrow` or `hexagon`
flow has a `spacing` of `gap` or `touch` and a `pointer` of `false`.

## Connection points

A flow places its own points: its element lists none, `"connection_points": []`, and the reader works
them out from the shape as it reads the document.

- Points 0 to 7 are at the standard eight places ([elements.md](../../elements.md#8-connection-points))
  on the box around everything the flow draws.
- Each step's two points are the middles of its two ends along the flow: its back, then its front, a
  chevron's back in its notch and its front at the tip of its point or pointer.

## How the app draws it

**Informative.** The steps share the frame's length equally and are as thick as the frame, every step
the same; a person resizes a flow freely. With `gap`, plain steps are 12 points apart and pointed ones
6; with `touch` they meet; with `overlap` each overlaps the next by a fifth of a step. A step's point is
35% of its thickness deep. Where two steps overlap, the earlier one is drawn in front, so each step's
point lies over the next step. A `down` flow is the `across` one reflected in the diagonal through the
top-left corner. A step's words sit in the largest upright box inside it, less a 6-point margin. The
smallest frame the app draws a flow for is 16 points along for each step by 16 across; a smaller frame
shows the smallest flow shrunk to fit. A new flow from the app's tile is 360 by 80 points: three
chevrons across, with a gap.

## An example

A flow of three chevrons. The texts are shortened here; each is a complete text record.

```json
{
  "type": "flow",
  "version": 1,
  "settings": {"base_shape": "chevron", "direction": "across", "spacing": "gap", "pointer": false},
  "parts": [
    {"id": "3A7F5B1D-6E4C-4D9B-8F5A-8B0C1D2E3F44", "role": "step", "text": "… Plan …", "point_ids": [8, 9]},
    {"id": "4B8A6C2E-7F5D-4EAC-9A6B-9C1D2E3F4A55", "role": "step", "text": "… Build …", "point_ids": [10, 11]},
    {"id": "5C9B7D3F-8A6E-4FBD-8B7C-0D2E3F4A5B66", "role": "step", "text": "… Ship …", "point_ids": [12, 13]}
  ]
}
```

The [`flows`](../../examples/flows.simplediagram/) example holds a flow of each base shape.
