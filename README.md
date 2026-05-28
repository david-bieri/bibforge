# BibForge

![Version](https://img.shields.io/github/v/tag/davidbieri/bibforge?label=version&style=flat-square&color=c8973a)
![No dependencies](https://img.shields.io/badge/dependencies-none-5a9a6e?style=flat-square)
![Deploy](https://img.shields.io/badge/deploy-GitHub%20Pages-5b8fa8?style=flat-square)

A single-file, client-side academic reference manager for large BibTeX libraries, deployable on GitHub Pages with no build step and no backend.

Built for academic collections with disciplinary emphasis on urban planning, spatial and regional economics, monetary history, and political economy. Handles libraries of 10,000+ entries without performance degradation.

**Current version: v1.3** · [Changelog](CHANGELOG.md)

---

## Quick Start

**First time:**
1. Open the app and drop your `.bib` file onto the landing screen, or click **Import .bib**
2. The library loads in a few seconds — a 12,000-entry file takes 2–4 seconds
3. Use the search bar or sidebar filters to find entries; click any row to open its detail

**Adding a new entry:**
- Press `Ctrl/Cmd+D` → paste a DOI → click **Fetch** → **Add to Library** *(fastest path)*
- Or click **New** in the header to open the full edit form with all fetch options

**Importing from CSV:**
1. Click **Import .csv** in the header
2. A column mapping dialog opens — each CSV column is auto-matched to a BibTeX field
3. Review and adjust mappings; a sample value from row 1 is shown for each column
4. Click **Import** — entries are stamped `datasource = {csv-import}` and added to the library

**Fetching metadata for a book without a DOI:**
1. Click **New**, then switch to the **ISBN / Open Library** tab in the edit form
2. Enter the ISBN and click **Open Library ↗** — Google Books is tried automatically if the result is sparse
3. If neither API has it, click **WorldCat ↗** to search directly in a new tab

**Cataloging a book from a photo (single cover or spine):**
1. Click **New**, then switch to the **📷 Photo** tab in the edit form
2. Drop a photo of the cover or spine onto the upload zone (or click to browse)
3. If a barcode is visible, the ISBN is extracted locally — no API key required
4. If no barcode is present, Claude Vision reads the cover text and routes to title or ISBN search automatically (requires an Anthropic API key, entered once per session)

**Cataloging multiple books from a shelf photo:**
1. Click **Photo Batch** in the header
2. Drop a photo showing multiple spines — Claude Vision extracts the full inventory
3. Review the editable table; correct any misread titles (uncertain readings are highlighted)
4. Set a shelf tag and click **Look up N books** — each entry is looked up in sequence
5. Add resolved entries directly to the library, or download as `photo-import.bib`
6. Unresolved entries export as `photo-batch-residuals.txt` for manual follow-up

**Classifying an entry:**
- Scroll to the **Classification** section of the edit form
- Click **Fetch JEL** — the app suggests the top 3 codes ranked by relevance; click chips to accept
- For books, click **Suggest LOC** — picks the best Library of Congress subclass from title and keywords

**Library analytics:**
- Click **Insights** in the toolbar to open the six-panel analytics dashboard
- Tabs: Overview · Citation Age · Authors · Venues · JEL Coverage · Quality

**Saving your work:**
- Click **Export .bib** in the header at any point — the file includes all edits and custom fields
- The app holds everything in memory; nothing is saved automatically between sessions

**Theme:** click the sun/moon icon in the top-right corner to switch between dark and light modes. The preference is remembered in your browser.

---

## Deployment

1. Rename `bibforge.html` to `bibforge.html` (or `index.html` if deploying without a separate landing page)
2. Optionally rename `landing_v1.html` to `index.html` as the repo landing page
3. Push to any GitHub repository
4. Enable GitHub Pages: **Settings → Pages → Source: main branch, / (root)**
5. Access at `https://yourusername.github.io/your-repo/`

No dependencies to install. No build process. No server required.

**Recommended repo structure:**

```
your-repo/
├── index.html           ← landing_v1.html renamed (optional)
├── bibforge.html        ← the app
├── data/
│   └── library.bib      ← auto-loaded on startup
├── README.md
└── CHANGELOG.md
```

---

## Features

### Importing and Exporting

| Action | How |
|---|---|
| Import `.bib` | Click **Import .bib** in the header, or drag and drop onto the landing screen |
| Import `.csv` | Click **Import .csv** → header mapping dialog → **Import** |
| Import from photo (single) | Click **New** → **📷 Photo** tab → drop cover or spine photo |
| Import from photo (batch) | Click **Photo Batch** in the header → drop shelf/stack photo → review → lookup |
| Export `.bib` | Click **Export .bib** — exports the full library with all edits and custom fields |
| Start fresh | Click **Start with empty library** to begin without a file |

All standard BibTeX entry types are supported: `@article`, `@book`, `@inbook`, `@incollection`, `@inproceedings`, `@techreport`, `@phdthesis`, `@misc`, `@unpublished`, and working papers.

> **Note:** Changes are held in memory only. Export before closing the browser tab.

---

### CSV Import

The CSV import accepts any file with a header row. Column names are auto-detected against common BibTeX fields:

- Recognizes variations: `Authors`, `Publication Year`, `ISBN-13`, `Issue Number`, `Book Title`, etc.
- Shows a sample value from row 1 for each column to guide manual mapping
- Unmapped columns are skipped
- `type` column values are normalized (`Journal Article` → `article`, `Book Chapter` → `inbook`, etc.)
- Duplicate citation keys are auto-resolved with letter suffixes (`smithb`, `smithc`)
- All imported entries receive `datasource = {csv-import}` for tracking

---

### Photo Import

BibForge can catalog physical books directly from photos of covers, spines, or entire shelves.

#### Single Book — 📷 Photo fetch tab

Available in the **New / Edit** form as a fourth fetch tab alongside DOI, ISBN, and Title Search.

- Drop or browse to a photo of a book cover or spine
- **Barcode present:** ISBN extracted client-side via ZXing — no API key required; feeds directly into the Open Library → Google Books lookup chain
- **No barcode:** Claude Vision (Anthropic API) reads cover/spine text and auto-routes to ISBN or title search

#### Batch Import — Photo Batch modal

Accessible via the **Photo Batch** header button. Designed for shelf photos showing multiple spines simultaneously.

1. Upload photo → Claude Vision returns a structured spine inventory
2. Review table shows every detected book with editable Title, Author, and ISBN fields; rows with uncertain readings are highlighted in amber
3. Set a shelf tag (applied to all entries; editable per-entry afterwards)
4. Batch lookup runs entry by entry with live status — Open Library first, Google Books fallback
5. Results: **Add to Library** directly, or **Download .bib** for manual review
6. Unresolved entries: **Download unresolved** → `photo-batch-residuals.txt` in `needs_manual_scan.txt` tier format

**API key:** Vision features require an Anthropic API key. On first use a modal prompts for the key; it is stored in `sessionStorage` only and cleared when the tab closes. It is never written to `localStorage` or transmitted anywhere except `api.anthropic.com`.

---

### Search and Filtering

The left sidebar and header search bar work together to narrow the entry list.

**Search** (`Ctrl/Cmd+F` to focus) covers title, author, citation key, DOI, JEL codes, keywords, and data source simultaneously. Results update within 200 ms.

**Sidebar filters:**

| Filter | Behavior |
|---|---|
| Entry type | Click to show only that type; live counts update as other filters change |
| Year range | From / To inputs; either or both can be set |
| JEL code | Dropdown of codes present in your library |
| LOC class | Dropdown of classes present in your library |
| Data source | Filter by CrossRef, Open Library, Google Books, CSV Import, Photo (barcode), Photo (Vision), or Manual |
| Sort | Year ↓↑, Author A–Z / Z–A, Title A–Z, Citation key A–Z |

---

### Library Insights

Click **Insights** in the toolbar to open the six-panel analytics dashboard. All analytics run on the in-memory library — no data is sent anywhere.

| Panel | Content |
|---|---|
| **Overview** | Total entries, JEL/LOC classification rates, recent (≤5yr) percentage, datasource breakdown |
| **Citation Age** | Publication year histogram (up to 40 buckets), median age, average age, decade breakdown |
| **Authors** | Top 20 authors by entry count, multi-author rate, unique author count |
| **Venues** | Top 20 journals/booktitles by frequency, venue coverage rate |
| **JEL Coverage** | Top codes with descriptions, category letter roll-up (A–Z), unique codes used |
| **Quality** | Average metadata completeness, distribution across quality tiers, per-field fill rate |

---

### Adding and Editing Entries

#### Quick Add via DOI — `Ctrl/Cmd+D`

The fastest path for any entry with a DOI. Press `Ctrl/Cmd+D`, paste a DOI or full `https://doi.org/…` URL, click **Fetch**, review the preview, and click **Add to Library**. Populates title, authors, year, journal, volume, issue, pages, publisher, DOI, ISBN, ISSN, URL, and abstract.

#### Full Edit Form

Click **New** in the header, or select an entry and click **Edit** in the detail panel.

**Tab 1 — DOI / CrossRef:** For journal articles and recent books.

**Tab 2 — ISBN / Open Library:** For books and edited volumes.
- **Open Library ↗** — queries the Open Library API; falls back to Google Books if sparse
- **Google Books ↗** — queries Google Books API directly
- **WorldCat ↗** — opens a WorldCat search in a new tab

**Tab 3 — Title Search:** When you have no ISBN or DOI.

**Tab 4 — 📷 Photo:** For physical books with a cover or spine photo. See [Photo Import](#photo-import) for full details.

---

### Metadata Provenance — `datasource` Field

Every entry fetched via an API or imported from CSV is stamped with a `datasource` field:

| Value | Source |
|---|---|
| `crossref` | CrossRef API (DOI lookup) |
| `openlibrary` | Open Library |
| `googlebooks` | Google Books (direct call or fallback) |
| `csv-import` | CSV file import |
| `photo-barcode` | ISBN extracted from barcode in a photo (client-side, no API key) |
| `photo-vision` | Title/author extracted via Claude Vision, resolved via API |
| `manual` | Hand-entered or imported without a fetch |

---

### Classification

#### JEL Codes (Journal of Economic Literature)

Click **Fetch JEL** in the Classification section of the edit form. The app fetches the full AEA EconLit classification tree on first use (cached for the session) and runs a keyword-scoring engine against title, abstract, and keywords. Top 3 codes are shown as ranked chips with matched terms and their full descriptions.

**Output format:**
```bibtex
jel          = {R11; R31; H72},
```

#### Library of Congress Classification

Click **Suggest LOC** (active for book-type entries only). Up to 3 candidate classes appear as clickable chips. The engine uses a keyword table weighted toward economics, social science, and urban planning subclasses.

**Output format:**
```bibtex
lcc          = {HD},
```

---

### Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl/Cmd+D` | Open Quick Add via DOI modal |
| `Ctrl/Cmd+F` | Focus search bar |
| `Ctrl/Cmd+S` | Save entry (while editing) |
| `↑` / `↓` | Navigate the entry list |
| `Escape` | Cancel edit / close modal |

---

### Citation Key Convention

BibForge uses the format `author:year:keyword` throughout, matching the Better BibTeX plugin pattern:

```
[auth:lower]:[year]:[title:lower:clean:truncate=20]
```

Examples: `muth:1969:cities`, `alonso:1964:location`, `keynes:1936:employment`

When a fetch or CSV import generates a key that already exists in the library, a letter suffix is appended automatically (`smithb`, `smithc`, etc.).

---

### Compatibility with Zotero and JabRef

The exported `.bib` file is standard BibTeX and imports directly into:

- **Zotero** — File → Import → BibTeX
- **JabRef** — File → Import → BibTeX
- **LaTeX** — `\bibliography{library_export}` and `\cite{key}`

The custom fields (`jel`, `lcc`, `datasource`) are preserved through round-trips in both Zotero and JabRef.

---

## Auto-Loading a Library from the Repository

Configure `BIBFORGE_CONFIG.autoLoad` to a repo-relative path and the library fetches automatically on startup:

```javascript
const BIBFORGE_CONFIG = {
  autoLoad: '/data/library.bib',   // path relative to repo root
  autoLoadLabel: 'library.bib',    // display name shown in the UI
};
```

Set `autoLoad: null` to disable. On startup BibForge issues a `fetch()` to the configured path. Because the `.bib` file is served from the same GitHub Pages origin as the app, the request succeeds without CORS restrictions or authentication.

### Recommended editing cycle with auto-load

1. Open BibForge — library loads automatically from the repo
2. Browse, add entries, fetch metadata, classify, run Insights
3. Click **Export .bib** — save the file back to `data/library.bib` locally
4. Commit and push — the updated library is live on the next page load

---

## Analytics

BibForge uses [GoatCounter](https://www.goatcounter.com) for privacy-friendly page analytics on the landing page and app. GoatCounter collects no personal data, sets no cookies, and does not track individuals across sites. Only page views and referrers are recorded. Public stats are available at [bibforge.goatcounter.com](https://bibforge.goatcounter.com).

---

## Technical Notes

### Performance

The entry list uses virtual scrolling — only the rows currently visible in the viewport are rendered as DOM elements, regardless of library size. Row height is fixed at 76px.

### Privacy and Outbound Requests

All processing happens in the browser. No library data is ever transmitted. The only outbound requests are:

| Endpoint | When | What is sent |
|---|---|---|
| `api.crossref.org` | DOI lookup | The DOI only |
| `openlibrary.org` | ISBN or title lookup | The ISBN or search query |
| `googleapis.com/books` | Google Books lookup or fallback | The ISBN or search query |
| `www.aeaweb.org/econlit/classificationTree.xml` | First Fetch JEL click | Nothing (GET request) |
| `api.anthropic.com/v1/messages` | Photo Import (Vision) — when no barcode detected | Base64-encoded image + spine-extraction prompt |
| `cdn.jsdelivr.net` | App startup (ZXing barcode library) | Nothing (GET request) |
| `gc.zgo.at/count.js` | Page load | Page URL and referrer (no cookies) |
| `fonts.googleapis.com` | App startup | Nothing (GET request) |
| Same-repo `.bib` path | Auto-load on startup | Nothing (same-origin GET) |

WorldCat opens in a new browser tab — no data passes through the app.

> **Vision privacy note:** When Claude Vision is used, the uploaded image is sent to `api.anthropic.com` for spine text extraction. No library data or BibTeX content is ever transmitted. The Anthropic API key is session-only and never persisted.

### Browser Storage

Theme preference (light/dark) is saved to `localStorage`. All library data lives in memory only for the duration of the session — nothing is written to disk automatically. The Anthropic API key (if set) is stored in `sessionStorage` and cleared automatically when the tab closes.

---

## Version Control

Tag each release before pushing:

```bash
# Tag v1.3
git tag -a v1.3 -m "BibForge v1.3 — Photo Import (single + batch), Claude Vision, ZXing barcode"
git push origin v1.0
```

Create a GitHub Release from the tag with the relevant CHANGELOG section as the description.

---

## Developed for

David Bieri · Virginia Tech SPIA  
Academic library inventory workflow — ~550 volume collection spanning urban planning, spatial/regional economics, monetary history, political economy, and social theory.

*Paired with `library_inventory.bib`, `library_zotero.csv`, and `inventory_workflow.md`.*
