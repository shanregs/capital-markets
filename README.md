# Capital Markets — Self-Paced Learning Path

A self-paced study plan for learning Capital Markets from an IT/reporting perspective — one recurring mental model, worked through 15 core modules plus 9 numbered deep-dive/reference modules (16–24) and standalone reference material:

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
└── docs/
    ├── 1-capital-markets/                            # Non-reporting modules & reference material
    │   ├── capital_markets_self_paced_learning_path.md   # Full original curriculum, all modules in one doc
    │   ├── module-01-fundamentals.{md,html}  … module-13-parties-leis.{md,html}, module-15-listed-derivatives.{md,html}
    │   ├── module-19-interest-rate-swap-deep-dive.md         # Reference: IRS deep dive (no .html yet)
    │   ├── module-22-hedging-deep-dive.{md,html}             # Reference: Hedging deep dive
    │   ├── module-23-otc-forward-swap-option-deep-dive.{md,html}  # Reference: Forward/Swap/Option deep dive
    │   ├── module-24-otc-derivative-usd-cad-forward-complete-lifecycle.{md,html}  # Module 24: full lifecycle walkthrough
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

Modules 16–24 are deep-dive/reference modules — each expands one topic the first 15 modules already touched into a full write-up. See `index.md`'s Modules table for the current numbered list.

Each module has a matching `.md` and `.html` version (same content). The `.md` files are the source of truth; edit those first.

## Conventions

- Example bank used throughout: **Bank Of Toronto** (fictional — never the real bank name).
- Modules 4–9 are the **Asset Class Roadmap** (Equity, Fixed Income, FX, Commodities, Rates, Credit), each covered through the same eight-part pattern. Module 15 (Listed Derivatives) is cross-cutting and sits outside that roadmap.
- Trade examples map to the real Trade/Valuation Avro schema taxonomy used at work — see the **Work data model reference** section in [`CLAUDE.md`](./CLAUDE.md).

This is not a software project — there is no build system, linter, or test suite.
