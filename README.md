# XJustiz Schema Workbench

Single-file, dependency-free browser tool for working with XJustiz XSD schemas:

- **Outline** — browse a schema file as a tree
- **Diagram** — XSD-style visual model of any element/type
- **Generate** — build an XJustiz message from the schema, auto-fill with valid German test data, validate, and download
- **Validate** — best-effort structural check of a schema bundle or an XML message (order, occurrence, required attributes, enumerations, patterns, basic datatypes)

Everything runs client-side. No build step, no server-side code, no external libraries.

## The two HTML variants

| File | Schemas | How to run | Use for |
|------|---------|------------|---------|
| `index.html` | **Preloaded** — pick a version from the dropdown; it fetches the set from `schemas/` on the server | Must be **served over http(s)** (the dropdown uses `fetch`) | Hosted use — quickest way to load a full version |
| `xjustiz_workbench.html` *(optional, manual variant)* | **None preloaded** — drag in `.xsd` files or use "Add files" | Works by **double-click** (`file://`), fully offline | Local/offline use without hosting |

Both share the same engine; they differ only in whether the version dropdown is wired to the hosted `schemas/` folder. The manual variant also works inside `index.html` — drag-drop and "Add files" are always available.

## Repo contents

```
index.html                 the app (preloaded version loader + manual upload)
schemas/                   deploy bundle served by index.html
  3.5.1/  3.6.2/  4.0.0/   each: the version's .xsd files + manifest.json
schemas.zip                zipped schemas/ for easy upload
XJustiz_3.5.1/             original download — spec PDF + schemata
XJustiz_3.6.2/             original download — schemata
XJustiz_4.0.0/             original download — spec PDF + schemata
XJustizExamples/           6 sample 3.6.2 messages (.xml) for testing validation
```

## Deploy (for `index.html`)

Upload `index.html` **and** the `schemas/` folder to the same location, served over http(s):

```
/xjustiz/index.html
/xjustiz/schemas/3.6.2/...
```

Same-origin, so no CORS setup. The base path is the `SCHEMA_BASE` constant near the top of the script (`'schemas/'`) — change it if you host the schemas elsewhere.

Opening `index.html` via `file://` (double-click) disables the version dropdown — browsers block `fetch` from local files. Use the manual upload/drag-drop instead, or serve the file.

## Add a new XJustiz version

1. Create `schemas/<version>/` and drop in that version's `.xsd` files.
2. Add a `manifest.json` listing the filenames, e.g.:
   ```json
   ["din-norm-91379-datatypes.xsd", "xjustiz_0000_grunddatensatz_x_y.xsd", "..."]
   ```
3. Add one `<option value="<version>">` to the `#verSel` dropdown in `index.html`.

## Notes

- Target version for day-to-day work is **3.6.2**.
- To generate a *complete* message, load the full version set — single schema files reference imported types (grunddatensatz, codelists, din-norm); the generator flags which dependency files are still missing.
- Auto-fill produces *valid fake* data, not real codelist values (court codes etc. live in external code lists not shipped in the XSDs). Click Auto-fill again for fresh values; Reset clears all fields.
- Validation is best-effort structural — not a full XSD processor (no substitution groups, `xsi:type`, or identity constraints).
