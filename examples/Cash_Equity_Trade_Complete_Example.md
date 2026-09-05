# Cash Equity Trade — Complete Example

## 1. What is a Cash Equity Trade?

A Cash Equity trade is the purchase or sale of an actual, real security — one unit of ownership in a company. Unlike an OTC derivative (see the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)), there is no "notional reference amount" here — the **Quantity** genuinely changes hands, and the buyer genuinely becomes a part-owner of the company.

> **Cash Equity ↔ Real, deliverable security**

The important point is:

- The shares are **actually delivered** to the buyer's custody account.
- The cash is **actually paid** to the seller.
- Nothing about this trade is a "reference" or a "contract whose value derives from something else" — the stock itself IS the thing being traded.

## 2. Simple Example

Suppose:

- Maple Leaf Pension Fund wants to buy shares of Coca-Cola Company (ticker **KO**).
- It routes the order through its dealer, **Bank Of Toronto**.
- Bank Of Toronto executes the order on the exchange.

### Trade terms

| Term | Value |
|---|---|
| Instrument | Coca-Cola Company common stock |
| Ticker | KO |
| ISIN | US1912161007 |
| Quantity | 10,000 shares |
| Price | USD 68.40 per share |
| Trade Date | 17-Aug-2026 (Monday) |
| Settlement Date | 18-Aug-2026 (T+1) |
| Currency | USD |
| Market Value | USD 684,000 |

This is a **cash instrument** — it has an ISIN, not a UPI, and it settles via Delivery-versus-Payment (DvP), not via periodic net cash flows.

## 3. Why This Is Fundamentally Different From a Derivative

Compare the two:

```
CASH EQUITY                          OTC DERIVATIVE (e.g. IRS)
|                                     |
+-- Real, deliverable security       +-- A contract between two parties
+-- Ownership claim on the company   +-- No ownership of anything
+-- Voting rights                    +-- No voting rights
+-- Dividend entitlement             +-- No dividend (unless a TRS references one)
+-- ISIN identifies it               +-- UPI identifies the product template
+-- Settles: shares + cash           +-- Settles: net cash flows only
```

Maple Leaf Pension Fund isn't just gaining "exposure" to Coca-Cola's price — it becomes one of Coca-Cola's actual shareholders, with a legal claim on the company's residual assets and a vote at the annual meeting.

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
Clearing (CCP novation + netting)
  ↓
Settlement (DvP, T+1)
  ↓
Position
  ↓
Corporate Actions (dividends, splits)
  ↓
Valuation / MTM
  ↓
Regulatory Reporting
```

## 5. Order and Execution

Maple Leaf Pension Fund's order:

| Field | Value |
|---|---|
| Order Type | Market Order |
| Side | Buy |
| Instrument | KO |
| Quantity | 10,000 shares |
| Time-in-Force | Day |

Bank Of Toronto routes this to the exchange (NYSE). The order matches against a resting sell order from another market participant at USD 68.40. **Execution** creates the first legally binding trade.

## 6. Trade Capture

| Field | Value |
|---|---|
| Trade ID | EQ-20260817-00231 |
| Buyer | Maple Leaf Pension Fund |
| Dealer | Bank Of Toronto |
| Instrument | KO — Coca-Cola Company |
| ISIN | US1912161007 |
| Quantity | 10,000 |
| Price | USD 68.40 |
| Gross Consideration | USD 684,000 |
| Commission | USD 342 (0.05%, illustrative) |
| Net Consideration | USD 684,342 |
| Trade Date | 17-Aug-2026 |
| Settlement Date | 18-Aug-2026 |

## 7. Confirmation

The confirmation is a formal statement of trade economics sent between Bank Of Toronto and Maple Leaf Pension Fund, matching quantity, price, and settlement date. Both sides' independent records must agree exactly — this is the **MATCHED** status from the general trade lifecycle.

## 8. Clearing — Where a Cash Equity Trade Genuinely Differs From an IRS

For a cash equity trade, "Clearing" means something narrower and shorter-lived than for an IRS:

```
Trade
  ↓
Novation to CCP (e.g. NSCC)
  ↓
Multilateral netting across ALL of today's KO trades
  ↓
One net DvP settlement instruction per CSD participant
```

There is **no ongoing margin relationship** the way there is for a centrally-cleared IRS (Section 12 of the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)) — clearing here exists purely to net down settlement volume and eliminate one-to-one counterparty risk for a single day, then it's done.

## 9. Settlement — Delivery-versus-Payment (DvP)

On 18-Aug-2026 (T+1):

```
Bank Of Toronto's custodian          Maple Leaf Pension Fund's custodian
        |                                       |
        |  10,000 KO shares  ------------------>|
        |<------------------  USD 684,342       |
        |                                       |
              Both legs move simultaneously
              via the CSD (DTC) — DvP
