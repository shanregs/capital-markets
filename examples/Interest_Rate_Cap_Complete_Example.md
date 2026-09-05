# Interest Rate Cap — Complete CAD Example

*Continuing the same institutional style used in the IRS, FX Option, and FRA lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional primary dealer used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What Is a Cap?

An **Interest Rate Cap** is an OTC derivative that protects a floating-rate borrower against rates **rising**, without giving up the benefit if rates fall. A Cap is really a **strip of individual options** — one per period, each called a **caplet** — and each caplet behaves like an FRA that only pays out in one direction.

```
CAP
 │
 +-- A strip of CAPLETS (one option per period)
 +-- Buyer pays an upfront PREMIUM
 +-- Buyer receives a payment ONLY if the reference rate
     rises ABOVE the Cap Rate in that period
 +-- If the rate stays below, buyer receives NOTHING —
     but also pays NOTHING extra
```

This is the single biggest difference from the [FRA example](./FRA_Forward_Rate_Agreement_Complete_Example.md): an FRA settles **both ways** (either party can owe the other); a Cap only ever pays the buyer, never the other way around — that asymmetry is exactly why a Cap needs an upfront premium, the same reason [Module 4](../docs/1-capital-markets/module-04-equity.md)'s Equity Option needs one.

## 2. Business Scenario

Suppose:

- ABC Mining Corporation has a **3-year, CAD 50,000,000 floating-rate loan**, priced at compounded **CORRA + 1.25%**, resetting quarterly.
- ABC is worried CORRA could rise sharply over the next 3 years, but ABC's finance team also believes there's a decent chance rates could **fall**, and doesn't want to give up that benefit the way an IRS or a strip of FRAs would force it to.
- ABC therefore buys a **3-year Interest Rate Cap** from CBT, struck at **5.00%**, instead of swapping into a fully fixed rate.

## 3. The Participants

```
                    CAP
                     │
        ┌────────────┴────────────┐
        │                         │
       ABC                       CBT
 Canadian Corp.          Canada Bank of Toronto
        │                         │
        │   Buys Cap              │
        │  (Pays Premium,         │
        │   Receives if           │
        │   CORRA > 5.00%)        │
        └─────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation | Cap buyer (protecting its floating-rate loan) |
| Canada Bank of Toronto | Cap seller / dealer (writer of the caplets) |
| CBT Rates Trading Desk | Prices the strip of caplets and hedges the option risk |
| CBT Risk Management | Monitors option Greeks and counterparty risk |
| CBT Operations | Confirmation, fixings, and settlement per caplet |
| CBT Regulatory Reporting | Regulatory reporting |
| External market | Provides CORRA fixings and the volatility surface used to price each caplet |

## 4. The Trade

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | CAP-20260905-00001 |
| Product | Interest Rate Cap |
| Currency | CAD |
| Notional | CAD 50,000,000 |
| Cap Rate (Strike) | 5.00% |
| Reference Rate | Compounded CORRA (3-month) |
| Term | 3 years (12 quarterly caplets) |
| Buyer | ABC Mining Corporation |
| Seller | Canada Bank of Toronto |
| Trade Date | 05-Sep-2026 |
| First Caplet Period | 05-Dec-2026 → 05-Mar-2027 |
| Premium | CAD 875,000 (paid upfront, 07-Sep-2026) |
| Day Count | Act/365 |

The key economic statement is:

> **ABC has purchased protection: for each of the next 12 quarters, if CORRA fixes above 5.00%, CBT pays ABC the difference on CAD 50,000,000; if CORRA fixes at or below 5.00%, nothing is paid on that caplet.**

## 5. Each Caplet Is Priced and Settled Like an FRA — But One-Sided

```
Caplet Settlement (if Reference Rate > Cap Rate)
=
Notional × (Reference Rate − Cap Rate) × (Days/365)
─────────────────────────────────────────────────────
1 + (Reference Rate × Days/365)

