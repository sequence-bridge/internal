# `internal/` — workspace rules

This folder holds the project's source-of-truth artifacts: steering docs, the design system, feature specs, content pieces, and the release log. Code is derived from these. When code and spec disagree, fix the spec first.

Invoke the `internal-scaffolding` skill whenever you start work in this project — it handles both first-time setup and all ongoing phases (filling in steering docs, speccing features, planning, implementing, archiving). This file owns the **rules** for what lives here and how it's named.

## Why this folder exists

Solo and small-team projects drift when intent lives only in someone's head. `internal/` is a small, opinionated structure that keeps intent (specs, steering) and outcome (releases) in version control alongside the code. It costs a few minutes of writing per feature and saves hours of "wait, why did we build it this way?" later.

## Folder map

```
internal/
├── CLAUDE.md              ← this file
├── steering/              ← stable across features; updated deliberately
│   ├── product.md         ← vision, strategy, objectives
│   ├── roadmap.md         ← monthly headings + backlog (no version table)
│   └── tech-stack.md      ← languages, components, conventions, TDD policy
├── design/                ← visual identity; gap-tracked + browser-viewable
│   ├── design.md          ← ideal design system as a checklist (gap tracker)
│   ├── tokens.json        ← design tokens, W3C DTCG format
│   ├── elements.html      ← atoms, rendered + annotated
│   ├── components.html    ← composites, rendered + annotated
│   └── sections.html      ← page sections, rendered + annotated
├── specs/                 ← per-feature specs, flat list
│   └── YYYY-MM-DD-<slug>.md
├── qa/                    ← raw QA feedback from hands-on testing
│   └── YYYY-MM-DD-<slug>.md
├── content/               ← writing style guide + per-piece drafts
│   ├── style.md           ← voice, tone, word swaps, paired examples
│   └── YYYY-MM-DD-<slug>.md  ← individual content pieces
└── releases/
    ├── CHANGELOG.md       ← auto-populated per push (see rule below)
    └── roadmap-log.md     ← hand-curated, populated by the archive command
```

## Filename conventions

- **Specs, QA notes, and content pieces**: `YYYY-MM-DD-<slug>.md`. Date = creation date. Slug = kebab-case, derived from the feature/piece name. Same-day duplicates get a `-2`, `-3` counter.
- **Steering**: fixed names (`product.md`, `roadmap.md`, `tech-stack.md`). One file per concern.
- **Design**: fixed filenames inside `design/` (`design.md`, `tokens.json`, `elements.html`, `components.html`, `sections.html`).
- **Style guide**: `content/style.md` is a fixed-name meta file that sits alongside the dated content pieces.
- **Releases**: fixed names (`CHANGELOG.md`, `roadmap-log.md`). No dating in the filename — the dates live inside.

## Append-version, not edit-in-place

Once a spec or content piece is **shipped/published**, never edit it in place. A second iteration gets a new dated file. The old file is a record of what was intended at the time.

While a spec or content piece is **in-progress**, edit freely. Freeze only on ship.

## Spec sections

Every file in `internal/specs/` uses these sections (template at the skill's `templates/specs.md`):

`## Summary` · `## User value` · `## Requirements` · `## Out of scope` · `## Acceptance criteria` · `## Open questions`

Specs cover **WHAT**, not HOW. Library choices and implementation steps go in the plan (Phase 3), not the spec.

## QA notes

`internal/qa/` holds raw feedback from hands-on testing of shipped work — bugs, things that feel wrong, reference screenshots and links. Deliberately unpolished: no template, no required sections, write it the way you'd say it.

These files are **inputs, never edited to match the outcome**. When a QA file drives work, derive a normal dated spec in `internal/specs/` from it (with a `Derived from internal/qa/<file>` line at the top) and leave the QA file exactly as written. It's the record of what was actually observed, and rewriting it destroys that.

## Content sections

Dated content pieces in `internal/content/` follow `templates/content.md`. The template covers ideas, structure, meta, intro, AEO (answer-engine optimization), post, image, and video scripts. Strip sections that don't apply to a given piece.

When I tell you to create an html from a content md file, review grammar and update any issues and create the html file with the same name but .html extension in the same folder. For different sections from the intro, post body, and conclusion, use the appropriate html tags (e.g., `<h2>`, `<p>`, `<ul>`, etc.) to structure the content clearly. Make sure to include any inline links as specified in the markdown file.

## Writing style

`internal/content/style.md` is the persistent style guide every content piece should respect. Sections: Audience, Voice anchors (each paired with its failure mode), Tone-by-context, Word swaps, House vocabulary, Paired good/bad examples, Hard rules, Reusable structures, Notes & exceptions.

Workflow when drafting a piece: read `style.md` first; pull in the relevant voice anchors and tone shift; check word swaps and house vocabulary against the draft before shipping. If a piece needs to break a rule, document the exception in `style.md` under Notes & exceptions — don't quietly drift.

`style.md` is a living reference (edit in place) unlike dated pieces (append-version on publish).

## Design system

`internal/design/` is the visual source of truth. Five files, fixed names:

- `design.md` — the **ideal** design system as a checklist. Each item is marked `[ ]` (missing), `[~]` (partial), or `[x]` (captured). Single place to see what's left to build out.
- `tokens.json` — design tokens in [W3C DTCG](https://tr.designtokens.org/format/) format (`{ "$value", "$type" }`). The values that drive everything else.
- `elements.html`, `components.html`, `sections.html` — standalone, browser-viewable pages. Open via `file://`. Each loads Tailwind v4 from the Play CDN (`https://unpkg.com/@tailwindcss/browser@4`) and includes an inline `@theme` block at the top that mirrors `tokens.json`, so every specimen uses the project's actual tokens.

Workflow when adding a specimen: render it in the appropriate HTML file → flip its checkbox in `design.md` from `[ ]` to `[~]` or `[x]`. When token values change, update `tokens.json` **and** the inline `@theme` block in each HTML file (no automatic sync until the project has a real Tailwind build pipeline; at that point, replace the CDN script + inline `@theme` with the compiled CSS).

## Roadmap format

`internal/steering/roadmap.md` uses monthly headings (current month when using the skill and the next month) with checkbox bullets:

```markdown
# Roadmap

## 2026-05
- [ ] Onboarding redesign
- [ ] Pricing page A/B

## 2026-06
- [ ] Dashboard v2

## Backlog
- Billing flow rewrite
- Referrals program
```

Checked items get archived to `releases/roadmap-log.md` when the user runs the archive command on a month. Unchecked items stay put.

## Changelog on push (load-bearing automation)

**Before every `git push`** to this project's remote:

1. List the commits about to ship: `git log @{u}..HEAD --oneline` (or `git log origin/<branch>..HEAD --oneline` if no upstream is set).
2. Prepend a dated, branch-grouped entry to `internal/releases/CHANGELOG.md`:
   ```markdown
   ## YYYY-MM-DD — <branch>
   - <commit-hash> <commit-subject>
   - …
   ```
3. Commit that update separately with the message `chore: update changelog`.
4. Push both commits.

**Skip the changelog step only when** the push is changelog-only (nothing to log about itself), or the push contains only docs changes that are entirely inside `internal/` (already self-documenting).


