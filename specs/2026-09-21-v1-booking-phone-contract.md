# V1 booking and phone contract

**Status:** Corrected and approved — 2026-09-22
**Original approval:** 2026-09-21
**Decision record:** `ai-reviews/2026-09-22-v1-booking-phone-contract.md`
**Roadmap item:** `app/internal/steering/roadmap.md` — Resolve the v1 booking and phone contract in `central`
**Branch:** `docs/v1-booking-phone-contract`

## Summary

V1 is one indivisible six-part package for every signed client: landing page, IVR call capture, booking, SMS confirmations/reminders, referral ask, and operated reporting. A client does not launch with a subset, and there is no à la carte version.

V1 uses NexHealth Synchronizer as the only normal path for reading availability, identifying or creating the booking patient, writing confirmed appointments, and reading the minimum appointment status required for reminders, show-rate reporting, and the post-visit referral trigger. The web calendar, conversational SMS, and IVR all use this contract. Hold slots are not a second booking mode, and unsupported practice-management systems (PMSs) are not eligible for the first pilot. If Synchronizer is temporarily unavailable, all three channels switch to request-to-book until health is restored.

V1 phone handling is a bounded English/Spanish IVR on a Sequence Bridge tracking number. The caller can use DTMF or a small speech vocabulary to start booking or request the office. Twilio speech recognition may produce a transient `SpeechResult`; Sequence Bridge stores only the normalized intent or selection and never stores raw call audio or raw speech transcripts. Broader conversational voice AI remains v2.

V1 uses the caller's phone number only to find possible patient records. It verifies an existing patient with DTMF date of birth before disclosing a first name or selecting a patient ID. New-patient demographics are collected through a secure SMS/web form rather than through voice. Patient and appointment creation are both retry-safe.

The former “no PHI” promise is replaced by a narrower, testable boundary. Sequence Bridge prohibits clinical intake, but identifiable scheduling and messaging data may be PHI when handled for a covered practice. Production launch therefore requires the appropriate Twilio plan and BAA, other required BAAs, approved A2P registration, a documented security/compliance posture, and launch-state review.

## User value

- A prospective new patient sees or hears real availability and can receive a confirmed PMS appointment without front-desk transcription.
- A caller can use the bounded IVR or request the office at any point.
- A caller may opt into scheduling texts near the beginning of the call, enabling one follow-up when the booking or transfer is not completed.
- A new patient enters sensitive demographic fields through a secure form rather than speaking them over the phone.
- The practice keeps its main number and PMS as the operational systems of record.
- During a scheduling-provider outage, the system makes no unverified availability promise and cannot create a double-booking.
- The owner receives call, booking, show, and referral-ask metrics with explicit coverage and exclusions.

## Requirements

### 0. Complete v1 offer

1. Every signed client receives all six pillars: landing page, IVR call capture, booking, SMS confirmations/reminders, referral ask, and operated reporting.
2. The six pillars share one tenant configuration, lead record, patient-link state, booking contract, consent/suppression state, event trail, and reporting model.
3. A client cannot be declared live while any pillar is disabled, substituted with an unapproved manual process, or unable to pass its launch test.
4. Referral is implemented after the core capture, booking, transfer, reminder, and reporting paths, but it must pass its launch test before the first client is declared live.
5. There is no à la carte packaging in v1. Qualification happens before signing so a client who cannot support the complete package is not sold or launched as v1.

### 1. Client qualification and booking contract

1. Synchronizer is the v1 provider for availability reads, patient search/creation, appointment writes, and the minimum appointment-status reads used by all three booking channels: web calendar, conversational SMS, and IVR. Sequence Bridge must not present a slot as confirmed until the PMS write succeeds.
2. The initial PMS coverage targets are Dentrix, Eaglesoft, Open Dental, Curve Hero, and Dentrix Ascend. This is an operating priority list, not an unqualified market-share claim. Exact PMS product, version, location, and operation support must be verified for each practice.
3. A practice qualifies for the first pilot only after all of the following are verified:
   - its exact PMS and version are supported in production for the required patient, availability, booking, and appointment-status operations;
   - the practice authorizes and completes the connection;
   - the required operations pass a sandbox test and a non-patient production test or another approved production-validation method;
   - commercial terms, support path, BAA, and service expectations are accepted;
   - location, provider, operatory, appointment-type, duration, timezone, and appointment-status mappings are complete.
