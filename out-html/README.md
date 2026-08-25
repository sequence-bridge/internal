# out-html — how this gets updated

This folder is a self-contained static site, deployed to Netlify. It holds the HTML versions of deliverables from `out/` — one page per document, organized by venture.

## Structure

```
out-html/
├── index.html              # landing page, links every deliverable below
├── favicon.svg
├── netlify.toml             # publish = "." — no build step
├── ria/                     # Riamatic deliverables
└── sequence-bridge/         # Sequence Bridge deliverables
```

## Adding a new page

Per the standing instruction (`~/.claude/instructions/llm-wiki.md`): whenever a markdown deliverable in `out/` is requested as HTML,

1. Create `out-html/<venture>/<YYYY-MM-DD>-<slug>.html`, matching the markdown filename but with `.html`.
2. Add a `<li><a href="...">` entry for it under the right venture card in `index.html`, and bump that card's "Working documents" stat count.
3. Commit and push — see Deploy below.

There's no build step (`netlify.toml` just publishes the folder as-is), so a page only needs to be valid, self-contained HTML.

## Deploy

This folder is **not** a separate repo — it deploys as part of `https://github.com/sequence-bridge/internal` (`main` branch, repo root is `internal/`). Netlify is connected to that repo with `publish = "out-html"` (`internal/netlify.toml:3`) and auto-builds on every push — there's no Netlify CLI or local site link (`.netlify/` is gitignored).

The old standalone repo `h-garcia/go-agency` (`out-html/.git` with `publish = "."`) was collapsed into `sequence-bridge/internal` on 2026-08-25. If you still see `out-html/.git` locally, delete it.

So "deploy" just means (from `internal/`):

```
git add -A
git commit -m "..."
git push origin main
```

Netlify picks it up within a minute or two of the push landing on `main`.

## Notes

- `out/` (the markdown sources) lives alongside this folder at `internal/out/` — this folder only holds the rendered HTML.
- Keep filenames date-prefixed and matched to their markdown source so it's obvious what's stale if the source changes.
