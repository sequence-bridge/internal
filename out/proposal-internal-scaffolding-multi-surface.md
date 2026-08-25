# Proposal — Multi-surface update to the `internal-scaffolding` skill

**Source:** `internal/design/design.md` + `internal/CLAUDE.md` + current skill `SKILL.md`
**Date:** 2026-08-25
**Status:** draft for approval — no files moved yet
**Trigger:** `app/` and `marketing/` will each become their own project with their own `internal/` and independent tech stack. Three surfaces, one shared token foundation.

---

## 1. Why the skill must change

The current skill (`internal-scaffolding` v1, `SKILL.md:1-149`) assumes:

- one repo → one `internal/` → one app → one `tech-stack.md` → one `roadmap.md` → one `steering/design.md` → one `releases/CHANGELOG.md`.
- design assets live "in the base application alongside consumers" (`templates/CLAUDE.md:40-49`, `SKILL.md:59`).

`internal/design/design.md:24-38` already contradicts that model and documents the break, with local workarounds in `internal/CLAUDE.md`:

- **Three consumers, not one** (design.md:25-33): `marketing/` (static brand site, persuasion components), `app/` (dense workhorse UI: dashboard + lead DB + SMS), `app/themes/` (per-client token deltas for tenant landing pages — a platform feature, not marketing content).
- **One shared token foundation on top of two independent stacks** (design.md:60-65, 51-70): "Share the tokens, not the components" / "Per-surface component layers" / "Themeable from day one".
- **Transitional location for the system** (design.md:42-46, 196-203): `internal/design/tokens.json` is canonical *today* but its target is `app/themes/tokens.json` alongside consumers; `elements.html/components.html/sections.html` are browser-viewable stand-ins with a manual `@theme` sync rule until the app has a real Tailwind pipeline. `internal/CLAUDE.md` codifies `steering/design.md` as a thin pointer to avoid duplication.
- **Two independent lifecycles coming** (user constraint): `app/` and `marketing/` will each carry their own `internal/` with an independent `tech-stack.md`, their own roadmap, specs, QA, and releases. The skill has no concept of this.

If we do nothing, the next spec will be ambiguous about where it lives, which `tech-stack.md` it must respect, which `roadmap.md` it advances, and which `CHANGELOG.md` it logs to — the exact drift `internal/` was built to prevent.

---

## 2. What `design.md` already decided — the skill must codify, not revisit

| Principle (design.md:60-69) | Implication for the scaffold |
|---|---|
| Share the tokens, not the components | The skill must treat `tokens.json` as a **shared contract** (W3C DTCG, two-layer `palette` → `color`, `space/radius/font/shadow/motion`) and treat `elements/components/sections` as **per-surface**. Rejected: "one component library for both marketing and app" (design.md:204). |
| Per-surface component layers | Marketing gets persuasion components (hero/pricing/testimonials/FAQ/CTA); app gets workhorse UI (tables/stat tiles/forms/filters). Skill must not force one surface's checklist onto the other. |
| Themeable from day one + Client themes live in the app | Brand theme is one theme among many. Per-client deltas live strictly in `app/themes/<slug>.json` (design.md:37, 64; `app/themes/README.md:3-7`). Marketing never owns a tenant theme. |
| Tokens drive everything (semantic, not palette) | Skill's design coverage checks must verify that specimens consume semantic tokens (`primary`, `surface`, `fg`), not raw `palette.*` steps. |
| Browser-viewable without a build + manual sync until pipeline | Skill must preserve the transitional workflow: edit `tokens.json` → hand-update `@theme` in all three HTML files → flip checkbox in `design.md`. And name the exit condition (real Tailwind pipeline → replace CDN + inline `@theme` with compiled CSS). |
| Aesthetic duality | Dark dense dashboard vs light conversion pages. Direction 3 (Thread — wine/apricot/brick, Sentient/Switzer/Space Mono in `internal/out/flows.html` + `token-directions.html`) is **exploration only**; tokens still reflect round-one scaffold (design.md:54, 200). Skill must not auto-promote direction 3. |

