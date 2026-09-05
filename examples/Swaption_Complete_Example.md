# Swaption — Complete CAD Example

*Continuing the same institutional style used in the IRS, FX Option, FRA, Cap, Floor, and Collar lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional primary dealer used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What Is a Swaption?

A **Swaption** is an option to **enter an Interest Rate Swap** at a future date, on terms agreed today. Unlike a [Cap or Floor](./Interest_Rate_Cap_Complete_Example.md) — which are strips of many small per-period options — a Swaption is a **single option on one entire multi-year swap**, exercised (or not) on **one** decision date.

```
SWAPTION
 │
 +-- ONE option
 +-- On ONE future decision date
 +-- Right to enter an ENTIRE multi-year IRS
 +-- "Payer Swaption"    = right to enter as the FIXED-RATE PAYER
 +-- "Receiver Swaption" = right to enter as the FIXED-RATE RECEIVER
```

If the underlying [IRS](./Interest_Rate_Swap_IRS_Complete_Example.md) is the "many periods" instrument and the [FRA](./FRA_Forward_Rate_Agreement_Complete_Example.md) is the "one period" instrument, a Swaption answers a different question entirely: **"do I even want to enter this swap at all, once I know more?"**

## 2. Business Scenario

Suppose:

- ABC Mining Corporation is bidding on a large capital project. If ABC wins the contract (expected to be confirmed in **6 months**), it will need to draw down a **5-year, CAD 50,000,000 term loan**, and will want to swap that loan's floating CORRA exposure into fixed — exactly the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)'s structure.
- ABC doesn't want to commit to an IRS today, because the loan itself is contingent on winning the bid.
- But ABC is worried that if it **waits** and rates rise in the meantime, entering the IRS later will lock in a worse fixed rate.
- ABC buys a **6-month Payer Swaption** on a 5-year CAD IRS, struck at **4.75%**, from CBT.

## 3. The Participants

```
                 SWAPTION
                     │
        ┌────────────┴────────────┐
        │                         │
       ABC                       CBT
 Canadian Corp.          Canada Bank of Toronto
        │                         │
        │   Buys Payer Swaption   │
        │  (right to pay 4.75%    │
        │   fixed on a 5Y IRS,    │
        │   starting in 6 months) │
        └─────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation | Swaption buyer (Payer Swaption) |
| Canada Bank of Toronto | Swaption seller / dealer |
| CBT Rates Trading Desk | Prices the swaption and hedges the resulting swap risk |
| CBT Risk Management | Monitors option Greeks and, post-exercise, the swap's ongoing exposure |
| CBT Operations | Confirmation, exercise notice processing, and (if exercised) new IRS trade capture |
| CBT Regulatory Reporting | Regulatory reporting for the swaption, and separately for the IRS if exercised |
| External market | Provides the swap rate curve and swaption volatility surface |

## 4. The Trade

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | SWO-20260905-00001 |
| Product | Swaption (Payer) |
| Currency | CAD |
| Underlying Swap Notional | CAD 50,000,000 |
| Strike (Swap Fixed Rate) | 4.75% |
| Option Type | Payer Swaption (right to pay fixed) |
| Option Expiry (decision date) | 05-Mar-2027 (6 months) |
| Underlying Swap Tenor | 5 years, starting 05-Mar-2027 |
| Reference Rate (if exercised) | Compounded CORRA (3-month) |
| Exercise Style | European (one decision date) |
| Buyer | ABC Mining Corporation |
| Seller | Canada Bank of Toronto |
| Trade Date | 05-Sep-2026 |
| Premium | CAD 612,000 (paid upfront, 07-Sep-2026) |

The key economic statement is:

> **ABC has the right, on 05-Mar-2027, to enter a 5-year CAD 50,000,000 IRS paying 4.75% fixed — but only if it wants to.**

## 5. Why "Payer" Swaption?

ABC's future loan will make it a floating-rate payer — exactly like the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)'s Bank Of Toronto, ABC will want to **pay fixed, receive floating** to neutralize that exposure. The right to enter that specific position is therefore a **Payer Swaption**.

```
ABC's future loan
        │
        ▼
   Will pay CORRA (floating)
        │
