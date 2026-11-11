# The complex-shape catalogue

Every complex shape is saved in one structure, the same for every type
([elements.md](../elements.md#12-complex-shapes)): a type, its version, its settings, its parts and,
for a shape that holds shapes on its whole body, its members. This catalogue says what each **type** holds
within that structure in document format 21: which settings, of which kinds and values; which roles its parts
have, in what order and how many; which keys a part of each role holds; and the rules its values keep
together. Each entry also says, informatively, how the app draws the shape, which the file never
records.

Only SimpleDiagrams defines complex shapes, so every type is a short word with no namespace. A writer
MUST write only the types listed here, each with the `version` its entry gives and exactly what
its entry declares.

## The entries

Document format 21 has these entries. Each is written with `version` 1. Entries added within format 21 are listed
here as they are published.

| `type` | Version | The shape | First drawn by | Page and schema |
| --- | --- | --- | --- | --- |
| `container` | 1 | A frame with a title that holds other shapes | SimpleDiagrams 5.1 | [page](container/README.md), [`1.schema.json`](container/1.schema.json) |
| `swimlanes` | 1 | One shape divided into parallel lanes, each holding shapes | SimpleDiagrams 5.1 | [page](swimlanes/README.md), [`1.schema.json`](swimlanes/1.schema.json) |
| `table` | 1 | Rows and columns of text | SimpleDiagrams 5.1 | [page](table/README.md), [`1.schema.json`](table/1.schema.json) |
| `entity_table` | 1 | One entity of a database: its name, then a row for each field | SimpleDiagrams 5.2 | [page](entity_table/README.md), [`1.schema.json`](entity_table/1.schema.json) |
| `callout` | 1 | A speech bubble with an optional tail | SimpleDiagrams 5.1 | [page](callout/README.md), [`1.schema.json`](callout/1.schema.json) |
| `lifeline` | 1 | A participant in a UML sequence diagram | SimpleDiagrams 5.1 | [page](lifeline/README.md), [`1.schema.json`](lifeline/1.schema.json) |
| `ring` | 1 | A ring cut into sections | SimpleDiagrams 5.2 | [page](ring/README.md), [`1.schema.json`](ring/1.schema.json) |
| `flow` | 1 | A row of steps, such as chevrons | SimpleDiagrams 5.2 | [page](flow/README.md), [`1.schema.json`](flow/1.schema.json) |
| `pyramid` | 1 | A pyramid or funnel cut into layers | SimpleDiagrams 5.2 | [page](pyramid/README.md), [`1.schema.json`](pyramid/1.schema.json) |
| `cycle` | 1 | Steps round a circle | SimpleDiagrams 5.2 | [page](cycle/README.md), [`1.schema.json`](cycle/1.schema.json) |
| `gauge` | 1 | A half dial with bands and a needle | SimpleDiagrams 5.2 | [page](gauge/README.md), [`1.schema.json`](gauge/1.schema.json) |
| `audience` | 1 | An arch of seats in coloured groups. New in document format 20 | SimpleDiagrams 5.3 | [page](audience/README.md), [`1.schema.json`](audience/1.schema.json) |
| `gantt` | 1 | A time chart of tasks in sections. New in document format 21 | SimpleDiagrams 5.4 | [page](gantt/README.md), [`1.schema.json`](gantt/1.schema.json) |

Each entry's folder is named after its type and holds its page, `README.md`, and its schema,
`1.schema.json`, named after the `version` it is written with. A type drawn first by SimpleDiagrams 5.1 was saved in document format
18 with a record of its own; document formats 19 to 21 save it in the one structure, with the meaning the
entry gives. The `audience`, first drawn by SimpleDiagrams 5.3, is new in document format 20: a document
claiming an earlier format that holds one is refused. The `gantt`, first drawn by SimpleDiagrams 5.4, is
new in document format 21, and is refused the same way in a document claiming format 20 or lower.

## What an entry declares

Each entry gives, for its type:

- what the shape is, and how its parts make it up;
- its **settings**: each one's name, kind, range or words, and default. Every setting is required, and
  written even when it holds its default, except one the entry marks optional, whose absence has the
  meaning the entry states;
- its **roles**: the role of each part, in order, with how many parts of each a shape has;
- the **keys** a part of each role holds: which it must hold, which it may, and the limits on them. A
  key of the ten the entry does not list for a role is forbidden on it;
- whether the shape holds **members** on its whole body, has **text of its own**, lets a person place
  that text (`own_text_layout`), and **places its own connection points**, and where those points are;
- its **rules**: what its values must satisfy together;
- informatively, how the app draws it and how it resizes.

A record of a listed type MUST hold exactly what its entry declares and keep its rules; a
reader that knows the entry refuses one that does not, with "The shape … is not a valid complex
shape." ([elements.md](../elements.md#128-reading-a-complex-shape)).

## The schemas

Each entry has a JSON Schema, `<type>/1.schema.json`, such as `ring/1.schema.json`. An element of
`document.json` whose `complex_shape` names that type MUST pass it as well as
[`document.schema.json`](../schemas/document.schema.json). The core schema checks the structure every
complex shape shares; the entry's schema adds what the entry declares, as far as JSON Schema can. What
it cannot check, such as the order of the roles or a gauge's minimum below its maximum, is stated on the
entry's page.

## How the catalogue grows

Within document format 21, a later SimpleDiagrams release may **add an entry**: a new type, with its own
page and schema. Nothing else changes within the format:

- an entry, once published, stays as it is for the rest of document format 21;
- every list of words is **closed**: a word the entry does not list refuses the document;
- the structure every complex shape shares never changes.

What a type holds may change only with a new **document format version**: a new setting, role, key or
word for a type, or a new meaning for one it has, comes with the next format, whose catalogue
describes the type as it then is. The app still reads each type as every earlier format saved it. A
record's `version` is not raised for such a change; the document format version marks it, and
`version` is kept so that documents saved earlier read as they always have.

So a document that was valid stays valid, and means what it meant. A reader that does not know an
entry, because a later release added it, opens the document and shows that shape as a placeholder,
keeping its record exactly as it read it ([elements.md](../elements.md#128-reading-a-complex-shape)).
Each addition is recorded in the repository's CHANGELOG.
