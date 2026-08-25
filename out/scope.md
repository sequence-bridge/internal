# Scope — offer v1

**Status:** current · **Declared:** 2026-08-10

The single authoritative statement of what Sequence Bridge sells: what's in, what's out, what it costs, and what bounds it. If any other document contradicts this one, this one wins and the other gets fixed.

**"v1" is an offer version, not a code release.** It names what a client signs up for. `releases/CHANGELOG.md` tracks code shipping per push and moves on a completely different clock — a v1.1 offer might take twenty deploys, and a deploy never bumps the offer version.

**Before the first edit that changes this doc to a new offer version**, copy it to `internal/releases/scope-v1.md`. Freeze on starting the change, not on shipping — shipping is when you've already forgotten.

---

## In scope

The installed sequence. All six are delivered to every v1 client; there is no à la carte.

1. **Landing page** — the practice's existing page with our form embedded, or a single conversion-focused page we build and host. One template, themed per client.
2. **Call capture** — a tracking number published on the landing page and Google Business Profile, forwarding to the practice's existing line. Counts every call. Fires a missed-call text-back with a booking link when nobody picks up.
3. **Booking** — appointment scheduling against the practice's real availability, reachable from the landing page, the instant SMS reply, and the missed-call text-back.
4. **SMS confirmations and reminders** — automated, timed to cut no-shows.
5. **Referral ask** — post-visit SMS asking the serviced patient for a referral.
6. **Operated and reported** — we run the weekly failed-automation inspection and metrics check, and deliver one monthly client report. This is not a bonus; it is what the retainer buys, and it is in scope as much as any feature above.

## Out of scope for v1

The authoritative deferral list. Nothing else claims to hold one.

| Deferred | Why |
|---|---|
| Call answering / AI receptionist | Its own product, not a feature of this one |
| Call recording and transcription | Breaks the no-PHI constraint below |
| Porting or replacing the practice's main number | Highest-risk change we could ask of a new client; leaves calls dialed directly to that line unmeasured, a gap we accept knowingly |
| No-show recovery | No defined path once a reminder fails — see open question 3 below |
| Reactivation campaigns against dormant patient lists | Needs patient list access; PHI-adjacent |
| Multi-step nurture + lead-source tracking | One sequence first; prove it moves the metrics |
| Launch / Growth / Scale tiering | Single package until we have pricing data |
| CRM integration and routing rules | No client has asked; premature |
| Outcome-based pricing (per booking or per show) | Can't price an outcome honestly without first-cohort data |
| Settings management over SMS | We operate it; the practice configures nothing in v1 |
| Expansion beyond dentistry | One vertical at a time |
| Self-serve product | Not a bet we're making |
| Lead generation, ads, SEO | We convert demand; we don't create it |

## Constraints that bound v1

- **No PHI.** Contact and booking-preference information only. Clinical detail stays in the practice's PMS. Keeps the first cohort out of HIPAA scope and constrains every spec until deliberately revisited.
- **A2P 10DLC registration** is required per client SMS number in the US and Canada. It has lead time and starts at signing, not at launch. Voice needs no 10DLC.
- **The practice's main phone line is never touched.** Our tracking number is additive. If it fails, their existing number still rings.
- **Opt-out language** in every outbound template, including missed-call text-back.

## Pricing

| Component | v1 |
|---|---|
| Implementation fee | $3,500–8,500, depending on existing page vs. new build and calendar complexity |
| Managed service retainer | $750–2,000/mo |
| Minimum term | 3 months |
| SMS allowance | ~500 segments/mo included; overages passed through at cost |

Single package. No tiers in v1.

## Client responsibilities

The boundary of what we deliver depends on these, so they belong in scope:

- **Real calendar availability**, kept accurate. Our booking is only as good as what they publish.
- **Entering booked appointments into their PMS.** No integration exists in v1 — see open question 1 below. This adds manual work to a front desk whose stated bar is "must not add work," and it must be said out loud during the sale rather than discovered at launch.
- **Ad spend figures**, if they want cost per booked appointment in the monthly report. We don't own their spend — see open question 5 below.

## Open questions affecting this scope

Recorded here in full, deliberately. This file gets frozen when the offer version changes, and a frozen record that points into a living document is a record you can't trust — so nothing below depends on reading another file. `flows.md` carries the same questions with extended reasoning and diagrams, but it always describes the *current* offer and is never versioned.

1. **PMS handoff.** The practice management system — Dentrix, Eaglesoft, Open Dental, Curve and others — is the operational core of a dental practice: records, charting, claims, billing, and the appointment book. Practically every practice has one, so "sell to practices without a PMS" is not an available strategy.

   The problem runs both ways. Writing into it means the front desk re-enters every booked appointment by hand — work added to people whose stated bar is "must not add work." Reading out of it is harder and matters more: **in dental the appointment book usually is the PMS**, so without a read path we don't know real availability, and booking a patient into a slot the front desk has already filled produces a double-booking — a patient arriving to no chair. That is worse than admin work.

   Four options: request-to-book (no integration, but a human sits in the path); **hold slots** (the practice reserves a few new-patient slots a day, blocked in the PMS, that only we book into — instant booking is real and double-booking becomes structurally impossible); middleware such as Sikka or NexHealth's Synchronizer (real integration, per-practice cost, vendor dependency, and NexHealth competes with us); or direct integration starting with Open Dental, the most integration-friendly of the common systems.

   **Recommendation:** hold slots for v1 with no integration, the transcription step stated plainly during the sale, and PMS brand captured on every discovery call so the first cohort picks the v1.1 integration target for us. Vendor specifics need verifying before anything is committed.

2. **Phone channel depth.** *Decided 2026-08-10, pending confirmation.* v1 covers a tracking number plus missed-call text-back; call answering and recording stay out. The decision rests on an unmeasured assumption — that phone is the majority inquiry channel for single-location dental. Baseline capture is part of discovery. **Reverses if** calls turn out to be a small minority of inquiries across the first cohort, since the argument for including them is volume and denominator integrity and both collapse without the volume.

3. **No-show recovery.** Reminders reduce no-shows; they don't eliminate them. We report show rate, so we hand clients a number with no lever attached to it.

4. **Referral attribution.** The referral ask is an in-scope pillar, but the return path isn't tagged. A referred patient entering through the same untagged form makes the pillar unprovable — and unprovable value is the first thing cut at renewal.

5. **Cost-per-booked-appointment input.** We don't run ads or own lead volume, so the spend figure has to come from the practice. Confirm they'll share it before promising the metric.

## Version history

| Offer version | Declared | Frozen record |
|---|---|---|
| v1 | 2026-08-10 | current — this file |
