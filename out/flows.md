# Product flows

Three views of the same v1 product: what the patient experiences, what we install, and what we operate. `scope.md` owns the current offer; these diagrams explain it. If a diagram implies different coverage, the diagram is wrong.

Mermaid source is edited directly. Red dashed nodes mark honest gaps or fallback states.

---

## Layer 1 — Patient journey

```mermaid
flowchart TD
    SITE["Practice website<br/>or Google listing"]
    WOM["Word of mouth"]
    CHOICE{"How does the patient<br/>reach out?"}
    DIRECT["Practice main line directly<br/>INVISIBLE TO US"]

    SITE --> CHOICE
    WOM --> CHOICE
    CHOICE -->|"Web calendar/form"| WEB["Secure new-patient form + SMS choice<br/>+ live tentative slot"]
    CHOICE -->|"Texts tracking number"| SMS["Conversational SMS"]
    CHOICE -->|"Calls tracking number"| LANG["Language<br/>English / 9 Español"]
    CHOICE -->|"Calls main line"| DIRECT

    LANG --> CONSENT{"Optional SMS consent<br/>scheduling + reminders + follow-up"}
    CONSENT -->|"Yes: record evidence"| MENU{"1 book · 2 office<br/>speech equivalents"}
    CONSENT -->|"No: continue without SMS"| MENU

    MENU -->|"Book"| PREF["Preferred date/time"]
    MENU -->|"Office / global 0"| TRANSFER["Warm transfer<br/>20s default · 10–30s configured"]
    PREF --> SLOTS["2–3 live Synchronizer slots"]
    SLOTS --> TENTATIVE["Tentative selection"]
    TENTATIVE --> LOOKUP{"Phone candidates +<br/>DTMF date of birth"}
    LOOKUP -->|"Unique existing + first-name yes"| TRANSFER
    LOOKUP -->|"New / unresolved + SMS consent"| SECURE["Short-lived secure form:<br/>name · email · phone · DOB · gender"]
    LOOKUP -->|"No SMS / cannot use form"| TRANSFER
    SECURE --> MATCH{"Match before create"}
    MATCH -->|"Ambiguous / conflicting"| REVIEW["Front-desk secure review task"]
    MATCH -->|"Verified no-match"| CREATE["Idempotent patient create"]
    MATCH -->|"Unique match"| PATIENT["Verified patient ID"]
    CREATE --> PATIENT

    WEB --> MATCH
    SMS --> PREF
    PATIENT --> WRITE["Revalidate slot +<br/>idempotent PMS write"]
    WRITE -->|"Success"| BOOKED(["Appointment booked"])
    WRITE -->|"Conflict"| SLOTS

    TRANSFER --> AMD{"Twilio transfer outcome"}
    AMD -->|"human"| HUMAN["Front desk handles call"]
    AMD -->|"machine / fax / busy /<br/>no-answer / failed / canceled /<br/>unknown / timeout"| UNAVAILABLE["Unavailable fallback"]
    UNAVAILABLE --> FOLLOW{"Valid SMS consent?"}
    FOLLOW -->|"Yes"| SMS
    FOLLOW -->|"No"| END["No automated text"]

    BOOKED --> CONF["Confirmation SMS"]
    CONF --> REM["Reminder SMS"]
    REM --> STATUS{"PMS appointment status"}
    STATUS -->|"completed / kept"| REF["Basic referral ask"]
    STATUS -->|"no-show"| NOSHOW["NO RECOVERY PATH IN V1"]
    STATUS -->|"canceled / unknown"| NOREF["No referral ask"]

    OUTAGE["Synchronizer circuit open:<br/>request-to-book · 24h response"]
    REVIEW --> TASK
    WRITE -.-> OUTAGE
    OUTAGE --> TASK["Opaque task ID + secure link<br/>to front desk by SMS + email"]

    classDef gap stroke:#c0392b,stroke-width:2px,stroke-dasharray:5
    class DIRECT,NOSHOW,OUTAGE,END gap
```

**Booking is a PMS fact, not a message.** A selected slot is tentative until the patient identity step completes, the slot is revalidated, and the PMS write succeeds. Patient creation and appointment creation have separate idempotency protection.

