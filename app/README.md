# app/

The multi-tenant platform we own and operate.

Serves two audiences:

1. **Agency dashboard** — per-client metrics and reporting, lead DB, SMS sequence management. Dense workhorse UI (data tables, stat tiles, forms, filters) on the Sequence Bridge brand theme.
2. **Tenant landing pages** — conversion pages generated per client practice, each themed with that practice's own logo and colors. Themes live in `app/themes/`.

Core pieces: landing-page templates, booking engine with per-practice availability, Twilio SMS sequences, lightweight lead DB/CRM, metrics dashboard.

Not yet built. Spec it via the `internal-scaffolding` skill before implementation.
