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

# 12-Module Self-Paced Learning Path

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
Trade: BMO buys CAD 10M from Goldman Sachs
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

# Module 4 — Fixed Income / Bonds

### Learn

- Government Bonds
- Corporate Bonds
- Treasury Bills
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

---

# Module 5 — Equity

### Learn

- Shares
- Quantity
- Price
- Market Value
- Dividends
- Corporate Actions
- Settlement

### Goal

Use Equity as a simpler asset class to compare with Fixed Income and Derivatives.

---

# Module 6 — Derivatives Fundamentals

### Learn

- Forward
- Future
- Option
- Swap
- OTC vs Exchange-Traded Derivatives

### Important Concept

Understand why a derivative can have a **Notional** amount without that notional necessarily being exchanged.

---

# Module 7 — FX & Interest Rate Products

## FX Forward Example

```text
Buy USD 10M
Contract Rate = 1.35 CAD/USD
```

Later:

```text
Market Rate = 1.38 CAD/USD
```

Learn how the difference creates a positive or negative valuation.

## Interest Rate Swap Example

```text
Notional = CAD 100M
Party A pays fixed 4%
Party B pays floating
Maturity = 5 years
```

### Learn

- Fixed Leg
- Floating Leg
- Reference Rate
- Payment Dates
- Notional
- Maturity
- MTM

---

# Module 8 — Valuation

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

---

# Module 9 — Risk & Exposure

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

---

# Module 10 — Collateral & Margin

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

---

# Module 11 — Parties, LEIs & Market Infrastructure

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

---

# Module 12 — Regulatory Reporting + IT View

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
- Local regulatory regimes

### Goal

Understand:

> **What happened in the trade → what data describes it → why does the regulator need that data?**

You do not need to become a regulatory lawyer. The focus is on the business and data perspective.

---

# Final End-to-End Case Study

By the end, I should be able to understand a record like this:

```text
Trade ID: IRS12345

Asset Class: Derivative
Product: Interest Rate Swap

Notional: CAD 100M

Party A: BMO
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

BMO and Goldman Sachs.

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
| Fixed Income + Derivatives | 20% |
| Valuation | 20% |
| Collateral + Exposure | 20% |
| Parties / LEIs / Settlement | 10% |
| General Capital Markets | 10% |

---

# Recommended Starting Point

Start with:

**Module 1 → Module 2 → Module 3**

Pay particular attention to:

> **Asset Class → Product → Instrument → Trade**

Once this hierarchy is clear, the later topics — valuation, collateral, exposure and regulatory reporting — will be much easier to connect.
