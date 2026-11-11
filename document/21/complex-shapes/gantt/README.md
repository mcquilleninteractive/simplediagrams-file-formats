# `gantt`

A **Gantt chart** is a time chart of **tasks** in **sections**. Each task is a bar on its own row from
the day it starts to the day it ends, or a diamond if it is a milestone. The sections group the rows
from top to bottom, their names in a column on the left, and a date axis runs along the bottom. A task
starts on a day, or when another task ends, so moving or lengthening one task moves every task that
follows it. First drawn by SimpleDiagrams 5.4; new in document format 21.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a Gantt chart holds within it in document format 21. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `gantt`, `1` |
| Settings | `skip_weekends` (below) |
| Roles and counts | `section`, 1 to 20 parts; then `task`, 1 to 60 parts, from the top row down |
| `members` | Not allowed |
| Own text | The chart's title: the element's `text_content`, which may be empty, at most 500 characters |
| `own_text_layout` | Allowed: where a person placed the title |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips; `preserves_aspect_ratio` | Not allowed; never `true` |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `skip_weekends` | boolean | `true`: Saturdays and Sundays are not counted in a task's length ([When each task happens](#when-each-task-happens)) | `false` |

## Parts

The sections come first, then the tasks, each in the order of the chart's rows from the top. A
section's `size` says how many of the tasks that follow, in order, are its own, so the first section
holds the first tasks, the second the next, and so on.

A **section** is a part with role `section`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The section's identity |
| `role` | Yes | `section` | |
| `text` | Yes | At most 500 characters | The section's name, which may be empty |
| `size` | Yes | A whole number from 1 to 60 | How many of the tasks are this section's |
| `text_layout` | No | A placed text layout ([elements.md](../../elements.md#123-parts)) | Where a person placed the name |
| `style` | No | Part paint ([elements.md](../../elements.md#123-parts)) | The paint of the section's tasks |

A **task** is a part with role `task`:

| Key | Required | Rules | Meaning |
| --- | --- | --- | --- |
| `id` | Yes | Unique among the shape's parts | The task's identity |
| `role` | Yes | `task` | |
| `text` | Yes | At most 500 characters | The task's name |
| `point_ids` | Yes | Exactly two | Its points: the middle of the start of its bar, then of its end ([Connection points](#connection-points)) |
| `start` | No | A day ([elements.md](../../elements.md#123-parts)) from 1900-01-01 to 2199-12-31 | The day it starts |
| `after` | No | Exactly one entry: the `id` of another task of this chart | It starts when that task ends |
| `size` | No | A whole number from 1 to 3,650 | Its length in days |
| `marks` | No | Any of `milestone`, `done` and `crit` | `milestone`: a single day, drawn as a diamond. `done`: finished, drawn paler. `crit`: on the critical path, its outline drawn heavier |
| `text_layout` | No | A placed text layout | Where a person placed the name |
| `style` | No | Part paint | Its own paint. Absent: its section's, then the shape's |

Sections and tasks have no other keys.

## When each task happens

A task starts on its `start`; or, with `after`, when that task ends; or, with neither, when the task
above it in the list ends, in whichever section that task is. Its end is its start plus its `size` in
days, and a day's end is the next day's start: a 5-day task starting on Monday 2026-11-09 ends where
Saturday 2026-11-14 begins, and the task after it starts on 2026-11-14. With `skip_weekends`, Saturdays
and Sundays are not counted: the end moves later past them, and a start worked out from another task
that lands on a weekend moves to the Monday, while a `start` the record holds is kept as written. A
milestone is the single day it starts on, and the task after it starts that same day. Days are calendar
days with no time and no time zone, so a chart gives the same days wherever it is read.

## Rules

- The sections' sizes add up to the number of tasks.
- A task holds `start` or `after`, never both, and the first task holds one of them.
- A task's `after` names another task of this chart, never a section.
- Following `after` from task to task never comes back to where it began.
- A task holds `size` exactly when it is not marked `milestone`.
- Every task's start and end, worked out as above, lie within 1900-01-01 to 2199-12-31.

A document breaking any of them is refused: "The shape … is not a valid complex shape." Nothing is cut
to fit. The schema checks the counts, the sizes, the marks, one entry in a task's `after` and the
years of a `start`; it cannot check these rules. The Gantt chart came with document format 21, so a
document claiming format 20 or lower that holds one is refused the same way.

## Connection points

A Gantt chart places its own points: its element lists none, `"connection_points": []`, and the reader
works them out from the shape as it reads the document. Points 0 to 7 are the frame's: 0 and 1 at the
middles of its left and right sides, 2 and 3 at the middles of its top and bottom, and 4, 5, 6 and 7 at
its top left, bottom left, top right and bottom right corners. Each task owns two, the numbers in its
`point_ids`: the first at the middle of the start of its bar, the second at the middle of its end; a
milestone's at the left and right of its diamond. Sections own none. The frame's eight and two for
each of 60 tasks make 128, the most an element may have
([elements.md](../../elements.md#8-connection-points)), which is why a chart holds at most 60 tasks.

## How the app draws it

**Informative.** The title runs in a strip across the top and the axis along the bottom; between them
every task has a row of the same height, and the sections' names stand in a column on the left, a thin
line between one section and the next. The strip and the column are there whether or not they hold
words, so typing a title never moves the chart. The axis spans the first task's start to the last
task's end, with ticks and labels at the finest of days, weeks, months, quarters or years whose labels
do not crowd. Bars are drawn in the task's paint: its own, else its section's, else the shape's. The
smallest frame the app draws a chart for is 200 points wide and as tall as its rows need; a smaller
frame shows the smallest chart shrunk to fit. A new chart from the app's tile is 480 by 220 points: two
sections, Plan and Build, of four tasks and a milestone.

## An example

The tile's chart, its names and texts shortened. The element's `text_content` holds the title,
"Project plan".

```json
{
  "type": "gantt",
  "version": 1,
  "settings": {"skip_weekends": false},
  "parts": [
    {"id": "…", "role": "section", "text": "… Plan …", "size": 2, "style": {"fill_hex": "#4E97D2"}},
    {"id": "…", "role": "section", "text": "… Build …", "size": 3, "style": {"fill_hex": "#ED692F"}},
    {"id": "…", "role": "task", "text": "… Research …", "start": "2026-11-09", "size": 5, "point_ids": [8, 9]},
    {"id": "B…", "role": "task", "text": "… Sketches …", "size": 5, "marks": ["done"], "point_ids": [10, 11]},
    {"id": "…", "role": "task", "text": "… Prototype …", "after": ["B…"], "size": 10, "marks": ["crit"], "point_ids": [12, 13]},
    {"id": "…", "role": "task", "text": "… Testing …", "size": 5, "point_ids": [14, 15]},
    {"id": "…", "role": "task", "text": "… Launch …", "marks": ["milestone"], "point_ids": [16, 17]}
  ]
}
```

The [`gantt-charts`](../../examples/gantt-charts.simplediagram/) example holds this chart and a second,
counting weekdays only and drawn with exact lines, whose one section has no name.