Caplet Settlement (if Reference Rate ≤ Cap Rate) = 0
```

This is the exact same discounted-settlement formula as the [FRA example §7](./FRA_Forward_Rate_Agreement_Complete_Example.md), except a floor of zero is applied — the caplet is an *option*, not an obligation, so it can never require ABC to pay CBT.

## 6. Worked Example — Quarter 1 (CORRA Fixes Above the Cap Rate)

Suppose on the first fixing date (05-Dec-2026), compounded 3-month CORRA is observed at **5.60%**.

**Step 1 — Interest differential (undiscounted):**

```
50,000,000 × (5.60% − 5.00%) × 90/365
= 50,000,000 × 0.0060 × 0.2466
≈ CAD 73,973
```

**Step 2 — Discount factor:**

```
1 / (1 + 5.60% × 90/365)
= 1 / (1 + 0.01381)
≈ 0.98637
```

**Step 3 — Discounted settlement amount:**

```
73,973 × 0.98637
≈ CAD 72,964
```

Since CORRA (5.60%) exceeded the Cap Rate (5.00%), CBT pays ABC **CAD 72,964** for this caplet — offsetting almost all of the extra interest ABC now owes its lender on the loan.

## 7. Worked Example — Quarter 5 (CORRA Fixes Below the Cap Rate)

Suppose over a year later, CORRA fixes at **4.20%** for that quarter.

```
Reference Rate (4.20%) ≤ Cap Rate (5.00%)
        ↓
Caplet Settlement = CAD 0
```

ABC receives nothing on this caplet — but ABC's loan interest for that quarter is also **lower**, because CORRA fell. ABC keeps the full benefit of the lower rate, unlike a Payer IRS or a Payer FRA, which would have forced ABC to pay the fixed-rate counterparty the difference.

```
IRS/FRA (if ABC had used one instead):
  Rates fall → ABC still pays the fixed leg → benefit is GIVEN UP

CAP:
  Rates fall → caplet simply pays zero → ABC KEEPS the benefit
```

## 8. Scenario Summary Across the Cap's Life

| Scenario | CORRA Fixing | Caplet Payout to ABC | ABC's Net Position |
|---|---|---|---|
| Rates rise sharply | 5.60% | ≈ CAD 72,964 | Protected — extra loan cost offset |
| Rates rise moderately | 5.10% | Small, proportional payout | Mostly protected |
| Rates at the strike | 5.00% | CAD 0 | Loan cost exactly at the capped level |
| Rates fall | 4.20% | CAD 0 | ABC keeps the full benefit of lower rates |

**ABC's maximum possible loan cost (all-in) is effectively capped at approximately 5.00% + 1.25% = 6.25% per quarter, while its downside cost can fall as low as CORRA actually goes** — the defining asymmetric shape of any option-based hedge.

## 9. Why ABC Pays a Premium At All

```
Buyer's Payoff Per Caplet
    +  |                         /
       |                       /
       |                     /
  -----+-------------------+------------------  CORRA at fixing
       |. . . . . . . . . /  Cap Rate (5.00%)
    -  |________________/
       (flat — never negative; capped downside is the premium
        already paid, upside is theoretically unlimited)
```

Because ABC can never lose more on the Cap than the premium it already paid — and stands to gain every time CORRA rises above 5.00% — CBT must be compensated upfront for taking on that one-sided risk. This is precisely [Module 4 §4d](../docs/1-capital-markets/module-04-equity.md)'s option asymmetry, applied to an interest rate instead of a stock.

## 10. Valuation and MTM (Before Each Fixing)

Before each caplet's fixing date, its value fluctuates with the market's expectation of where CORRA will land, **and** with implied volatility (since a caplet is an option, not a plain forward).

```
Forward Curve (expected future CORRA)
          +
Volatility Surface (uncertainty around that expectation)
          +
Discount Curve
          ↓
     Caplet Value (via a Black-style option model)
          ↓
   Sum across all remaining caplets
          ↓
        Cap MTM
```

```
Market-implied forward CORRA ↑  → Cap becomes MORE valuable to ABC
Market-implied forward CORRA ↓  → Cap becomes LESS valuable to ABC
Implied volatility ↑             → Cap becomes MORE valuable to ABC
                                    (more uncertainty = more chance of a payout)
