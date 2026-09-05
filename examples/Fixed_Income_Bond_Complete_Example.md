# Fixed Income Bond Trade — Complete Example

## 1. What is a Cash Bond?

A bond is a real, deliverable debt security — a certificate (today, book-entry) that says "the issuer owes the holder a Face Value, repaid at Maturity, plus periodic Coupon interest along the way." Like a cash equity, it is **not** a contract between two counterparties whose value derives from something else — the bond itself IS the thing being traded.

> **Cash Bond ↔ Real, deliverable debt security**

## 2. Simple Example

Suppose:

- The **Government of Canada** issued a bond several years ago.
- **Goldman Sachs** currently holds a position in it.
- **Bank Of Toronto** wants to buy it for its own book.

### Trade terms

| Term | Value |
|---|---|
| Instrument | Canada 5% Bond 2035 |
| ISIN | CA1358XXXXX |
| Face Value (Quantity) | CAD 10,000,000 |
| Clean Price | 98.50 (per 100 face) |
| Coupon | 5.00%, paid semi-annually |
| Trade Date | 03-Mar-2026 |
| Settlement Date | 05-Mar-2026 (T+2) |
| Last Coupon Date | 01-Jan-2026 |
| Currency | CAD |

## 3. Clean Price vs Dirty Price — Why This Trade Needs Both

Bond quoting convention separates the price into two pieces:

```
Dirty Price = Clean Price + Accrued Interest
```

**Accrued Interest** is the coupon interest that has built up since the last coupon date, which the buyer must pay the seller (since the seller earned it by holding the bond, but the *next* coupon will be paid in full to whoever holds the bond then — the buyer).

### Accrued interest calculation

```
Days since last coupon (01-Jan-2026 → 05-Mar-2026) = 63 days
Day count convention: Actual/365

Accrued Interest = Face Value × Coupon Rate × (Days / 365)
                  = CAD 10,000,000 × 5.00% × (63 / 365)
                  ≈ CAD 86,301
```

### Dirty (settlement) price

```
Clean Price consideration = 10,000,000 × 98.50 / 100 = CAD 9,850,000
Accrued Interest                                     = CAD    86,301
------------------------------------------------------------------
Total Settlement Amount (Dirty Price consideration)  = CAD 9,936,301
```

Bank Of Toronto pays Goldman Sachs **CAD 9,936,301** in total on settlement date — not just the CAD 9,850,000 the quoted Clean Price alone would suggest.

## 4. The Trade Lifecycle

```
Order
  ↓
Execution
  ↓
Trade Capture
  ↓
Confirmation / Matching
  ↓
Clearing (bilateral — Section 6)
  ↓
Settlement (DvP, T+2, via CSD)
  ↓
Position (accruing coupon daily)
  ↓
Coupon Payment
  ↓
Valuation / MTM
  ↓
Regulatory Reporting
```

## 5. Trade Capture

| Field | Value |
|---|---|
| Trade ID | FI-20260303-00089 |
| Buyer | Bank Of Toronto |
| Seller | Goldman Sachs |
| Instrument | Canada 5% Bond 2035 |
| ISIN | CA1358XXXXX |
| Face Value | CAD 10,000,000 |
| Clean Price | 98.50 |
| Accrued Interest | CAD 86,301 |
| Settlement Amount | CAD 9,936,301 |
| Trade Date | 03-Mar-2026 |
| Settlement Date | 05-Mar-2026 |

## 6. Clearing — Bilateral, Not CCP-Novated

Unlike a listed future, most government and corporate bond trades remain **bilateral** — there is no CCP standing between Bank Of Toronto and Goldman Sachs. Settlement risk is instead managed through DvP at the CSD, not through novation and daily margining.

## 7. Settlement — DvP via the CSD

```
Bank Of Toronto's custodian           Goldman Sachs' custodian
        |                                     |
        |  CAD 9,936,301  ------------------->|
        |<------------------  Bond (CAD 10M face) |
        |                                     |
              Both legs move simultaneously
              via CDSX (Canada) — book-entry DvP
```

## 8. Position — What Happens Every Day After Settlement

Bank Of Toronto's books now show:

| Field | Value |
|---|---|
| Holding | Canada 5% Bond 2035, CAD 10,000,000 face |
| Purchase Price | 98.50 clean |
| Accrued Interest (running) | Grows daily until the next coupon |

