# Scope — offer v1

**Status:** current · **Offer version:** v1 · **Declared:** 2026-09-22

The single authoritative statement of what Sequence Bridge sells: what's in, what's out, what it costs, and what bounds it. If another document contradicts this one, this one wins and the other gets fixed.

**`v1` is an offer version, not a code release.** It names what a client signs up for. Code releases may ship many times without changing the offer version. Before a future offer change, freeze this file as a dated record in `releases/`; a historical draft must not present itself as the current v1.

---

## In scope

The installed sequence. All six pillars are delivered to every v1 client; there is no à la carte or partial launch.

1. **Landing page** — the practice's existing page with our form/calendar embedded, or a single conversion-focused page we build and host. One template, themed per client.
2. **Call capture** — a bounded English/Spanish IVR on a tracking number published on the landing page and Google Business Profile. It offers new-patient booking or warm transfer to the unchanged practice line. With valid SMS consent, an abandoned booking or unsuccessful transfer receives one follow-up into conversational booking.
3. **Booking** — one `New patient visit` mapping against real PMS availability through embedded web calendar, conversational SMS, and IVR. All three use NexHealth Synchronizer for the required patient, availability, appointment, and status operations; confirm only after a successful PMS write; and use the same matching, conflict, consent, and idempotency rules.
4. **SMS confirmations and reminders** — automated, consented, and timed to cut no-shows, with sender identification, delivery events, and opt-out handling.
5. **Referral ask** — post-visit SMS only after a completed/kept appointment status. V1 reports ask eligibility, sent, delivery, reply, and link click; it does not claim an attributed referral booking or show.
6. **Operated and reported** — we run the weekly failed-automation inspection and metrics check and deliver one monthly client report. This operating rhythm is what the retainer buys.

## Out of scope for v1

| Deferred | Why |
|---|---|
| Open-ended call answering / AI receptionist | V1 has bounded booking/transfer intents, not general conversation or clinical triage |
| Call recording or retained/raw speech transcription | V1 permits transient Twilio speech recognition but stores only normalized commands and selections |
| Porting or replacing the practice's main number | Highest-risk change for a new client; direct calls to that line remain an explicit reporting gap |
| Existing-patient self-service booking | V1 auto-booking exposes one new-patient appointment type; verified existing patients go to the office/request-to-book |
| No-show recovery | Reminders and show-rate reporting are included; a recovery sequence is not |
| Referral lead/booking/show attribution | V1 measures the ask and engagement only; unique referral code/link attribution is v2 |
| Reactivation campaigns against dormant patient lists | Requires a broader patient-list data flow |
| Multi-step nurture and lead-source tracking | One sequence first; prove the core metrics |
| Launch / Growth / Scale tiering | Single package until pricing data exists |
| CRM integration and routing rules | No validated client demand yet |
| Outcome-based pricing | First-cohort outcome and cost data is required before pricing honestly |
| Settings management over SMS | We operate the system; the practice does not configure it over SMS |
| Expansion beyond dentistry | One vertical at a time |
| Self-serve product | Not the v1 bet |
| Lead generation, ads, or SEO | We convert existing demand; we do not create it |

## Constraints that bound v1

