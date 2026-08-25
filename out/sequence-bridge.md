## Sequence Bridge 
A productized-services company selling operating results. We want to sell good business outcomes using software and AI to help small companies advance their conversions, revenue and margins using technological sequences.   

* We don't do bespoke software or "AI custom development"
* Reusable across verticals later


## Initial market test

> *Original brain dump, kept unedited as a record of the initial thinking. Figures and constraints here are historical — superseded by [`internal/steering/scope.md`](internal/steering/scope.md).*

* Sequence:
    * Landing page (the client can have an existing page or we create a single landing page for them) 
    * Booking system via the landing page or calls + SMS reminders and confirmations 
    * referral component after being serviced
* Details / constraints
    * Lean reusable stack: landing-page + form + calendar + SMS provider + call handling + lightweight lead DB/CRM + automation + back office (lead/appointment metrics or reporting) + booking availabilities. 
    * Business owners might not be tech-savvy, so settings can be managed through SMS
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

> **This document is the origin story and the narrative, not the spec.** The authoritative statement of what's in v1, what's out, and what it costs is `internal/steering/scope.md`. Where this file and `scope.md` disagree, `scope.md` wins. Descriptions below are kept because they read well for a pitch; they are not the contract.

### One-liner

Sequence Bridge installs a proven booking sequence in your dental practice so every lead becomes a booked appointment that shows up.

### Positioning

A productized-services company selling operating results, not software projects.

* Not an agency, not custom development: fixed scope, fixed price, one vertical at a time.
* We don't generate leads — we stop practices from leaking the ones they already get. The promise is conversion: every lead and call gets an instant response, gets booked, gets reminded, and shows up.
* The sequence is reusable across verticals later; dentists are the first market test.

### The Sequence (the v1 product)

1. **Landing page** — the practice's existing page, or a single conversion-focused page we create.
2. **Call capture** — a tracking number published on the landing page and Google Business Profile that forwards to the practice's existing line, counts every call, and fires a missed-call text-back with a booking link when nobody picks up. The practice's main number is not touched or ported.
3. **Booking** — appointment scheduling against the practice's real availability, reachable from the landing page or calls.
4. **SMS confirmations + reminders** — automated, timed to cut no-shows.
5. **Referral ask** — post-visit SMS asking the serviced patient for a referral.

### Who it's for

* US/Canada dental practices, starting with 2–3 pilot clients.
* No regulated health data (PHI) in v1: we collect only contact and booking-preference info; clinical details stay in the practice's existing PMS.
* A2P 10DLC registration is a required onboarding step for each client's SMS number.

### Pricing

Single package, sold as a fixed implementation fee plus a monthly managed-service retainer on a minimum term. The retainer is justified by the weekly operating rhythm — failed-automation inspection, lead and appointment metrics, and a concise monthly client report.

**Current figures live in [`internal/steering/scope.md`](internal/steering/scope.md).** They are deliberately not repeated here: a stale price in the origin doc is how a wrong number reaches a proposal.

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

### What's deferred

The authoritative deferral list — every capability explicitly out of the current offer, with the reason for each — lives in [`internal/steering/scope.md`](internal/steering/scope.md).

It used to be duplicated here. That duplication is what let the missed-call contradiction survive from 2026-07-31 to 2026-08-10: this file deferred missed-call text-back while `product.md` led its key-problems list with unanswered calls. One list, one home.






