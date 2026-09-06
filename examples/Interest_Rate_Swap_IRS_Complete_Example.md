# Interest Rate Swap (IRS) — Complete Example

*Continuing the same institutional style used in the FRA, Cap, Floor, Collar, Swaption, and Cross-Currency Swap lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional dealer and **ABC Mining Corporation** the fictional corporate client used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What is an IRS?

An Interest Rate Swap is an OTC derivative where two parties agree to exchange interest cash flows based on a notional amount.

The most common structure is:

> **Fixed Interest ↔ Floating Interest**

The important point is:

- The notional principal is normally **NOT** exchanged.
- Only the interest cash flows are exchanged/netted.
- An IRS is not a loan and creates no new borrowing — it only swaps one interest characteristic for another on a notional reference amount.

## 2. Simple Example — and the Debt Already Existed

Suppose:

- **ABC Mining Corporation already has an existing CAD 50,000,000 floating-rate term loan** — drawn down on 01-Sep-2023, a 6-year facility maturing 01-Sep-2029, borrowed from its lending syndicate to fund a mine expansion.
- The loan's interest is: **CORRA + 1.00%**, reset and paid quarterly.
- **This debt exists before, and independently of, any swap.** By the time this example starts, ABC has already been paying floating interest on the loan for three years.
- By 30-Aug-2026 — with three years still left on the loan — ABC is worried CORRA will keep rising and push its borrowing cost up for the remainder of the loan's life.
- So ABC enters a **separate transaction**: an IRS with **Canada Bank of Toronto (CBT)**, to hedge the interest-rate risk on the loan's *remaining* three years.

> **Timeline check, answering the obvious question directly: yes, ABC is already in debt before the swap exists.** The loan is the debt — drawn down in 2023, three years before this swap is even discussed. The IRS entered in 2026 creates **no new principal, no new borrowing**. It is a second, independent contract layered on top of an already-existing loan, whose only job is to exchange the *interest* cash flows on a matching notional — floating for fixed — for the loan's remaining term.

### Swap terms

