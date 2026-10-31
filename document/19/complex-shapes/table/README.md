# `table`, version 1

A **table** is rows and columns with text in each cell. First drawn by SimpleDiagrams 5.1.

Read [elements.md](../../elements.md#12-complex-shapes) first for the structure every complex shape
shares. This page gives what a table version 1 holds within it. Its schema is
[`1.schema.json`](1.schema.json). An entity table is a type of its own,
[`entity_table`](../entity_table/README.md).

## The record

| | |
| --- | --- |
| `type`, `version` | `table`, `1` |
| Settings | `header_row` (below) |
| Roles and counts | `column`, 1 to 50 parts, from the left; then `row`, 1 to 200 parts, from the top |
| `members` | Not allowed |
| Own text | None: the element's `text_content.string` is empty, because a table's text is in its cells |
| `own_text_layout` | Not allowed |
| Connection points | The element's own, listed as on any element ([elements.md](../../elements.md#8-connection-points)) |
| Flips | Not allowed |

## Settings

| Setting | Kind | Values | Default |
| --- | --- | --- | --- |
| `header_row` | boolean | `true`: the first row is drawn as a header, its text bold on a band of the fill colour. It changes only how the row is drawn, never the stored text | `true` |

## Parts

All the columns come first, then all the rows.

| Role | Keys, all required | Rules |
| --- | --- | --- |
| `column` | `role`, `size` | `size` 0.000001 to 1,000,000: the column's width as a share of the sum of all the columns' sizes |
| `row` | `role`, `size`, `cells` | `size` as a column's, for the row's height. `cells`: the row's texts, one for each column, from the left |

Each cell is a complete text record ([elements.md](../../elements.md#6-text-content)) of at most 2,000
characters and 200 runs. All the cells of one table together hold at most 100,000 characters and 10,000
runs ([elements.md](../../elements.md#1211-how-much-a-shape-and-a-page-hold)).

## Rules

- Every row has exactly one cell for each column, and the table has at most 2,000 cells.
- A column's width is the frame width × its share ÷ the sum of the columns' sizes, and a row's height
  likewise from the frame height. With the frame as stored, no column is narrower than 16 points and no
  row shorter than 12 points (with a relative allowance of 10⁻⁹).

The schema cannot check either rule.

## Connection points

The table's points are the element's own, numbered and placed as on any element. A new table made by
the app has the standard eight.

## How the app draws it

**Informative.** The frame in the shape's fill and line, with a line between each pair of rows and
columns, and each cell's text in its cell. A new table made by the app has three rows and three
columns, all shares 1, and empty cells.

## An example

A table of two rows and two columns, the first column twice as wide as the second. The cells are
shortened here; each is a complete text record, and the second row's are empty.

```json
{
  "type": "table",
  "version": 1,
  "settings": {"header_row": true},
  "parts": [
    {"role": "column", "size": 2},
    {"role": "column", "size": 1},
    {"role": "row", "size": 1, "cells": ["… Item …", "… Qty …"]},
    {"role": "row", "size": 1, "cells": ["… empty …", "… empty …"]}
  ]
}
```

The [`table`](../../examples/table.simplediagram/) example holds a table of three rows and two columns.