ABC holds a Payer Swaption
        │
        ▼
   Right to pay Fixed 4.75% / receive CORRA
        │
        ▼
   If exercised, CORRA cancels against the loan,
   exactly like the IRS example's mechanic
```

## 6. What Happens on the Decision Date — Two Scenarios

**Scenario A — ABC wins the bid, and 5-year swap rates have risen to 5.40%:**

```
Market rate for a new 5Y IRS today: 5.40%
ABC's Swaption strike:              4.75%

ABC EXERCISES — entering the swap at 4.75% is far better than
the 5.40% available in the market
```

```
Result: ABC now holds a 5-year Payer IRS at 4.75%, exactly as
        if it had entered an IRS the ordinary way — the
        Swaption has converted into a plain IRS
```

**Scenario B — ABC does NOT win the bid (no loan materializes):**

```
ABC simply lets the Swaption expire
        ↓
No obligation, no swap, no ongoing exposure
        ↓
Maximum loss = the CAD 612,000 premium already paid
```

**Scenario C — ABC wins the bid, but 5-year swap rates have fallen to 4.20%:**

```
Market rate for a new 5Y IRS today: 4.20%
ABC's Swaption strike:              4.75%

ABC does NOT exercise — entering a fresh IRS in the market at
4.20% is cheaper than the swaption's 4.75% strike
```

```
Result: ABC lets the Swaption lapse, then enters a brand-new
        5Y IRS in the market at the better 4.20% rate. Its only
        cost from the swaption was the CAD 612,000 premium.
```

## 7. Why This Is Fundamentally Different From a Cap

```
CAP:      12 SEPARATE decisions (one per caplet), each small
SWAPTION: ONE decision, on the ENTIRE 5-year swap at once
```

A Cap protects a stream of individual future periods independently; a Swaption is a single all-or-nothing bet on whether to lock in one swap rate for the whole life of a future position. This is why a Swaption's premium, for a comparable notional and tenor, behaves differently from a Cap's — it depends on the volatility of a **single** swap rate observed on **one** date, not on 12 independent rate observations.

## 8. Payoff Shape at Exercise

```
ABC's Payoff (Payer Swaption)
    +  |                         /
       |                       /
       |                     /
  -----+-------------------+------------------  5Y Swap Rate at expiry
       |. . . . . . . . . /  Strike (4.75%)
    -  |________________/
       (flat — capped at the premium paid, no matter how far
        rates fall below the strike; uncapped gain if rates
        rise well above it)
```

The same asymmetric shape as every other option in this series ([FX Option](./FX_Option_Complete_USDCAD_Example.md), [Cap](./Interest_Rate_Cap_Complete_Example.md), [Floor](./Interest_Rate_Floor_Complete_Example.md)) — a Swaption is simply an option whose underlying is an entire swap rather than a spot price or a single period's rate.

## 9. Valuation and MTM (Before Exercise)

```
Forward Swap Rate Curve (expected 5Y swap rate, starting 6 months forward)
          +
Swaption Volatility Surface (uncertainty around that expectation)
          +
Discount Curve
          ↓
     Swaption Value (via a Black-style swaption pricing model)
```

```
Market-implied forward 5Y swap rate ↑  → Payer Swaption becomes MORE valuable to ABC
Market-implied forward 5Y swap rate ↓  → Payer Swaption becomes LESS valuable to ABC
Implied volatility ↑                    → Swaption becomes MORE valuable (more chance
                                           of a favorable exercise decision)
```

Suppose one month later, the market-implied forward 5Y swap rate for that same starting date has risen to 5.00%:

```
MTM (illustrative, via option pricing model) ≈ +CAD 340,000 to ABC
```

## 10. After Exercise — The Swaption Becomes an IRS

If ABC exercises (Scenario A), the trade record doesn't just close out — it **generates a brand-new IRS trade**, which then follows the exact lifecycle already covered in the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md):

```
Swaption Exercise Notice
        ↓
New IRS Trade Capture (Notional CAD 50,000,000, Fixed 4.75%,
                        5-year term, starting 05-Mar-2027)
        ↓
