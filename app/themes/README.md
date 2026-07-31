# app/themes/

Per-client token files for tenant landing pages. One file per practice, overriding the brand theme with that client's own logo and colors.

These live here rather than in `internal/design/` because tenant landing pages are a platform feature generated from platform data, not marketing content. `internal/design/tokens.json` stays the single source of truth for the Sequence Bridge brand and defines the token *shape* these files override.

Naming: `<practice-slug>.json`.
