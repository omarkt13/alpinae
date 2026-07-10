# Alpinae

Swiss-hosted, read-only reconciliation & reporting layer for wealth managers.
Successor project to ChiefRiskBot after discovery with GADD & Cie — see SPEC.md
for the full pivot rationale.

## Current phase

**Demo only.** Static, zero-dependency HTML/CSS/JS — no build step, no
node_modules (deliberate: this machine's Documents folder is iCloud-synced and
disk-constrained). Do not introduce npm/bun/Vite here without explicit approval.

- `index.html` — marketing page
- `demo/advisor.html` — advisor portal demo
- `demo/client.html` — client portal demo (read-only)
- `assets/` — shared CSS + mock data
- Serve with `python3 -m http.server 4173`.

## Design

Always read `DESIGN.md` before visual changes. Short version: ivory paper
(`#FAF7F2`), EB Garamond headlines, Inter UI, IBM Plex Mono for all numerals,
pine green `#1E4033` as the only accent, red `#A63A2E` reserved for guardrail
warnings. Aesthetic: "Swiss private bank annual report", never SaaS-glossy.
No dark mode.

## Hard product rules (from the spec — do not violate in any demo or code)

- Read-only throughout. Nothing writes back to any external system.
- Every consolidated figure must be traceable to a source statement.
- Guardrail warnings require explicit human confirmation, never auto-resolve.
- Not a CRM; no CRM integration as a dependency.
- All demo data is fictional — never use real client/firm names from calls.

## Future MVP

See ARCHITECTURE.md. Key constraints locked in: Swiss hosting (Exoscale, not
Fly.io), no LLM calls on raw client data, deterministic per-custodian parsers,
append-only hash-chained audit log, single-tenant per firm.
