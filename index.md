# Capital Markets — Self-Paced Learning Path — Index

Master index for all module files. See [`capital_markets_self_paced_learning_path.md`](./capital_markets_self_paced_learning_path.md) for the full original curriculum (all 14 modules, study method, and priorities) in one document. As each module gets its own expanded write-up (with extra examples, numbers, actor tables, and diagrams), it's linked below.

Modules 4–9 form the **Asset Class Roadmap** — Equity, Fixed Income, FX, Commodities, Rates, and Credit, each studied one at a time through the same eight-part pattern (What is it → Products → Security vs. Contract → Real trade example → Trade lifecycle → Identifiers → Exchange vs OTC/Bilateral/Cleared → Regulatory perspective). **Module 15 (Listed Derivatives)** sits apart from that roadmap — it's cross-cutting (Futures &amp; Listed Options on any of the six asset classes' underlyings), matching the work data model taxonomy's own "7. Listed Derivatives" node.

Example bank used throughout every module: **Bank Of Toronto** (fictional, for illustration only). Every module from 4 onward also maps its trade examples to the real Trade/Valuation Avro schema taxonomy used at work (e.g. `EquityOptionTrade.avsc`) — see the **Work data model reference** section in `CLAUDE.md` for the full taxonomy.

Also see: [**Abbreviations Glossary**](./abbreviations.md) ([HTML](./abbreviations.html)) — the most commonly used capital markets acronyms (identifiers, market structure, products, rates, valuation, settlement, risk, regulation) in one lookup reference.

Also see: [**Systems Landscape**](./systems-landscape.md) ([HTML](./systems-landscape.html)) — the actual Front/Middle/Back Office systems, data flows, and settlement cycles used in Canada, the US, the UK, and the EU, plus the regulatory bodies in each.

Also see: [**SFTR Reporting Deep Dive**](./sftr-reporting.md) ([HTML](./sftr-reporting.html)) — what SFTR is, exactly which trades must be reported, the lifecycle-event reporting model, and links to the primary regulatory sources.

Also see: [**UK DMO Reporting Deep Dive**](./dmo-uk-reporting.md) ([HTML](./dmo-uk-reporting.html)) — how Gilt-Edged Market Makers (GEMMs) report gilt auctions and secondary-market trading to the UK Debt Management Office: Auction, Daily, Weekly, and Aggregated reports, worked with real numbers.

Also see: [**UPI, UTI &amp; ANNA-DSB Deep Dive**](./upi-uti-dsb-deep-dive.md) ([HTML](./upi-uti-dsb-deep-dive.html)) — derivative identifiers explained end to end: UTI structure and generation waterfall, UPI reference-data templates across five asset classes, the ANNA-DSB organization that runs the system, and exactly which reporting regimes need which identifier.

---

## Modules