```

If either leg is missing, the trade **fails** and neither side moves — this is exactly why DvP exists: to eliminate the risk of paying cash without receiving the shares (or vice versa).

## 10. Position

After settlement, Maple Leaf Pension Fund's custody statement shows:

| Field | Value |
|---|---|
| Holding | 10,000 shares of KO |
| Cost Basis | USD 684,342 |
| Custodian | State Street (illustrative) |

This position sits there, generating no cash flows of its own, until Maple Leaf Pension Fund either sells it, or Coca-Cola pays a dividend, or a corporate action occurs.

## 11. Daily Valuation — Mark-to-Market

Suppose one month later, KO's market price has moved to **USD 71.20**.

```
Market Value = Price × Quantity
             = USD 71.20 × 10,000
             = USD 712,000
```

```
Cost Basis        USD 684,342
Market Value      USD 712,000
------------------------------
Unrealized P&L    +USD 27,658
```

Notice how simple this is compared to an IRS's Mark-to-Market (Section 15 of the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)): there is no discounting, no forward curve, no reference-rate observation — just **Price × Quantity**, because a cash equity's entire value lives in one observable, quoted number.

## 12. Corporate Action — A Dividend

Suppose Coca-Cola declares a quarterly dividend of **USD 0.485 per share**.

| Field | Value |
|---|---|
| Ex-Dividend Date | 15-Sep-2026 |
| Record Date | 16-Sep-2026 |
| Payment Date | 01-Oct-2026 |
| Dividend per Share | USD 0.485 |
| Shares Held | 10,000 |
| **Dividend Received** | **USD 4,850** |

This cash lands directly in Maple Leaf Pension Fund's custody account — no confirmation, no netting, no CSA. This is one of the defining features of Cash Equity versus an Equity Derivative: a Total Return Swap (see the [Equity TRS example](./Equity_Total_Return_Swap_Complete_Example.md)) would instead pay this dividend amount as a cash-flow leg of the swap contract, without the receiving party ever actually owning the stock.

## 13. Identifiers Recap

| Identifier | Value | Identifies |
|---|---|---|
| ISIN | US1912161007 | The instrument, globally |
| CUSIP | 191216100 | The instrument, North America |
| Ticker | KO | The instrument, on NYSE specifically |
| LEI | (illustrative) | Coca-Cola Company as a legal entity |
| LEI | (illustrative) | Bank Of Toronto as counterparty |
| LEI | (illustrative) | Maple Leaf Pension Fund as counterparty |

There is **no UPI** here, and there never will be for this trade — UPI exists specifically for OTC derivative product templates ([Module 2 §12](../docs/1-capital-markets/module-02-hierarchy.md)), and a cash equity trade is not one.

## 14. Regulatory Reporting

Under MiFIR-style transaction reporting, a conceptual record could contain:

- Trade ID
- ISIN (US1912161007)
- Buyer LEI / Seller LEI
- Executing Entity LEI (Bank Of Toronto)
- Quantity (10,000)
- Price (USD 68.40)
- Trading Venue (NYSE MIC code)
- Trade Date/Time
- Settlement Date

There is no UTI, no UPI, and no valuation/MTM field in this report the way an EMIR/Dodd-Frank derivative report would carry one (Section 23 of the [IRS example](./Interest_Rate_Swap_IRS_Complete_Example.md)) — MiFIR transaction reporting is a surveillance record of "what happened," not an ongoing exposure record.

## 15. Cash Equity vs OTC Derivative — Side by Side

| Cash / Derivative | Aspect | Cash Equity (KO) | OTC Derivative (IRS) |
|---|---|---|---|
| CASH | What settles | Shares + cash | Only net cash flow differences |
| CASH | Identifier | ISIN | UPI (product) + UTI (trade) |
| CASH | Ownership | Real, with voting rights | None |
| CASH | Valuation | Price × Quantity | PV of discounted future cash flows |
| CASH | Clearing | One-off netting per settlement cycle | Ongoing daily margin for the life of the trade |
| CASH | Collateral | None (settles DvP outright) | IM/VM under a CSA |

## 16. The Three Most Important Cash Equity Concepts

### 1. It's a real, deliverable thing

Unlike a derivative's notional, the Quantity here genuinely changes hands at settlement.

### 2. DvP eliminates settlement risk

```
Securities  ────┐
                ├──── simultaneous
Cash        ────┘
```

### 3. Valuation is just Price × Quantity

```
Market Value = Price × Quantity
```

No curve, no model, no discounting — Section 02 of [Module 10 (Valuation)](../docs/1-capital-markets/module-10-valuation.md) calls this the "Quote-Based" Valuation Family, the simplest of the three.

## 17. Mental Model Recap

```
Cash uncertainty (share price today) → Cash Equity Trade → Real ownership, real risk, real reward
```

Compare this to every derivative example in this folder: a cash trade doesn't *transform* or *hedge* risk the way a derivative does — it simply **transfers ownership**, in full, right now.
