# Listed Futures & Listed Options — Complete Example

## 1. What Are Listed Derivatives?

A Listed Derivative is a Future or Option that trades on a regulated **exchange**, with **standardized** contract terms, and clears through that exchange's own CCP **from the instant of execution**. Unlike every OTC derivative in this series ([IRS](./Interest_Rate_Swap_IRS_Complete_Example.md), [FX Forward](./FX_Forward_Complete_Example.md), [CDS](./Credit_Default_Swap_CDS_Complete_Example.md)), nothing here is bilaterally negotiated — two traders executing the same contract an hour apart get identical terms, down to the tick.

> **Listed Derivative ↔ Standardized contract, exchange-traded, always centrally cleared**

The important point is:

- There is **no UPI and no UTI** — a Listed Derivative's fungibility makes per-trade bilateral identifiers meaningless. The exchange's own contract code and trade/clearing IDs serve that role instead.
- Clearing isn't a separate lifecycle stage that happens later — it happens **at execution**, automatically, every single time.

## 2. Worked Example 1 — Interest Rate Future (Hedging, Not Speculating)

Suppose **Bank Of Toronto** owns a large book of long-dated Canadian government bonds ([Fixed Income example](./Fixed_Income_Bond_Complete_Example.md)) and wants to hedge against rates rising (bond prices falling).

### Trade terms

| Term | Value |
|---|---|
| Product | CME 10-Year Treasury Note Future ("ZN") |
| Contract Size | USD 100,000 notional 6% coupon 10-Year T-Note (a standardized fiction) |
| Action | **SELL** 100 contracts |
| Price | 112'16 (112 + 16/32 = 112.500) |
| Notional | 100 × 100,000 × 112.500/100 = **USD 11,250,000** |
| Trade Date | 05-Oct-2026 |
| Seller | Bank Of Toronto |
| Buyer | Anonymous counterparty on the CME order book |

```
Bank Of Toronto SELLS Futures
        ↓
This is a HEDGE, not speculation — it already owns the underlying
bonds and wants to offset a potential price fall (rates rising)
```

### Next business day — rates rise, bond prices fall

```
Price falls from 112.500 to 111.750 (111'24)

Variation Margin = (112.500 − 111.750)/100 × 100,000 × 100 contracts
                  = 0.0075 × 100,000 × 100
                  = USD 75,000
```

Because Bank Of Toronto is **SHORT** the future, a price **fall** is a **gain** — USD 75,000 is credited to its margin account, directly offsetting the loss on the actual bond portfolio the future is hedging.

```
Bond portfolio value  ↓  (loses money as rates rise)
Short Future position ↑  (gains money as rates rise)
------------------------------------------------
              Net P&L ≈ flat — the hedge is working
```

## 3. Worked Example 2 — Listed Equity Option

Suppose **Maple Leaf Pension Fund** wants upside exposure to Apple Inc. shares, with strictly limited downside.

### Trade terms

| Term | Value |
|---|---|
| Product | Listed Equity Call Option |
| Underlier | AAPL — Apple Inc. common stock |
| Expiry | 15-Jan-2027 |
| Strike | USD 250.00 |
| Contracts | 100 (each covering 100 shares = 10,000 shares total) |
| Premium | USD 185,000 (paid upfront) |
| Buyer | Maple Leaf Pension Fund |

### Scenario A — AAPL rallies to USD 280 by expiry

```
Intrinsic Value = (280.00 − 250.00) × 10,000 shares
                = 30.00 × 10,000
                = USD 300,000

Net gain = 300,000 − 185,000 (premium paid) = USD 115,000
```

### Scenario B — AAPL falls to USD 230 by expiry

```
Option expires WORTHLESS (strike above market price)
Maple Leaf Pension Fund loses the full USD 185,000 premium — and
nothing more. This is the defining feature of being long an option:
the maximum loss is fixed and known upfront; the maximum gain is
theoretically unlimited.
```

```
Buyer's P&L
    +  |                         /
       |                       /
       |                     /
  -----+-------------------+------------------  Market Price at expiry
       |. . . . . . . . . /  Strike Price (250)
    -  |________________/
       (flat — capped at the USD 185,000 premium, no matter how
        far the market moves against the position)
```

## 4. Why Both Examples Follow the Same Mechanic

```
Equity Index Future    -> E-mini S&P 500 Future
Commodity Future       -> CME WTI Crude Future
Interest Rate Future   -> CME 10-Year T-Note Future  (Section 2, above)
FX Future              -> CME EUR/USD Future
Listed Option          -> AAPL Call                  (Section 3, above)
```

Five different underlyings, but only **two** contract mechanics across all of them — this is why Listed Derivatives gets pulled out as its own cross-cutting node rather than being described six separate times inside each asset class module ([Module 15 §2](../docs/1-capital-markets/module-15-listed-derivatives.md)).

## 5. The Trade Lifecycle — Instantaneous Clearing

```
Order
  ↓
Execution  ═══════▶  Novation to CCP happens IMMEDIATELY, same instant
  ↓
Trade Capture
  ↓
Daily Variation Margin (every day, for the life of the position)
  ↓
[Expiry: cash settlement, physical delivery, or exercise]
  ↓
Regulatory Reporting (Position Reporting, not EMIR/Dodd-Frank trade reporting)
```

Compare this to a bilateral OTC derivative's Clearing stage (Section 6 of the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)), which happens as a discrete, later step, sometimes optionally — here, it isn't optional and it isn't later.

