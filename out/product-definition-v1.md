# Product Definition v1 — Sequence Bridge

Source: Derived from `steering/product.md` (WHY), `out/flows.md` (HOW), and `out/scope.md` (authoritative IN/OUT + pricing). Where this file and `out/scope.md` disagree, scope wins.

**One-liner:** We install and operate a proven booking sequence in your dental practice so new leads become booked appointments that show up.

**Status:** Offer v1 · Declared 2026-09-22 · One six-pillar package, no tiers · PMS sync via NexHealth Synchronizer

## Who it's for

- Single-location general dentistry, roughly 1–4 chairs, owner-operator, no marketer on staff.
- Front desk of one or two people, busy with patients — the bar is “must not add recurring work.”
- Already has inquiry volume from its website, Google Business Profile, or word of mouth. We convert demand; we do not create it.
- Uses a qualified version of Dentrix, Eaglesoft, Open Dental, Curve Hero, or Dentrix Ascend with the required Synchronizer operations.

**Not for:** multi-location groups, specialty workflows, unsupported PMSs, clinical intake, existing-patient self-service in v1, lead generation/ads/SEO, bespoke development, or self-serve software.

## The promise

Every new lead hears back in under five minutes, every booked patient gets reminded, and every month the owner gets one report showing what the channels we manage produced.

The report is explicit about its denominator: direct calls to the practice's main line are outside v1 measurement.

## Problems we solve

- Calls go unanswered during procedures or after hours, so callers book elsewhere.
- Web forms sit unread for hours or days.
- New patients cannot see and book real availability without staff transcription.
- Booked patients no-show.
- The owner lacks a trustworthy inquiries → bookings → shows funnel.
- Point tools exist, but the practice is expected to configure and operate them.

## IN — v1 delivers all six to every client

### 1. Landing page

The practice's existing site with our form/calendar embedded, or a single conversion-focused page we build and host. One template, themed per client.

### 2. Call capture — bounded IVR front door

All calls to the tracking number published on the landing page and Google Business Profile enter the IVR. The practice's main line is never ported or replaced.

1. **Language:** English by default; press `9` or say “Español” for Spanish.
2. **SMS consent:** immediately after language selection, callers without current documented consent may press `1`/say yes to receive scheduling, confirmation, reminder, and disconnected-call follow-up texts, or press `2`/say no and continue without texts. Consent is optional, versioned, auditable, and revocable.
3. **Main menu:** press `1` or use an approved booking phrase to book; press `2` or use an approved office phrase to reach the practice.
4. **Global escape:** after the main menu, `0` or an approved office phrase reaches the office so numeric slot choices remain unambiguous.
5. **Bounded speech:** English/Spanish booking and office phrases, explicit emergency routing keywords, preferred date/time, and numeric selections only. Each prompt waits five seconds and retries twice before office transfer.

Twilio may return a transient raw `SpeechResult`. Sequence Bridge stores only the normalized language, intent, date/time or numeric selection, and provider confidence/status. It does not store call audio, raw speech, or raw transcripts. Open-ended conversational AI remains v2.

**Route 1 — New-patient auto-book:** The IVR captures a preferred time, offers two or three live Synchronizer slots, and records a tentative selection. A new caller who consented to SMS completes a short-lived secure form for the identity/demographic fields required by NexHealth/PMS. The system matches or creates the patient, revalidates the slot, writes to the PMS, and confirms only after success. If the slot is gone, it offers fresh availability. A caller who cannot use the secure form is transferred to the office.

**Route 2 — Human on demand:** The practice leg rings 20 seconds by default, adjustable from 10–30 seconds per practice. Provider outcome `human` bridges the call. Machine/fax, busy, no-answer, failed, canceled, unknown, timeout, and caller hang-up use the approved unavailable fallback. When valid SMS consent exists, an incomplete call generates at most one follow-up into conversational booking. Duplicate or delayed callbacks cannot bridge or message twice.

If the primary IVR application fails, a provider-hosted handler forwards directly to the practice main line. Voice reachability wins even if event capture and follow-up automation are temporarily unavailable.

### 3. Booking — three channels, one PMS contract

One PMS of record, three entry points:

1. **IVR** — the bounded new-patient route above.
2. **Web calendar** — embedded calendar reads live availability, collects the secure patient fields and SMS choice, revalidates, writes, then confirms.
3. **Conversational SMS** — patient supplies a preferred time, receives two or three live options, replies `1/2/3`, and is booked only after revalidation and a successful write.

**Patient identity:** phone number finds candidates but never verifies a patient alone. IVR asks for DTMF date of birth, then confirms only the first name if one candidate remains. The secure form collects first/last name, email, phone, date of birth, and the gender value required by the PMS/NexHealth contract. Full supplied information is checked before creation. Ambiguous or conflicting matches go to front-desk review; records are never auto-merged. Patient and appointment writes have separate idempotency keys.

**Appointment type:** one practice-configured `New patient visit` label, duration, provider, operatory, and PMS mapping. A verified existing patient is transferred/request-to-book rather than placed into a new-patient type.

**Synchronizer:** reads availability and the minimum appointment status needed for reminders, show-rate reporting, and referral eligibility; searches/creates the patient; writes the confirmed appointment. The PMS remains the source of truth, with no editable shadow calendar.

**Outage:** three transient failures within two minutes, or one authentication/permission failure, opens the tenant's circuit. Web, SMS, and IVR stop offering live slots and create request-to-book instead. The front desk receives SMS and email containing only an opaque task ID and secure link. The patient is acknowledged immediately and promised a response within 24 clock hours. Requests are never replayed automatically after recovery.

