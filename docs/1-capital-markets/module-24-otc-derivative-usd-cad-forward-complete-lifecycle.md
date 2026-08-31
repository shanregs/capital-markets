# Module 24 — Complete OTC Derivative Lifecycle — USD/CAD FX Forward

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Reference module — full end-to-end architecture walkthrough.** Where [Module 6](./module-06-fx.md) §4b, [Module 21](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md), [Module 22](./module-22-hedging-deep-dive.md), and [Module 23](./module-23-otc-forward-swap-option-deep-dive.md) each cover one slice of an FX Forward (the trade mechanics, its ISDA classification, its use as a hedge, its structural family), this module takes **one single trade** — Bank Of Toronto's USD/CAD FX Forward with ABC Canada Inc. — through **every stage of its real operational lifecycle**, system by system, field by field: Trade Capture → Confirmation → Enrichment → Valuation → MTM → Collateral → Settlement → Regulatory Reporting, closing with a suggested microservices/event-driven architecture for building this as a platform.

---

## 1. Purpose

This document walks through one complete OTC derivative trade — a **USD/CAD FX Forward** — from:

**Trade Capture → Confirmation → Enrichment → Valuation → MTM → Collateral → Settlement → Regulatory Reporting**

The objective is to understand both:

1. The **business lifecycle** of an OTC derivative.
2. The **data/fields generated or consumed** by systems at each stage.

---

## 2. Business Scenario

### 2.1 Parties

| Role | Party |
|---|---|
| Bank | Bank Of Toronto |
| Client / Counterparty | ABC Canada Inc. |
| Trade | USD/CAD FX Forward |
| Product | FX Forward |
| Purpose | Hedge future USD payment |

ABC Canada expects to pay a US supplier **USD 10,000,000** on 30-Nov-2026.

ABC is concerned that USD/CAD may rise, making the future USD payment more expensive in CAD terms.

ABC therefore enters an OTC FX Forward with Bank Of Toronto.

---

## 3. Trade Economics

Assume the trade is executed on **30-Aug-2026**.

