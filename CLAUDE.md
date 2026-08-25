# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is **not a software project**. It is a single-document, self-paced study plan (`capital_markets_self_paced_learning_path.md`) for learning Capital Markets from an IT/reporting perspective. There is no source code, build system, linter, or test suite.

## Document structure

The learning path follows one core mental model, repeated throughout the doc:

```
Asset Class → Product → Instrument/Security → Trade → Valuation → Exposure/Risk → Collateral/Margin → Clearing & Settlement → Regulatory Reporting
```

It is organized into 14 modules. Modules 1–3 are foundational (Fundamentals, Asset Class/Product/Instrument/Trade hierarchy, Trade Lifecycle). Modules 4–9 are the **Asset Class Roadmap** — one module per asset class (Equity, Fixed Income, FX, Commodities, Rates, Credit), each studied through the same eight-part pattern before moving to the next: (1) What is it? (2) What are the products? (3) What exactly is being traded (security vs. contract)? (4) Real-world trade example (Buyer → Seller → Trade) (5) Trade lifecycle (Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting) (6) Identifiers (ISIN, CUSIP, SEDOL, Ticker, LEI, UPI, FIGI) (7) Exchange vs OTC vs Bilateral vs Centrally Cleared (8) Regulatory perspective (MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO, etc.). Modules 10–14 cover cross-cutting topics that build on the asset classes (Valuation, Risk & Exposure, Collateral & Margin, Parties/LEIs, Regulatory Reporting), followed by an end-to-end case study and a self-study pacing guide. Standalone **reference modules** outside this numbering (Abbreviations Glossary, Systems Landscape) cover material that cuts across every module.

Stated priorities for the current work focus (see the "Priority for My Current Work" table near the end of the doc): Trade Lifecycle, Fixed Income + Rates + Credit, Valuation, and Collateral + Exposure are weighted highest. Within the Asset Class Roadmap, build **one asset class at a time**, in the order given (Equity first), rather than all six at once.

## Work data model reference — Avro schema taxonomy

The user's actual job works against a Trade/Valuation Avro schema taxonomy, organized by asset class → product, one `.avsc` per Trade and (usually) one per Valuation:

```
CAPITAL MARKETS
├── 1. Fixed Income        — Bond (Trade only: BondTrade.avsc)
├── 2. Interest Rate       — Cap/Floor, IR Forward Debt, FRA, Interest Rate Swap, Swaption
│                            (each: <Product>Trade.avsc + <Product>Valuation.avsc)
├── 3. Foreign Exchange    — FX, FX Option, FX/IR Exotic (each: Trade + Valuation)
├── 4. Equity              — Equity (Trade only), Equity Option, Equity Swap (Trade + Valuation)
├── 5. Commodities         — Commodity Option, Commodity Spot/Forward, Commodity Swap
│                            (each: Trade + Valuation), Commodity Warrant (Trade only)
├── 6. Credit              — Credit Default Swap (Trade + Valuation)
└── 7. Listed Derivatives  — Futures, Listed Options (each: Trade + Valuation)
                             — cross-cutting: not one of the 6 asset classes above,
                               a Future/Listed Option can reference any underlying
```

This taxonomy is the reason Module 8 (Rates) and Module 9 (Credit) exist and what they must cover product-for-product. It's also why **Module 15: Listed Derivatives** exists as a standalone, non-asset-class module — Futures and Listed Options are traded across every asset class, so they don't belong folded into just one.

**Schema-mapping convention:** every module's trade examples (Section 4 of the eight-part pattern) should include a short callout naming the corresponding `.avsc` file(s) from this taxonomy, e.g. `Schema: EquityOptionTrade.avsc / EquityOptionValuation.avsc`. This ties the conceptual material directly back to the user's real data model. Two asset-class nodes deliberately have **no Valuation schema** for their plain product (`BondTrade.avsc`, `CommodityWarrantTrade.avsc`) — when writing those sections, call out *why* (valuation is handled by a shared/generic pricing model rather than a product-specific one) rather than silently omitting the mapping.

## Content conventions

- Never use the real bank name as the example bank in explanations or examples. Use a fictional example bank called **"Bank Of Toronto"** instead.

## Working in this repo

- When asked to expand, quiz, or add content, edit `docs/capital_markets_self_paced_learning_path.md` directly and preserve its existing structure (module numbering, `###` subheadings of Learn/Example/Goal, fenced `text` diagrams for conceptual flows).
- All module and reference documents (`.md`/`.html`) live under `docs/`. Only `index.md` and `index.html` stay in the repo root.
- Keep explanations grounded in the IT/reporting angle the doc emphasizes (what a field means, not just its definition) rather than pure finance theory.
- No commands to run, no dependencies to install.
