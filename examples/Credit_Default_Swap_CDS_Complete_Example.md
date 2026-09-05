# Credit Default Swap (CDS) — Complete Example

## 1. What is a CDS?

A Credit Default Swap is an OTC derivative that behaves like **insurance against a company defaulting**. One party (the protection buyer) pays a periodic premium; the other (the protection seller) pays out a large amount, but only if a defined Credit Event actually happens.

> **CDS ↔ Protection Buyer pays premium ↔ Protection Seller pays out on default**

The important point is:

- Unlike an IRS or FX Forward, a CDS's cash flows are **contingent** — the big payout may never happen at all.
- A CDS doesn't reference the Reference Entity's stock — it references its **default risk**.

## 2. Simple Example

Suppose:

- **Bank Of Toronto** holds credit exposure to **Northstar Resources Inc.** (a fictional corporate) and wants to hedge it.
- It buys protection from **Goldman Sachs**.

### Trade terms

| Term | Value |
|---|---|
| Product | Single-Name CDS |
| Reference Entity | Northstar Resources Inc. |
| Reference Obligation | Northstar Resources Inc. 6% Bond 2031 |
| Notional | USD 10,000,000 |
| Term | 5 years |
| Standardized Running Coupon | 100bps (1.00%), paid quarterly |
| Market-Quoted Credit Spread (today) | 250bps (2.50%) |
| Protection Buyer | Bank Of Toronto |
| Protection Seller | Goldman Sachs |
| Trade Date | 17-Aug-2026 |

## 3. Why There's Both a Coupon AND an Upfront Payment

Since 2009's ISDA "Big Bang" Protocol, CDS Running Coupons are standardized to fixed levels (100bps or 500bps) rather than freely quoted, so the market instead reconciles the gap between the *actual* market spread and the standardized coupon with a one-time **Upfront Payment**:

```
Approx. Upfront = (Market Spread − Running Coupon) × Risky Duration × Notional
                = (2.50% − 1.00%) × 4.5 (illustrative Risky Duration) × 10,000,000
                ≈ USD 675,000
```

Because the market spread (250bps) is **higher** than the standardized coupon (100bps), Bank Of Toronto (the protection buyer) pays this **USD 675,000 upfront** to Goldman Sachs, on top of the ongoing quarterly coupon.

## 4. The Two "Legs" of a CDS

```
                    CDS
                     |
            +--------+--------+
            |                 |
      Premium Leg       Protection Leg
            |                 |
   Buyer pays Running    Seller pays out
   Coupon quarterly,     ONLY if a Credit
   + Upfront today       Event occurs
```

## 5. Quarterly Running Coupon Payment

```
Quarterly Coupon = Notional × Running Coupon × (days / 360)
                  = 10,000,000 × 1.00% × (90 / 360)
                  = USD 25,000
```

Bank Of Toronto pays Goldman Sachs **USD 25,000 every quarter**, for as long as no Credit Event occurs and the contract hasn't matured.

## 6. The Trade Lifecycle

```
Trade Capture
  ↓
Confirmation (via MarkitWire / ICE Link)
  ↓
Enrichment (LEI — including Reference Entity LEI, UTI, UPI)
  ↓
Upfront Payment Settlement
  ↓
Quarterly Coupon Payments
  ↓
Daily Valuation (credit spread curve)
  ↓
Collateral (CSA-driven IM/VM)
  ↓
[Either: Maturity, unchanged]
[Or: Credit Event → Auction → Payout]
  ↓
Regulatory Reporting
```

## 7. Trade Capture

| Field | Value |
|---|---|
| Trade ID | CDS-20260817-00033 |
| Product | Single-Name CDS |
| Reference Entity | Northstar Resources Inc. |
| Reference Obligation | Northstar Resources Inc. 6% Bond 2031 |
| Notional | USD 10,000,000 |
| Running Coupon | 100bps |
| Market Spread at Execution | 250bps |
| Upfront | USD 675,000 (paid by Bank Of Toronto) |
| Direction | Bank Of Toronto — Protection Buyer |
| Maturity | 20-Jun-2031 (standardized IMM date, illustrative) |

## 8. Enrichment — A CDS-Specific Twist on LEI

Unlike every prior product in this series, a CDS carries **two separate kinds of LEI**:

```
Counterparty LEIs        : identify Bank Of Toronto and Goldman Sachs (WHO is trading)
Reference Entity LEI      : identifies Northstar Resources Inc. (WHAT is being referenced)
```

Northstar Resources Inc. is **not a counterparty at all** — it never agrees to anything, never signs anything, and may not even know the trade exists. It's simply the subject of the contract ([Module 9 §6](../docs/1-capital-markets/module-09-credit.md)).

## 9. Daily Valuation — Mark-to-Market

Suppose one month later, Northstar Resources Inc.'s credit spread widens to **320bps** (the market now sees Northstar as riskier).

```
PV(CDS) ≈ (New Spread − Contract Spread) × Risky Duration × Notional
         = (3.20% − 2.50%) × 4.4 (illustrative, duration shortens slightly) × 10,000,000
        ≈ USD 308,000
```

