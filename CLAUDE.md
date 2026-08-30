# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is **not a software project**. It is a single-document, self-paced study plan (`capital_markets_self_paced_learning_path.md`) for learning Capital Markets from an IT/reporting perspective. There is no source code, build system, linter, or test suite.

## Document structure

The learning path follows one core mental model, repeated throughout the doc:

```
Asset Class → Product → Instrument/Security → Trade → Valuation → Exposure/Risk → Collateral/Margin → Clearing & Settlement → Regulatory Reporting
```

The original curriculum doc is organized into 14 modules. Modules 1–3 are foundational (Fundamentals, Asset Class/Product/Instrument/Trade hierarchy, Trade Lifecycle). Modules 4–9 are the **Asset Class Roadmap** — one module per asset class (Equity, Fixed Income, FX, Commodities, Rates, Credit), each studied through the same eight-part pattern before moving to the next: (1) What is it? (2) What are the products? (3) What exactly is being traded (security vs. contract)? (4) Real-world trade example (Buyer → Seller → Trade) (5) Trade lifecycle (Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting) (6) Identifiers (ISIN, CUSIP, SEDOL, Ticker, LEI, UPI, FIGI) (7) Exchange vs OTC vs Bilateral vs Centrally Cleared (8) Regulatory perspective (MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO, etc.). Modules 10–14 cover cross-cutting topics that build on the asset classes (Valuation, Risk & Exposure, Collateral & Margin, Parties/LEIs, Regulatory Reporting), followed by an end-to-end case study and a self-study pacing guide.

Beyond the original 14, the repo has grown its own numbered modules: **Module 15 (Listed Derivatives)** is cross-cutting — Futures & Listed Options on any of the six asset classes' underlyings. **Modules 16–23** are standalone **deep-dive/reference modules**, each expanding one topic the first 15 modules already touched into a full write-up (theory, worked examples with real numbers, an actors cast, a recap quiz) rather than adding a new asset class or lifecycle stage: 16 SFTR Reporting, 17 UK DMO Reporting, 18 UPI/UTI & ANNA-DSB, 19 Interest Rate Swap (IRS), 20 MiFID II/MiFIR Reporting, 21 ISDA Product Taxonomy, 22 Hedging, 23 Forward/Swap/Option. `index.md`'s Modules table is the source of truth for the current full list and status. Only the **Abbreviations Glossary** and **Systems Landscape** stay outside the numbering entirely, by design — both are cross-cutting lookup material meant to be consulted alongside any module, not a topic deep dive in their own right.

Stated priorities for the current work focus (see the "Priority for My Current Work" table near the end of the doc): Trade Lifecycle, Fixed Income + Rates + Credit, Valuation, and Collateral + Exposure are weighted highest. Within the Asset Class Roadmap, build **one asset class at a time**, in the order given (Equity first), rather than all six at once.

## Content conventions

- Never use the real bank name as the example bank in explanations or examples. Use a fictional example bank called **"Bank Of Toronto"** instead.

## Working in this repo

- When asked to expand, quiz, or add content, edit `docs/1-capital-markets/capital_markets_self_paced_learning_path.md` directly and preserve its existing structure (module numbering, `###` subheadings of Learn/Example/Goal, fenced `text` diagrams for conceptual flows).
- All module and reference documents (`.md`/`.html`) live under `docs/`, split into two subdirectories: `docs/1-capital-markets/` (all non-reporting modules and reference material — Modules 1–13, 15, 19 (IRS), 22 (Hedging), 23 (Forward/Swap/Option), Abbreviations, Systems Landscape, the full curriculum doc) and `docs/2-reporting/` (reporting-focused modules and deep dives — Module 14 Regulatory Reporting, 16 SFTR, 17 UK DMO, 18 UPI/UTI/ANNA-DSB, 20 MiFID, 21 ISDA Product Taxonomy). Only `index.md` and `index.html` stay in the repo root. Modules 16+ use the `module-NN-topic-name.md`/`.html` filename pattern, same as Modules 1–15.
- Keep explanations grounded in the IT/reporting angle the doc emphasizes (what a field means, not just its definition) rather than pure finance theory.
- No commands to run, no dependencies to install.
