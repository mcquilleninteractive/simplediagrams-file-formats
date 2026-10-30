# `com.simplediagrams.flow`, version 1

A **flow** is a row of steps that shows a process in order: chevrons pointing the way, boxes, circles
or other shapes, laid across the page or down it. Each step is one part a person can paint and label.
First drawn by SimpleDiagrams 5.2.

Read [generated-shapes.md](../../generated-shapes.md) first for the generated record every reader reads.
This page gives what a flow version 1 record holds beyond it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `generator`, `generator_version` | `com.simplediagrams.flow`, `1` |
| Settings | `base_shape`, `direction`, `spacing`, `pointer` (below) |
| Roles and counts | `step`, 2 to 12 parts |
| Slot on each part | `label` |
| Points each part owns | 2: its back, then its front |
| The shape's own points | All of 0 to 7 |
| `own_text` | Absent: a flow has no text of its own, and its element's `text_content` is empty |
| `stacking` | `first_in_front`, so each step's point lies over the next step |
| `resizing` | `free` |
| Sequence | First step to last |
| Incomplete form in format 19 | Yes, with the words listed for format 19 below only |

## Settings

| Setting | Type | Values |
| --- | --- | --- |
| `base_shape` | word, open | The shape every step takes: `chevron` (a notched back and a pointed front), `arrow-box` (a box with a pointed front), `block-arrow` (a shaft with an arrowhead), `rectangle`, `rounded-rectangle`, `circle`, `hexagon` |
| `direction` | word, closed | `across` (left to right) or `down` (top to bottom) |
| `spacing` | word, open | How neighbouring steps meet: `gap`, `touch` or `overlap` |
| `pointer` | `true` or `false` | Whether each step but the last has a small point at its front |

The words above are those listed for format 19. The open settings `base_shape` and `spacing` may gain
words in a later release, within version 1. A reader keeps a word it does not know; the incomplete form
takes only the words listed for format 19.

## Rules

A known base shape allows its spacing and pointer: only `rectangle`, `rounded-rectangle` and `circle`
allow `overlap` and a `pointer` of `true`. An unknown base shape sets both rules aside. An unknown
spacing sets aside only the rule on spacing: a `pointer` of `true` still needs one of those three base
shapes.

## Parts

Each step is one part with role `step`:

- **Its outline** is one loop of lines, or of lines and arcs for rounded rectangles and circles,
  following its base shape, with its pointer where it has one.
- **Its slot**, `label`, holds the step's words.
- **Its points**, two numbers of 8 or more, are its back and its front, in that order: the middles of its
  two ends along the flow, a chevron's back in its notch and its front at the tip of its point or
  pointer.

The shape's own points 0 to 7 are at the standard eight places on the box around everything drawn.

## How the app draws it

**Informative.** The steps share the frame's length equally and are as thick as the frame, every step the
same. With `gap`, plain steps are 12 points apart and pointed ones 6; with `touch` they meet; with
`overlap` each overlaps the next by a fifth of a step. A step's point is 35% of its thickness deep. A
`down` flow is the `across` one reflected in the diagonal through the top-left corner. A step's label
sits in the largest upright box inside it, less a 6-point margin. The smallest frame the app draws for is
16 points along for each step by 16 across. A new flow from the app's tile is 360 by 80 points: three
chevrons across, with a gap.

The [`flows`](../../examples/flows.simplediagram/) example holds a flow of each base shape.
