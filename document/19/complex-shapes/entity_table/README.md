# `entity_table`, version 1

An **entity table** describes one entity of a database: its name in a header row, then one row for
each field, holding the field's name in the first column and its type in the second. Each field has two
connection points of its own, so a relationship can be drawn to the field itself. First drawn by
SimpleDiagrams 5.2.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what an entity table version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json).

## The record

| | |
| --- | --- |
| `type`, `version` | `entity_table`, `1` |
| Settings | None: `settings` is `{}` |
| Roles and counts | `column`, exactly 2; then `header`, exactly 1; then `field`, 1 to 60 |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty |
| `own_text_layout` | Not allowed |
| Connection points | Placed by the shape: the element lists none (below) |
| Flips | Not allowed |

## Parts

| Role | Keys, all required | Rules |
| --- | --- | --- |
| `column` | `role`, `size` | Two columns, the names and then the types. `size` 0.000001 to 1,000,000: the column's width as a share of both |
| `header` | `id`, `role`, `size`, `cells` | `cells`: exactly two, the entity's name and then an empty text, so the name runs across both columns. `size` as below |
| `field` | `id`, `role`, `size`, `cells`, `marks`, `point_ids` | `cells`: exactly two, the field's name and its type. `marks`: 0 to 8 marks, none repeated. `point_ids`: exactly two, the points at the field's left and right ends, in that order |

`size`, on the header and each field, is the row's height as a share of the sum of the header's and
the fields' sizes. The header's and the fields' `id`s are unique among the shape's parts. Each cell is a
complete text record of at most 2,000 characters and 200 runs, and all the cells together hold at most
100,000 characters and 10,000 runs ([elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold)).

**Marks.** A mark says what a field is. The list is closed in version 1:

| Mark | Meaning |
| --- | --- |
| `primary_key` | The field is the entity's primary key |
| `foreign_key` | The field refers to another entity |
| `unique` | No two rows hold the same value |
| `indexed` | The field is indexed |
| `nullable` | The field may be empty |

## Rules

With the frame as stored, no column is narrower than 16 points and no row shorter than 12 points (with a
relative allowance of 10⁻⁹). The schema cannot check this rule.

## Connection points

An entity table places its own points: its element lists none, `"connection_points": []`, and the
reader works them out from the shape as it reads the document.

- Points 0 to 7 are the standard eight, at their places on the frame
  ([elements.md](../../elements.md#8-connection-points)): for lines to the whole entity.
- Each field's two points, the numbers in its `point_ids`, sit at `x` 0 and `x` 1, at the middle of the
  field's row: for a relationship drawn to that field. They move with the field whenever rows change, so
  a connector follows its field.

## How the app draws it

**Informative.** The header row spans both columns. The drawn marks of a field are small icons stacked
in one narrow column at the start of the name cell, always in the order of the table above, whatever
their saved order; that column is present whenever any field has a mark, so the names line up. A new
field takes the two smallest unused point numbers from 8 up. A new entity table made by the app is 220
by 96 points, with a header and the fields `id` (type `uuid`, marked `primary_key`) and `name` (type
`text`).

## An example

An entity table of two fields, the first a primary key. The cells are shortened here; each is a complete
text record.

```json
{
  "type": "entity_table",
  "version": 1,
  "settings": {},
  "parts": [
    {"role": "column", "size": 2},
    {"role": "column", "size": 1},
    {"id": "0B6E5C1A-2D3F-4A5B-8C6D-7E8F9A0B1C2D", "role": "header", "size": 1,
     "cells": ["… Customer …", "… empty …"]},
    {"id": "1C7F6D2B-3E4A-4B6C-9D7E-8F9A0B1C2D3E", "role": "field", "size": 1,
     "cells": ["… id …", "… uuid …"], "marks": ["primary_key"], "point_ids": [8, 9]},
    {"id": "2D80EE3C-4F5B-4C7D-8E8F-9A0B1C2D3E4F", "role": "field", "size": 1,
     "cells": ["… email …", "… text …"], "marks": [], "point_ids": [10, 11]}
  ]
}
```

The [`entity-tables`](../../examples/entity-tables.simplediagram/) example holds two entity tables with
marked fields, and a relationship drawn from one field's point to another's.
