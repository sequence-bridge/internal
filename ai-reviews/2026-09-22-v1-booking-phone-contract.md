# Adversarial review — v1 booking and phone contract

**Type:** Review
**Date:** 2026-09-22
**Branch:** `docs/v1-booking-phone-contract`
**Spec:** `specs/2026-09-21-v1-booking-phone-contract.md`
**Verdict:** Pass after approved correction — 0 P0, 0 P1, 0 P2
**Initial verdict:** Changes required — 0 P0, 5 P1, 1 P2

## Review scope

Reviewed the approved spec, current scope, canonical product, product definition, flows, frozen scope, app product, and app roadmap. Checked internal consistency, launchability of the six-pillar promise, data-boundary claims, Twilio IVR/warm-transfer feasibility, Synchronizer scheduling prerequisites, outage behavior, and metric attribution.

## Findings

### P1 — Speech IVR contradicts the prohibition on transcription

**Evidence:** The spec permits speech phrases and speech booking input while stating that v1 does not “transcribe” call audio (`specs/2026-09-21-v1-booking-phone-contract.md`, phone requirements 2–8). The canonical product, scope, product definition, and flows likewise prohibit transcripts or open-ended voice capture while promising spoken human-request phrases and preferred-time input.

Twilio's `<Gather input="speech">` performs speech-to-text and posts a `SpeechResult` containing the transcribed caller speech to the application. Restricting hints or vocabulary does not stop a caller from saying clinical information or stop Twilio from producing a transcript: https://www.twilio.com/docs/voice/twiml/gather

**Impact:** The documented data contract is false as written. The application and a speech-to-text subcontractor may process patient speech and a transcript while product and sales materials promise that transcription is out of scope. This affects the approved data map, vendor/BAA review, logging rules, retention, incident response, and patient-facing disclosure.

**Required resolution:** Choose and document one of two contracts before phone implementation:

1. DTMF-only booking and human escape, with no speech recognition; or
2. Explicitly permit transient, bounded speech transcription, define allowed processing/storage/logging and deletion behavior, include the speech-to-text provider in the compliance/BAA review, and specify what happens when a caller says clinical information.

Either choice changes approved behavior and requires a spec revision and reapproval before implementation changes.

### P1 — Synchronizer new-patient prerequisites are missing from the intake and data contract

**Evidence:** The contract says a new lead can book through web, SMS, or IVR using identity/contact details, a generic appointment type, and scheduling preferences. It does not identify the patient record that the PMS write targets, define patient matching/creation idempotency, or require date of birth and gender.

NexHealth's current patient documentation says that creating a new patient for booking requires first name, last name, email, phone, date of birth, and gender, and warns against duplicate patient creation: https://docs.nexhealth.com/reference/patients-1. Its scheduling guide says booking requires the patient, provider, appointment type, location, and time: https://docs.nexhealth.com/v3.1.0/docs/book-an-appointment

**Impact:** A first-time patient cannot reliably reach the promised PMS write from the documented intake. Treating a retry-safe appointment write as sufficient still permits duplicate patient records. IVR collection of date of birth and gender also materially expands the voice flow, error surface, and sensitive-data exposure.

**Required resolution:** Verify the exact production API/version and pilot PMS behavior, then define the minimum patient fields, existing-patient match policy, duplicate resolution, patient-creation idempotency, and channel-specific collection method. Add those fields explicitly to the permitted data map or select a different verified booking approach. This requires a spec revision and reapproval.

### P1 — Warm-transfer voicemail handling promises a deterministic result that is not yet contracted

**Evidence:** The product definition promises a “verified human answer,” voicemail detection, return to IVR, and exactly one abandoned-IVR text-back. The spec leaves the voicemail-detection rule, ring duration, and low-confidence behavior open.

Twilio supports answering-machine detection on `<Dial><Number>`, but detection may be asynchronous, can return `unknown`, introduces timing/experience tradeoffs, and requires per-scenario tuning: https://www.twilio.com/docs/voice/answering-machine-detection and https://www.twilio.com/docs/voice/answering-machine-detection-faq-best-practices

