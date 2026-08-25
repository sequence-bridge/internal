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

This folder is its own git repo, pushed to `https://github.com/h-garcia/go-agency` (`main` branch). Netlify is connected to that GitHub repo and auto-builds on every push — there's no Netlify CLI or local site link (`.netlify/` is gitignored and unused here).

So "deploy" just means:

```
git add -A
git commit -m "..."
git push origin main
```

Netlify picks it up within a minute or two of the push landing on `main`.

## Notes

- `raw/` and `out/` (the markdown sources) live one level up, outside this repo — this folder only holds the rendered HTML.
- Keep filenames date-prefixed and matched to their markdown source so it's obvious what's stale if the source changes.
