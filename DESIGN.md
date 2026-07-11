# Alpinae — Design System

Brand and visual source of truth. Read this before any visual or UI decision.

## Brand

**Alpinae** is a Swiss-hosted, read-only reconciliation and reporting layer for
wealth managers. The buyer is a conservative, Excel-bound Geneva/Luxembourg firm
whose biggest fear is a wrong number reaching a client or regulator.

The aesthetic is therefore **"Swiss private bank annual report"** — engraved,
restrained, unhurried. Nothing glows, nothing bounces, nothing gradients.
Credibility comes from typography, whitespace, and hairline rules, the way it
does in a Pictet mandate letter. If a design choice would look at home in a
SaaS landing page template, it is wrong here.

Explicitly **not**: Bloomberg-terminal density, fintech gradients, dark mode,
glassmorphism, emoji in product UI, marketing superlatives.

## Palette

| Token | Hex | Use |
|---|---|---|
| `--paper` | `#FAF7F2` | Page background (warm ivory) |
| `--paper-raised` | `#FFFFFF` | Cards, tables, statement surfaces |
| `--ink` | `#1A2420` | Primary text (near-black pine) |
| `--ink-soft` | `#5C6660` | Secondary text, captions |
| `--pine` | `#1E4033` | The single accent: links, CTAs, active states |
| `--pine-deep` | `#142B23` | Hover on pine |
| `--hairline` | `#E4DED2` | Rules, borders, dividers |
| `--flag` | `#A63A2E` | Guardrail warnings ONLY (mismatch, stale data, blocked egress) |
| `--ok` | `#2E6B4F` | Reconciled / verified states |
| `--pending` | `#7A5C1E` on `#F7F0DF` | Staged / awaiting a human decision — the third and last status hue |

One accent colour. Red is reserved for the
verification guardrail and data-quality flags — it must never appear
decoratively, so that when it appears it means something.

## Typography

- **Headlines / display:** EB Garamond (500, 600). Classic banking serif.
- **UI / body:** Inter (400, 500, 600). Tight, neutral.
- **All numerals in tables, figures, and money:** IBM Plex Mono (400, 500)
  with `font-variant-numeric: tabular-nums`. Reconciliation is the product;
  numbers must align to the glyph.
- Small-caps letterspaced labels (`text-transform: uppercase; letter-spacing: .08em;
  font-size: 11–12px`) for section eyebrows, table headers, badges.

## Layout & texture

- Content widths, one per surface: **1080px** marketing (`landing.css`
  container), **1200px** advisor app main column, **960px** client portal
  (statement-like, centered). Side gutter **32px** everywhere (20px under
  640px). These are decisions — do not drift them per page.
- Hairline rules (`1px solid var(--hairline)`) instead of shadows wherever
  possible. Shadows, when used, are barely-there (`0 1px 2px rgb(26 36 32 / .06)`).
- Corners: 6px on controls, 10px on cards. Never pill-shaped except badges.
- Spacing scale: 4 / 8 / 12 / 16 / 24 / 32 / 48 / 72 / 120.
- Motion: opacity/transform fades ≤ 200ms only. No parallax, no scroll-jacking.

## Voice

Understatement. Short declarative sentences. Numbers over adjectives.
"Every figure traceable to its source statement" — never "revolutionary",
"seamless", "supercharge". French/German-adjacent formality is fine
("statements", "mandates", not "docs", "portfolios you manage" not "your book").

## Component & interaction guidelines

### Radii — one role each
`4px` printed-paper surfaces (report sheet) · `6px` controls (buttons, inputs,
chips) · `8px` list items (statements, queue rows) · `10px` cards & panels ·
`12px` overlays (modals, tour) · `999px` badges only. Never invent a fifth.

### Spacing roles
Panel padding `24px`; gap between sibling panels `20px`; gap inside lists
`10–12px`; stat/step grids `14–16px`; marketing grids `28px`; section rhythm
`72px` (`48px` tight). Inline one-off margins stay on the 4/8/12/16/24 scale.

### Type roles
| Role | Face / size |
|---|---|
| Display | EB Garamond 600 — h1 30–58, h2 28–38 |
| Panel/card titles | Inter 600 · 14–17px |
| Body / UI | Inter 400–500 · 13.5–16px |
| Meta, locations, hashes, dates | IBM Plex Mono · 10.5–13px, `--ink-soft` |
| All numerals | IBM Plex Mono, tabular. No exceptions. |
Diagram/chart annotations render **below** body size (cap SVG width so nominal
sizes hold; grid labels 10px, endpoint labels 11px).

### Buttons
Primary (pine fill) = the one action that advances the journey per view.
Ghost = secondary/dismissive. `btn-sm` inside panels; full size only in
hero/CTA bands. Disabled = 45% opacity + `title` reason, never hidden.
Hover changes background/colour only — nothing moves, lifts, or scales.

### Focus & keyboard
Every interactive element shows `:focus-visible` — 2px pine outline, 2px
offset. Anything clickable that isn't a `<button>`/`<a>` (e.g. directory rows)
gets `tabindex="0"` + Enter activation. Focus is never suppressed.

### Hover
Links darken (`--pine-deep`); rows and summaries tint `pine/4%`; trace
affordances turn their dotted underline solid. Cursor `pointer` on all of the
above. Transitions ≤ 200ms, opacity/colour only. Honour
`prefers-reduced-motion` globally (kills spinner, smooth scroll, transitions).

### Toggles & disclosure
`<details>` panels: `+` / `−` mono marker, summary hover tint, no animation.
Tabs: 2px bottom border marks active; hover darkens text only.

### Modals
**Decision modals** (guardrail, conflict) never close on scrim click — a
choice must be explicit; Esc backs out safely only because the queue keeps the
item reviewable. **Informational modals** (trace) close on scrim and Esc.
One primary action per modal, placed right.

### Status chips
Exactly three status hues: `--ok` (verified/consolidated), `--pending`
(staged/awaiting decision), `--flag` (blocked/mismatch/stale). Grey chips for
neutral terminal states (reassigned, superseded). A chip is never the only
signal — it always sits next to a sentence or a count.

### Demo chrome vs product surface — the hard rule
Anything that *explains the demo* lives on **ink-dark chrome**: the top
banner, the floating tour card, demo notices. Product surfaces (paper) carry
only copy the real product would ship — labels, empty states, helper
microcopy an advisor would genuinely see. If a paragraph teaches the viewer
about Alpinae rather than serving the advisor's task, it belongs in the tour,
not the page.

## Product-UI rules

- Every consolidated figure in the app carries a trace affordance (source icon).
- Warnings (`--flag`) are always accompanied by a plain-language sentence and a
  human confirmation step — never auto-resolve.
- Read-only is a visible property: the client portal carries a permanent
  "Read-only" badge; nothing in it looks editable.
- Timestamps and "as of" dates are always visible next to any valuation.
