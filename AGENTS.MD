# AGENTS.MD — sbridge_internal

> Repo root is `internal/` (`sequence-bridge/internal` on GitHub). Only `out-html/` deploys to Netlify (`internal/netlify.toml:3` → `publish = "out-html"`). All pages are self-contained static HTML — no build step.

## Venture deliverables live in `out-html/`

```
out-html/                         # what Netlify publishes
├── index.html                    # landing — must link every page below
├── shared.css                    # single clean style — linked by every page (see Rule 3)
├── favicon.svg
├── netlify.toml                  # publish = "." fallback (root netlify.toml is authoritative)
├── ria/                          # Riamatic venture
│   └── YYYY-MM-DD-<slug>.html
└── sequence-bridge/              # Sequence Bridge venture
    └── YYYY-MM-DD-<slug>.html
```

`out/` holds the markdown sources. `out-html/<venture>/<YYYY-MM-DD>-<slug>.html` mirrors the markdown filename with `.html`.

---

## Rule 1 — `out-html/index.html` must stay fully connected

**Whenever you add, rename, move, or remove an HTML file under `out-html/`, you must update `out-html/index.html` in the same commit so every page is reachable from the index.**

This is not optional — the index is the only navigation between ventures. An orphaned page will deploy but never be discovered.

### What to do on add

1. Create `out-html/<venture>/<YYYY-MM-DD>-<slug>.html` (e.g. `out-html/ria/2026-08-15-intake-automation.html`). Filename must match its `out/*.md` source, date-prefixed.
2. In `out-html/index.html:80-107` add a `<li>` under the correct venture card's `<ul class="document-list">`:

   ```html
   <li><a href="ria/2026-08-15-intake-automation.html"><strong>Intake Automation</strong><span>One-line subtitle · 2026-08-15</span></a></li>
   <!-- or for Sequence Bridge: href="sequence-bridge/2026-08-15-..." -->
   ```

   Keep the list in reverse-chronological order (newest first) to match the existing cards.

3. Bump that card's stat count (`out-html/index.html:85` / `98`):

   ```html
   <span class="stat-value">6</span><span class="stat-label">Working documents</span>
   <!-- was 5, now 6 — must equal the actual file count in out-html/ria/ (or sequence-bridge/) -->
   ```

4. If you are adding a new venture (new folder), add a new `<section class="initiative-card ...">` card alongside the two existing ones, following the same structure, and update the `<p class="foot">` note at `out-html/index.html:110` if it lists folders.

### What to do on rename / remove

- Same rule in reverse: update the `href`, the `<strong>` title / `<span>` subtitle, and decrement/increment the `stat-value` so the count stays true. Remove the `<li>` entirely on delete. Never leave a dead link.

### Verify before pushing

```powershell
# from internal/
Get-ChildItem out-html/ria/*.html, out-html/sequence-bridge/*.html | Select-Object Name
# count must match the two stat-values in out-html/index.html
Select-String -Path out-html/index.html -Pattern 'href="(ria|sequence-bridge)/.*\.html"'
```

Open `out-html/index.html` via `file://` and click every link — each must resolve.

Reference: full deploy + file-naming rules live in `out-html/README.md:16`.

---

## Rule 2 — Every deliverable page must have a back button to the index

Every file under `out-html/ria/*.html` and `out-html/sequence-bridge/*.html` must contain a visible, fixed-position link back to `../index.html`. The index itself (`out-html/index.html`) is the only page that does not have one.

### Required markup

Place the link as the first element inside `<body>`, before any header/wrap:

```html
<a class="back-button" href="../index.html">&larr; Back to Index</a>
<!-- variant that uses .btn styling on paper-themed pages: -->
<a class="btn back-button" href="../index.html">&larr; Back to Index</a>
<!-- variant inside a topbar (e.g. sequence-bridge/2026-07-28-...): -->
<a class="topbar-link" href="../index.html">&larr; Back to Index</a>
```

`href` must be `../index.html` (relative from one level deep). Do not use `/index.html`, `./index.html`, or an absolute URL — the site is served from `out-html/` as the publish root and must also work via `file://`.

### Required styling

Styling comes from the single stylesheet `out-html/shared.css` — do not duplicate `.back-button` per page. The canonical pill style in `shared.css:41` already overrides legacy per-page inline rules via `!important`:

```css
.back-button{
  position:fixed !important; top:18px !important; left:18px !important; z-index:50 !important;
  display:inline-flex !important; font-family:var(--mono) !important; font-size:.72rem !important;
  color:var(--ink) !important; background:var(--panel) !important;
  border:1px solid var(--rule-strong) !important; border-radius:999px !important;
  padding:.5rem .9rem !important; box-shadow:0 6px 18px rgba(0,0,0,.12) !important;
}
```

