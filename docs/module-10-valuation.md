# Module 10 — Valuation

[← Back to Index](../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Cross-cutting module** — builds on all six Asset Class Roadmap modules (4–9) plus [Module 15](./module-15-listed-derivatives.md) (Listed Derivatives). Not organized around the eight-part asset-class pattern, since Valuation isn't itself an asset class — it's the mechanism every asset class's Position/Valuation-P&L lifecycle stage ([Module 3](./module-03-trade-lifecycle.md), and every asset-class module's Section 5) actually runs on.

---

## 1. What Is Valuation? — Price, Market Value, Fair Value, MTM, PV &amp; Exposure

**Definition:** six terms that sound interchangeable in casual conversation mean six genuinely different things on a trade record, and confusing them is the single most common valuation mistake a reporting system can make.

| Term | Meaning |
|---|---|
| Price | The per-unit quote for an instrument — CAD 68.40 for one Coca-Cola share ([Module 4](./module-04-equity.md) §4a), 98.50 for one Canada bond per 100 face ([Module 5](./module-05-fixed-income.md) §4a). Static at a point in time, not multiplied by quantity yet |
| Market Value | Price × Quantity — the total worth of a position right now, at today's Price. For Coca-Cola: 10,000 shares × current price |
| Fair Value | The accounting-standard concept of Market Value, formalized with a specific measurement hierarchy (Section 6) — the number that actually lands in financial statements |
| MTM (Mark-to-Market) | The *act* of revaluing a position to current Market/Fair Value, and by extension the resulting Unrealized P&amp;L versus cost or the prior valuation date ([Module 4](./module-04-equity.md) §5's Coca-Cola example, [Module 7](./module-07-commodities.md) §5's daily futures margin) |
| PV (Present Value) | The value *today* of a cash flow (or stream of cash flows) that will actually happen in the future, discounted back at an appropriate rate — the mechanism behind every derivative's valuation (Section 4) |
| Exposure | How much could be lost if things go wrong — NOT the same as Market Value. A CAD 100,000,000 notional IRS ([Module 8](./module-08-rates.md) §4a) might have a Market Value near zero at inception but a very real, positive Potential Future Exposure ([Module 11](./module-11-risk-exposure.md)) |

```text
WHY THESE SIX AREN'T SYNONYMS — one trade record, six different numbers
========================================================================

  Canada 5% Bond 2035 (Module 5 §4a), one week after purchase:

  Price          : 98.65                (today's per-100 quote)
  Market Value    : CAD 9,865,000        (98.65/100 x 10,000,000 face)
  Fair Value      : CAD 9,865,000        (same number, IFRS 13/ASC 820
                     framing -- Section 06 explains WHY it's the same
                     number here but often ISN'T for a derivative)
  MTM / Unrealized P&amp;L : +CAD 15,000    (Market Value today minus
                     Market Value at purchase -- Module 5 §5's exact
                     worked example)
  PV of remaining cash flows : NOT the same as Market Value for a
                     bond quoted on a clean-price convention -- see
                     Section 03's YTM/Duration deep dive
  Exposure        : depends entirely on WHO holds the risk and
                     against WHOM -- covered fully in Module 11
```

**Takeaway:** every worked example across Modules 4–9 and 15 already used at least two or three of these six terms without formally separating them. This module's whole job is to make that separation explicit, then show the actual math — YTM/Duration for Fixed Income (Section 3), DCF/NPV and option pricing for derivatives (Section 4) — that every prior module deferred to "[Module 10](./module-10-valuation.md)."

---

## 2. Two Valuation Families — Quote-Based vs Model-Based

**Definition:** every instrument in this roadmap falls into exactly one of two valuation families. **Quote-Based** instruments have an observable market price you can read directly off a screen. **Model-Based** instruments require calculating a value from inputs, because no single observable price exists for that exact contract.

