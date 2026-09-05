# Interest Rate Collar — Complete CAD Example

*Continuing the same institutional style used in the IRS, FX Option, FRA, Cap, and Floor lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional primary dealer used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What Is a Collar?

An **Interest Rate Collar** combines a [Cap](./Interest_Rate_Cap_Complete_Example.md) and a [Floor](./Interest_Rate_Floor_Complete_Example.md) into a single structure — the buyer **buys a Cap** (protection against rates rising) and simultaneously **sells a Floor** (giving up protection against rates falling below a lower level), collecting a premium on the Floor that offsets the Cap's cost.

```
COLLAR
 │
 +-- BUY a Cap  (struck HIGH)  — protection if rates rise
 +-- SELL a Floor (struck LOW)  — give up upside if rates fall
 +-- Net Premium = Cap Premium PAID − Floor Premium RECEIVED
 +-- Interest rate is effectively "collared" between the two strikes
```

The whole point is **cost**: a standalone Cap (see the [Cap example](./Interest_Rate_Cap_Complete_Example.md)) can be expensive, especially in a volatile market. Selling a Floor alongside it brings in premium income that narrows — or in a **zero-cost collar**, completely eliminates — the net cost, in exchange for capping the borrower's *downside benefit* as well as its downside risk.

## 2. Business Scenario

Suppose:

- ABC Mining Corporation still has its **CAD 50,000,000 floating-rate loan** (CORRA + 1.25%, quarterly resets) from the [Cap example](./Interest_Rate_Cap_Complete_Example.md).
- ABC's treasury team likes the idea of Cap protection, but doesn't want to pay the full CAD 875,000 premium out of pocket.
- ABC agrees to **give up** some of the benefit it would get if rates fall very low, in exchange for a much cheaper (or free) hedge.
- ABC buys a Cap struck at **5.00%** and simultaneously **sells** a Floor struck at **3.25%**, both referencing the same notional, term, and reset dates.

## 3. The Participants

```
                   COLLAR
                     │
        ┌────────────┴────────────┐
        │                         │
       ABC                       CBT
 Canadian Corp.          Canada Bank of Toronto
        │                         │
        │   BUYS Cap @ 5.00%      │
        │   SELLS Floor @ 3.25%   │
        └─────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation | Collar buyer — long the Cap leg, short the Floor leg |
| Canada Bank of Toronto | Collar seller — short the Cap leg, long the Floor leg |
| CBT Rates Trading Desk | Prices both legs and hedges the net option risk |
| CBT Risk Management | Monitors combined option Greeks and counterparty risk |
| CBT Operations | Confirmation, fixings, and settlement per period, both legs |
| CBT Regulatory Reporting | Regulatory reporting — as **two linked derivative legs** |
| External market | Provides CORRA fixings and the volatility surface for both legs |

## 4. The Trade

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | COL-20260905-00001 |
| Product | Interest Rate Collar (Cap bought + Floor sold) |
| Currency | CAD |
| Notional | CAD 50,000,000 |
| Cap Rate (bought) | 5.00% |
| Floor Rate (sold) | 3.25% |
| Reference Rate | Compounded CORRA (3-month) |
| Term | 3 years (12 quarterly periods) |
| Buyer of Collar | ABC Mining Corporation |
| Seller of Collar | Canada Bank of Toronto |
| Trade Date | 05-Sep-2026 |
| Cap Premium (ABC pays) | CAD 875,000 |
| Floor Premium (ABC receives) | CAD 740,000 |
| **Net Premium (ABC pays)** | **CAD 135,000** |

The key economic statement is:

> **ABC's effective borrowing cost is collared between approximately 3.25% + 1.25% = 4.50% and 5.00% + 1.25% = 6.25% for the next 3 years, for a much smaller net premium than the standalone Cap would have cost.**

## 5. Why the Floor Premium Offsets the Cap Premium

```
Cap Premium (ABC PAYS CBT)          CAD 875,000
Floor Premium (ABC RECEIVES from CBT) CAD 740,000
--------------------------------------------------
Net Premium (ABC pays CBT)           CAD 135,000
```

If ABC had instead chosen a Floor Rate that made the two premiums exactly equal, this would be a **zero-cost collar** — a very common real-world structure precisely because it hedges a borrower's key risk (rates spiking) at no net upfront cash cost.

## 6. Per-Period Settlement — Three Zones

```
CORRA fixing this quarter:

        Below 3.25%              Between 3.25% and 5.00%           Above 5.00%
             │                              │                            │
             ▼                              ▼                            ▼
    ABC's SOLD FLOOR is           Neither leg triggers          ABC's BOUGHT CAP
    triggered — ABC PAYS          — no payment either way       is triggered — CBT
    CBT the difference                                          PAYS ABC the
                                                                 difference