Specimen checklist in `design.md:72-192` is the **ideal vs captured** tracker (`[ ]`/`[~]`/`[x]`). The skill's steering coverage should read it, not duplicate it.

---

## 3. Proposed repo map — umbrella + two autonomous surfaces

Keep a thin **umbrella** `internal/` at the repo root for offer-level intent, and give each surface a full `internal/` of its own for delivery. No file is moved in this proposal; this is the target to migrate toward.

```
sequence bridge/                         # repo root — offer + shared design contract
├── internal/                            # UMBRELLA — offer-level truth, not per-surface delivery
│   ├── CLAUDE.md                        # umbrella rules (this map + shared vs per-surface table)
│   ├── steering/
│   │   ├── product.md                   # WHY — vision/strategy/bets (shared)
│   │   ├── scope.md                     # WHAT — authoritative in/out + pricing (versioned, shared)
│   │   ├── flows.md                     # HOW — Mermaid view of scope (always current, shared)
│   │   ├── roadmap.md                   # offer roadmap only — sequencing of offer capabilities (thin index)
│   │   └── design.md                    # pointer → design/design.md (skill slot, never canonical)
│   ├── design/                          # CANONICAL today, TRANSITIONAL — single source of visual identity
│   │   ├── design.md                    # canonical checklist + locations/aesthetic/principles/decisions
│   │   ├── tokens.json                  # W3C DTCG — shared foundation until promoted
│   │   ├── elements.html                # atoms — shared reference (consume semantic tokens)
│   │   ├── components.html              # composites — shared reference
│   │   └── sections.html                # page patterns — shared reference
│   ├── specs/                           # cross-cutting specs only (PMS handoff, scope changes, token contract changes)
│   ├── qa/                              # cross-cutting QA (rare — most QA lives per-surface)
│   ├── ai-reviews/                      # cross-cutting reviews
│   ├── out/                             # renderings (flows.html, product-definition, token-directions, this proposal)
│   └── releases/
│       ├── CHANGELOG.md                 # offer-level changelog (scope freezes, cross-cutting merges) + umbrella pushes
│       ├── roadmap-log.md               # archived offer roadmap headings
│       └── scope-v<N>.md                # frozen offer records — self-contained by rule (see CLAUDE.md:95-102)
│
├── app/                                 # AUTONOMOUS PROJECT — multi-tenant platform we own and operate
│   ├── internal/                        # app delivery — independent lifecycle
│   │   ├── CLAUDE.md                    # app workspace rules (inherits umbrella, adds app paths)
│   │   ├── steering/
│   │   │   ├── tech-stack.md            # app stack only (languages, components, conventions, TDD policy)
│   │   │   ├── design.md                # app pointer → app/themes/tokens.json + app/components/* (per-surface)
│   │   │   └── roadmap.md               # app delivery roadmap (monthly + backlog, app slices only)
│   │   ├── specs/                       # app specs — YYYY-MM-DD-<slug>.md (dashboard, booking, themes, SMS)
│   │   ├── qa/                          # app QA
│   │   ├── ai-reviews/                  # app reviews
│   │   ├── content/                     # NOT HERE — content belongs to marketing (see below)
│   │   └── releases/
│   │       ├── CHANGELOG.md             # app changelog (per push to app)
│   │       └── roadmap-log.md           # app archived roadmap
│   ├── themes/
│   │   ├── tokens.json                  # TARGET — promoted shared tokens (W3C DTCG) once pipeline exists
│   │   ├── <practice-slug>.json        # per-client token deltas (themeable contract, not component fork)
│   │   └── README.md
│   ├── components/                      # (future) app workhorse components — built on shared tokens
│   └── ...                              # app source (framework TBD in app/internal/steering/tech-stack.md)
│
├── marketing/                           # AUTONOMOUS PROJECT — static marketing site
│   ├── internal/                        # marketing delivery — independent lifecycle
│   │   ├── CLAUDE.md
│   │   ├── steering/
│   │   │   ├── tech-stack.md            # marketing stack only (e.g. Astro/Next static, CMS, hosting)
│   │   │   ├── design.md                # marketing pointer → consumed brand tokens + marketing/components/*
│   │   │   └── roadmap.md               # marketing roadmap (copy, SEO, persuasion sections)
│   │   ├── specs/                       # marketing specs
│   │   ├── qa/
│   │   ├── ai-reviews/
│   │   ├── content/                     # style.md + dated pieces (already exists at root — migrate here)
│   │   ├── video/                       # video-style.md + dated scripts (opt-in, per skill)
│   │   └── releases/
│   │       ├── CHANGELOG.md
│   │       └── roadmap-log.md
│   ├── components/                      # (future) persuasion components — built on shared tokens
│   ├── content/ or src/                 # site source
│   └── ...
│
└── packages/  (optional, later)         # only if/when sharing tokens as a package beats file sync
    └── tokens/                          # extracted @sequence-bridge/tokens from app/themes/tokens.json
```