```text
QUOTE-BASED  vs  MODEL-BASED
========================================================================

  QUOTE-BASED (read the price, multiply by quantity):
    Coca-Cola stock (Module 4 §4a)     -&gt; NYSE prints a price every trade
    Canada 5% Bond 2035 (Module 5 §4a) -&gt; dealers quote a Clean Price
    CME WTI Future (Module 7 §4b)      -&gt; exchange publishes a daily
                                            settlement price
    E-mini S&amp;P 500 Future (Module 15 §4a) -&gt; same mechanic

  MODEL-BASED (calculate a value from inputs, no single quote exists):
    5Y CAD IRS (Module 8 §4a)          -&gt; no exchange prints "the"
                                            price of THIS specific swap;
                                            value comes from discounting
                                            its cash flows (Section 04a)
    AAPL Call Option (Module 4 §4d)    -&gt; value comes from an option
                                            pricing model (Section 04b)
    CDS on Northstar Resources
    (Module 9 §4a)                     -&gt; value comes from the credit
                                            spread curve (Section 04c)

  -&gt; The dividing line isn't "exchange-traded vs OTC" (Module 6 §7's
     axis) -- Listed Options ARE exchange-traded (Module 15 §7) but
     still need a MODEL to price, because a specific strike/expiry
     combination may not trade often enough to have its own live quote.
```

**Takeaway:** this is the single most useful lens for predicting how hard a given instrument is to value correctly — Quote-Based instruments are a lookup; Model-Based instruments are a calculation, with every input in that calculation being a potential source of disagreement between two parties (or two systems) valuing the exact same trade.

---

## 3. Fixed Income Deep Dive — YTM and Duration/DV01, In Full

[Module 5](./module-05-fixed-income.md) §1 introduced Yield to Maturity and Duration as previews, promising the full math here. This section delivers both, using the exact Canada 5% Bond 2035 position from [Module 5](./module-05-fixed-income.md) §4a.

### Yield to Maturity — the approximate formula

```text
YIELD TO MATURITY (approximate) — Canada 5% Bond 2035
========================================================================

  Face Value (F): CAD 10,000,000
  Clean Price Value (P): CAD 9,850,000 (Price 98.50 — Module 5 §4a)
  Annual Coupon Cash Flow (C): 10,000,000 x 5% = CAD 500,000
  Years to Maturity (n): 9 (2035 - 2026)

  Approx. YTM = [ C + (F - P)/n ] / [ (F + P)/2 ]
              = [ 500,000 + (10,000,000 - 9,850,000)/9 ] / [ (10,000,000 + 9,850,000)/2 ]
              = [ 500,000 + 16,666.67 ] / 9,925,000
              = 516,666.67 / 9,925,000
              = 5.21%

  -&gt; YTM (5.21%) &gt; Coupon Rate (5.00%), exactly the discount-bond
     rule Module 1 §1 established: paying below par means the buyer's
     actual return exceeds the printed coupon rate.
```

### Modified Duration and DV01

**Definition:** [Module 5](./module-05-fixed-income.md) §1 previewed Modified Duration as "how much a bond's price moves for a 1% change in yield." Given this bond's actual 9-year, 5% semi-annual cash flow schedule at a 5.21% yield, its Modified Duration works out to approximately **7.2 years** — the full Macaulay-Duration-to-Modified-Duration derivation is a standard fixed income calculation, not reproduced step-by-step here, but the *consequence* of that number is exactly what an IT/reporting system needs to get right:

```text
DV01 (Dollar Value of 1 Basis Point) — Canada 5% Bond 2035
========================================================================

  Modified Duration: 7.2
  Market Value: CAD 9,850,000

  DV01 = Modified Duration x Market Value x 0.0001
       = 7.2 x 9,850,000 x 0.0001
       = CAD 7,092 per basis point

  SCENARIO — yields rise 25 basis points (0.25%):
    Approx. Price Impact = -Modified Duration x ΔYield x Market Value
                          = -7.2 x 0.0025 x 9,850,000
                          = -CAD 177,300

  -&gt; This is the SAME DV01 concept Module 8 §1 previewed for Rates
     products, applied here to the instrument it was originally
     defined for: bond price sensitivity. A 25bp rate move costs
     Bank Of Toronto's bond position roughly CAD 177,300 -- a real,
     quantifiable number a risk report needs, not just a directional
     "rates went up, bonds went down" statement.
```

