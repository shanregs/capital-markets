# Commodity Swap — Complete Example

## 1. What is a Commodity Swap?

A Commodity Swap is an OTC derivative where two counterparties exchange cash flows over a period — one side pays a **fixed** price, the other pays a **floating** price referencing a published index. No physical barrels ever move — this is the derivative counterpart to the [Physical Commodity example](./Commodity_Physical_Complete_Example.md).

> **Commodity Swap ↔ Fixed price ↔ Floating price, referencing a commodity index**

The important point is:

- The Notional (100,000 barrels) is a **reference quantity only** — it is never physically delivered.
- Only the net cash difference between fixed and floating settles, exactly like an IRS.

## 2. Simple Example

Suppose:

- **Bank Of Toronto** has a client, an airline, that consumes jet fuel and wants to hedge against rising oil prices.
- Bank Of Toronto in turn hedges its own resulting exposure with **Goldman Sachs** via a Brent Crude swap.

### Swap terms

| Term | Value |
|---|---|
| Product | Commodity Swap |
| Underlier | ICE Brent Crude |
| Notional Quantity | 100,000 barrels |
| Term | 1 year |
| Bank Of Toronto pays | Fixed USD 78.00/barrel |
| Bank Of Toronto receives | Floating (average monthly ICE Brent settlement price) |
| Payment Frequency | Monthly |
| Trade Date | 01-Sep-2026 |
| Effective Date | 01-Oct-2026 |
| Maturity Date | 01-Oct-2027 |

## 3. Why the Airline Wants This, and How Bank Of Toronto Passes the Risk Along

```
Airline's jet fuel cost tracks Brent Crude
        |
        +-- Airline is worried Brent will rise
        |
        v
Airline enters a swap with Bank Of Toronto:
  Airline pays Fixed, receives Floating Brent
        |
        v
Bank Of Toronto is now short the floating leg — it hedges by
entering the OFFSETTING swap with Goldman Sachs:
  Bank Of Toronto pays Fixed, receives Floating Brent
        |
        v
Bank Of Toronto's two swaps net out (minus a small spread it earns as dealer)
```

This "back-to-back" pattern is exactly how a dealer bank runs a derivatives book — [Module 22 (Hedging)](../docs/1-capital-markets/module-22-hedging-deep-dive.md) covers this in full.

## 4. The Two Legs

```
             COMMODITY SWAP
                    |
            +-------+-------+
            |               |
        Fixed Leg      Floating Leg
            |               |
     Pay USD 78.00/bbl   Receive avg. ICE Brent settlement
```

## 5. First Monthly Settlement Period

Suppose for October 2026, the average ICE Brent settlement price comes in at **USD 82.50/barrel**.

```
Fixed-leg amount (Bank Of Toronto pays):
  100,000 barrels × USD 78.00 = USD 7,800,000

Floating-leg amount (Bank Of Toronto receives):
  100,000 barrels × USD 82.50 = USD 8,250,000
```

## 6. Net Settlement

| Item | Amount |
|---|---|
| Fixed payment (Bank Of Toronto pays) | USD 7,800,000 |
| Floating receipt (Bank Of Toronto receives) | USD 8,250,000 |
| **Net receipt to Bank Of Toronto** | **USD 450,000** |

Because Brent rose above the fixed strike, Bank Of Toronto is paid the difference — exactly offsetting the higher fuel cost the airline is now paying at the pump, which is the whole point of the hedge.

## 7. What if Brent Falls Instead?

Suppose in a later month, average Brent settles at **USD 72.00/barrel**:

```
Fixed-leg amount (Bank Of Toronto pays):    100,000 × 78.00 = USD 7,800,000
Floating-leg amount (Bank Of Toronto receives): 100,000 × 72.00 = USD 7,200,000
------------------------------------------------------------------------
Net payment BY Bank Of Toronto                                = USD 600,000
```

Bank Of Toronto now pays the net difference — this is the trade-off inherent in any fixed-for-floating swap: certainty in one direction means giving up the benefit of favorable moves in the other, the identical lesson [Module 19 §12](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.md) draws for interest rate swaps.

## 8. The Trade Lifecycle

```
Trade Capture
  ↓
Confirmation
  ↓
Enrichment (LEI, UTI, UPI)
  ↓
Valuation (DCF against the forward Brent curve)
  ↓
MTM
  ↓
Collateral (CSA-driven IM/VM)
  ↓
Monthly Interest Calculation & Settlement
  ↓
Regulatory Reporting
```

