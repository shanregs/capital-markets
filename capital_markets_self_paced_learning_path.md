# Capital Markets — Self-Paced Learning Path

## What I want to learn

My goal is to understand a Capital Markets trade from:

**Business concept → Trade lifecycle → Valuation → Exposure/Risk → Collateral/Margin → Clearing & Settlement → Regulatory Reporting**

I want to learn this from an **IT / reporting perspective**, so that I can understand the business meaning behind the data and fields I see in Capital Markets reporting.

---

# Core Mental Model

```text
Capital Markets
      ↓
Asset Class
      ↓
Product
      ↓
Instrument / Security
      ↓
Trade
      ↓
Valuation
      ↓
Exposure / Risk
      ↓
Collateral / Margin
      ↓
Clearing & Settlement
      ↓
Regulatory Reporting
```

---

# Asset Class Roadmap (Modules 4–9)

Modules 4–9 are organized **by asset class**, not by topic — each one is studied through the same eight-part pattern before moving to the next, one asset class at a time.

```text
CAPITAL MARKETS
│
├── 4. EQUITY
│   ├── Cash Equity
│   │   ├── Common Stock
│   │   └── Preferred Stock
│   │
│   └── Equity Derivatives
│       ├── Equity Futures
│       └── Equity Options
│
├── 5. FIXED INCOME
│   ├── Government Bonds
│   ├── Corporate Bonds
│   ├── Municipal Bonds
│   └── Other Debt Instruments
│
├── 6. FX
│   ├── FX Spot
│   ├── FX Forward
│   ├── FX Swap
│   └── FX Options
│
├── 7. COMMODITIES
│   ├── Physical Commodities
│   ├── Commodity Futures
│   ├── Commodity Options
│   └── Commodity Swaps
│
├── 8. RATES
│   ├── Interest Rate Products
│   ├── IRS
│   ├── OIS
│   ├── FRA
│   ├── Swaptions
│   └── Rate Futures
│
└── 9. CREDIT
    ├── Corporate Credit
    ├── CDS
    ├── Credit Derivatives
    └── Structured Credit
```