**Phone is candidate discovery, not identity proof.** Caller ID may locate possible records but never selects one. DTMF date of birth must reduce the set to one candidate before the IVR says only the first name. A verified existing patient goes to the office because v1 exposes one new-patient visit, not existing-patient self-service.

**Consent precedes automated text.** The IVR asks immediately after language selection. Declining cannot block booking or office transfer, but it means an abandoned call cannot receive a text-back. The consent event stores the practice, number, call ID, time, language, disclosure version, response method/value, message subjects, and current state.

**Speech is bounded but real.** Twilio speech recognition can produce a transient raw `SpeechResult`. Sequence Bridge uses it in memory for the approved English/Spanish commands and bounded date/time or numeric selections, then stores only normalized results. Raw call audio and raw speech are absent from our persistence and logs.

**The main-line gap remains.** Calls made directly to the practice's line are invisible and excluded from “calls managed through Sequence Bridge.” The tracking number covers the landing page and Google Business Profile, the channels we manage.

---

## Layer 2 — What we install

```mermaid
flowchart TB
    subgraph platform["Sequence Bridge platform — multi-tenant, operated by us"]
        direction TB
        LP["Landing page / embed"]
        FORM["Secure patient + lead forms"]
        TRACK["Tracking number + bounded IVR"]
        CONSENT["Consent + suppression ledger"]
        ID["Patient matching / idempotency"]
        BOOK["Booking engine<br/>web · SMS · IVR"]
        TASK["Request-to-book workbench"]
        AUTO["Confirm · remind · referral ask · follow-up"]
        EVENTS[("Minimum event + audit store")]
        DASH["Operator dashboard + monthly report"]
    end

    subgraph vendors["Approved vendors / practice systems"]
        direction TB
        TWILIO["Twilio Voice + Messaging + Speech + AMD"]
        A2P["A2P sender/campaign per practice"]
        EMAIL["Email provider"]
        SYNC["NexHealth Synchronizer"]
        PMS["Practice PMS<br/>system of record"]
        PHONE["Practice main line<br/>unchanged"]
    end

    LP --> FORM
    LP --> BOOK
    FORM --> CONSENT
    FORM --> ID
    TRACK --> TWILIO
    TRACK --> CONSENT
    TRACK --> ID
    TRACK --> BOOK
    TRACK -->|"human route"| PHONE
    TRACK -.->|"primary handler fails:<br/>provider-hosted forwarding"| PHONE
    ID <--> SYNC
    BOOK <--> SYNC
    SYNC <--> PMS
    BOOK --> AUTO
    BOOK -.->|"circuit open"| TASK
    TASK --> TWILIO
    TASK --> EMAIL
    CONSENT --> AUTO
    AUTO --> TWILIO
    A2P -.->|"gates SMS"| TWILIO
    ID --> EVENTS
    BOOK --> EVENTS
    TRACK --> EVENTS
    AUTO --> EVENTS
    TASK --> EVENTS
    EVENTS --> DASH
```

**Minimum-data boundary.** Scheduling and messaging may be PHI even though v1 prohibits clinical intake. Persisted data is limited to identity/contact fields, date of birth, the PMS-required gender value, consent/suppression evidence, the configured generic appointment type, scheduling preferences/logistics/status, normalized IVR results, provider references, delivery/call events, tenant ownership, and audit fields. Clinical details, charts, insurance, billing, recordings, raw speech/transcripts, and unrestricted notes do not belong in the platform.

**Production is contract- and configuration-gated.** Twilio requires the qualifying commercial edition, executed BAA, HIPAA-enabled eligible-service configuration, A2P approval, consent/opt-out evidence, and signed webhooks. Synchronizer requires exact PMS/version and operation tests, commercial terms, BAA, support path, and completed/kept status support. Hosting, logging, monitoring, analytics, support, and backup paths are in the same data-flow review.

**Synchronizer fails closed for booking.** Three transient failures in two minutes or one auth/permission failure opens the tenant circuit. All channels stop showing live slots and create request-to-book. The patient is acknowledged immediately and promised a response within 24 clock hours; notifications carry only an opaque task ID and secure link. Recovery checks back off at 1, 2, 5, then 10 minutes and close after three successes. Queued requests never replay automatically.

