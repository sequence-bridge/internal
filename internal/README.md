# internal/

This folder is the source of truth for where the project is going and why. Code is derived from what's in here — not the other way around. When code and spec disagree, fix the spec first.

Invoke the `internal-scaffolding` skill (type `/internal-scaffolding`) to work through any of the phases below. It handles interviews, drafting, and file management so you don't have to.

---

## Folder map

```
internal/
├── README.md              ← this file
├── CLAUDE.md              ← AI workspace rules (do not edit casually)
├── steering/              ← stable intent; updated deliberately
│   ├── product.md         ← vision, strategy, objectives
│   ├── roadmap.md         ← monthly planned work + backlog
│   └── tech-stack.md      ← languages, libraries, conventions
├── design/                ← visual identity
│   ├── design.md          ← design system checklist (gap tracker)
│   ├── tokens.json        ← design tokens (colors, type, spacing…)
│   ├── elements.html      ← atoms: buttons, inputs, badges… (open in browser)
│   ├── components.html    ← composites: cards, navbar, forms… (open in browser)
│   └── sections.html      ← page sections: hero, pricing, footer… (open in browser)
├── specs/                 ← one file per feature, flat list
│   └── YYYY-MM-DD-slug.md
├── content/               ← writing style guide + content drafts
│   ├── style.md           ← voice, tone, word swaps, house vocabulary
│   └── YYYY-MM-DD-slug.md ← individual content pieces
└── releases/
    ├── CHANGELOG.md       ← auto-updated before each git push
    └── roadmap-log.md     ← archive of completed roadmap items
```

---

## The four phases

Work flows through four phases. Each has a distinct question — don't conflate them. The skill pauses between phases so you can review and redirect before the next one starts.

### Phase 1 — Context
**Question:** Where are we going and with what?

Run this first, and any time a steering doc feels out of date. The skill reads `product.md`, `roadmap.md`, `tech-stack.md`, `design/design.md`, and `content/style.md`, then shows you a gap table: which sections are missing or still placeholder. Pick a file and the skill interviews you, then drafts it.

→ Invoke: `/internal-scaffolding` → option 1 (default)

### Phase 2 — Specify
**Question:** What does this feature need to do?

Takes a roadmap item or idea and turns it into a spec file in `internal/specs/`. The skill reads your steering docs first for context, then asks focused questions about the specific feature. The spec covers WHAT — not how. Library choices and implementation steps go in Phase 3.

→ Invoke: `/internal-scaffolding` → option 2, or just say "spec the [feature name]"

**Spec sections:** Summary · User value · Requirements · Out of scope · Acceptance criteria · Open questions

**Naming:** `YYYY-MM-DD-slug.md`. Once a feature ships, the spec is frozen — new iteration = new dated file.

### Phase 3 — Plan
**Question:** How will we build it?

Takes an approved spec and produces an implementation plan: approach, task list, test strategy, risks. Hands off to `superpowers:writing-plans` if installed, otherwise drafts manually and saves as `internal/specs/YYYY-MM-DD-slug-plan.md`.

→ Invoke: `/internal-scaffolding` → option 3, or say "plan [spec name]"

### Phase 4 — Implement
**Question:** Build it (TDD-first).

Takes an approved plan and executes it test-first. Hands off to `superpowers:subagent-driven-development` if installed, otherwise proceeds task by task — failing test → minimum code to pass → next task. If implementation reveals the spec was wrong, the spec gets updated first.

→ Invoke: `/internal-scaffolding` → option 4

---

## Other workflows

### New content piece
Draft a blog post, email, landing page copy, etc. The skill reads `content/style.md` first, then interviews you about the piece. Output: a dated file in `internal/content/`.

→ Invoke: `/internal-scaffolding` → option 5

### Archive roadmap items
At month-end, moves every checked `- [x]` item from `roadmap.md` into `releases/roadmap-log.md`. Unchecked items stay put.

→ Invoke: `/internal-scaffolding` → option 6, or say "archive [month]"

---

## Design system — open in browser

The three HTML files in `design/` are standalone — open them directly via `file://` in any browser. They use Tailwind v4 and the project's own tokens from `tokens.json`. When you update a token value, update the `@theme` block in all three HTML files to match.

---

## Changelog automation

Before every `git push`, the skill (via `CLAUDE.md` rules) prepends a dated entry to `releases/CHANGELOG.md` listing the commits going out, then commits it as `chore: update changelog`.
