# Interest Rate Swap (IRS) — Complete Example

## 1. What is an IRS?

An Interest Rate Swap is an OTC derivative where two parties agree to exchange interest cash flows based on a notional amount.

The most common structure is:

> **Fixed Interest ↔ Floating Interest**

The important point is:

- The notional principal is normally **NOT** exchanged.
- Only the interest cash flows are exchanged/netted.

## 2. Simple Example

Suppose:

- ABC Corporation has a ₹100 crore floating-rate loan.
- Its loan interest is: **CORRA + 1.00%**
- ABC is worried that CORRA might increase.
- So ABC enters an IRS with Bank B.

### Swap terms

| Term | Value |
|---|---|
| Notional | ₹100 crore |
| Trade Date | 30-Aug-2026 |
| Effective Date | 01-Sep-2026 |
| Maturity | 01-Sep-2029 |
| ABC pays | Fixed 6.00% |
| ABC receives | Floating CORRA |
| Payment frequency | Quarterly |

The IRS is OTC.

## 3. Why does ABC enter the swap?

ABC has:

```
Loan
  |
  +-- Pay CORRA
  +-- Pay 1%
```

ABC wants to eliminate the uncertainty associated with CORRA.

So it enters:

```
IRS
  |
  +-- Pay 6% fixed
  +-- Receive CORRA
```

Combine them:

```
LOAN
Pay CORRA + 1%
       +
IRS
Pay 6%
Receive CORRA
       ↓
CORRA cancels
       ↓
Effective cost ≈ 6% + 1%
       ↓
≈ 7% fixed
```

This is the basic economic purpose of an IRS.

## 4. The Two Legs

Every standard IRS has two legs.

**Fixed Leg** — ABC pays: `6.00%`

**Floating Leg** — ABC receives: `CORRA`

```
             IRS
              |
       +------+------+
       |             |
   Fixed Leg     Floating Leg
       |             |
    Pay 6%        Receive CORRA
```

## 5. What is Notional?

Suppose:

> Notional = ₹100 crore

This does **not** normally mean ABC gives the bank ₹100 crore. It is a reference amount used to calculate interest.

For example:

```
₹100 crore × 6% = ₹6 crore annual fixed interest
```

If payments are quarterly, the actual calculation depends on the accrual fraction.

## 6. First Quarterly Period

Suppose the first accrual period is:

> 01-Sep-2026 → 01-Dec-2026

Assume:

| Parameter | Value |
|---|---|
| Notional | ₹100 crore |
| Fixed rate | 6% |
| Accrual factor | 90/365 |

**Fixed-leg interest:**

```
₹100 crore × 6% × 90/365 ≈ ₹1.479 crore
```

So ABC owes approximately **₹1.479 crore** on the fixed leg.

## 7. Floating CORRA Leg

Now suppose the applicable compounded CORRA rate for the period is: **5.20%**

Simplified calculation:

```
₹100 crore × 5.20% × 90/365 ≈ ₹1.282 crore
```

ABC receives approximately **₹1.282 crore**.

## 8. Net Settlement

Instead of physically exchanging both amounts, the parties commonly net the two payments.

| Item | Amount |
|---|---|
| Fixed payment | ₹1.479 crore |
| Floating receipt | ₹1.282 crore |
| **Net payment** | **₹0.197 crore** |

Therefore ABC pays approximately **₹19.7 lakh** for this simplified period.

## 9. Now Combine It With the Loan

Remember ABC's loan:

```
Loan interest = CORRA + 1%
```

Suppose CORRA = 5.2%.

**Loan cost:**
```
5.2% + 1% = 6.2%
```

ABC also has the swap:

```
Pay 6%
Receive 5.2%
```

**Economic combination:**

```
Loan
Pay      5.2% + 1%

Swap
Pay      6%
Receive  5.2%

--------------------
5.2% cancels
Effective ≈ 7%
```

So ABC has transformed its floating-rate exposure into approximately **7% fixed**, subject to the precise loan and swap conventions.

## 10. What if CORRA Rises?

Suppose CORRA increases to **8%**.

**Loan:**
```
Pay: 8% + 1% = 9%
```

**IRS:**
```
Pay fixed: 6%
Receive floating: 8%
```

**Net swap:**
```
Receive 8%
Pay 6%
= +2%
```

**Combined:**