**Takeaway:** YTM answers "what am I actually earning if I hold to maturity"; Duration/DV01 answers "how much does this position's *value* move if yields shift." Both are derived entirely from the same three inputs (Price, Coupon, Maturity) that [Module 5](./module-05-fixed-income.md) §1 already established as the bond's core fields — nothing new is needed, just the math those fields were always building toward.

---

## 4. Derivative Valuation Deep Dive — DCF, Option Pricing &amp; Credit Spreads

Three worked examples — one per Model-Based family from Section 2 — each revisiting a trade already introduced earlier in this roadmap, now shown mid-life rather than at inception.

### 4a. Interest Rate Swap — DCF/NPV Mark-to-Market

```text
5Y CAD IRS  —  Mark-to-Market, 6 Months After Inception
========================================================================

  At inception (Module 8 §4a): Bank Of Toronto pays FIXED 4.00%,
  Notional CAD 100,000,000. A fairly-priced swap has NPV = 0 at
  inception -- the fixed rate is SET so that the PV of the fixed
  leg exactly equals the PV of the expected floating leg.

  SIX MONTHS LATER: the market's quoted rate for a new 4.5-year CAD
  swap has moved from 4.00% to 4.30% -- rates rose.

  Bank Of Toronto is locked into paying 4.00% fixed, now BELOW the
  4.30% market rate for the same remaining term -- a good deal, so
  the swap now has POSITIVE value to Bank Of Toronto.

  Approx. MTM Value (using the swap's own DV01, the same concept as
  Section 03's bond DV01, just computed off the swap's cash flow
  schedule instead of a bond's):
    Swap DV01 (approx, CAD 100,000,000 notional, 4.5Y remaining):
      ≈ CAD 41,000 per basis point
    Rate moved 30 basis points (4.00% -&gt; 4.30%)
    Approx. MTM Gain = 41,000 x 30 = CAD 1,230,000 (to Bank Of Toronto)

  -&gt; A full DCF valuation discounts EVERY remaining fixed and
     floating cash flow off the current CORRA curve individually --
     the DV01 shortcut shown here is the same simplification real
     risk systems use for a quick sensitivity estimate between full
     revaluations, not a replacement for the full curve-based DCF.
```

### 4b. Equity Option — Intrinsic Value vs Time Value

```text
AAPL 15-JAN-2027 250 CALL  —  Six Months Before Expiry
========================================================================

  At inception (Module 4 §4d): Strike USD 250, Premium USD 18.50/share
  100 contracts x 100 shares/contract = 10,000 shares equivalent

  SIX MONTHS LATER: AAPL trades at USD 260, option premium now
  quoted at USD 22.00/share

  Intrinsic Value = max(Spot - Strike, 0) x Shares
                   = max(260 - 250, 0) x 10,000
                   = 10 x 10,000 = USD 100,000

  Market Value (current premium) = 22.00 x 10,000 = USD 220,000

  Time Value = Market Value - Intrinsic Value
             = 220,000 - 100,000 = USD 120,000

  -&gt; Time Value exists because of everything an option pricing
     model (the industry standard is a Black-Scholes-family model)
     actually prices in beyond today's spot-vs-strike gap: time
     remaining to expiry, volatility, the risk-free rate, and
     dividends -- the five/six inputs an option pricing model needs
     that Section 02's "Quote-Based" instruments never require at all.
```

