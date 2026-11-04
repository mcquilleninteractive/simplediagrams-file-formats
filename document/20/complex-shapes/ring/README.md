# `ring`

A **ring** is a circle cut into sections, like a donut or pie chart: each section is one part a person
can paint and label, and the band can be a ring of a given width or a solid disc. Each section may
hold a share, a percentage of the circle. It shows how a whole is shared out, or a set of things that
belong together. First drawn by SimpleDiagrams 5.2; shares from document format 20, SimpleDiagrams
5.3.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a ring holds within it in document format 20. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `ring`, `1` |
| Settings | `ring_width` (below) |
| Roles and counts | `section`, 1 to 12 parts, clockwise from twelve o'clock |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty |
| `own_text_layout` | Not allowed |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `ring_width` | number or word | A multiple of 10 from 10 to 10,000: how wide the band is, in points. Or the word `full`: a solid disc cut into slices | 40 |

## Parts

Each section is one part with role `section`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The section's identity |
| `role` | Yes | `section` | |
| `text` | Yes | At most 500 characters | The section's words, written even when empty, because an empty text still carries the style the next words take |
| `text_layout` | No | A placed text layout ([elements.md](../../elements.md#123-parts)) | Where a person placed the words |
| `style` | No | Part paint ([elements.md](../../elements.md#123-parts)) | The section's own paint |
| `size` | No | A whole number from 1 to 100. New in document format 20 | The section's share of the circle, as a percentage |
| `point_ids` | Yes | Exactly one number, 8 or more | The section's point |

## Rules

Sections may hold shares or not, each on its own. A ring whose sections hold none has equal sections,
as in document format 19. Otherwise the sections without a share share equally what the others leave
of 100, the first of them taking 1 more each until it is all shared: shares of 30, none and none count
as 30, 35 and 35. The shares, counted so, add up to at most 100, and each counts at least 1; a ring
whose shares break either rule is refused: "The shape … is not a valid complex shape." Every other
value the record allows can be drawn. A section's `size` came with document format 20, so a document
claiming format 19 or lower that holds one is refused the same way. SimpleDiagrams 5.2, which reads
document format 19, refuses it too.

## Connection points

A ring places its own points: its element lists none, `"connection_points": []`, and the reader works
them out from the shape as it reads the document.

- Points 0 to 7 lie on the circle: at its left, right, top and bottom (0, 1, 2, 3) and at the four
  diagonals (4 top-left, 5 bottom-left, 6 top-right, 7 bottom-right).
- Each section's point sits at the middle of its outer edge, on the circle.

## How the app draws it

**Informative.** The circle fills the smaller side of the frame, centred, so a ring stays round in a
frame of any shape; a person resizes a ring keeping its proportions. The band is `ring_width` wide, or
the whole radius when that is wider, which draws as `full`. A ring whose sections hold no share has equal
sections, the first starting at twelve o'clock, with a gap of up to 4 points between neighbours along the
band's middle, so each section's sides run straight out from the centre. Once its sections hold shares,
each section takes exactly its percentage of the circle, clockwise from twelve o'clock, and
neighbours touch, the side between them one line drawn alike for both; shares that add up to less
than 100 leave the rest of the circle empty after the last section, a missing piece. Each section is filled and outlined; where two
overlap, the later one is drawn in front. A section's words are centred on the band's middle at the
section's middle angle, in a box twice as wide as tall, as large as fits inside the section with 6
points to spare. The smallest frame the app draws a ring for is 24 by 24 points; a smaller frame shows
the smallest ring shrunk to fit. A new ring from the app's tile is 200 by 200 points, one section, its
band 40 points wide.

## An example

A ring of three sections; the second has its own fill, and a person placed and turned the third's
words. The texts are shortened here; each is a complete text record.

```json
{
  "type": "ring",
  "version": 1,
  "settings": {"ring_width": 40},
  "parts": [
    {"id": "0D4C2E8A-3B1F-4A6E-9C2D-5E7F8A9B0C11", "role": "section", "text": "… North …",
     "point_ids": [8]},
    {"id": "1E5D3F9B-4C2A-4B7F-8D3E-6F8A9B0C1D22", "role": "section", "text": "… South …",
     "style": {"fill_hex": "#F59F00"}, "point_ids": [9]},
    {"id": "2F6E4A0C-5D3B-4C8A-9E4F-7A9B0C1D2E33", "role": "section", "text": "… West …",
     "text_layout": {"frame": {"x": 0.06, "y": 0.44, "width": 0.3, "height": 0.1},
                     "position": "middle", "rotation_degrees": 350},
     "point_ids": [10]}
  ]
}
```

The [`complex-shapes`](../../examples/complex-shapes.simplediagram/) example holds a ring as its tile
places it; [`complex-shape-placement`](../../examples/complex-shape-placement.simplediagram/) a turned
ring and one in a frame twice as wide as tall; and [`part-paint`](../../examples/part-paint.simplediagram/)
a ring whose sections paint themselves.