**Why not three fully independent repos?** The offer (scope/flows/product/reporting metrics) and the token contract are single-company concerns. Splitting them into three repos makes `scope.md` vs `flows.md` vs `tokens.json` drift again — the contradiction that survived 2026-07-31→2026-08-10 (CLAUDE.md:80-83). Umbrella keeps the offer and the token shape in one place; per-surface `internal/` keeps the *delivery* independent.

**Why not keep one root `internal/` only?** One `tech-stack.md`, one `roadmap.md`, one `CHANGELOG.md` cannot describe two stacks with independent cadence, independent deploys, and independent conventions (formatter/linter/test runner/DB) without becoming a merge-conflict fiction.

---

## 4. Steering ownership — shared vs per-surface (load-bearing)

Proposed rule, to replace `SKILL.md:78-92` and `internal/CLAUDE.md:80-92`:

| File | Lives at | Owns | Must never contain |
|---|---|---|---|
| `product.md` | umbrella `internal/steering/` | WHY — vision/promise/strategy/bets/best-fit/differentiators/objectives | in/out or price |
| `scope.md` | umbrella | WHAT — authoritative in/out + pricing per offer version (self-contained, versioned via `scope-v<N>.md`) | — |
| `flows.md` | umbrella | HOW — Mermaid view of scope (never frozen, three layers on three clocks) | scope declarations not in `scope.md` |
| `roadmap.md` | **both** — umbrella + each surface | umbrella: offer sequencing (WHEN for capabilities); per-surface: delivery slices (WHEN for "dashboard v2", "pricing refresh") | per-surface roadmap may not declare offer in/out |
| `tech-stack.md` | **per-surface only** (`app/internal/steering/`, `marketing/internal/steering/`) | languages/components/conventions/TDD per stack | umbrella copy — there is none |
| `design.md` (steering slot) | umbrella = pointer → `design/design.md`; per-surface = pointer → consumed token path + component locations | umbrella: aesthetic/principles/decisions/checklist locations table; per-surface: where *that surface* keeps its components and which token source it consumes | duplication of the canonical checklist — `design/design.md` and later `app/themes/tokens.json` own the tokens/checklist |

Consequence: `SKILL.md:83-84` (four steering files) becomes six logical files but in two layers. The skill's "Steering coverage" check must read **umbrella** `product/scope/flows/design` plus **per-surface** `tech-stack`/`roadmap`/`design`, and report incomplete sections per layer.

Design-system specifics: `internal/design/design.md:40-48` location table stays authoritative during transition. When `app/themes/tokens.json` becomes the pipeline source, the table flips to `app/themes/tokens.json` as current, `marketing` consumes it (via file copy or package), and `internal/design/` shrinks to a frozen reference or is retired. The skill must enforce the existing `steering/design.md` pointer pattern at both umbrella and per-surface levels so the template slot is always satisfied.

---

## 5. What changes in the skill — section by section

### 5.1 Bootstrap (`SKILL.md:25-60`)

Current bootstrap creates one `internal/` from five templates and assumes one app path. Proposed:

