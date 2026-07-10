# Alpinae

**Consolidated reconciliation & reporting for Swiss wealth managers.**

Alpinae consolidates a client's positions across the firm's proprietary fund
and every third-party custodian statement into one accurate, fully traceable
exposure view — Swiss-hosted, read-only, built for FINMA/CSSF-regulated firms.

## Status

Demo phase. This repo contains the marketing site and a mock-data product demo
— **no working backend, no real data**.

- `index.html` — marketing page
- `demo/advisor.html` — advisor portal demo (client directory, statement
  ingestion with file-to-client guardrail, cross-custodian reconciliation with
  per-figure traceability, report generation, audit log)
- `demo/client.html` — read-only client portal demo

## Running locally

Zero dependencies, no build step:

```sh
python3 -m http.server 4173
# → http://localhost:4173
```

## Documents

- [SPEC.md](SPEC.md) — product spec (problem, positioning, MVP scope)
- [ARCHITECTURE.md](ARCHITECTURE.md) — Swiss-law-compliant MVP architecture
- [DESIGN.md](DESIGN.md) — brand & design system

All client names, firms, custodians, and figures in the demo are fictional.
