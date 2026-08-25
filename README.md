# sbridge_internal

Internal workspace for Sequence Bridge — steering, design system, specs, and the static HTML deliverables that deploy to Netlify. This repo is the **source of truth** for *what* we build and *why*; `app/` and `marketing/` are separate repos that consume from it.

## Repository layout on disk

The parent folder `sequence bridge/` is a **container, not a git repo**. It holds three sibling repos:

```
sequence bridge/                 ← container (no .git)
├── internal/                    ← this repo (sbridge_internal)
│   ├── netlify.toml             ← publish = "out-html"
│   ├── CLAUDE.md                ← workspace rules (load-bearing)
│   ├── design/                  ← canonical design system
│   │   ├── design.md            ← consolidated source (checklist + decisions)
│   │   ├── tokens.json          ← W3C DTCG tokens — single source of truth
│   │   ├── elements.html        ← atoms (browser-viewable)
│   │   ├── components.html      ← composites
│   │   └── sections.html        ← page sections
│   ├── steering/                ← product, scope, flows, roadmap, tech-stack
│   ├── out/                     ← rendered outputs (flows.html, token-directions.html, scope.md, …)
│   ├── out-html/                ← static HTML deliverables — **this is what Netlify deploys**
│   ├── specs/, qa/, releases/, content/, ai-reviews/
│   └── steering/sequence-bridge.md ← origin narrative (was sequence-bridge.md at container root)
├── app/                         ← separate repo — multi-tenant platform
│   └── themes/                  ← per-client token deltas (future home for tokens.json)
└── marketing/                   ← separate repo — static marketing site
```

Previously `sequence bridge/` was a single repo and `internal/out-html/` was a **nested** repo (`h-garcia/go-agency`) with its own `netlify.toml` (`publish = "."`). That nesting is now collapsed: `out-html/.git` was removed and history was folded into this repo. If you still see `internal/out-html/.git` locally, delete it — it is no longer used.

## What deploys to Netlify

**Only `out-html/` deploys.** Nothing else in this repo is published.

- **Config:** `internal/netlify.toml:3` — `publish = "out-html"` (no build command, no base directory). The old `internal/out-html/netlify.toml` (`publish = "."`) is kept as a fallback but the root `netlify.toml` is authoritative when the repo root is `internal/`.
- **Dashboard:** Point Netlify at `sbridge_internal` (new GitHub repo), **not** `h-garcia/go-agency`. Build settings: *Base directory* empty, *Publish directory* `out-html`, *Build command* empty. Alternatively set *Base directory* `out-html` and keep `publish = "."` inside `out-html/netlify.toml` — both work, don't set both.
- **Deploy trigger:** `git push` to `main` on `sbridge_internal` deploys within 1–2 minutes. No Netlify CLI or `.netlify/` linkage required (`.netlify/` is gitignored).
- **Local preview:** Open `out-html/index.html` or any file in `out-html/ria/` / `out-html/sequence-bridge/` via `file://` — all pages are self-contained static HTML.

If Netlify is still connected to `h-garcia/go-agency`, disconnect it after verifying the new site serves `out-html/index.html` correctly — otherwise pushes to the old repo will keep deploying stale content.

## How the three repos are connected

They are **independent git repos** with no shared history. The only contract between them is the design system:

1. `app` and `marketing` are standalone — run `git init` inside each (already scaffolded with `README.md`) and push to their own GitHub remotes. The container `sequence bridge/` stays unversioned; do not `git init` at the container root.
2. `sequence-bridge.md` (origin narrative) was moved from the container root into `internal/steering/sequence-bridge.md` so it is versioned with scope. The old `readme.md` at the container root and `app/README.md` / `marketing/README.md` are **not** tracked here — they live in their respective repos.
3. Do not add `app/` or `marketing/` as submodules of `internal` — that would re-nest repos and reintroduce the `adding embedded git repository` warning. Keep them as siblings.

## Design tokens — does cross-repo sharing still work?

**Short answer:** The *file* can no longer be imported by relative path, so you need an explicit sharing mechanism. The *design intent* is unchanged.

Per `design/design.md:38`, the locations table is authoritative:

| Asset | Canonical path (now) | Target |
|---|---|---|
| `tokens.json` | `internal/design/tokens.json` | `app/themes/tokens.json` |

This repo remains the canonical source; `app` and `marketing` consume it. When this was a monorepo, `app` could import `../internal/design/tokens.json` directly. As separate repos, that relative import breaks — `internal` is not on `app`'s filesystem in CI/checkout.

**Options (pick one, in order of preference):**

1. **Manual copy (current, simplest while scaffold-only):** When you change `design/tokens.json`, copy the file to `app/themes/tokens.json` and `marketing/` (or update the inline `@theme` blocks in `design/*.html` per `design/design.md:13` sync rule). No automation until a Tailwind pipeline exists. Works today because `app` and `marketing` are not yet built.
2. **Git submodule (cheap, versioned):** `git -C app submodule add <sbridge_internal-url> vendor/internal` and have the build read `vendor/internal/design/tokens.json`. Keeps a pinned version but adds submodule overhead.
3. **Published package (correct at scale):** Publish `tokens.json` as an npm package (`@go-agency/tokens`) or a private registry artifact. `app` and `marketing` depend on it. The inline `@theme` blocks in `design/*.html` get replaced by compiled CSS once the Tailwind pipeline exists (`design/design.md:13`).
4. **Build-time fetch:** `app`'s build step curls `raw.githubusercontent.com/.../design/tokens.json` at a pinned commit. No submodule, but network-dependent.

**Recommendation:** Stay on (1) until `app` has a real Tailwind build pipeline. At that point, move to (3) and delete the manual `@theme` sync rule. Do not copy `elements.html` / `components.html` across repos — per `design/design.md:58`, components are per-surface; only tokens are shared.

## Working in this repo

- Read `CLAUDE.md` before any steering or spec work — it defines the `internal/` lifecycle (`roadmap → branch → spec → implement → adversarial review → push → PR → merge`) and the `internal-scaffolding` skill workflow.
- `steering/design.md` is a thin pointer; the canonical design doc is `design/design.md`.
- `internal/README.md` previously described the `internal/` folder map when `internal` was nested. That description now lives here at the repo root — do not recreate `internal/README.md` inside itself.

## Migration notes (for history)

- `2026-08-25`: Collapsed `internal/out-html/.git` (h-garcia/go-agency) into this repo, moved `sequence bridge/.git` → `internal/.git`, added `netlify.toml` at repo root (`publish = "out-html"`), created this README, initialized `app/` and `marketing/` as independent repos.
