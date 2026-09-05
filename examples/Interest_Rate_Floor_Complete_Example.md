# Interest Rate Floor — Complete CAD Example

*Continuing the same institutional style used in the IRS, FX Option, FRA, and Cap lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional primary dealer used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What Is a Floor?

An **Interest Rate Floor** is the mirror image of the [Cap](./Interest_Rate_Cap_Complete_Example.md): it protects the holder of a **floating-rate asset** (an investor or lender) against rates **falling**, without giving up the benefit if rates rise. Like a Cap, a Floor is a **strip of options** — one per period, each called a **floorlet**.

```
FLOOR
 │
 +-- A strip of FLOORLETS (one option per period)
 +-- Buyer pays an upfront PREMIUM
 +-- Buyer receives a payment ONLY if the reference rate
     falls BELOW the Floor Rate in that period
 +-- If the rate stays above, buyer receives NOTHING —
     but also pays NOTHING extra
```

Where a Cap protects a **borrower** (short the floating rate, worried it rises), a Floor protects an **investor** (long the floating rate, worried it falls) — the same underlier, the same mechanic, opposite economic direction.

## 2. Business Scenario

Suppose this time:

- **ABC Mining Corporation's own pension fund** holds a **CAD 40,000,000** position in floating-rate notes, paying compounded **CORRA + 0.60%**, resetting quarterly.
- The pension fund's actuarial return targets assume a *minimum* level of interest income — if CORRA falls too far, the fund's floating-rate income could drop below what it needs to meet obligations.
- The pension fund is comfortable keeping the *upside* if rates rise, but wants a guaranteed **floor** under its income.
- It buys a **2-year Interest Rate Floor** from CBT, struck at **3.50%**.

## 3. The Participants

```
                   FLOOR
                     │
        ┌────────────┴────────────┐
        │                         │
  ABC Pension Fund               CBT
                          Canada Bank of Toronto
        │                         │
        │   Buys Floor            │
        │  (Pays Premium,         │
        │   Receives if           │
        │   CORRA < 3.50%)        │
        └─────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation Pension Fund | Floor buyer (protecting its floating-rate note income) |
| Canada Bank of Toronto | Floor seller / dealer (writer of the floorlets) |
| CBT Rates Trading Desk | Prices the strip of floorlets and hedges the option risk |
| CBT Risk Management | Monitors option Greeks and counterparty risk |
| CBT Operations | Confirmation, fixings, and settlement per floorlet |
| CBT Regulatory Reporting | Regulatory reporting |
| External market | Provides CORRA fixings and the volatility surface used to price each floorlet |

## 4. The Trade

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | FLR-20260905-00001 |
| Product | Interest Rate Floor |
| Currency | CAD |
| Notional | CAD 40,000,000 |
| Floor Rate (Strike) | 3.50% |
| Reference Rate | Compounded CORRA (3-month) |
| Term | 2 years (8 quarterly floorlets) |
| Buyer | ABC Mining Corporation Pension Fund |
| Seller | Canada Bank of Toronto |
| Trade Date | 05-Sep-2026 |
| First Floorlet Period | 05-Dec-2026 → 05-Mar-2027 |
| Premium | CAD 460,000 (paid upfront, 07-Sep-2026) |
| Day Count | Act/365 |

The key economic statement is:

> **The pension fund has purchased protection: for each of the next 8 quarters, if CORRA fixes below 3.50%, CBT pays the fund the difference on CAD 40,000,000; if CORRA fixes at or above 3.50%, nothing is paid on that floorlet.**

## 5. Floorlet Settlement — The Mirror of a Caplet

```
Floorlet Settlement (if Reference Rate < Floor Rate)
=
Notional × (Floor Rate − Reference Rate) × (Days/365)
─────────────────────────────────────────────────────
1 + (Reference Rate × Days/365)

Floorlet Settlement (if Reference Rate ≥ Floor Rate) = 0
```

Notice the subtraction is **flipped** relative to the [Cap example §5](./Interest_Rate_Cap_Complete_Example.md) — a floorlet pays out when the reference rate is *below* the strike, a caplet pays out when it's *above*.

## 6. Worked Example — Quarter 1 (CORRA Fixes Below the Floor Rate)

Suppose on the first fixing date (05-Dec-2026), compounded 3-month CORRA is observed at **2.90%**.

**Step 1 — Interest differential (undiscounted):**

```
40,000,000 × (3.50% − 2.90%) × 90/365
= 40,000,000 × 0.0060 × 0.2466
≈ CAD 59,178
```

**Step 2 — Discount factor:**

```
1 / (1 + 2.90% × 90/365)
= 1 / (1 + 0.00715)
≈ 0.99290
```

**Step 3 — Discounted settlement amount:**

```
59,178 × 0.99290
≈ CAD 58,758
```

Since CORRA (2.90%) fell below the Floor Rate (3.50%), CBT pays the pension fund **CAD 58,758** — topping up the fund's floating-rate note income back toward the 3.50% floor level.

## 7. Worked Example — Quarter 5 (CORRA Fixes Above the Floor Rate)

Suppose over a year later, CORRA fixes at **4.80%** for that quarter.

```
Reference Rate (4.80%) ≥ Floor Rate (3.50%)
        ↓
Floorlet Settlement = CAD 0
```

The pension fund receives nothing on this floorlet — but its floating-rate note income for that quarter is also **higher**, because CORRA rose. The fund keeps the full benefit of the higher rate, unlike a Receiver IRS, which would have forced it to hand that upside to its swap counterparty.

```
Receiver IRS (if the fund had used one instead):
  Rates rise → fund still receives only the fixed leg → benefit is GIVEN UP