- Ask surface question up front: **"Is this a single-project repo or a multi-surface monorepo (shared offer + `app/` + `marketing/`)?"** Default to multi-surface if `app/` or `marketing/` already exists (as it does here).
- If multi-surface:
  1. Create umbrella `internal/` from templates: `CLAUDE.md`, `product.md`, `roadmap.md`, plus a **new** `scope.md` and `flows.md` template (today those two are Sequence Bridge extensions not in `templates/` — needed for reuse).
  2. Create `internal/design/` with the five canonical files (`design.md`, `tokens.json`, `elements.html`, `components.html`, `sections.html`) — or leave existing ones intact (current scaffold already has them; never overwrite).
  3. For each surface (`app/`, `marketing/`), create `app/internal/` and `marketing/internal/` with **only** `CLAUDE.md` (scoped), `steering/tech-stack.md`, `steering/design.md`, `steering/roadmap.md`, and empty `specs/qa/ai-reviews/releases/`. Leave umbrella `product/scope/flows` absent from surfaces.
  4. Write the surface's `steering/design.md` as a pointer: app → `app/themes/tokens.json` (target) and marketing → consumed brand tokens. Record actual component paths when the stack is chosen.
  5. Add new template `templates/tech-stack.per-surface.md` note: marketing stacks are typically static (Astro/Next export + CDN) with no DB; app stacks include booking, Twilio, DB, auth.
- Rule to preserve: `internal/design/` is transitional; design-system source files belong beside consumers (`templates/CLAUDE.md:40-49`), not permanently in `internal/`. The skill already says this — keep it, but apply it per-surface.

### 5.2 Menu (`SKILL.md:62-75`)

Current six-item menu is single-project. Proposed multi-surface menu:

```
What would you like to do?
  1. Check steering coverage  [default]  → picks surface (umbrella / app / marketing / all)
  2. Specify a roadmap item           → picks surface, then creates branch
  3. Continue an approved delivery stage → picks surface, then stage
  4. Log QA feedback / derive a fix spec → picks surface where bug was observed
  5. Archive completed roadmap items  → picks surface + month
  6. Marketing content or video       → always in marketing/internal/content|video
  7. Promote design tokens            → umbrella: promote internal/design/tokens.json → app/themes/tokens.json
```

If the user's request names a surface ("spec the pricing page"), route directly without menu.

### 5.3 Steering coverage (`SKILL.md:77-92`)

Expand the four-file check to a **layered** check:

- **Umbrella** (always): `product.md`, `scope.md`, `flows.md`, `roadmap.md`, `design.md` (pointer), `design/design.md` + `tokens.json` + three HTML.
- **Per-surface** (each surface that exists): `tech-stack.md`, `roadmap.md`, `design.md` (pointer), `releases/CHANGELOG.md` presence.

Report as two tables: umbrella coverage and per-surface coverage. Interview still one document at a time.

Add explicit scope-ownership table (from `internal/CLAUDE.md:80-92`) into the skill's `templates/CLAUDE.md` and `SKILL.md` so the four-way WHY/WHAT/HOW/WHEN split survives beyond this repo.

### 5.4 Specify + Delivery stages (`SKILL.md:93-117`)

- **Branch naming:** prefix with surface — `app/booking-…`, `mktg/pricing-…`, `offer/scope-v1.1-…`. Cross-cutting token changes use `design/tokens-…` at umbrella.
- **Spec location:** cross-cutting/offer specs → `internal/specs/`; app work → `app/internal/specs/`; marketing work → `marketing/internal/specs/`. Spec template (`templates/specs.md`) gains a `Surface:` field (`umbrella | app | marketing`) and a `Consumes:` token field for design work.
- **Steering reads before spec:** per-surface specs must read umbrella `product/scope/flows` plus their own `tech-stack/design/roadmap`. Enforce in interview round (a).
- **Approval gates unchanged** — still `Roadmap item → new branch → spec → implement → adversarial review → push, PR, merge` with explicit approval per transition — but **push/PR/merge target the surface**. An app PR must not require a marketing approval.
- **Adversarial review output** stays `YYYY-MM-DD-<branch>.md` with `**Type:** Review` under title, but saved under the surface's `ai-reviews/` (umbrella reviews under umbrella). Needed for per-surface `CHANGELOG.md`.
- **Implement notes:** design specs that change `tokens.json` must name the sync: update `tokens.json` + `@theme` blocks in all three HTML files (manual until pipeline) and flip checkbox in `design/design.md`. When pipeline exists, update `app/themes/tokens.json` and let the build replace the CDN block.