## 9. Trade Capture

| Field | Value |
|---|---|
| Trade ID | CS-20260901-00042 |
| Product | Commodity Swap |
| Underlier | ICE Brent Crude |
| Notional Quantity | 100,000 barrels |
| Fixed Price | USD 78.00/bbl |
| Direction | Pay Fixed |
| Trade Date | 01-Sep-2026 |
| Effective Date | 01-Oct-2026 |
| Maturity Date | 01-Oct-2027 |
| UPI | (illustrative) "Brent Crude, Swap, Cash-Settled" |

## 10. Daily Valuation — Mark-to-Market

The valuation engine forecasts every remaining monthly floating cash flow using the **forward Brent curve** (not just today's spot price), nets it against the fixed leg, and discounts back to today:

```
Forecast Future Floating Cash Flows (off forward Brent curve)
              +
Forecast Future Fixed Cash Flows (known — 78.00/bbl every month)
              |
              v
         Net Cash Flows
              |
              v
        Discount to PV
              |
              v
             MTM
```

Suppose the forward Brent curve implies an average future price of USD 80.00/bbl across the remaining 11 months, versus Bank Of Toronto's fixed rate of 78.00:

```
Undiscounted value ≈ 100,000 × (80.00 − 78.00) × 11 months ≈ USD 2,200,000
Discounted MTM (illustrative, ~5% annualized rate) ≈ USD 2,150,000
```

Bank Of Toronto's swap has **positive value** (~USD 2,150,000) because the market now expects Brent to stay above its locked-in fixed rate.

## 11. Collateral

Suppose this swap sits under a CSA with Goldman Sachs and is **above** the UMR threshold ([Module 11 §8](../docs/1-capital-markets/module-11-risk-exposure.md)):

```
MTM (+USD 2,150,000)
        ↓
   Exposure
        ↓
   CSA terms + SIMM
        ↓
Variation Margin call: Goldman Sachs posts ≈ USD 2,150,000 to Bank Of Toronto
```

## 12. Regulatory Reporting

A conceptual EMIR record would contain:

- UTI
- UPI ("Brent Crude, Swap, Cash-Settled")
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class: Commodity
- Notional Quantity: 100,000 barrels
- Fixed Price: USD 78.00/bbl
- Trade Date / Effective Date / Maturity Date
- Valuation Amount (Section 10's MTM)
- Collateralisation flag: Yes

## 13. Identifiers Recap

| Identifier | Applies to Commodity Swap? |
|---|---|
| ISIN / CUSIP / SEDOL / Ticker / FIGI | No — not a security |
| Exchange Contract Code | No — this is OTC, not exchange-listed |
| **UPI** | **Yes** — "Brent Crude, Swap, Cash-Settled," reused across every trade sharing this shape |
| **UTI** | **Yes** — fresh per trade |
| LEI | Yes — Bank Of Toronto and Goldman Sachs |

## 14. Physical Commodity vs Commodity Swap — Side by Side

| Cash / Derivative | Aspect | Physical Gold | Commodity Swap |
|---|---|---|---|
| CASH | Delivery | Real, physical | None — cash-settled |
| CASH | Identifier | Commodity Code | UPI + UTI |
| CASH | Valuation | Spot × Quantity | DCF off forward curve |
| CASH | Regulatory scope | Mostly outside EMIR | Inside EMIR |
| CASH | Purpose | Ownership, industrial use | Price hedging, speculation |

## 15. The Three Most Important Commodity Swap Concepts

### 1. Notional never settles

The 100,000 barrels is purely a multiplier for the cash-flow calculation.

### 2. It's Model-Based, off a forward curve

```
PV(Swap) = PV(Floating Leg) − PV(Fixed Leg)
```

### 3. It's the derivative half of a Physical/Swap pair

```
Physical Commodity  →  Cash instrument, real delivery
Commodity Swap       →  Derivative, cash-settled only
```

## 16. Mental Model Recap

```
Commodity price risk (jet fuel, via Brent) → Commodity Swap → Fixed cost certainty for the airline, risk passed up the dealer chain
```

This mirrors the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)'s risk-transformation model exactly — only the underlying reference (Brent Crude instead of CORRA) has changed.
