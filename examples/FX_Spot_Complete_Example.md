# FX Spot Trade — Complete Example

## 1. What is FX Spot?

FX Spot is an agreement to exchange two currencies almost immediately — the "cash" end of the FX product family. Unlike an FX Forward or FX Swap (see the [FX Forward example](./FX_Forward_Complete_Example.md)), there's no locked-in future rate and no derivative economics — you agree on a rate today, and the currencies actually move within a day or two.

> **FX Spot ↔ Immediate exchange of two real currency amounts**

The important point is:

- FX Spot has **no ISIN** — currencies aren't securities.
- FX Spot has **no UPI** either — it isn't a derivative.
- Its only "identifier" is the currency pair itself (ISO 4217 codes).

## 2. Simple Example

Suppose:

- **Bank Of Toronto** needs USD 10,000,000 for an upcoming payment obligation, but only holds CAD.
- It buys USD against CAD in the spot market from **Goldman Sachs**.

### Trade terms

| Term | Value |
|---|---|
| Currency Pair | USD/CAD |
| Notional | USD 10,000,000 |
| Spot Rate | 1.3400 |
| CAD Amount | CAD 13,400,000 |
| Trade Date | 17-Aug-2026 (Monday) |
| Value Date (Settlement) | 19-Aug-2026 (T+2 for USD/CAD... but see Section 3) |

## 3. A Deliberate Wrinkle: USD/CAD Is T+1, Not T+2

Most currency pairs settle T+2, but **USD/CAD is a well-known exception, settling T+1** ([Module 1 §6](../docs/1-capital-markets/module-01-fundamentals.md)). So the actual Value Date here is:

```
Trade Date (T)  : Monday,   17 August 2026
Value Date (T+1): Tuesday,  18 August 2026
```

A reporting system that hard-codes "FX = T+2" will get this specific pair wrong — exactly the kind of assumption [systems-landscape §7](../docs/1-capital-markets/systems-landscape.md) warns against.

## 4. The Trade Lifecycle

```
Order / Quote Request
  ↓
Execution
  ↓
Trade Capture
  ↓
Confirmation (SWIFT MT300-style)
  ↓
Settlement (PvP via CLS, T+1)
  ↓
Position
  ↓
Daily Revaluation
  ↓
Regulatory Reporting (out of scope for most derivative regimes — Section 9)
```

## 5. Trade Capture

| Field | Value |
|---|---|
| Trade ID | FX-20260817-00512 |
| Buyer | Bank Of Toronto |
| Seller | Goldman Sachs |
| Currency Pair | USD/CAD |
| Notional (USD) | 10,000,000 |
| Rate | 1.3400 |
| Notional (CAD) | 13,400,000 |
| Trade Date | 17-Aug-2026 |
| Value Date | 18-Aug-2026 |

## 6. Settlement — PvP via CLS

Settling two different currencies simultaneously across two different countries' payment systems is genuinely hard — CLS (Continuous Linked Settlement) exists to solve exactly this:

```
Bank Of Toronto                                Goldman Sachs
     |                                              |
     |  pays CAD 13,400,000 into CLS                |
     |                                              |  pays USD 10,000,000 into CLS
     |                                              |
              CLS releases BOTH legs simultaneously,
              or releases NEITHER — Payment-versus-Payment (PvP)
     |                                              |
     |<---------------- USD 10,000,000 -------------|
     |----------------- CAD 13,400,000 ------------>|
```

PvP for FX is the direct analogue of DvP for securities ([Module 6 §5](../docs/1-capital-markets/module-06-fx.md)) — it eliminates **settlement risk** (also called Herstatt risk), not counterparty *credit* risk in the CCP-novation sense.

## 7. Position

After settlement, Bank Of Toronto's books show:

| Field | Value |
|---|---|
| USD Cash Position | +10,000,000 |
| CAD Cash Position | −13,400,000 |
| Cost Basis (implied rate) | 1.3400 |

Notice: unlike an IRS's notional, the **full USD 10,000,000 genuinely moved** — this is a cash instrument, not a reference amount.

## 8. Daily Revaluation

Suppose one week later, the USD/CAD spot rate has moved to **1.3450**.

```
Current CAD value of the USD 10,000,000 position:
  = 10,000,000 × 1.3450
  = CAD 13,450,000

Original CAD cost:
  = CAD 13,400,000

Unrealized gain (on the USD leg, in CAD terms) = CAD 50,000
```

[Module 10 §2](../docs/1-capital-markets/module-10-valuation.md) classifies FX Spot as **Quote-Based** valuation — same simple family as a cash equity or bond, since the rate is directly observable in the market with no model needed.

## 9. Why FX Spot Falls Outside Derivative Reporting Regimes

| Regulation | Applies to FX Spot? | Why |
|---|---|---|
| **EMIR** | **No** | FX Spot is explicitly carved out — the Spot/Forward boundary is a settlement-date threshold, roughly the standard spot cycle for that pair ([Module 6 §8](../docs/1-capital-markets/module-06-fx.md)) |
| **MiFID II / MiFIR** | Mostly No | Physically-settled Spot used for genuine currency exchange sits outside MiFID's investment-product scope |
| **SFTR** | No | Not a financing transaction |

Once the settlement date pushes beyond that standard spot window, the *same currency pair* becomes an **FX Forward** instead — and suddenly is in scope for EMIR, needs a UPI, and gets a UTI. This is why the Spot/Forward boundary matters so much operationally: it isn't about the product "feeling" different, it's a pure settlement-date threshold.

## 10. Identifiers Recap

| Identifier | Applies to FX Spot? |
|---|---|
| ISIN / CUSIP / Ticker / FIGI | No — FX has no security to identify |
| Currency Pair (ISO 4217) | **Yes** — the closest thing to an "instrument" identifier |
| UPI | No — not a derivative |
| UTI | No — not typically required for Spot |
| LEI | Yes — identifies Bank Of Toronto and Goldman Sachs as counterparties |

## 11. FX Spot vs FX Forward — Side by Side

| Cash / Derivative | Aspect | FX Spot | FX Forward |
|---|---|---|---|
| CASH | Settlement | T+1/T+2 (immediate) | Any future date agreed today |
| CASH | Identifier | Currency pair only | Currency pair + UPI + UTI |
| CASH | Regulatory scope | Outside EMIR | Inside EMIR |
| CASH | What settles | Both real currency amounts, immediately | Both real currency amounts, later |
| CASH | Rate | Today's market rate | Spot ± Forward Points |

## 12. The Three Most Important FX Spot Concepts

### 1. Notional genuinely settles

Unlike an IRS, the full USD 10,000,000 physically moves.

### 2. PvP, not DvP

```
Currency A  ────┐
                ├──── simultaneous, via CLS
Currency B  ────┘
```

### 3. It's the boundary case for derivative regulation

```
Settlement within the standard spot window  → FX Spot   → Out of EMIR scope
Settlement beyond that window                → FX Forward → In EMIR scope
```

## 13. Mental Model Recap

```
Need for a foreign currency, right now → FX Spot → Immediate exchange, no forward risk, no derivative machinery
```

FX Spot is the simplest possible FX trade — everything more complex in the FX product family (Forward, Swap, Option — see [Module 6](../docs/1-capital-markets/module-06-fx.md)) is built by adding a future date, an option feature, or by combining two Spot/Forward legs together.