**IT/reporting angle:** a trade record showing an option's current Market Value with no Intrinsic-Value/Time-Value split is hiding the single most useful diagnostic for whether that value looks right — Intrinsic Value is a pure arithmetic check (spot vs. strike, no model needed) that either matches a floor under the Market Value or signals a stale/wrong price feed.

### 4c. Credit Default Swap — Spread-Driven Mark-to-Market

```text
5Y CDS ON NORTHSTAR RESOURCES INC.  —  Bank Of Toronto (protection
buyer) vs Goldman Sachs (protection seller) — One Year After Inception
========================================================================

  At inception (Module 9 §4a): Notional USD 10,000,000, Running
  Coupon 100bps, market spread was 250bps, Bank Of Toronto (protection
  buyer) paid Goldman Sachs (protection seller) an upfront of approx.
  USD 675,000
    = (2.50% - 1.00%) x ~4.5 x 10,000,000

  ONE YEAR LATER: Northstar Resources Inc.'s credit has deteriorated
  further (still no formal Credit Event, Module 9 §4b) -- market
  spread has WIDENED to 400bps. Remaining Risky Duration ≈ 4.0
  (one year of the original 5 has passed)

  Current Approx. Value = (Market Spread - Running Coupon) x Risky
                            Duration x Notional
                          = (4.00% - 1.00%) x 4.0 x 10,000,000
                          = 3.00% x 4.0 x 10,000,000
                          = USD 1,200,000

  Unrealized Gain to Bank Of Toronto = 1,200,000 - 675,000 = USD 525,000

  -&gt; Bank Of Toronto's PROTECTION position gains value as Northstar
     Resources Inc.'s credit WORSENS (spread widens) -- the mirror
     image of a bond holder, who LOSES value as the same company's
     credit worsens. This is exactly why CDS makes an effective
     hedge against a bond position referencing the same Reference
     Entity (Module 9 §1) -- and exactly the "wrong-way risk"
     concept flagged in Module 9's Q4 answer, from the opposite
     side of the trade.
```

> **Callout — one snapshot among several.** This is a single illustrative valuation snapshot: Northstar Resources Inc.'s spread widening from 250bps to 400bps. [Module 11](./module-11-risk-exposure.md) §3 revalues this exact same trade under a *different* illustrative scenario — a spread that has instead NARROWED, leaving Bank Of Toronto's locked-in protection worth less than current market pricing would provide, an unrealized loss rather than a gain. The two sections aren't describing the same moment in time; they're two independent "what if the spread moves this way" scenarios applied to the same trade, each self-consistent on its own terms.

**Takeaway:** all three derivative valuations reduce to the same underlying idea — compare what was locked in at trade date against what the market offers *today* for the same remaining risk, then translate that gap into a dollar value using a sensitivity measure (Swap DV01, Option Greeks, CDS Spread DV01) that's structurally the same concept every time, just calibrated to a different underlying risk factor.

---

## 5. Cross-Asset-Class Valuation Reference

Every asset class and Listed Derivatives, side by side — the fully worked-out version of the "Compare valuation across trade types" table from this module's original curriculum outline.