| Term | Value |
|---|---|
| Notional | CAD 50,000,000 |
| Trade Date | 30-Aug-2026 |
| Effective Date | 01-Sep-2026 |
| Maturity | 01-Sep-2029 (matches the loan's remaining term exactly) |
| ABC pays | Fixed 4.00% |
| ABC receives | Floating CORRA |
| Payment frequency | Quarterly |

The IRS is OTC, and the notional CAD 50,000,000 is chosen to match the loan's outstanding principal — but the two contracts (loan and swap) remain legally separate.

## 3. Why does ABC enter the swap?

ABC already has, from 2023:

```
LOAN (already exists, drawn down 01-Sep-2023)
  |
  +-- Pay CORRA
  +-- Pay 1%
```

ABC wants to eliminate the uncertainty associated with CORRA for the loan's remaining three years.

So, separately, in 2026 it enters:

```
IRS (new contract, Trade Date 30-Aug-2026)
  |
  +-- Pay 4% fixed
  +-- Receive CORRA
```

Combine them:

```
LOAN (pre-existing debt)
Pay CORRA + 1%
       +
IRS (new hedge, no new debt)
Pay 4%
Receive CORRA
       ↓
CORRA cancels
       ↓
Effective cost = 4% (fixed) + 1% (loan margin)
       ↓
= 5% fixed, for as long as both contracts run
```

This is the basic economic purpose of an IRS: it doesn't touch the loan itself, but it converts the *interest rate character* of an already-existing debt from floating to fixed.

## 4. The Two Legs

Every standard IRS has two legs.

**Fixed Leg** — ABC pays: `4.00%`

**Floating Leg** — ABC receives: `CORRA`

```
             IRS
              |
       +------+------+
       |             |
   Fixed Leg     Floating Leg
       |             |
    Pay 4%        Receive CORRA
```

## 5. What is Notional?

Suppose:

> Notional = CAD 50,000,000

This does **not** mean ABC gives CBT CAD 50,000,000 — that principal already sits with ABC's original lending syndicate, from the 2023 loan draw-down. The swap's notional is purely a reference amount used to calculate interest.

For example:

```
CAD 50,000,000 × 4.00% = CAD 2,000,000 annual fixed interest
```

If payments are quarterly, the actual calculation depends on the accrual fraction.

## 6. First Quarterly Period

Suppose the first accrual period is:

> 01-Sep-2026 → 01-Dec-2026 (91 days)

Assume:

| Parameter | Value |
|---|---|
| Notional | CAD 50,000,000 |
| Fixed rate | 4.00% |
| Accrual factor | 91/365 (actual/365) |

**Fixed-leg interest:**

```
CAD 50,000,000 × 4.00% × 91/365 = CAD 498,630.14
```

So ABC owes **CAD 498,630.14** on the fixed leg.

## 7. Floating CORRA Leg

Now suppose the applicable compounded CORRA rate for the period is: **3.20%**

```
CAD 50,000,000 × 3.20% × 91/365 = CAD 398,904.11
```

ABC receives **CAD 398,904.11**.

## 8. Net Settlement

Instead of physically exchanging both amounts, the parties commonly net the two payments.

| Item | Amount |
|---|---|
| Fixed payment | CAD 498,630.14 |
| Floating receipt | CAD 398,904.11 |
| **Net payment** | **CAD 99,726.03** |

Therefore ABC pays **CAD 99,726.03** to CBT for this quarter.

## 9. Now Combine It With the Loan

Remember ABC's (pre-existing) loan:

```
Loan interest = CORRA + 1%
```

Suppose CORRA = 3.20% for this quarter, same as Section 7.

**Loan cost (paid to the lending syndicate, not CBT):**

```
CAD 50,000,000 × (3.20% + 1.00%) × 91/365 = CAD 523,561.64
```

ABC also has the swap with CBT (Section 8):

```
Pay 4.00% fixed   = CAD 498,630.14
Receive floating  = CAD 398,904.11
Net paid to CBT   = CAD 99,726.03
```

**Economic combination for the quarter:**

```
Loan (to syndicate)     -523,561.64
Swap net (to CBT)        -99,726.03
--------------------------------------
Total outflow            -623,287.67

CAD 623,287.67 / (CAD 50,000,000 x 91/365) = 5.00% annualized
```

So ABC has transformed its floating-rate exposure into exactly **5.00% fixed** (its 4.00% swap rate + the loan's 1.00% margin) for as long as the loan and swap both run — CORRA cancels out completely, regardless of where it actually settles.

## 10. What if CORRA Rises?

Suppose CORRA rises to **6.00%** in a later quarter.

**Loan (to syndicate):**
```
CAD 50,000,000 × (6.00% + 1.00%) × 91/365 = CAD 872,602.74
```

**IRS (with CBT):**
```
Pay fixed 4.00%       = CAD 498,630.14
Receive floating 6.00% = CAD 747,945.21
Net received from CBT  = CAD 249,315.07
```

**Combined:**

```
Loan            -872,602.74
Swap net        +249,315.07
----------------------------
Net             -623,287.67   (= 5.00% annualized, same as before)
```

ABC is still exactly at **5.00%**, even though CORRA moved from 3.20% to 6.00%.

## 11. What if CORRA Falls?

Suppose CORRA falls to **1.50%** in a later quarter.

**Loan (to syndicate):**
```
CAD 50,000,000 × (1.50% + 1.00%) × 91/365 = CAD 311,643.84
```

**IRS (with CBT):**
```
Pay fixed 4.00%        = CAD 498,630.14
Receive floating 1.50%  = CAD 186,986.30
Net paid to CBT         = CAD 311,643.84
```

**Combined:**

```
Loan            -311,643.84
Swap net        -311,643.84
----------------------------
Net             -623,287.67   (= 5.00% annualized, again)
```

Again: **exactly 5.00%**. This is why the swap is useful for hedging — the all-in cost stops moving with CORRA entirely.

## 12. But There Is an Important Trade-Off

ABC has exchanged uncertainty for certainty.

**Without the swap** (loan-only, floating):

| CORRA | Loan cost (annualized) |
|---|---|
| 1.50% | 2.50% |
| 6.00% | 7.00% |

**With the swap:** always **5.00%**, regardless of where CORRA lands.

So if CORRA falls dramatically (say, to 1.50%, where the loan-only cost would have been just 2.50%), ABC doesn't get the benefit — it's still locked into 5.00%.

> **This is the fundamental trade-off:** hedging reduces uncertainty, but it can also reduce the benefit of favorable market movements.

## 13. What Happens to the IRS Every Day?

The trade doesn't remain worth zero. Every day the market changes.

Recall ABC entered:

```
Pay fixed = 4.00%
Receive CORRA
```

Suppose that, a year later, market participants would only enter a new swap of similar remaining tenor at **4.75%** fixed.

ABC's existing swap is attractive because ABC is locked in paying only **4.00%**. Therefore ABC's swap has **positive value**.

Conversely, if market swap rates for that tenor fell to **3.50%**, ABC's existing obligation to pay 4.00% would be unfavorable.

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

Suppose, one year in (30-Aug-2027, with about two years of the swap remaining), the valuation system determines:

> IRS Fair Value = **+CAD 900,000** (from ABC's perspective)

That means: if the position were valued/closed under the applicable valuation framework, it has approximately CAD 900,000 of positive economic value to ABC — a direct consequence of ABC's locked-in 4.00% being cheaper than the 4.75% market rate from Section 13.

From CBT's perspective:

> MTM = **-CAD 900,000**

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
| Notional | CAD 50,000,000 |
| Currency | CAD |
| Fixed Rate | 4.00% |
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
| Party A | ABC Mining Corporation |
| Party B | Canada Bank of Toronto (CBT) |
| Party A | Pay Fixed 4.00% |
| Party B | Pay Floating CORRA |
| Notional | CAD 50,000,000 |
| Maturity | 01-Sep-2029 |

## 20. Enrichment

Additional data can be added:

- ABC Mining Corporation LEI
- CBT LEI
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

> MTM = +CAD 900,000

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

Suppose for the first quarter (Section 8's numbers):

| Item | Amount |
|---|---|
| Fixed amount | CAD 498,630.14 |
| Floating amount | CAD 398,904.11 |
| **Net** | **CAD 99,726.03** |

The settlement system receives an instruction:

| Field | Value |
|---|---|
| Trade ID | IRS-20260830-00001 |
| Payment Date | 01-Dec-2026 |
| Currency | CAD |
| Net Amount | CAD 99,726.03 |
| Direction | ABC → CBT |
| Status | PENDING |

After payment:

> Settlement Status = **SETTLED**

Note that this net CAD 99,726.03 flow is entirely separate from ABC's quarterly loan payment to its lending syndicate (Section 9) — two independent cash flows, on two independent contracts, that only add up to a stable 5.00% all-in cost when looked at together.

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
USD ↔ CAD
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
Day 91 → CORRA
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

Used to calculate interest. CAD 50,000,000 usually isn't exchanged.

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
