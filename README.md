# Capital Markets — Self-Paced Learning Path

A self-paced study plan for learning Capital Markets from an IT/reporting perspective — one recurring mental model, worked through 15 modules plus reference material:

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
    ├── capital_markets_self_paced_learning_path.md   # Full original curriculum, all modules in one doc
    ├── module-01-fundamentals.{md,html}  … module-15-listed-derivatives.{md,html}
    ├── abbreviations.{md,html}           # Acronym glossary
    ├── systems-landscape.{md,html}       # Front/Middle/Back Office systems by region
    ├── sftr-reporting.{md,html}          # SFTR deep dive
    ├── dmo-uk-reporting.{md,html}        # UK DMO gilt reporting deep dive
    ├── upi-uti-dsb-deep-dive.{md,html}   # UTI/UPI/ANNA-DSB deep dive
    └── docx/                             # Supplementary Word documents
```

Each module has a matching `.md` and `.html` version (same content). The `.md` files are the source of truth; edit those first.

## Conventions

- Example bank used throughout: **Bank Of Toronto** (fictional — never the real bank name).
- Modules 4–9 are the **Asset Class Roadmap** (Equity, Fixed Income, FX, Commodities, Rates, Credit), each covered through the same eight-part pattern. Module 15 (Listed Derivatives) is cross-cutting and sits outside that roadmap.
- Trade examples map to the real Trade/Valuation Avro schema taxonomy used at work — see the **Work data model reference** section in [`CLAUDE.md`](./CLAUDE.md).

This is not a software project — there is no build system, linter, or test suite.