- **All six pillars for every client.** A practice that cannot support the complete contract does not qualify for v1.
- **Initial PMS targets.** Dentrix, Eaglesoft, Open Dental, Curve Hero, and Dentrix Ascend are the operating priority list. Exact product/version and required Synchronizer operations must be verified before signing or launch; this is not an unqualified market-share claim.
- **One appointment type.** Each practice configures one `New patient visit` label, duration, provider, operatory, and PMS mapping. V1 does not ask for symptoms, treatment reason, or procedure to choose a type.
- **Patient identity is not phone-only.** Phone finds candidates. IVR requires DTMF date of birth before confirming only a first name. New-patient name, email, phone, date of birth, and PMS-required gender value are collected in a secure web form. Ambiguous matches go to front-desk review; patient and appointment creation are separately idempotent.
- **No clinical intake, not “no PHI.”** Scheduling and messaging may be PHI. Clinical details, charts, procedure detail beyond the generic appointment type, recordings, persisted raw speech/transcripts, insurance, claims, billing, and unrestricted free text are prohibited. Production requires counsel-reviewed data/retention rules, applicable BAAs, minimum-necessary controls, and the approved security baseline.
- **Speech is transient and bounded.** Twilio may produce a transient `SpeechResult`; Sequence Bridge stores only normalized language, intent, date/time or numeric selection, and provider confidence/status. Broader conversational voice AI remains v2.
- **SMS consent comes first.** After language selection, the IVR asks optional consent for scheduling, confirmation, reminder, and disconnected-call follow-up texts. Declining cannot block booking or transfer. Without documented consent, no automated outbound SMS is sent.
- **A2P 10DLC and Twilio compliance gate SMS.** Registration starts at signing. Production also requires the qualifying Twilio edition, executed BAA, approved HIPAA-eligible configuration, per-practice sender/campaign, and tested STOP/HELP handling.
- **The main phone line is unchanged.** The tracking number is additive. Main-menu `1` books and `2` reaches the office; `0` is the global human escape after the main menu. English and Spanish are supported.
- **Warm transfer is bounded.** The practice leg rings 20 seconds by default, configurable from 10–30 seconds. Only the configured provider `human` outcome bridges. Machine/fax, busy, no-answer, failed, canceled, unknown, timeout, and caller hang-up follow the approved safe fallback. Duplicate or delayed callbacks cannot send or bridge twice.
- **Synchronizer is the only normal booking path.** Hold slots are not a parallel model. Unsupported practices are deferred.
- **Safe booking outage mode.** Three transient provider failures within two minutes, or one authentication/permission failure, opens the per-tenant circuit. All channels switch to request-to-book; the front desk receives SMS and email with only an opaque task ID and secure link; the patient is promised a response within 24 clock hours. Nothing queued is replayed automatically.
- **Safe voice outage mode.** If the primary IVR handler fails, a provider-hosted direct-forwarding fallback sends the call to the practice main line.
- **Referral has a narrow truth claim.** The basic ask is mandatory in v1, but referred bookings/shows are not attributed until v2.

## Pricing

| Component | v1 |
|---|---|
| Implementation fee | $3,500–8,500, depending on existing page vs. new build and calendar complexity |
| Managed service retainer | $750–2,000/mo |
| Minimum term | 3 months |
| SMS allowance | ~500 segments/mo included; overages passed through at cost |
| Synchronizer | Passed through at contracted usage cost; verify current terms before quoting |
| Tracking number and IVR usage | Included in the managed experience; usage above the agreed allowance passed through at cost |

Single package. No tiers in v1.

## Client responsibilities

- Authorize and install Synchronizer and complete exact PMS/version/operation validation.
- Keep PMS availability, new-patient appointment mapping, and appointment statuses accurate.
- Keep the main line operational and approve its warm-transfer schedule and 10–30-second ring value.
- Approve English/Spanish IVR, SMS, consent, opt-out, emergency, and outage copy.
- Assign front-desk recipients for request-to-book SMS/email tasks and respond within 24 clock hours.
- Complete required BAAs, disclosures, A2P registration inputs, and state-specific production review.
- Share ad-spend figures if cost per booked appointment is included in the report.

## Resolved decisions and honest gaps

1. **Synchronizer is the sole v1 integration.** Unsupported PMSs are deferred; outages use request-to-book without automatic replay.
2. **IVR is mandatory for every v1 client.** It uses bounded DTMF/speech, transient speech processing, early optional SMS consent, new-patient booking, and human-on-demand transfer.
3. **Direct main-line calls remain invisible.** Reports count only calls received by the tracking number and state that denominator explicitly.
4. **Existing-patient self-service is not included.** Verified existing patients go to the office/request-to-book because v1 exposes only a new-patient visit.
5. **No-show recovery remains open for a later version.** V1 still reports show rate.
6. **Referral attribution is deliberately deferred.** V1 proves that the ask was eligible, sent, delivered, replied to, or clicked; v2 may add an opaque short link/code and `referral_lead`, `referral_booking`, and `referral_show`.
7. **Cost per booked appointment depends on client-supplied spend.** Do not promise it when the practice will not provide the input.

## Production gates

Before the first real patient interaction, the practice must pass the launch gates in `specs/2026-09-21-v1-booking-phone-contract.md`, including PMS operation tests, completed/kept status support, Twilio edition/BAA and eligible-service configuration, A2P approval, consent/opt-out tests, security risk review, data/retention approval, and state-law review for the pilot location.

## Version history

| Record | Declared | Location |
|---|---|---|
| Pre-resolution draft | 2026-08-10 | `releases/scope-pre-resolution-2026-09-21.md` |
| v1 | 2026-09-22 | current — this file |