4. V1 exposes one practice-configured appointment type: `New patient visit`. The practice selects its patient-facing label, duration, provider, operatory, and PMS mapping during onboarding. V1 does not ask for symptoms, treatment reason, procedure, or urgency to choose an appointment type.
5. If a person is verified as an existing patient, V1 does not place that person into the new-patient appointment type. The caller is offered the office route; web/SMS creates an attributable request-to-book for front-desk handling.
6. Sequence Bridge reads and writes only the minimum identity, contact, consent, availability, booking, and appointment-status fields needed for the approved workflow. It does not read clinical charts, treatment plans, diagnoses, billing, claims, insurance, or free-text clinical notes.
7. Patient creation and appointment creation are separately idempotent. A provider timeout, duplicate callback, retry, or repeated patient action cannot create a second patient or appointment.
8. Immediately before writing, the selected slot is revalidated. A conflict returns the patient to fresh availability rather than silently choosing another time.
9. The PMS appointment is the source of truth. Sequence Bridge stores the provider references and minimum local scheduling projection needed for workflow state, audit, messaging, and reporting; it does not maintain an independently editable shadow calendar.
10. Hold slots are not offered as a parallel v1 integration. A practice on an unsupported PMS is deferred rather than launched with an unproven operating model.
11. All three channels use the same availability, patient matching, patient creation, revalidation, conflict, idempotency, timezone, consent, confirmation, reminder, referral, audit, and reporting rules. Channel-specific behavior cannot create a different definition of “booked.”
12. Conversational SMS accepts an approved preferred date/time response, returns two or three live Synchronizer options, accepts a bounded selection such as `1`, `2`, or `3`, revalidates the choice, writes it to the PMS, and confirms only after success.

### 2. Patient matching and creation

1. Patient search is scoped to the current tenant, practice, and location. Cross-tenant matching is prohibited.
2. Normalize phone numbers to E.164, lowercase and trim email addresses, normalize names without changing their semantic spelling, and represent date of birth in ISO format.
3. Phone number is a candidate-discovery key, not sufficient identity proof. Caller ID alone never selects a patient and never authorizes disclosure of a patient's full name.
4. The IVR existing-patient check follows this sequence:
   - search by the normalized calling number for active, bookable candidate patients;
   - ask the caller to enter date of birth by DTMF;
   - filter candidates by exact date of birth;
   - if exactly one candidate remains, disclose only the first name and ask the caller to confirm it;
   - on confirmation, select the existing provider patient ID and route the caller to the office because the v1 auto-book appointment type is for new patients;
   - if no candidate remains, the caller rejects the name, or multiple candidates remain, do not guess, merge, or disclose additional identity data. Offer the secure identity form or office transfer.
5. A new or unresolved patient who has consented to SMS receives a short-lived secure form. The form collects first name, last name, email, phone, date of birth, and the gender value required by the target PMS/NexHealth contract. A caller who declines SMS, uses a number that cannot receive SMS, or cannot use the form is transferred to the office.
6. Before creating a patient, the system searches with the full supplied identity/contact set and applies NexHealth's supported returning-patient match behavior. A unique strong match uses the existing patient ID. Conflicting or multiple matches create a front-desk review task and never auto-merge.
7. A verified no-match creates exactly one patient using an idempotency key derived from tenant + channel lead/session. Persist the NexHealth/provider patient ID before attempting the appointment write.
8. A patient-create timeout or retry first checks the local idempotency record and provider result before issuing another create request.
9. The secure form does not guarantee the previously offered slot. After form completion, the system revalidates that slot. If it is gone, it immediately offers fresh availability.

### 3. Booking outage fallback

