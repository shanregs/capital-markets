# Cross-Currency Swap — Complete CAD/USD Example

*Continuing the same institutional style used in the IRS, FX Option, FRA, Cap, Floor, Collar, and Swaption lessons: real institutions (RBC, TD, BMO, CIBC, Scotiabank, JPMorgan, Goldman Sachs, etc.) are referenced for context, while **Canada Bank of Toronto (CBT)** remains the fictional primary dealer used to build the worked example. CORRA — administered and published by the Bank of Canada — is used as the CAD reference rate.*

## 1. What Is a Cross-Currency Swap?

A **Cross-Currency Swap (CCS)** exchanges both **principal** and **periodic interest payments** in two different currencies, over a multi-year term. It looks like an [IRS](./Interest_Rate_Swap_IRS_Complete_Example.md) at first glance — periodic exchanges of interest — but adds a genuinely new feature: the **notional is physically exchanged**, in two currencies, both at the **start** of the trade and again at **maturity**, at the **same original exchange rate** both times.

```
CROSS-CURRENCY SWAP
 │
 +-- Notional EXCHANGED at inception (both currencies, real cash)
 +-- Periodic interest payments exchanged throughout the term
 +-- Notional RE-EXCHANGED at maturity, at the ORIGINAL rate
     (not the then-current spot rate)
```

> **Classification callout:** despite involving two currencies, a Cross-Currency Swap is classified under **Interest Rate**, not Foreign Exchange, in the ISDA taxonomy — because its periodic-exchange mechanic is fundamentally a Rates-family Swap, not an FX-family product. [Module 6 §2](../docs/1-capital-markets/module-06-fx.md) flags this explicitly as one of the easiest classification mistakes to make in this entire curriculum.

## 2. Business Scenario

Suppose:

