# `audience`

An **audience** is a half-circle arch of seats, drawn as dots in rows and divided into **groups**, like
the seats of an assembly. Each group is one part: a number of seats, which a person can paint. The
groups take wedges of the arch from left to right in the order they are listed, and the total of their
seats may be drawn in the middle. It shows at a glance how a body of people, such as an assembly, a
board or a panel, divides. First drawn by SimpleDiagrams 5.3; new in document format 20.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what an audience holds within it in document format 20. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `audience`, `1` |
| Settings | `show_total` (below) |
| Roles and counts | `group`, 1 to 20 parts, from the left end of the arch to the right |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty. The total is worked out from the groups, never typed |
| `own_text_layout` | Not allowed |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `show_total` | boolean | Whether the total of the groups' seats is drawn in the middle of the arch | `true` |

## Parts

An audience has 1 to 20 groups, in order from the left end of the arch to the right. Each group is one
part with role `group`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The group's identity |
| `role` | Yes | `group` | |
| `size` | Yes | A whole number from 1 to 1,000 | The group's seats |
| `style` | No | Part paint ([elements.md](../../elements.md#123-parts)) | The group's own paint, for its dots |

A group has no words, no placed text and no points: `text`, `text_layout`, `point_ids` and every other
key of the ten are forbidden on it. A group is only a number of seats and its paint; it has no name.

## Rules

The groups' seats add up to at most 1,000, so an audience holds from 1 to 1,000 seats in all. A
document whose audience holds more is refused: "The shape … is not a valid complex shape." Nothing is
cut to fit. The schema cannot check this rule. Every other value the record allows can be drawn.

The audience came with document format 20, so a document claiming format 19 or lower that holds one is
refused the same way.

## Connection points

An audience places its own points: its element lists none, `"connection_points": []`, and the reader
works them out from the shape as it reads the document. They are 0 and 1 at the ends of the arch, 2 at
its top, 4 and 6 on the arch at the upper diagonals, left and right, and 3, 5 and 7 along its base line:
3 at its middle, and 5 and 7 half way across the band of seats, on the left and on the right. Its groups
own none.

## How the app draws it

**Informative.** The arch is the half circle inscribed in the frame, as large as fits and centred, its
straight side, the base line, at the bottom; a person resizes an audience keeping its proportions. In
its middle is an opening of 0.4 of the arch's radius. The seats lie in rows between the opening and the
arch: the fewest rows of equal depth that hold every seat with the seats as far apart along a row as the
rows are apart. Each row holds seats in proportion to its length, spread evenly along it. Taken in turn
round the arch from its left end to its right, the first group's number of seats are the first group's,
then the second group's, and so on, so each group is one wedge from the outer row to the inner. Each
seat is a dot three quarters of that spacing across, so no two dots touch, filled and outlined with its
group's paint over the shape's. With `show_total`, the total, written as numbers are in the reader's
language, is set in the opening on the base line in the element's text style, as large as the opening
allows. The seats are worked out each time the shape is drawn and never saved: the same groups in the
same frame always give the same seats. The smallest frame the app draws an audience for is 48 by 28
points; a smaller frame shows the smallest audience shrunk to fit. A new audience from the app's tile is
240 by 140 points: three groups of 50, 30 and 20 seats, each in a colour of its own, with the total
shown.

## An example

The audience as the app's tile places it: 100 seats in three groups, each with its own fill, and the
total shown. The element's `text_content` is empty.

```json
{
  "type": "audience",
  "version": 1,
  "settings": {"show_total": true},
  "parts": [
    {"id": "57A2BE6D-416E-504E-8F72-E37F2AE3CF8F", "role": "group", "size": 50,
     "style": {"fill_hex": "#4E97D2"}},
    {"id": "E750C8B0-D581-5CB6-907A-50E43027F846", "role": "group", "size": 30,
     "style": {"fill_hex": "#ED692F"}},
    {"id": "FBD15E25-54DB-588E-A76D-47F36DEA67DB", "role": "group", "size": 20,
     "style": {"fill_hex": "#4BB275"}}
  ]
}
```

The [`audiences`](../../examples/audiences.simplediagram/) example holds this audience, a board of nine
drawn with exact lines and its total hidden, and an audience of 1,000 seats, the most one holds.