FLOOR:
  Rates rise → floorlet simply pays zero → fund KEEPS the benefit
```

## 8. Scenario Summary Across the Floor's Life

| Scenario | CORRA Fixing | Floorlet Payout to Fund | Fund's Net Position |
|---|---|---|---|
| Rates fall sharply | 2.90% | ≈ CAD 58,758 | Protected — lost income topped up |
| Rates fall moderately | 3.30% | Small, proportional payout | Mostly protected |
| Rates at the strike | 3.50% | CAD 0 | Income exactly at the floored level |
| Rates rise | 4.80% | CAD 0 | Fund keeps the full benefit of higher rates |

**The pension fund's minimum floating-rate income is effectively floored at approximately 3.50% + 0.60% = 4.10% per quarter, while its upside is uncapped** — the same asymmetric option shape as a Cap, just protecting the opposite side of the rate move.

## 9. Why the Fund Pays a Premium At All

```
Buyer's Payoff Per Floorlet
    +  |  \
       |    \
       |      \
  -----+-------+-------------------  CORRA at fixing
       |        \. . . . . . . . . Floor Rate (3.50%)
    -  |          \________________
       (flat — never negative; capped downside is the premium
        already paid, upside protection triggers as rates fall further)
```

Exactly as in the [Cap example §9](./Interest_Rate_Cap_Complete_Example.md), the fund can never lose more than the premium already paid, and gains every time CORRA falls below 3.50% — so CBT must be compensated upfront for taking on that one-sided risk.

## 10. Valuation and MTM (Before Each Fixing)

```
Forward Curve (expected future CORRA)
          +
Volatility Surface
          +
Discount Curve
          ↓
     Floorlet Value (via a Black-style option model)
          ↓
   Sum across all remaining floorlets
          ↓
        Floor MTM
```

```
Market-implied forward CORRA ↓  → Floor becomes MORE valuable to the fund
Market-implied forward CORRA ↑  → Floor becomes LESS valuable to the fund
Implied volatility ↑             → Floor becomes MORE valuable to the fund
```

This is the mirror-image sensitivity of the Cap's valuation ([Cap example §10](./Interest_Rate_Cap_Complete_Example.md)) — same inputs, opposite direction.

## 11. What Data Does the Valuation Engine Need?

- Trade ID
- Notional
- Floor Rate (Strike)
- Reference Index (CORRA)
- Trade Date, Floorlet Period Start/End dates (all 8)
- Fixing Dates (all 8)
- Day Count Convention
- Forward Curve
- Discount Curve
- Volatility Surface

## 12. The Floor Trade Lifecycle

```
Trade Capture
      ↓
Confirmation
      ↓
Enrichment (LEI, UTI, UPI)
      ↓
Static Data
      ↓
Market Data (Forward Curve, Discount Curve, Volatility Surface)
      ↓
Valuation (all remaining floorlets)
      ↓
MTM
      ↓
Risk / Exposure (incl. Vega)
      ↓
Collateral
      ↓
[Repeat per floorlet:]
   Fixing (CORRA observed) → Settlement Amount Calculation → Settlement (or zero)
      ↓
Regulatory Reporting
```

## 13. Trade Capture

| Field | Value |
|---|---|
| Trade ID | FLR-20260905-00001 |
| Product | Interest Rate Floor |
| Notional | CAD 40,000,000 |
| Currency | CAD |
| Floor Rate | 3.50% |
| Reference Index | CORRA |
| Number of Floorlets | 8 |
| Direction | Buy Floor |
| Premium | CAD 460,000 |
| Trade Date | 05-Sep-2026 |
| Maturity | 05-Sep-2028 |

## 14. Confirmation

| Field | Value |
|---|---|
| Party A | ABC Mining Corporation Pension Fund |
| Party B | Canada Bank of Toronto |
| Party A pays | Premium (upfront) |
| Party B pays | Any floorlet where CORRA < 3.50% |
| Notional | CAD 40,000,000 |
| Term | 2 years, quarterly floorlets |

## 15. Regulatory Reporting

A conceptual regulatory record could contain:

- UTI
- UPI
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class (Interest Rate)
- Product Type (Floor)
- Notional
- Floor Rate
- Floating Rate Index
- Premium, Premium Currency
- Trade Date / Maturity Date
- Number of Floorlets / Floorlet Schedule
- Clearing Status
- Collateralisation
- Valuation Amount
- Action Type / Event Type
- Reporting Timestamp

## 16. Floor vs Cap — Where the Distinction Becomes Clear

| Aspect | Cap | Floor |
|---|---|---|
| Protects | Floating-rate **borrower** | Floating-rate **investor/lender** |
| Pays out when rate is | **Above** the strike | **Below** the strike |
| Worry being hedged | Rates rising | Rates falling |
| Structure | Strip of caplets | Strip of floorlets |
| Premium | Yes, upfront | Yes, upfront |
| Combine the two | Buying a Cap + Selling a Floor = a **Collar** (next lesson) | Buying a Floor + Selling a Cap = a **Collar**, from the opposite side |

```
CAP:   protects against rates going UP    (borrower's worry)
FLOOR: protects against rates going DOWN  (investor's worry)
```

### Mental Model

```
Single-direction rate protection, opposite side of the market
        ↓
       FLOOR
        ↓
Pay a premium once, get paid whenever rates fall below the strike,
pay nothing extra when they don't
```

## Next Lesson: Collar — Interest Rate Collar

The natural next step continues the same progression:

```
Floor → Collar → Swaption → Cross-Currency Swap → CDS
```

A Collar combines a Cap and a Floor into a single structure — typically buying one and selling the other — to narrow the premium cost, at the price of also narrowing the range of rates the hedger benefits from.
