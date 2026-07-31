# Writing voice & style

Canonical reference for *how* we write — voice, tone, mechanics, vocabulary. Every content piece in this folder should sound like it came from the same person. When something feels off, check it against this.

Fill in the placeholders as you make decisions; the empty sections are gaps, not noise. The four highest-leverage sections to fill in first are **Audience**, **Voice anchors**, **Word swaps**, and **Paired examples** — those four alone will get most drafts unstuck.

---

## Audience

<one short paragraph. Who reads us, what they already know, what state of mind they're in when they encounter our writing. Be specific — "developers at small SaaS companies who've tried two other tools and are skeptical of marketing speak" is useful; "tech-savvy professionals" is not.>

---

## Voice — three anchors

Each anchor is a positive trait paired with its failure mode. The pairing is what stops drift; a positive trait alone is too easy to over-apply.

1. **<anchor 1>** (not <failure mode>) — <one sentence on what this looks like in practice>
2. **<anchor 2>** (not <failure mode>) — <one sentence>
3. **<anchor 3>** (not <failure mode>) — <one sentence>

Candidates to choose from: direct (not blunt), specific (not jargony), confident (not arrogant), warm (not chummy), plainspoken (not dumbed-down), opinionated (not preachy), curious (not naive), generous (not sycophantic).

---

## Tone — how voice shifts by context

Voice stays constant; tone moves. Note where it should flex.

| Context | Tone shift |
|---|---|
| Landing / hero copy | <e.g. more rhythm, fewer hedges, sentence fragments allowed> |
| Documentation | <e.g. plainer, concrete examples, no metaphors> |
| Onboarding & empty states | <e.g. warmer, lower stakes, gently celebratory> |
| Error messages | <e.g. take the blame, be specific, suggest a next step> |
| Changelog / release notes | <e.g. dry, factual, lead with the user-visible change> |
| Social / short-form | <e.g. punchier, one idea per post, no setups> |

---

## Word swaps

| Avoid | Prefer | Why |
|---|---|---|
| leverage | use | "leverage" means physical advantage from a lever |
| utilize | use | same problem; longer for no reason |
| in order to | to | the longer form adds zero meaning |
| at this time | now | brevity |
| facilitate | help / let | usually you mean "let" |
| solution | <the specific thing it is> | "solution" obscures; name the thing |
| seamless / robust / world-class / cutting-edge | <delete> | corporate filler that signals nothing |
| game-changer / thought leader / synergy | <delete> | clichés people skim past |

Add to this table whenever you catch yourself reaching for a word that smells corporate.

---

## House vocabulary

Proper nouns, capitalization, hyphenation — the boring stuff that drifts if you don't pin it down.

- **<Product name>** — one word, capitalized like this. Not "<Product Name>", not "<product-name>".
- **<feature name>** — lowercase unless starting a sentence.
- <add more as the project's vocabulary develops>

---

## Paired examples

The fastest way to see voice working. Show the same idea written two ways.

**Feature announcement opener**
- Off: <wrong tone, in the failure mode of one of the voice anchors above>
- On:  <right tone>

**Error message**
- Off: "An unexpected error occurred. Please try again."
- On:  <something specific, owns the problem, suggests a next step>

**Button label**
- Off: "Submit"
- On:  <names what actually happens, e.g. "Send invoice", "Create workspace">

Add three to five pairs that match the kinds of writing this project actually produces.

---

## Hard rules

Only rules that are load-bearing — drop anything that's just there to look complete.

- **Person**: <we / you / I — pick one and stick to it>
- **Contractions**: <yes / no / situational>
- **Sentence case in headings** (not Title Case)
- **Active voice** by default; passive only when the actor is genuinely irrelevant
- **Numbers**: numerals for everything except a sentence-starter
- **Em-dashes — use sparingly** — they're seasoning, not the meal
- **No emoji** in body copy (UI status icons are fine)
- **Read it aloud** before shipping — if you stumble, the reader will too

---

## Reusable structures

Patterns you reach for so often that codifying them saves time.

- **Opener** — <e.g. "Lead with the user's problem in one sentence; never with our product name">
- **CTA style** — <e.g. action verb + concrete object: "Create your first project", not "Get started">
- **Sign-off** — <e.g. closing line / signature pattern>

---

## Notes & exceptions

<freeform: surfaces or pieces that get to break the rules, and why>