```
Loan       -9%
IRS        +2%
----------------
Net        -7%
```

ABC is still approximately at **7%**.

## 11. What if CORRA Falls?

Suppose CORRA falls to **4%**.

**Loan:**
```
Pay: 4% + 1% = 5%
```

**IRS:**
```
Pay 6%
Receive 4%
```

**Net:**
```
5% + 6% - 4% = 7%
```

Again: **approximately 7%**.

This is why the swap is useful for hedging.

## 12. But There Is an Important Trade-Off

ABC has exchanged uncertainty for certainty.

**Without the swap:**

| CORRA | Loan |
|---|---|
| 3% | 4% |
| 8% | 9% |

**With the swap:** Approximately **7%**

So if CORRA falls dramatically, ABC doesn't get the full benefit.

> **This is the fundamental trade-off:** Hedging reduces uncertainty, but it can also reduce the benefit of favorable market movements.

## 13. What Happens to the IRS Every Day?

The trade doesn't remain worth zero. Every day the market changes.

Suppose ABC entered:

```
Pay fixed = 6%
Receive CORRA
```

Later, market participants would only enter a new IRS at **7%**.

ABC's existing swap is attractive because ABC is paying only **6%**. Therefore ABC's swap has **positive value**.

Conversely, if market swap rates fall to **5%**, ABC's existing obligation to pay 6% is unfavorable.

Therefore:

```
Market rates ↑
      ↓
Pay-fixed IRS becomes more valuable

Market rates ↓
      ↓
Pay-fixed IRS becomes less valuable
```

## 14. MTM

This brings us to **Mark-to-Market**.

Suppose the valuation system determines:

