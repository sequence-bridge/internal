## Sequence Bridge 
A productized-services company selling operating results. We want to sell good business outcomes using software and AI to help small companies advance their conversions, revenue and margins using technological sequences.   

* We don't do bespoke sofware or "AI custom development"
* Reusable across verticals later


## Initial market test

* Sequence:
    * Landing page (the client can have an existing page or we create a single landing page for them) 
    * Booking system via the landing page or calls + SMS reminders and confirmations 
    * referral component after being serviced
* Details / constraints
    * Lean reusable stack: landing-page + form + calendar + SMS provider + call handling + lightweight lead DB/CRM + automation + back office (lead/appointment metrics or reporting) + booking availabilities. 
    * Business owners might not be tech savy so setttings can be managed through sms
    * Must support the weekly operating rhythm: failed-automation inspection, lead/appointment metrics (speed-to-lead, booking rate, show rate, reactivation bookings, cost per booked appointment), and a concise client report — the monthly fee is justified by this.
    * Multi-client from day one (agency operates many practices), with Launch/Growth/Scale tiering: one SMS sequence → multi-step nurture + lead-source tracking → CRM integration + routing rules + multiple campaigns.
* Pricing
    * Sold as fixed-scope implementation (~$3.5k–$8.5k) plus recurring managed service ($750–$2,000/mo). Also it can be implementation fee + outcome based pricing. We would need to define how much per booking or show to service.
    * 3-month minimum
    * Usage costs (SMS, calendar, AI) passed through? To define this and create a solid pricing analysis.
* We want to start testing with a Dentist vertical
    * Avoid regulated health data in v1: collect only contact + booking-preference info; route clinical details to the practice's existing PMS.


---

## Polished business definition (drafted by Claude Fable 5, 2026-07-31)

### One-liner

Sequence Bridge installs a proven booking sequence in your dental practice so every lead becomes a booked appointment that shows up.

### Positioning

A productized-services company selling operating results, not software projects.

* Not an agency, not custom development: fixed scope, fixed price, one vertical at a time.
* We don't generate leads — we stop practices from leaking the ones they already get. The promise is conversion: every lead and call gets an instant response, gets booked, gets reminded, and shows up.
* The sequence is reusable across verticals later; dentists are the first market test.

### The Sequence (the v1 product)

1. **Landing page** — the practice's existing page, or a single conversion-focused page we create.
2. **Booking** — appointment scheduling against the practice's real availability, reachable from the landing page or calls.
3. **SMS confirmations + reminders** — automated, timed to cut no-shows.
4. **Referral ask** — post-visit SMS asking the serviced patient for a referral.

### Who it's for

* US/Canada dental practices, starting with 2–3 pilot clients.
* No regulated health data (PHI) in v1: we collect only contact and booking-preference info; clinical details stay in the practice's existing PMS.
* A2P 10DLC registration is a required onboarding step for each client's SMS number.

### Pricing (v1: single package)

* **Implementation fee:** $3.5k–$8.5k depending on scope (existing page vs. new landing page, calendar complexity).
* **Managed service retainer:** $750–$2,000/mo, 3-month minimum.
* The retainer is justified by the weekly operating rhythm: failed-automation inspection, lead/appointment metrics (speed-to-lead, booking rate, show rate, cost per booked appointment), and a concise monthly client report.
* **Usage costs:** retainer includes an SMS allowance (~500 segments/mo); overages passed through at cost. Keeps the sticker price clean without margin risk.

### The platform

One multi-tenant web app we own and operate for all client practices:

* Landing-page templates
* Booking engine with per-practice availability
* SMS sequences (Twilio)
* Lightweight lead DB / CRM
* Agency dashboard with per-client metrics and reporting

Near-zero marginal cost per added practice; the reporting that justifies the retainer is native to the platform.

### What we're testing (first 90 days)

* Sign 2–3 dentist practices.
* Prove the metrics move (speed-to-lead, booking rate, show rate).
* Validate that the retainer survives past the 3-month minimum.

### Roadmap (explicitly not v1)

* Missed-call text-back (instant SMS with booking link when the practice misses a call)
* Reactivation campaigns against dormant patient lists
* Multi-step nurture sequences + lead-source tracking
* Launch/Growth/Scale tiering and CRM integration / routing rules
* Outcome-based pricing (per booking or per show) — revisit once first-cohort data exists
* Settings management via SMS for non-tech-savvy owners
* Expansion to additional verticals