1. Provider health is evaluated per tenant. The booking circuit opens after three transient provider failures within two minutes, or immediately after an authentication or permission failure. Patient validation, slot conflict, and other business-rule errors do not open the circuit.
2. While the circuit is open, all entry points use request-to-book:
   - show no slots as available or confirmed;
   - collect contact details and one or more preferred time windows;
   - acknowledge the request immediately and state that the practice will respond within 24 clock hours;
   - create one attributable operator task;
   - notify the designated front desk through both SMS and email using only an opaque task ID and secure dashboard link, with no patient details in the notification body;
   - send appointment confirmation and reminders only after an operator records a successful PMS booking.
   For IVR, the caller is told that live booking is temporarily unavailable and may choose an immediate office transfer or, with consent, a texted request-to-book path. Conversational SMS and web offer no numbered/live slots while the circuit is open.
3. Queued requests are not replayed as appointment writes automatically when service returns. An operator must recheck current availability and explicitly confirm each request.
4. The circuit makes a half-open health/read attempt after 60 seconds and then uses exponential backoff at 1, 2, 5, and 10 minutes, capped at 10 minutes. It closes after three consecutive successful health/read checks.
5. Opening, half-open attempts, and closing are auditable events. An operator is alerted if the circuit remains open for five minutes.
6. The outage mode and its patient-facing wording are exercised before a tenant can launch.

### 4. Phone, transfer, and SMS-consent contract

1. Each tenant receives a Sequence Bridge tracking number published only on channels Sequence Bridge manages, initially the tenant landing page and Google Business Profile.
2. The first prompt selects language: continue in English by default; press `9` or say “Español” for Spanish.
3. Unless a current, documented, unrevoked consent record already covers scheduling, confirmation, reminder, and disconnected-call follow-up messages from that practice, the IVR asks for SMS consent immediately after language selection and before the route menu.
4. The English consent prompt is:

   > “[Practice name] can text this number about scheduling, appointment confirmations and reminders, including a follow-up if this call is disconnected. Message frequency varies. Message and data rates may apply. Reply STOP to opt out. Consent is not required to book. Press 1 or say yes to agree. Press 2 or say no to continue without texts.”

   The Spanish consent prompt is:

   > “[Nombre de la clínica] puede enviar mensajes de texto a este número sobre programación, confirmaciones y recordatorios de citas, incluso un seguimiento si se desconecta esta llamada. La frecuencia de los mensajes varía. Pueden aplicarse tarifas de mensajes y datos. Responda STOP para cancelar. El consentimiento no es necesario para reservar. Presione 1 o diga sí para aceptar. Presione 2 o diga no para continuar sin mensajes.”
5. Declining or not answering the consent prompt does not prevent IVR booking or office transfer. It prevents all automated outbound SMS until valid consent is obtained through another approved method.
6. Consent evidence stores tenant/practice ID, normalized phone, Twilio Call SID, timestamp, language, disclosure version, response method, response value, allowed message subjects, and current status. Sequence Bridge does not record the call solely to prove consent.
7. Web and secure identity forms use a separate, unchecked SMS-consent control with equivalent substance. A person who initiates an inbound text permits replies within that exchange but does not thereby consent to unrelated recurring reminders.
8. The main route menu is `1` to book or `2` to speak with the office. It also accepts the approved bounded phrases:
   - English booking: “book an appointment,” “schedule an appointment,” “make an appointment,” and “new appointment”;
   - English office: “talk to someone,” “speak to the office,” “front desk,” “receptionist,” “representative,” “operator,” “human,” and “help”;
   - Spanish booking: “reservar una cita,” “agendar una cita,” “hacer una cita,” “programar una cita,” and “cita”;
   - Spanish office: “hablar con alguien,” “hablar con la oficina,” “recepción,” “recepcionista,” “representante,” “operador,” and “ayuda.”
