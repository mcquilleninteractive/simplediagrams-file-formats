# SimpleDiagrams file formats

This repository describes the files that [SimpleDiagrams](https://simplediagrams.com) for macOS reads and
writes, so that other programs can read and write them too. It holds, for each format version, a
plain-language specification, JSON Schemas, documents the app accepts and documents it refuses.

| Format | Folder | Status |
| --- | --- | --- |
| Document format 19 (`.simplediagram`), the current format | [`document/19/`](document/19/README.md) | Draft until SimpleDiagrams 5.2 is released and the `document-19` tag; final from that tag |
| Document format 18 (`.simplediagram`) | [`document/18/`](document/18/README.md) | Draft until the `document-18` tag; final from that tag |

## The SimpleDiagrams document

A SimpleDiagrams document is a file whose name ends in `.simplediagram`. On disk it is a *document
package*: a folder that macOS Finder shows as a single file. Inside are a small `manifest.json`, the
whole diagram in `document.json`, and an `assets/` folder for embedded artwork and images.

The **document format version** is the whole number in `manifest.json` under `format_version`. It goes
up only when the way a document is laid out changes, not with every app release. Inside
`document.json`, `schema_version` is always one less.

| Document format | Written by SimpleDiagrams |
| --- | --- |
| 19 | 5.2 |
| 18 | 5.1.0 and later 5.1 releases |
| 17 | 5.0.16 |
| 16 | No public release |
| 15 | 5.0.8 to 5.0.14 |
| 14 | 5.0.4 to 5.0.7 |
| 11 to 13 | No public release |
| 10 | 5.0.0 to 5.0.3 |
| 1 to 9 | No public release |

Each document format version that this repository specifies has its own folder. Document format 19 is
the current format. A program that writes documents MUST write the newest format it implements; the
app reads every earlier format itself.

## The compatibility promise

1. **A released format version's specification is frozen.** Its wording may be clarified; what it
   allows or refuses never changes. Clarifications are listed in [CHANGELOG.md](CHANGELOG.md). The one
   exception is a format's generator catalogue (`document/19/generators/`), which may gain entries,
   and words in its entries' open settings, without changing what any valid document means; each
   addition is listed in the CHANGELOG.
2. **The app opens every earlier document format version** it has ever written.
3. **A document written to a published specification opens** in every app version that writes that
   format version or a later one. "Written to the specification" means using only what it lists
   wherever a reader must know a value to proceed: for example, a generated shape that a format 19
   document holds in its incomplete form, for the app to draw on opening, names only the generators,
   versions and setting words the format 19 specification lists.
4. **No promise is made that an older app opens a newer format.** The app refuses a newer document
   and asks the person to update SimpleDiagrams.
5. **The app always saves in its newest format,** so a document saved by a newer app needs that app or
   a later one.

## How to use this repository

- **Read** a format folder's `README.md` first; it links every page of that format.
- **Validate** a document's JSON with the schemas in the format's `schemas/` folder. JSON Schema cannot
  express every rule (for example that an identifier refers to something that exists), so each
  `schemas/README.md` lists the rules a program must also check, with the page that states them.
- **Test** a reader or writer against `examples/` (documents the app opens) and `refused/` (documents
  the app refuses, one broken rule each).
- The schemas' `$id` values (such as `https://simplediagrams.com/schemas/document/19/document.schema.json`)
  are identifiers. They are not promised to be served at those addresses; use the files here.

## Versions and tags

A format folder is a **draft** until this repository is tagged `document-<version>`, for example
`document-19`, which happens when the SimpleDiagrams release that writes that format is published.
From that tag on, the folder is final and its rules are frozen
([The compatibility promise](#the-compatibility-promise)), except that `document/19/generators/` may gain
entries, and words in its entries' open settings, after the tag. Such an addition never changes what an
existing valid document means, and is recorded in [CHANGELOG.md](CHANGELOG.md).

## Questions and corrections

Open an issue on this repository.

- If the app refuses a document that conforms to a released format's specification, that is a bug in
  the app, and it is fixed in the app.
- If the specification's wording is ambiguous, it is clarified, and the clarification is listed in
  [CHANGELOG.md](CHANGELOG.md).

The rules of a released format never change: neither kind of correction changes what that format allows
or refuses.

## Licence

The specification text (every Markdown file) is licensed under Creative Commons Attribution 4.0
International; the JSON Schemas, the built-in shape catalogue and the example and refused documents are
licensed under the MIT License. See [LICENSE.md](LICENSE.md).

These licences cover the files in this repository, not the SimpleDiagrams name, the app, or its shape
libraries.
