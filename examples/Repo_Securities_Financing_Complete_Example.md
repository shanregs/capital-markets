# Repo (Securities Financing Transaction) — Complete Example

## 1. What is a Repo?

A Repurchase Agreement ("repo") is neither a plain cash instrument nor an OTC derivative — it sits in its own category, a **Securities Financing Transaction (SFT)**. Economically, it's a collateralized cash loan structured as two linked trades: sell a security now, agree to buy it back later at a fixed price.

> **Repo ↔ Collateralized cash loan, wrapped in a sale-and-repurchase structure**

The important point is:

- The security **does** change hands (like a cash trade).
- But economically, nobody actually wants to buy or sell the bond — the whole point is **borrowing/lending cash**, with the bond as collateral.

## 2. Simple Example

Suppose:

- **Bank Of Toronto** needs overnight cash.
- **Goldman Sachs** has surplus cash to lend, and wants safe collateral.
- Bank Of Toronto sells Goldman Sachs a government bond today, and agrees to buy it back tomorrow at a slightly higher price — the difference being the implicit interest (the "repo rate").

### Trade terms

| Term | Value |
|---|---|
| SFT Type | Repurchase transaction (repo) |
| Collateral | Germany 3% Bund 2033 |
| Collateral ISIN | DE0001102481 (illustrative) |
| Nominal Amount | EUR 10,000,000 |
| Collateral Market Value | EUR 9,800,000 |
| Haircut | 2% |
| Cash Lent (Purchase Price) | EUR 9,604,000 |
| Repo Rate | 3.50% |
| Term | Overnight (1 day) |
| Trade Date | 10-Sep-2026 |
| Repurchase Date | 11-Sep-2026 |

## 3. Why the Haircut Exists

```
Collateral Market Value       EUR 9,800,000
Haircut (2%)                  EUR   196,000
------------------------------------------
Cash Lent (Purchase Price)    EUR 9,604,000
```

The **Haircut** buffers Goldman Sachs (the cash lender) against the collateral losing value before it could be liquidated if Bank Of Toronto defaulted overnight. This is the exact same haircut mechanic used for posting collateral under a CSA ([Module 12 §3](../docs/1-capital-markets/module-12-collateral-margin.md)) — a repo is really just collateral management wearing a "sale and repurchase" costume.

## 4. Computing the Repurchase Price

```
Interest = Cash Lent × Repo Rate × (Days / 360)
         = 9,604,000 × 3.50% × (1 / 360)
        ≈ EUR 934

Repurchase Price = Cash Lent + Interest
                  = 9,604,000 + 934
                  = EUR 9,604,934
```

On 11-Sep-2026, Bank Of Toronto pays Goldman Sachs **EUR 9,604,934** and gets its Bund back.

## 5. The Trade Lifecycle

```
Trade Capture (repo leg)
  ↓
Confirmation
  ↓
Collateral Selection & Haircut Calculation
  ↓
Settlement Leg 1 (Bond out, cash in)
  ↓
Daily Valuation of Collateral
  ↓
(Margin Call if collateral value moves — Section 8)
  ↓
Settlement Leg 2 (Bond back, cash + interest out)
  ↓
Regulatory Reporting (SFTR)
```

## 6. Why This Is Neither Pure Cash Nor a Derivative

```
CASH BOND TRADE                REPO                          OTC DERIVATIVE (IRS)
|                               |                             |
+-- Buy to own it              +-- "Sell" but don't want to   +-- No security ever moves
+-- No repurchase obligation   +-- own it — cash is the point +-- Notional is a pure reference
+-- ISIN, no haircut logic     +-- Haircut on the collateral  +-- UPI, no haircut logic
```

[Module 12 §6](../docs/1-capital-markets/module-12-collateral-margin.md) makes this explicit: a repo doesn't need a separate Initial Margin/Variation Margin process the way a derivative does — the **haircut on the collateral itself already IS the risk mitigation**.

## 7. Rehypothecation / Reuse

Goldman Sachs, having received the Bund as collateral, may itself re-pledge it in a separate transaction — this is called **Reuse** or **Rehypothecation**:

```
Bank Of Toronto  --Bund-->  Goldman Sachs  --same Bund-->  a third counterparty
```

SFTR specifically requires reporting **Collateral Reuse** by an EU counterparty, regardless of where the reused security itself physically sits ([Module 16 §4](../docs/2-reporting/module-16-sftr-reporting.md)).

## 8. What if the Collateral's Value Moves Overnight?

Suppose, hypothetically, this were a **term repo** (say, 30 days) instead of overnight, and the Bund's market value fell by EUR 200,000 mid-term. The **Collateral update** Action Type would be triggered:

```
Collateral Market Value falls
        ↓
Bank Of Toronto (borrower) must post additional collateral
        ↓
Margin call, sized to restore the haircut buffer
```

This is exactly the **Margin update / Collateral update** Action Types SFTR defines ([Module 16 §7](../docs/2-reporting/module-16-sftr-reporting.md)).

## 9. Regulatory Reporting — SFTR

A conceptual SFTR record would contain:

**Loan data:**
- UTI
- Counterparty LEIs (Bank Of Toronto, Goldman Sachs)
- Trade Date / Value Date / Maturity Date
- Principal Amount (EUR 9,604,000)
- Rate (3.50%)
- SFT Type Code: **REPO**

**Collateral data:**
- Collateral ISIN (DE0001102481)
- Collateral Market Value (EUR 9,800,000)
- Haircut (2%)
- Collateral type: non-cash

Note the SFT Type Code **REPO** — SFTR uses this small, closed code set instead of the UPI/ISDA taxonomy a derivative would carry, because a repo isn't classified through the derivative product taxonomy at all ([Module 18 §6](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.md)).

## 10. Repo vs Cash Bond vs OTC Derivative — Side by Side

| Cash / Derivative / SFT | Aspect | Cash Bond Trade | Repo | OTC Derivative (IRS) |
|---|---|---|---|---|
| CASH | What settles | Face value + accrued interest, once | — | — |
| SFT | What settles | — | Security + cash, **twice** (out then back) | — |
| DERIVATIVE | What settles | — | — | Net cash flow differences only |
| SFT | Risk mitigation | None needed | Haircut on collateral | IM/VM under a CSA |
| SFT | Regulatory regime | MiFIR | **SFTR** | EMIR/Dodd-Frank |
| SFT | Identifier | ISIN | ISIN (of the collateral) + UTI, **no UPI** | UPI + UTI |

## 11. The Three Most Important Repo Concepts

### 1. It's a financing transaction, not an investment

Nobody actually wants to own the Bund — the cash is the point.

### 2. The haircut IS the risk management

```
Cash Lent = Collateral Value × (1 − Haircut %)
```

### 3. It reports under SFTR, not EMIR

```
Repo  → SFTR  → Type of SFT code REPO
IRS   → EMIR  → UPI + ISDA Taxonomy
```

Confusing these two regimes is a classic beginner mistake — a repo is a financing contract, not a derivative, even though "swap" sometimes appears informally in securities-lending language ([Module 6 §8](../docs/1-capital-markets/module-06-fx.md)'s explicit warning about this exact mix-up).

## 12. Mental Model Recap

```
Need for short-term cash → Repo → Cash borrowed, collateral posted, both legs reverse on the repurchase date
```

A repo is the bridge concept between Cash Instruments and Collateral & Margin — it's built entirely out of a cash security, but its *purpose* is identical to posting collateral against a derivative exposure.