Legacy pages still carry their old inline `.back-button` blocks (paper/navy/minimal) — they are overridden by `shared.css` and should be removed when you next touch the file. New pages must not add a new inline `.back-button` rule; rely on `shared.css`.

Any equivalent that keeps the button fixed, legible, and above content is acceptable — the contract is **presence + correct href + fixed top-left**, not pixel-perfect parity.

### Additional constraints

- Hide the button in print: `@media print{ .back-button{ display:none !important; } }` (see existing pages for example).
- `favicon` from subfolders must be `href="../favicon.svg"` (index uses `href="favicon.svg"`).
- Do not add a back button that navigates via `history.back()` / JavaScript — it must be a plain `<a href="../index.html">` so it works without JS and from a fresh tab.

### Verify before pushing

```powershell
# every deliverable must contain href="../index.html"
Select-String -Path out-html/ria/*.html, out-html/sequence-bridge/*.html -Pattern 'href="\.\./index\.html"' | Format-Table Path
# the count of matches must equal the count of deliverable HTML files
@(Get-ChildItem out-html/ria/*.html, out-html/sequence-bridge/*.html).Count
```

Open any deliverable via `file://` and via the deployed URL — the back button must be visible on load and must land on the index.

---

## Rule 3 — Single clean style via `out-html/shared.css`

All pages (`out-html/index.html` + every file under `out-html/ria/*.html` and `out-html/sequence-bridge/*.html`) share one external stylesheet. This is option 1 from the clean-style consolidation — no per-page theme forks.

### How it is wired

- `out-html/index.html:12` → `<link rel="stylesheet" href="shared.css" />`
- Every deliverable → `<link rel="stylesheet" href="../shared.css" />` (one level deep, so `../`). Added to every file on 2026-08-26. Verify with:
  ```powershell
  Select-String -Path out-html/index.html -Pattern 'shared\.css'
  Select-String -Path out-html/ria/*.html, out-html/sequence-bridge/*.html -Pattern 'shared\.css'
  ```

`shared.css:1-113` is the source of truth for the clean neutral palette (`--bg:#f2f2f0`, `--panel:#fff`, `--ink:#1a1a19`, `--accent:#1f4a40`), type scale (`--sans` / `--serif` / `--mono`), index layout (`.wrap`, `.sheet`, `.initiative-grid`, `.document-list`), and the canonical `.back-button` + print rules. Do not add a new `<style>` block that redefines these — extend `shared.css` instead.

### What to do on a new page

1. In `<head>`, after the `<title>` / favicon, add:
   ```html
   <link rel="stylesheet" href="../shared.css" />
   <link rel="icon" type="image/svg+xml" href="../favicon.svg" />
   ```
   (index uses `href="shared.css"` / `href="favicon.svg"` without `../`).
2. Reuse the classes from `shared.css` (`.wrap`, `.sheet`, `.kicker`, `h1`, `.lede`, `.section`, `table`, `.callout`) for structure. Only add inline `<style>` for content that truly cannot be expressed in the shared sheet (e.g. SVG diagram colors, interactive quiz JS styles) — and scope it narrowly.
3. Do not copy a legacy per-page theme (paper/navy/wine) into a new file — new files must inherit the clean `shared.css` look. If a legacy page is edited, move any reusable style it introduces into `shared.css` and delete the inline duplicate.

### Verify before pushing

```powershell
# no page should be missing the shared sheet
@(Get-ChildItem out-html/ria/*.html, out-html/sequence-bridge/*.html | Where-Object { -not (Select-String -Quiet -Path $_.FullName -Pattern 'shared\.css') }).Count  # must be 0
# index must still load file:// and via Netlify — sheet is inside out-html/ so both work
```

---

## Checklist — adding a new HTML deliverable

- [ ] Markdown source exists in `out/` with matching date/slug
- [ ] `out-html/<venture>/YYYY-MM-DD-<slug>.html` created, self-contained, `href="../shared.css"` + `href="../favicon.svg"`
- [ ] Back button present (`href="../index.html"`, fixed top-left, hidden in print — styling from `shared.css`)
- [ ] `out-html/index.html` — new `<li><a href="...">` under correct card, reverse-chron order
- [ ] `out-html/index.html` — bumped `stat-value` for that venture (count == files on disk)
- [ ] `file://` click-through tested: index → new page → Back to Index → index
- [ ] `git add -A && git commit && git push origin main` from `internal/` (Netlify deploys in ~1–2 min)

## Notes

- Do not re-create `out-html/.git` — the nested `h-garcia/go-agency` repo was collapsed into `sequence-bridge/internal` on 2026-08-25. If it exists locally, delete it.
- Keep filenames date-prefixed and identical to their markdown source so staleness is obvious.
- For design-token or brand-manual changes, the canonical is now `marketing/public/tokens.json` — see `internal/README.md:49`.