### 5.5 Roadmap format + Archive (`SKILL.md:127-129`, `internal/CLAUDE.md:154-171`)

- Each `roadmap.md` (umbrella + per-surface) keeps the same monthly heading + `## Backlog` format, two-sentence max per item, with a pointer to its spec/QA/review/PR.
- Umbrella roadmap tracks offer capabilities (e.g., "Book v1 call capture — tracking number + missed-call text-back"). Per-surface roadmaps track **delivery slices** for that stack (e.g., `app: warm-transfer edge + IVR`, `marketing: pricing 3-tier section`).
- Archive command takes a surface argument: `archive 2026-08 app` vs `archive 2026-08 marketing` vs `archive 2026-08 umbrella`. Moves only checked items for that month/surface into that surface's `releases/roadmap-log.md`.

### 5.6 Changelog on push (`SKILL.md:147-148`, `internal/CLAUDE.md:175-188`)

- Each `internal/` maintains its own `releases/CHANGELOG.md`. The umbrella changelog tracks offer-level merges + cross-cutting pushes; each surface changelog tracks that surface's pushes.
- Push rule: before an approved push of a surface branch, prepend to **that surface's** `CHANGELOG.md` (not the umbrella's), commit `chore: update changelog`, then push both commits. Skip only when changelog-only or docs-only inside that surface's `internal/`.
- Offer-version freezes (`scope-v<N>.md`) stay exclusively in umbrella `releases/`; per-surface releases never freeze scope.

### 5.7 Marketing content and video (`SKILL.md:132-145`)

- Move from umbrella `internal/content/` (`internal/CLAUDE.md:45`) to **`marketing/internal/content/`** and `marketing/internal/video/`. Umbrella `internal/content/style.md` migrates to `marketing/internal/content/style.md` with a deprecation note at the umbrella path.
- Skill must read `marketing/internal/content/style.md` (Voice anchors, Tone-by-context, Word swaps, House vocabulary) before drafting — the checklist in `internal/CLAUDE.md:140` stays, but path changes.
- Ask-before-create rule preserved: don't create `content/` or `video/` during bootstrap; prompt when user requests marketing work.

### 5.8 Design system (`templates/CLAUDE.md:40-49`, `SKILL.md:59`, `templates/design.md`)

- New template `templates/design.per-surface.md`:
  ```
  | Asset | Path | Notes |
  |---|---|---|
  | Consumed tokens | <path to shared tokens.json or package> |
  | Elements file   | <surface>/… | Atoms for this surface only |
  | Components      | <surface>/… | Composites for this surface only |
  | Sections        | <surface>/… | Page patterns for this surface only |
  ```
- Umbrella `design/design.md` template retains the canonical checklist (`Tokens/Elements/Components/Sections` with `[ ]/[~]/[x]`) and the `Repository surfaces` + `Design-system locations` + `Principles` tables. Per-surface `steering/design.md` is strictly a pointer — never the checklist.
- Skill's "Design-system source files live in the base app" sentence becomes "…live **per surface** alongside their consumers" and must name both `app/` and `marketing/` explicitly.
- Preserve the two-layer `palette → semantic` rule and the W3C DTCG `{$value,$type}` contract in the tokens template. Flag any per-surface token fork that bypasses semantic aliases as a review P1.

### 5.9 New templates to add

- `templates/scope.md` and `templates/flows.md` (today Sequence Bridge extensions baked into `internal/CLAUDE.md:95-105`). Generalize them: `scope.md` with In/Out/Constraints/Pricing/Responsibilities/Open questions + version history; `flows.md` with three Mermaid layers + gap markers + open questions list. Without these, bootstrapping a second multi-surface repo copies Sequence Bridge specifics.
- `templates/design.md` → split into `templates/design.umbrella.md` (canonical + checklist) vs `templates/design.per-surface.md` (pointer only).

---

## 6. Token promotion — the skill's new explicit workflow