**Impact:** A machine can be treated as a human answer, suppressing text-back and connecting the caller to voicemail, or a human can be delayed/misclassified. The stated funnel categories and “exactly one” behavior are not testable until `human`, `machine`, `unknown`, timeout, callback-order, and caller-hang-up outcomes are defined.

**Required resolution:** Define the state machine and user-visible behavior for every AMD result, including unknown/timeout, delayed callbacks, voicemail reached before classification, and caller abandonment. Replace “verified human answer” with a testable provider outcome until pilot validation proves the stronger claim.

### P1 — The mandatory referral pillar still has no attributable return path

**Evidence:** Current scope and product state that every client receives all six pillars with no partial launch. The same scope calls referral attribution an open question, and the product definition admits that the untagged return path makes referral value unprovable. The app roadmap still says the referral ask may be explicitly deferred before pilot launch (`app/internal/steering/roadmap.md`, November).

**Impact:** The central offer promises both a mandatory referral pillar and a report proving what the sequence produced, while the delivery roadmap permits removing that pillar and the metric cannot attribute its result. Those statements cannot all be true.

**Required resolution:** Keep the user's approved six-pillar decision authoritative: remove the app-roadmap deferral option and specify the minimum attributable referral return path before pilot launch. Define the post-visit trigger, referral link/tag, lead association, and reporting event. If attribution is intentionally excluded, narrow the reporting promise and explicitly define what “delivers the referral ask” means.

### P1 — App steering still carries the superseded phone and data contracts

**Evidence:** `app/internal/steering/product.md` still says “No PHI in v1” and refers to a “no-PHI v1 constraint.” The app roadmap still describes the November phone item as forwarding/missed-call text-back “or” IVR, although central now requires IVR for every client. Its October lead-capture item also says “no-PHI guardrails.”

**Impact:** The next app specs can faithfully follow the app's own steering and still implement behavior that contradicts the newly authoritative central contract. That defeats the purpose of resolving the contract before technical baseline and feature specs depend on it.

**Required resolution:** Update app product and roadmap language in the same correction set: bounded IVR is decided, clinical intake is prohibited but scheduling/messaging may be PHI, and production is compliance/BAA-gated. Keep the central scope authoritative rather than duplicating full detail.

### P2 — Offer version naming is ambiguous

**Evidence:** The approved discussion and headings call the product “v1,” while current scope and status label it v1.1. Files remain named `scope.md` and `product-definition-v1.md`; the latter's title is “Product Definition v1” but its status says v1.1. The frozen record is `releases/scope-v1.md`.

**Impact:** Sales, specs, and future changelog entries can use “v1” to mean either the pre-resolution hold-slot/forwarding offer or the approved six-pillar Synchronizer/IVR offer.

**Required resolution:** Declare one naming rule. Recommended: call the current external offer “v1,” label the frozen file “pre-resolution v1 draft” in its metadata, and reserve v1.1 for a post-launch offer change. If v1.1 is intentional, rename the readable definition or state clearly that “v1” denotes the family and v1.1 the current contract.

## Acceptance audit

| Area | Result | Notes |
|---|---|---|
| Six pillars in central documents | Pass | All four central documents include the complete package. |
| Synchronizer vs. hold slots | Pass | Synchronizer selected; hold slots retired; unsupported PMS practices deferred. |
| Three booking channels | Pass with blocker | One contract is stated, but required patient fields and patient deduplication are not defined. |
| Phone route | Pass with blocker | IVR and human escape are selected; speech-transcription and AMD outcomes conflict with the data/behavior contract. |
| Booking outage fallback | Pass | All channels switch to request-to-book without automatic replay. |
| Voice outage fallback | Pass | Provider-hosted direct forwarding is explicit. |
| Minimum-data boundary | Fail | Speech recognition necessarily returns transcribed speech under the chosen Twilio mechanism. |
| Cross-surface consistency | Fail | App steering retains no-PHI, optional phone-path, and referral-deferral language. |
| Referral/reporting promise | Fail | Mandatory pillar lacks an attributable return path while reporting claims provability. |
| Historical preservation | Pass | Frozen scope matches the pre-change Git blob; dated product-definition snapshot remains available. |

