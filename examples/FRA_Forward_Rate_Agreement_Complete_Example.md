# FRA — Forward Rate Agreement — Complete CAD Example

*Continuing the same institutional style used in the IRS and FX Option lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional primary dealer used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What Is an FRA?

A **Forward Rate Agreement (FRA)** is an OTC derivative in which two parties agree today on an interest rate that will apply to a **single future period**, on a notional amount that is **never exchanged**.

```
FRA
 │
 +-- Locks a rate
 +-- For ONE future period
 +-- Notional is NOT exchanged
```

Unlike an IRS (many periods), an FRA covers **exactly one** future interest period. It is the simplest building block of the interest-rate derivatives world — in fact, an IRS can be thought of as a **strip of FRAs**.

## 2. Business Scenario

Suppose:

- ABC Mining Corporation knows it will need to **borrow CAD 50 million** for 3 months, starting in 3 months' time (to fund a capital project).
- The loan will be priced at a floating rate tied to compounded **CORRA**.
- ABC is worried that CORRA might **rise** between now and the borrowing date, making the eventual loan more expensive.
- ABC does not want to enter a full floating-rate loan swap or wait — it wants to lock in the rate for that one specific period, in advance.

Therefore: **ABC buys a "3x6" CORRA-referenced FRA from CBT.**

A "3x6" FRA means:

```
3x6 FRA
 │
 +-- Period starts in 3 months
 +-- Period ends in 6 months
 +-- i.e. covers month 3 → month 6 (a 3-month period)
```

## 3. The Participants

```
                    FRA
                     │
        ┌────────────┴────────────┐
        │                         │
       ABC                       CBT
 Canadian Corp.          Canada Bank of Toronto
        │                         │
        │   Buys FRA              │
        │  (Pay Fixed /           │
        │   Receive Floating)     │
        └─────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation | FRA buyer (hedging a future borrowing) |
| Canada Bank of Toronto | FRA seller / dealer |
| CBT Rates Trading Desk | Prices and hedges the FRA |
| CBT Risk Management | Monitors market and counterparty risk |
| CBT Operations | Confirmation, fixing, and settlement |
| CBT Regulatory Reporting | Regulatory reporting |
| External market | Provides the CORRA fixing / forward curve |

## 4. Why Does ABC "Buy" the FRA?

By convention:

> **FRA Buyer** = pays the **fixed** FRA rate, receives the **floating** reference rate → protects against **rising** rates (the borrower's position).
>
> **FRA Seller** = pays floating, receives fixed → protects against **falling** rates (the lender's / investor's position).

ABC is a future borrower, so ABC **buys** the FRA:

```
ABC's future loan
        │
        ▼
   Pay CORRA (floating)
        │
ABC buys FRA
        │
        ▼
   Pay Fixed / Receive CORRA
        │
        ▼
  CORRA cancels
        │
        ▼
  ABC effectively pays the FRA fixed rate
  for that one period
```

## 5. The Trade

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | FRA-20260905-00001 |
| Product | FRA |
| Currency | CAD |
| Notional | CAD 50,000,000 |
| Buyer | ABC Mining Corporation (pays fixed) |
| Seller | Canada Bank of Toronto (pays floating) |
| FRA Rate (fixed) | 4.75% |
| Reference Rate | Compounded CORRA (3-month) |
| Trade Date | 05-Sep-2026 |
| FRA Period ("3x6") | 05-Dec-2026 → 05-Mar-2027 |
| Fixing Date | 05-Dec-2026 |
| Settlement Date | 07-Dec-2026 |
| Period Length | 90 days |
| Day Count | Act/365 |
| Settlement | Cash, discounted upfront |

The key economic statement is:

> **ABC has locked in a rate of 4.75% on a notional of CAD 50 million for the 3-month period beginning 05-Dec-2026 — regardless of where CORRA actually is on that date.**

## 6. The Reference Rate and Fixing

On the **fixing date** (05-Dec-2026), the applicable compounded CORRA rate for the 3-month forward period is observed and published.

```
Fixing Date
     │
     ▼
Observe compounded 3M CORRA
     │
     ▼
"Reference Rate" is set
     │
     ▼
