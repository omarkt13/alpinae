# Product Spec — Alpinae

Consolidated reconciliation & reporting for Swiss wealth managers.
Based on discovery with GADD & Cie (Luxembourg/Geneva/Stockholm) and analysis
of the Swiss/EU wealth-management market.

---

## 1. The problem

Wealth managers who run client accounts end-to-end hold positions across
multiple vehicles: their own proprietary fund plus external banks and asset
managers. Today they have no reliable, low-effort way to see a client's
**total consolidated exposure** across all of those sources.

The current workflow is manual and Excel-based. The proprietary fund arrives as
an Excel printout; each third-party asset manager sends its own
differently-formatted statement. Someone reconciles them by hand. This is slow,
error-prone, and hard to audit — a real liability in a CSSF/FINMA-regulated
environment where a manual error reaching a client or regulator is a serious
problem.

## 2. Market context

- **The market is conservative and Excel-bound.** GADD is representative, not
  an outlier: Excel is standard, trust in new tools is low, no client portals,
  elderly client base.
- **Compliance is table stakes.** CSSF (Luxembourg) and FINMA (Switzerland)
  rules on confidential client data set a hard bar. This is the same friction
  stalling incumbent CRM deals (e.g. GADD's paused Wise evaluation).
- **Wealth transfer is the tailwind.** The market *will* change as the next
  generation inherits both clients and tooling decisions. They won't tolerate
  Excel. The winning product is one the old guard accepts today and the next
  generation adopts as the system of record tomorrow.
- **Accuracy beats polish.** Clients care that the numbers are *right*, not
  that reports are branded. The bar is correctness and traceability, not design.

## 3. Positioning

A **Swiss-hosted, read-only reconciliation and reporting layer** that sits on
top of the tools firms already use. It does the one hard, valuable thing —
consolidating positions across the proprietary fund and third-party
statements — and surfaces the result as a report and an optional portal.

Deliberately **not** a CRM or system of record. That territory is crowded
(Wise, incumbents), high-friction, and where adoption stalls. This product is
additive, narrow, and defensible.

### Why it's the lowest-friction wedge

| Friction axis | Design choice that removes it |
|---|---|
| Behavior change | Firms keep Excel and existing statements; they get an output, not a new system |
| Data movement | Swiss-hosted, access-controlled; read-only, no writes back to any system of record |
| Approval/decision | Read-only + human-approved output lowers the compliance/security review bar |
| Reversibility | Additive layer; stop using it and nothing breaks |

### Moat

- **Cross-custodian reconciliation** — the genuinely hard part, and the part
  incumbents built on clean single-source data never solve.
- **Compliance-first / Swiss-housed / fully auditable** — turns the market's
  biggest blocker into the differentiator.
- **Incumbency on clean data** — once you produce the consolidated view every
  cycle, you hold the firm's reconciled, structured position data, earning the
  right to expand as firms turn over.

## 4. User jobs

**Manager / CIO**
- See a client's total consolidated exposure across the proprietary fund + all
  third-party accounts, without hand-reconciling spreadsheets.
- Trust that every figure is accurate and traceable back to its source statement.
- Produce the client report in the existing format without manual assembly.
- Answer "what's this client's total risk right now" in minutes.

**Compliance / audit**
- Trace any consolidated figure back to the source statement it came from.
- Have a complete log of what data entered, what was produced, who accessed it.
- Keep confidential client data within an approved (Swiss) jurisdiction.

**Next-generation principal (future)**
- Access a live, web-native consolidated view rather than a static spreadsheet.

**End client (optional, later)**
- View their own consolidated position self-serve, read-only.

## 5. Core features

1. **Client directory** — a lightweight native list of clients, each with a
   space where their statements land. A thin organizing layer, **not** a CRM,
   built natively (no external dependency).
2. **Statement ingestion & routing** — proprietary fund Excel printout
   (structured, easy) + third-party statements (varied formats, the real work).
   Drag-and-drop into the correct client space. Scope bounded to the specific
   banks/managers the firm's accounts actually touch.
3. **File-to-client verification (guardrail)** — the tool reads the statement
   and flags when the account name doesn't match the client space it was
   dropped into, with a confirmation step before reconciliation.
4. **Reconciliation engine** — consolidate holdings across sources into one
   accurate exposure view; resolve instrument-naming, currency, and
   valuation-date mismatches. *This is the product.*
5. **Traceability** — every figure links back to the source statement.
6. **Report generation** — the consolidated view in the firm's existing
   format, automated, no manual assembly.
7. **Audit logging** — full log of ingestion, output, and access.
8. **Read-only web portal (optional)** — internal-access by default;
   client-facing access only as a deliberate opt-in.
9. **Access control** — role/permission-based; permissioning *is* the
   compliance product.

### Explicitly out of scope

- No CRM / system of record.
- No CRM *integration* as a dependency (optional convenience later, never a
  requirement).
- No write actions, no decisions, no autonomy — read-only throughout, human
  approves before output is used.
- No capital calls, subscriptions, e-sign, data rooms.
- No branded report re-creation as a headline feature.

## 6. MVP

**Goal:** prove the riskiest assumption — that you can accurately and traceably
consolidate one real client account across its actual source statements.

**Scope:**
1. Ingest one real client's proprietary-fund Excel printout + that client's
   third-party statements.
2. Reconcile into one accurate consolidated exposure view.
3. Every figure traceable to source.
4. Output the report in the firm's existing format.
5. Basic audit log of what was ingested and produced.

**Not in MVP:** the portal, client-facing access, multi-firm support, polish.

**Validation before building screens:** obtain one real client account's full
statement set from GADD and attempt to **hand-reconcile** it. If it can be done
by hand, it can be built. If not, that is the problem to solve before any UI.

## 7. Open questions / next steps

- Get one real account's full statement set from GADD's tech lead.
- Confirm the finite list of third-party banks/asset managers to parse.
- Internal-only vs. client-facing portal sequencing (internal first).
- Confirm hosting/data-residency requirements against CSSF/FINMA specifics
  (see ARCHITECTURE.md).
- Identify the first reference customer.

## 8. This repo (demo phase)

What's here now is **not the MVP** — it's the pitch surface:
- `index.html` — marketing page for the Alpinae positioning.
- `demo/advisor.html` — mock advisor portal (client directory, ingestion with
  guardrail, reconciliation with traceability, report, audit log).
- `demo/client.html` — mock read-only client view.
All data is fictional and hardcoded. No backend exists.