Since Bank Of Toronto is the protection **buyer**, a wider spread (Northstar looking riskier) makes its position **more valuable** — its protection is now worth more than what it's paying for it:

```
Credit spread widens (Northstar riskier)
        ↓
Protection Buyer's CDS becomes MORE valuable
        ↓
MTM to Bank Of Toronto: +USD 308,000 (approx.)
```

[Module 10 §2](../docs/1-capital-markets/module-10-valuation.md) classifies this as **Model-Based** valuation: Credit spread curve × Risky Duration × Notional.

## 10. Collateral

If this CDS sits under a CSA and is above UMR thresholds:

```
MTM (+USD 308,000 to Bank Of Toronto)
        ↓
   Exposure
        ↓
   CSA + SIMM
        ↓
Goldman Sachs posts Variation Margin ≈ USD 308,000
```

## 11. Credit Event — Northstar Resources Inc. Defaults

Eighteen months later, suppose Northstar Resources Inc. **misses a bond coupon payment** — a defined Credit Event under the ISDA Master Agreement.

```
Trigger: Northstar Resources Inc. misses a bond coupon payment
        ↓
ISDA Determinations Committee rules a Credit Event has occurred
        ↓
Auction (or physical settlement) determines the Recovery Rate
        ↓
Payout = Notional × (100% − Recovery Rate)
```

Suppose the auction determines a **Recovery Rate of 40%**:

```
Payout = 10,000,000 × (100% − 40%)
       = 10,000,000 × 60%
       = USD 6,000,000
```

Goldman Sachs (the protection seller) pays Bank Of Toronto **USD 6,000,000**. In exchange, Bank Of Toronto may need to deliver the Reference Obligation (or a cash-settlement equivalent based on the auction price) — the mechanic depends on whether the contract is physically or cash-settled.

```
Bank Of Toronto (protection buyer)
        received a large payout on a bond it may never
        have even held — this is the entire economic
        purpose of buying credit protection
```

## 12. Who Decides a Credit Event Occurred?

Not a government regulator — the **ISDA Determinations Committee**, an industry self-governance body, rules on whether a Credit Event has genuinely occurred. This is the single most consequential decision in a CDS's entire lifecycle, and doesn't fit the usual "acronym + jurisdiction" regulatory pattern every other product in this series follows ([Module 9 §8](../docs/1-capital-markets/module-09-credit.md)).

## 13. Regulatory Reporting

A conceptual EMIR/Dodd-Frank record would contain:

- UTI
- UPI ("5Y USD Single-Name CDS, Northstar Resources Inc.")
- Reporting Counterparty LEI / Other Counterparty LEI
- **Reference Entity LEI** (Northstar Resources Inc. — a field unique to Credit products)
- Notional: USD 10,000,000
- Running Coupon: 100bps
- Upfront Payment: USD 675,000
- Valuation Amount (Section 9's MTM)
- Action Type: **Credit Event** (once triggered, Section 11)

Note the US jurisdictional split: broad-based CDS Indices are regulated by the **CFTC** as "swaps," while single-name CDS like this one are "security-based swaps," regulated instead by the **SEC** ([Module 9 §8](../docs/1-capital-markets/module-09-credit.md)) — the first genuine US regulator fork in this curriculum.

## 14. Identifiers Recap

| Identifier | Applies to CDS? |
|---|---|
| ISIN (of the CDS itself) | **No** |
| ISIN (of the Reference Obligation) | **Yes** — the Northstar Resources Inc. 6% Bond 2031 has its own real ISIN |
| **UPI** | **Yes** — "5Y USD Single-Name CDS, Northstar Resources Inc." |
| **UTI** | **Yes** — fresh per trade |
| LEI (Counterparty) | Yes — Bank Of Toronto, Goldman Sachs |
| **LEI (Reference Entity)** | Yes — Northstar Resources Inc., a genuinely new LEI role |

## 15. Cash Bond vs CDS — Side by Side

| Cash / Derivative | Aspect | Northstar Bond (cash) | CDS on Northstar (derivative) |
|---|---|---|---|
| CASH | What you own | A real claim on Northstar's cash flows | Nothing — just protection against its default |
| CASH | Downside | Lose principal if Northstar defaults | Lose only the premiums paid, if no default |
| CASH | Upside on default | You lose money | You receive a large payout |
| CASH | Identifier | ISIN | UPI + UTI + Reference Entity LEI |

## 16. The Three Most Important CDS Concepts

### 1. It's contingent, not certain

Unlike an IRS or FX Forward, the big cash flow (the payout) may **never** happen.

### 2. Two legs, asymmetric in nature

```
Premium Leg   : small, certain, periodic
Protection Leg: large, uncertain, contingent
```

### 3. Widening spreads help the protection buyer

```
Reference Entity looks riskier → CDS becomes more valuable to the protection BUYER
```

## 17. Mental Model Recap

```
Credit/default risk → CDS → Credit protection, insurance-like risk transfer
```

Just as an IRS transforms floating interest rate risk into fixed, and an FX Forward transforms future FX uncertainty into a locked rate, a CDS transforms default risk into a fixed, budgetable insurance premium.
