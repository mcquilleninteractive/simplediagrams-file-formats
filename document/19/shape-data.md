# Shape data

**Shape data** is typed information attached to a shape, such as a part number, a capacity or a status.
Each distinct set of field definitions is stored once, in the document's `shape_data_definitions` table.
Each element names the definition it uses and stores one value for every field of it.

Conventions and limits are in [README.md](README.md); the element record that carries the values is in
[elements.md](elements.md).

On this page:

- [Where shape data is stored](#where-shape-data-is-stored)
- [How shape-data records are read](#how-shape-data-records-are-read)
- [A definition](#a-definition)
- [A field](#a-field)
- [Choice options](#choice-options)
- [Number bounds](#number-bounds)
- [A value](#a-value)
- [An element's values](#an-elements-values)
- [The link to a library shape](#the-link-to-a-library-shape)
- [Budgets](#budgets)
- [Example](#example)
- [What the app refuses](#what-the-app-refuses)
- [Open points](#open-points)

## Where shape data is stored

| Key | Where | Type | Required | Meaning |
| --- | --- | --- | --- | --- |
| `shape_data_definitions` | Top level of `document.json` | array of definitions | Yes; `[]` when no element has shape data | The shared table of definitions ([A definition](#a-definition)) |
| `shape_data_definition_id` | Every element's outer record | UUID or `null` | Yes: MUST be present, and MAY be `null` | The definition this element uses, or `null` for none |
| `shape_data_values` | Every element's outer record | array of instance values | Yes: MUST be present and MUST NOT be `null`; `[]` when there is no definition | This element's value for each field ([An element's values](#an-elements-values)) |

A document may hold 1,000 copies of one library shape. Storing each definition once, rather than on
every copy, keeps documents small; keeping the definitions in the document, rather than relying on an
installed library, keeps the document complete on a Mac that lacks the library.

## How shape-data records are read

Shape-data records differ from the rest of `document.json` in two ways:

- **Their keys are camel case** (`defaultValue`, `allowsInstanceEditing`, `choiceOptions`,
  `allowsDefinitionEditing`, `fieldID`, `isOverride`, `libraryID`, `itemID`, `assetVersion`). They
  MUST be written exactly as given.
- **Every shape-data record is strict**: an unknown key refuses the document. This covers definitions,
  fields, choice options, bounds, values, instance values and `source`.

`null` is not a general stand-in for "absent" here:

- `source`, `minimum` and `maximum` MUST be left out when they have no value. Written as `null`, they
  refuse the document.
- An `empty` value MUST NOT have a `value` key at all, not even `null`.
- `choices` and `choiceOptions` MUST NOT be `null`.

## A definition

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique within `shape_data_definitions` | Elements refer to the definition by this |
| `fields` | array of fields | Yes | 0 to 128 | The fields, in display order |
| `source` | object | No; leave it out rather than writing `null` | See [The link to a library shape](#the-link-to-a-library-shape) | The library shape these fields came from |
| `allowsDefinitionEditing` | boolean | Yes | | Whether the field definitions may be edited when a person copies this shape into a library of their own |

Rules for the table:

- Every definition MUST be used by at least one element. An unused definition refuses the document:
  the app does not keep invisible data.
- Every non-null `shape_data_definition_id` MUST name a definition in the table.
- An `id` MUST NOT appear twice in the table.
- Definition identifiers are a namespace of their own: they are not compared with the document's shared
  identifiers ([README.md, Identity rules](README.md#identity-rules)), so one MAY equal an element's
  identifier, for example.
- At most 100,000 definitions, and 100,000 fields across all of them.

**Informative.** Two definitions with identical content but different identifiers are allowed; the app
treats them as one when it reads them.

## A field

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique within its definition | The field's identity. Values refer to it |
| `name` | string | Yes | 1 to 120 scalars; not blank once invisible characters are ignored; no control characters; no line separator (U+2028) or paragraph separator (U+2029) | The field's name |
| `type` | string | Yes | `text`, `number`, `boolean`, `date` or `choice` | What values the field holds |
| `defaultValue` | value | Yes | A value that suits `type`, or `empty` ([A value](#a-value)) | The value a newly placed shape starts with |
| `allowsInstanceEditing` | boolean | Yes | | Whether a person may edit this field's value on a placed shape |
| `unit` | string | Yes | At most 64 scalars; no control characters, U+2028 or U+2029; MUST be `""` unless `type` is `number` | A unit shown beside the number, such as `L/min` |
| `choiceOptions` | array of options | Exactly one of `choiceOptions` and `choices` | `choice` fields: 1 to 128 options. Other types: empty | The choices, each with a lasting identity ([Choice options](#choice-options)) |
| `choices` | array of strings | Exactly one of `choiceOptions` and `choices` | As above, labels only | The older form of the choices |
| `minimum` | bound | No; leave it out rather than writing `null` | `number` fields only | The smallest allowed number ([Number bounds](#number-bounds)) |
| `maximum` | bound | No; leave it out rather than writing `null` | `number` fields only | The largest allowed number |

Rules:

- A field MUST have exactly one of `choiceOptions` and `choices`. Neither, or both, refuses the document.
  A writer SHOULD write `choiceOptions` for a `choice` field and `"choices": []` for any other field, as
  the app does.
- Field **names** MAY repeat within a definition; only field identifiers must differ.
- "Invisible characters" are Unicode default-ignorable code points, such as zero-width spaces and
  joiners. A name made only of them, and white space, counts as blank.

**Informative.** Fields of a library published on the SimpleDiagrams website have identifiers derived
from the library's name and the field's key ([libraries.md](libraries.md#name-based-uuids)), so the
same field has the same identifier in every definition derived from it.

## Choice options

An option:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `id` | UUID | Yes | Unique within the field | The option's lasting identity. Values store this |
| `label` | string | Yes | 1 to 256 scalars; not blank once invisible characters are ignored; no control characters, U+2028 or U+2029 | The text shown |

Labels within one field MUST differ from each other once letter case, invisible characters and
look-alike forms are ignored. Precisely, two labels are the same when these steps give equal results:
remove Unicode default-ignorable code points; fold letter case without regard to locale; apply Unicode
compatibility normalization (NFKC); trim surrounding white space and line breaks. So `Open`, `OPEN`,
` open ` and the full-width `Ｏｐｅｎ` are all the same label.

Because values store an option's `id`, a label MAY be renamed without changing what saved values mean.

### The older `choices` form

`choices` gives the labels only. The app turns each label into a fixed option identifier derived from
the label's text, so documents from before option identifiers existed keep working. A writer SHOULD use
`choiceOptions` instead; this derivation is published so that a reader can interpret older documents and
so that a writer can match their identifiers.

The identifier of a label is computed as follows:

1. Normalize the label to Unicode normalization form C (NFC).
2. Form the bytes: the UTF-8 encoding of `com.simplediagrams.shape-data.legacy-choice.v1`, then one NUL
   byte (0x00), then the UTF-8 encoding of the normalized label.
3. Take the SHA-256 digest of those bytes and keep its first 16 bytes, numbered 0 to 15.
4. Set byte 6 to `(byte6 & 0x0F) | 0x80` and byte 8 to `(byte8 & 0x3F) | 0x80`.
5. Read the 16 bytes, in order, as a UUID. (Step 4 marks it as a version 8 UUID, RFC 9562.)

Test vectors:

| Label | Option identifier |
| --- | --- |
| `High` | `4A7DEDE9-0133-8ED0-8937-91D5AA28FF05` |
| `Low` | `7EFFFE61-7AFE-82DD-9F2A-A09C0C61BCCB` |
| `Café` (written with U+00E9) | `279F79DA-9B9D-8B01-9A36-7A3F39646765` |
| `Café` (written with `e` and U+0301) | `279F79DA-9B9D-8B01-9A36-7A3F39646765` |

## Number bounds

A bound is:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `value` | number | Yes | Finite | The limit |
| `inclusive` | boolean | Yes | | Whether the limit itself is allowed |

Bounds are allowed only on `number` fields. With both present, `minimum.value` MUST be below
`maximum.value`, or equal to it with both bounds inclusive. Either way, at least one number must be
allowed.

## A value

A value is an object with a `type` and, except for `empty`, a `value`.

| `type` | `value` | Fits a field of type | Rules |
| --- | --- | --- | --- |
| `empty` | MUST be absent | any | "No value", which differs from zero, false and empty text |
| `text` | string | `text` | At most 4,096 scalars. Tab, line feed, carriage return, U+2028 and U+2029 are allowed; other control characters are refused |
| `number` | number | `number` | Finite, and within the field's bounds |
| `boolean` | `true` or `false` | `boolean` | |
| `date` | string | `date` | Exactly `YYYY-MM-DD`: ten ASCII characters, digits and two hyphens, naming a real day of the Gregorian calendar, with a year from `0001` |
| `choiceID` | UUID | `choice` | The `id` of one of the field's options. This is what the app writes |
| `choice` | string | `choice` | The older form: a label, at most 256 scalars ([below](#the-older-choice-value)) |

A value's type MUST suit its field's type, as the table shows; `empty` suits every field.

**Informative.** The app limits text a person types to 500 characters, but reads stored text values up
to 4,096 scalars.

### The older `choice` value

`{"type": "choice", "value": "High"}` is read as the option identifier derived from the label `High`
([The older choices form](#the-older-choices-form)). It therefore matches only an option whose
identifier was derived that way, which is every option of a field declared with `choices`. For a field
declared with `choiceOptions` and other identifiers, it is refused even when `High` is one of the
field's labels. A writer SHOULD write `choiceID`.

## An element's values

`shape_data_values` holds one record per field of the element's definition:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `fieldID` | UUID | Yes | The `id` of a field of the element's definition | Which field |
| `value` | value | Yes | Suits the field ([A value](#a-value)) | This shape's current value |
| `isOverride` | boolean | Yes | | `true` when a person deliberately entered this value |

Rules:

- There MUST be exactly one value for every field of the definition: none missing, none extra, none
  repeated. Values MAY be in any order.
- An element whose `shape_data_definition_id` is `null` MUST have `"shape_data_values": []`.
- At most 128 values on one element, and 100,000 across the document.

**What `isOverride` means.** It records that a person entered the value, not that the value differs
from the default: entering 20 when the default is already 20 still marks it. **Informative.** When a
library publishes new defaults, the app's ordinary update replaces only values whose `isOverride` is
`false`. The value is stored even when it equals the default, because an earlier update may have left
it different from the current default on purpose.

A writer creating a new document SHOULD write `isOverride: false` with `value` equal to the field's
default, and `true` where it sets a value on purpose.

## The link to a library shape

A definition's `source` names the library shape its fields came from:

| Key | Type | Required | Rules / bounds | Meaning |
| --- | --- | --- | --- | --- |
| `libraryID` | string | Yes | 1 to 256 bytes; no NUL | The library's identifier |
| `itemID` | string | Yes | 1 to 256 bytes; no NUL | The shape's identifier within the library |
| `assetVersion` | whole number | Yes | Above 0 | The version of the artwork the shape was placed with |

These are the same three values as an element's `symbol_reference`, under camel-case names
([libraries.md](libraries.md#library-shape-references)).

`source` is what lets the app update placed shapes from their library later. It is matched to a library
shape ignoring letter case, and ignoring `assetVersion`. It never decides what is drawn.

**Informative.** When the app places a library shape, it always gives the shape a definition with a
`source`, even when the shape has no fields; so a placed shape usually has a definition with
`"fields": []`. For a built-in shape, `source` equals the element's `symbol_reference`. For a shape from a
downloaded or personal library, it holds that library's UUID and the shape's UUID, both in lower case,
with the shape's asset version; for an `editable_vector` element this is the only record of which
library it came from. A writer that does not need library updates MAY give an element no definition at
all (`shape_data_definition_id` `null`).

## Budgets

Shared definitions count once; values count on every element.

| Budget | Limit |
| --- | --- |
| Fields in one definition | 128 |
| Values on one element | 128 |
| Fields across all definitions | 100,000 |
| Values across the document | 100,000 |
| UTF-8 bytes of shape-data text across the document: field names, units, choice labels and default text and date values, counted once per distinct definition, plus every element's text and date values. (The app's second check, after reading, also counts 36 bytes for each `choiceID` value.) | 16 MiB |

## Example

A definition with five fields, and the values of one element that uses it. The definition came from a
shape of a personal library, so its `source` holds lower-case UUIDs.

```json
{
  "id": "8E0A2C4E-6B8D-4F1A-9C3E-5A7B9D1F3E80",
  "fields": [
    {"id": "1F3B5D7F-9A1C-4E3A-8B5D-7F9A1C3E5B01", "name": "Part number", "type": "text", "defaultValue": {"type": "empty"}, "allowsInstanceEditing": true, "unit": "", "choices": []},
    {"id": "2A4C6E8A-0B2D-4F4B-9C6E-8A0B2D4F6C02", "name": "Flow", "type": "number", "defaultValue": {"type": "number", "value": 20}, "allowsInstanceEditing": true, "unit": "L/min", "choices": [], "minimum": {"value": 0, "inclusive": true}, "maximum": {"value": 500, "inclusive": false}},
    {"id": "3B5D7F9B-1C3E-4A5C-8D7F-9B1C3E5A7D03", "name": "Status", "type": "choice", "defaultValue": {"type": "choiceID", "value": "6E8A0C2E-4F6B-4D8E-9A0C-2E4F6B8D0A11"}, "allowsInstanceEditing": true, "unit": "", "choiceOptions": [{"id": "6E8A0C2E-4F6B-4D8E-9A0C-2E4F6B8D0A11", "label": "Planned"}, {"id": "7F9B1D3F-5A7C-4E9F-8B1D-3F5A7C9E1B12", "label": "Installed"}]},
    {"id": "4C6E8A0C-2D4F-4B6D-9E8A-0C2D4F6B8E04", "name": "Installed on", "type": "date", "defaultValue": {"type": "empty"}, "allowsInstanceEditing": true, "unit": "", "choices": []},
    {"id": "5D7F9B1D-3E5A-4C7E-8F9B-1D3E5A7C9F05", "name": "Inspected", "type": "boolean", "defaultValue": {"type": "boolean", "value": false}, "allowsInstanceEditing": true, "unit": "", "choices": []}
  ],
  "source": {"libraryID": "0f2d4b6a-8c1e-4a3d-9b5f-7e1c3a5d7b90", "itemID": "a1c3e5b7-d9f1-4b3d-8e5a-7c9e1b3d5f20", "assetVersion": 1},
  "allowsDefinitionEditing": true
}
```

The element's `shape_data_definition_id` is `"8E0A2C4E-6B8D-4F1A-9C3E-5A7B9D1F3E80"`, and its
`shape_data_values` are:

```json
[
  {"fieldID": "1F3B5D7F-9A1C-4E3A-8B5D-7F9A1C3E5B01", "value": {"type": "text", "value": "PX-200"}, "isOverride": true},
  {"fieldID": "2A4C6E8A-0B2D-4F4B-9C6E-8A0B2D4F6C02", "value": {"type": "number", "value": 20}, "isOverride": false},
  {"fieldID": "3B5D7F9B-1C3E-4A5C-8D7F-9B1C3E5A7D03", "value": {"type": "choiceID", "value": "7F9B1D3F-5A7C-4E9F-8B1D-3F5A7C9E1B12"}, "isOverride": true},
  {"fieldID": "4C6E8A0C-2D4F-4B6D-9E8A-0C2D4F6B8E04", "value": {"type": "date", "value": "2026-03-14"}, "isOverride": true},
  {"fieldID": "5D7F9B1D-3E5A-4C7E-8F9B-1D3E5A7C9F05", "value": {"type": "boolean", "value": false}, "isOverride": false}
]
```

## What the app refuses

**Informative.** Most shape-data faults are found while `document.json` is read, so they give the
general message "The SimpleDiagrams document contains an invalid document.json file.": an unknown key,
`null` where it is not allowed, a missing key, a value that does not suit its field, a number outside its
bounds, an unknown option, an invalid date, a definition no element uses, a reference to a definition
that is not in the table, values that do not cover every field exactly once, and going over a budget.
The app checks the budgets again afterwards, and on every edit, where a breach reads "This document
contains too much shape data. Use fewer fields or shorter values."

## Open points

None.