Each day, the accrued interest balance grows:

```
Daily accrual = Face Value × Coupon Rate / 365
              = 10,000,000 × 5.00% / 365
              ≈ CAD 1,370 per day
```

## 9. Coupon Payment

On 01-Jul-2026, the next semi-annual coupon is paid:

```
Coupon = Face Value × Coupon Rate × (days in period / 365)
       = 10,000,000 × 5.00% × (181 / 365)
       ≈ CAD 247,945
```

Bank Of Toronto receives **CAD 247,945** in cash, and the accrued-interest clock resets to zero the next day.

## 10. Valuation — Mark-to-Market

Suppose by 01-Sep-2026, the bond's market Clean Price has moved to **99.20** (yields fell).

```
Market Value = Face Value × Clean Price / 100 + Accrued Interest
             = 10,000,000 × 99.20 / 100 + (accrued since 01-Jul)
             ≈ 9,920,000 + 27,397
             ≈ CAD 9,947,397
```

```
Cost (Dirty Price paid at purchase)     CAD 9,936,301
Current Value                            CAD 9,947,397 (approx, different accrual date)
```

[Module 10 (Valuation) §2](../docs/1-capital-markets/module-10-valuation.md) classifies this as **Quote-Based valuation** — a bond's price is directly observable in the market, the same Valuation Family as a cash equity, just with an Accrued Interest adjustment layered on top.

## 11. Why a Bond Never Gets a UPI

| Identifier | Applies? | Why |
|---|---|---|
| ISIN | **Yes** | CA1358XXXXX identifies this exact bond, globally |
| CUSIP | Yes (North America) | 135087XX1 (illustrative) |
| Ticker | No | Bonds don't trade under exchange tickers |
| LEI | Yes | Identifies Government of Canada as issuer |
| **UPI** | **No** | UPI is for OTC *derivative* product templates only — a cash bond is not a derivative |

If a reporting record ever shows both an ISIN **and** a UPI on the same trade, that's a red flag — except in the one genuine exception this roadmap covers: an **IR Forward Debt** trade, where a UPI identifies the forward contract and a separate ISIN field identifies the bond it may deliver ([Module 8 §6](../docs/1-capital-markets/module-08-rates.md)).

## 12. Regulatory Reporting

Under MiFIR bond transaction reporting:

- Trade ID
- ISIN (CA1358XXXXX)
- Buyer LEI / Seller LEI
- Quantity (Face Value)
- Price (Clean Price)
- Trading Venue or OTC flag
- Trade Date/Time
- Settlement Date

EMIR does **not** apply here — this is a cash bond trade, not a derivative ([Module 5 §8](../docs/1-capital-markets/module-05-fixed-income.md)).

## 13. Cash Bond vs OTC Derivative — Side by Side

| Cash / Derivative | Aspect | Cash Bond | OTC Derivative (IRS) |
|---|---|---|---|
| CASH | What settles | Face value + accrued interest | Net cash flow differences only |
| CASH | Identifier | ISIN | UPI + UTI |
| CASH | "Size" field | Quantity (face value) — actually delivered | Notional — reference only |
| CASH | Valuation | Quote-Based (Clean Price) | Model-Based (PV of forecast cash flows) |
| CASH | Regulatory regime | MiFIR transaction reporting | EMIR/Dodd-Frank derivative reporting |

## 14. The Three Most Important Cash Bond Concepts

### 1. Face Value actually settles

Unlike an IRS's notional, the CAD 10,000,000 Quantity here genuinely changes hands.

### 2. Clean Price ≠ what you actually pay

```
Dirty Price = Clean Price + Accrued Interest
```

### 3. Coupon is contractual, not modeled

```
Coupon = Face Value × Coupon Rate × (days / 365)
```

No forward curve is needed to know what the coupon will be — only the rate written into the bond itself.

## 15. Mental Model Recap

```
Cash uncertainty (default risk, rate risk) → Bond Trade → Real, deliverable debt claim
```

A bond sits exactly next to a cash equity in the Cash Instruments bucket ([Module 2 §6](../docs/1-capital-markets/module-02-hierarchy.md)) — the only real difference is that a bond has a Maturity and a Coupon, while equity has neither.
