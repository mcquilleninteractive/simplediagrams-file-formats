# Refused documents

Each folder here holds one document that the app refuses, and a `README.md` naming the one rule it
breaks. Every document is otherwise valid: with that one fault removed, it opens. The app refuses every
one of them. Another reader MAY be more lenient ([Conformance](../README.md#conformance)), but a
writer MUST NOT produce documents like these.

The messages are the app's English messages, quoted as examples. The app shows them in the Mac's
language where a translation exists. Programs MUST NOT match on their text (see
[Messages quoted in this specification](../README.md#messages-quoted-in-this-specification)).
Identifiers inside a message are the document's own.

"Schema" says whether the [JSON Schemas](../schemas/) can catch the fault on their own. "No" means a
program must check the rule itself.

## Before opening one

Every document needs an `assets/` folder, even an empty one, and Git does not keep empty folders. So
each document here that has no artwork is stored without `assets/`. Create an empty `assets/` folder
inside it first, or the app refuses most of them for the missing folder instead of for the rule they
demonstrate. Two are refused for their own fault either way, because the app checks for a newer format
version and for unexpected names in the package before it looks for `assets/`:
[newer-format-version](newer-format-version/README.md) and
[extra-file-in-package](extra-file-in-package/README.md). The documents about assets already have
their `assets/` folder.

## Cases

| Case | Area | Rule broken | Stated in | Schema | Message (English) |
| --- | --- | --- | --- | --- | --- |
| [extra-file-in-package](extra-file-in-package/README.md) | Package members | The package folder holds only `manifest.json`, `document.json`, `assets/` and, for a converted legacy document, `legacy/` | [README.md](../README.md#the-package-on-disk) | No | "The SimpleDiagrams document contains an invalid notes.txt file." |
| [unknown-format-name](unknown-format-name/README.md) | Manifest | `format` in `manifest.json` must be exactly `com.simplediagrams.document` | [README.md](../README.md#the-manifest) | Yes | "The document format com.example.diagram is not supported." |
| [legacy-source-claimed-but-missing](legacy-source-claimed-but-missing/README.md) | Manifest | `contains_legacy_source` must agree with the package: `true` requires `legacy/source.sdxml` | [README.md](../README.md#the-manifest) | No | "The SimpleDiagrams document is missing legacy/source.sdxml." |
| [schema-version-mismatch](schema-version-mismatch/README.md) | Versions | `schema_version` in `document.json` must be one less than `format_version` in `manifest.json`: 17 for document format 18 | [README.md](../README.md#versions) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [newer-format-version](newer-format-version/README.md) | Versions | A reader refuses a document format newer than its own, after reading only `format` and `format_version` | [README.md](../README.md#newer-documents) | Yes | "This document was saved by a newer version of SimpleDiagrams." |
| [complex-shape-in-format-17](complex-shape-in-format-17/README.md) | Versions | Complex shapes need document format 18 | [elements.md](../elements.md#14-reading-older-document-formats) | Indirectly | "The SimpleDiagrams document contains an invalid document.json file." |
| [unknown-top-level-key](unknown-top-level-key/README.md) | Unknown keys | Unknown keys are refused at the top level of `document.json` | [README.md](../README.md#unknown-keys) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [unknown-key-in-attachment](unknown-key-in-attachment/README.md) | Unknown keys | A connector attachment has exactly `element_id` and either `connection_point_id`, or `attach_line_id` with `distance` | [connections.md](../connections.md#attachments) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [other-types-key-in-complex-shape](other-types-key-in-complex-shape/README.md) | Unknown keys | A `complex_shape` record may hold only `type`, `hand_drawn` and the keys of its own type | [elements.md](../elements.md#121-the-record) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [zero-pages](zero-pages/README.md) | Bounds | A document has 1 to 100 pages | [README.md](../README.md#the-document) | Yes | "The diagram contains an invalid number of pages (0)." |
| [zero-width-frame](zero-width-frame/README.md) | Bounds | An element's frame has a width and height above 0 | [elements.md](../elements.md#3-identity-frame-rotation-and-flips) | Yes | "The diagram contains an invalid number in element width." |
| [text-region-outside-shape](text-region-outside-shape/README.md) | Bounds | A text region must touch its shape: `x` from `-width` to 1 and `y` from `-height` to 1, as fractions of the frame | [elements.md](../elements.md#7-text-layout) | Yes for this value (`x` above 1) | "The SimpleDiagrams document contains an invalid document.json file." |
| [unknown-element-kind](unknown-element-kind/README.md) | Closed vocabularies | An element's `kind` is one of eleven fixed values | [elements.md](../elements.md#1-element-kinds) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [unknown-line-ending](unknown-line-ending/README.md) | Closed vocabularies | A connector's `start_ending` and `end_ending` are names from a fixed list | [connections.md](../connections.md#line-endings) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [three-digit-colour](three-digit-colour/README.md) | Closed vocabularies | A colour is `#RRGGBB` or `#RRGGBBAA` | [README.md](../README.md#terms) | Yes | "The diagram contains an invalid color value: #FFF." |
| [repeated-identifier](repeated-identifier/README.md) | Cross-references | Document, page, element, connector, group, guide and Quick Style identifiers share one namespace: no two may be equal | [README.md](../README.md#identity-rules) | No | "The diagram repeats identifier 748E6ED8-9628-5613-B3ED-54285F38B73B." |
| [attachment-to-missing-point](attachment-to-missing-point/README.md) | Cross-references | An attached connector end names a connection point the element has | [connections.md](../connections.md#attachments) | No | "Connection 602FF722-E8D6-5BE4-9350-E381781C87B7 refers to a missing element." |
| [incomplete-drawing-order](incomplete-drawing-order/README.md) | Cross-references | When a page has `drawableOrder`, it names every element and connector on the page exactly once | [README.md](../README.md#drawing-order) | No | "The diagram contains an invalid number in drawable order." |
| [object-in-two-groups](object-in-two-groups/README.md) | Cross-references | Each element, connector and group belongs directly to at most one group | [connections.md](../connections.md#membership-rules) | No | "Group 068B780A-CBE2-51AA-A15D-CD28EAD7FE6F contains an invalid relationship." |
| [key-on-wrong-kind](key-on-wrong-kind/README.md) | Cross-references | Kind-specific keys belong only to their kind: `symbol_reference` only to `symbol` | [elements.md](../elements.md#10-which-kind-may-carry-which-key) | Yes | "The diagram contains invalid text in unexpected symbol reference." |
| [unlisted-asset-file](unlisted-asset-file/README.md) | Inventory and files | `assets/` holds exactly the files listed in `embedded_assets` | [assets.md](../assets.md) | No | "The SimpleDiagrams document contains an invalid assets file." |
| [asset-byte-count-wrong](asset-byte-count-wrong/README.md) | Inventory and files | An inventory entry's `byte_count` equals the size of its file | [assets.md](../assets.md) | No | "The SimpleDiagrams document contains an invalid assets file." |
| [asset-name-not-its-hash](asset-name-not-its-hash/README.md) | Asset name and hash | An asset's `id`, and so its file name, is the SHA-256 of the file's bytes | [assets.md](../assets.md) | No | "The SimpleDiagrams document contains an invalid assets file." |
| [svg-with-script-element](svg-with-script-element/README.md) | Asset contents | SVG artwork must pass the safety profile, which refuses active content such as a `script` element even when it is empty | [assets.md](../assets.md) | No | "The SimpleDiagrams document contains an invalid assets file." |
| [vector-asset-id-mismatch](vector-asset-id-mismatch/README.md) | Asset contents | A `.sdvector` file's `asset_id` is the SHA-256 of the canonical encoding of its artwork | [assets.md](../assets.md) | No | "The SimpleDiagrams document contains an invalid assets file." |
| [character-run-past-end](character-run-past-end/README.md) | Text ranges | A character run ends at or before the end of the text, counted in UTF-16 code units | [elements.md](../elements.md#63-runs-and-ranges) | No | "The diagram contains invalid text in character run." |
| [character-run-splits-character](character-run-splits-character/README.md) | Text ranges | Both ends of a run fall between whole characters as a person sees them | [elements.md](../elements.md#63-runs-and-ranges) | No | "The diagram contains invalid text in character run." |
| [container-in-group](container-in-group/README.md) | Complex shapes | A container or swimlanes is never a member of a group | [elements.md](../elements.md#1210-rules-across-the-page) | No | "Shape 991246F5-D565-5AAC-B093-C922F44392DF holds a container, swimlanes, table, callout or lifeline record that is not valid." |
| [table-cell-count-mismatch](table-cell-count-mismatch/README.md) | Complex shapes | A table has one row of `cells` per entry of `row_sizes` and one cell per entry of `column_sizes` | [elements.md](../elements.md#127-table) | No | "Shape 890CB3FA-71CA-55F8-A832-0F424FD90E6D holds a container, swimlanes, table, callout or lifeline record that is not valid." |
| [shape-in-two-containers](shape-in-two-containers/README.md) | Complex shapes | A shape belongs to at most one container or lane | [elements.md](../elements.md#1210-rules-across-the-page) | No | "Shape 33D5BD73-2FFB-555A-B77C-9ECBC8FC0B96 holds a container, swimlanes, table, callout or lifeline record that is not valid." |
| [container-holding-1001-shapes](container-holding-1001-shapes/README.md) | Complex shapes | A container holds at most 1,000 members | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | Yes | "The diagram contains a container holding too many shapes (1,001)." |
| [lane-holding-1001-shapes](lane-holding-1001-shapes/README.md) | Complex shapes | A lane holds at most 1,000 members | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | Yes | "The diagram contains a lane holding too many shapes (1,001)." |
| [swimlanes-holding-2001-shapes](swimlanes-holding-2001-shapes/README.md) | Complex shapes | All the lanes of one swimlanes shape hold at most 2,000 members together | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | No | "The diagram contains swimlanes holding too many shapes (2,001)." |
| [lane-title-of-1001-characters](lane-title-of-1001-characters/README.md) | Complex shapes | A lane title holds at most 1,000 characters | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | Yes | "The diagram contains a lane title that is too long (1,001 characters)." |
| [lane-title-with-101-runs](lane-title-with-101-runs/README.md) | Complex shapes | A lane title has at most 100 runs | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | Yes | "The diagram contains a lane title with too many text-formatting runs (101)." |
| [cell-of-2001-characters](cell-of-2001-characters/README.md) | Complex shapes | A table cell holds at most 2,000 characters | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | Yes | "The diagram contains a table cell that is too long (2,001 characters)." |
| [cell-with-201-runs](cell-with-201-runs/README.md) | Complex shapes | A table cell has at most 200 runs | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | Yes | "The diagram contains a table cell with too many text-formatting runs (201)." |
| [table-of-100001-characters](table-of-100001-characters/README.md) | Complex shapes | All the cells of one table hold at most 100,000 characters together | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | No | "The diagram contains a table with too much text (100,001 characters)." |
| [table-with-10001-runs](table-with-10001-runs/README.md) | Complex shapes | All the cells of one table have at most 10,000 runs together | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | No | "The diagram contains a table with too many text-formatting runs (10,001)." |
| [page-with-2001-complex-shapes](page-with-2001-complex-shapes/README.md) | Complex shapes | A page holds at most 2,000 complex shapes | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | No | "The diagram contains a page with too many complex shapes (2,001)." |
| [page-with-501-containers](page-with-501-containers/README.md) | Complex shapes | A page holds at most 500 containers and swimlanes together | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | No | "The diagram contains a page with too many containers and swimlanes (501)." |
| [page-with-1000001-characters-in-tables](page-with-1000001-characters-in-tables/README.md) | Complex shapes | The lane titles and table cells of one page hold at most 1,000,000 characters together | [elements.md](../elements.md#1211-how-much-a-shape-and-a-page-hold) | No | "The diagram contains a page with too much text in its complex shapes (1,000,001 characters)." |
| [unused-shape-data-definition](unused-shape-data-definition/README.md) | Shape data | Every entry of `shape_data_definitions` is used by at least one element | [shape-data.md](../shape-data.md) | No | "The SimpleDiagrams document contains an invalid document.json file." |
| [missing-shape-data-value](missing-shape-data-value/README.md) | Shape data | An element has exactly one value for every field of its definition | [shape-data.md](../shape-data.md) | No | "The SimpleDiagrams document contains an invalid document.json file." |
| [null-shape-data-source](null-shape-data-source/README.md) | Shape data | A definition's `source` is left out when there is none; it is never written as `null` | [shape-data.md](../shape-data.md) | Yes | "The SimpleDiagrams document contains an invalid document.json file." |
| [required-libraries-in-format-15](required-libraries-in-format-15/README.md) | Required libraries | `required_libraries` needs document format 16 or later; document formats 11 to 15 refuse it | [libraries.md](../libraries.md), [README.md](../README.md#what-an-older-document-format-refuses) | Indirectly | "The SimpleDiagrams document contains an invalid document.json file." |
| [library-shape-names-in-format-17](library-shape-names-in-format-17/README.md) | Library shape names | `library_shape_names` needs document format 18; document formats 11 to 17 refuse it | [libraries.md](../libraries.md), [README.md](../README.md#what-an-older-document-format-refuses) | Indirectly | "The SimpleDiagrams document contains an invalid document.json file." |

## Described only

These faults need a file or folder that Git does not store, or a folder that must be missing, so they
are described here rather than stored. Each is a common way a good document gets broken by copying it.

| Fault | Area | Stated in | Message (English) |
| --- | --- | --- | --- |
| Finder's `.DS_Store` file inside the package | Package members | [README.md](../README.md) | "The SimpleDiagrams document contains an invalid .DS_Store file." |
| An AppleDouble `._document.json` file, added when a package is copied to a drive without extended attributes | Package members | [README.md](../README.md) | "The SimpleDiagrams document contains an invalid ._document.json file." |
| No `assets/` folder (a tool such as Git dropped the empty folder) | Package members | [README.md](../README.md) | "The SimpleDiagrams document is missing assets." |
| A `.gitkeep` file inside an otherwise empty `assets/` | Inventory and files | [assets.md](../assets.md) | "The SimpleDiagrams document contains an invalid assets file." |

## How these were checked

Every document here was opened with the same reading code as SimpleDiagrams 5.1.1, with an empty
`assets/` folder added where the document has none. Each was refused with the message shown, and the
same document without its one fault opened. The four described-only faults were checked the same way.
The twelve cases on how much a shape and a page hold were checked the same way with the reading code of
SimpleDiagrams 5.2, which adds those limits; SimpleDiagrams 5.1.0 and 5.1.1 open them.
