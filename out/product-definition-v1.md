# Product Definition v1 — Sequence Bridge

**Source:** Derived from `internal/steering/product.md` (WHY) + `internal/steering/flows.md` (HOW). Authoritative in/out + pricing lives in `internal/steering/scope.md` — where this file and that file disagree, that file wins. This file is a rendering for reading/sharing, next to `internal/out/flows.html`.

**One-liner:** We install a proven booking sequence in your dental practice so every lead becomes a booked appointment that shows up.

**Status:** Offer v1 · Declared 2026-08-10 · Single package, no tiers · PMS sync via Synchronizer (https://synchronizer.io)

---

## Who it's for

* Single-location general dentistry, 1–4 chairs, owner-operator, no marketer on staff
* Front desk = 1–2 people, busy in-chair — bar is "must not add work"
* Already has inquiry volume (website, Google Business Profile, word of mouth) — we convert demand, we don't create it

**Not for:** multi-location, specialty, needs PHI/clinical data in v1, wants lead gen/ads/SEO, wants self-serve software.

## The promise

Every new lead hears back in under 5 minutes, every booked patient gets reminded, every month you get one report proving what it produced.

## Problems we solve

1. Calls go unanswered during procedures / after hours → caller books elsewhere
2. Web forms sit unread for hours/days
3. Booked patients no-show
4. Owner has no numbers (inquiries → bookings → shows)
5. Tools exist but require the practice to operate them

---

## IN — v1 delivers all 6 to every client (no à la carte)

### 1. Landing page
The practice's existing site with our form embedded, **or** a single conversion-focused page we build and host. One template, themed per client.

### 2. Call capture — IVR front door (2 routes)
All calls to the tracking number (landing page + GBP) are answered first by our IVR.

**Route 1 — Default (auto-book):** IVR greets → asks preferred date/time → reads real availability via **NexHealth Synchronizer (https://synchronizer.io)** → offers 2–3 slots → caller confirms → writes booking directly to PMS → fires SMS confirmation. No clinic staff involved.

**Route 2 — Human on demand:** At any point caller can `Press 0` or say `“talk to a human” / “agent” / “representative”` → **warm transfer** to clinic main line (we stay on for 20–25s, detect answer; if answered → bridge caller + clinic and drop off; if not answered/voicemail → pull caller back and fire abandoned-IVR text-back). Practice main line never ported — additive, fails safe.

> **Metrics — “Calls managed through us” (report this, not “total calls”):**
> `Total IVR answered → Auto-booked + Rerouted-and-booked + Abandoned → text-back → booked`. Provable and auditable. Footnote every report: *“Direct dials to your main line are not routed through us in v1 and are not counted here — tracking number covers landing page + GBP, the channels we are accountable for. No backfill for calls we didn’t count.”* Capture 1-time baseline at discovery (`Last month: __ forms vs __ calls` from phone provider) to anchor scale.

### 3. Booking — 3 channels, one PMS via Synchronizer (https://synchronizer.io)
One PMS of record, three entry points — all write via Synchronizer and all trigger same SMS confirm/reminder/referral sequence.

1. **Call** — IVR auto-book (Route 1) or warm-transfer to human → front desk books via Synchronizer (Route 2)
2. **Calendar on website** — embedded calendar on landing page → Synchronizer write, instant confirmation
3. **SMS (conversational)** — standalone bookable channel: patient texts preferred time → we reply with 2–3 Synchronizer availabilities → patient replies `1/2/3` → we book → confirm. Also used for abandoned-IVR text-back.

* **How it connects:** Synchronizer agent installed + PMS authorization at onboarding. Booking engine reads free/busy and writes confirmed appointments as source of truth — no shadow calendar.
* **Why it matters:** Eliminates manual re-entry and double-booking risk. Front desk does no transcription.
* **Coverage:** Synchronizer supports Dentrix, Eaglesoft, Open Dental, Curve, Denticon, CareStack — verify exact coverage + per-location fee before committing. Unsupported PMS falls back to hold slots (reserved new-patient slots blocked in PMS) or request-to-book, decided at discovery.
* **Data boundary:** We sync availability + booking writes only; clinical records/charting/billing stay in PMS. Still contact + booking-preference only — preserves no-PHI posture, subject to Synchronizer's HIPAA handling.

### 4. SMS confirmations + reminders
Automated, timed to cut no-shows. Every outbound template includes opt-out language.

### 5. Referral ask
Post-visit SMS asking the serviced patient for a referral.

### 6. Operated + reported
We run the weekly failed-automation inspection + metrics check and deliver one monthly client report. This is not a bonus — it is what the retainer buys.

**Metrics reported:** speed-to-lead, booking rate, show rate, cost per booked appointment (when practice shares ad spend).

## Constraints that bound v1

* **No PHI.** Contact + booking-preference only. Clinical detail stays in the practice's PMS — keeps first cohort out of HIPAA scope. Synchronizer is the transport for availability/booking, not a clinical data store.
* **A2P 10DLC** required per client SMS number (US/Canada). Start at signing, not at launch. It is the long pole for the 14-day launch target. Voice needs no 10DLC. Synchronizer needs no 10DLC.
* **Main line never touched.** Our number is additive; if it breaks, their old number still rings.
* **Synchronizer + IVR dependency.** Per-location Synchronizer fee, vendor availability, agent health, and IVR minutes are now on the critical path — add Synchronizer health + IVR warm-transfer success rate to weekly inspection; define outage fallback (abandoned-IVR → SMS conversational booking).
* **Practice responsibilities:** authorize + install Synchronizer agent and keep PMS availability accurate, share ad spend if they want cost-per-booked. No manual re-entry of bookings. No staff needed to answer IVR-booked calls.

---

## OUT — explicitly deferred for v1

| Deferred | Why |
|---|---|
| Call answering / AI receptionist | Own product, not a feature |
| Call recording + transcription | Breaks no-PHI constraint |
| Porting / replacing the main number | Highest-risk change; leaves main-line calls unmeasured — gap we accept |
| No-show recovery | No defined lever after reminder fails (open question 3) |
| Referral attribution tagging | Return path not tagged — value unprovable (open question 4) |
| Reactivation campaigns (dormant lists) | Needs patient list — PHI-adjacent |
| Multi-step nurture + lead-source tracking | One sequence first; prove metrics |
| CRM integration + routing rules | No client has asked |
| Settings management via SMS | We operate it; practice configures nothing in v1 |
| Launch / Growth / Scale tiering | Single package until pricing data |
| Outcome-based pricing (per booking / per show) | Can't price honestly without cohort data (open question 5) |
| Lead generation, ads, SEO | We convert demand, we don't create it |
| Expansion beyond dentistry | One vertical at a time |
| Self-serve product | Not the bet |

> **Moved IN:** Direct PMS read/write via **NexHealth Synchronizer (https://synchronizer.io)** is now **IN for v1** — real availability read + booking write. Hold slots remain only as fallback for unsupported PMS.

## Honest gaps (say out loud in the sale — red dashed in flows.md)

* **Main-line gap — Summary: we report only what we can prove.** Patients who dial the practice's main line directly are invisible to us — we publish the tracking number on landing page + GBP only; porting the main line is deferred as highest-risk
* No-show has no recovery path in v1 — we still report show rate
* Referral return via same untagged form is unattributed
* Synchronizer dependency — PMS sync outage reverts to SMS conversational booking; unsupported PMS reverts to hold slots (capture PMS brand at discovery to plan coverage)
* **IVR gap — Summary: abandoned IVR is not lost.** Hang-ups/timeouts/warm-transfer no-answer pull back to abandoned-IVR text-back → SMS channel; we still count them in “calls managed”
* Synchronizer per-location fee + IVR minutes are passed through at cost — verify current pricing before quoting

## Pricing (from scope.md) — with pass-through to assess margin

| Component | v1 | Cheapest | Best | Notes |
|---|---|---|---|---|
| Implementation fee | $3,500–$8,500 | — | — | Existing page vs new build + calendar complexity |
| Managed service retainer | $750–$2,000/mo | — | — | Single package, 3-mo minimum |
| SMS allowance | ~500 segments/mo included; overages at cost | ~$6/mo | ~$12/mo | Twilio $0.0075/seg + $10/mo A2P amortized; best = ~800 seg with conversational turns |
| Tracking number | included | $1.15/mo | $1.15/mo | Twilio local number |
| Voice — IVR + warm transfer | included | ~$8/mo | ~$25/mo | ~150 min @ $0.022 + 1 extra leg for warm transfer; best = neural voice + speech rec. |
| A2P 10DLC | included | $15 one-time + $10/mo | $15 + $10 | Gates SMS only |
| Synchronizer fee | passed through at cost | est. $99-149/mo | est. $199-249/mo | Per-location, verify at https://synchronizer.io |
| Hosting + calendar | included | $0 | $20 | Vercel free vs Pro |
| **Total pass-through / mo** | — | **~$124-184** | **~$267-317** | Before your weekly ops time |
| **Gross margin @ $750 / $2,000 retainer** | — | **77-85% / 91-94%** | **58-64% / 84-87%** | Cheapest proves margin, best proves quality |

> SMS overages + Synchronizer fee + IVR minutes are true pass-through — retainer justification is the operating rhythm + reporting, not the minutes themselves.

## Objectives through 2026-10-31 (first 90 days)

1. 3 practices signed + paying implementation fee
2. Every client live ≤14 days from signing (now includes Synchronizer provisioning + PMS authorization)
3. Median speed-to-lead <5 min for 30 consecutive days
4. 2 of 3 still paying past month 3 (key success metric)
5. Zero missed monthly reports

## Open questions affecting this scope

1. **PMS handoff — Decided: Synchronizer for v1.** Verify Synchronizer terms/pricing and exact PMS coverage (Dentrix/Eaglesoft/Open Dental/Curve/Denticon/CareStack) before committing. Capture PMS brand on every discovery call; define fallback (hold slots vs request-to-book) for unsupported systems. Hold-slot model retired as primary, retained only as fallback.
2. **Phone depth** — tracking number + missed-call in v1, pending confirmation via 3 discovery baselines (reverses to form-only if calls are minority)
3. **No-show recovery**
4. **Referral attribution**
5. **Cost-per-booked input**

---

*Derived from `internal/steering/product.md` + `internal/steering/flows.md` + `internal/steering/scope.md` on 2026-08-20, updated 2026-08-20 for Synchronizer (https://synchronizer.io). `internal/out/flows.html` is the visual companion — update its Layer 2 PMS edge from `handoff undefined` to `Synchronizer sync (read availability / write booking)` when regenerating the HTML.*