```

## 7. Worked Example — Quarter 1 (CORRA in the Middle Zone)

Suppose CORRA fixes at **4.10%** — inside the collar band (3.25%–5.00%).

```
4.10% is above the Floor (3.25%) and below the Cap (5.00%)
        ↓
Neither the Cap nor the Floor leg triggers
        ↓
Settlement Amount = CAD 0 (on both legs)
```

ABC simply pays its loan at CORRA + 1.25% = 5.35% for the quarter, with no derivative cash flow at all — exactly as if it had no hedge in place for this particular quarter.

## 8. Worked Example — Quarter 6 (CORRA Above the Cap)

Suppose CORRA fixes at **5.80%**.

```
50,000,000 × (5.80% − 5.00%) × 90/365 ≈ CAD 98,630 (undiscounted)
Discounted (same method as the Cap example) ≈ CAD 97,282
```

CBT pays ABC **≈ CAD 97,282** on the Cap leg — the Floor leg does not trigger (5.80% is well above 3.25%). ABC's effective borrowing cost is held near the 6.25% ceiling.

## 9. Worked Example — Quarter 9 (CORRA Below the Floor)

Suppose CORRA fixes at **2.70%** — this is the scenario a standalone Cap buyer would have loved (free ride on low rates), but ABC gave that away by selling the Floor.

```
50,000,000 × (3.25% − 2.70%) × 90/365 ≈ CAD 67,808 (undiscounted)
Discounted ≈ CAD 67,325
```

This time, **ABC pays CBT ≈ CAD 67,325** on the Floor leg it sold — because CORRA fell below 3.25%, CBT exercises its right on the Floor, and ABC owes the difference. ABC's effective borrowing cost is held near the 4.50% floor, even though the raw loan rate (CORRA + 1.25% = 3.95%) would have been cheaper without the Collar.

```
This is the Collar's central trade-off:
  ABC gave up some Cap premium cost
        in exchange for
  ABC giving up some benefit when rates fall very low
```

## 10. Scenario Summary Across the Collar's Life

| Scenario | CORRA Fixing | Cap Leg | Floor Leg | Net Cash Flow | ABC's Effective Rate |
|---|---|---|---|---|---|
| Rates spike | 5.80% | CBT pays ABC | — | + to ABC | Held near 6.25% ceiling |
| Rates mid-range | 4.10% | — | — | CAD 0 | CORRA + 1.25% (unhedged that quarter) |
| Rates fall moderately | 3.25%–5.00% | — | — | CAD 0 | Floats freely inside the band |
| Rates fall sharply | 2.70% | — | ABC pays CBT | − from ABC | Held near 4.50% floor |

## 11. Valuation and MTM

A Collar's MTM is simply the **net** of its two option legs:

```
Collar MTM = Cap MTM (long position) − Floor MTM (short position)
```

```
Market-implied forward CORRA ↑  → Cap leg gains value, Floor leg loses value
                                   → Collar MTM generally RISES (net long Cap-like exposure)
Market-implied forward CORRA ↓  → Cap leg loses value, Floor leg gains value (against ABC,
                                   since ABC is short the Floor)
                                   → Collar MTM generally FALLS
Implied volatility ↑             → BOTH legs' absolute value rises — the NET effect partially
                                   cancels, unlike a standalone Cap or Floor