**Twilio callbacks are idempotent.** Delayed or reordered AMD/call/message callbacks cannot bridge after fallback, duplicate a task, send a second follow-up, or create another booking.

---

## Layer 3 — Client lifecycle

```mermaid
flowchart TD
    SALE["Discovery + sale<br/>baseline calls vs. forms · PMS/version · state"] --> SIGN(["Contract signed<br/>implementation fee + retainer"])

    SIGN --> PAGE["Landing page / embed"]
    SIGN --> SYNC["Synchronizer agreement + BAA<br/>install · map · test operations"]
    SIGN --> TWILIO["Twilio edition + BAA<br/>eligible services · webhooks"]
    SIGN --> A2P["A2P brand/campaign + sender<br/>start at signing"]
    SIGN --> CONFIG["New-patient mapping · status ·<br/>main line · ring time · urgent wording"]
    SIGN --> COPY["English/Spanish IVR +<br/>consent · SMS · outage copy"]
    SIGN --> COMPLY["Counsel/state review · data map ·<br/>retention · security controls"]

    READY{"Every launch gate passes?"}
    PAGE --> READY
    SYNC --> READY
    TWILIO --> READY
    A2P --> READY
    CONFIG --> READY
    COPY --> READY
    COMPLY --> READY
    READY -->|"No"| BLOCK["Not live; no partial package"]
    READY -->|"Yes"| TEST["Synthetic end-to-end tests:<br/>all six pillars + failure paths"]
    TEST --> LAUNCH(["Live in production<br/>target: ≤14 days"])

    subgraph rhythm["Weekly operating rhythm"]
        direction TB
        INSPECT["Inspect failed automation +<br/>provider health + unresolved tasks"]
        METRICS["Check speed-to-lead · booking · show ·<br/>calls managed · referral-ask engagement"]
        FIX["Fix failures and tune approved configuration"]
        INSPECT --> METRICS --> FIX
    end

    LAUNCH --> INSPECT
    FIX --> MONTH{"Month end?"}
    MONTH -->|"No"| INSPECT
    MONTH -->|"Yes"| REPORT["Monthly report<br/>explicit denominators + gaps"]
    REPORT --> M3{"Past month 3?"}
    M3 -->|"No"| INSPECT
    M3 -->|"Yes"| RENEW{"Renews?"}
    RENEW -->|"Yes"| INSPECT
    RENEW -->|"No"| CHURN["Churn"]
```

**Launch is all-or-nothing.** The referral ask is implemented after the core booking/transfer/reminder/reporting paths but must still pass before the first client is live. A reliable completed/kept status is part of qualification; recurring front-desk work is not an acceptable substitute.

**The 14-day target has real long poles.** A2P, Twilio BAA/account readiness, Synchronizer provisioning, exact PMS tests, practice approvals, and state/compliance review begin as early as possible. The target never overrides a failed production gate.

**The report proves only what v1 can attribute.** It reports the managed-channel funnel and referral-ask engagement, not direct main-line calls or referred bookings/shows. Cost per booked appointment appears only when the practice supplies spend.

---

## Resolved contracts and remaining product gaps

1. **PMS:** Synchronizer is the only normal path. Initial targets are Dentrix, Eaglesoft, Open Dental, Curve Hero, and Dentrix Ascend, subject to exact operation/version qualification. Hold slots are retired.
2. **Patient:** phone is candidate lookup only; DTMF DOB precedes first-name confirmation; new-patient identity uses the secure form; ambiguous records are never auto-merged.
3. **Phone:** every v1 client receives the bounded bilingual IVR, early optional SMS consent, deterministic warm transfer, one consented follow-up, and provider-hosted voice fallback.
4. **Speech:** transient Twilio recognition is in; raw audio/transcript persistence and open-ended conversation are out.
5. **Referral:** the basic completed-visit ask is in and measured through click/reply; booking/show attribution is v2.
6. **No-show recovery:** not in v1 even though show rate is reported.
7. **Cost input:** practice-supplied spend is required before cost per booked appointment can be shown.