9. After the main menu, pressing `0` or saying an approved office phrase is the global human escape. This keeps slot choices `1`, `2`, and `3` unambiguous while preserving human access at every later prompt.
10. Each bounded speech/DTMF prompt waits five seconds. Silence, an invalid response, an unmatched phrase, or a low-confidence result receives this language-appropriate reprompt: “Sorry, I didn't understand. Please say ‘book an appointment’ or ‘speak to the office.’” After two unsuccessful retries, the IVR transfers to the office.
11. Speech processing is limited to the allowlisted route phrases, explicit `emergency`/`emergencia` routing keywords, bounded date/time preference input, and bounded numeric selections. V1 does not offer general conversation or clinical interpretation.
12. Twilio may return transient raw speech in `SpeechResult`. Sequence Bridge processes it in memory, stores only the normalized language/intent/date/time/selection and provider confidence/status, and excludes raw speech from application storage, logs, traces, analytics, alerts, and support tooling. Twilio's own handling is governed by the executed BAA, eligible-service list, and approved configuration.
13. If a caller attempts to provide medical information, the IVR does not repeat, summarize, interpret, or advise on it. It says: “Please don't share medical details here. I can help you book an appointment or connect you with the office.” Unmatched content follows the retry/transfer rule.
14. On the explicit words `emergency` or `emergencia`, the IVR does not triage. It offers immediate office transfer and plays the practice-approved statement directing a person who believes they have a medical emergency to call emergency services or the practice's approved urgent-care number.
15. The auto-book route offers only the approved new-patient appointment type. It captures the caller's preference, offers two or three live slots, and records one tentative selection. A new caller then completes the secure patient form from section 2. After the form returns a verified patient ID, the system revalidates the selected slot, writes the appointment, and confirms only after success; if the slot is gone, it offers fresh availability. The completed booking triggers the standard confirmation/reminder sequence.
16. The human route keeps the caller leg active while the practice leg rings. The default ring interval is 20 seconds and is configurable per practice from 10 through 30 seconds during onboarding.
17. Warm-transfer outcomes are deterministic:
   - provider outcome `human`: bridge the parties and then remove the Sequence Bridge leg;
   - `machine` or fax: terminate the practice leg, return the caller to the unavailable message, and offer SMS/request-to-book when consent permits;
   - `busy`, `no-answer`, `failed`, or `canceled`: use the same unavailable fallback;
   - answering-machine detection `unknown` or timeout: never treat it as a verified human; stop the practice leg at the configured ring limit and use the unavailable fallback;
   - caller hang-up: cancel the practice leg and send at most one follow-up only when prior SMS consent exists.
18. Delayed, duplicate, or reordered AMD/call callbacks cannot bridge after fallback, create duplicate tasks, send duplicate messages, or create duplicate appointments. “Human answered” in application events means the provider returned the configured `human` outcome; reports must not claim certainty beyond that provider result.
19. A caller hang-up, timeout, failed transfer, or uncompleted booking is classified as abandoned. When consent and suppression state permit, it schedules exactly one follow-up into conversational SMS booking.
20. The first outbound SMS identifies the practice, states why the recipient is receiving it, and includes “Reply STOP to unsubscribe.” A STOP or equivalent supported keyword immediately suppresses further messages for that sender and subject until a valid re-opt-in.
21. Sequence Bridge records the minimum call-event data required for routing, deduplication, operations, and reporting: tenant, provider call identifiers, caller/called numbers, language, consent event reference, normalized route/state transitions, offered-slot references, timestamps, leg status, duration, booking outcome, and whether a follow-up was attempted and delivered.
22. All Twilio webhooks use HTTPS and verified Twilio signatures. Invalid callbacks have no side effects.
23. The tracking number has a provider-hosted fallback handler, independent of the application runtime, that forwards directly to the practice main line when the primary IVR handler fails. This preserves voice reachability but may temporarily reduce application-side event capture and follow-up behavior.
24. The practice's main number is neither ported nor replaced. Direct calls to it remain outside Sequence Bridge reporting.

### 5. Data, security, and compliance boundary