| Attribute | Value |
|---|---|
| Trade Date | 30-Aug-2026 |
| Value Date / Maturity | 30-Nov-2026 |
| Product | FX Forward |
| Currency Pair | USD/CAD |
| Buy Currency | USD |
| Sell Currency | CAD |
| USD Notional | USD 10,000,000 |
| Forward Rate | CAD 1.3545 per USD |
| CAD Notional | CAD 13,545,000 |
| Client | ABC Canada Inc. |
| Bank | Bank Of Toronto |
| Settlement | Physical (Deliverable Forward) |
| Business Purpose | Hedge |
| Direction from ABC perspective | Buy USD / Sell CAD |
| Governing Agreement | ISDA Master Agreement + CSA between Bank Of Toronto and ABC Canada Inc. ([Module 12](./module-12-collateral-margin.md)) |
| ISDA Taxonomy Path | Foreign Exchange / Forward / Deliverable / Forward ([Module 21](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) §3's identical worked example) |
| Hedge Designation | Yes — hedges a known future USD payable ([Module 22](./module-22-hedging-deep-dive.md) §9's formal hedge-designation flag, not just an economic observation) |

ABC has locked in:

```text
USD 10,000,000 × CAD 1.3545
= CAD 13,545,000
```

> **Callout — USD/CAD is a G10, fully convertible pair, so Deliverable is the default.** Not every FX Forward needs a Deliverable-vs-NDF decision the way an emerging-market currency does. CAD, like every other G10 currency, has no capital-account restrictions, so a USD/CAD forward — like this trade — almost always settles physically: both currencies actually exchange at maturity (Section 19), with no dealer authorization or residency test required the way it would be for a genuinely restricted currency. Contrast this with the **INR** example this module was originally built around: because INR is *not* fully convertible, USD/INR forwards split into an onshore **Deliverable Forward** (booked through an RBI-authorized dealer bank with an Indian resident counterparty) versus an offshore **Non-Deliverable Forward (NDF)**, which settles only a cash difference in USD because the offshore counterparty has no way to actually deliver INR at all. The `NDF Flag` field kept on this trade's data model (Section 8) is still worth carrying on every FX Forward record for exactly that reason — it will read `N` on almost every G10 trade like this one, but the same schema needs to be ready for the day it has to carry an EM currency pair where the answer genuinely varies.

---

## 4. High-Level Lifecycle

```text
                    OTC FX FORWARD
                          |
                          v
                  1. Trade Capture
                          |
                          v
                  2. Confirmation
                          |
                          v
                  3. Enrichment
                          |
                          v
                  4. Valuation
                          |
                          v
                     5. MTM
                          |
                          v
                  6. Collateral
                          |
                          v
                  7. Settlement
                          |
                          v
               8. Regulatory Reporting
```

The same trade identifier should generally allow downstream systems to trace the trade throughout its lifecycle.

---

## 5. Stage 1 — Trade Capture

### 5.1 What happens?

The trader and ABC agree the trade.

The front-office trading system captures the economic terms.

A unique trade identifier is generated.

Example:

```text
Trade ID = BOT-FX-20260830-00012345
```

The trade is then passed to downstream systems.

---

### 5.2 Trade Capture Data

| Field | Example |
|---|---|
| Trade ID | BOT-FX-20260830-00012345 |
| Product Type | FX_FORWARD |
| Trade Date | 2026-08-30 |
| Effective Date | 2026-08-30 |
| Maturity Date | 2026-11-30 |
| Currency Pair | USD/CAD |
| Buy Currency | USD |
| Sell Currency | CAD |
| Buy Amount | 10,000,000 USD |
| Sell Amount | 13,545,000 CAD |
| Forward Rate | 1.3545 |
| Client | ABC Canada Inc. |
| Counterparty ID | ABC12345 |
| Trader | TRADER001 |
| Trading Desk | FX-CANADA |
| Legal Entity | Bank Of Toronto |
| Book | FX_HEDGE_BOOK |
| Settlement Type | PHYSICAL |
| Execution Timestamp | 2026-08-30 10:32:15 ET |
| Trade Status | NEW |

---

## 6. Stage 2 — Confirmation

### 6.1 What happens?

The trade economics captured by Bank Of Toronto are compared with the counterparty's understanding.

A confirmation document/message is generated.

Both parties confirm:

> "We agree that this is the trade we entered into."

The confirmation contains legally important economic and settlement terms.

---

### 6.2 Example Confirmation

```text
Confirmation Number:
CONF-FX-20260830-00012345

Trade:
USD/CAD FX Forward

Trade Date:
30-Aug-2026

Value Date:
30-Nov-2026

Party A:
Bank Of Toronto

Party B:
ABC Canada Inc.

Party A buys:
USD 10,000,000

Party A sells:
CAD 13,545,000

Forward Rate:
CAD 1.3545 per USD

Settlement:
Physical
```

---

### 6.3 Important Confirmation Fields

| Category | Fields |
|---|---|
| Identification | Trade ID, Confirmation ID |
| Parties | Party A, Party B |
| Product | FX Forward |
| Dates | Trade Date, Value Date |
| Currency | Currency Pair |
| Amounts | Buy Amount, Sell Amount |
| Rate | Forward Rate |
| Settlement | Physical/Cash |
| Legal | Master Agreement |
| Documentation | Confirmation Status |
| Operations | Settlement Instructions |

---

## 7. Stage 3 — Enrichment

### 7.1 Why enrichment is required

The original trade contains business economics, but downstream systems need additional standardized information.

Enrichment may add:

- LEIs
- Legal entity identifiers
- UTI
- Product classification
- UPI
- Booking entity
- Jurisdiction
- Settlement instructions
- Counterparty classification
- Regulatory attributes
- Risk attributes

This is especially important for regulatory reporting.

---

## 8. Example Enriched Trade

```text
Trade ID:
BOT-FX-20260830-00012345

Product:
FX Forward

Currency Pair:
USD/CAD

Buy Currency:
USD

Sell Currency:
CAD

Buy Amount:
10,000,000

Sell Amount:
13,545,000

Forward Rate:
1.3545

Trade Date:
2026-08-30

Maturity Date:
2026-11-30

Bank LEI:
5493001KJTIIGC8Y1R12

Counterparty LEI:
549300ABCEXAMPLE45

UTI:
5493001KJTIIGC8Y1R1220260830FX00012345

UPI:
FX9M3RXQ2K18

ISDA Taxonomy Path:
Foreign Exchange / Forward / Deliverable / Forward

Underlier:
USD/CAD

NDF Flag:
N (Deliverable Forward — Section 3's callout)

Hedge Designation:
Y — hedges ABC's forecasted USD payable

Counterparty Country:
CA

Counterparty Classification:
Non-Financial Counterparty (NFC), per EMIR-style classification — see
Module 22 §9's hedging-exemption discussion for why this classification
matters for clearing obligations

Booking Entity:
Bank Of Toronto

Jurisdiction:
Canada / Other applicable regimes (e.g. if either party is US-connected)

Settlement Type:
Physical
```

> Note: The LEI, UTI and UPI above are illustrative placeholders — same format convention as [Module 18](../2-reporting/module-18-upi-uti-dsb-deep-dive.md) (20-character LEI-prefixed UTI) and [Module 21](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) (12-character UPI). In a real implementation they must come from authoritative sources/processes, not be assigned by the booking system itself.

---

## 9. Stage 4 — Valuation

### 9.1 Why valuation is required

After the trade is booked, the bank needs to determine:

> "What is this trade worth today?"

The trade was agreed at a forward rate of:

**1.3545 CAD/USD**

But the market forward rate changes every day.

Suppose shortly afterward the current market 3-month USD/CAD forward rate is:

**1.3650**

The original trade at 1.3545 has become valuable to the party that buys USD at 1.3545.

---

## 10. FX Forward Valuation Concept

A simplified economic calculation is:

```text
MTM ≈ Notional × (Current Forward Rate - Contract Forward Rate)
```

For a USD buyer, ignoring discounting and other market conventions.

Suppose:

```text
Notional = USD 10,000,000

Contract Rate = CAD 1.3545

Current Forward Rate = CAD 1.3650
```

Then:

```text
Difference
= 1.3650 - 1.3545
= CAD 0.0105/USD
```

Approximate value:

```text
USD 10,000,000 × CAD 0.0105
= CAD 105,000
```

ABC's position is economically favorable.

---

## 11. More Accurate Valuation

A production valuation engine normally does not simply use:

```text
Notional × rate difference
```

It considers:

- Current spot FX rate
- Forward FX points / forward curve
- Remaining maturity
- Interest-rate curves
- Discount factors
- Currency
- Settlement convention
- Market data timestamp
- Credit valuation adjustments where applicable
- Funding/valuation adjustments depending on the bank's framework

A simplified conceptual formula is:

```text
PV =
Expected future cash-flow difference
× Discount Factor
```

The exact FX-forward pricing methodology depends on the bank's valuation framework.

---

## 12. Valuation Data Fields

| Field | Example |
|---|---|
| Trade ID | BOT-FX-20260830-00012345 |
| Valuation Date | 2026-09-01 |
| Spot USD/CAD | 1.3490 |
| Forward USD/CAD | 1.3650 |
| Contract Rate | 1.3545 |
| USD Notional | 10,000,000 |
| Remaining Days | 90 |
| Discount Factor | 0.985 |
| Base Currency | CAD |
| Present Value | 103,425 |
| Valuation Currency | CAD |
| Valuation Timestamp | 2026-09-01 18:00:00 ET |
| Pricing Source | Market Data Service |
| Valuation Model | FX Forward PV |

---

## 13. Stage 5 — MTM

### 13.1 What is MTM?

**MTM = Mark-to-Market**

It answers:

> "If we valued this trade using today's market prices, how much would we gain or lose?"

Suppose:

```text
Trade Rate = 1.3545
Market Forward Rate = 1.3650
Notional = USD 10M
```

Simplified MTM:

```text
(1.3650 - 1.3545) × 10M
= CAD 105,000
```

So:

```text
ABC perspective: +CAD 105,000
Bank Of Toronto perspective: -CAD 105,000
```

The signs reverse because the two counterparties have opposite positions.

---

## 14. MTM Over Time

Imagine:

| Date | Market Forward Rate | Approx. MTM to USD Buyer |
|---|---:|---:|
| 30-Aug | 1.3545 | 0 |
| 01-Sep | 1.3650 | +105,000 CAD |
| 30-Sep | 1.3400 | -145,000 CAD |
| 31-Oct | 1.3750 | +205,000 CAD |
| 30-Nov | 1.3900 | +355,000 CAD |

This shows why daily valuation is important.

The trade can move from positive to negative during its life.

---

## 15. Stage 6 — Collateral

### 15.1 Why collateral?

OTC derivatives create **counterparty credit risk**.

Suppose Bank Of Toronto owes ABC a large amount if Bank Of Toronto defaults.

To reduce this risk, the parties may exchange collateral under a:

**CSA — Credit Support Annex**

Collateral may be posted as:

- Cash
- Government securities
- Other eligible collateral

depending on the agreement.

---

## 16. Margin Concepts

Two important concepts:

### Variation Margin — VM

Reflects changes in the current value of positions.

Conceptually:

```text
MTM increases
      ↓
Collateral requirement may increase
```

### Initial Margin — IM

Provides protection against potential future exposure during the period needed to close/replace positions after a default.

The actual applicability depends on the counterparties, regulatory requirements and documentation.

---

## 17. Example Collateral Calculation

Suppose:

```text
Portfolio MTM = +CAD 105,000 to ABC
Threshold = CAD 50,000
Minimum Transfer Amount = CAD 10,000
```

Simplified collateral requirement:

```text
105,000 - 50,000
= CAD 55,000
```

ABC may therefore be entitled to receive/post collateral depending on the direction defined by the CSA and the party's perspective.

Collateral processing is generally portfolio/CSA based rather than simply a standalone trade calculation.

---

## 18. Collateral Data Fields

| Field | Example |
|---|---|
| CSA ID | CSA-BOT-ABC-001 |
| Counterparty | ABC12345 |
| Valuation Date | 2026-09-01 |
| Portfolio Value | CAD 105,000 |
| Threshold | CAD 50,000 |
| MTA | CAD 10,000 |
| Collateral Requirement | CAD 55,000 |
| Collateral Type | Cash |
| Collateral Currency | CAD |
| Margin Type | VM |
| Call Date | 2026-09-01 |
| Settlement Date | 2026-09-02 |
| Collateral Status | CALLED |
| Dispute Status | NONE |

---

## 19. Stage 7 — Settlement

The trade reaches maturity on:

**30-Nov-2026**

The contract says:

```text
ABC buys USD 10M
ABC pays CAD 13,545,000
```

Therefore:

```text
ABC Canada Inc.                      Bank Of Toronto
       |                                   |
       |---------- CAD 13,545,000 -------->|
       |                                   |
       |<----------- USD 10M --------------|
```

The FX forward is physically settled.

---

## 20. Settlement Instructions

The settlement system needs instructions for both currencies.

Example:

### USD

```text
Currency: USD
Amount: 10,000,000
Settlement Account: USD account
SSI: ABC-USD-SSI-001
```

### CAD

```text
Currency: CAD
Amount: 13,545,000
Settlement Account: CAD account
SSI: ABC-CAD-SSI-001
```

SSI means:

**Standard Settlement Instructions**

---

## 21. Settlement Data Fields

| Field | Example |
|---|---|
| Trade ID | BOT-FX-20260830-00012345 |
| Settlement ID | SET-FX-20261130-00012345 |
| Value Date | 2026-11-30 |
| Buy Currency | USD |
| Buy Amount | 10,000,000 |
| Sell Currency | CAD |
| Sell Amount | 13,545,000 |
| USD SSI | ABC-USD-SSI-001 |
| CAD SSI | ABC-CAD-SSI-001 |
| Settlement Status | PENDING |
| Payment Status | RELEASED |
| USD Settlement Status | SETTLED |
| CAD Settlement Status | SETTLED |
| Final Status | COMPLETE |

---

## 22. Settlement Risk

FX transactions introduce settlement risk.

For example:

```text
Bank Of Toronto sends USD
      ↓
ABC does not deliver CAD
```

or:

```text
ABC sends CAD
      ↓
Bank Of Toronto does not deliver USD
```

This is why FX settlement infrastructure, payment controls and settlement-risk management are important.

---

## 23. Stage 8 — Regulatory Reporting

OTC derivatives are often subject to regulatory reporting requirements depending on:

- Jurisdiction
- Counterparty type
- Product
- Trading location
- Legal entity
- Reporting regime

Examples of regimes you may encounter in Capital Markets/RDP work include:

- EMIR
- UK EMIR
- CFTC reporting
- Canadian derivatives reporting
- ASIC reporting
- MAS reporting
- HKMA reporting

The exact fields and rules differ by jurisdiction and reporting regime.

---

## 24. Regulatory Trade Data

A reporting record may conceptually contain:

```text
UTI
UPI
LEI of Reporting Counterparty
LEI of Other Counterparty
Trade ID
Product Classification
Asset Class
Notional
Notional Currency
Price
Price Currency
Trade Date
Effective Date
Maturity Date
Settlement Type
Direction
Execution Timestamp
Clearing Status
Collateralisation
Valuation Amount
Valuation Currency
Action Type
Event Type
Reporting Timestamp
```

---

## 25. Example Regulatory Record

Illustrative only:

| Regulatory Field | Example |
|---|---|
| UTI | 5493001KJTIIGC8Y1R1220260830FX00012345 |
| UPI | FX9M3RXQ2K18 |
| ISDA Taxonomy Path | Foreign Exchange / Forward / Deliverable / Forward |
| Reporting Counterparty LEI | 5493001KJTIIGC8Y1R12 |
| Other Counterparty LEI | 549300ABCEXAMPLE45 |
| Product | FX Forward |
| Asset Class | Foreign Exchange |
| Currency Pair | USD/CAD |
| Notional | 10,000,000 |
| Notional Currency | USD |
| Price | 1.3545 |
| Price Currency | CAD |
| Trade Date | 2026-08-30 |
| Effective Date | 2026-08-30 |
| Maturity Date | 2026-11-30 |
| Settlement Type | Physical (Deliverable) |
| NDF Flag | N |
| Clearing Status | Not Cleared |
| Collateralisation | Bilateral |
| Hedging Exemption Claimed | Y — NFC hedging a commercial FX exposure |
| Valuation Amount | 103,425 |
| Valuation Currency | CAD |
| Action Type | NEW |
| Event Type | NEW_TRADE |
| Reporting Timestamp | 2026-08-30 10:35:00 ET |

Again, this is a **conceptual example**, not a jurisdiction-specific reporting template.

---

## 26. Trade Lifecycle Events

The trade does not necessarily have only one regulatory event.

During its life, events may include:

```text
NEW
 |
 +---- AMENDMENT
 |
 +---- VALUATION UPDATE
 |
 +---- COLLATERAL UPDATE
 |
 +---- PARTIAL TERMINATION
 |
 +---- NOVATION
 |
 +---- EARLY TERMINATION
 |
 +---- MATURITY
 |
 +---- CANCEL / ERROR CORRECTION
```

A regulatory reporting platform therefore needs to distinguish between:

**Trade-level data** and **event-level changes**.

---

## 27. End-to-End Data Flow

A typical architecture may look like:

```text
             FRONT OFFICE
                  |
                  | Trade
                  v
          +---------------+
          | Trade Capture |
          +---------------+
                  |
                  v
          +---------------+
          | Confirmation  |
          +---------------+
                  |
                  v
          +---------------+
          |  Enrichment   |
          +---------------+
                  |
          +-------+-------+
          |               |
          v               v
   +-------------+   +-------------+
   | Valuation   |   | Regulatory  |
   | / Risk      |   | Enrichment  |
   +-------------+   +-------------+
          |               |
          v               v
        MTM          Regulatory
          |            Report
          v               |
      Collateral          v
          |          Trade Repository
          v
      Settlement
          |
          v
      Accounting
```

---

## 28. Important System-of-Record Concepts

A large OTC derivatives platform usually has several systems.

| System | Primary Responsibility |
|---|---|
| Front Office | Execute/capture trade |
| Trade Store | Persist trade |
| Confirmation System | Confirm economics |
| Static Data | Counterparty/entity data |
| Reference Data | Product/instrument data |
| Market Data | Spot/forward curves |
| Valuation Engine | Calculate PV/MTM |
| Risk Engine | Exposure/risk |
| Collateral System | Margin/collateral |
| Settlement System | Payments |
| Regulatory Platform | Regulatory reporting |
| Trade Repository | Receive regulatory reports |
| Accounting | P&L/accounting |

---

## 29. Key Identifiers

Identifiers are extremely important when designing an RDP.

### Trade ID

Internal identifier:

```text
BOT-FX-20260830-00012345
```

### UTI

**Unique Transaction Identifier**

Used to identify a derivative transaction consistently for regulatory reporting.

### UPI

**Unique Product Identifier**

Identifies the type/product of derivative.

### LEI

**Legal Entity Identifier**

Identifies the legal entity.

A simplified relationship:

```text
LEI
 |
 +---- identifies Counterparty
 |
 +---- identifies Reporting Entity


UTI
 |
 +---- identifies Transaction


UPI
 |
 +---- identifies Product
```

---

## 30. Trade vs Product vs Counterparty

This distinction is critical.

### Product

"What is being traded?"

```text
FX Forward
```

### Counterparty

"Who is involved?"

```text
Bank Of Toronto
ABC Canada Inc.
```

### Trade

"What specific transaction occurred?"

```text
Trade ID:
BOT-FX-20260830-00012345
```

### Position

"What exposure remains after considering transactions?"

This distinction becomes increasingly important when designing data models.

---

## 31. Example Canonical Trade Model

A normalized internal trade representation could look conceptually like:

```json
{
  "tradeId": "BOT-FX-20260830-00012345",
  "productType": "FX_FORWARD",
  "assetClass": "FX",
  "tradeDate": "2026-08-30",
  "effectiveDate": "2026-08-30",
  "maturityDate": "2026-11-30",
  "buyCurrency": "USD",
  "buyAmount": 10000000,
  "sellCurrency": "CAD",
  "sellAmount": 13545000,
  "contractRate": 1.3545,
  "settlementType": "PHYSICAL",
  "ndfFlag": false,
  "isdaTaxonomy": {
    "assetClass": "FOREIGN_EXCHANGE",
    "baseProduct": "FORWARD",
    "subProduct": "DELIVERABLE",
    "transactionType": "FORWARD"
  },
  "hedge": {
    "isHedge": true,
    "hedgedExposure": "FORECASTED_USD_PAYABLE_2026_11_30"
  },
  "counterparty": {
    "lei": "549300ABCEXAMPLE45",
    "classification": "NFC"
  },
  "reportingEntity": {
    "lei": "5493001KJTIIGC8Y1R12"
  },
  "regulatory": {
    "uti": "5493001KJTIIGC8Y1R1220260830FX00012345",
    "upi": "FX9M3RXQ2K18"
  }
}
```

This is illustrative and should not be treated as a prescribed regulatory schema.

---

## 32. Event-Based Model

For an RDP architecture, an event-oriented model is often useful.

Example:

```text
TradeCreated
     |
     v
TradeConfirmed
     |
     v
TradeEnriched
     |
     v
TradeValued
     |
     v
MTMUpdated
     |
     v
CollateralUpdated
     |
     v
SettlementInitiated
     |
     v
SettlementCompleted
     |
     v
RegulatoryReported
```

Each event can contain:

```text
Event ID
Trade ID
UTI
Event Type
Event Timestamp
Source System
Previous State
New State
Version
Correlation ID
Payload
```

This is particularly useful in distributed microservices architectures.

---

## 33. Suggested Microservices

For a modern microservices implementation, the lifecycle could be decomposed into:

```text
trade-service
      |
      +--> confirmation-service
      |
      +--> enrichment-service
      |
      +--> valuation-service
      |
      +--> risk-service
      |
      +--> collateral-service
      |
      +--> settlement-service
      |
      +--> regulatory-service
```

Supporting services:

```text
reference-data-service
counterparty-service
market-data-service
identifier-service
audit-service
notification-service
```

An event bus such as Kafka can distribute trade lifecycle events.

---

## 34. Example Kafka Event Flow

```text
trade.created
      |
      +----> confirmation-service
      |
      +----> enrichment-service
      |
      +----> risk-service
      |
      +----> regulatory-service
```

Later:

```text
valuation.updated
      |
      +----> risk-service
      +----> collateral-service
      +----> regulatory-service
```

And:

```text
settlement.completed
      |
      +----> accounting-service
      +----> regulatory-service
      +----> audit-service
```

---

## 35. What Changes During the Lifecycle?

Consider the original trade:

```text
USD 10M
@ CAD 1.3545
Maturity 30-Nov-2026
```

The core economics generally remain the same.

But dynamic fields change:

```text
Market Spot
Forward Curve
PV
MTM
Exposure
Collateral
Settlement Status
Reporting Status
```

Therefore, an RDP should distinguish:

### Static / contractual data

```text
Trade ID
Product
Notional
Currency
Contract Rate
Trade Date
Maturity Date
Counterparties
```

from:

### Dynamic data

```text
Valuation
MTM
Market Data
Exposure
Collateral
Settlement Status
Reporting Status
```

---

## 36. Full Example Timeline

### 30-Aug-2026

```text
10:32
Trade executed

10:33
Trade captured

10:34
Trade confirmed

10:35
Counterparty / product enrichment completed

10:36
Regulatory identifiers generated

End of day
Trade valued
MTM calculated
```

### 01-Sep-2026

```text
Market moves
      ↓
Forward curve changes
      ↓
Trade revalued
      ↓
MTM changes
      ↓
Collateral requirement recalculated
      ↓
Collateral call generated if required
```

### 30-Nov-2026

```text
Maturity
    ↓
Settlement instructions validated
    ↓
USD payment
    +
CAD payment
    ↓
Settlement completed
    ↓
Trade closed
    ↓
Final regulatory lifecycle event
```

---

## 37. Most Important Fields by Lifecycle Stage

| Stage | Key Data |
|---|---|
| Trade Capture | Trade ID, product, notional, currencies, rate, dates |
| Confirmation | Confirmation ID, contractual terms, parties |
| Enrichment | LEI, UTI, UPI, classification, jurisdiction |
| Valuation | Spot, forward curve, discount factor, PV |
| MTM | Market value, valuation currency, P&L |
| Collateral | CSA, VM/IM, threshold, MTA, collateral |
| Settlement | SSI, payment amount, value date, settlement status |
| Regulatory | UTI, UPI, LEIs, action/event, valuation, reporting fields |

---

## 38. The Big Picture

The most important mental model is:

```text
                  TRADE
                    |
                    v
             What did we do?
                    |
                    v
             CONFIRMATION
                    |
                    v
             What did both
             parties agree?
                    |
                    v
              ENRICHMENT
                    |
                    v
             Who/what/how
             for downstream
             processing?
                    |
                    v
              VALUATION
                    |
                    v
             What is it
             worth today?
                    |
                    v
                 MTM
                    |
                    v
             Who is gaining
             or losing?
                    |
                    v
              COLLATERAL
                    |
                    v
             How much credit
             protection?
                    |
                    v
              SETTLEMENT
                    |
                    v
             What actually
             gets exchanged?
                    |
                    v
        REGULATORY REPORTING
                    |
                    v
             What must be
             reported to the
             regulator/repository?
```

---

## 39. Key Capital Markets Vocabulary to Remember

| Term | Meaning |
|---|---|
| OTC | Over The Counter |
| Derivative | Contract whose value derives from underlying |
| Forward | Obligation to transact in future at agreed price |
| Option | Right, not obligation |
| Call | Right to buy |
| Put | Right to sell |
| Strike | Option exercise price |
| Premium | Price paid for option |
| Spot | Current market price |
| Forward Rate | Agreed/market future FX rate |
| Notional | Reference amount used for calculation |
| Maturity | Contract end date |
| MTM | Current market value |
| PV | Present value |
| VM | Variation Margin |
| IM | Initial Margin |
| CSA | Credit Support Annex |
| SSI | Standard Settlement Instructions |
| LEI | Legal Entity Identifier |
| UTI | Unique Transaction Identifier |
| UPI | Unique Product Identifier |
| Trade Repository | Infrastructure receiving regulatory reports |

---

## 40. Recommended Next Step

After understanding this FX Forward example, study the same lifecycle for an:

**Interest Rate Swap (IRS)** — see [Module 19](./module-19-interest-rate-swap-deep-dive.md), which already takes one 5Y CAD IRS through all 20 quarterly settlements at this same level of field-by-field detail.

An IRS's lifecycle introduces additional concepts on top of everything this module covered:

```text
IRS
 |
 +--> Fixed Rate
 |
 +--> Floating Rate
 |
 +--> Reference Rate
 |       |
 |       +--> CORRA
 |       +--> SOFR
 |       +--> SONIA
 |
 +--> Reset Dates
 |
 +--> Payment Dates
 |
 +--> Accrual Period
 |
 +--> Day Count Convention
 |
 +--> Compounding
 |
 +--> Discounting
 |
 +--> MTM
 |
 +--> Collateral
 |
 +--> Settlement
 |
 +--> Regulatory Reporting
```

The IRS example will connect directly to **CORRA**, which is central to Rates-desk regulatory data platform (RDP) work more broadly.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Sells USD / buys CAD forward to ABC Canada Inc.; the reporting/booking entity whose systems this module walks through end to end |
| ABC Canada Inc. | Buy-side client — a Canadian resident hedging a real USD 10,000,000 payable due 30-Nov-2026 (Section 2) |
| A US supplier | The (unnamed) counterparty ABC ultimately owes the USD 10,000,000 to — outside this trade entirely, the underlying commercial reason ABC needs the hedge |
| Trade Repository / Regulatory Platform | Downstream systems receiving the enriched, valued, and event-tagged trade record (Sections 23–28) |

---

## Recap Quiz

1. Using Section 3's callout, why is a USD/CAD forward almost always a Deliverable Forward rather than an NDF, and what field on the trade record captures that distinction?
2. Using Section 8, name two fields this module's enrichment adds that a raw trade-capture record (Section 5.2) does not have, and explain what each answers.
3. Using Sections 9–12, why does the trade's MTM change even though its contractual terms (rate, notional, dates) never change after execution?
4. Using Section 15, what specific risk does posting collateral under a CSA reduce, and why does that risk exist even for a trade with zero MTM at inception?
5. Using Section 19 vs. Section 3's callout, what would settlement look like for this same trade if it had instead been booked as a currency-pair NDF?
6. Using Sections 27–34, why does an event-based (Kafka-style) architecture suit this lifecycle better than a single monolithic "update the trade" service call?
7. Using the enriched trade in Section 8, what does the ISDA Taxonomy Path field tell a downstream system that the UPI field alone does not?

---

## Answers — Recap Quiz, with Explanations

**1. Because CAD, like every other G10 currency, is fully convertible with no capital-account restrictions, so both currencies can and do physically exchange at maturity without needing any special dealer authorization or residency test. The `NDF Flag` field (`N` in Section 8's enriched trade) captures this directly — and the same field would flip to `Y` for a currency like INR that isn't fully convertible.**

Section 3's callout states this explicitly, contrasting the G10 default (CAD) against the INR example the module was originally built around, where onshore/offshore status genuinely determines Deliverable vs. NDF.

**2. Any two of: LEIs (answer "which legal entities are the two parties"), UTI (answers "which specific transaction is this, for regulatory pairing"), UPI (answers "what product template does this belong to"), ISDA Taxonomy Path (answers "how is this product classified"), or Hedge Designation (answers "is this trade formally linked to an offsetting commercial exposure"). None of these exist on the raw Section 5.2 trade-capture record — they're all added downstream, once the trade's basic economics are already known.**

Section 7's list and Section 8's enriched-trade example both show these fields appearing only after enrichment, not at capture.

**3. Because MTM measures the trade's value against *today's* market forward rate, not its own contract rate — the contract rate (1.3545) is fixed forever at trade date, but the market's own 3-month forward rate moves every day (Section 14's table shows it ranging from 1.3400 to 1.3900 over the trade's life). MTM is therefore a function of two things: the fixed contract terms, and the ever-changing external market — only the second one moves.**

Section 10's formula (`MTM ≈ Notional × (Current Forward Rate − Contract Forward Rate)`) makes this explicit — Contract Forward Rate is a constant in that formula, Current Forward Rate is the only variable.

**4. Collateral under a CSA reduces counterparty credit risk — the risk that whichever party currently owes money on the trade's MTM doesn't pay if it defaults. This risk exists even at zero initial MTM because the trade's value can (and does, per Section 14's timeline) swing significantly in either direction over its 3-month life — a trade worth nothing today can easily be worth CAD 355,000 to one side by maturity, and collateral protects against that future swing, not today's snapshot.**

Section 15's framing and Section 16's Variation Margin concept both tie collateral directly to protecting against MTM movement over time, not a static day-one number.

**5. Only a single net USD cash payment would move at maturity — calculated as the difference between the contract rate (1.3545) and a reference fixing rate published on the fixing date, multiplied by the USD notional — with no CAD ever exchanged. Section 19's two-way physical exchange (CAD 13,545,000 one way, USD 10M the other) would not happen at all in an NDF structure.**

Section 3's callout and Section 19's physical settlement diagram, read together, describe exactly this contrast — even though a USD/CAD trade is essentially never actually booked this way in practice, given CAD's full convertibility.

**6. Because different downstream consumers (risk, collateral, regulatory reporting, accounting) each need to react to the SAME underlying change (e.g., a valuation update) independently and at their own pace, without waiting on each other or being tightly coupled to a single "trade service" — Section 34's Kafka flow shows one `valuation.updated` event fanning out to risk-service, collateral-service, and regulatory-service simultaneously, each consuming it on its own schedule, which a single synchronous update call would force into one blocking chain instead.**

Section 33's microservices decomposition and Section 34's event-fan-out diagram both illustrate this directly.

**7. The UPI identifies WHICH specific issued product-reference-data record this trade matches (a single 12-character code, looked up or requested from the DSB) — it doesn't itself tell you the classification structure. The ISDA Taxonomy Path (`Foreign Exchange / Forward / Deliverable / Forward`) is the human-readable classification the UPI's reference data was built from in the first place, immediately telling a downstream system or reader what asset class, base product, sub-product, and transaction type it's dealing with without a lookup.**

[Module 21](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) §5 covers this exact lineage — the taxonomy is the "grammar," the UPI reference data is the "dictionary" built from it.

---

[← Back to Index](../../index.md)