> **Work data model note:** the Avro schema taxonomy actually used at work organizes Futures and Listed Options (e.g. Module 8's "Rate Futures", Module 4's Equity Futures, Module 7's Commodity Futures) as a **separate, cross-cutting "Listed Derivatives" category** spanning all six asset classes above, rather than folding them into each asset class individually. See **[Module 15 — Listed Derivatives](./module-15-listed-derivatives.md)** ([HTML](./module-15-listed-derivatives.html)), placed after Module 9 since it builds on all six asset classes rather than being a seventh one. Every expanded module write-up (4 onward) also calls out the specific `.avsc` Trade/Valuation schema its worked examples map to.

### The eight-part pattern, repeated for every asset class

1. **What is it?** — theory and terminology
2. **What are the products?** — e.g. in Equity: stock, preferred stock, future, option
3. **What exactly is being traded?** — the actual security vs. a contract
4. **Real-world trade example** — Buyer → Seller → Trade
5. **Trade lifecycle** — Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting
6. **Identifiers** — introduced gradually: ISIN, CUSIP, SEDOL, Ticker, LEI, UPI, FIGI
7. **Exchange vs OTC vs Bilateral vs Centrally Cleared**
8. **Regulatory perspective** — only after the product itself is clear: MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

Doing it this way means acronyms in Step 8 never get memorized in a vacuum — by the time a regulation shows up, the product and trade it applies to are already familiar.

---

# 14-Module Self-Paced Learning Path

## Module 1 — Capital Markets Fundamentals

### Learn

- What is Capital Markets?
- Buy-side vs Sell-side
- Banks, brokers, dealers, funds
- Front Office / Middle Office / Back Office
- Primary vs Secondary Market
- Exchange-traded vs OTC

### Goal

Be able to explain where a trade sits within the Capital Markets ecosystem.

---

## Module 2 — Asset Class → Product → Instrument → Trade

This is a high-priority area.

### Example

```text
Asset Class: Fixed Income
        ↓
Product: Government Bond
        ↓
Instrument: Canada 5% Bond 2035
        ↓
ISIN: Identifies the specific security
        ↓
Trade: Bank Of Toronto buys CAD 10M from Goldman Sachs
```

### Learn

- Asset Class
- Product
- Instrument
- Security
- ISIN
- CUSIP
- Ticker
- Trade ID
- Notional
- Quantity
- Price
- Currency

### Goal

When looking at a reporting record, understand what each field represents.

---

## Module 3 — Trade Lifecycle

### Learn the complete journey

```text
Order
  ↓
Execution
  ↓
Trade Capture
  ↓
Confirmation
  ↓
Matching
  ↓
Clearing
  ↓
Settlement
  ↓
Reconciliation
  ↓
Reporting
```

Understand what happens in:

- Front Office
- Middle Office
- Back Office

### Goal

Given a trade record, explain where it came from and what happens next.

---

# Module 4 — Equity

### Learn

- Cash Equity: Common Stock, Preferred Stock
- Equity Derivatives: Equity Futures, Equity Options
- What's actually being traded: a real ownership security vs. a contract whose value derives from one

### Example

```text
Buyer:  Maple Leaf Pension Fund
Seller: Goldman Sachs
Trade:  10,000 shares, Coca-Cola Company (NYSE: KO) @ USD 68.40
        via Bank Of Toronto (dealer)
```

### Goal

Work through the full eight-part pattern (see the Asset Class Roadmap above) for Cash Equity and Equity Derivatives before moving to Fixed Income.

Full expanded write-up: [`module-04-equity.md`](./module-04-equity.md) ([HTML](./module-04-equity.html))

---

# Module 5 — Fixed Income

### Learn

- Government Bonds, Corporate Bonds, Municipal Bonds, Other Debt Instruments
- Coupon
- Face Value
- Maturity
- Yield
- Price
- Clean Price
- Dirty Price
- Accrued Interest
- Market Value
- Duration

### Example

```text
Face Value = CAD 10M
Coupon = 5%
Price = 98.50
Maturity = 2035
```

Understand how these values contribute to the bond's valuation.

Full expanded write-up: [`module-05-fixed-income.md`](./module-05-fixed-income.md) ([HTML](./module-05-fixed-income.html))

### Deep Dive — Coupon Rate vs. Yield: what does "5%" actually mean?

Two different numbers hide behind the same "5%":

- **Coupon Rate** — a fixed percentage of **Face Value**, set once at issuance, printed on the bond, and never changes for the life of the bond. It only tells you the cash the issuer pays each year — it says nothing about whether the bond was a good buy.
- **Yield** — the actual return the buyer earns, based on the **price actually paid**, not the face value. Yield moves every time the market price moves, even though the coupon rate never does.

```text
Annual Coupon Cash Flow = Face Value × Coupon Rate      (fixed, from the issuer)
Current Yield            = Annual Coupon Cash Flow / Price Paid   (varies, from the market)
```

If the buyer pays **below** par (a discount, Price < 100), Yield > Coupon Rate — the buyer is getting more income per dollar invested than the coupon rate suggests.
If the buyer pays **above** par (a premium, Price > 100), Yield < Coupon Rate — the buyer is getting less income per dollar invested than the coupon rate suggests.

#### Worked example: Bank Of Toronto buys a different 5%-coupon bond each year

Same coupon rate (5%) every year, but different Face Value and different market Price each time — this is the situation an IT/reporting system actually sees: `Coupon Rate` sits static on the security master, while `Price` and `Yield` are refreshed from the market feed every day.

| Year | Face Value (CAD) | Coupon Rate | Market Price (% of par) | Price Paid (Purchase Cost) | Annual Coupon Cash Flow | Current Yield | Yield vs. Coupon | Bank Of Toronto's Position |
|---|---|---|---|---|---|---|---|---|
| 2026 | 10,000,000 | 5% | 98.50 (discount) | 9,850,000 | 500,000 | 500,000 / 9,850,000 = **5.08%** | Yield > Coupon | **Better off** — paid less than face value, so the fixed CAD 500,000 coupon represents a higher return on the actual cash invested. More profit. |
| 2027 | 15,000,000 | 5% | 101.25 (premium) | 15,187,500 | 750,000 | 750,000 / 15,187,500 = **4.94%** | Yield < Coupon | **Slightly worse off** — paid more than face value, so the same 5% coupon represents a lower return on the actual cash invested. Less profit, though still positive income. |
| 2028 | 8,000,000 | 5% | 95.00 (deep discount) | 7,600,000 | 400,000 | 400,000 / 7,600,000 = **5.26%** | Yield > Coupon | **Best outcome of the three** — biggest discount to par means the biggest gap between coupon rate and actual yield, in the buyer's favor. |
| 2029 | 20,000,000 | 5% | 103.75 (premium) | 20,750,000 | 1,000,000 | 1,000,000 / 20,750,000 = **4.82%** | Yield < Coupon | **Worst outcome of the four** — largest premium paid, so the real return is furthest below the printed coupon rate. Still profitable, just least efficient use of capital. |

Key takeaways for the IT/reporting angle:

- The **Coupon Rate** field never changes after issuance — it is static reference data on the bond/security master.
- **Price** is a market-driven field that changes daily — it's what actually decides whether the buyer is winning or losing relative to the coupon.
- **Yield** is a *derived* field (Coupon Cash Flow ÷ Price Paid) — never store it as if it were static; it must be recalculated whenever price updates.
- "Is Bank Of Toronto profiting more or less?" is answered by comparing **Yield to Coupon Rate**, not by looking at the coupon rate alone — a 5% coupon can mean a great deal (2028) or a mediocre one (2029) depending purely on the price paid.
- This worked example uses **Current Yield** (simple, price-based) for clarity. In practice, valuation/reporting systems more often use **Yield to Maturity (YTM)**, which also accounts for the gain/loss recovered at redemption (buying at a discount pulls the price back up to 100 at maturity; buying at a premium pulls it back down to 100) — YTM is covered in Module 10 (Valuation).

---

# Module 6 — FX

### Learn

- FX Spot, FX Forward, FX Swap, FX Options

### FX Forward Example

```text
Buy USD 10M
Contract Rate = 1.35 CAD/USD
```

Later:

```text
Market Rate = 1.38 CAD/USD
```

Learn how the difference creates a positive or negative valuation.

Full expanded write-up: [`module-06-fx.md`](./module-06-fx.md) ([HTML](./module-06-fx.html))

---

# Module 7 — Commodities

### Learn

- Physical Commodities, Commodity Futures, Commodity Options, Commodity Swaps

### Goal

Understand why most of a bank's commodity exposure is accessed via derivatives (futures/swaps) rather than physical delivery, and how that changes what settles.

Full expanded write-up: [`module-07-commodities.md`](./module-07-commodities.md) ([HTML](./module-07-commodities.html))

---

# Module 8 — Rates

### Learn

- Interest Rate Products, IRS, OIS, FRA, Swaptions, Rate Futures
- Fixed Leg
- Floating Leg
- Reference Rate
- Payment Dates
- Notional
- Maturity
- MTM

### Important Concept

Understand why a derivative can have a **Notional** amount without that notional necessarily being exchanged.

### Interest Rate Swap Example

```text
Notional = CAD 100M
Party A pays fixed 4%
Party B pays floating
Maturity = 5 years
```

Full expanded write-up: [`module-08-rates.md`](./module-08-rates.md) ([HTML](./module-08-rates.html)) — covers all five Interest Rate products (Cap/Floor, IR Forward Debt, FRA, IRS, Swaption); Rate Futures are covered separately in Module 15 (Listed Derivatives).

---

# Module 9 — Credit

### Learn

- Corporate Credit, CDS, Credit Derivatives, Structured Credit

### Goal

Build on the CDS mechanics already introduced in Module 2 §09 (protection buyer/seller, spread, recovery rate) and extend to credit derivatives more broadly and structured credit (CLOs, tranching).

Full expanded write-up: [`module-09-credit.md`](./module-09-credit.md) ([HTML](./module-09-credit.html))

---

# Module 15 — Listed Derivatives

### Learn

- Futures and Listed Options, standardized and centrally cleared, on ANY underlying from Modules 4-9 (equity index, commodity, interest rate, FX, or a single security)
- Contract Specification, Tick Size/Value, Contract Month Codes, Open Interest
- Cheapest-to-Deliver (CTD) and delivery baskets for bond futures

### Goal

Recognize that "Futures" and "Listed Options" aren't asset-class-specific products — they're a cross-cutting venue/clearing category, matching the work data model's "7. Listed Derivatives" taxonomy node, which sits apart from the six asset-class nodes above.

Full expanded write-up: [`module-15-listed-derivatives.md`](./module-15-listed-derivatives.md) ([HTML](./module-15-listed-derivatives.html))

---

# Module 10 — Valuation

This is one of the highest-priority modules.

### Learn the difference between

- Price
- Market Value
- Fair Value
- MTM
- PV
- Exposure

### Compare valuation across trade types

| Trade | Basic valuation idea |
|---|---|
| Equity | Price × Quantity |
| Bond | Price + Accrued Interest |
| FX Forward | Difference between contract and market forward |
| IRS | PV of future cash-flow differences |
| Option | Pricing model |
| Repo | Cash/interest vs collateral value |

### Goal

When seeing a valuation field, understand **why that number exists**.

Full expanded write-up: [`module-10-valuation.md`](./module-10-valuation.md) ([HTML](./module-10-valuation.html)) — delivers the full YTM/Duration/DV01 math previewed in Module 5 §1 and Module 8 §1, plus DCF/option-pricing/credit-spread valuation for derivatives, the Fair Value Level 1/2/3 hierarchy, and a full cross-asset-class comparison table.

---

# Module 11 — Risk & Exposure

### Learn

- Market Value
- Current Exposure
- Potential Future Exposure
- Net Exposure
- Counterparty Risk
- Credit Exposure

### Critical Concept

**MTM ≠ Exposure**

### Example

```text
MTM                = +$2.4M
Exposure           =  $2.0M
```

Understand why these numbers can be different.

Full expanded write-up: [`module-11-risk-exposure.md`](./module-11-risk-exposure.md) ([HTML](./module-11-risk-exposure.html)) — delivers Current Exposure/PFE, ISDA netting math, CVA and wrong-way risk, a bilateral-vs-CCP exposure reference, and a worked Value at Risk calculation.

---

# Module 12 — Collateral & Margin

This is a major focus because it relates directly to Capital Markets reporting.

### Learn

- Collateral
- Margin
- Initial Margin
- Variation Margin
- Cash Collateral
- Securities Collateral
- Eligible Collateral
- Haircut
- Collateral Value
- Collateral Movement
- Pledge / Receive
- Collateral Substitution
- Custodian
- Tri-party Agent

### Connect the concepts

```text
Trade
  ↓
Valuation
  ↓
Exposure
  ↓
Margin
  ↓
Collateral
```

### Example

```text
IRS MTM              = +$2.4M
Exposure             =  $2.0M
Collateral Held      =  $1.8M
Remaining Exposure   =  $0.2M
```

Understand why each number exists and how they relate.

Full expanded write-up: [`module-12-collateral-margin.md`](./module-12-collateral-margin.md) ([HTML](./module-12-collateral-margin.html)) — runs the exact MTM $2.4M → Exposure $2.0M → Collateral $1.8M → Remaining Exposure $0.2M chain end to end, with IM/VM mechanics, haircuts, tri-party custody, and the UMR phase-in.

---

# Module 13 — Parties, LEIs & Market Infrastructure

### Learn the roles of

- Buyer
- Seller
- Counterparty
- Broker
- Dealer
- CCP
- Clearing Member
- Custodian
- Settlement Agent
- CSD
- Tri-party Agent

## LEI

**LEI = Legal Entity Identifier**

Understand which legal entity each LEI field identifies.

Examples:

- Buyer LEI
- Seller LEI
- Counterparty LEI
- Reporting Entity LEI
- Custodian LEI
- Other entity identifiers

### Goal

Do not just memorize LEI definitions. Understand:

> **Which legal entity does this field identify, and why is that entity relevant to the trade?**

Full expanded write-up: [`module-13-parties-leis.md`](./module-13-parties-leis.md) ([HTML](./module-13-parties-leis.html)) — covers Clearing Members and client clearing, a full seven-LEI-field worked trade, dual-sided vs single-sided reporting, Golden Source reference data, and the "No LEI, No Trade" rule.

---

# Module 14 — Regulatory Reporting + IT View

### Learn the purpose of

- Trade Reporting
- Transaction Reporting
- Position Reporting
- Derivatives Reporting
- Securities Financing Reporting

### Frameworks to understand

- EMIR
- MiFID II
- SFTR
- Local regulatory regimes — see [Systems Landscape](./systems-landscape.md) for how MiFID/EMIR/SFTR map to specific regulators and systems in Canada, the US, the UK, and the EU

### Goal

Understand:

> **What happened in the trade → what data describes it → why does the regulator need that data?**

You do not need to become a regulatory lawyer. The focus is on the business and data perspective.

Full expanded write-up: [`module-14-regulatory-reporting.md`](./module-14-regulatory-reporting.md) ([HTML](./module-14-regulatory-reporting.html)) — disambiguates Trade/Transaction/Position/Derivatives/Securities Financing Reporting, covers EMIR pairing/reconciliation and MiFID II Transaction Reporting in depth, and closes with a full field-by-field annotation of the `IRS12345` record below, tracing every value back to the module and section that built it.

---

# Final End-to-End Case Study

By the end, I should be able to understand a record like this:

```text
Trade ID: IRS12345

Asset Class: Derivative
Product: Interest Rate Swap

Notional: CAD 100M

Party A: Bank Of Toronto
Party B: Goldman Sachs

Maturity: 5Y

MTM: +CAD 2.4M
Exposure: CAD 2.0M
Collateral: CAD 1.8M

Custodian: XYZ

Settlement: ...
Reporting: ...
```

I should be able to answer:

### What is this?

An Interest Rate Swap.

### What are the parties?

Bank Of Toronto and Goldman Sachs.

### What is CAD 100M?

The Notional — not necessarily the amount exchanged.

### What is CAD 2.4M?

The trade's valuation / MTM.

### Why is Exposure CAD 2M?

Exposure is a risk measure and does not necessarily equal the raw MTM.

### Why is there CAD 1.8M collateral?

To mitigate counterparty exposure.

### What does the LEI identify?

A specific legal entity.

### What happens next?

Depending on the product and setup, the trade may go through clearing, settlement, collateral processing, reconciliation and regulatory reporting.

---

# Self-Paced Study Method

## Recommended pace

**30–45 minutes per session**

**3 sessions per week**

The exact pace can be adjusted depending on workload.

## Repeat this pattern for every module

1. **Concept** — 5–10 minutes
2. **Real trade example** — 10 minutes
3. **Numbers / valuation** — 10 minutes
4. **Reporting fields** — 10 minutes
5. **Quick quiz / recap** — 5 minutes

The objective is not just to memorize definitions.

The objective is to learn how to:

> **Read a Capital Markets trade from a reporting/data perspective.**

---

# Priority for My Current Work

| Topic | Priority |
|---|---:|
| Trade Lifecycle | 20% |
| Fixed Income + Rates + Credit (Derivatives) | 20% |
| Valuation | 20% |
| Collateral + Exposure | 20% |
| Parties / LEIs / Settlement | 10% |
| General Capital Markets (incl. Equity, FX, Commodities) | 10% |

---

# Recommended Starting Point

Start with:

**Module 1 → Module 2 → Module 3**

Pay particular attention to:

> **Asset Class → Product → Instrument → Trade**

Once this hierarchy is clear, move into the Asset Class Roadmap **one asset class at a time**, starting with **Module 4: Equity** — it's the simplest product set to see the full eight-part pattern (Section header above) work end to end before applying it to Fixed Income, FX, Commodities, Rates, and Credit. The later topics — valuation, collateral, exposure and regulatory reporting — will be much easier to connect once a couple of asset classes are familiar.