```

Notice the extra input — **volatility** — that neither the [IRS](./Interest_Rate_Swap_IRS_Complete_Example.md) nor the [FRA](./FRA_Forward_Rate_Agreement_Complete_Example.md) valuation needs. This is the same reason the [FX Option example §10-11](./FX_Option_Complete_USDCAD_Example.md) introduced Vega — any instrument with optionality needs a volatility input that a pure forward-type instrument does not.

## 11. What Data Does the Valuation Engine Need?

- Trade ID
- Notional
- Cap Rate (Strike)
- Reference Index (CORRA)
- Trade Date, Caplet Period Start/End dates (all 12)
- Fixing Dates (all 12)
- Day Count Convention
- Forward Curve
- Discount Curve
- **Volatility Surface** (new — required because a Cap is a strip of options, not a strip of forwards)

## 12. The Cap Trade Lifecycle

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
Valuation (all remaining caplets)
      ↓
MTM
      ↓
Risk / Exposure (incl. Vega)
      ↓
Collateral
      ↓
[Repeat per caplet:]
   Fixing (CORRA observed) → Settlement Amount Calculation → Settlement (or zero)
      ↓
Regulatory Reporting
```

## 13. Trade Capture

| Field | Value |
|---|---|
| Trade ID | CAP-20260905-00001 |
| Product | Interest Rate Cap |
| Notional | CAD 50,000,000 |
| Currency | CAD |
| Cap Rate | 5.00% |
| Reference Index | CORRA |
| Number of Caplets | 12 |
| Direction | Buy Cap |
| Premium | CAD 875,000 |
| Trade Date | 05-Sep-2026 |
| Maturity | 05-Sep-2029 |

## 14. Confirmation

| Field | Value |
|---|---|
| Party A | ABC Mining Corporation |
| Party B | Canada Bank of Toronto |
| Party A pays | Premium (upfront) |
| Party B pays | Any caplet where CORRA > 5.00% |
| Notional | CAD 50,000,000 |
| Term | 3 years, quarterly caplets |

## 15. Regulatory Reporting

A conceptual regulatory record could contain:

- UTI
- UPI
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class (Interest Rate)
- Product Type (Cap)
- Notional
- Cap Rate
- Floating Rate Index
- Premium, Premium Currency
- Trade Date / Maturity Date
- Number of Caplets / Caplet Schedule
- Clearing Status
- Collateralisation
- Valuation Amount
- Action Type / Event Type
- Reporting Timestamp

## 16. Cap vs FRA vs IRS — Where the Distinction Becomes Clear

| Aspect | FRA | Cap | IRS |
|---|---|---|---|
| Number of periods | One | Many (strip of caplets) | Many (strip of exchanges) |
| Obligation or right? | Obligation (both ways) | **Right only** (buyer never pays extra) | Obligation (both ways) |
| Premium | No | **Yes, upfront** | No |
| Payout if rate rises | Seller pays buyer | Seller pays buyer | Floating-rate payer pays more |
| Payout if rate falls | Buyer pays seller | **Nothing — buyer keeps the benefit** | Buyer still pays the fixed leg |
| Relationship | Atomic building block | A strip of FRA-shaped **options** | A strip of FRA-shaped **obligations** |

```
FRA:  1 period,  obligation, both directions settle
CAP:  N periods, RIGHT only, one direction settles (or nothing)
IRS:  N periods, obligation, one net amount every period, always
```

### Mental Model

```
Single-direction rate protection, keep the upside
        ↓
        CAP
        ↓
Pay a premium once, get paid whenever rates cross the strike,
pay nothing extra when they don't
```

A Cap is what you get when you take a strip of FRAs and turn each one into an option instead of an obligation — the exact same "asymmetric payoff, capped downside" shape [Module 4](../docs/1-capital-markets/module-04-equity.md) and the [FX Option example](./FX_Option_Complete_USDCAD_Example.md) already established for equities and currencies.

---

## Next Lesson: Floor — Interest Rate Floor

The natural next step continues the same progression:

```
Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS
```

A Floor is a Cap's mirror image — a strip of options that pay out when a floating rate falls **below** a chosen level, protecting an *investor* in floating-rate assets rather than a *borrower*.