Compare to FRA Rate (4.75%)
     │
     ▼
Calculate Settlement Amount
```

This fixing is the single most important event in the life of an FRA — everything before it is pricing and risk management; everything after it is arithmetic and settlement.

## 7. Settlement Amount — How It's Calculated

Unlike an IRS, an FRA settles **once**, **upfront**, at the start of the period (not at the end) — and because it is being paid early, the amount is **discounted**.

Conceptually:

```
Settlement Amount
=
Notional × (Reference Rate − FRA Rate) × (Days/365)
─────────────────────────────────────────────────────
1 + (Reference Rate × Days/365)
```

The discounting reflects the fact that, economically, the interest differential would otherwise have been settled at the *end* of the period (like a loan), but FRAs conventionally settle at the *start*.

### Worked Example

Suppose on the fixing date, compounded 3-month CORRA is observed at:

> **5.25%**

Inputs:

| Parameter | Value |
|---|---|
| Notional | CAD 50,000,000 |
| FRA Rate | 4.75% |
| Reference Rate | 5.25% |
| Days | 90 |
| Day Count | 365 |

**Step 1 — Interest differential (undiscounted):**

```
50,000,000 × (5.25% − 4.75%) × 90/365
= 50,000,000 × 0.0050 × 0.2466
≈ CAD 61,644
```

**Step 2 — Discount factor:**

```
1 / (1 + 5.25% × 90/365)
= 1 / (1 + 0.01295)
≈ 0.98722
```

**Step 3 — Discounted settlement amount:**

```
61,644 × 0.98722
≈ CAD 60,856
```

Since the **Reference Rate (5.25%) > FRA Rate (4.75%)**, and ABC is the FRA **buyer** (receives floating, pays fixed), CBT owes ABC the settlement amount.

```
Settlement Direction: CBT ───────► ABC
                        CAD 60,856
```

## 8. Scenario Analysis

### Scenario A — CORRA fixes higher than the FRA rate (as above: 5.25% vs 4.75%)

```
Reference Rate > FRA Rate
        ↓
CBT pays ABC ≈ CAD 60,856
        ↓
This settlement offsets ABC's higher borrowing cost
```

### Scenario B — CORRA fixes exactly at the FRA rate (4.75%)

```
Reference Rate = FRA Rate
        ↓
Settlement Amount = 0
        ↓
No payment either way
```

### Scenario C — CORRA fixes lower than the FRA rate, e.g. 4.00%

```
Reference Rate < FRA Rate
        ↓
ABC pays CBT the (discounted) differential
        ↓
ABC's loan is cheaper, but the FRA payment offsets the saving
```

**In every scenario, ABC's *combined* cost (loan + FRA) is fixed at approximately the FRA rate.** This is the same hedging trade-off principle seen with the IRS: certainty is gained, but the upside from favorable rate moves is given up.

## 9. Valuation and MTM (Before Fixing)

Before the fixing date, the FRA still has a fluctuating mark-to-market value, because the market's expectation of the future CORRA fixing changes daily.

```
Market-implied forward CORRA ↑
              ↓
FRA (pay fixed) becomes more valuable to ABC

Market-implied forward CORRA ↓
              ↓
FRA (pay fixed) becomes less valuable to ABC
```

Conceptually, the valuation engine needs:

```
Forward Curve (to project the expected CORRA fixing)
          +
Discount Curve (to bring the expected settlement to present value)
          ↓
        MTM
```

Formulaically, before fixing:

```
MTM ≈ Notional × (Forward Rate − FRA Rate) × (Days/365) × Discount Factor
```

This mirrors the IRS valuation logic — forecast a cash flow, then discount it — except an FRA has **only one** cash flow to forecast, versus an IRS's many.

## 10. What Data Does the Valuation Engine Need?

- Trade ID
- Notional
- FRA Rate
- Reference Index (CORRA)
- Trade Date
- FRA Period Start / End
- Fixing Date
- Settlement Date
- Day Count Convention
- Business Day Convention
- Holiday Calendar
- Forward Curve
- Discount Curve

This is a strict subset of what an IRS valuation engine needs — one period instead of many, one fixing instead of a schedule of resets.

## 11. Collateral

Suppose, before fixing, the FRA has:

> MTM = +CAD 45,000 (from ABC's perspective)

```
MTM
 ↓