| Asset Class / Product | Valuation Family (Section 02) | Core Valuation Basis | Worked Example |
|---|---|---|---|
| **Equity** (Common/Preferred) | Quote-Based | Price × Quantity | [Module 4](./module-04-equity.md) §5 — Coca-Cola daily MTM |
| **Fixed Income** (Bonds) | Quote-Based (Clean Price) + a PV concept underneath (YTM) | Clean Price × Face Value ÷ 100, plus Accrued Interest for Dirty Price | Section 03, this module |
| **FX Spot/Forward/Swap** | Quote-Based (Spot) / Model-Based (Forward points derived from rate differential) | Notional × Rate; Forward = Spot + Forward Points ([Module 6](./module-06-fx.md) §1) | [Module 6](./module-06-fx.md) §5 — daily spot MTM |
| **FX Option** | Model-Based | Option pricing model (same family as Section 04b) | [Module 6](./module-06-fx.md) §4d |
| **Commodity Physical/Spot/Forward** | Quote-Based (Spot) / Model-Based (Forward, via storage/carry cost) | Spot × Quantity; Forward = Spot + Carry ([Module 7](./module-07-commodities.md) §1) | [Module 7](./module-07-commodities.md) §4a |
| **Commodity Future** | Quote-Based | Exchange daily settlement price × contract size | [Module 7](./module-07-commodities.md) §5 — daily variation margin |
| **Commodity Swap/Option** | Model-Based | DCF (Swap) / pricing model (Option) | [Module 7](./module-07-commodities.md) §4c, §4d |
| **Interest Rate Swap / FRA / Cap-Floor / Swaption** | Model-Based | DCF/NPV off the CORRA curve; Cap/Floor and Swaption additionally need an option pricing model layered on top | Section 04a, this module |
| **IR Forward Debt** | Model-Based (until/unless it delivers a real bond, then Quote-Based) | Forward bond price = Spot – Carry ([Module 8](./module-08-rates.md) §4e) | [Module 8](./module-08-rates.md) §4e |
| **Credit Default Swap** | Model-Based | Credit spread curve × Risky Duration × Notional | Section 04c, this module |
| **Futures (Listed Derivatives)** | Quote-Based | Exchange daily settlement price × contract size | [Module 15](./module-15-listed-derivatives.md) §4a-c |
| **Listed Options** | Model-Based (despite being exchange-traded — Section 02's key exception) | Option pricing model, same family as Section 04b | [Module 15](./module-15-listed-derivatives.md) §4d |

**Takeaway:** the Valuation Family (Quote-Based vs Model-Based) predicts almost everything else about how hard a product is to value correctly — every Model-Based row needs a curve, a spread, or a pricing model as an *input*, which means every Model-Based valuation is only as good as those inputs, exactly the concern Section 6's Fair Value Hierarchy exists to formalize.

---

## 6. The Fair Value Hierarchy — IFRS 13 / ASC 820 Levels 1, 2 &amp; 3

**Definition:** accounting standards (IFRS 13 internationally, ASC 820 in the US — the first time this roadmap has needed an *accounting* standard rather than a markets regulator) classify every Fair Value measurement into one of three Levels, based entirely on how observable its inputs are — not on which asset class the instrument belongs to.

```text
LEVEL 1, 2, 3 — RANKED BY INPUT OBSERVABILITY, NOT ASSET CLASS
========================================================================

  LEVEL 1 — quoted price, identical instrument, active market:
    Coca-Cola stock (Module 4 §4a), CME WTI Future settlement price
    (Module 7 §5), E-mini S&amp;P 500 Future (Module 15 §4a)
    -&gt; the cleanest case: read the price, no judgment involved

  LEVEL 2 — observable inputs, but not a direct quote for THIS
  exact instrument:
    5Y CAD IRS (Module 8 §4a) -- no one quotes "Bank Of Toronto's
      exact swap," but the CORRA curve feeding its DCF is fully
      observable
    Canada 5% Bond 2035, if it trades infrequently -- valued off
      comparable, more liquid bonds' observable yields
    A liquid, standardized single-name CDS (Module 9 §2) -- market
      spread is observable even though no exchange quotes it directly

  LEVEL 3 — unobservable inputs, model assumptions, genuine
  judgment required:
    CDS on a THINLY-TRADED reference entity, where the "market
      spread" input itself has to be estimated, not observed
      (Module 9 §4a's Northstar Resources Inc. trade, if Northstar
      bonds/CDS rarely trade)
    A bespoke FX/IR Exotic (Module 6 §4c's naming-trap Cross-
      Currency Swap, or an exotic barrier option) with no comparable
      liquid market at all

  -&gt; Notice EQUITY, COMMODITY, and RATES products all show up in
     BOTH Level 1 and Level 2 depending on liquidity, not asset
     class -- the same instrument TYPE can sit at different Levels
     for two different specific trades.
```

**Takeaway:** the Fair Value Hierarchy is Section 2's Quote-Based/Model-Based split, refined one step further — Quote-Based instruments are always Level 1; Model-Based instruments split into Level 2 (observable model inputs) or Level 3 (unobservable ones), and that Level 2/3 split is exactly what determines how much independent scrutiny a valuation needs (Section 7).

---

## 7. IT/Reporting Angle — Which Field Is Which, and Who Checks It

**Definition:** three fields that look similar on a trade record answer three different questions, and a Model-Based (Section 2) position needs a fourth process entirely: someone independent re-deriving the number to make sure it's right.

```text
THREE FIELDS, THREE QUESTIONS
========================================================================

  "Price" / "Rate" field   -&gt;  What does the market say THIS
                                 instrument is worth, per unit?

  "Market Value" field     -&gt;  What is my TOTAL position worth,
                                 right now?

  "Unrealized P&amp;L" field   -&gt;  How much has that total worth CHANGED
                                 since I entered (or since the last
                                 valuation date)?

  -&gt; A reporting system that only stores "Market Value" and expects
     a downstream consumer to infer Price or P&amp;L by comparing rows
     over time is pushing work onto every consumer that the source
     system could have done once, correctly.
```

**Independent Price Verification (IPV):** for Level 2 and especially Level 3 positions (Section 6), the Front Office trader's own mark is never the last word — a separate function (commonly Product Control, inside Middle Office, [Module 1](./module-01-fundamentals.md) §4) independently re-derives the valuation, typically monthly, using its own sourced inputs, and any material difference from the trader's mark gets investigated and, if justified, adjusted. This is precisely why Level 3 positions ([Module 9](./module-09-credit.md)'s illiquid single-name CDS, [Module 6](./module-06-fx.md)'s bespoke Cross-Currency Swap) draw far more IPV scrutiny than a Level 1 Coca-Cola position ever would — there's no independent market quote to check a Level 1 mark against a second source; there's only a Level 3 mark's *model and assumptions* to check.

**Takeaway:** the distinction between Market Value and Exposure (Section 1) matters most here too — IPV exists to verify that today's valuation number is *correct*, a completely separate question from [Module 11](./module-11-risk-exposure.md)'s concern about how much that valuation could still move against you before the next check.

---

## 8. Regulatory Perspective — IFRS 13/ASC 820, Basel FRTB, EMIR/Dodd-Frank Valuation Reporting

| Regulation / Body | How it touches Valuation specifically |
|---|---|
| **IFRS 13** (international) | The global accounting standard defining Fair Value and its three-Level hierarchy (Section 06) — issued by the **IASB (International Accounting Standards Board)**, the first standard-setter in this roadmap that isn't a markets/derivatives regulator at all, but an accounting one |
| **ASC 820** (US) | The US GAAP equivalent of IFRS 13, issued by the **FASB (Financial Accounting Standards Board)** — same three-Level concept, different standard-setter, the accounting-world parallel to [Module 8](./module-08-rates.md) §08's CFTC/SEC or [Module 9](./module-09-credit.md) §08's split-jurisdiction pattern, just for financial reporting rather than trading regulation |
| **BCBS FRTB** (Fundamental Review of the Trading Book) | The Basel Committee's post-2008 overhaul of how banks must calculate regulatory capital against trading-book market risk — directly built on the same Quote-Based/Model-Based and Level 1/2/3 distinctions this module established: FRTB explicitly penalizes banks (with higher capital charges) for holding positions with less-observable, harder-to-verify valuations |
| **EMIR / Dodd-Frank** | Both regimes require OTC derivative trades reported to a Trade Repository ([Module 2](./module-02-hierarchy.md) §07) to include periodic **valuation/mark-to-market fields** alongside the static trade economics — this module's Section 04 worked examples are exactly the numbers those reporting fields are populated with |
| **IOSCO / BCBS** (joint) | Their joint margin requirements for non-centrally-cleared derivatives (referenced in [Module 12](./module-12-collateral-margin.md): Collateral &amp; Margin) depend entirely on accurate, independently-verified valuations (Section 07's IPV) to calculate Initial and Variation Margin correctly |

**Takeaway:** this module's regulatory section is the first in the roadmap dominated by accounting standard-setters (IASB, FASB) rather than markets regulators — a reminder that "Valuation" as a topic sits at the intersection of trading (why the number needs to exist at all) and financial reporting (why the number needs to be defensible on a balance sheet), two different disciplines this roadmap has kept mostly separate until now.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Holder of every revalued position across Sections 3–4: the bond, the IRS, and (indirectly, as protection buyer) the CDS |
| Goldman Sachs | Original counterparty on the bond and IRS trades being revalued |
| Northstar Resources Inc. | Reference Entity whose widening credit spread drives Section 4c's CDS revaluation |
| Product Control / Middle Office | Performs Independent Price Verification on Level 2/3 positions (Section 07) |
| IASB / FASB | Standard-setters behind IFRS 13 / ASC 820's Fair Value hierarchy (Section 08) |
| BCBS | Standard-setter behind FRTB's capital treatment of valuation observability (Section 08) |

---

## Recap Quiz — Numbers Edition

1. Using Section 03's approximate YTM formula, if the Canada 5% Bond 2035 had instead been purchased at a Clean Price of 102.00 (a premium) rather than 98.50, what would the approximate YTM be, and would it sit above or below the 5.00% coupon rate?
2. On the same bond, using Section 03's DV01 figure of CAD 7,092/bp, what is the approximate price impact if yields instead FALL 40 basis points?
3. On the AAPL Call in Section 4b, if AAPL had instead fallen to USD 245 (below the USD 250 strike) six months before expiry, what would the Intrinsic Value be, and could the option's Market Value still be positive?
4. Using Section 5's table, name one product that can sit in EITHER the Quote-Based or Model-Based family depending on the specific trade, and explain why.
5. A single-name CDS on a highly liquid, frequently-traded corporate reference entity is being valued. Using Section 06, is this more likely a Level 2 or Level 3 Fair Value measurement, and why?
6. Why does Section 07 say a Level 1 position "draws far less IPV scrutiny" than a Level 3 position — doesn't every position deserve the same level of checking?
7. Using Section 08, contrast what IFRS 13 and BCBS FRTB each specifically care about regarding the SAME underlying fact (an instrument's valuation observability) — are they solving the same problem or two different problems?

Answer whenever ready — then move to **[Module 11](./module-11-risk-exposure.md): Risk &amp; Exposure**, which builds directly on this module's Section 1 distinction between Market Value and Exposure.

---

## Answers — Recap Quiz, with Explanations

**1. Approximate YTM ≈ 4.79%, sitting BELOW the 5.00% coupon rate.**

```text
C = 500,000, F = 10,000,000, P = 10,200,000 (Price 102.00), n = 9

Approx. YTM = [ 500,000 + (10,000,000 - 10,200,000)/9 ] / [ (10,000,000 + 10,200,000)/2 ]
            = [ 500,000 - 22,222.22 ] / 10,100,000
            = 477,777.78 / 10,100,000
            = 4.73%
```

Paying a premium (Price &gt; Face Value) means the buyer's actual return falls below the printed coupon rate — the mirror image of Section 03's discount-bond result, and the exact rule [Module 1](./module-01-fundamentals.md) §1 established: Yield &lt; Coupon Rate whenever Price &gt; Par.

**2. Approximate price GAIN of CAD 283,680.**

```text
Price Impact = -DV01 x Basis Points Moved (with sign flipped for a FALL)
             = 7,092 x 40
             = CAD 283,680 gain
```

DV01 measures the price impact per basis point in either direction — Section 03's worked example showed a loss when yields ROSE 25bps; the same CAD 7,092/bp sensitivity produces a GAIN when yields FALL, simply because bond prices and yields move inversely ([Module 1](./module-01-fundamentals.md) §1's foundational price-vs-rates relationship).

**3. Intrinsic Value = USD 0. The Market Value could still be positive, entirely from Time Value.**

```text
Intrinsic Value = max(245 - 250, 0) x 10,000 = max(-5, 0) x 10,000 = USD 0
```

An option's Intrinsic Value can never go negative — the buyer simply wouldn't exercise an out-of-the-money option, so its floor is zero, not a negative number ([Module 4](./module-04-equity.md) §4d's Scenario B already established this: the option "expires WORTHLESS," never at a loss beyond the premium). But Section 4b's Time Value component (driven by time-to-expiry, volatility, etc.) doesn't depend on being in-the-money — a six-months-out option with real volatility can absolutely still command a positive premium even while USD 5 out-of-the-money, entirely as Time Value with zero Intrinsic Value.

**4. Bonds (Fixed Income) — Quote-Based when actively traded with a live dealer quote, but Level-2-Model-Based (via comparable bonds' yields) when illiquid; the SAME distinction Section 06 draws for Fair Value Levels applies directly to which Valuation Family a specific trade falls into.**

Section 5's table flags Fixed Income and Commodity Physical/Spot/Forward as having a "Quote-Based (Clean Price)" primary basis, but Section 06's Level 2 example explicitly uses "Canada 5% Bond 2035, if it trades infrequently" as valued off comparable liquid bonds instead — proving that Valuation Family (Section 02) isn't fixed per product type, it's fixed per *how liquid that specific instrument is right now*.

**5. More likely Level 2 — the market spread for a highly liquid, frequently-traded name is genuinely observable (dealers quote it regularly), even though no single exchange prints "the" price of any one specific CDS contract the way it does for a stock.**

Section 06 draws exactly this line: "a liquid, standardized single-name CDS... market spread is observable even though no exchange quotes it directly" is explicitly called out as a Level 2 example, contrasted directly against the Level 3 example of a CDS on a thinly-traded reference entity, where the spread input itself has to be estimated rather than observed.

**6. Because IPV's whole purpose is to catch cases where a valuation number could plausibly be WRONG due to model or input uncertainty — a Level 1 position has no such uncertainty to catch: the market price IS the value, verifiable by simply checking a second independent quote source, which takes seconds, not a full model reconstruction. Every position technically gets SOME check, but the depth and cost of that check scales with how much judgment went into the original number.**

Section 07's takeaway makes this proportionality explicit: Level 3 positions draw more scrutiny "because there's no independent market quote to check a Level 1 mark against a second source; there's only a Level 3 mark's model and assumptions to check" — the checking mechanism itself has to be fundamentally different (re-run the model with independently-sourced assumptions) rather than just more thorough in the same way.

**7. They're solving two RELATED but genuinely DIFFERENT problems: IFRS 13 cares about what number should appear on a balance sheet and how confidently a reader of financial statements should trust it (an external financial-reporting/disclosure concern); BCBS FRTB cares about how much regulatory CAPITAL a bank must hold against the risk that a hard-to-verify valuation turns out to be wrong (an internal, prudential-safety-of-the-bank concern).**

Section 08 states this directly: FRTB "explicitly penalizes banks... for holding positions with less-observable, harder-to-verify valuations" — the SAME Level 1/2/3 observability concept IFRS 13 uses for balance-sheet transparency gets reused by BCBS for an entirely different purpose (how much loss-absorbing capital a bank needs to hold), which is exactly why Section 08's takeaway flags this module as the first to sit at the intersection of trading and financial-reporting disciplines rather than belonging cleanly to just one.

---

[← Back to Index](../index.md)