This is the transitional seam `internal/design/design.md:40-46` leaves open. The skill should name the workflow to avoid drift:

**Today (manual, no pipeline):**
```
internal/design/tokens.json  (W3C DTCG, canonical)
   ↓ hand-copy @theme
internal/design/elements.html | components.html | sections.html  (Tailwind CDN + inline @theme)
   ↓ consumed by reference
marketing/  → reads brand values from internal/design/tokens.json (no build)
app/        → reads brand shape; app/themes/<slug>.json are deltas (per-client theme)
```

**At pipeline (promotion):**
1. Create `app/themes/tokens.json` from `internal/design/tokens.json` (brand theme as one theme). Verify W3C DTCG validity and `palette.*` → `color.*` aliases.
2. Give `app/` a real Tailwind build — replace `https://unpkg.com/@tailwindcss/browser@4` + inline `@theme` with compiled CSS in `app/`. Keep the three HTML files as reference renderings but sourced from the built CSS.
3. Make `marketing/` consume the same token source — either a file copy at build or a workspace package (`@sequence-bridge/tokens`). Skill prompts which; default is file copy until a monorepo package justifies itself.
4. Deprecate `internal/design/tokens.json` to a pointer + frozen reference (or remove after one release). Update both umbrella `design/design.md` location table and each surface's `steering/design.md` pointer. Keep `steering/design.md` delegation intact.
5. Per-client themes (`app/themes/<slug>.json`) remain token deltas, not component forks. Skill review must reject any tenant theme that introduces new components.

The skill's `tokens.json` sync rule ("update `@theme` in all three HTML files by hand") should live in the umbrella `CLAUDE.md` and survive promotion as a fallback until the pipeline is proven — not disappear the day the pipeline is added.

---

## 7. Filename conventions and immutability — per-surface extension

Current conventions (`internal/CLAUDE.md:66-72`) stay, but scoped:

- `specs/`, `qa/`, `ai-reviews/`, `content/`, `video/` files are `YYYY-MM-DD-<slug>.md` within **their surface's** `internal/`. Cross-cutting files keep `internal/` at umbrella.
- Same-day collisions still use `-2`, `-3`.
- Shipped specs are immutable per surface; a later iteration gets a new dated file in that surface's `specs/`.
- `releases/CHANGELOG.md` and `releases/roadmap-log.md` are fixed names **per `internal/`** (so three of each in the full layout).

---

## 8. Migration plan — no-bump, phased

No scope version bump; this rehouses scaffolding, not the offer.

**Phase 0 — Approve this proposal (now).** No file moved, no `git mv`. Mark spec decisions as accepted once reviewed.

**Phase 1 — Skill patch (umbrella).**
- Patch `SKILL.md` and `templates/CLAUDE.md` / `templates/design.md` in this repo's `internal/` to the layered wording above (Bootstrap, Menu, Steering coverage, per-surface changelog, content path). Keep `internal/design/` canonical for now.
- Generalize `internal/CLAUDE.md`'s Sequence Bridge extensions (scope ownership table, freeze/self-contained rules, design system section) into skill templates so the next repo doesn't re-learn them by copying.

**Phase 2 — Spawn per-surface `internal/`.**
- Create `app/internal/` and `marketing/internal/` with their own `CLAUDE.md`, `steering/tech-stack.md`, `steering/design.md` (pointers), `steering/roadmap.md`, `specs/qa/ai-reviews/releases/`.
- Move no history: leave umbrella specs/QA/reviews in place; new per-surface specs start in their surfaces. Optionally leave `MIGRATED.md` pointers at umbrella if a spec is re-issued per-surface.

**Phase 3 — Relocate content.**
- Move `internal/content/` → `marketing/internal/content/` (git mv + pointer `internal/content/README.md` → "migrated to marketing/internal/content").

**Phase 4 — Token promotion (decoupled, when app pipeline lands).**
- Promote `internal/design/tokens.json` → `app/themes/tokens.json`, wire Tailwind build, make `marketing/` consume the shared source. Retire the manual `@theme` sync only after both surfaces build cleanly.

Each phase is its own branch + spec + review; none rewrites frozen `scope-v<N>.md`.