> IRS Fair Value = **+₹4.5 crore** (from ABC's perspective)

That means: if the position were valued/closed under the applicable valuation framework, it has approximately ₹4.5 crore of positive economic value to ABC.

From Bank B's perspective:

> MTM = **-₹4.5 crore**

*(Ignoring valuation adjustments and bilateral considerations for simplicity.)*

## 15. How is IRS MTM Calculated?

This is where things become more interesting. The valuation engine forecasts future cash flows.

```
Future Fixed Cash Flows
          +
Future Floating Cash Flows
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

Conceptually:

```
PV = Σ Future Cash Flow × Discount Factor
```

For an IRS:

```
PV(IRS) = PV(Floating Leg) − PV(Fixed Leg)
```

The sign depends on whether we are looking from the payer or receiver perspective.

## 16. What Data Does the Valuation Engine Need?

For example:

- Trade ID
- Notional
- Fixed Rate
- Floating Index
- Trade Date
- Effective Date
- Maturity Date
- Payment Frequency
- Reset Frequency
- Day Count Convention
- Business Day Convention
- Holiday Calendar
- Payment Dates
- Reset Dates
- Market Curve
- Discount Curve
- CORRA Forward Curve
- Discount Factors

This is where trade data + market data come together.

## 17. The IRS Trade Lifecycle

Now connect this with the FX Forward lifecycle mentioned earlier.

```
Trade Capture
      ↓
Confirmation
      ↓
Enrichment
      ↓
Static Data
      ↓
Market Data
      ↓
Valuation
      ↓
MTM
      ↓
Risk / Exposure
      ↓
Collateral
      ↓
Interest Calculation
      ↓
Settlement
      ↓
Regulatory Reporting
```

## 18. Trade Capture

Example:

| Field | Value |
|---|---|
| Trade ID | IRS-20260830-00001 |
| Product | IRS |
| Notional | ₹100 crore |
| Currency | CAD |
| Fixed Rate | 6% |
| Floating Index | CORRA |
| Direction | Pay Fixed |
| Trade Date | 30-Aug-2026 |
| Effective Date | 01-Sep-2026 |
| Maturity Date | 01-Sep-2029 |
| Frequency | Quarterly |

## 19. Confirmation

The confirmation establishes the contractual terms.

For example:

| Field | Value |
|---|---|
| Party A | ABC Corporation |
| Party B | Bank B |
| Party A | Pay Fixed 6% |
| Party B | Pay Floating CORRA |
| Notional | ₹100 crore |
| Maturity | 01-Sep-2029 |

## 20. Enrichment

Additional data can be added:

- ABC LEI
- Bank LEI
- UTI
- UPI
- Legal Entity
- Jurisdiction
- Trading Desk
- Book
- Counterparty Classification
- Collateral Agreement
- Clearing Status
- Reporting Obligation

This is where a regulatory data platform becomes important.

## 21. Collateral

Suppose the IRS has:

> MTM = +₹4.5 crore

The CSA between the parties determines how much collateral is required.

Conceptually:

```
MTM
 ↓
Exposure
 ↓
CSA terms
 ↓
Margin calculation
 ↓
Collateral call
```

Collateral may include:

- Cash
- Government securities
- Other eligible assets

...depending on the agreement.

## 22. Settlement

Suppose for one quarter:

| Item | Amount |
|---|---|
| Fixed amount | ₹1.479 crore |
| Floating amount | ₹1.282 crore |
| **Net** | **₹19.7 lakh** |

The settlement system receives an instruction:

| Field | Value |
|---|---|
| Trade ID | IRS-20260830-00001 |
| Payment Date | 01-Dec-2026 |
| Currency | CAD |
| Net Amount | ₹19.7 lakh |
| Direction | ABC → Bank B |
| Status | PENDING |

After payment:

> Settlement Status = **SETTLED**

## 23. Regulatory Reporting

The IRS may need to be reported under the applicable jurisdiction/regime.

A conceptual regulatory record could contain:

- UTI
- UPI
- Reporting Counterparty LEI
- Other Counterparty LEI
- Asset Class
- Product Type
- Notional
- Notional Currency
- Fixed Rate
- Floating Rate Index
- Trade Date
- Effective Date
- Maturity Date
- Payment Frequency
- Reset Frequency
- Clearing Status
- Collateralisation
- Valuation Amount
- Valuation Currency
- Action Type
- Event Type
- Reporting Timestamp

The exact fields and values depend on the relevant reporting regime.

## 24. Why IRS Is More Complicated Than FX Forward

Compare them.

**FX Forward** — One future exchange

```
USD ↔ INR
```

**IRS** — Multiple future periods

```
Period 1: Fixed ↔ Floating
Period 2: Fixed ↔ Floating
Period 3: Fixed ↔ Floating
...
Period N: Fixed ↔ Floating
```

Therefore IRS introduces:

- Reset dates
- Payment dates
- Accrual periods
- Day-count conventions
- Business-day adjustments
- Reference-rate observations
- Compounding
- Forward curves
- Discount curves
- Multiple cash flows

## 25. CORRA Makes It Even More Interesting

For a CORRA-based IRS, the floating rate may be determined using daily CORRA observations over an accrual period.

Conceptually:

```
Day 1 → CORRA
Day 2 → CORRA
Day 3 → CORRA
...
Day 90 → CORRA
          |
          v
   Compounded rate
          |
          v
Floating-leg interest
```

So you should not simply average CORRA percentages unless the specific contract says so.

A compounded-in-arrears structure typically compounds the daily rates using the applicable methodology.

## 26. The Three Most Important IRS Concepts

If you remember only three things:

### 1. Notional

Used to calculate interest. ₹100 crore usually isn't exchanged.

### 2. Two legs

```
Fixed Leg
   ↕
Floating Leg
```

### 3. Cash flows

The valuation engine forecasts and discounts the future cash flows.

```
Future Cash Flows
       ↓
Discounting
       ↓
PV / MTM
```

## 27. IRS vs FRA vs Cap

This distinction is extremely important.

| Product | What it does |
|---|---|
| FRA | Locks a future interest rate for one period |
| IRS | Exchanges fixed and floating rates over multiple periods |
| Cap | Protects against rates rising above a level |
| Floor | Protects against rates falling below a level |
| Collar | Combines cap and floor |
| Swaption | Option to enter an IRS |

Think:

```
FRA      → One future rate period
IRS      → Many future rate periods
CAP      → Maximum rate protection
SWAPTION → Option to enter IRS
```

## 28. A Very Important Capital Markets Mental Model

You can now start thinking about derivatives in terms of risk transformation.

**Forward**
```
Uncertain future price → Forward → Locked price
```

**Option**
```
Uncertain future price → Option → Protection + flexibility
```

**IRS**
```
Floating interest risk → IRS → Fixed interest exposure
```

**FX Forward**
```
Future FX uncertainty → FX Forward → Locked FX rate
```

**CDS**
```
Credit/default risk → CDS → Credit protection
```

This is one of the most useful ways to understand the entire derivatives universe.
