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
| `--flag` | `#A63A2E` | Guardrail warnings ONLY (mismatch, stale data) |
| `--ok` | `#2E6B4F` | Reconciled / verified states |

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

- Max content width 1120px marketing, full-bleed app shell for the portal.
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

## Product-UI rules

- Every consolidated figure in the app carries a trace affordance (source icon).
- Warnings (`--flag`) are always accompanied by a plain-language sentence and a
  human confirmation step — never auto-resolve.
- Read-only is a visible property: the client portal carries a permanent
  "Read-only" badge; nothing in it looks editable.
- Timestamps and "as of" dates are always visible next to any valuation.
