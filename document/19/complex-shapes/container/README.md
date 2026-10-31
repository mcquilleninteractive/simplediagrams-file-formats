# `container`, version 1

A **container** is a frame with a title that holds other shapes: a box round the web tier of a system,
a team's area on a board. It records the shapes it holds, in order, and moves, locks and deletes them
with it. First drawn by SimpleDiagrams 5.1.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a container version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `container`, `1` |
| Settings | `layout`, `title_style`, and optionally `artwork` (below) |
| Roles | None: `parts` is `[]` |
| `members` | Required: the shapes the container holds, in order. `[]` when it holds none |
| Own text | The title, the element's `text_content` |
| `own_text_layout` | Not allowed: the title sits where `title_style` puts it |
| Connection points | The element's own, listed as on any element ([elements.md](../../elements.md#8-connection-points)) |
| Flips | Not allowed |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `layout` | word | `free`, `automatic` or `proportional` (below) | `free` |
| `title_style` | word | `band`: the title along the top. `corner_tag`: the title in a tag in the top-left corner, sized to the title, as a UML frame names itself | `band` |
| `artwork` | embedded asset | An embedded asset of kind `editable_vector` the document holds. Optional: absent, the container is the app's own rounded box | Absent |

**`layout`.**

- `free`: members stay where they are put.
- `automatic`: the members are meant to sit in order, left to right and then down. **What it does in
  SimpleDiagrams 5.2.** The app keeps and draws an automatic container and its members exactly as
  stored: it does not move the members or resize the container on its own, either when the document
  opens or when it is edited. Members are drawn where they are stored.
- `proportional`: when a person resizes the container, its members keep their sizes, and their places
  spread out or close in with it. Nothing more is stored for it: members are drawn where they are
  stored, and where they go is worked out only when a person resizes the container.

**`artwork`.** The container wears this drawing in place of its plain box, stretched to its frame. It
is painted as an `editable_vector` element is: the drawing's fill- and line-coloured parts take the
container's `fill_hex`, `stroke_hex` and `line_width`, and its `stroke_pattern_override` applies to the
line-coloured parts ([elements.md](../../elements.md#5-style)). Its title sits where `title_style` puts
it, and it holds, moves, locks and resizes exactly as any container does.

## Parts

A container has no parts. Its members are on its whole body, in the record's `members`
([elements.md](../../elements.md#125-members)): at most 1,000, none repeated, never a connector.

## Rules

- A container whose `layout` is `automatic` or `proportional` is never turned: its `rotation_degrees`,
  reduced to one turn, is 0 (so 0, 360 and −720 are all accepted).
- Its members keep the rules across the page ([elements.md](../../elements.md#1210-rules-across-the-page)).

## Connection points

The container's points are the element's own, numbered and placed as on any element. A new container
made by the app has the standard eight.

## How the app draws it

**Informative.** A rounded box in the shape's fill and line, or the `artwork` drawing, with the title in
a 32-point band along the top or in a corner tag sized to the title each time it is drawn. A new
container made by the app has an empty title whose `base_alignment` is `leading`.

**The arrangement of an automatic container.** The RECOMMENDED placement for an automatic container's
members, and its height, is this arrangement. Members keep their sizes and are placed in list order,
each in a slot as large as its upright bounding box: from 12 points inside the left edge and 32 points
below the top, 12 points apart, wrapping to a new row before crossing 12 points inside the right edge.
Each row is as tall as its tallest slot, and members are centred on their row's middle line. The
container's height is the title band, the rows and 12 points of padding at the bottom.

**Resizing a proportional container.** The app keeps each member's centre at the same fraction of the
container's content area (12 points in from the sides and bottom, below a 32-point title band), held
inside that area, and moves no member that is locked. A container inside a proportional container moves
like any member and then spreads its own members when it is resized.

## An example

```json
{
  "type": "container",
  "version": 1,
  "settings": {"layout": "free", "title_style": "corner_tag"},
  "members": [{"kind": "element", "id": "3B9E1F2A-6C4D-4E8F-A0B2-C4D6E8F0A1B3"}],
  "parts": []
}
```

The [`container`](../../examples/container.simplediagram/) example holds a container of each title
style, and [`proportional-container`](../../examples/proportional-container.simplediagram/) one whose
layout is `proportional`.