1. Product and sales material must not claim that “contact + booking preference” categorically keeps Sequence Bridge outside HIPAA. Appointment scheduling and patient messaging performed for a covered practice may involve PHI.
2. The permitted persisted data set is limited to identity/contact fields, date of birth, PMS-required gender value, consent and suppression evidence, approved generic appointment type, scheduling preferences, appointment logistics/status, normalized IVR intent and selections, delivery/call events, provider identifiers, tenant ownership, and audit fields.
3. Raw `SpeechResult` content is permitted only as transient in-memory input to the bounded matcher. Raw audio, raw speech transcripts, and caller utterances are prohibited from Sequence Bridge storage and logging.
4. The prohibited data set includes symptoms, diagnoses, treatment reasons, medical or dental history, chart data, procedure details beyond the configured generic appointment type, prescriptions, images, insurance, claims, billing, recordings, persisted transcripts, and unrestricted patient free text.
5. Patient-facing forms, messages, and IVR prompts instruct users not to submit clinical details. V1 provides no general-purpose notes field or open-ended voice capture.
6. The founder is the initial internal privacy/security owner. Before production patient traffic, qualified healthcare counsel must review the covered-entity/business-associate determination, practice BAA, vendor/subprocessor BAAs, data map, state-specific rules, retention/deletion schedule, incident/breach obligations, and patient-facing disclosures.
7. The production security baseline includes a documented risk analysis; minimum-necessary data inventory and flow map; MFA and least-privilege access; encryption in transit and at rest; audit logging and access review; secrets management; tested backup/restore where applicable; incident and breach response; vendor/subprocessor inventory; workforce policies/training; and contract termination, return, and destruction procedures.
8. HIPAA-required compliance documentation is retained for the required six-year period. Patient and operational data retention is not assumed to be six years; counsel and client contracts must approve an explicit minimum retention/deletion schedule before launch.
9. Synchronizer, Twilio, hosting, logging, monitoring, analytics, support, and backup paths are included in the data-flow review. A vendor is not approved merely because it advertises a HIPAA-capable product; the contracted service and configuration must be covered.
10. Before production traffic, the Twilio account must:
   - use the Security or Enterprise Edition required for Twilio's BAA process;
   - have an executed BAA and HIPAA-enabled account/workflow approved through Twilio Sales or the account representative;
   - use only currently HIPAA-eligible voice, speech-recognition, AMD, messaging, and supporting services in the approved configuration;
   - complete the applicable ISV/customer A2P 10DLC brand and campaign registration for each practice sender;
   - document every opt-in method in the campaign message flow and configure STOP/HELP handling, preferably through Advanced Opt-Out;
   - pass test evidence for consent, suppression, sender identification, and webhook security.
11. Logs, traces, analytics, alerts, and error messages must not contain prohibited data and must minimize permitted patient identifiers.

### 6. Referral ask

1. V1 sends the basic referral ask only after a PMS/Synchronizer status identifies the appointment as completed/kept. Canceled, no-show, unresolved, and unknown-status appointments are suppressed.
2. The message asks the serviced patient to share the practice's booking link. It contains the practice identity and required SMS opt-out language.
3. V1 records `referral_ask_eligible`, `referral_ask_sent`, delivery, reply, and booking-link click events. It does not claim that a later lead, booking, or show was caused by a particular referral ask.
4. If the exact pilot PMS cannot provide a reliable completed/kept status, the practice is not launch-ready until another approved automated status signal is verified. V1 does not add recurring manual front-desk work to compensate.
5. V2 may add a short referral URL plus an opaque six-character code mapped to the tenant and source appointment, without exposing the referring patient's identity. `referral_lead`, `referral_booking`, and `referral_show` remain v2 metrics.

### 7. Metrics and operational truth

1. “Calls managed through Sequence Bridge” means calls received by the tracking number. Reports separately show IVR auto-booked, human-route provider-human outcome, abandoned/follow-up eligible, follow-up delivered, request-to-book, and subsequently booked outcomes.
2. Reports state that direct calls to the practice main line are not observed and are excluded from the denominator.
3. A booking is counted only after a successful PMS write. A request-to-book is reported separately and never counted as a booking.
4. V1 referral reporting is limited to eligibility, ask sent, delivery, reply, and link click. Reports do not claim referred bookings or shows until the v2 attribution contract exists.
5. Provider outages, fallback-handler use, circuit-open time, IVR and warm-transfer failures, abandoned sessions, failed follow-ups, and unresolved request-to-book tasks are visible to the operator.
6. Operated reporting includes the weekly failed-automation inspection and metrics check plus the monthly client report. The report covers speed-to-lead, booking rate, show rate, and cost per booked appointment only when the practice supplies the required spend input.