---

## 9. Open questions for this proposal

1. **Content/video home:** confirm `marketing/internal/content/` + `marketing/internal/video/` is the right final home vs `marketing/content/` alongside site source. Proposal picks the former to keep durable intent inside `internal/`.
2. **Roadmap granularity:** should umbrella `roadmap.md` remain thethin offer sequencer plus per-surface delivery roadmaps, or should the umbrella roadmap be retired entirely once per-surface roadmaps exist? Proposal keeps umbrella as the offer index (its archive feeds `scope` history) and lets per-surface roadmaps own delivery — subject to a one-month trial.
3. **Changelog audience:** three `CHANGELOG.md` files (umbrella + app + marketing) vs a single root changelog with surface-grouped headings. Proposal keeps three to match independent deploys and `gh` per-project release notes.
4. **Package extraction:** at what point does `packages/tokens` beat file sync? Proposal's heuristic: extract a package only when marketing and app are on different release cadences and a token change needs version pinning; until then, file copy is enough.
5. **Scope/flows templates:** should `templates/scope.md` and `templates/flows.md` be strict copies of Sequence Bridge's structure (In/Out/Constraints/Pricing/Responsibilities/Open questions + three Mermaid layers) or lighter placeholders? Proposal leans exact — that structure caught the missed-call contradiction.

---

## 10. What this proposal does not change

- Offer versioning and the scope freeze (`scope.md` self-contained, `flows.md` never frozen) — untouched, just restated per surface.
- Spec sections (`Summary/User value/Requirements/Out of scope/Acceptance criteria/Open questions`) — untouched, just given a `Surface:` prefix.
- Delivery gates (`branch → spec → implement → adversarial review → push/PR/merge` with explicit approval) — unchanged, just surfaced per project.
- The `[ ]/[~]/[x]` specimen tracker — stays in `design/design.md`, per surface component checklists remain separate.

---

## 11. Minimal diff sketch (what a skill PR would touch)

```
SKILL.md:                          Bootstrap → multi-surface question; Menu → 7 items;
                                   Steering coverage → umbrella + per-surface tables;
                                   Specify/Delivery → Surface: field + per-surface paths;
                                   Archive/Changelog → surface argument + per-surface CHANGELOG;
                                   Content → marketing/internal/content;
                                   Design → per-surface lives beside consumers

templates/CLAUDE.md:               Folder map → umbrella + app/internal + marketing/internal
                                   Design → per-surface; Changelog → per-surface; Roadmap → per-surface

templates/design.md → templates/design.umbrella.md   (canonical checklist + locations + principles)
                +   → templates/design.per-surface.md  (pointer only)

templates/scope.md  (new)          In/Out/Constraints/Pricing/Responsibilities/Open Qs + version history
templates/flows.md  (new)          Three Mermaid layers + gap markers + open Qs (gap-aware)
templates/tech-stack.per-surface.md (new)  App vs marketing notes

internal/CLAUDE.md:                Folder map + Delivery lifecycle + Filename conventions + Roadmap format
                                   updated to layered wording (no behavior change until per-surface dirs exist)
internal/design/design.md:        No content change — remains canonical until Phase 4 promotion
app/themes/README.md:              Unchanged — already names <practice-slug>.json deltas
```

---

## 12. Acceptance

- [ ] Umbrella vs per-surface split accepted (or "fully independent repos" chosen instead)
- [ ] Token promotion workflow accepted (manual `@theme` until pipeline, then `app/themes/tokens.json` + package decision)
- [ ] Roadmap/changelog per-surface accepted vs single-root alternative
- [ ] Content migration path accepted
- [ ] Proceed to Phase 1 skill patch branch on approval

---

*Next step after approval: branch `design/skill-multi-surface`, apply the `SKILL.md` + `templates/` diff above, update `internal/CLAUDE.md` to the layered folder map, and open the adversarial review. No `app/internal` or `marketing/internal` directories are created until that review clears.*

---

## Appendix — Current `internal/` structure (2026-08-25)

Captured from `internal/` on disk. `.git` internals inside `out-html/` omitted; the listing below is the durable structure the proposal starts from.

