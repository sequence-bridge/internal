# sbridge_internal

Internal workspace for Sequence Bridge — steering and the static HTML deliverables that deploy to Netlify. This repo is the **source of truth** for *what* we build and *why*; `app/` and `marketing/` are separate repos that consume from it.

## Repository layout on disk

The parent folder `sequence bridge/` is a **container, not a git repo**. It holds three sibling repos:

```
sequence bridge/                 ← container (no .git)
├── internal/                    ← this repo (sbridge_internal) — https://github.com/sequence-bridge/internal
│   ├── netlify.toml             ← publish = "out-html"
│   ├── steering/                ← product, scope, flows, roadmap, tech-stack
│   │   └── sequence-bridge.md   ← origin narrative (was sequence-bridge.md at container root)
│   ├── out/                     ← rendered outputs (flows.html, token-directions.html, scope.md, …)
│   ├── out-html/                ← static HTML deliverables — **this is what Netlify deploys**
│   ├── .gitignore
│   └── README.md
├── app/                         ← separate repo — multi-tenant platform
│   └── themes/                  ← vendored copy of marketing/public/tokens.json + per-client deltas
└── marketing/                   ← separate repo — static marketing site — **now canonical for tokens + brand manual**
    ├── public/tokens.json       ← single source of truth (W3C DTCG), published at https://<marketing-domain>/tokens.json
    └── public/brand/            ← brand manual (design.md + specimens), published at https://<marketing-domain>/brand/
```

> **2026-08-25:** `CLAUDE.md`, `design/`, `specs/`, `qa/`, `releases/`, `content/`, `ai-reviews/` were removed from this repo — docs-only scaffolding is no longer tracked here. Design tokens / brand manual canonical is now `marketing/public/tokens.json` + `marketing/public/brand/` (see Design tokens section).

Previously `sequence bridge/` was a single repo and `internal/out-html/` was a **nested** repo (`h-garcia/go-agency`) with its own `netlify.toml` (`publish = "."`). That nesting is now collapsed: `out-html/.git` was removed and history was folded into this repo. If you still see `internal/out-html/.git` locally, delete it — it is no longer used.

## What deploys to Netlify

**Only `out-html/` deploys.** Nothing else in this repo is published.

- **Config:** `netlify.toml:3` (repo root is `internal/`) — `publish = "out-html"` (no build command, no base directory). `out-html/netlify.toml` (`publish = "."`) is kept as a fallback but the root `netlify.toml` is authoritative.
- **Dashboard:** Point Netlify at `sequence-bridge/internal` (`main` branch). Build settings: *Base directory* empty, *Publish directory* `out-html`, *Build command* empty. Alternatively set *Base directory* `out-html` and keep `publish = "."` inside `out-html/netlify.toml` — both work, don't set both.
- **Deploy trigger:** `git push` to `main` on `sequence-bridge/internal` deploys within 1–2 minutes. No Netlify CLI or `.netlify/` linkage required (`.netlify/` is gitignored).
- **Local preview:** Open `out-html/index.html` or any file in `out-html/ria/` / `out-html/sequence-bridge/` via `file://` — all pages are self-contained static HTML.

If Netlify is still connected to `h-garcia/go-agency`, disconnect it after verifying the new site serves `out-html/index.html` correctly — otherwise pushes to the old repo will keep deploying stale content.

## How the three repos are connected

They are **independent git repos** with no shared history. The only contract between them is the design system:

1. `app` and `marketing` are standalone — run `git init` inside each (already scaffolded with `README.md`) and push to their own GitHub remotes. The container `sequence bridge/` stays unversioned; do not `git init` at the container root.
2. `sequence-bridge.md` (origin narrative) was moved from the container root into `internal/steering/sequence-bridge.md` so it is versioned with scope. The old `readme.md` at the container root and `app/README.md` / `marketing/README.md` are **not** tracked here — they live in their respective repos.
3. Do not add `app/` or `marketing/` as submodules of `internal` — that would re-nest repos and reintroduce the `adding embedded git repository` warning. Keep them as siblings.

