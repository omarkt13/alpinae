# Alpinae — MVP Architecture (Swiss-law compliant)

Working draft. Covers the *real* MVP (report-first reconciliation engine), not
the demo in this repo. The demo is static mock-data HTML; nothing here is built yet.

---

## 1. The compliance frame (what "Swiss law" actually requires)

Two jurisdictions matter for the first customers (GADD profile: Luxembourg
ManCo + Geneva branch):

**Switzerland**
- **nFADP** (revised Federal Act on Data Protection, in force Sept 2023):
  client statements are personal data; requires processing register, DPAs with
  processors, breach notification, privacy-by-design. Cross-border transfer
  only to adequate jurisdictions or with safeguards.
- **Professional secrecy** under FinIA/FinSA for portfolio managers (and
  Art. 47 BankA where bank data is involved): client-identifying data handed
  to a third party (us) is the sensitive act. Outsourcing is permitted with
  contractual controls, audit rights, and confidentiality — but every layer of
  our stack that can *see* client data becomes part of that story.
- **FINMA outsourcing guidance** (Circular 2018/3 for banks; analogous
  expectations flow down to FinIA portfolio managers via their supervisory
  organisations): inventory of outsourced functions, audit/access rights,
  data location known and controlled, exit plan.

**Luxembourg (for GADD's ManCo)**
- **CSSF Circular 22/806** (outsourcing incl. cloud): notification duties,
  cloud-provider register, audit rights, EU/adequate-country data location.
- **GDPR** + professional secrecy (Art. 41 LFS, relaxed for outsourcing since
  2018 but with client-information duties).

**The practical bar** — what a conservative firm's compliance officer will ask:
1. Where is the data, physically? (Answer must be: Switzerland, one region, listed.)
2. Who can access it? (Named roles, logged, MFA.)
3. Is any of it processed by a US-controlled entity? (CLOUD Act exposure —
   AWS Zurich is legally defensible but *narratively* weak; a Swiss-owned
   provider makes the pitch airtight.)
4. Can we audit it? (Append-only log, export on demand, contractual audit right.)
5. What happens when we leave? (Export everything, cryptographic deletion.)

**Positioning consequence:** compliance is the moat, so over-comply. Choose the
architecture that makes question 3 a non-question.

## 2. Hosting decision

| Option | Verdict |
|---|---|
| **Exoscale** (Swiss-owned, CH-GVA-2 Geneva / CH-DK-2 Zurich, S3-compatible object storage, managed Postgres) | **Recommended.** Swiss entity, Swiss datacenters, no CLOUD Act story to explain, cheap (≈ CHF 100–200/mo for MVP). |
| Infomaniak (Geneva) | Good fallback; Swiss-owned, strong sovereignty branding; weaker managed-DB/K8s story. |
| AWS eu-central-2 (Zurich) | Legally workable, best tooling — but a US-parent processor is exactly the objection that stalled the Wise deal. Not worth the sales friction. |
| Fly.io (current ChiefRiskBot host) | **No.** No Swiss region, US company. Do not reuse. |

Everything — compute, Postgres, object storage, backups — in one Swiss region.
No CDN in front of authenticated surfaces. TLS 1.3; optional per-firm IP allowlist.

## 3. System shape (deliberately boring monolith)

```
                      ┌─────────────────────────── Swiss region ──┐
 Browser (advisor) ──▶│  FastAPI monolith                          │
                      │   ├─ Auth (sessions, TOTP MFA, RBAC)       │
                      │   ├─ Ingestion API  ──▶ ClamAV scan        │
                      │   ├─ Parser registry (per custodian)       │
                      │   ├─ Reconciliation engine                 │
                      │   ├─ Report renderer (xlsx/pdf)            │
                      │   └─ Audit writer (append-only)            │
                      │  Postgres (encrypted at rest)              │
                      │  Object store: raw statements, immutable,  │
                      │    versioned, checksummed (SSE)            │
                      └────────────────────────────────────────────┘
```

One service, one database, one bucket. No queues, no microservices, no
multi-region until a paying firm forces it. The demo portal and marketing site
stay static and separate.

## 4. AI on client documents — Swiss-hosted options (evaluated Jul 2026)

> **Decision (15 Jul 2026):** provider = **Infomaniak AI Tools** (their
> published FINMA Circular 2018/3 mapping was the deciding artifact; Safe
> Swiss Cloud is the named fallback), and parser strategy is
> **general-first** — the AI-assisted parser is the default door for any
> document, with recurring formats graduated to deterministic parsers.
> This puts the Infomaniak DPA on the pilot's critical path. Details and
> consequences: alpinae-engine/ARCHITECTURE.md.


The constraint stands: raw client statements must never reach a US-hosted LLM
API (Anthropic, OpenAI direct) — one API call breaks the entire Swiss story.
But statement parsing *does* benefit from a vision-language model (custody PDFs
are messy, multi-layout, multi-language), and Swiss-hosted inference now exists.
The options, in the order we should use them:

**Step 0 — now, for the first real statement set (validation):** process it
locally on our own machine. `pdfplumber`/`openpyxl` for extraction; if a VLM
helps, run one locally via Ollama (e.g. Qwen3-VL — strong document OCR/parsing,
32-language support). Client data never leaves the laptop; zero procurement;
unblocks the hand-reconciliation validation immediately.

**Step 1 — pilot: Swiss-owned inference API** (managed, pay-per-token, no ops):

| Provider | What it is | Notes |
|---|---|---|
| **Infomaniak AI Tools** | OpenAI-compatible API, Swiss datacenters, Swiss-owned. Open models incl. Qwen 3, Gemma 3n, Llama, Mistral, Whisper | Prompts not logged or used for training; data never leaves CH. Cheap. **Verify current vision-model lineup + sign DPA before first real document.** |
| **Safe Swiss Cloud "Private AI" API** | Unified API over curated open models — incl. multimodal Llama 4 Maverick, plus Apertus, DeepSeek, Mistral | All hosted in CH by Swiss-owned entities; positioned exactly at this compliance niche. |
| **Swisscom Swiss AI Platform** | Sovereign Swiss platform; hosts Apertus (the EPFL/ETHZ open Swiss model) | Strongest enterprise/compliance brand a Geneva buyer can hear. Apertus is text-only → pair with a deterministic OCR layer. |

**Step 2 — production end-state: self-hosted VLM in our own Swiss VPC.**
Exoscale GPU instance + vLLM serving Qwen3-VL (open weights). The strongest
possible answer to a compliance officer: *the model runs on our machines, in
Switzerland; no third party — Swiss or otherwise — ever sees a document.*
More ops, ~CHF 1–2k/mo GPU; adopt once a paying firm justifies it.

**Fallback (capability, weaker story): Azure OpenAI, Switzerland North.**
GPT-4o deployable with in-region processing; FINMA-aligned contracts; widely
used by Swiss financial institutions. But a US-parent processor is precisely
the objection we sell against — keep as a documented fallback, not the default.

**Role of the model at every step: proposal, never authority.** The VLM locates
tables, extracts candidate rows, and proposes instrument matches. Deterministic
checks (row totals must cross-foot to statement totals, ISINs must validate)
and human confirmation remain the gate before anything is staged. Golden-file
tests per custodian format regardless of which engine extracts.

## 5. Traceability as a data model (the moat, built first)

Every consolidated number is a derived fact with lineage. First-class tables:

- `statements` — immutable raw file ref (object key, sha256, custodian,
  as-of date, uploaded_by, client_space).
- `extracted_rows` — one per source line: statement_id, page/sheet, line ref,
  raw text, parsed qty/price/ccy. Never mutated; re-parses create new versions.
- `instruments` — canonical instrument (ISIN-first) + `instrument_aliases`
  (source string → instrument, with who/when confirmed).
- `fx_rates` — rate, date, source.
- `consolidated_positions` — the output view: each cell links the set of
  `extracted_row` ids + fx_rate ids + transform notes that produced it.
- Report cells store fact ids, so any figure in any generated report resolves
  to source statements in one query. "Where did this number come from" is a
  SELECT, not archaeology.

## 6. Guardrails & audit

- **File-to-client verification:** on upload, parse the account-holder line and
  fuzzy-compare against the client space; mismatch blocks reconciliation behind
  an explicit human reassignment/confirmation. Both the flag and the human
  decision are audit events.
- **Valuation-date policing:** consolidation across statements with different
  as-of dates is allowed but always flagged in the UI and stamped in the report.
- **Append-only audit log:** hash-chained rows (each entry embeds the previous
  entry's hash) covering ingest, parse, confirm, reconcile, report-generate,
  and every read access. Exportable to CSV for the firm's own compliance file.
- **Read-only by construction:** no write-back integrations exist in the
  codebase at all — not disabled, absent.

## 7. AuthN/AuthZ

- Session cookies, TOTP MFA from day one (cheap, disproportionate compliance value).
- Roles: `advisor` (scoped to assigned client spaces), `compliance`
  (read-all + audit export), `admin` (user management, no silent data access —
  admin reads are logged like everyone's). `client` role exists in the schema
  but is disabled until the portal phase.
- Tenancy: single-tenant-per-firm deployment for the first 2–3 firms (one VM +
  one DB each). Multi-tenancy is a scaling problem we should be lucky to have;
  per-firm isolation is also the easiest possible compliance answer.

## 8. Report generation

Server-side render into the firm's existing format — openpyxl into their actual
Excel template, WeasyPrint for PDF. The template is per-firm configuration, not
product surface. Every generated artifact is stored, checksummed, and logged.

## 9. Sequencing (mirrors the spec)

1. **Validation before code:** hand-reconcile one real GADD client account from
   its actual statement set. If manual reconciliation fails, that's the problem
   to solve — no UI work until it succeeds.
2. Parsers for that account's exact custodians → reconciliation engine →
   traceability model → report in GADD's format → audit log. CLI/notebook is an
   acceptable first interface; the advisor portal comes after the numbers are
   trusted.
3. Portal (internal-only) → client-facing read-only view as deliberate opt-in.

## 10. Cost sketch (MVP, one design partner)

Exoscale: 1 small instance + managed Postgres + object storage ≈ **CHF 100–200/mo**.
No GPU until step 2 of the LLM ladder. Backups to a second Swiss zone included.