Exposure
 ↓
CSA terms
 ↓
Margin calculation
 ↓
Collateral call (if applicable)
```

Because FRAs are short-dated, single-period instruments, collateral amounts are typically much smaller than for a multi-year IRS on the same notional — but the mechanics are identical.

## 12. The FRA Trade Lifecycle

```
Trade Capture
      ↓
Confirmation
      ↓
Enrichment
      ↓
Static Data
      ↓
Market Data (Forward Curve, Discount Curve)
      ↓
Valuation (pre-fixing)
      ↓
MTM
      ↓
Risk / Exposure
      ↓
Collateral
      ↓
Fixing (CORRA observed)
      ↓
Settlement Amount Calculation
      ↓
Settlement (single cash payment)
      ↓
Regulatory Reporting
```

Notice the lifecycle is almost identical to the IRS — but everything from "Fixing" onward collapses into a **single event**, instead of repeating every quarter.

## 13. Trade Capture

| Field | Value |
|---|---|
| Trade ID | FRA-20260905-00001 |
| Product | FRA |
| Notional | CAD 50,000,000 |
| Currency | CAD |
| FRA Rate | 4.75% |
| Reference Index | CORRA |
| Direction | Buy FRA (Pay Fixed) |
| Trade Date | 05-Sep-2026 |
| Period Start | 05-Dec-2026 |
| Period End | 05-Mar-2027 |
| Fixing Date | 05-Dec-2026 |
| Settlement Date | 07-Dec-2026 |

## 14. Confirmation

| Field | Value |
|---|---|
| Party A | ABC Mining Corporation |
| Party B | Canada Bank of Toronto |
| Party A | Pay Fixed 4.75% |
| Party B | Pay Floating (Compounded CORRA) |
| Notional | CAD 50,000,000 |
| Period | 05-Dec-2026 → 05-Mar-2027 |

## 15. Regulatory Reporting

A conceptual regulatory record could contain:

- UTI
- UPI
- Reporting Counterparty LEI
- Other Counterparty LEI
- Asset Class (Interest Rate)
- Product Type (FRA)
- Notional
- Notional Currency
- FRA Rate
- Floating Rate Index
- Trade Date
- Period Start / End Dates
- Fixing Date
- Settlement Date
- Clearing Status
- Collateralisation
- Valuation Amount
- Valuation Currency
- Action Type
- Event Type
- Reporting Timestamp

As with the IRS, the exact fields depend on the applicable jurisdiction/regime.

## 16. FRA vs IRS — Where the Distinction Becomes Clear

This is the comparison worth internalizing.

| Aspect | FRA | IRS |
|---|---|---|
| Number of periods | One | Many (strip of periods) |
| Cash flow timing | Single, upfront (discounted) | Recurring, at end of each period |
| Purpose | Hedge one specific future interest period | Hedge/transform interest exposure over the life of a loan or asset |
| Complexity | Lower | Higher |
| Fixing events | One | One per reset period |
| Relationship | **An IRS is economically a strip of FRAs, one per period** | Composed of multiple FRA-like periods |

```
FRA
 │
 +-- 1 period
 +-- 1 fixing
 +-- 1 settlement (discounted, upfront)

IRS
 │
 +-- N periods
 +-- N fixings
 +-- N settlements (at period end)
      = a strip of N FRAs, chained together
```

### Mental Model

```
Single future rate exposure
        ↓
       FRA
        ↓
Locked rate for one period

Repeated future rate exposure
        ↓
       IRS
        ↓
Locked (or floating) rate across many periods
```

An FRA is the **atomic unit** of interest-rate hedging; an IRS is what you get when you chain many FRA-like periods together and let the notional persist across the whole schedule instead of settling and closing out after each one.

---

## Next Lesson: Cap — Interest Rate Cap

The natural next step continues the same progression:

```
FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS
```

A Cap can be thought of as **a strip of FRA-like options** — one per period — where the buyer pays a premium for the *right* (not obligation) to receive a payment whenever the reference rate rises above a chosen level, protecting a floating-rate borrower without giving up the benefit of rates falling.
