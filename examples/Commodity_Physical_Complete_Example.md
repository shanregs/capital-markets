# Physical Commodity Trade — Complete Example

## 1. What is a Physical Commodity Trade?

A physical commodity trade is the purchase or sale of the actual, tangible good — gold, crude oil, wheat — with real delivery, storage, and title transfer. Like a cash equity or bond, this is **not** a contract that derives its value from something else; the commodity itself IS the thing being traded.

> **Physical Commodity ↔ Real, deliverable tangible good**

## 2. Simple Example

Suppose:

- **Bank Of Toronto**'s commodities desk buys physical gold for its own inventory.
- The counterparty is a bullion dealer, illustratively "Shell Trading" (reused from [Module 7](../docs/1-capital-markets/module-07-commodities.md)'s cast of actors).

### Trade terms

| Term | Value |
|---|---|
| Instrument | Physical Gold |
| Commodity Code | XAU |
| Quantity | 100 oz (one standard gold bar) |
| Spot Price | USD 2,650.00 per oz |
| Total Consideration | USD 265,000 |
| Trade Date | 05-Oct-2026 |
| Settlement Date | 07-Oct-2026 (T+2, physical delivery + vaulting) |
| Delivery Location | Bank Of Toronto's designated vault |

## 3. Why Physical Commodities Rarely Sit on a Bank's Own Book

```
Physical Gold
   |
   +-- Storage cost (vaulting, insurance)
   +-- Delivery logistics
   +-- No coupon, no dividend — pure price exposure
   +-- Illiquid to unwind quickly
```

This is exactly why [Module 7 §2](../docs/1-capital-markets/module-07-commodities.md) notes that physical commodities are "rare in a bank's own book — usually accessed via derivatives instead" (see the [Commodity Swap example](./Commodity_Swap_Complete_Example.md)). A bank wanting *price exposure* to gold, without the storage headache, would use a Commodity Swap or Future instead.

## 4. The Trade Lifecycle

```
Order
  ↓
Execution
  ↓
Trade Capture
  ↓
Confirmation
  ↓
Physical Settlement (title transfer + vaulting)
  ↓
Position (inventory, marked at spot)
  ↓
Valuation / MTM
  ↓
Regulatory Reporting (minimal — mostly outside derivative regimes)
```

## 5. Trade Capture

| Field | Value |
|---|---|
| Trade ID | CMD-20261005-00014 |
| Buyer | Bank Of Toronto |
| Seller | Shell Trading (illustrative) |
| Instrument | Physical Gold |
| Quantity | 100 oz |
| Price | USD 2,650.00/oz |
| Total | USD 265,000 |
| Trade Date | 05-Oct-2026 |
| Settlement Date | 07-Oct-2026 |

## 6. Settlement — Physical Delivery, Not DvP via a CSD

Unlike a security, there is no CSD for a gold bar. Settlement instead means:

```
Seller's vault                             Buyer's vault
     |                                          |
     |  100 oz gold bar (physical transfer)     |
     |----------------------------------------->|
     |<---------------------------- USD 265,000 |
     |                                          |
   Bilateral, dealer-to-dealer, verified by assay/serial number
```

## 7. Position

After settlement, Bank Of Toronto's books show:

| Field | Value |
|---|---|
| Holding | 100 oz physical gold |
| Cost Basis | USD 265,000 |
| Storage | Designated vault, insured |

## 8. Daily Valuation

Suppose the gold spot price moves to **USD 2,710.00/oz** one month later:

```
Market Value = Quantity × Spot Price
             = 100 × 2,710.00
             = USD 271,000

Unrealized P&L = 271,000 − 265,000 = +USD 6,000
```

[Module 10 §2](../docs/1-capital-markets/module-10-valuation.md) classifies this as **Quote-Based** valuation — same simple family as a cash equity or bond.

## 9. Contango and Backwardation — Why This Matters Even for a Spot Holding

If Bank Of Toronto wanted to know what a **future** delivery of gold should cost (relevant if it's weighing physical storage against a Commodity Future instead), it would compare today's spot price to the futures curve:

```
Futures Price = Spot Price + Storage Cost + Financing Cost − Convenience Yield
```

If the futures curve slopes upward (later delivery costs more), the market is in **Contango**; if it slopes downward, it's in **Backwardation** ([Module 7 §1](../docs/1-capital-markets/module-07-commodities.md)). This concept doesn't affect the physical trade's settlement mechanics — it only matters when comparing physical ownership to a derivative alternative.

## 10. Identifiers Recap

| Identifier | Applies to Physical Commodity? |
|---|---|
| ISIN / CUSIP / SEDOL / Ticker / FIGI | **No** — these identify securities, and a gold bar is not one |
| Commodity Code | **Yes** — XAU (ISO 4217-style commodity code) |
| UPI | No — not a derivative |
| LEI | Yes — identifies Bank Of Toronto and Shell Trading as counterparties |

## 11. Regulatory Perspective

Physical commodity trades sit mostly **outside** derivative reporting regimes:

| Regulation | Applies? |
|---|---|
| EMIR / Dodd-Frank | No — not a derivative |
| MiFID II / MiFIR | Generally no, for pure physically-settled spot commodity trades used commercially |
| SFTR | No — not a financing transaction |

Compare this to the [Commodity Swap example](./Commodity_Swap_Complete_Example.md), which is squarely inside EMIR's scope.

## 12. Physical Commodity vs Commodity Swap — Side by Side

| Cash / Derivative | Aspect | Physical Gold | Commodity Swap |
|---|---|---|---|
| CASH | What settles | The actual physical good | Net cash flow differences only |
| CASH | Identifier | Commodity Code | UPI + UTI |
| CASH | Storage/logistics | Real cost, real risk | None — no physical good involved |
| CASH | Valuation | Quote-Based (spot price) | Model-Based (DCF against a floating index) |
| CASH | Regulatory scope | Mostly outside EMIR | Inside EMIR |

## 13. The Three Most Important Physical Commodity Concepts

### 1. It's a real, deliverable good

Unlike a swap's notional, the 100 oz genuinely changes vaults.

### 2. Storage and logistics are real costs

```
Total cost of ownership = Purchase Price + Storage + Insurance + Financing
```

### 3. Valuation is just Quantity × Spot Price

```
Market Value = Quantity × Spot Price
```

## 14. Mental Model Recap

```
Desire for real commodity ownership → Physical Trade → Real delivery, real storage, real price exposure
```

Physical Commodity sits at the "Cash" end of the commodity product family, exactly parallel to how Cash Equity sits at the "Cash" end of the equity product family — everything else in [Module 7](../docs/1-capital-markets/module-07-commodities.md) (Future, Option, Swap) is a derivative built on top of this same underlying good.
