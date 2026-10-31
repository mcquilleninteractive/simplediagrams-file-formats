# Connections and groups

This page describes two records that sit on a page beside its elements: **connections**, the lines
whose ends can be attached to shapes, and **groups**, which bind elements, connections and other groups
together. Conventions, terms and limits are in [README.md](README.md); elements, including their
connection points, are in [elements.md](elements.md).

On this page:

- [Where connections and groups are stored](#where-connections-and-groups-are-stored)
- [The connection record](#the-connection-record)
- [Attachments](#attachments)
- [Where an end is drawn](#where-an-end-is-drawn)
- [Line endings](#line-endings)
- [Direct routing](#direct-routing)
- [Right-angle routing](#right-angle-routing)
- [Routing orientation](#routing-orientation)
- [Labels](#labels)
- [Locking](#locking)
- [What the app refuses in a connection](#what-the-app-refuses-in-a-connection)
- [Groups](#groups)
- [Examples](#examples)
- [Open points](#open-points)

## Where connections and groups are stored

A page is stored in nested layers ([README.md, A page, layer by layer](README.md#a-page-layer-by-layer)).
Connections and groups sit at different depths:

| Record | Location inside a page object |
| --- | --- |
| The page's connections | `content.content.connections` (layer B) |
| The page's groups | `content.groups` (layer A) |

The other `connections` and `groups` arrays in a page are left-overs from older formats and MUST be
written as `[]`.

A connection or group belongs to the page it is stored on. Everything it refers to MUST be on the same
page.

How connections are layered against elements when drawn is decided by the page's drawing order
([README.md, Drawing order](README.md#drawing-order)).

## The connection record

A connection is an ordered list of points in page coordinates, with optional attachments at its two
ends. Like a page, it is stored in two layers: an outer record holding its lock and routing, and an inner
record, under `content`, holding everything else.

```text
connection
├─ is_locked                    required
├─ routing                      optional: absent (direct) or "right_angle"
├─ routes_around_shapes         optional; ignored unless right-angle
├─ routing_rotation_degrees     optional; non-zero only when right-angle
└─ content
   ├─ id                        required
   ├─ points                    required, at least 2
   ├─ start_attachment          optional
   ├─ end_attachment            optional
   ├─ stroke_hex                required
   ├─ line_width                required
   ├─ stroke_pattern            required
   ├─ start_ending              required
   ├─ end_ending                required
   ├─ stroke_texture            optional
   └─ label                     optional
```

### Outer record

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `content` | object | Yes | The inner record, below | The connector itself |
| `is_locked` | boolean | Yes | | Whether a person locked this connector ([Locking](#locking)) |
| `routing` | string | No | `direct` or `right_angle`. Absent means `direct` | How the line is worked out from its points ([Direct routing](#direct-routing), [Right-angle routing](#right-angle-routing)) |
| `routes_around_shapes` | boolean | No | Absent means `true`. Ignored unless `routing` is `right_angle` | Whether an automatic right-angle route also avoids shapes it is not attached to |
| `routing_rotation_degrees` | number, degrees | No | At least 0 and less than 360. Absent means 0. MUST be 0 (or absent) unless `routing` is `right_angle` | The angle of the connector's right-angle axes ([Routing orientation](#routing-orientation)) |

**Informative.** The app writes `routing`, `routes_around_shapes` and `routing_rotation_degrees` only for
a right-angle connector, and then writes all three. For a direct connector it writes none of them.

### Inner record

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique in the document's shared namespace ([README.md, Identity rules](README.md#identity-rules)) | The connector's permanent identity |
| `points` | array of connector points | Yes | At least 2. Counted, with every path node, against 1,000,000 for the document | The connector's points, start first |
| `start_attachment` | attachment | No | See [Attachments](#attachments). Absent: the start is free | Binds the first point to a shape |
| `end_attachment` | attachment | No | As above. Absent: the end is free | Binds the last point to a shape |
| `stroke_hex` | colour | Yes | `#RRGGBB` or `#RRGGBBAA` | Line colour and opacity |
| `line_width` | number, diagram points | Yes | 0 to 10,000 | Line weight |
| `stroke_pattern` | string | Yes | `solid`, `dashed`, `dotted` | Line pattern |
| `start_ending` | string | Yes | A line ending ([Line endings](#line-endings)) | Decoration drawn at the start |
| `end_ending` | string | Yes | A line ending | Decoration drawn at the end |
| `stroke_texture` | string | No | A texture name ([elements.md](elements.md#1310-textures)). Absent: no texture | A hand-drawn texture for the line |
| `label` | object | No | See [Labels](#labels) | Text drawn along the line |

### Connector points

Each entry of `points`:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `x` | number, page coordinates | Yes | Within ±1,000,000,000 | Position |
| `y` | number, page coordinates | Yes | Within ±1,000,000,000 | Position |
| `kind` | string | Yes | `straight` or `curve` | A corner or a smooth bend at this point ([Direct routing](#direct-routing)). Not used by a right-angle connector, which SHOULD write `straight` |

The outer record, the inner record, each point and the label are lenient records: the app ignores keys
it does not know ([README.md, Unknown keys](README.md#unknown-keys)). The attachment record is strict.

**Informative.** A new connector made by the app has `stroke_hex` `#495057`, `line_width` 2,
`stroke_pattern` `solid`, `start_ending` `stop` and `end_ending` `arrow`. Note that `stop` draws a short
bar across the end of the line; a plain end is `none`.

## Attachments

An attachment binds one end of a connector to an element on the same page. It takes one of two forms:

- a **numbered point**: one of the element's connection points
  ([elements.md](elements.md#8-connection-points)), named by its number;
- a **place along an attach line**: a line the element offers, named by its number, and a distance along
  it.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `element_id` | UUID | Yes | The `id` of an element on the **same page** | The shape this end is attached to |
| `connection_point_id` | whole number | One of the two forms | The `id` of one of that element's `connection_points` | Which numbered point |
| `attach_line_id` | whole number | One of the two forms | The number of an attach line that element offers ([Attach lines](#attach-lines)) | Which attach line |
| `distance` | number, diagram points | With `attach_line_id`, and only then | 0 to 10,000,000 | How far along the attach line the end sits, measured from the line's start |

Rules:

- An attachment MUST have either `connection_point_id` alone, or `attach_line_id` and `distance`
  together. Any other combination (both forms, neither, or `distance` without `attach_line_id`) is
  refused.
- The attachment record is **strict**: any other key refuses the document. An optional key written as
  `null` counts as absent; a writer MUST leave it out instead and MUST NOT write `null`.
- The element MUST be on the same page as the connector, and MUST have the named connection point or
  offer the named attach line.
- Both ends MAY be attached, one, or neither. Both ends MAY be attached to the same element.

The two forms:

```json
{"element_id": "7E1A3C5F-9B2D-4E6A-8C0B-1D3F5A7C9E02", "connection_point_id": 1}
```

```json
{"element_id": "9C2E4A6B-1D3F-4B5C-8E7A-0F2B4D6C8E1A", "attach_line_id": 0, "distance": 64}
```

A callout's tail uses the same record, restricted to the numbered-point form on another shape
([elements.md](elements.md#128-callout)).

### Attach lines

An **attach line** is a straight line on a shape that a connector end can sit anywhere along, rather
than at one fixed point. Its two ends are defined relative to the shape's frame, so it moves, stretches
and turns with the shape. An end on it stores the line's number and a distance in diagram points from
the line's start, so lengthening the shape leaves the end where it was on the page instead of sliding it.

In document format 18 exactly one kind of shape offers an attach line:

| Shape | Line number | The line |
| --- | --- | --- |
| A UML lifeline (a `complex_shape` element of type `lifeline`; [elements.md](elements.md#129-lifeline)) | `0` | Its dashed line, from the bottom centre of its head down to the bottom centre of its frame, or, when the lifeline ends in a destroy mark, to the centre of that mark, 12 points higher |

No other element offers any attach line, so `attach_line_id` on an attachment to any other element is
refused. A line's number is permanent: a lifeline's dashed line is always `0`.

A `distance` beyond the line's present length is valid; the end is then drawn at the line's far end.

## Where an end is drawn

An **attached** end is drawn where its target currently is, wherever the shape has moved, turned,
flipped or been resized to:

- for a numbered point, at that connection point's position on the page
  ([elements.md](elements.md#8-connection-points) gives how a point's fractions, the frame, flips and
  rotation combine);
- for an attach line, at `distance` along the line, measured from its start, and at the line's end when
  `distance` is longer than the line.

The stored first or last point of an attached end is never used for drawing while the end is attached.
It is still required.

When the app detaches an end, or releases it because the shape or the connection point it was attached
to is deleted (unless the connector is deleted with it), the line does not move: the app first works
out where the end is currently drawn and writes that position into the stored points, together with every corner of a route drawn from the
shapes (a right-angle route, or a lifeline's call to itself). A program that edits documents SHOULD do
the same. Removing the attachment without updating the stored points would make the end jump to
wherever the stored point happens to be.

A writer SHOULD also store, as the first or last point, the page position the attachment currently
resolves to, so that a reader that does not resolve attachments still draws the line in the right
place.

A **free** end is drawn at its stored point.

**Informative.** A connector with both ends attached to the same attach line is drawn by the app as a
loop: out 30 points to the side of the line, along, and back, as UML draws a message to itself. The loop
is worked out from the line each time and never stored; the connector's stored points are still
required.

## Line endings

`start_ending` and `end_ending` take one of the line-ending values listed in
[elements.md](elements.md#139-line-endings), the same list a `path` element uses. Any other value refuses
the document. Note the historical names: `solidArrow` is drawn **hollow**, `openDiamond` is the outlined
diamond and `diamond` the filled one, and `stop` draws a bar across the end; a plain end is `none`.

## Direct routing

With `routing` absent (or `direct`), the line runs through every stored point in order:

- A `straight` point between two others is a **corner**: the line arrives in one direction and leaves in
  another.
- A run of consecutive `curve` points is drawn as one **smooth curve** through each of them. At each
  curved point the curve's direction is parallel to the line joining the point's two neighbours, so the
  bend is shared evenly between the segment arriving and the segment leaving. A `straight` point ends one
  smooth run and starts the next.
- The `kind` of the first and last points makes no difference.

So a straight two-point line is two `straight` points; an S-bend is four points with the middle two
`curve`; and a line drawn with right angles by hand is simply points that happen to form right angles.

## Right-angle routing

With `routing` set to `right_angle`, the line travels only along two perpendicular directions. **What the
stored points mean depends on how many there are.** There is deliberately no other flag, so the two
cannot disagree:

| Stored points | Meaning |
| --- | --- |
| Exactly 2 | **Automatic.** The two ends only. The corners are worked out from the shapes every time the connector is drawn, and are never stored |
| More than 2 | **Hand-adjusted.** A person shaped the route. The points are the route's corners, ends included, in order, in page coordinates |

### What a writer stores

- For an automatic route, the two end positions, with their attachments.
- For a hand-adjusted route, every corner in order. Each segment MUST be exactly horizontal or exactly
  vertical in the connector's own axes ([Routing orientation](#routing-orientation)), to within 0.01
  diagram points. The app does not refuse a document that breaks this; it draws the automatic route
  instead and keeps the stored points.
- Point `kind` values are not used; write `straight`.

### How the app draws an automatic route

**Informative.** This describes the app's router so that another reader can draw something similar. It
does not affect validity.

- An attached end leaves its shape at right angles to the side of the frame that its connection point
  is nearest to, turned with the shape's rotation and rounded to the nearest of the four directions. A
  point equally near two sides (a corner) or all four (the centre) leaves by whichever side faces the
  other end. A connector attached along a lifeline's line leaves at right angles to the line, on the side
  facing its other end. A free end has no required direction.
- The route goes around the two shapes it joins, keeping 20 points clear of them where there is room
  (then 8, then 1, when shapes are tightly packed).
- With `routes_around_shapes` true, it also goes around up to 16 other shapes near its path, nearest
  first. It never avoids `path` elements (lines and freehand strokes), nor a shape that contains one of
  its ends, such as a background panel.
- Among routes that work, it prefers the one with the fewest corners unless that route is much longer,
  and turns halfway across a gap.
- If no clear route exists, it draws the plain route that ignores other shapes. A connector always draws
  something.

### How the app uses a hand-adjusted route

**Informative.** The stored corners are kept. Only the first and last corners move, each along the one
axis that keeps its end segment square, so the route stretches to follow its shapes as they move. If the
stored route no longer fits (a segment is no longer horizontal or vertical, or an end segment would have
to leave its shape backwards), the automatic route is drawn instead. The stored points are not changed,
and the hand-made route returns if the shapes move back.

## Routing orientation

`routing_rotation_degrees` turns a right-angle connector's two directions of travel. At 30, the line runs
along directions at 30 and 120 degrees, clockwise, and its corners are still right angles. The stored
points stay in page coordinates; only the router works in the turned axes.

It exists so that rotating a whole group turns the connectors inside it with their shapes, instead of
their routes being worked out afresh square to the page. The angle belongs to the connector, not to the
group, so ungrouping keeps it.

Rules: finite; at least 0 and less than 360; 0 (or absent) unless `routing` is `right_angle`. A new
connector made by the app starts at 0.

## Labels

A connector MAY carry one label, drawn upright (never turned with the line) at a point along it.

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `content` | text content | Yes | As an element's text content ([elements.md](elements.md#6-text-content)) | The label's text and formatting |
| `position` | number | Yes | 0 to 1 | How far along the line, as a fraction of its length: 0 is the start, 1 the end, 0.5 the middle |
| `normal_offset` | number, diagram points | Yes | Within ±1,000,000,000 | How far the label's centre is moved sideways from the line. Positive values move it to the right of the direction of travel from start to end: on a line drawn left to right, downwards |

`position` is measured along the line **as drawn**, including the corners of an automatic right-angle
route and the curves of a direct one, not along the stored points.

## Locking

`is_locked` records that a person locked this connector. **Informative.** The app does not let a person
move, restyle or delete a locked connector until it is unlocked. Only this direct lock is stored. A connector inside a locked group is
effectively locked too ([Locking and groups](#locking-and-groups)); that is worked out when needed and
never written.

## What the app refuses in a connection

| Fault | Message (English example) |
| --- | --- |
| Fewer than two points | "The diagram contains an invalid number in connection point count." |
| A point outside ±1,000,000,000 | "The diagram contains an invalid number in connection point x." (or "…connection point y.") |
| An attachment to a missing element, to an element on another page, to a connection point number the element does not have, to an attach line it does not offer, or with a `distance` out of range | "Connection *id* refers to a missing element." |
| A `stroke_hex` that is not a colour | "The diagram contains an invalid color value: *value*." |
| A `line_width` outside 0 to 10,000 | "The diagram contains an invalid number in connection line width." |
| A label `position` outside 0 to 1, or a `normal_offset` out of range | "The diagram contains an invalid number in connector label position." (or "…connector label offset.") |
| An unknown `routing`, `stroke_pattern`, ending, texture or point `kind`; a `routing_rotation_degrees` that breaks its rules; an attachment with an unknown key or with neither or both forms; a missing required key | "The SimpleDiagrams document contains an invalid document.json file." |
| A repeated connection `id` | "The diagram repeats identifier *id*." |
| More than 200,000 connections in the document | "The diagram contains too many connections (*n*)." |
| More than 1,000,000 path nodes and connector points together | "The diagram contains too many path points (*n*)." |

Faults in the label's text give the text-content messages of [elements.md](elements.md). Messages are
shown translated where a translation exists; programs MUST NOT match on them
([README.md, Messages](README.md#messages-quoted-in-this-specification)).

## Groups

A group is a list of **references** to things on its page. It does not contain copies of them. Groups
nest by referring to other groups. Every element, connection and group belongs directly to at most one
group.

### The group record

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique in the document's shared namespace | The group's identity |
| `members` | array of members | Yes | At least 2, none repeated | The group's direct members, in order |
| `is_locked` | boolean | Yes | | Whether a person locked this group |
| `rotation_degrees` | number, degrees | Yes | At least 0 and less than 360 | The angle of the group's selection frame. `0` is always valid |

A member:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `kind` | string | Yes | `element`, `connection` or `group` | What the member is |
| `id` | UUID | Yes | The `id` of an element, connection or group of that kind on the **same page** | Which one |

The group record and its members are lenient records: the app ignores keys it does not know.

### Membership rules

A writer MUST meet every rule in this table.

| Rule | If broken (English example) |
| --- | --- |
| At least two members, none listed twice | "Group *id* contains an invalid relationship." |
| Each member is an element, connection or group of the same page, of the stated `kind` | Same |
| A group is not a member of itself | Same |
| Each element, connection and group has at most one direct parent group | Same |
| No loops (group A in group B in group A) | Same |
| A container or swimlanes element is never a member of a group; a container holding it does that job ([elements.md](elements.md#1210-rules-across-the-page)) | "The shape *id* is not a valid complex shape." (SimpleDiagrams 5.1: "Shape *id* holds a container, swimlanes, table, callout or lifeline record that is not valid.") |
| Nesting at most 64 levels deep | "The diagram contains groups nested too deeply (*n*)." |
| `rotation_degrees` at least 0 and less than 360 | "The SimpleDiagrams document contains an invalid document.json file." |
| Group identifiers unique, and distinct from every other identifier in the shared namespace | "The diagram repeats identifier *id*." |
| At most 100,000 groups, and 200,000 direct members across all groups, in the document | "The diagram contains too many groups (*n*)." / "The diagram contains too many grouped objects (*n*)." |

A group MAY be a member of a container or a lane, but only as a whole and only if it is not itself inside
another group; then none of the shapes or groups inside it may also be a member of a container or lane on
its own ([elements.md](elements.md#1210-rules-across-the-page)).

### Geometry

A group stores no geometry of its members. Every member's position is stored on the member itself, in
page coordinates. A group's `rotation_degrees` never moves or turns what is drawn: it only keeps the
group's selection frame lined up with its contents after the group has been rotated.

### Order

The order of `members` is kept, but does not affect drawing order, which is decided by the page
([README.md, Drawing order](README.md#drawing-order)).

### Locking and groups

Only each object's own lock (`is_locked`) is stored. An object is **effectively locked** when it, or any
group above it, is locked. **Informative.** The app also treats a group as locked for whole-group
operations when anything inside it is locked, because moving the group would move the locked thing.
None of this derived state is written to the file, so unlocking a group never erases a lock a person set
on something inside it.

## Examples

A right-angle connector from point 1 (middle of the right edge) of one shape to point 0 (middle of the
left edge) of another, with an automatic route and a label above its middle. The shapes' frames are
`{x: 100, y: 100, width: 120, height: 60}` and `{x: 400, y: 220, width: 120, height: 60}`, so the
stored end points are where those connection points lie:

```json
{
  "is_locked": false,
  "routing": "right_angle",
  "routes_around_shapes": true,
  "routing_rotation_degrees": 0,
  "content": {
    "id": "0B6E3D2A-5C1F-4A8E-9D7B-2E4F6A8C0D13",
    "points": [
      {"x": 220, "y": 130, "kind": "straight"},
      {"x": 400, "y": 250, "kind": "straight"}
    ],
    "start_attachment": {"element_id": "7E1A3C5F-9B2D-4E6A-8C0B-1D3F5A7C9E02", "connection_point_id": 1},
    "end_attachment": {"element_id": "3D5F7A9B-1C2E-4F6A-8B0C-2D4E6F8A0B24", "connection_point_id": 0},
    "stroke_hex": "#495057",
    "line_width": 2,
    "stroke_pattern": "solid",
    "start_ending": "none",
    "end_ending": "arrow",
    "label": {
      "content": {"string": "sends", "base_style": {"font_family": "Helvetica", "font_size": 14, "weight": "regular", "is_italic": false, "is_underlined": false, "color_hex": "#212529"}, "character_runs": [], "base_alignment": "center", "paragraph_runs": []},
      "position": 0.5,
      "normal_offset": -12
    }
  }
}
```

The label's `content` is a text-content record; its exact keys are defined in
[elements.md](elements.md), which is the authority if this example and that page ever differ.

A group of those two shapes and the connector:

```json
{
  "id": "5A7C9E1B-3D5F-4A6C-8E0A-2B4D6F8A1C35",
  "is_locked": false,
  "rotation_degrees": 0,
  "members": [
    {"kind": "element", "id": "7E1A3C5F-9B2D-4E6A-8C0B-1D3F5A7C9E02"},
    {"kind": "element", "id": "3D5F7A9B-1C2E-4F6A-8B0C-2D4E6F8A0B24"},
    {"kind": "connection", "id": "0B6E3D2A-5C1F-4A8E-9D7B-2E4F6A8C0D13"}
  ]
}
```

## Open points

None.