### 8. Steering and version alignment

After this corrected spec is reapproved, implementation of this roadmap item updates the following documents together so they express one contract:

- `central/out/scope.md`
- `central/steering/product.md`
- `central/out/product-definition-v1.md`
- `central/out/flows.md`
- `app/internal/steering/product.md`
- `app/internal/steering/roadmap.md`

The active external offer and all current steering use the name `v1`. `v1.1` is reserved for a post-launch offer change. The frozen pre-resolution scope is renamed/relabelled as a dated pre-resolution draft so it is not a second artifact claiming to be the current v1.

Central scope remains authoritative. App steering links to or summarizes that contract without inventing a second product definition. Any generated or published rendering is updated from the authoritative sources in the same implementation stage if it still exists.

## Out of scope

- Implementing Synchronizer, Twilio, booking UI, messaging, storage, or operator workflows.
- Selecting the application framework, database, hosting platform, observability stack, or job runner.
- Open-ended AI receptionist behavior, clinical triage, or general voice assistance beyond the bounded IVR routes.
- A hold-slot operating model for unsupported PMSs.
- Direct PMS integrations.
- Porting the practice's main number.
- Call recording or persistent/raw speech transcription.
- Clinical intake or storage of clinical information.
- A legal conclusion that Sequence Bridge is or is not a HIPAA business associate.
- No-show recovery beyond reminders and show-rate reporting.
- Referral lead/booking/show attribution beyond v1 ask eligibility, delivery, reply, and click events.
- Outcome-based pricing or changes to the approved v1 commercial model.

## Acceptance criteria

1. All current offer and steering documents call the active offer `v1`; no active document uses `v1.1`, and the historical pre-resolution scope is not presented as the current v1.
2. Central and app steering state that every v1 client receives all six pillars with no à la carte or partial-launch variant.
3. Central documents name Synchronizer as the sole normal v1 booking provider, do not describe hold slots as a parallel option, and defer unsupported PMSs.
4. The target PMS list and per-practice version/operation qualification gates are consistent across the product definition and scope.
5. Web, SMS, and IVR use the same patient matching/creation, slot revalidation, conflict, idempotency, consent, confirmation, reminder, and reporting contract.
6. A patient cannot be matched solely by phone number. The approved phone + DTMF DOB + first-name confirmation flow and the secure new-patient form are documented and testable.
7. No document calls an appointment confirmed before a successful PMS write; request-to-book is never reported as booked.
8. The booking circuit opens after the approved thresholds, every channel switches to request-to-book, front-desk SMS/email notifications contain only an opaque task ID and secure link, and the patient receives the approved 24-clock-hour promise.
9. The IVR consistently implements English/Spanish selection, early optional SMS consent, main-menu `1`/`2`, global `0` human escape, five-second prompts, two retries, bounded speech vocabulary, and the safe emergency route.
10. The consent prompt can be declined without blocking booking or transfer, proof is retained, no automated SMS is sent without valid consent, and the initial SMS and opt-out behavior meet the approved contract.
11. Speech recognition is explicitly disclosed as transient processing. Raw `SpeechResult` text and call audio do not appear in Sequence Bridge persistence or logs; only normalized results are retained.
12. Warm-transfer behavior is specified for `human`, `machine`, fax, `busy`, `no-answer`, `failed`, `canceled`, `unknown`, timeout, delayed callbacks, and caller hang-up. The ring default is 20 seconds and tenant configuration is constrained to 10–30 seconds.
13. The main-line gap and “calls managed through us” reporting denominator are stated consistently.
14. The outage paths are explicit for both dependencies: Synchronizer failure produces request-to-book or office transfer, and primary IVR-handler failure produces provider-hosted direct forwarding.
15. The documents replace the categorical no-PHI/out-of-HIPAA claim with the permitted/prohibited data boundary and the compliance/BAA production gate.
16. Twilio account edition/BAA, HIPAA-eligible service configuration, A2P registration, opt-out, webhook, and test-evidence gates appear in onboarding or launch requirements.
17. The v1 referral trigger requires a completed/kept status, and reporting is limited to ask eligibility, sent, delivery, reply, and click without claiming attributed bookings.
18. App product and roadmap no longer describe phone handling as undecided, claim a no-PHI v1, or permit referral to be omitted from v1.
19. Vendor capability statements and links are current as of the implementation date and distinguish vendor claims from Sequence Bridge decisions.
20. A cross-document search finds no surviving contradiction on version name, six pillars, PMS coverage, booking provider, patient matching, IVR routes, SMS consent, speech processing, AMD outcomes, referral scope, outage behavior, or the data boundary.

