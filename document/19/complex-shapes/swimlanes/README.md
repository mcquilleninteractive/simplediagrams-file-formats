# `swimlanes`, version 1

**Swimlanes** are one shape divided into parallel lanes, each with a title strip and the shapes it
holds: who does which step of a process. A title band across the top holds the shape's own title.
First drawn by SimpleDiagrams 5.1.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what swimlanes version 1 hold within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `swimlanes`, `1` |
| Settings | `direction`, `title_strip_size`, `title_band_size` (below) |
| Roles and counts | `lane`, 1 to 50 parts, from the top (horizontal) or from the left (vertical) |
| `members` | Not allowed: each lane holds its own |
| Own text | The title across the top, the element's `text_content` |
| `own_text_layout` | Not allowed |
| Connection points | The element's own, listed as on any element ([elements.md](../../elements.md#8-connection-points)) |
| Flips | Not allowed |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `direction` | word | `horizontal`: lanes are rows, their title strips down the left side. `vertical`: lanes are columns, their title strips across the top of each lane | `horizontal` |
| `title_strip_size` | number | 28 to 10,000: how thick every lane's title strip is, in diagram points | 28 |
| `title_band_size` | number | 28 to 10,000: how tall the band across the top is, in diagram points | 28 |

## Parts

Each lane is one part with role `lane`, holding exactly these keys:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's lanes | The lane's identity |
| `role` | Yes | `lane` | |
| `text` | Yes | At most 1,000 characters and 100 runs | The lane's title |
| `size` | Yes | 0.000001 to 1,000,000 | The lane's thickness, as a share of the sum of all the lanes' sizes |
| `members` | Yes | At most 1,000; `[]` when empty | The shapes the lane holds, in order ([elements.md](../../elements.md#125-members)) |

All the lanes of one shape hold at most 2,000 members together.

## Rules

**Laying out the lanes.** The title band is `title_band_size` points tall across the top of the frame.
The lanes share what is left of the frame between them, in proportion to their `size` values:

| `direction` | Each lane's frame, before rotation | Smallest sizes the frame MUST allow |
| --- | --- | --- |
| `horizontal` | Full frame width; height = (frame height − title band) × `size` ÷ sum of sizes; stacked top to bottom below the band | Frame width at least `title_strip_size`; every lane at least 28 points tall |
| `vertical` | Full height below the band; width = frame width × `size` ÷ sum of sizes; side by side from the left | Every lane at least 28 points wide; frame height minus the title band at least `title_strip_size` |

A frame too small for its lanes refuses the document. The comparison allows a relative shortfall of
10⁻⁹, so a size worked out from shares that lands a rounding error below its smallest still passes. The
schema cannot check this rule.

Swimlanes are never a member of a container or a lane, and the lanes' members keep the rules across the
page ([elements.md](../../elements.md#1210-rules-across-the-page)).

## Connection points

The shape's points are the element's own, numbered and placed as on any element. New swimlanes made by
the app have the standard eight.

## How the app draws it

**Informative.** The frame in the shape's fill and line, the title band along its top holding the
shape's title, and each lane with its title in its title strip. New swimlanes made by the app have three horizontal lanes of `size` 1, titled "Lane 1"
to "Lane 3" in the language of the Mac that made them.

## An example

The lane's title is shortened here; it is a complete text record.

```json
{
  "type": "swimlanes",
  "version": 1,
  "settings": {"direction": "horizontal", "title_strip_size": 28, "title_band_size": 28},
  "parts": [
    {
      "id": "5C0A7E21-94B3-4D6F-8A1E-0B2C3D4E5F60",
      "role": "lane",
      "text": "… Sales …",
      "size": 1,
      "members": [{"kind": "element", "id": "9F1D2C3B-4A5E-4F60-8172-93A4B5C6D7E8"}]
    }
  ]
}
```

The [`swimlanes`](../../examples/swimlanes.simplediagram/) example holds swimlanes of two lanes.