### 4. SMS confirmations and reminders

Automated and timed to cut no-shows. Messages send only under valid consent/suppression state, identify the practice, and include required opt-out behavior. Delivery and reply events enter the same audit/reporting model.

### 5. Referral ask

After Synchronizer/PMS reports the appointment completed/kept, v1 sends the serviced patient the basic referral ask with the practice booking link. Canceled, no-show, unresolved, and unknown-status appointments are suppressed.

V1 reports referral-ask eligibility, sent, delivery, reply, and link click. It does not claim a referred lead, booking, or show. V2 may add an opaque six-character code/short link and full attribution.

### 6. Operated and reported

We run the weekly failed-automation inspection and metrics check and deliver one monthly client report. This is the retainer, not a bonus feature.

Metrics include speed-to-lead, booking rate, show rate, calls managed through the tracking number, provider/fallback failures, request-to-book state, and cost per booked appointment only when the practice supplies ad-spend input. Referral metrics stop at ask engagement in v1.

## Constraints that bound v1

- **One package:** all six pillars pass launch tests before the client is live.
- **No clinical intake:** scheduling/messaging may still be PHI. Clinical detail, charts, detailed procedures, recordings, retained raw speech/transcripts, unrestricted free text, insurance, claims, and billing are prohibited.
- **Production compliance gate:** founder is initial security/privacy owner; qualified healthcare counsel reviews BAAs, state law, data map, retention/deletion, incident obligations, and disclosures. Required security controls include risk analysis, least privilege/MFA, encryption, audit/access review, secrets management, incident response, vendor inventory, and tested recovery where applicable.
- **Twilio gate:** qualifying Security or Enterprise Edition, executed BAA, HIPAA-enabled eligible services/configuration, A2P sender/campaign approval per practice, documented opt-in methods, STOP/HELP handling, signed webhooks, and test evidence.
- **Synchronizer gate:** exact PMS/version support, patient/availability/booking/status tests, commercial terms, BAA, support path, and per-location pricing verified before commitment.
- **Main-line gap:** direct dials are invisible and excluded from reporting. Reports say so.
- **No recurring manual workaround:** hold slots and staff transcription are not alternate v1 models.

## OUT — explicitly deferred

| Deferred | Why |
|---|---|
| Open-ended AI receptionist / call answering | Broader conversation is v2, not the bounded v1 IVR |
| Call recording or retained/raw transcription | Not required for bounded routing and outside the approved persistence boundary |
| Existing-patient self-service | V1 exposes only a new-patient visit |
| Porting/replacing the main number | Highest-risk change for a new relationship |
| No-show recovery | No approved lever beyond reminders |
| Referral lead/booking/show attribution | Unique return token/code is v2 |
| Reactivation, nurture, CRM routing | Broader patient/lead workflows come after core proof |
| Tiering or outcome pricing | Requires cohort evidence |
| Lead generation, ads, SEO | We convert demand; we do not create it |
| Multi-location, other verticals, self-serve | One operated dental package first |

## Honest gaps

- Patients who dial the main line directly are invisible; no backfill exists for calls we did not route.
- Existing patients are not self-scheduled in v1.
- A caller who declines SMS or cannot use the secure form needs the office for new-patient identity completion.
- Synchronizer outage converts all channels to request-to-book; the practice has up to 24 clock hours to respond.
- No-show recovery is absent even though show rate is reported.
- The referral ask is measurable, but referral bookings/shows are not attributed until v2.
- Vendor pricing and exact PMS operations must be verified before quoting or signing.

## Pricing

| Component | v1 | Notes |
|---|---|---|
| Implementation fee | $3,500–8,500 | Existing page vs. new build and calendar complexity |
| Managed service retainer | $750–2,000/mo | One package, 3-month minimum |
| SMS allowance | ~500 segments/mo included | Overage at cost |
| Tracking number + IVR | Included | Usage above allowance at contracted cost |
| A2P 10DLC | Included operationally | Registration and recurring carrier/vendor fees passed through as agreed |
| Synchronizer | Passed through at contracted usage cost | Verify current terms and qualified-practice usage |
| Hosting + calendar | Included | Provider choice belongs to the technical baseline |

The retainer is justified by the operating rhythm and reporting, not vendor minutes.

## Objectives through 2026-10-31

1. Three practices signed and paying implementation fees.
2. Every client live with all six pillars within 14 days of signing, including Synchronizer validation, Twilio/BAA readiness, A2P approval, consent tests, and referral-status support.
3. Median speed-to-lead under five minutes for 30 consecutive days.
4. Two of the first three clients still paying past month three.
5. Zero missed monthly reports.

## Launch inputs still required per client

- Exact PMS/version and required Synchronizer operation evidence.
- New-patient label, duration, provider, operatory, hours, timezone, and completed/kept status mapping.
- Main line, transfer schedule, 10–30-second ring value, and practice-approved emergency wording/number.
- Practice recipients for request-to-book SMS/email and acknowledgment of the 24-clock-hour response commitment.
- Twilio/A2P/BAA and state-specific compliance evidence.
- Ad-spend input if cost per booked appointment will be reported.

*Derived from `steering/product.md`, `out/flows.md`, and authoritative `out/scope.md`; corrected and approved 2026-09-22 under `specs/2026-09-21-v1-booking-phone-contract.md`.*