## 6. What Actually Delivers at Expiry — Three Different Answers

```
E-mini S&P 500 Future at expiry:
  → CASH-SETTLED. No shares ever move; only the final price
    difference is paid.

WTI Crude Future at expiry:
  → PHYSICAL DELIVERY of one specific thing (barrels of WTI at
    Cushing, Oklahoma), if not closed out or rolled first.

10-Year T-Note Future at expiry:
  → PHYSICAL DELIVERY, but the SHORT chooses which eligible bond,
    from a defined basket, to deliver — a genuinely different
    "physical settlement" than either of the above.
```

"Physically settled" doesn't mean the same thing across every Future — a reporting or settlement system that assumes it always means "one predetermined instrument moves" will handle a bond future's delivery notice incorrectly ([Module 15 §5](../docs/1-capital-markets/module-15-listed-derivatives.md)).

## 7. Clearing — Always, No Exceptions

| Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|
| Futures (any underlying) | **Always exchange-traded** | **Always centrally cleared**, from execution |
| Listed Options (any underlying) | **Always exchange-traded** | **Always centrally cleared** (OCC for US equity options, CME Clearing for futures options) |

Every prior product in this series had a genuine two-axis question ("is it OTC or exchange, bilateral or cleared?") with a real answer that varied by product. Listed Derivatives is the one node where the answer is **always the same cell** — by definition ([Module 15 §7](../docs/1-capital-markets/module-15-listed-derivatives.md)).

## 8. Collateral — CCP Margin, Not a Bilateral CSA

| Product Category | IM Required? | VM Required? |
|---|---|---|
| Exchange-traded Futures / Listed Options | Yes (CCP's own model) | Yes, **daily** |

There is no CSA to negotiate here — the CCP (CME Clearing for the T-Note future, OCC for the AAPL option) simply sets its own Initial Margin and calls Variation Margin daily, mechanically, the same way for every participant.

## 9. Identifiers — The Reverse Diagnostic

| Identifier | Applies to Listed Derivatives? |
|---|---|
| ISIN / CUSIP / Ticker | **No** — not securities |
| **Exchange Contract Code** | **Yes** — e.g. `ZNZ26` for the T-Note future, an OCC option symbol for the AAPL call |
| **UPI / UTI** | **No** — these identify bilaterally negotiated OTC derivatives; fungibility makes per-trade bilateral identifiers meaningless here |
| LEI | Yes — identifies Bank Of Toronto and Maple Leaf Pension Fund as counterparties |

A Listed Derivative trade record with an exchange contract code but **no** UPI/UTI is exactly correct — the reverse of the diagnostic OTC records carry. Seeing a UPI/UTI populated on an otherwise clearly listed Futures/Options record would itself be the anomaly ([Module 15 §6](../docs/1-capital-markets/module-15-listed-derivatives.md)).

## 10. Regulatory Reporting

Listed Derivatives fall under **Position Reporting** regimes (exchange/regulator-driven, concentration-risk focused) rather than the EMIR/Dodd-Frank Trade Repository reporting every OTC product in this series requires:

| Regulation | Applies? |
|---|---|
| EMIR / Dodd-Frank (Trade Repository reporting) | No — not an OTC derivative |
| Position Reporting (exchange/regulator) | **Yes** — e.g. large-position reporting thresholds |
| MiFID II / MiFIR | Transaction reporting still applies, since execution happened on a regulated venue |

## 11. Listed vs OTC Derivative — Side by Side

| Cash / Derivative | Aspect | Listed (T-Note Future) | OTC (IRS) |
|---|---|---|---|
| DERIVATIVE | Terms | Standardized by the exchange | Bilaterally negotiated |
| DERIVATIVE | Clearing | Instant, mandatory, always | A separate, sometimes-optional stage |
| DERIVATIVE | Identifier | Exchange contract code | UPI + UTI |
| DERIVATIVE | Margin | CCP's own model, daily | CSA-driven SIMM, if above UMR thresholds |
| DERIVATIVE | Counterparty | Anonymous (CCP-novated) | Known (Goldman Sachs, etc.) |

## 12. The Three Most Important Listed Derivatives Concepts

### 1. Standardization is a venue property, not an asset-class property

The same Future/Option mechanic repeats identically across equity, commodity, rates, and FX underliers.

### 2. Clearing happens at execution, not later

```
OTC:    Trade → (sometimes) → Clearing, as a separate stage
Listed: Trade = Clearing, the same instant
```

### 3. "Physical settlement" means different things for different Futures

An index future never delivers anything; a commodity future delivers one specific thing; a bond future delivers the short's choice from a menu.

## 13. Mental Model Recap

```
Need for standardized, fungible, exchange-cleared exposure → Listed Derivative → Instant novation, daily margin, no bilateral negotiation
```

Where every OTC example in this series ([IRS](./Interest_Rate_Swap_IRS_Complete_Example.md), [FX Forward](./FX_Forward_Complete_Example.md), [Commodity Swap](./Commodity_Swap_Complete_Example.md), [CDS](./Credit_Default_Swap_CDS_Complete_Example.md), [Equity TRS](./Equity_Total_Return_Swap_Complete_Example.md)) is built around two counterparties negotiating bespoke terms, a Listed Derivative trades the ability to customize terms for the ability to transact instantly, anonymously, and with the CCP's credit standing behind every trade from the very first second.