```

This last point is the key operational lesson: a Collar's **Vega** is much smaller than either leg's individual Vega, because ABC is long volatility on one leg and short volatility on the other — a genuinely different risk profile from either the [Cap](./Interest_Rate_Cap_Complete_Example.md) or [Floor](./Interest_Rate_Floor_Complete_Example.md) example on its own.

## 12. What Data Does the Valuation Engine Need?

Everything from the [Cap](./Interest_Rate_Cap_Complete_Example.md) and [Floor](./Interest_Rate_Floor_Complete_Example.md) examples, **for both legs**, plus:

- A flag identifying the two legs as economically linked (a single Collar structure, not two unrelated trades)
- Net Premium (rather than two separate gross premiums, for a single cash settlement)
- Combined Greeks reporting (net Delta, net Vega across both legs)

## 13. The Collar Trade Lifecycle

```
Trade Capture (TWO linked legs: Cap bought + Floor sold)
      ↓
Confirmation
      ↓
Enrichment (LEI, UTI, UPI — one per leg, or a package UPI depending on the regime)
      ↓
Static Data
      ↓
Market Data (Forward Curve, Discount Curve, Volatility Surface)
      ↓
Valuation (both legs, netted)
      ↓
MTM (net)
      ↓
Risk / Exposure (net Greeks)
      ↓
Collateral
      ↓
[Repeat per period:]
   Fixing → Determine which leg (if any) triggers → Settlement
      ↓
Regulatory Reporting (both legs)
```

## 14. Trade Capture

| Field | Value |
|---|---|
| Trade ID | COL-20260905-00001 |
| Product | Interest Rate Collar |
| Leg 1 | Buy Cap, Strike 5.00%, Premium CAD 875,000 (paid) |
| Leg 2 | Sell Floor, Strike 3.25%, Premium CAD 740,000 (received) |
| Notional | CAD 50,000,000 |
| Currency | CAD |
| Reference Index | CORRA |
| Net Premium | CAD 135,000 (paid) |
| Trade Date | 05-Sep-2026 |
| Maturity | 05-Sep-2029 |

## 15. Confirmation

| Field | Value |
|---|---|
| Party A | ABC Mining Corporation |
| Party B | Canada Bank of Toronto |
| Party A | Buys Cap @ 5.00%, Sells Floor @ 3.25% |
| Party B | Sells Cap @ 5.00%, Buys Floor @ 3.25% |
| Notional | CAD 50,000,000 |
| Term | 3 years, quarterly |

## 16. Regulatory Reporting

A conceptual regulatory record — typically reported as **two linked derivative legs** sharing a package/strategy identifier — would contain:

- UTI (per leg, or one shared per the regime's linked-transaction convention)
- UPI (per leg — one for the Cap shape, one for the Floor shape)
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class (Interest Rate)
- Product Type: Cap (Leg 1), Floor (Leg 2)
- Notional, Strike (per leg)
- Net Premium
- Trade Date / Maturity Date
- Clearing Status
- Collateralisation
- Valuation Amount (net)
- Action Type / Event Type
- Reporting Timestamp

## 17. Collar vs Cap vs Floor — Where the Distinction Becomes Clear

| Aspect | Cap (standalone) | Floor (standalone) | Collar |
|---|---|---|---|
| Legs | One (bought) | One (bought) | Two (one bought, one sold) |
| Premium | Paid in full | Paid in full | **Net** — often much smaller, or zero |
| Upside if rates favorable | Fully kept | Fully kept | **Given up beyond the sold leg's strike** |
| Protection if rates unfavorable | Full, above the strike | Full, below the strike | Full, but only to the sold leg's strike |
| Typical use | Pure protection, cost not a concern | Pure protection, cost not a concern | Cost-conscious hedging |

```
CAP alone:    pay premium, keep ALL upside, capped downside risk
FLOOR alone:  pay premium, keep ALL upside, capped downside risk (opposite direction)
COLLAR:       pay LESS (or nothing) net, but upside is now ALSO capped
```

### Mental Model

```
Want Cap-like protection but don't want to pay the full premium?
        ↓
     COLLAR
        ↓
Buy the Cap, sell a Floor to fund it, accept a capped
range of outcomes instead of one-sided protection
```

## Next Lesson: Swaption — Option to Enter an Interest Rate Swap

The natural next step continues the same progression:

```
Collar → Swaption → Cross-Currency Swap → CDS
```

A Swaption is a genuinely different kind of optionality: instead of a strip of small per-period options like a Cap, Floor, or Collar, it is a **single option on an entire multi-year swap**, exercised (or not) on one decision date.