## Recommendation

Do not advance to push/PR yet. Resolve the five P1 findings as a spec correction, obtain reapproval for the speech and Synchronizer intake decisions, then update the central and app documents and rerun this review. No P0 finding indicates immediate destructive or security exposure because this branch changes documentation only, but the current contract is not implementation-ready.

## Owner response prompts

Use the response blocks below to accept, reject, or modify each recommendation. These answers will become the input to the corrected spec and its reapproval.

### 1. Twilio speech recognition stays in v1

Use a hybrid IVR:

- DTMF: `1` to book, `2` to speak with the office, `9` for Spanish.
- Speech: accept a small, explicit vocabulary for booking and human-transfer intents.
- Twilio's transient `SpeechResult` is permitted, but raw audio and transcripts are never persisted or written to logs.
- Store only normalized results such as `intent=book`, language, requested date/time, and confidence/result status.
- Unrecognized speech gets two retries, then transfers to the office.
- Broader conversational AI remains v2.

Twilio lists Programmable Voice, Speech Recognition, IVR, transfers, and Answering Machine Detection as HIPAA-eligible services. Production still requires an appropriate Twilio edition and an executed BAA. Sources: [Twilio `<Gather>`](https://www.twilio.com/docs/voice/twiml/gather), [Twilio HIPAA accounts](https://www.twilio.com/docs/iam/twilio-editions/hippa), and [Twilio HIPAA-eligible services](https://www.twilio.com/content/dam/twilio-com/global/en/other/hipaa/pdf/HIPAA-Eligible-Services.pdf).

Suggested speech vocabulary:

- English booking: “book an appointment,” “schedule an appointment,” “make an appointment,” and “new appointment.”
- English human: “talk to someone,” “front desk,” “receptionist,” “representative,” “operator,” and “human.”
- Spanish booking: “reservar una cita,” “agendar una cita,” “hacer una cita,” and “programar una cita.”
- Spanish human: “hablar con alguien,” “recepción,” “recepcionista,” “representante,” and “operador.”

For explicit “emergency” language, route to the office and play the practice-approved emergency statement. v1 should not attempt clinical triage or infer urgency from free-form speech.

**Questions:** Do you approve this hybrid DTMF-and-speech contract? Do you want to add or remove any English or Spanish phrases? Should `9` be the Spanish-language key?

**Your response:**

> Yes, sounds good.

### 2. Patient matching and creation

NexHealth requires patient identity information before a new patient can be booked, including name, email, phone, date of birth, and gender. It also warns against creating duplicate patients. Sources: [NexHealth patient API](https://docs.nexhealth.com/reference/patients-1) and [NexHealth booking guide](https://docs.nexhealth.com/v3.1.0/docs/book-an-appointment).

Recommended deterministic contract:

1. Search only within the current practice.
2. Normalize phone to E.164, lowercase email, normalize name, and use ISO date of birth.
3. Automatically match only when there is a unique strong match:
   - phone + date of birth; or
   - email + date of birth; or
   - a previously stored PMS/NexHealth patient ID.
4. Name + date of birth alone is not strong enough for automatic matching.
5. If identifiers point to different patients or return multiple candidates, stop automation and create a front-desk review task.
6. If there is no match, collect all required fields and create exactly one patient.
7. Use an idempotency record based on practice + lead/session ID so retries cannot create duplicate patients or appointments.
8. Never automatically merge patient records.

For IVR callers, the recommendation is to collect sensitive identity fields through a secure SMS/web form rather than asking callers to speak their date of birth, gender, and email. After completion, recheck the chosen slot and book it; if the slot is gone, immediately offer alternatives. A caller who cannot use the link is transferred to the office. This is a modest compromise to “complete everything during the call,” but it substantially improves accuracy and privacy.

**Questions:** Do you approve the deterministic match/create rules? Should new IVR callers complete identity through the recommended secure SMS/web form, or must the booking be completed entirely by voice during the call?

**Your response:**

> When looking for a patient can we use just the phone number and then confirm with the user if that's their name from the records on our system??
To create a new user, I do like the option to collect that data through a secure sms / web form.
Research how do other systems do it.

**Follow-up research:** Phone number alone is useful for finding candidate records, but it should not be treated as a verified match. Caller ID can be spoofed, phone numbers can be reassigned, and family members may share one number. NexHealth's own returning-patient booking flow matches first and last name (within one character), date of birth, and either email or phone. Its patient-creation endpoint matches date of birth + name + phone, and its waitlist documentation explicitly describes multiple family members sharing a phone number. Other healthcare scheduling systems similarly send an existing patient through an authenticated portal or one-time sign-in link, while collecting name, date of birth, email, and phone for a new patient. Sources: [NexHealth returning-patient matching](https://help.nexhealth.com/en/articles/10414644-online-booking-enhancements-new-and-returning-patient-appointment-types), [NexHealth create-patient matching](https://docs.nexhealth.com/reference/postpatients), [NexHealth shared-number behavior](https://help.nexhealth.com/en/articles/10046719-how-do-i-send-waitlist-requests), and [SimplePractice existing/new patient flows](https://support.simplepractice.com/hc/en-us/articles/41956868349581-Previewing-the-appointment-request-widget).

**Proposed v1 resolution:**

1. Use the normalized phone number only to retrieve candidate patients inside the current practice.
2. Do not read a patient's full name merely because the caller's number matches a record.
3. If the phone returns one or more candidates, ask the caller to enter date of birth by DTMF. Filter candidates by exact date of birth.
4. If one candidate remains, confirm only the first name: “I found a record for [first name]. Is that you?” A positive answer selects the existing patient ID.
5. If no candidate remains, the caller declines the name, or multiple candidates remain, do not guess or merge. Send the secure identity form or transfer to the office.
6. The secure form asks for first name, last name, date of birth, email, phone, and gender. It first attempts NexHealth's returning-patient match; only a verified no-match may create a patient.
7. Callers without SMS/web access transfer to the office. Retries use the stored practice + call/session idempotency key.

This keeps the phone-number shortcut while requiring a second factor before disclosing even a first name. It also avoids collecting names, email addresses, or gender through speech recognition.

**Decision to approve:** Approve this phone-candidate + DTMF date-of-birth + first-name confirmation flow, or describe what you want changed.

**Your follow-up response:**

> Yes. proposed flow approved.

### 3. Warm transfer and voicemail behavior

Use this state machine:

- Default ring window: 20 seconds, adjustable per practice during onboarding.
- Human detected: bridge the caller.
- Voicemail/machine detected: terminate the practice leg, return to the IVR, explain that the office is unavailable, and offer SMS/request-to-book.
- Busy, no-answer, failed, or canceled: use the same fallback.
- AMD `unknown` or timeout: never assume a human answered; use the safe fallback.
- If the caller hangs up, cancel the outbound leg.
- Send a follow-up text only if the caller gave explicit SMS consent.
- Make callback processing idempotent so delayed Twilio events cannot bridge or text twice.

Twilio notes that AMD results and accuracy depend on configuration, so `unknown` needs explicit handling. Sources: [Twilio Answering Machine Detection](https://www.twilio.com/docs/voice/answering-machine-detection) and [Twilio AMD best practices](https://www.twilio.com/docs/voice/answering-machine-detection-faq-best-practices).

**Questions:** Do you approve this state machine and the default 20-second ring window? Should a practice be allowed to configure any ring duration, or should v1 constrain it to a range such as 10–30 seconds?

**Your response:**

> It looks mostly fine.
how do we collect sms consent? I want that consent as soon as possible to then send a follow-up text if no booking was made

**Follow-up research:** Twilio requires prior express consent before sending informational texts, including appointment messages. The caller must be able to decline without losing access to booking, and the disclosure must identify the sender, explain how the number will be used and what messages it covers, and explain opt-out. Twilio requires proof containing the date and method of consent. A2P registration explicitly supports `VERBAL` as an opt-in method. Sources: [Twilio Messaging Policy](https://www.twilio.com/en-us/legal/messaging-policy), [Twilio A2P consent requirements](https://www.twilio.com/docs/messaging/compliance/a2p-10dlc/collect-business-info), [Twilio verbal opt-in category](https://www.twilio.com/docs/messaging/compliance/a2p-10dlc/compliance-embeddable-onboarding), and [Twilio optional-consent requirement](https://www.twilio.com/docs/api/errors/30931).

**Proposed v1 resolution:** Ask immediately after language selection and before the booking/human menu, unless the practice already has documented, unrevoked consent covering scheduling and reminder messages for that number.

English prompt:

> “[Practice name] can text this number about scheduling, appointment confirmations and reminders, including a follow-up if this call is disconnected. Message frequency varies. Message and data rates may apply. Reply STOP to opt out. Consent is not required to book. Press 1 or say yes to agree. Press 2 or say no to continue without texts.”

Spanish prompt:

> “[Nombre de la clínica] puede enviar mensajes de texto a este número sobre programación, confirmaciones y recordatorios de citas, incluso un seguimiento si se desconecta esta llamada. La frecuencia de los mensajes varía. Pueden aplicarse tarifas de mensajes y datos. Responda STOP para cancelar. El consentimiento no es necesario para reservar. Presione 1 o diga sí para aceptar. Presione 2 o diga no para continuar sin mensajes.”

Store practice/tenant ID, normalized phone number, Twilio Call SID, timestamp, language, disclosure version, response method (`DTMF` or speech), response value, allowed message subjects, and consent status. Do not record the call solely to prove consent. If the caller declines, is silent, or hangs up before answering, send no automated follow-up text. Continue IVR booking and human transfer normally. The first SMS identifies the practice and includes “Reply STOP to unsubscribe.” A later STOP immediately revokes consent through Twilio's opt-out handling.

For web booking and secure identity forms, use a separate unchecked SMS-consent box with the same substance. An inbound text permits replies within that conversation but does not by itself authorize unrelated recurring reminders.

**Decision to approve:** Approve this early optional-consent prompt and evidence record, or describe what you want changed.

**Your follow-up response:**

> yes, approved.

### 4. Referral attribution

Recommendation:

- v1 keeps the basic post-visit referral ask so the six-pillar promise remains intact.
- Schedule it after booking, transfer, reminders, and reporting rather than as an early implementation priority.
- v1 reports only `ask_sent`, delivery, reply, and link click. It must not claim referred bookings without evidence.
- v2 adds booking attribution using a short referral URL plus a six-character code. The token maps to the practice and referring appointment but contains no patient information.
- The referred lead can follow the link or enter the code through the form/SMS flow.
- Report `referral_lead`, `referral_booking`, and `referral_show` once attribution exists.
- Do not expose the referring patient's identity to the referred lead.

**Question:** Should the basic referral ask remain part of v1, with booking attribution deferred to v2, or should the entire referral feature move to v2? Moving the entire feature would change the approved “all six in v1” decision.

**Your response:**

> yes. That's fine. v1 keeps the basic post-visit referral ask and then on v2 we fully build it.

### 5. Other recommendations from the open-question answers

- **PMS launch targets:** Dentrix, Eaglesoft, Open Dental, Curve Hero, and Dentrix Ascend. Treat exact version compatibility as an onboarding gate rather than making an unqualified market-share claim. Source: [Synchronizer installation guide](https://docs.nexhealth.com/docs/nexhealth-synchronizer-installation-guide-1).
- **Appointment type:** Expose one configurable “New patient visit” in v1. The practice selects its duration, provider, operatory, and PMS mapping during onboarding. Avoid automated emergency or treatment-type classification.
- **Request-to-book:** Notify the front desk through SMS and email, but include only an opaque task ID and secure dashboard link—not patient details. Acknowledge the patient instantly and promise confirmation within 24 hours.
- **Synchronizer circuit breaker:** Open after three transient failures within two minutes, or immediately after an authentication/permission failure. Retry after 60 seconds, use exponential backoff, and close after three successful health checks. Patient validation errors do not trip the circuit.
- **SMS consent:** Ask explicitly before sending abandoned-call or scheduling texts, record the disclosure version and response, identify the practice in the first message, and include STOP language. Source: [Twilio Messaging Policy](https://www.twilio.com/en-us/legal/messaging-policy).
- **Compliance ownership:** The founder can be the internal privacy/security owner, but production launch should require healthcare counsel's review, BAAs with applicable vendors, a security risk analysis, access controls, audit logging, incident response, vendor inventory, and written retention/deletion rules. HIPAA requires certain compliance documentation to be retained for six years, but does not itself establish a universal six-year retention rule for all patient data. Sources: [HHS Security Rule](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) and [HHS business-associate provisions](https://www.hhs.gov/hipaa/for-professionals/covered-entities/sample-business-associate-agreement-provisions/index.html).
- **Market selection:** Do not select states based on patients being “less technologically savvy.” Choose pilot states based on available practices, PMS compatibility, call volume, and a state-law review.
- **App steering alignment:** Update app product and roadmap language in the same correction set. Bounded IVR is decided; clinical intake is prohibited, scheduling/messaging may be PHI, and production is compliance/BAA-gated. Keep central scope authoritative instead of duplicating full details.

**Questions:** Which of these recommendations do you approve or want changed? For the 24-hour request-to-book commitment, do you mean 24 clock hours or one business day? Do you approve the proposed circuit-breaker thresholds? Which states, if any, should receive the first legal/compliance review?

**Your response:**

> yes. these all sound good.

**Recorded interpretation:** All recommendations in this section are approved. “Within 24 hours” means 24 clock hours, the proposed circuit-breaker thresholds are approved, and no launch state is preselected; the first state-law review will follow the location of the first otherwise-qualified pilot practice. Correct this interpretation in place if any part is wrong.

### 6. Version naming

All active offer, product, scope, flow, spec, and roadmap documents should say simply `v1`. The previous frozen scope should be labeled a dated “pre-resolution draft,” not `v1`, so two different definitions are not both presented as v1. Reserve `v1.1` for a post-launch offer change.

**Question:** You said “let's just have v1 everywhere.” Does this naming rule capture that decision, including relabeling the historical frozen scope as a pre-resolution draft?

**Your response:**

> yes.

## Correction re-review — 2026-09-22

**Type:** Review

Reviewed the corrected and approved spec plus the implemented current surfaces:

- `specs/2026-09-21-v1-booking-phone-contract.md`
- `out/scope.md`
- `steering/product.md`
- `out/product-definition-v1.md`
- `out/flows.md`
- `out-html/sequence-bridge/2026-08-11-product-flows.html`
- `out-html/index.html`
- `app/internal/steering/product.md`
- `app/internal/steering/roadmap.md`
- `releases/scope-pre-resolution-2026-09-21.md`

Historical snapshots and the frozen pre-resolution draft were excluded from current-contract contradiction searches except to verify that they are clearly labeled as historical.

### Finding resolution

| Initial finding | Result | Evidence in the corrected contract |
|---|---|---|
| Speech IVR contradicted the transcription prohibition | Resolved | Twilio `SpeechResult` is explicitly allowed as transient in-memory processing. Sequence Bridge persists only normalized intent/date/time/selection and provider status; raw audio and raw speech/transcripts are prohibited from persistence and logs. Twilio processing is gated by the qualifying edition, BAA, eligible-service configuration, and approved data flow. |
| Synchronizer patient prerequisites and deduplication were missing | Resolved | Phone is candidate discovery only; IVR uses DTMF date of birth before first-name confirmation. New-patient fields use a secure form, full identity is checked before create, ambiguous records go to front-desk review, and patient and appointment writes have separate idempotency rules. Verified existing patients do not enter the new-patient type. |
| Warm-transfer voicemail behavior was not deterministic | Resolved | The contract now defines `human`, machine/fax, busy, no-answer, failed, canceled, AMD unknown/timeout, delayed callback, and caller-hang-up behavior. Ring time is 20 seconds by default, configurable from 10–30 seconds, and duplicate/delayed callbacks cannot bridge or message twice. |
| Mandatory referral had no honest attribution contract | Resolved | V1 keeps the mandatory basic ask after a completed/kept status and reports eligibility, sent, delivery, reply, and click only. Referred lead/booking/show attribution and its opaque short link/code are explicitly v2. |
| App steering retained superseded phone and data contracts | Resolved | App product and roadmap now name Synchronizer, the bounded bilingual IVR, early optional consent, patient matching/creation, transient speech handling, compliance gates, deterministic fallbacks, and the required v1 referral ask. Optional forwarding-vs-IVR and no-PHI language are removed. |
| Offer version naming was ambiguous | Resolved | Every active surface calls the offer `v1`. `v1.1` is reserved for a future post-launch offer change. The former `releases/scope-v1.md` is now `releases/scope-pre-resolution-2026-09-21.md` and is labeled frozen historical draft. |

### Acceptance audit after correction

| Area | Result | Notes |
|---|---|---|
| Six mandatory pillars | Pass | Central and app steering prohibit partial launch; referral may be implemented later in build order but must pass before client launch. |
| PMS qualification | Pass | Synchronizer is the sole normal path; Dentrix, Eaglesoft, Open Dental, Curve Hero, and Dentrix Ascend are target priorities subject to exact version/operation validation. Unsupported PMSs and hold slots are excluded. |
| Patient match/create | Pass | Phone-only selection is prohibited; DTMF DOB, first-name confirmation, secure new-patient form, ambiguity review, and retry-safe writes are specified. |
| Three booking channels | Pass | Web, SMS, and IVR share matching, availability, revalidation, write, idempotency, consent, and reporting rules. |
| SMS consent | Pass | Optional consent occurs after language and before the route menu, can be declined without losing service, is auditable, and gates automated outbound SMS. |
| Bounded speech | Pass | English/Spanish vocabulary, five-second timeout, two retries, clinical-detail redirect, emergency keyword route, transient provider transcription, and persistence limits are explicit. |
| Warm transfer and AMD | Pass | Every provider result and callback-order outcome has a testable state transition and safe fallback. |
| Booking outage | Pass | Approved circuit thresholds switch every channel to request-to-book, notify the front desk with opaque secure tasks, promise 24 clock hours, and prohibit automatic replay. |
| Referral/reporting truth | Pass | V1 measures the ask and engagement without claiming attributed referral bookings or shows. |
| Compliance boundary | Pass | Scheduling/messaging may be PHI; production is gated by counsel, BAAs, Twilio edition/configuration, A2P, state review, minimum-data controls, and the security baseline. |
| Cross-surface consistency | Pass | Focused searches found no active v1.1, no-PHI, optional-phone-path, hold-slot fallback, or optional-referral language in the current surfaces. |
| Published rendering | Pass | The visual companion uses the shared stylesheet and index back-link, is listed in the deliverables index, and was browser-rendered at 1440 px without clipped sequence cards after correcting the flow-row sizing. |
| Historical preservation | Pass | The original product-definition context snapshot remains dated; the old scope is retained under an unambiguous pre-resolution filename and warning. |

### Non-blocking production dependencies

These are launch evidence, not unresolved product choices: select the design partner; verify its exact PMS/version and completed/kept status; execute Twilio/Synchronizer agreements and BAAs; approve A2P campaigns; complete the first launch-state legal review; approve the retention/deletion schedule; and run the synthetic consent, booking, fallback, referral, and reporting tests.

## Final recommendation

The documentation implementation passes adversarial review and is ready for the push/PR stage. Do not commit, push, open a PR, merge, or close the `[WIP]` roadmap item without the next explicit approval required by the delivery workflow.
