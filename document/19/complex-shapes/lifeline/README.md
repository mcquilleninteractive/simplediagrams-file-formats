# `lifeline`, version 1

A **lifeline** is a participant in a UML sequence diagram: a head holding its name, and a dashed line
down from the bottom centre of the head to the bottom of the frame. Messages are connectors attached
along that line. First drawn by SimpleDiagrams 5.1.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a lifeline version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `lifeline`, `1` |
| Settings | `head_style`, `ends_in_destroy_mark` (below) |
| Roles | None: `parts` is `[]` |
| `members` | Not allowed |
| Own text | The participant's name, the element's `text_content` |
| `own_text_layout` | Not allowed |
| Connection points | The element's own, listed as on any element, and attach line `0` |
| Flips | Not allowed |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `head_style` | word | `box`: the name in a box. `actor`: a stick figure with the name below it | `box` |
| `ends_in_destroy_mark` | boolean | `true`: the line ends in UML's destroy mark, an X centred on the line's end | `false` |

## Parts

A lifeline has no parts.

## Rules

**Geometry.** The head spans the full width of the frame at its top and is 40 points tall for `box`, 72
points for `actor`. The line runs from the bottom centre of the head (`x` = 0.5 of the frame) down to
the bottom of the frame, or, with a destroy mark, to 12 points above the bottom, where the centre of the
24-point mark is.

**Smallest height.** The frame's height is at least the head's height plus 20 points of line, plus 12
more with a destroy mark (with a relative allowance of 10⁻⁹):

| `head_style` | Without destroy mark | With destroy mark |
| --- | --- | --- |
| `box` | 60 | 72 |
| `actor` | 92 | 104 |

## Connection points

**The attach line.** A lifeline offers connectors one **attach line**, number `0`: its dashed line,
measured from its start at the head. A connector end may be attached anywhere along it; how such an
attachment is stored is specified in [connections.md](../../connections.md). A lifeline offers the
attach line whatever its `connection_points` are. A callout's tail cannot lock to the attach line, but
it may lock to one of the lifeline's numbered connection points.

Its numbered points are the element's own, numbered and placed as on any element. A new lifeline made
by the app has one, number `2`, at the top centre of its head.

## How the app draws it

**Informative.** The head in the shape's fill and line, holding the name (or, for `actor`, a stick
figure with the name below it), and the dashed line below it in the shape's line colour.

## An example

```json
{
  "type": "lifeline",
  "version": 1,
  "settings": {"head_style": "actor", "ends_in_destroy_mark": false},
  "parts": []
}
```

The [`lifelines`](../../examples/lifelines.simplediagram/) example holds a lifeline of each head style,
one ending in a destroy mark, with messages attached along their attach lines.