Confirmation, Enrichment, Valuation, MTM, Collateral,
Quarterly Settlement — everything from the IRS example, Sections 16-23
```

This is a genuinely important operational fact for a reporting system: a Swaption's **Action Type** on its exercise date isn't just "Termination" — it's "Exercise," and it must be linked to the **new** UTI generated for the resulting IRS.

## 11. What Data Does the Valuation Engine Need?

- Trade ID
- Underlying Swap Notional, Fixed Rate (Strike), Floating Index
- Option Expiry (Decision Date)
- Underlying Swap Start Date, Tenor, Payment Frequency
- Exercise Style (European, in this example)
- Forward Swap Rate Curve
- Swaption Volatility Surface (often quoted as a "swaption grid" — expiry × underlying tenor)
- Discount Curve

## 12. The Swaption Trade Lifecycle

```
Trade Capture
      ↓
Confirmation
      ↓
Enrichment (LEI, UTI, UPI)
      ↓
Static Data
      ↓
Market Data (Forward Swap Curve, Swaption Vol Surface, Discount Curve)
      ↓
Valuation (pre-exercise)
      ↓
MTM
      ↓
Risk / Exposure
      ↓
Collateral
      ↓
Decision Date
      ↓
   ┌──────────────┴──────────────┐
   ▼                              ▼
EXERCISE                       EXPIRE
   ↓                              ↓
New IRS Trade Capture           Trade closed, no further
(→ IRS lifecycle from here)     obligation
      ↓
Regulatory Reporting (Swaption, then separately the new IRS)
```

## 13. Trade Capture

| Field | Value |
|---|---|
| Trade ID | SWO-20260905-00001 |
| Product | Swaption (Payer) |
| Underlying Notional | CAD 50,000,000 |
| Strike | 4.75% |
| Option Expiry | 05-Mar-2027 |
| Underlying Swap Tenor | 5 years |
| Direction | Buy Payer Swaption |
| Premium | CAD 612,000 |
| Trade Date | 05-Sep-2026 |

## 14. Confirmation

| Field | Value |
|---|---|
| Party A | ABC Mining Corporation |
| Party B | Canada Bank of Toronto |
| Party A | Buys the right to pay Fixed 4.75% on a 5Y IRS |
| Party B | Sells that right (must enter as floating-rate payer, if exercised) |
| Underlying Notional | CAD 50,000,000 |
| Option Expiry | 05-Mar-2027 |

## 15. Regulatory Reporting

A conceptual regulatory record could contain:

- UTI (Swaption) — and a **separate, new UTI** for the resulting IRS if exercised
- UPI
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class (Interest Rate)
- Product Type (Swaption — Payer/Receiver flag)
- Underlying Swap Notional, Strike, Tenor
- Option Expiry Date
- Premium, Premium Currency
- Trade Date
- Clearing Status
- Collateralisation
- Valuation Amount
- Action Type (New → Exercise or Expire)
- Reporting Timestamp

## 16. Swaption vs Cap vs IRS — Where the Distinction Becomes Clear

| Aspect | Cap | Swaption | IRS |
|---|---|---|---|
| Number of decisions | Many (one per caplet) | **One** (single expiry date) | None — an obligation, not an option |
| What's being optioned | Each individual period's rate | **The decision to enter an entire swap** | N/A |
| Premium | Yes, upfront | Yes, upfront | No |
| If never exercised/entered | Each caplet independently pays or doesn't | **The whole swap simply never happens** | N/A — always happens |
| Typical use | Ongoing protection on an existing floating exposure | Protecting a **future, contingent** need for a swap | Hedging a **known, current** floating exposure |

```
CAP:      many small options, each on ONE period
SWAPTION: one large option, on an ENTIRE swap
IRS:      no optionality at all — a firm obligation from day one
```

### Mental Model

```
Uncertain future NEED for a swap → Swaption → Right (not obligation)
to enter the swap later, on terms fixed today
```

A Swaption is what you get when you take the IRS itself — not just one period of it — and wrap it in an option, exactly the same conceptual step [Module 8 §2](../docs/1-capital-markets/module-08-rates.md) describes as "an option to enter an IRS at a future date."

## Next Lesson: Cross-Currency Swap

The natural next step continues the same progression:

```
Swaption → Cross-Currency Swap → CDS
```

A Cross-Currency Swap looks like an IRS at first glance — periodic exchanges of interest — but adds a genuinely new feature none of this series' products have shown yet: the **notional itself is physically exchanged**, in two different currencies, both at the start of the trade and again at maturity.
