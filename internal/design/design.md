# Design system

Single source of truth for visual identity. This file tracks the **ideal** design system as a checklist; the actual specimens live alongside it in `tokens.json` and the three `.html` files.

## How this folder works

| File | Role |
|---|---|
| `design.md` (this file) | The ideal inventory, as a checklist. `[ ]` = missing, `[~]` = partial, `[x]` = captured. |
| `tokens.json` | Design tokens in [W3C DTCG](https://tr.designtokens.org/format/) format. The values that drive everything else. |
| `elements.html` | Atoms (typography, buttons, inputs, …) rendered + annotated. |
| `components.html` | Composites (cards, navbars, forms, …) rendered + annotated. |
| `sections.html` | Page sections (hero, pricing, footer, …) rendered + annotated. |

Each HTML file is **standalone** — open it directly in a browser (`file://`). It loads Tailwind v4 from the Play CDN (`https://unpkg.com/@tailwindcss/browser@4`) and includes an inline `@theme` block at the top that mirrors `tokens.json`, so specimens render with the project's actual tokens.

**Sync rule.** When you change a value in `tokens.json`, update the inline `@theme` block in all three HTML files by hand (no automation until the project has a real Tailwind build pipeline; at that point, replace the CDN script + inline `@theme` with the compiled CSS). After updating tokes, prompt the user to update the inline themes in the HTML files. 

**Update workflow.** When you add a specimen to an HTML file, flip the matching checkbox below from `[ ]` → `[~]` (partial coverage, e.g. some states still missing) or `[x]` (fully captured).

---

## Aesthetic

<one paragraph: overall feel, words a user would use to describe it, references that inspire it>

---

## Tokens — `tokens.json`

### Color

Two layers: raw `palette` ramps (the vocabulary) + `color` semantic roles (the grammar) that reference palette steps. Components consume semantic tokens; reach into the palette only for charts, custom tints, or one-offs.

**Palette ramps (50–950 per hue)**
- [x] violet — anchors `primary` (500) and `primary-hover` (600)
- [x] teal — anchors `secondary` (500) and `secondary-hover` (600)
- [x] amber — anchors `tertiary` (500) and `tertiary-hover` (600)
- [ ] gray / neutral ramp (currently flat `bg` / `surface` / `fg` tokens; promote to a ramp when more shades are needed)
- [ ] semantic ramps (`success` / `warning` / `danger` / `info` — flat today; promote if alert backgrounds need a 100/950 tint)

**Semantic roles**
- [x] Surfaces (`bg`, `surface`, `surface-elevated`)
- [x] Foreground (`fg`, `fg-muted`, `fg-inverse`)
- [x] Brand (`primary`, `primary-hover`, `secondary`, `secondary-hover`, `tertiary`, `tertiary-hover`)
- [ ] Brand soft tints (e.g. `primary-soft` for alert backgrounds — currently using `bg-primary/15` inline)
- [x] Accent
- [x] Semantic (`success`, `warning`, `danger`, `info`)
- [x] Borders / dividers

### Typography
- [ ] Display family + weights
- [ ] Body family + weights
- [ ] Mono family
- [ ] Size scale (`xs` → `4xl`)
- [ ] Line-height scale
- [ ] Letter-spacing tokens

### Spacing
- [ ] Base scale (4 / 8 / 12 / 16 / 24 / 32 / 48 / 64)

### Radius
- [ ] `sm` / `md` / `lg` / `full`

### Shadow / elevation
- [ ] `sm` / `md` / `lg`
- [ ] Focus ring

### Motion
- [ ] Easing curves (`standard`, `decelerate`, `accelerate`)
- [ ] Durations (`fast` / `default` / `slow`)
- [ ] Stated principles (e.g. "things enter from below; things exit by fading")

---

## Elements — `elements.html`

Atoms. Single-purpose primitives.

### Typography
- [ ] h1–h6 + lead paragraph
- [ ] Body, small, mono, kbd, code
- [ ] Lists (ul / ol)
- [ ] Blockquote

### Form atoms
- [ ] Button — primary / secondary / ghost / destructive
- [ ] Button states — default / hover / active / focus / disabled / loading
- [ ] Text input + label + help + error + disabled
- [ ] Textarea
- [ ] Select
- [ ] Checkbox / radio / switch
- [ ] File input

### Navigation atoms
- [ ] Link — default / hover / visited
- [ ] Breadcrumb item

### Display atoms
- [ ] Badge / tag / chip
- [ ] Avatar (sm / md / lg)
- [ ] Icon sizes
- [ ] Divider
- [ ] Skeleton / placeholder bar

---

## Components — `components.html`

Composites. Primitives assembled into reusable units.

- [ ] Card (default / elevated / interactive)
- [ ] Stat card
- [ ] Form pattern (label + input + help + error + submit row)
- [ ] Navbar (top, with logo + nav + actions)
- [ ] Footer (compact)
- [ ] Sidebar / side nav
- [ ] Alert / banner (info / success / warning / danger)
- [ ] Toast / notification
- [ ] Modal / dialog
- [ ] Drawer / sheet
- [ ] Tooltip
- [ ] Popover
- [ ] Dropdown menu
- [ ] Tabs
- [ ] Accordion
- [ ] Pagination
- [ ] Breadcrumbs
- [ ] Empty state
- [ ] Data table row + header

---

## Sections — `sections.html`

Page-level building blocks.

- [ ] Hero (with CTA)
- [ ] Feature grid (3-column)
- [ ] Pricing (3-tier)
- [ ] CTA banner
- [ ] Logo cloud
- [ ] Testimonial(s)
- [ ] FAQ (accordion)
- [ ] Stats row
- [ ] Footer (full)
- [ ] Sign-in / sign-up forms
- [ ] 404 / empty state page

---

## Notes & decisions

<freeform: tradeoffs, open questions, references, rejected approaches>
