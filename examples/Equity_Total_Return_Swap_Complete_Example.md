# Equity Total Return Swap (TRS) — Complete Example

## 1. What is an Equity TRS?

A Total Return Swap is an OTC derivative where one party receives the **total return** of an underlying stock (price appreciation **plus** dividends), while paying a **financing rate** in exchange. It gives the "receiver" all the economics of owning the stock, **without ever actually owning it**.

> **Equity TRS ↔ Total stock return ↔ Financing rate (floating)**

The important point is:

- No shares ever change hands — this is the derivative counterpart to the [Cash Equity example](./Cash_Equity_Trade_Complete_Example.md).
- The TRS Receiver gets dividend-equivalent cash flows, but **no voting rights** — Bank Of Toronto (as payer, since it's economically short the stock and typically hedges by actually holding it) keeps the vote.

## 2. Simple Example

Suppose:

- **Maple Leaf Pension Fund** wants economic exposure to Apple Inc. shares, but for internal reasons prefers not to hold the stock directly (e.g. avoiding the operational overhead of custody, or wanting leverage).
- It enters a TRS with **Bank Of Toronto**, which hedges itself by actually buying the shares.

### Swap terms

| Term | Value |
|---|---|
| Product | Equity Total Return Swap |
| Underlier | Apple Inc. common stock (AAPL) |
| Reference Notional | 5,000 shares |
| Initial Reference Price | USD 227.15 |
| Initial Notional Value | USD 1,135,750 |
| TRS Receiver | Maple Leaf Pension Fund (receives total return) |
| TRS Payer | Bank Of Toronto (receives financing rate) |
| Financing Rate | SOFR + 0.40% |
| Reset Frequency | Quarterly |
| Trade Date | 17-Aug-2026 |
| Maturity | 17-Aug-2027 |

## 3. Why Bank Of Toronto Actually Buys the Shares

```
Maple Leaf Pension Fund wants AAPL's total return economics
        ↓
Enters a TRS with Bank Of Toronto (Receiver of total return)
        ↓
Bank Of Toronto is now economically SHORT AAPL through the swap
        ↓
Bank Of Toronto HEDGES by actually buying 5,000 AAPL shares
  (a real Cash Equity trade — see the Cash Equity example)
        ↓
Bank Of Toronto's cash position + short TRS position nets to flat
  (it earns the spread between financing received and dividends paid)
```

This is the exact same "dealer hedges the offsetting cash position" pattern the [Commodity Swap example §3](./Commodity_Swap_Complete_Example.md) shows for a commodity swap desk.

## 4. The Two Legs

```
                    EQUITY TRS
                         |
              +----------+----------+
              |                     |
      Total Return Leg      Financing Leg
              |                     |
   Receiver gets:              Payer gets:
   - Price appreciation        - SOFR + 0.40% on the
   - Dividends                   Reference Notional Value
```

## 5. First Quarterly Reset — Suppose AAPL Rises

Suppose after one quarter, AAPL's price has risen from USD 227.15 to **USD 241.60**, and Apple paid a dividend of **USD 0.26/share** during the period.

```
Price Return  = (241.60 − 227.15) × 5,000 shares
              = 14.45 × 5,000
              = USD 72,250

Dividend Return = 0.26 × 5,000
                = USD 1,300

Total Return owed to Maple Leaf Pension Fund = 72,250 + 1,300 = USD 73,550
```

```
Financing Leg (Bank Of Toronto receives):
  Reference Notional Value × (SOFR + 0.40%) × (91/360)
  = 1,135,750 × 5.30% × (91/360)
  ≈ USD 15,225
```

## 6. Net Settlement

| Item | Amount |
|---|---|
| Total Return owed to Maple Leaf Pension Fund | USD 73,550 |
| Financing owed to Bank Of Toronto | USD 15,225 |
| **Net payment to Maple Leaf Pension Fund** | **USD 58,325** |

Only this **net** amount actually moves — exactly the same netting principle as the IRS's fixed-vs-floating exchange ([Module 19 §8](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.md)), just with "price + dividend return" standing in for "fixed rate" as one of the two legs.

## 7. What if AAPL Falls Instead?

Suppose AAPL instead falls to **USD 210.00** over the quarter (still assume the same USD 0.26 dividend):

```
Price Return   = (210.00 − 227.15) × 5,000 = −USD 85,750
Dividend Return                            = +USD 1,300
Total Return                               = −USD 84,450

Financing owed to Bank Of Toronto           = +USD 15,225

Net: Maple Leaf Pension Fund OWES Bank Of Toronto
     84,450 + 15,225 = USD 99,675
```

This is the crucial risk to understand: a TRS Receiver bears the **full downside** of the stock, just like an actual owner would — but without ever holding the shares.

## 8. The Trade Lifecycle

```
Trade Capture
  ↓
Confirmation
  ↓
Enrichment (LEI, UTI, UPI)
  ↓
Bank Of Toronto's Hedge Trade (Cash Equity purchase)
  ↓
Daily Valuation (tracking AAPL's price)
  ↓
Quarterly Reset & Net Settlement
  ↓
Collateral (CSA-driven IM/VM)
  ↓
Regulatory Reporting
```

## 9. Trade Capture

| Field | Value |
|---|---|
| Trade ID | TRS-20260817-00061 |
| Product | Equity Total Return Swap |
| Underlier | AAPL — Apple Inc. common stock |
| Reference Shares | 5,000 |
| Initial Reference Price | USD 227.15 |
| Financing Rate | SOFR + 0.40% |
| Receiver | Maple Leaf Pension Fund |
| Payer | Bank Of Toronto |
| Trade Date | 17-Aug-2026 |
| Maturity | 17-Aug-2027 |
| UPI | (illustrative) "Equity / Swap / PriceReturn / Swap, AAPL" |

## 10. Daily Valuation — Mark-to-Market

Between quarterly resets, the TRS still has value every single day, tracking the underlying share price move since the last reset:

```
Current AAPL Price
        ↓
minus Last Reset Price
        ↓
× Reference Shares
        ↓
minus Accrued Financing Owed
        ↓
      MTM
```

Suppose one month into the quarter, AAPL has risen from 227.15 to 235.00:

```
Unrealized Price Return = (235.00 − 227.15) × 5,000 = USD 39,250
Accrued Financing (1 month) ≈ 1,135,750 × 5.30% × 30/360 ≈ USD 5,017

MTM to Maple Leaf Pension Fund ≈ 39,250 − 5,017 ≈ +USD 34,233
```

## 11. Why This Isn't Ownership

| Aspect | Owning AAPL Directly | AAPL TRS (Receiver side) |
|---|---|---|
| Voting rights | Yes | **No** |
| Legal ownership | Yes | **No** |
| Price exposure | Yes | Yes (synthetic) |
| Dividend economics | Yes | Yes (paid as a cash-flow leg) |
| Settles via | DvP, T+1 | Net cash flow, quarterly |
| Identifier | ISIN | UPI + UTI |
| Leverage | Full cash outlay required | Only margin/collateral required |

## 12. Regulatory Reporting

A conceptual EMIR/Dodd-Frank record would contain:

- UTI
- UPI ("Equity / Swap / PriceReturn / Swap, AAPL")
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class: Equity
- Underlier: AAPL (with its own ISIN referenced as the underlying instrument)
- Reference Notional: 5,000 shares / USD 1,135,750
- Valuation Amount (Section 10's MTM)
- Trade Date / Maturity Date

Relevant only because this is an OTC equity derivative — a listed Equity Future or Option instead would get an exchange-specific contract code, not a UPI ([Module 4 §6](../docs/1-capital-markets/module-04-equity.md)).

## 13. Cash Equity vs Equity TRS — Side by Side

| Cash / Derivative | Aspect | Cash Equity (own AAPL) | Equity TRS (synthetic) |
|---|---|---|---|
| CASH | What settles | Shares + cash, once | Net cash flow, every reset |
| CASH | Identifier | ISIN | UPI + UTI |
| CASH | Ownership | Real | Synthetic |
| CASH | Capital required | Full purchase price | Margin/collateral only |
| CASH | Regulatory scope | MiFIR | EMIR/Dodd-Frank |

## 14. The Three Most Important Equity TRS Concepts

### 1. Synthetic ownership, real economics

Price appreciation and dividends flow through, but nothing is actually owned.

### 2. Two legs, same shape as an IRS

```
Total Return Leg   ↕   Financing Leg
```

### 3. The dealer usually hedges with the real stock

```
TRS Receiver's exposure  =  Dealer's real Cash Equity hedge, offsetting
```

## 15. Mental Model Recap

```
Desire for stock exposure without ownership → Equity TRS → Synthetic return, real financing cost
```

This is the equity-market analogue of everything this series has already shown: a derivative transforms one kind of risk (here, "I want stock exposure without buying stock") into a new shape — exactly like an IRS transforms floating-rate risk, or an FX Forward transforms FX timing risk.