## Design tokens — does cross-repo sharing still work? (updated 2026-08-25)

**Short answer:** Yes — via a published URL, not a relative path. The file can no longer be imported across repos, so the mechanism changed but the intent (share tokens, not components) is unchanged.

**New canonical:** `marketing` now owns base tokens and the brand manual. `internal/design/` was removed from this repo on 2026-08-25; its last snapshot is in git history before `ef78dd8` — use `marketing/public/tokens.json` going forward.

| Asset | Canonical path (now) | Target in `app` | Published URL |
|---|---|---|---|
| `tokens.json` | `marketing/public/tokens.json` (W3C DTCG) | `app/themes/tokens.json` (vendored copy) | `https://<marketing-domain>/tokens.json` (or `https://raw.githubusercontent.com/<org>/marketing/<sha>/public/tokens.json` for pinned pulls) |
| Brand manual | `marketing/public/brand/` + `src/pages/brand/` | — | `https://<marketing-domain>/brand/` |

When this was a monorepo, `app` could import `../internal/design/tokens.json` directly. As separate repos, that relative import breaks — `marketing` is not on `app`'s filesystem in CI/checkout.

**Options:**

1. **Publish without npm — marketing hosts, app pulls manually (Accepted 2026-08-25):** Tokens and brand manual are static assets on the marketing site. When you change `marketing/public/tokens.json`, notify `app` maintainer to run `curl -o app/themes/tokens.json https://<marketing-domain>/tokens.json` (or the pinned raw URL) and commit the result. No registry, no build-time network dependency if the file is vendored. See `marketing/internal/steering/design.md` for the authoritative decision and paths. This saves the npm route entirely, as requested.
2. **Git submodule (rejected for now):** `git -C app submodule add <marketing-url> vendor/marketing` — versioned but overhead for a manual trigger.
3. **Published npm package (rejected):** `@go-agency/tokens` — correct at scale but unnecessary for two repos; explicitly avoided per your constraint.
4. **Build-time fetch at SHA (fallback if marketing site not yet live):** `app` curls `raw.githubusercontent.com/.../marketing/<sha>/public/tokens.json`. Same manual trigger, uses GitHub as host.

**Recommendation:** Use (1). Keep `app/themes/tokens.json` committed (vendored) after each pull — do not fetch at runtime. Do not copy `elements.html` / `components.html` across repos — components are per-surface; only tokens are shared.

## Working in this repo

- Steering lives in `steering/` (`product.md`, `roadmap.md`, `tech-stack.md`, `sequence-bridge.md`).
- Deliverables live in `out/` (markdown sources) and `out-html/` (deployed HTML). To add a page, follow `out-html/README.md:16`.
- `internal/README.md` previously described the `internal/` folder map when `internal` was nested. That description now lives here at the repo root — do not recreate `internal/README.md` inside itself.

## Migration notes (for history)

- `2026-08-25` (3): Removed `CLAUDE.md`, `design/`, `specs/`, `qa/`, `releases/`, `content/`, `ai-reviews/` from `sequence-bridge/internal` (`ef78dd8`); repo now tracks only `steering/`, `out/`, `out-html/`, `netlify.toml`, `README.md`. Design canonical remains `marketing/public/tokens.json`.
- `2026-08-25` (2): Scaffolded `marketing/internal/` (product, roadmap, design, tech-stack), moved canonical tokens + brand manual to `marketing/public/tokens.json` + `public/brand/` per `marketing/internal/steering/design.md` Option 1 (publish without npm, manual pull), updated this README to reflect new canonical.
- `2026-08-25` (1): Collapsed `internal/out-html/.git` (h-garcia/go-agency) into this repo, moved `sequence bridge/.git` → `internal/.git`, added `netlify.toml` at repo root (`publish = "out-html"`), created this README, initialized `app/` and `marketing/` as independent repos.