- ABC Mining Corporation is Canadian, with CAD as its functional currency, but its newly-won project (the one financed via the [Swaption example](./Swaption_Complete_Example.md)'s IRS) requires paying a **US-based equipment supplier USD 50,000,000** upfront, then servicing that cost over 5 years.
- Rather than borrowing directly in USD (a market where ABC has no existing banking relationships or credit lines), ABC borrows in **CAD**, its natural currency, and enters a **Cross-Currency Swap** with CBT to convert that CAD borrowing into the USD it actually needs.
- At the same time, ABC wants its ongoing interest payments to be in CAD (matching its own revenue currency), while paying down the USD side's economics through CBT.

## 3. The Participants

```
                CROSS-CURRENCY SWAP
                        │
        ┌───────────────┴───────────────┐
        │                               │
       ABC                             CBT
 Canadian Corp.                 Canada Bank of Toronto
        │                               │
        │  Pays CAD interest (floating) │
        │  Receives USD interest (fixed)│
        │  Exchanges CAD/USD principal  │
        │  at inception AND maturity    │
        └───────────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation | CCS counterparty — receives USD principal upfront, pays CAD interest |
| Canada Bank of Toronto | CCS counterparty — receives CAD principal upfront, pays USD interest |
| CBT Rates & FX Trading Desks | Jointly price and hedge the swap — a genuinely cross-desk product |
| CBT Risk Management | Monitors both interest rate risk (both currencies) and FX risk on the notional exchange |
| CBT Operations | Confirmation, periodic settlement (two currencies), and final notional re-exchange |
| CBT Regulatory Reporting | Regulatory reporting — classified as an Interest Rate product |
| External market | Provides CORRA, SOFR (USD reference rate), the CAD/USD basis spread, and spot FX |

## 4. The Trade

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | CCS-20260905-00001 |
| Product | Cross-Currency Swap |
| Notional (CAD leg) | CAD 67,500,000 |
| Notional (USD leg) | USD 50,000,000 |
| Initial Exchange Rate | 1.3500 (CAD per USD) |
| ABC pays | Floating CORRA (on the CAD leg) |
| ABC receives | Fixed 4.20% (on the USD leg) |
| CBT pays | Fixed 4.20% (on the USD leg) |
| CBT receives | Floating CORRA (on the CAD leg) |
| Term | 5 years |
| Payment Frequency | Quarterly |
| Trade Date | 05-Sep-2026 |
| Effective Date | 08-Sep-2026 |
| Maturity Date | 08-Sep-2031 |

The key economic statement is:

> **On 08-Sep-2026, ABC gives CBT CAD 67,500,000 and receives USD 50,000,000 in return. For the next 5 years, ABC pays CORRA-based interest on the CAD amount and receives fixed 4.20% interest on the USD amount. On 08-Sep-2031, the ENTIRE principal amounts are exchanged back — CAD 67,500,000 back to CBT, USD 50,000,000 back to ABC — at the SAME 1.3500 rate used at inception.**

## 5. Exchange of Notional at Inception — A Real Cash Event

```
ABC                                            CBT
 │                                              │
 │  pays CAD 67,500,000                         │
 │--------------------------------------------->│
 │                                              │
 │<---------------------------------------------│
 │           receives USD 50,000,000            │
 │                                              │
   ABC now has the USD it needs for its supplier,
   funded through a CAD borrowing it's comfortable with
```

This is the single biggest structural difference from an IRS: **the notional genuinely moves**, just like a [cash equity trade](./Cash_Equity_Trade_Complete_Example.md) or an [FX Spot trade](./FX_Spot_Complete_Example.md), not just a reference number for calculating interest.

## 6. Quarterly Interest Exchange — Both Currencies, Every Period

Suppose for the first quarterly period, compounded 3-month CORRA fixes at **4.80%**.

```
ABC's CAD leg (floating, pays CBT):
  67,500,000 × 4.80% × 90/365
  ≈ CAD 799,726

CBT's USD leg (fixed, pays ABC):
  50,000,000 × 4.20% × 90/360   (USD convention typically Act/360)
  ≈ USD 525,000
```

Unlike an IRS, these two legs are in **different currencies** — they are **not netted against each other** the way the [IRS example §8](./Interest_Rate_Swap_IRS_Complete_Example.md)'s same-currency fixed and floating legs are. Both payments settle in full, gross, each in their own currency:

```
ABC ──────── CAD 799,726 ────────► CBT
CBT ──────── USD 525,000 ────────► ABC
```

## 7. Why the Legs Aren't Netted

```
Same currency (IRS):        Fixed CAD ↔ Floating CAD  → NET, one payment
Different currencies (CCS): Floating CAD ↔ Fixed USD  → GROSS, two separate payments,
                                                          typically via CLS PvP if eligible
```

Because CAD and USD are different currencies, there's no single number to "net" them into — each leg settles independently, exactly like the [FX Spot example §6](./FX_Spot_Complete_Example.md)'s PvP settlement mechanic, just recurring every quarter instead of happening once.

## 8. Final Exchange of Notional at Maturity — At the ORIGINAL Rate

On 08-Sep-2031, the notional amounts are exchanged back — but critically, **at the original 1.3500 rate agreed in 2026, not at whatever the spot rate happens to be on that date**:

```
Suppose spot USD/CAD on 08-Sep-2031 has moved to 1.4200 (USD stronger)

Re-exchange STILL happens at the ORIGINAL rate:
  ABC pays back USD 50,000,000
  CBT pays back CAD 67,500,000

NOT at the current spot rate — if it were, ABC would owe:
  50,000,000 × 1.4200 = CAD 71,000,000  (CAD 3,500,000 MORE)
```

This is precisely why a Cross-Currency Swap is a genuinely different risk profile from a plain [FX Forward](./FX_Forward_Complete_Example.md): the FX rate risk on the **notional itself** is fixed at inception for the full 5-year term, while only the ongoing *interest* legs carry floating-rate risk (on the CAD side, in this example).

## 9. The Full Cash Flow Timeline

```
Inception (08-Sep-2026):
  ABC  → CAD 67,500,000 → CBT
  CBT  → USD 50,000,000 → ABC

Every quarter for 5 years (20 payment dates):
  ABC  → Floating CORRA interest on CAD 67,500,000 → CBT
  CBT  → Fixed 4.20% interest on USD 50,000,000    → ABC

Maturity (08-Sep-2031):
  ABC  → USD 50,000,000 → CBT   (re-exchange, ORIGINAL rate)
  CBT  → CAD 67,500,000 → ABC   (re-exchange, ORIGINAL rate)
```

## 10. Valuation and MTM

A Cross-Currency Swap's valuation combines **three** things an IRS alone never needs together: two separate discount curves (one per currency) and the FX rate itself, since the notional exchange legs have real, discountable cash flow value.

```
PV(CAD leg, in CAD)     — discounted off the CAD (CORRA) curve
          +
PV(USD leg, in USD)     — discounted off the USD (SOFR) curve
          +
PV(final notional exchange, both currencies)
          ↓
Convert one side to the other's currency at the CURRENT spot rate
          ↓
        Net MTM (in one chosen currency, e.g. CAD)
```

```
CAD rates ↑ relative to USD rates  → generally favors the CAD payer (ABC)
Spot USD/CAD moves                 → directly changes the MTM, because the
                                       notional re-exchange is a REAL future
                                       cash flow, not just a reference number
```

This is why a Cross-Currency Swap desk sits at the intersection of the Rates desk and the FX desk — its valuation genuinely needs both curve families plus an FX conversion, unlike any single-currency product in this series.

## 11. What Data Does the Valuation Engine Need?

- Trade ID
- Notional (both currencies) and Initial Exchange Rate
- Fixed Rate (USD leg) / Floating Index (CAD leg)
- Trade Date, Effective Date, Maturity Date
- Payment Frequency (both legs — sometimes different)
- Day Count Conventions (both legs — often different, e.g. Act/365 CAD vs Act/360 USD)
- CAD Discount Curve (CORRA-based)
- USD Discount Curve (SOFR-based)
- Cross-Currency Basis Spread (the CAD/USD-specific adjustment reflecting relative funding costs)
- Current Spot FX Rate

## 12. Collateral — Now With an FX Dimension

```
MTM (in CAD, after FX conversion)
        ↓
   Exposure
        ↓
   CSA terms (which currency is collateral posted in?)
        ↓
   Margin calculation
        ↓
Collateral call — itself sometimes subject to FX conversion
                  if the CSA's collateral currency differs
                  from the trade's valuation currency
```

## 13. The Cross-Currency Swap Trade Lifecycle

```
Trade Capture
      ↓
Confirmation
      ↓
Enrichment (LEI, UTI, UPI)
      ↓
Static Data
      ↓
Market Data (CAD curve, USD curve, CCS basis spread, spot FX)
      ↓
Initial Notional Exchange (Settlement — real cash, both currencies)
      ↓
Valuation
      ↓
MTM
      ↓
Risk / Exposure (rates AND FX)
      ↓
Collateral
      ↓
[Repeat quarterly for 5 years:]
   Interest Calculation (both legs, gross) → Settlement (both currencies)
      ↓
Final Notional Re-Exchange (Settlement — at ORIGINAL rate)
      ↓
Regulatory Reporting
```

## 14. Trade Capture

| Field | Value |
|---|---|
| Trade ID | CCS-20260905-00001 |
| Product | Cross-Currency Swap |
| Notional CAD | 67,500,000 |
| Notional USD | 50,000,000 |
| Initial FX Rate | 1.3500 |
| ABC pays | Floating CORRA (CAD) |
| ABC receives | Fixed 4.20% (USD) |
| Trade Date | 05-Sep-2026 |
| Effective Date | 08-Sep-2026 |
| Maturity Date | 08-Sep-2031 |

## 15. Confirmation

| Field | Value |
|---|---|
| Party A | ABC Mining Corporation |
| Party B | Canada Bank of Toronto |
| Party A pays | CORRA floating on CAD 67,500,000 |
| Party B pays | Fixed 4.20% on USD 50,000,000 |
| Initial Exchange | CAD 67,500,000 ↔ USD 50,000,000 |
| Final Exchange | Same amounts, same rate, at maturity |

## 16. Regulatory Reporting

A conceptual regulatory record could contain:

- UTI
- UPI
- Reporting Counterparty LEI / Other Counterparty LEI
- Asset Class: **Interest Rate** (not Foreign Exchange — Section 1's classification callout)
- Notional (both currencies), Initial Exchange Rate
- Fixed Rate / Floating Rate Index (both legs)
- Trade Date / Effective Date / Maturity Date
- Payment Frequency (both legs)
- Clearing Status
- Collateralisation
- Valuation Amount, Valuation Currency
- Action Type / Event Type (including the Initial and Final Exchange as distinct settlement events)
- Reporting Timestamp

## 17. Cross-Currency Swap vs IRS vs FX Forward — Where the Distinction Becomes Clear

| Aspect | IRS | FX Forward | Cross-Currency Swap |
|---|---|---|---|
| Currencies | One | Two | Two |
| Notional settles? | Never | Once, at maturity | **Twice** — at inception AND maturity |
| Interest exchanged? | Yes, every period, netted | No | Yes, every period, **gross** (not netted) |
| ISDA Asset Class | Interest Rate | Foreign Exchange | **Interest Rate** (not FX!) |
| Discount curves needed | One | One (implicitly, via forward points) | **Two**, plus a cross-currency basis spread |
| FX rate risk on notional | None | Full, on one future date | **None on the notional** (fixed at the original rate); only ongoing interest legs carry rate risk |

```
IRS:   1 currency,  notional never moves,        interest netted
FX FWD: 2 currencies, notional moves ONCE,         no interest legs
CCS:   2 currencies, notional moves TWICE,         interest moves every period, gross
```

### Mental Model

```
Need to convert a borrowing from one currency to another,
for a MULTI-YEAR term, with ongoing interest in both
        ↓
   CROSS-CURRENCY SWAP
        ↓
Real principal exchanged twice, real interest exchanged
every period, FX rate on the principal locked for the
whole term
```

A Cross-Currency Swap is what you get when you take an IRS's periodic-exchange mechanic and an FX Forward's genuine two-currency settlement, and combine them into one instrument that does both, repeatedly, across many years.

## Next Lesson: Credit Default Swap (CDS)

The natural final step in this progression:

```
Cross-Currency Swap → CDS
```

A fully worked CDS example — Reference Entity, Running Coupon, Upfront Payment, and a Credit Event payout — already exists in this same examples folder: see [Credit Default Swap (CDS) — Complete Example](./Credit_Default_Swap_CDS_Complete_Example.md). Everything from Sections 1 (Fixed vs Floating exchange mechanics), 9-10 (Valuation and MTM), and 16 (side-by-side comparison against a cash bond) in that lesson completes the same FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS progression this chain of lessons has been building toward.
