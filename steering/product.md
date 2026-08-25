# Product

## Vision
Small practices stop leaking the leads they already pay for. Sequence Bridge installs a proven booking sequence ΓÇö landing page, booking, SMS confirmations and reminders, referral ask ΓÇö so every inquiry becomes a booked appointment that shows up. Dental practices are the first market; the sequence is reusable across verticals after that.

## Purpose
Most small practices don't have a lead problem, they have a conversion problem: calls go unanswered, forms sit unread, and booked patients no-show. The tools to fix this exist but are sold as software the owner has to operate. We exist to sell the outcome instead ΓÇö installed, operated, and reported on ΓÇö so the practice owner never has to become a systems administrator.

## Promise
Every new lead hears from your practice in under five minutes, every booked patient gets reminded, and every month you get one report showing exactly what that produced.

## Key success metric
**Percentage of clients still paying past month 3.** Practices do not keep paying $750ΓÇô$2,000/mo for something that isn't producing, so renewal past the contractual minimum is the honest verdict on whether we deliver the outcome we sell. It is also the assumption the first 90 days exist to test. Target for the first cohort: 2 of 3.

## Strategy
We sell an installed outcome, not software and not custom development. The bet is that a single sequence, built once and operated by us across many practices, beats both the tools practices fail to operate themselves and the agencies that sell them more leads they'll also fail to convert.

Bets we are making:
- **Conversion, not acquisition.** We take the leads a practice already generates and stop them leaking. We don't run ads or own lead volume.
- **One owned platform, not assembled tools.** Multi-tenant from day one, so marginal cost per added practice is near zero and the reporting that justifies the retainer is native rather than stitched together.
- **Fixed scope, one vertical at a time.** Dentists first. No bespoke builds, however tempting the deal.
- **Flat retainer over outcome pricing** until we have first-cohort data to price an outcome honestly.

Bets we are explicitly not making:
- No lead generation, ad management, or SEO.
- No custom software or "AI custom development" engagements.
- No handling of clinical or regulated health data in v1 (see the no-PHI constraint below).
- No self-serve product. We operate it; the practice doesn't configure anything.

### Key problems we solve
- Calls go unanswered during procedures and after hours, and the caller books with whoever answers first.
- Web form inquiries sit unread for hours or days, long past the window where the patient is still deciding.
- Booked patients no-show because reminders are ad hoc or left to whatever the PMS does by default.
- The owner has no idea how many inquiries arrived last month, how many booked, or how many showed.
- Software that could fix this requires someone in the practice to set it up and keep operating it, and nobody there has the time or inclination.

### Best-fit customers / users
- Single-location general dentistry practices, roughly 1ΓÇô4 chairs.
- Owner-operator dentist; no dedicated marketing person on staff.
- Front desk is one or two people who are already busy with patients in the chair.
- Already generating some inquiry volume ΓÇö a website, a Google listing, word of mouth. We convert demand; we don't create it.
- The buyer is the owner. The daily user is the front desk, and their bar is that it must not add work.

### Competitive alternatives
Two, and neither is a software vendor:

1. **Nothing ΓÇö the manual front desk.** Staff answer when they can, call back when they remember, and reminders are whatever the PMS sends by default. This is the honest default for most small practices and the real thing we displace.
2. **An answering service or virtual assistant.** Outsourced humans catching overflow calls. Covers the capture half only: no booking against real availability, no reminder sequence, no referral ask, and no reporting on what any of it produced.

Point tools like Weave, Podium, and NexHealth exist in this market, but they compete for budget rather than for the job ΓÇö they're sold to the practice as software to operate, which is precisely the failure mode our best-fit client already has.

### Differentiated value
- **It runs whether or not anyone at the practice does anything.** The alternatives all depend on a busy human remembering. Ours doesn't.
- **The whole sequence, not one piece of it.** Capture, book, remind, and ask for the referral are one system, so nothing falls between tools.
- **Someone else operates it.** We do the weekly failed-automation inspection and the monthly report. The practice never logs in to fix anything.
- **The owner finally sees the numbers.** Speed-to-lead, booking rate, show rate, and cost per booked appointment, in one report per month. Most practices have never had this.

## Objectives
Current horizon: through 2026-10-31 (first 90 days).

1. Three single-location general dentistry practices signed and paying implementation fees.
2. Every signed client live in production ΓÇö landing page, booking, SMS sequence, referral ask ΓÇö within 14 days of signing.
3. Median speed-to-lead under five minutes across all clients, sustained for 30 consecutive days.
4. At least two of the first three clients paying past the 3-month minimum.
5. A monthly report delivered to every client, on schedule, with none missed.

## Additional Product Notes or Phases

**No PHI in v1.** We collect contact and booking-preference information only. Clinical details route to the practice's existing PMS. This keeps us out of HIPAA scope for the first cohort and is a constraint on every spec until deliberately revisited.

**A2P 10DLC registration** is a required onboarding step for each client's SMS number in the US and Canada. It has lead time and must be started at signing, not at launch ΓÇö objective 2 depends on it.

**Deferred to later phases:** missed-call text-back, reactivation campaigns against dormant patient lists, multi-step nurture with lead-source tracking, Launch/Growth/Scale tiering, CRM integration and routing rules, outcome-based pricing, settings management over SMS, and expansion beyond dentistry. Full list in `sequence-bridge.md`.