| # | Title | Priority | Status | Markdown | HTML |
|---:|---|---|---|---|---|
| 1 | Capital Markets Fundamentals | Standard | ✅ Available | [module-01-fundamentals.md](./module-01-fundamentals.md) | [module-01-fundamentals.html](./module-01-fundamentals.html) |
| 2 | Asset Class → Product → Instrument → Trade | 🔺 High | ✅ Available | [module-02-hierarchy.md](./module-02-hierarchy.md) | [module-02-hierarchy.html](./module-02-hierarchy.html) |
| 3 | Trade Lifecycle | 🔺 High (20%) | ✅ Available | [module-03-trade-lifecycle.md](./module-03-trade-lifecycle.md) | [module-03-trade-lifecycle.html](./module-03-trade-lifecycle.html) |
| 4 | **Equity** *(Asset Class Roadmap 1/6)* | Standard | ✅ Available | [module-04-equity.md](./module-04-equity.md) | [module-04-equity.html](./module-04-equity.html) |
| 5 | **Fixed Income** *(Asset Class Roadmap 2/6)* | 🔺 High (20%) | ✅ Available | [module-05-fixed-income.md](./module-05-fixed-income.md) | [module-05-fixed-income.html](./module-05-fixed-income.html) |
| 6 | **FX** *(Asset Class Roadmap 3/6)* | Standard | ✅ Available | [module-06-fx.md](./module-06-fx.md) | [module-06-fx.html](./module-06-fx.html) |
| 7 | **Commodities** *(Asset Class Roadmap 4/6)* | Standard | ✅ Available | [module-07-commodities.md](./module-07-commodities.md) | [module-07-commodities.html](./module-07-commodities.html) |
| 8 | **Rates** *(Asset Class Roadmap 5/6)* | 🔺 High (20%) | ✅ Available | [module-08-rates.md](./module-08-rates.md) | [module-08-rates.html](./module-08-rates.html) |
| 9 | **Credit** *(Asset Class Roadmap 6/6)* | 🔺 High (20%) | ✅ Available | [module-09-credit.md](./module-09-credit.md) | [module-09-credit.html](./module-09-credit.html) |
| 10 | Valuation | 🔺 Highest | ✅ Available | [module-10-valuation.md](./module-10-valuation.md) | [module-10-valuation.html](./module-10-valuation.html) |
| 11 | Risk & Exposure | 🔺 High (20%) | ✅ Available | [module-11-risk-exposure.md](./module-11-risk-exposure.md) | [module-11-risk-exposure.html](./module-11-risk-exposure.html) |
| 12 | Collateral & Margin | 🔺 Major focus (20%) | ✅ Available | [module-12-collateral-margin.md](./module-12-collateral-margin.md) | [module-12-collateral-margin.html](./module-12-collateral-margin.html) |
| 13 | Parties, LEIs & Market Infrastructure | Standard (10%) | ✅ Available | [module-13-parties-leis.md](./module-13-parties-leis.md) | [module-13-parties-leis.html](./module-13-parties-leis.html) |
| 14 | Regulatory Reporting + IT View | Standard | ✅ Available | [module-14-regulatory-reporting.md](./module-14-regulatory-reporting.md) | [module-14-regulatory-reporting.html](./module-14-regulatory-reporting.html) |
| 15 | **Listed Derivatives** *(cross-cutting — not part of the 6-asset-class roadmap)* | Standard | ✅ Available | [module-15-listed-derivatives.md](./module-15-listed-derivatives.md) | [module-15-listed-derivatives.html](./module-15-listed-derivatives.html) |

Priority column reflects the weighting in the "Priority for My Current Work" table of the original curriculum doc. Modules 4–9 (the Asset Class Roadmap) are built **one at a time, in order** — Equity first. **The full 14-module path, plus the cross-cutting Module 15 (Listed Derivatives), is now complete.** Module 14 closes the loop by fully annotating the curriculum's own capstone `IRS12345` trade record field-by-field against everything built in Modules 1–13 and 15 — see the curriculum doc's own "Final End-to-End Case Study" to test it cold.

---

## Reference

| Resource | Markdown | HTML |
|---|---|---|
| Abbreviations Glossary — identifiers, market structure, products, rates, valuation, settlement, risk, regulation | [abbreviations.md](./abbreviations.md) | [abbreviations.html](./abbreviations.html) |
| Systems Landscape — Front/Middle/Back Office systems, data flows &amp; settlement cycles in Canada, US, UK &amp; EU, plus regulators | [systems-landscape.md](./systems-landscape.md) | [systems-landscape.html](./systems-landscape.html) |
| SFTR Reporting Deep Dive — what SFTR is, which trades must be reported, lifecycle-event reporting model &amp; official links | [sftr-reporting.md](./sftr-reporting.md) | [sftr-reporting.html](./sftr-reporting.html) |
| UK DMO Reporting Deep Dive — how GEMMs report gilt auctions &amp; secondary-market trading to the UK DMO (Auction/Daily/Weekly/Aggregated reports) | [dmo-uk-reporting.md](./dmo-uk-reporting.md) | [dmo-uk-reporting.html](./dmo-uk-reporting.html) |
| UPI, UTI &amp; ANNA-DSB Deep Dive — derivative identifiers explained: UTI generation waterfall, UPI reference-data templates, ANNA-DSB, where each is used | [upi-uti-dsb-deep-dive.md](./upi-uti-dsb-deep-dive.md) | [upi-uti-dsb-deep-dive.html](./upi-uti-dsb-deep-dive.html) |

---

## Conventions for every module

- Each module file follows: **Concept → Real trade example (with numbers) → Actors involved → ASCII flow diagram → Takeaway → Recap quiz.**
- Example bank is always **Bank Of Toronto** — never the real BMO name (this repo happens to live under a `bmo` path, but that's incidental).
- Each `.md` module has a matching `.html` version (same content, styled as a standalone page) once built.
- Update this table's **Status** and links as new modules are written.