```
internal/
├── CLAUDE.md                 ← workspace rules (umbrella map, delivery gates, filename conventions)
├── steering/                 ← stable steering — updated deliberately, one file per concern
│   ├── product.md            ← WHY — vision/strategy/bets/best-fit/objectives
│   ├── scope.md              ← WHAT — authoritative in/out + pricing for current offer version (Sequence Bridge extension)
│   ├── flows.md              ← HOW — Mermaid patient journey / system / lifecycle (view of scope)
│   └── roadmap.md            ← WHEN — monthly headings + Backlog (index, not record)
│   # steering/tech-stack.md  — missing (per proposal, will live per-surface)
│   # steering/design.md      — missing at this path; pointer satisfied via design/design.md (canonical)
├── design/                   ← visual identity — CANONICAL today, TRANSITIONAL (consolidated)
│   ├── design.md             ← canonical record: locations, aesthetic, principles, ideal checklist [ ]/[~]/[x], decisions
│   └── tokens.json           ← design tokens, W3C DTCG format (palette 50-950 → semantic color/space/radius/font/shadow/motion)
│   # elements.html / components.html / sections.html — referenced in design.md but not present on disk at this snapshot
├── out/                      ← rendered outputs — not part of skill, project-specific
│   ├── flows.html            ← visual rendering of steering/flows.md (Thread direction)
│   ├── product-definition-v1.md ← rendering of product+scope+flows for sharing
│   ├── token-directions.html ← direction 3 (Thread) exploration — wine/apricot/brick
│   └── proposal-internal-scaffolding-multi-surface.md ← this proposal
├── out-html/                 ← published HTML bundle (separate git history, netlify.toml, favicon)
│   ├── index.html
│   ├── favicon.svg
│   ├── netlify.toml
│   ├── README.md
│   ├── .gitignore
│   ├── sequence-bridge/
│   │   ├── 2026-08-11-product-flows.html
│   │   ├── 2026-08-01-design-system-options.html
│   │   └── 2026-07-28-parallel-productized-venture-options.html
│   └── ria/
│       ├── 2026-07-13-ria-roadmap.html
│       ├── 2026-06-03-ria-domain-baseline-assessment.html
│       ├── 2026-06-03-founder-partnership-term-sheet.html
│       ├── 2026-06-03-company-name-options.html
│       └── 2026-05-20-gtm-roadmap-options.html
├── ai-reviews/               ← agent reviews & observed-system records
│   └── .gitkeep              ← empty (no reviews yet)
├── steering/ — absent dirs: specs/, qa/, content/, video/, releases/  (not yet created on disk)
└── specs/ · qa/ · content/ · video/ · releases/ — not present at this snapshot; skill will scaffold on demand
```

**What the snapshot says about the gap to the target:**

- Umbrella `internal/steering/` today holds 4 files; `tech-stack.md` and `design.md` are absent as files — `tech-stack.md` is entirely missing and `design.md` is satisfied by the pointer to `design/design.md` (see `internal/CLAUDE.md` folder map note). Both will become per-surface files under `app/internal/steering/` and `marketing/internal/steering/` in the target (§3).
- `internal/design/` today holds 2 of the 5 canonical files (`design.md`, `tokens.json`); `elements.html`, `components.html`, `sections.html` are checklist-tracked in `design.md:72-192` but not on disk — the browser-viewable stand-ins still to be materialized (or treated as already captured under the transitional rule).
- `internal/specs/`, `qa/`, `releases/` are not yet materialized; per the folder map they are flat dated lists (`YYYY-MM-DD-<slug>.md`) plus `releases/CHANGELOG.md`/`roadmap-log.md`/`scope-v<N>.md` — in the target these exist both at umbrella and per-surface (`app/internal/`, `marketing/internal/`).
- `internal/content/` + `video/` are absent (marketing-only workflows, ask before creating) — target moves them to `marketing/internal/content/` + `video/`.
- `internal/out-html/` is a publishing artifact with its own `.git` (not part of the skill); `internal/out/` is the working render folder. Neither changes under the proposal except `out/` gains the token promotion render when it happens.

