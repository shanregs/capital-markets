# FX Forward — Complete Example

## 1. What is an FX Forward?

An FX Forward is an OTC derivative where two parties agree **today** to exchange two currencies at a **fixed rate**, on a **specified future date**. Unlike FX Spot (see the [FX Spot example](./FX_Spot_Complete_Example.md)), nothing moves today — only the rate is locked in.

> **FX Forward ↔ A promise to exchange two real currency amounts, later, at a rate fixed now**

The important point is:

- Both currencies **do** eventually settle in full — unlike an IRS's notional, an FX Forward's notional is not just a reference number.
- It **is** an OTC derivative — it needs a UPI and a UTI, and it falls inside EMIR's scope.

## 2. Simple Example

Suppose:

- **Bank Of Toronto** knows it will need USD 10,000,000 in 3 months for a client payment obligation.
- Rather than risk the USD getting more expensive, it locks in a rate today with **Goldman Sachs**.

### Trade terms

| Term | Value |
|---|---|
| Product | FX Forward |
| Currency Pair | USD/CAD |
| Notional | USD 10,000,000 |
| Today's Spot Rate | 1.3500 |
| 3-Month Forward Points | +0.0045 |
| Forward Rate | 1.3545 |
| CAD Amount (fixed today) | CAD 13,545,000 |
| Trade Date | 17-Aug-2026 |
| Value Date (3 months forward) | 17-Nov-2026 |
| Direction | Bank Of Toronto pays CAD, receives USD |

```
Today:                            In 3 months (Value Date):
Bank Of Toronto agrees with        Bank Of Toronto pays CAD 13,545,000
Goldman Sachs on the RATE only.    and receives USD 10,000,000.
NO cash moves yet.                 (10,000,000 × 1.3545 = 13,545,000)
```

## 3. Why the Forward Rate Isn't Just "Today's Spot Rate"

```
Forward Rate = Spot Rate + Forward Points
             = 1.3500 + 0.0045
             = 1.3545
```

Forward Points are driven by the interest rate differential between USD and CAD — this is a **Model-Based** valuation input, not a second quoted market price the way Spot is ([Module 10 §2](../docs/1-capital-markets/module-10-valuation.md)).

## 4. What Happens at the Value Date — Two Scenarios

**Scenario A — Spot rises to 1.3700 (USD got more expensive):**

```
Bank Of Toronto still only pays CAD 13,545,000, not CAD 13,700,000
→ Forward SAVED Bank Of Toronto CAD 155,000 vs. buying at spot then
```

**Scenario B — Spot falls to 1.3400 (USD got cheaper):**

```
Bank Of Toronto still pays CAD 13,545,000, even though spot would have
only cost CAD 13,400,000
→ Forward COST Bank Of Toronto CAD 145,000 vs. buying at spot then
```

> **This is not "the trade going wrong"** — it's the price of certainty. Bank Of Toronto hedged a known future need and gave up any chance of a better rate in exchange for eliminating the risk of a worse one — exactly the same fundamental trade-off the [IRS example §12](./Interest_Rate_Swap_IRS_Complete_Example.md) makes for interest rate risk instead of FX risk.

## 5. The Trade Lifecycle

```
Trade Capture
  ↓
Confirmation
  ↓
Enrichment (LEI, UTI, UPI)
  ↓
Valuation (daily, until Value Date)
  ↓
Collateral (if a CSA is in place and thresholds are breached)
  ↓
Settlement (Value Date — both currencies, in full)
  ↓
Regulatory Reporting (EMIR)
```

## 6. Trade Capture

| Field | Value |
|---|---|
| Trade ID | FXF-20260817-00077 |
| Product | FX Forward |
| Currency Pair | USD/CAD |
| Notional | USD 10,000,000 |
| Forward Rate | 1.3545 |
| Direction | Pay CAD / Receive USD |
| Trade Date | 17-Aug-2026 |
| Value Date | 17-Nov-2026 |
| UPI | FX9M3RXQ2K18 (illustrative) |

## 7. Confirmation & Enrichment

| Field | Value |
|---|---|
| Party A | Bank Of Toronto |
| Party B | Goldman Sachs |
| Party A pays | CAD 13,545,000 |
| Party B pays | USD 10,000,000 |
| Value Date | 17-Nov-2026 |

Enrichment adds: LEIs for both parties, UTI, UPI, Legal Entity, Trading Desk, Book, Clearing Status (**Bilateral** — most FX Forwards remain uncleared, [Module 6 §7](../docs/1-capital-markets/module-06-fx.md)), Reporting Obligation.

