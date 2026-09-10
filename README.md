# Capital Markets — Self-Paced Learning Path

A self-paced study plan for learning Capital Markets from an IT/reporting perspective — one recurring mental model, worked through 15 core modules plus 10 numbered deep-dive/reference modules (16–25) and standalone reference material:

```
Asset Class → Product → Instrument/Security → Trade → Valuation → Exposure/Risk → Collateral/Margin → Clearing & Settlement → Regulatory Reporting
```

## Start here

Open **[`index.md`](./index.md)** (or **[`index.html`](./index.html)** for the styled version) — it's the master index linking every module and reference document.

## Layout

```
.
├── index.md / index.html      # Master index — start here
├── CLAUDE.md                  # Guidance for Claude Code working in this repo
├── README.md                  # This file
├── examples/                                     # Standalone "Complete Worked Example" files
│   ├── Interest_Rate_Swap_IRS_Complete_Example.{md,html}
│   ├── FRA_Forward_Rate_Agreement_Complete_Example.{md,html} … Cross_Currency_Swap_Complete_Example.{md,html}
│   ├── Cash_Equity_Trade_Complete_Example.{md,html}, Fixed_Income_Bond_Complete_Example.{md,html}, FX_Spot_Complete_Example.{md,html}, Commodity_Physical_Complete_Example.{md,html}
│   ├── Repo_Securities_Financing_Complete_Example.{md,html}
│   ├── FX_Forward_Complete_Example.{md,html}, FX_Option_Complete_USDCAD_Example.{md,html}, Commodity_Swap_Complete_Example.{md,html}, Equity_Total_Return_Swap_Complete_Example.{md,html}, Credit_Default_Swap_CDS_Complete_Example.{md,html}
│   └── Listed_Futures_and_Options_Complete_Example.{md,html}
└── docs/
    ├── 1-capital-markets/                            # Non-reporting modules & reference material
    │   ├── capital_markets_self_paced_learning_path.md   # Full original curriculum, all modules in one doc
    │   ├── module-01-fundamentals.{md,html}  … module-13-parties-leis.{md,html}, module-15-listed-derivatives.{md,html}
    │   ├── module-19-interest-rate-swap-deep-dive.md         # Reference: IRS deep dive (no .html yet)
    │   ├── module-22-hedging-deep-dive.{md,html}             # Reference: Hedging deep dive
    │   ├── module-23-otc-forward-swap-option-deep-dive.{md,html}  # Reference: Forward/Swap/Option deep dive
    │   ├── module-24-otc-derivative-usd-cad-forward-complete-lifecycle.{md,html}  # Module 24: full lifecycle walkthrough
    │   ├── module-25-basis-points-deep-dive.{md,html}        # Reference: Basis Points deep dive
    │   ├── abbreviations.{md,html}           # Acronym glossary
    │   ├── systems-landscape.{md,html}       # Front/Middle/Back Office systems by region
    │   └── Capital-Markets.docx               # Supplementary Word document
    └── 2-reporting/                                  # Reporting-focused modules & deep dives
        ├── module-14-regulatory-reporting.{md,html}  # Regulatory Reporting + IT View
        ├── module-16-sftr-reporting.{md,html}          # SFTR deep dive
        ├── module-17-dmo-uk-reporting.{md,html}        # UK DMO gilt reporting deep dive
        ├── module-18-upi-uti-dsb-deep-dive.{md,html}   # UTI/UPI/ANNA-DSB deep dive
        ├── module-20-mifid-reporting.{md,html}         # MiFID II/MiFIR reporting deep dive
        ├── module-21-isda-product-taxonomy-deep-dive.{md,html}  # ISDA Product Taxonomy deep dive
        └── SFTR architecture.docx, SFTR-Haircut.docx   # Supplementary Word documents
```

Modules 16–25 are deep-dive/reference modules — each expands one topic the first 15 modules already touched into a full write-up. See `index.md`'s Modules table for the current numbered list.

`examples/` holds standalone **Complete Worked Example** files, one product per file — each walks a single trade end-to-end (Business Scenario → Trade Economics → worked numeric scenarios → Valuation/MTM → Trade Lifecycle → Trade Capture → Confirmation → Regulatory Reporting → a comparison table). They sit alongside the modules rather than replacing them: modules link out to their matching example(s), and examples link back. See `index.md`'s "Complete Worked Examples" section for the current grouped list (Cash Instruments; Securities Financing/SFT; a connected OTC Derivatives Rates chain — IRS → FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS; OTC Derivatives for other asset classes; Listed Derivatives).

Each module (and each example) has a matching `.md` and `.html` version (same content). The `.md` files are the source of truth; edit those first.

## Conventions

- Example bank used throughout: **Bank Of Toronto** (fictional — never the real bank name). Exception: `examples/`'s FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS chain (plus the FX Option example) uses **Canada Bank of Toronto (CBT)** and counterparty **ABC Mining Corporation** instead — a deliberate variant for that specific chain, not a mistake.
- Modules 4–9 are the **Asset Class Roadmap** (Equity, Fixed Income, FX, Commodities, Rates, Credit), each covered through the same eight-part pattern. Module 15 (Listed Derivatives) is cross-cutting and sits outside that roadmap.
- Tables that mix cash instruments with OTC derivatives (or repo/SFTs) get a leading `Cash / Derivative` (or `Cash / Derivative / SFT`) tag column — `CASH` / `DERIVATIVE` / `SFT` per row, styled in HTML with the `side-tag sell/buy/infra` classes already defined on every module page.
- A handful of modules (8 Rates, 9 Credit, 15 Listed Derivatives) carry an inline `> Work data model:` callout mapping the module to the internal Trade/Valuation Avro schema taxonomy this curriculum is loosely modeled on — see those modules directly rather than a dedicated reference section.

This is not a software project — there is no build system, linter, or test suite.
