# Example documents

Each folder ending in `.simplediagram` here is a complete document format 19 document that the app
opens. They are small and made up, and laid out to be read: the JSON is indented, with keys in
sorted order and identifiers in upper case, as the app writes them. (The app writes the same JSON without
white space; key order and white space do not matter to a reader.)

The examples leave out every optional key they do not need, so they show what a writer must supply.
A document the app has saved also carries keys with their default values, such as
`preserves_aspect_ratio` on every element and the grid's `visible` and `appearance`.

## Before opening one

Every document needs an `assets/` folder, even an empty one, and Git does not keep empty folders. So
every example except `embedded-assets.simplediagram` is stored without `assets/`. **Create an empty
`assets/` folder inside an example before opening it**, or the app refuses it with "The SimpleDiagrams
document is missing assets." Do not add a placeholder file such as `.gitkeep`: the app refuses any file
in `assets/` that the document does not list.

The repository's `.gitattributes` file stops Git from converting line endings inside these packages, so
every file keeps the exact bytes that its SHA-256 name and inventory entry depend on.

## The examples

| Example | What it shows | Read with |
| --- | --- | --- |
| [`minimal.simplediagram`](minimal.simplediagram/) | The smallest useful document: one page holding one rectangle with a label. It has no connection points, no Quick Styles and no optional keys | [README.md](../README.md#the-document) |
| [`basic-shapes.simplediagram`](basic-shapes.simplediagram/) | A rectangle with a pencil fill texture; an ellipse turned 15 degrees with a half-transparent fill; a sticky note; an index card with its label region at the top; a `text` element with bold and italic character runs and a right-aligned second paragraph; an ellipse whose caption sits below it in a region three times its width, starting left of the shape; a smoothed pencil stroke drawn with a marker tip; a closed, dashed path. The page has a styled grid, a solid background and a guide, and the document two Quick Styles | [elements.md](../elements.md), [README.md](../README.md#pages) |
| [`connectors.simplediagram`](connectors.simplediagram/) | A direct connector with a label; an automatic right-angle connector (two stored points) with entity-relationship endings; a hand-adjusted right-angle connector whose four points are its corners, routed without avoiding other shapes; a free, dashed S-curve made of `curve` points. The page's `drawableOrder` puts every connector behind the shapes | [connections.md](../connections.md) |
| [`groups.simplediagram`](groups.simplediagram/) | Nested groups: an inner group holds two shapes and the connector between them; a locked outer group holds the inner group and a title | [connections.md](../connections.md#groups) |
| [`container.simplediagram`](container.simplediagram/) | A container with its title in a band, holding two shapes (the connector between them is not a member); a second container with its title in a corner tag, drawn without the hand-drawn pen, holding a note | [elements.md](../elements.md#125-container) |
| [`swimlanes.simplediagram`](swimlanes.simplediagram/) | Horizontal swimlanes with two equal lanes, each holding one shape, and a right-angle connector from one lane to the other | [elements.md](../elements.md#126-swimlanes) |
| [`table.simplediagram`](table.simplediagram/) | A table of three rows and two columns, the first column twice as wide, with a header row and aligned cells. The element's own text is empty, as a table's must be | [elements.md](../elements.md#127-table) |
| [`callout.simplediagram`](callout.simplediagram/) | A callout whose tail is attached to connection point 6 of a rectangle, with the tip stored where the tail is drawn; and a flipped callout with a free tail | [elements.md](../elements.md#128-callout) |
| [`lifelines.simplediagram`](lifelines.simplediagram/) | A lifeline with a box head and one with a stick-figure head that ends in a destroy mark. Two messages attach along each lifeline's attach line `0`, at stored distances: a labelled request and a dashed reply | [elements.md](../elements.md#129-lifeline), [connections.md](../connections.md#attach-lines) |
| [`library-shapes.simplediagram`](library-shapes.simplediagram/) | A built-in shape placed by reference (Basic's `Cylinder`), and a shape from a downloadable library placed by its library's and shape's UUIDs, with `required_libraries` naming that library and `library_shape_names` giving both shapes' slugs | [libraries.md](../libraries.md) |
| [`embedded-assets.simplediagram`](embedded-assets.simplediagram/) | Three embedded assets, each named by the SHA-256 of its bytes and listed in `embedded_assets`: a 16 by 16 pixel PNG shown as an `image` with a border; an SVG shown as `svg_artwork`; and a `.sdvector` file of vector artwork format 1 shown as an `editable_vector`, whose body follows the shape's fill and line colours while one small part keeps its own colour | [assets.md](../assets.md) |
| [`shape-data.simplediagram`](shape-data.simplediagram/) | One shared definition with a field of each type (text; number with a unit and bounds; boolean; date; choice with lasting option identifiers), used by two shapes with their own values, some empty and some marked as overrides; and a built-in shape with a definition that has no fields and a `source` | [shape-data.md](../shape-data.md) |
| [`generated-shapes.simplediagram`](generated-shapes.simplediagram/) | One shape of each generator SimpleDiagrams 5.2 ships, as its tile places it, with words in their labels: a ring of one section, a flow of three chevrons, a pyramid of three layers, a cycle of four arrows, and a gauge of three bands with a caption | [generated-shapes.md](../generated-shapes.md) |
| [`flows.simplediagram`](flows.simplediagram/) | A flow of each of the seven base shapes across the page, in the spacings and with the pointers they allow, and a flow of chevrons running down | [generated-shapes.md](../generators/com.simplediagrams.flow/README.md) |
| [`gauges.simplediagram`](gauges.simplediagram/) | A gauge at its minimum, one at its maximum, and one of five bands whose labels stay level | [generated-shapes.md](../generators/com.simplediagrams.gauge/README.md) |
| [`generated-placement.simplediagram`](generated-placement.simplediagram/) | A ring turned 30 degrees; a ring in a frame twice as wide as the one it was drawn for, which the one transform keeps round and centred, with one label a person placed and turned; a pyramid of lines alone stretched to a frame of another shape; and two connectors attached to points of these shapes | [generated-shapes.md](../generated-shapes.md#7-the-one-transform) |
| [`part-paint.simplediagram`](part-paint.simplediagram/) | A ring with a pencil-textured fill whose three sections paint themselves: one with a flat colour of its own, one with its own colour and the texture, one with its own line colour and weight | [generated-shapes.md](../generated-shapes.md#3-parts) |
| [`generated-from-newer-versions.simplediagram`](generated-from-newer-versions.simplediagram/) | Generated shapes this release does not know. A shape of another program's generator, `com.example.steps`, drawn from its saved parts: a writer may write shapes of its own generators so, under its own domain. And what a later SimpleDiagrams might write: a `com.simplediagrams.ring` of a version this release does not know, with words of its own and a setting version 1 lacks, and a flow whose base shape is a word version 1 does not list yet. Each opens and is drawn from its parts. The last two show what a reader does with a document from a later release; a writer MUST NOT write a `com.simplediagrams.` generator, version or word the catalogue does not list | [generated-shapes.md](../generated-shapes.md#10-the-three-checks) |
| [`incomplete-generated-shapes.simplediagram`](incomplete-generated-shapes.simplediagram/) | One shape of each of the five generators in the incomplete form, with words but no drawing, as another program may write them; the app draws them as it opens the document | [generated-shapes.md](../generated-shapes.md#12-the-incomplete-form) |
| [`entity-tables.simplediagram`](entity-tables.simplediagram/) | Two entity tables with marked fields, one mark a later release might add (`archived_after`, kept and not drawn), and a relationship drawn from one field's point to another's | [elements.md](../elements.md#entity-tables) |
| [`proportional-container.simplediagram`](proportional-container.simplediagram/) | A container whose layout is `proportional`, holding two shapes | [elements.md](../elements.md#125-container) |
| [`notes-and-highlights.simplediagram`](notes-and-highlights.simplediagram/) | A note of each of the six looks; a text with a highlighted word; and a rectangle whose label has a see-through highlight. Its manifest names the program that wrote it with `app_version` and `app_build` | [elements.md](../elements.md#113-note), [README.md](../README.md#the-manifest) |

## Notes on particular examples

**`library-shapes.simplediagram`.** The downloadable library is made up: no library with this name
exists, so the app draws that shape as a dashed box with a question mark and shows a notice naming
"Example Library". The identifiers are derived from readable names exactly as a published library's
are, so a program can check its own derivation against them:

| Name | UUID |
| --- | --- |
| Library `com.simplediagrams.shapeLibrary.exampleLibrary` | `678BF989-9CFF-895B-A260-AE0831F9E4C1` |
| Shape `rain-barrel` in that library | `A7B42E71-F63D-86CB-A432-C991DD0D7BB0` |

**`embedded-assets.simplediagram`.** The `.sdvector` file is indented, so its file name (the SHA-256 of
its bytes) differs from the digest of the compact canonical encoding of its artwork. The `asset_id`
inside it, `8c267a32bf4c6f6e89e1db2a05312d14531d6103096cd3a50e610539eb617fb7`, is that canonical digest, which a program can use to test its own
canonical encoder.

**Identifiers.** Every UUID in these examples was made up for them, except the library and shape UUIDs
in `library-shapes.simplediagram`, which are derived from names as shown above. A program writing a new
document makes fresh ones.

**`generated-placement`, `part-paint`, `flows`, `gauges` and `generated-shapes`.** The drawings of these
generated shapes are the app's own output for each generator at version 1, so they show what the app
writes; a writer that draws its own parts need not match them.

**`incomplete-generated-shapes.simplediagram`.** The app opens this document as edited: it has drawn the
five shapes, and saving stores them in the complete form.

## How these were checked

Every example was opened with the reading code of SimpleDiagrams 5.2, after adding an empty `assets/`
folder where it has none. Each opened, and the diagram the app read was saved and read again unchanged.

SimpleDiagrams' own automated tests keep an exact copy of this folder. On every run they open each
example, require each refused case to be refused with its stated message, and check that every shape in
`built-in-shapes.json` still has its artwork in the app.