## 8. ISDA Classification

**Asset Class** = Foreign Exchange, **Base Product** = Forward, **Sub-Product** = Deliverable (both currencies physically exchange — contrast an **NDF**, where only a cash difference settles because one currency isn't freely convertible), **Transaction Type** = Forward.

## 9. Daily Valuation — Mark-to-Market

Suppose one month later, on 17-Sep-2026, the market's current 2-month forward rate for this same value date has moved to **1.3620**.

```
Original locked-in rate:            1.3545
Current market forward rate:        1.3620
Difference:                         0.0075 per USD
```

```
MTM (undiscounted) = Notional × (Current Forward Rate − Locked Rate)
                    = 10,000,000 × 0.0075
                    = CAD 75,000

MTM (discounted to today, at a 4% discount rate, ~2 months remaining)
                   ≈ 75,000 / (1 + 4% × 2/12)
                   ≈ CAD 74,505
```

From Bank Of Toronto's perspective (it will receive USD at a now-cheap locked rate relative to where the market has moved): **MTM ≈ +CAD 74,505**. From Goldman Sachs' perspective: **MTM ≈ −CAD 74,505**.

```
Forecast Future Cash Flows (both legs)
          ↓
     Net at Value Date
          ↓
     Discount to PV
          ↓
         MTM
```

## 10. Settlement — Both Currencies, In Full

On 17-Nov-2026 (Value Date):

```
Bank Of Toronto                                Goldman Sachs
     |                                              |
     |  pays CAD 13,545,000                         |
     |                                              |  pays USD 10,000,000
     |                                              |
              Settled via CLS (PvP) if CLS-eligible,
              exactly as in the FX Spot example — Section 6
```

This is precisely why an FX Forward's notional is treated differently from an IRS's: **it genuinely settles**, in full, on one future date — not as a stream of periodic net differences.

## 11. Regulatory Reporting — EMIR

A conceptual EMIR trade report would contain:

- UTI
- UPI (FX9M3RXQ2K18)
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class: Foreign Exchange
- Notional: USD 10,000,000
- Forward Rate: 1.3545
- Trade Date / Value Date
- Clearing Status: Bilateral
- Valuation Amount (the MTM from Section 9)
- Action Type: New

## 12. Identifiers Recap

| Identifier | Applies to FX Forward? |
|---|---|
| ISIN / CUSIP / Ticker / FIGI | No |
| Currency Pair | Yes |
| **UPI** | **Yes** — one UPI for "USD/CAD, Forward, Deliverable," reused across every Forward sharing this shape |
| **UTI** | **Yes** — a fresh UTI for this one trade |
| LEI | Yes — Bank Of Toronto and Goldman Sachs |

## 13. FX Spot vs FX Forward vs IRS — Side by Side

| Cash / Derivative | Aspect | FX Spot | FX Forward | IRS |
|---|---|---|---|---|
| CASH | What settles | Both currencies, T+1/T+2 | — | — |
| DERIVATIVE | What settles | — | Both currencies, on Value Date | Net cash flow differences only |
| DERIVATIVE | Identifier | Currency pair only | Currency pair + UPI + UTI | UPI + UTI |
| DERIVATIVE | Notional | Fully settles | Fully settles | Reference only, never settles |
| DERIVATIVE | Regulatory scope | Outside EMIR | Inside EMIR | Inside EMIR |

## 14. The Three Most Important FX Forward Concepts

### 1. Notional genuinely settles — just later

```
Today:        Rate locked, nothing moves
Value Date:   Both currencies move, in full
```

### 2. It's a Model-Based valuation

```
Forward Rate = Spot + Forward Points (interest rate differential)
```

### 3. It's the exact EMIR/Spot boundary case

Cross the standard spot settlement window, and Spot becomes Forward — same currency pair, completely different regulatory treatment ([Module 6 §8](../docs/1-capital-markets/module-06-fx.md)).

## 15. Mental Model Recap

```
Future FX uncertainty → FX Forward → Locked FX rate, certainty today, real settlement later
```

This is the cleanest possible illustration of a "Forward" in the Forward/Swap/Option mental model that runs across every asset class in this curriculum ([Module 23](../docs/1-capital-markets/module-23-otc-forward-swap-option-deep-dive.md)) — one future exchange, at a rate fixed now.
