# Product

## Vision
Small practices stop leaking the leads they already pay for. Sequence Bridge installs a proven booking sequence — landing page, IVR call capture, booking, SMS confirmations and reminders, referral ask, and operated reporting — so every inquiry can become a booked appointment that shows up. Dental practices are the first market; the sequence is reusable across verticals after that.

## Purpose
Most small practices don't have a lead problem, they have a conversion problem: calls go unanswered, forms sit unread, and booked patients no-show. The tools to fix this exist but are sold as software the owner has to operate. We exist to sell the outcome instead — installed, operated, and reported on — so the practice owner never has to become a systems administrator.

## Promise
Every new lead hears from your practice in under five minutes, every booked patient gets reminded, and every month you get one report showing what the channels we manage produced. V1 reports referral-ask engagement but does not claim an attributed referral booking or show.

## Key success metric
**Percentage of clients still paying past month 3.** Practices do not keep paying $750–$2,000/mo for something that isn't producing, so renewal past the contractual minimum is the honest verdict on whether we deliver the outcome we sell. It is also the assumption the first 90 days exist to test. Target for the first cohort: 2 of 3.

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
- No clinical intake or unrestricted clinical data in v1. Identifiable scheduling and messaging data may still be regulated, so the minimum-data and compliance boundary below applies.
- No self-serve product. We operate it; the practice doesn't configure anything.

### Key problems we solve
- Calls go unanswered during procedures and after hours, and the caller books with whoever answers first.
- Web form inquiries sit unread for hours or days, long past the window where the patient is still deciding.
- Booked patients no-show because reminders are ad hoc or left to whatever the PMS does by default.
- The owner has no idea how many inquiries arrived last month, how many booked, or how many showed.
- Software that could fix this requires someone in the practice to set it up and keep operating it, and nobody there has the time or inclination.

### Best-fit customers / users
- Single-location general dentistry practices, roughly 1–4 chairs.
- Owner-operator dentist; no dedicated marketing person on staff.
- Front desk is one or two people who are already busy with patients in the chair.
- Already generating some inquiry volume — a website, a Google listing, word of mouth. We convert demand; we don't create it.
- The buyer is the owner. The daily user is the front desk, and their bar is that it must not add work.

### Competitive alternatives
Two, and neither is a software vendor:

1. **Nothing — the manual front desk.** Staff answer when they can, call back when they remember, and reminders are whatever the PMS sends by default. This is the honest default for most small practices and the real thing we displace.
2. **An answering service or virtual assistant.** Outsourced humans catching overflow calls. Covers the capture half only: no booking against real availability, no reminder sequence, no referral ask, and no reporting on what any of it produced.

Point tools like Weave, Podium, and NexHealth exist in this market, but they compete for budget rather than for the job — they're sold to the practice as software to operate, which is precisely the failure mode our best-fit client already has.

### Differentiated value
- **It runs whether or not anyone at the practice does anything.** The alternatives all depend on a busy human remembering. Ours doesn't.
- **The whole sequence, not one piece of it.** Capture, book, remind, and ask for the referral are one system, so nothing falls between tools.
- **Someone else operates it.** We do the weekly failed-automation inspection and the monthly report. The practice never logs in to fix anything.
- **The owner finally sees the numbers.** Speed-to-lead, booking rate, show rate, and cost per booked appointment, in one report per month. Most practices have never had this.

## Objectives
Current horizon: through 2026-10-31 (first 90 days).

1. Three single-location general dentistry practices signed and paying implementation fees.
2. Every signed client live in production — landing page, IVR call capture, booking through Synchronizer, SMS sequence, referral ask, and operated reporting — within 14 days of signing. No client launches with a partial package.
3. Median speed-to-lead under five minutes across all clients, sustained for 30 consecutive days.
4. At least two of the first three clients paying past the 3-month minimum.
5. A monthly report delivered to every client, on schedule, with none missed.

## Additional Product Notes or Phases

**V1 is one six-pillar package.** Every client receives landing page, IVR call capture, booking, SMS confirmations/reminders, referral ask, and operated reporting. There is no à la carte or partial-launch version.

**Booking and patient contract.** Web calendar, conversational SMS, and the bounded IVR use NexHealth Synchronizer for the required patient, availability, appointment, and status operations; the PMS appointment is the source of truth and nothing is confirmed before a successful write. V1 exposes one practice-configured `New patient visit`. Phone number finds possible patient records but never verifies one by itself: IVR requires DTMF date of birth before confirming only a first name. New-patient demographics are collected through a secure form, ambiguous matches go to the front desk, and both patient and appointment creation are idempotent. Verified existing patients go to the office/request-to-book rather than into the new-patient appointment type. Initial PMS targets are Dentrix, Eaglesoft, Open Dental, Curve Hero, and Dentrix Ascend, subject to exact version/operation validation. When the Synchronizer circuit opens, every channel uses request-to-book, the front desk receives opaque secure task notifications, and the patient is promised a response within 24 clock hours.

**Phone and consent contract.** Every v1 client receives an English/Spanish IVR. Main-menu `1` books and `2` reaches the office; after the main menu, `0` is the global human escape so slot choices remain unambiguous. The IVR asks optional SMS consent immediately after language selection, and no automated follow-up is sent without documented consent. Twilio speech recognition may produce a transient `SpeechResult`, but Sequence Bridge retains only normalized commands/selections and never stores raw call audio or raw speech transcripts. Warm transfer rings 20 seconds by default, configurable from 10–30 seconds, and every human/machine/failure/unknown/hang-up outcome has an idempotent fallback. Synchronizer outage switches every channel to request-to-book; primary IVR-handler failure uses provider-hosted direct forwarding.

**No clinical intake in v1.** We collect only the minimum identity/contact fields (including date of birth and the PMS-required gender value), consent/suppression evidence, generic appointment, scheduling/status, normalized IVR selections, communications events, provider references, and audit data needed to operate the sequence. Symptoms, diagnoses, treatment reasons, history, charts, detailed procedures, prescriptions, images, insurance, claims, billing, recordings, persisted raw speech/transcripts, unrestricted free text, and open-ended voice capture are prohibited. Because identifiable appointment scheduling and messaging may involve PHI, production requires counsel-reviewed data/retention rules, applicable practice/vendor BAAs, the approved security baseline, the qualifying Twilio edition and HIPAA configuration, A2P approval, and state-specific review.

**A2P 10DLC registration** is a required onboarding step for each client's SMS number. It starts at signing, not launch. The sender's campaign documents the IVR/web opt-in flows; the first message identifies the practice and includes STOP language.

**Referral truth in v1.** The basic referral ask is mandatory and sends only after a completed/kept appointment status. V1 reports ask eligibility, sent, delivery, reply, and click; it does not claim a referred lead, booking, or show. A unique short link/code and full attribution are v2.

**Deferred to later phases:** open-ended AI receptionist behavior, clinical triage, call recording or persisted/raw transcription, existing-patient self-service booking, referral booking/show attribution, no-show recovery, reactivation campaigns, multi-step nurture with lead-source tracking, Launch/Growth/Scale tiering, CRM integration and routing rules, outcome-based pricing, settings management over SMS, and expansion beyond dentistry. Full list in `out/scope.md`.
