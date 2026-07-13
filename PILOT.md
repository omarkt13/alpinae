# Pilot plan — GADD, one real account

Drafted 15 Jul 2026. The landing page makes the promise; this document is
how we keep it: **"One client. One week. No integration."**

## Definition of done

For one real GADD client account, all of the following hold:

1. Every source statement for the period is ingested, archived, and
   holder-verified.
2. The engine's consolidated total matches GADD's own hand reconciliation
   **figure for figure** — or every delta is explained and documented
   (a delta the engine can defend is a *finding*, not a failure; see the
   dry run, where the engine exposed an unsubstantiated USD 1.85M position
   in the reference aggregate itself).
3. Output delivered as the **Alpinae standard consolidated statement**
   (identity header, summary, by-source, positions, lineage annex).
4. The audit chain verifies end to end and exports.
5. GADD's advisor says, unprompted or asked plainly: *"I would show this
   to the client."* That sentence is the pilot's real exit criterion.

## Pilot shape: concierge

We operate the engine; GADD sees inputs and outputs, never software. No
hosting, no auth, no web UI is needed to prove value — those come *after*
parity is proven (phase 1, Exoscale). This keeps the pilot's surface area
tiny and its timeline honest.

## Workstreams

### WS1 — Commercial & data (Omar) · BLOCKING

| Task | Notes |
|---|---|
| Pilot letter to GADD | Scope (one account, one period), confidentiality, data handling, deletion on request. One page. |
| Agree the processing location | Options: our machine (state where it physically is) or a Swiss VM we control. Record the answer — it will be asked. |
| Secure handover channel | Agreed encrypted channel for the statement set — not plain email attachments. |
| Obtain the inputs | (a) full statement set for one client account, one period; (b) GADD's own consolidated figure for the same period (the parity reference); (c) the list of custodians involved. |

### WS2 — AI provider (Omar) · CRITICAL PATH

General-first parsing cannot touch real documents without the Infomaniak
DPA. Checklist is in alpinae-engine/ARCHITECTURE.md: briefing → written
confirmation of (FINMA 2018/3 mapping, vision-model lineup, logging
policy) → DPA review → sign → key + `ALPINAE_AI_DPA_CONFIRMED=yes`.

- **Fallback trigger:** if written answers are weak or slow beyond ~2
  weeks, switch to Safe Swiss Cloud (explicit vision/OCR lineup, stated
  no-logging) rather than stall the pilot.
- **Interim:** validation runs can use `local` (Ollama on our machine) —
  slower, weaker, but unblocks WS4 if the DPA lags.

### WS3 — Engine build (Claude) · DONE 15 Jul 2026

| # | Task | Status |
|---|---|---|
| 1 | Wire the assisted parser into extraction (`alpinae parse <sha> --engine assisted`), engine recorded per statement in the audit log | ✅ |
| 2 | Parsed-vs-attested valuation-date check → data-quality flag | ✅ |
| 3 | **Alpinae standard statement v1**: identity header (firm name + logo slot), summary, by-source with dates, positions with weights, **lineage annex sheet** (every contribution: source file, location, raw text, FX) | ✅ |
| 4 | `alpinae check --run-id N --aggregate <csv>`: compare any aggregate against the approved run — unsubstantiated / missing / mismatched positions listed with amounts (productize the dry run's Blackstone catch) | ✅ |
| 5 | `alpinae audit export` → CSV for the firm's compliance file | ✅ |
| 6 | Ops hygiene: document/wrap the `PYTHONPATH=src` invocation quirk (`./alpinae.sh`) | ✅ |

Beyond plan (same date): the full phase-1 backend surface landed early —
users & sessions in the store, `auth.py` (scrypt passwords, opt-in TOTP,
hashed session tokens), and a FastAPI app (`alpinae serve`) exposing the
whole pipeline over HTTP with session-cookie auth, CSRF, and
advisor/compliance/admin roles. The concierge pilot still runs on the CLI;
the API is the hosted-pilot foundation, not a pilot dependency.

### WS4 — Validation sprint (joint) · T0 = statements + parser path in hand

- **Day 1 — ingest & parse.** All statements through the general door.
  Decide the graduation list: formats that will recur every quarter get
  deterministic parsers during or right after the sprint.
- **Day 2 — match & reconcile.** Confirm proposals using GADD's own
  instrument vocabulary (these confirmations are the firm's permanent
  alias memory). Run recon; compare against GADD's figure; iterate on
  deltas until parity or documented findings.
- **Day 3 — deliver.** Standard statement + lineage annex + aggregate
  check + audit export. Walkthrough with GADD's tech lead; ask the exit
  question.

### WS5 — Decision & next

Debrief against the exit criteria, then in order: pricing conversation;
hosted-pilot scoping (Exoscale, thin upload UI, auth); second account or
second custodian set; the reference ask — in this market a reference
customer is worth more than any feature.

## Sequence & critical path

```
now ──────────────► T0 ───────► T0+3d ─────► decision
WS3 (engine tasks)  │            │
WS1 (GADD asks)  ───┤            │
WS2 (Infomaniak) ───┘  WS4 sprint┘   WS5
```

T0 = statement set received AND a lawful parser path exists (DPA signed,
or `local` accepted for validation). WS3 completes before T0. The
week-long promise is measured from T0.

## Risks

| Risk | Mitigation |
|---|---|
| DPA slower than GADD | `local` Ollama for the validation sprint; Safe Swiss Cloud fallback trigger at ~2 weeks |
| Statements are scanned images | Confirm early with one sample page; this is exactly what the vision models are for — but it raises the OCR bar; test before promising the week |
| GADD's own reference figure is wrong | Expected, honestly: deltas the engine can trace are the product working. Frame as findings, never as "your numbers are wrong" |
| Data-location objection | Offer processing on a Swiss VM we control, or on-site |
| No control totals on some statements | The uncontrolled-parse path exists and is audited; those documents get an explicit human check of row sums |

## Explicitly out of pilot scope

Hosting, auth, web UI, client portal, capital calls / cash-flow documents,
multi-account, firm-branded templates. Every one of these is a phase-1+
conversation that only matters if the exit criterion is met.