## Launch gates and client-specific inputs

These are not open product choices. They are evidence or configuration that must exist before the affected production launch:

1. Select the first design partner and verify its exact PMS/version against the approved five-product target list and every required operation.
2. Obtain the practice-approved new-patient label, duration, provider, operatory, hours, timezone, completed/kept status, main-line number, urgent-care instruction, and warm-transfer ring value.
3. Confirm the founder as the documented privacy/security owner and engage qualified healthcare counsel to approve the BAA set, state-specific requirements, retention/deletion schedule, incident obligations, and patient disclosures.
4. Establish the qualifying Twilio commercial edition, execute the BAA, enable the approved HIPAA workflow, register each practice's A2P sender/campaign, and test English/Spanish consent and opt-out paths.
5. Execute and verify the Synchronizer commercial agreement, BAA, sandbox/production access, per-location price, support path, and exact target-PMS capabilities.
6. Perform the first state-law review for the state of the first otherwise-qualified pilot practice rather than selecting a market based on an assumption about patients' technical sophistication.

## Evidence informing the decisions

- NexHealth patient search/create guidance, required new-patient fields, and duplicate prevention: https://docs.nexhealth.com/reference/patients-1
- NexHealth returning-patient online-booking matching: https://help.nexhealth.com/en/articles/10414644-online-booking-enhancements-new-and-returning-patient-appointment-types
- NexHealth `return_existing_if_match` behavior: https://docs.nexhealth.com/reference/postpatients
- NexHealth supported-system installation guidance: https://docs.nexhealth.com/docs/nexhealth-synchronizer-installation-guide-1
- NexHealth Synchronizer pricing and vendor capability claims: https://synchronizer.nexhealth.com/pricing
- Twilio bounded DTMF/speech input and `SpeechResult`: https://www.twilio.com/docs/voice/twiml/gather
- Twilio Answering Machine Detection results and tuning considerations: https://www.twilio.com/docs/voice/answering-machine-detection and https://www.twilio.com/docs/voice/answering-machine-detection-faq-best-practices
- Twilio Messaging Policy consent, proof, sender-identification, and opt-out requirements: https://www.twilio.com/en-us/legal/messaging-policy
- Twilio A2P campaign message-flow and opt-in requirements: https://www.twilio.com/docs/messaging/compliance/a2p-10dlc/collect-business-info
- Twilio BAA/account requirements: https://www.twilio.com/docs/iam/twilio-editions/hippa
- Twilio HIPAA-eligible services: https://www.twilio.com/content/dam/twilio-com/global/en/other/hipaa/pdf/HIPAA-Eligible-Services.pdf
- Twilio fallback URL guidance: https://www.twilio.com/docs/usage/security/availability-reliability
- Twilio signed webhook guidance: https://www.twilio.com/docs/usage/webhooks/webhooks-security
- HHS business-associate guidance for appointment/scheduling applications: https://www.hhs.gov/hipaa/for-professionals/privacy/guidance/business-associates/index.html
- HHS Security Rule summary and administrative/technical safeguard expectations: https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html
- HHS business-associate contract provisions: https://www.hhs.gov/hipaa/for-professionals/covered-entities/sample-business-associate-agreement-provisions/index.html
