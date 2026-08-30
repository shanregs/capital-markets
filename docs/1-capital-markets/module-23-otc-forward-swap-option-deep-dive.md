# Module 23 — Forward, Swap &amp; Option Deep Dive — The Three OTC Derivative Building Blocks

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Reference module**, in the same family as the [Hedging Deep Dive](./module-22-hedging-deep-dive.md) and the [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md). Worked examples for these three structures already exist scattered across [Module 2](./module-02-hierarchy.md) §9–10, [Module 6](./module-06-fx.md) §4b–4d, [Module 7](./module-07-commodities.md) §4c–4d, and [Module 8](./module-08-rates.md) §4a–4d — this module pulls every one of them together under a single theory, fills the two gaps (Equity Forward, Commodity Forward) the roadmap never separately worked, and lays every asset class's version of Forward/Swap/Option side by side.

---

## 1. What Are Forward, Swap &amp; Option? — Theory: The Three OTC Building Blocks

**Definition:** almost every OTC derivative this curriculum has built — across Rates, Credit, FX, Equity, and Commodity — is one of exactly three underlying structures. [ISDA's Product Taxonomy](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) §2 even encodes this as its **Transaction Type** level: `Forward`, `Swap`, and (via Call/Put) `Option` are the actual controlled-vocabulary values that level uses, across every one of the five asset classes.

| | **Forward** | **Swap** | **Option** |
|---|---|---|---|
| **One-line theory** | An obligation to transact ONCE, at a fixed price, on one future date | An obligation to transact REPEATEDLY, on a schedule, over a term | A RIGHT (not obligation) to transact once, at a fixed price, by/at expiry |
| **Number of exchanges** | One | Many (periodic) | Zero (if not exercised) or one (if exercised) |
| **Premium at inception** | None | None | Yes — paid upfront |
| **Payoff shape** | Linear, symmetric — moves dollar-for-dollar either direction | Linear, symmetric, repeated every period | Asymmetric — capped downside for the buyer, unlimited (in principle) for the seller |

```text
THE STRUCTURAL RELATIONSHIP — HOW THE THREE FIT TOGETHER
========================================================================

  A SWAP is literally a STRIP OF FORWARDS -- one forward-style
  exchange per payment date, bundled into a single legal contract
  instead of negotiated separately. Section 3's IRS worked example
  makes this exact -- each quarterly settlement is its own tiny
  forward-rate calculation.

  An OPTION is the RIGHT to enter a Forward, not the obligation.
  Strip away the "right, not obligation" and pay no premium, and an
  Option degenerates into a Forward -- which is why a Forward is
  sometimes described as "an Option with a 100% guaranteed exercise
  decision, and therefore no premium charged for that certainty."

  Forward  ---- repeat many times ---->  Swap
     |
     +---- make it optional, charge a premium ---->  Option
```

**Takeaway:** this three-way structure isn't a curriculum simplification — it's the actual regulatory classification. [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) §2's Transaction Type level uses exactly these values, which is why the same three words appear on trade records regardless of whether the underlying is a rate, a credit spread, a currency pair, a stock, or a barrel of oil (Section 7 returns to this directly).

---

## 2. FORWARD — Theory &amp; Worked Examples Across Asset Classes

**Definition:** a **Forward** is a bilateral agreement to transact a fixed amount of something, at a fixed price, on one specific future date — no premium exchanged at inception, and (bar credit risk) no cash moves at all until that one future date. Its payoff is **linear**: P&amp;L = (Agreed Price − Market Price at maturity) × Notional, moving dollar-for-dollar in either direction, exactly [Module 2](./module-02-hierarchy.md) §10's FX Forward already showed for currencies.

```text
FORWARD PAYOFF — LINEAR, BOTH DIRECTIONS
========================================================================

  Buyer's P&amp;L
      +  |                    /
         |                  /
         |                /
    -----+--------------+------------------  Market Price at maturity
         |            / Agreed
         |          /  (Forward) Price
      -  |        /

  -&gt; unlike Section 4's Option, there is NO flat/capped region on
     either side -- the line runs straight through, in both directions
```

### FX — reusing Module 2 §10 in full

```text
3-MONTH USD/CAD FORWARD — Bank Of Toronto needs USD 10,000,000
========================================================================
  Forward Rate locked today: 1.3545 (Spot 1.3500 + 0.0045 forward points)
  In 3 months: Bank Of Toronto pays CAD 13,545,000, receives USD 10,000,000
  -&gt; no cash moves today; only the RATE is locked in now
```

### Rates — the FRA, and its cousin IR Forward Debt (Module 8 §4b, §4e)

```text
FRA (Forward Rate Agreement) — a "forward" written on a RATE itself
========================================================================
  3x6 FRA: Bank Of Toronto BUYS (pays fixed, receives floating)
  Notional CAD 20,000,000 | FRA Rate: 4.85% | 91-day reference period

  At fixing, 3-month CORRA settles at 4.50% (lower than locked-in 4.85%)
  Bank Of Toronto PAYS Goldman Sachs CAD 17,259.79 -- the discounted
  difference between the FRA Rate and the settled floating rate

  -&gt; ONE isolated forward-rate exchange, settled ONCE, upfront
     (discounted) rather than at period-end -- Section 3 shows this
     is exactly what an IRS repeats every quarter, undiscounted

IR FORWARD DEBT — a "forward" written on a BOND'S PRICE instead
========================================================================
  Bank Of Toronto agrees TODAY to buy Canada 5% Bond 2035 in 3 months
  Forward Price: 97.80 (today's spot 98.50, minus 3 months of carry)
  In 3 months, if physically settled: pays CAD 9,780,000, receives
  CAD 10,000,000 face value of the bond via DvP

  -&gt; structurally identical to the FX Forward above (fixed price,
     one future exchange), just referencing a BOND'S PRICE instead
     of a CURRENCY PAIR'S rate -- and unlike the FRA, an ACTUAL
     security can change hands if not cash-settled first
```

### Equity — new worked example (no existing curriculum trade to reuse)

```text
EQUITY FORWARD — Bank Of Toronto agrees to sell KO shares forward
========================================================================
(illustrative -- fills a gap Module 4 never separately worked;
built on Module 4 §4a's real KO position: 10,000 shares @ 68.40)

  Bank Of Toronto (holder of 10,000 KO shares) agrees TODAY to sell
  all 10,000 shares to a client in 6 months, at a forward price fixed
  now instead of whatever the market happens to be then.

  Forward Price = Spot + Cost of Carry - Expected Dividends
                = 68.40 + (68.40 x 4.5% x 0.5) - (68.40 x 1.5% x 0.5)
                = 68.40 + 1.539 - 0.513 = 69.43 (illustrative)

  IN 6 MONTHS: client pays 10,000 x 69.43 = USD 694,300, receives
  10,000 KO shares -- regardless of where KO is actually trading then

  -&gt; same cost-of-carry logic as Module 6's FX forward points
     (interest-rate differential), with an extra term for the
     dividends the SELLER forgoes by not holding the position anymore
```

### Commodity — new worked example (no existing curriculum trade to reuse)

```text
COMMODITY FORWARD — OTC, bilateral, uncleared version of a Future
========================================================================
(illustrative -- contrast directly with Module 7 §4b's EXCHANGE-
TRADED WTI Future, same underlying, different venue)

  Bank Of Toronto agrees TODAY, bilaterally with an oil producer
  client, to buy 50,000 bbl WTI crude in 2 months at USD 77.00/bbl
  fixed -- negotiated directly, NOT executed on CME's order book,
  NOT novated to a CCP at execution (contrast Module 7 §4b's Future,
  which clears instantly)

  IN 2 MONTHS, if WTI spot is USD 82.00/bbl:
    Bank Of Toronto still only pays 77.00/bbl -- gain of
    (82.00 - 77.00) x 50,000 = USD 250,000 vs. buying at spot then

  -&gt; ECONOMICALLY near-identical to Module 7 §4b's exchange Future
     -- the difference is entirely about VENUE and CLEARING (Module
     15's "standardization is a venue property, not an asset-class
     property"), not about the payoff mechanics
```

> **Callout — Credit doesn't have a clean "Forward" building block.** Unlike the other four asset classes, Credit's core OTC product (the CDS, Section 3) is already priced and structured as a periodic-premium contract with a *contingent* payout, not a one-off fixed-price exchange. A "forward-starting CDS" (protection that only begins on a future date) exists, but it's a variant of the Swap-family CDS, not a distinct Forward product the way FX/Rates/Equity/Commodity each have one — a genuine asset-class-specific exception, not an oversight.

**Takeaway:** every Forward in this section shares the exact same shape — one fixed price, agreed today, for one exchange on one future date, no premium — whether it references a currency pair, a rate, a bond, a stock, or a barrel of oil. What changes across asset classes is only *what* the fixed price is being applied to and *whether* an actual asset can ever be delivered (FX, IR Forward Debt, Equity, and Commodity Forwards can all physically settle; the FRA never can, because a rate itself isn't a deliverable thing).

---

## 3. SWAP — Theory &amp; Worked Examples Across Asset Classes

**Definition:** a **Swap** is an agreement to exchange a *series* of cash flows over time, each one calculated off a formula — most commonly a fixed rate/price against a floating (market-observed) one, on the same underlying Notional, repeated on a schedule. Structurally, Section 1 already established this as a strip of Forwards bundled into one contract; the worked IRS example below makes that literal.

```text
5Y CAD IRS — Bank Of Toronto pays fixed, receives floating (Module 8 §4a)
========================================================================
  Notional CAD 100,000,000 | Fixed Rate 4.00% | Floating: CORRA + spread
  Quarterly settlements, netted, for 5 years (20 total settlements)

  -&gt; EACH quarterly settlement is its own isolated fixed-vs-floating
     comparison -- economically the SAME calculation as Section 2's
     FRA, just repeated 20 times inside one contract instead of
     negotiated as 20 separate one-off forwards. This is Section 1's
     "Swap = strip of Forwards" claim, made concrete: an IRS really
     is 20 FRAs, wrapped in a single legal document, undiscounted
     (Module 8 §4a/§4b's Q2 answer covers exactly why the discounting
     convention differs between the two).

  Full 20-settlement walkthrough: see the standalone
  Interest Rate Swap (IRS) Deep Dive.
```

### Credit — the CDS, named a "Swap" but structurally different

```text
5Y CDS ON NORTHERN RAIL CORP — Bank Of Toronto buys protection
(Module 2 §9)
========================================================================
  Notional CAD 20,000,000 | Spread 120bps | Quarterly premium

  Bank Of Toronto pays Goldman Sachs CAD 59,836 EVERY QUARTER
  (the periodic, swap-like leg) -- UNLESS Northern Rail Corp
  defaults, in which case Goldman Sachs pays Bank Of Toronto
  CAD 12,000,000 ONCE (a CONTINGENT leg, not a periodic one)

  -&gt; ONE leg (the premium) behaves exactly like a swap's periodic
     exchange. The OTHER leg (the default payout) behaves like
     Section 4's OPTION -- it only pays if a specific contingency
     is triggered, and its maximum payout is fixed in advance. A
     CDS is documented AS a swap (hence the name), but economically
     sits on the boundary between Section's 3 Swap family and
     Section 4's Option family.
```

### FX — the FX Swap (a short-dated exception), and Cross-Currency Swap (the true Rates-classified long-dated one)

```text
FX SWAP vs. CROSS-CURRENCY SWAP — A NAMING TRAP (Module 6 §2)
========================================================================
  FX SWAP (Module 6 §4c): commonly overnight to 1 year, just a Spot
  and a Forward bolted together -- TWO principal exchanges, NO
  periodic interest payments at all. Really closer to Section 2's
  Forward family than a true multi-period Swap.

  CROSS-CURRENCY SWAP: longer-dated (years), WITH periodic interest
  payments exchanged in two currencies over the life of the trade --
  this is the one that genuinely behaves like Section 3's IRS, just
  with two different-currency legs instead of one fixed/one floating
  leg in the same currency. ISDA classifies it under INTEREST RATE,
  not FX (Module 6 §2's own explicit warning) -- precisely because
  its periodic-exchange mechanic is a Rates-family Swap, not an
  FX-family product, despite involving two currencies.
```

### Equity — Total Return Swap on Apple Inc. shares (Module 2 §8)

```text
EQUITY TRS ON APPLE INC. SHARES
========================================================================
  Periodic net cash flows tracking the share's price movement PLUS
  dividends, exchanged against a financing-rate leg (typically a
  floating short-term rate) -- same fixed/floating-style exchange
  shape as the IRS, just with "price + dividend return" standing in
  for "fixed rate" as one of the two legs being swapped.
```

### Commodity — Brent Crude Oil Swap (Module 7 §4c)

```text
1Y BRENT CRUDE OIL SWAP — Bank Of Toronto vs. Shell Trading
========================================================================
  Fixed Price: USD 78.00/bbl | Monthly notional: 8,333 bbl

  MONTH 1 -- ICE Brent monthly average settles at USD 81.20/bbl
    Floating leg owed to Bank Of Toronto = 8,333 x 81.20 = USD 676,640
    Fixed leg owed by Bank Of Toronto    = 8,333 x 78.00 = USD 649,974
    Net cash flow: Bank Of Toronto RECEIVES USD 26,666
    -&gt; only the NET amount moves, repeating monthly for 12 months --
       Section 1's "strip of Forwards" pattern again, one Forward-
       style netted exchange per month
```

**Takeaway:** four of five asset classes' Swaps follow the identical fixed-vs-floating (or price-return-vs-financing) periodic netting mechanic — the genuine outlier is Credit's CDS, whose contingent default leg borrows Section 4's Option-family payoff shape even while its premium leg behaves like a plain Swap. That dual identity is *why* CDS sits awkwardly across two sections of this module, and it's not a modeling error — it's a real structural fact a risk or valuation system has to handle by pricing the two legs with genuinely different models (a discounted cash flow for the premium leg, a contingent-claim/default-probability model for the protection leg).

---

## 4. OPTION — Theory &amp; Worked Examples Across Asset Classes

**Definition:** an **Option** grants its buyer the **right, but not the obligation**, to transact at a fixed strike price by/at expiry, in exchange for an upfront **premium**. Its payoff is **asymmetric**: the buyer's maximum loss is capped at the premium paid, while the potential gain is uncapped (for a call) or bounded only by the strike falling to zero (for a put) — the seller (writer) takes the mirror-image, uncapped-risk side.

```text
OPTION PAYOFF — ASYMMETRIC, CAPPED ON ONE SIDE
========================================================================

  Buyer's P&amp;L
      +  |                         /
         |                       /
         |                     /
    -----+-------------------+------------------  Market Price at expiry
         |. . . . . . . . . /  Strike Price
      -  |________________/
         (flat -- capped at premium paid, no matter how far the
          market moves against the position)
```

### FX — reusing Module 6 §4d in full

```text
FX OPTION — Bank Of Toronto's client hedges a future USD payable
========================================================================
  USD Call / CAD Put, Notional USD 5,000,000, Strike 1.3600
  Premium: 0.0180 CAD per USD notional = CAD 90,000

  Spot to 1.3800: exercise, net benefit CAD 10,000 (after premium)
  Spot to 1.3400: expires worthless, max loss = CAD 90,000 premium
```

### Commodity — reusing Module 7 §4d in full

```text
COMMODITY OPTION — Call on CME WTI Crude Oil Future
========================================================================
  Strike USD 80.00/bbl, Notional 100,000 bbl, Premium USD 3.20/bbl
  Premium Paid = USD 320,000

  WTI to USD 88.00/bbl: exercise, net benefit USD 480,000
  WTI to USD 74.00/bbl: expires worthless, max loss = USD 320,000 premium
```

### Rates — the Swaption (Module 8 §4d)

```text
SWAPTION — 1Y-into-5Y Payer Swaption (an OPTION on a SWAP)
========================================================================
  Bank Of Toronto buys the RIGHT to enter a 5Y IRS, as fixed-rate
  PAYER, in 1 year -- notice this is literally "the option to enter
  Section 3's IRS," Section 1's Forward-vs-Option relationship
  applied one layer up, to an entire Swap instead of a single exchange

  Notional CAD 50,000,000, Strike 4.25%, Premium 0.60% = CAD 300,000

  Prevailing 5Y rate at expiry = 4.75%: EXERCISE -- enters the swap
  paying only 4.25% fixed when the market rate is 4.75%
  Prevailing 5Y rate at expiry = 3.90%: DON'T exercise -- a fresh
  market swap beats the 4.25% strike; loss capped at CAD 300,000 premium
```

### Equity — an OTC put, contrasted directly with a listed one

```text
OTC EQUITY OPTION — the bespoke, dealer-written version of a listed
option, reusing the Hedging Deep Dive's protective put on KO
========================================================================
  Maple Leaf Pension Fund already holds 10,000 KO shares (Module 4
  §4a). It buys a PROTECTIVE PUT directly from Bank Of Toronto (its
  dealer) rather than through a listed options exchange:

  KO Put, Strike USD 65.00, Notional 10,000 shares, Expiry 1 month
  Premium USD 1.20/share = USD 12,000

  KO falls to 58.00: exercise, avoided loss = USD 70,000
  KO rises to 75.00: expires worthless, max loss = USD 12,000 premium

  -&gt; the PAYOFF SHAPE is identical to Module 4 §4d's LISTED AAPL
     call -- the only real difference is VENUE: this trade is
     negotiated bilaterally and never touches the OCC (Module 15
     §7's "standardization is a venue property, not an asset-class
     property," restated one more time here for Options specifically)
```

> **Callout — Credit's option-like behavior already lives inside the CDS.** Rather than a separate standalone "Credit Option" product worked here, Section 3 already showed the CDS's own default-contingent leg behaves exactly like an Option's payoff (premium paid regardless, one contingent payout capped by notional). A true standalone product exists (CDS Index Swaptions — options to enter a CDS index position), but it's a niche instrument this curriculum doesn't build a separate worked example for.

**Takeaway:** the asymmetric shape — premium paid, capped downside, uncapped upside, worthless expiry as the base case — recurs identically whether the underlying is a currency pair, a commodity, an entire interest rate swap, or a single stock. The Swaption is the most conceptually important entry in this list: it proves Section 1's Forward → Swap, Option-on-Forward relationship generalizes one level up — an Option can be written on a Swap just as easily as on a single exchange.

---

## 5. The Full Matrix — Forward × Swap × Option, Across Five Asset Classes

**Definition:** every worked example from Sections 2–4, in one reference table.

| Asset Class | Forward | Swap | Option |
|---|---|---|---|
| **Interest Rate** | FRA (§2); IR Forward Debt (§2) | Interest Rate Swap (§3) | Swaption (§4) |
| **Credit** | *No standalone product* (§2's callout) | Credit Default Swap (§3) | *Embedded in the CDS's protection leg* (§4's callout) |
| **Foreign Exchange** | FX Forward (§2) | FX Swap / Cross-Currency Swap (§3, the latter classified under Interest Rate) | FX Option (§4) |
| **Equity** | Equity Forward (§2, new) | Equity Total Return Swap (§3) | Equity Option, OTC or listed (§4) |
| **Commodity** | Commodity Forward (§2, new) | Commodity Swap (§3) | Commodity Option (§4) |

**Takeaway:** the grid is nearly complete — Credit is the one asset class where the Forward *and* Option cells don't have clean standalone entries, because both are effectively absorbed into the CDS's own dual-natured structure (Section 3, Section 4). Every other cell follows the theory in Section 1 exactly, which is the strongest evidence that the Forward/Swap/Option framework isn't just a convenient teaching device — it's the actual shape the OTC derivatives market is built on, asset class after asset class.

---

## 6. Why the Structural Difference Matters — Premium Timing &amp; Counterparty Credit Exposure

**Definition:** beyond the payoff-shape theory, Forward/Swap vs. Option produce genuinely different **counterparty credit exposure profiles** over a trade's life — directly relevant to [Module 11](./module-11-risk-exposure.md) §2's Current Exposure vs. Potential Future Exposure (PFE) distinction.

```text
EXPOSURE PROFILE OVER TIME — FORWARD/SWAP vs. OPTION
========================================================================

  FORWARD / SWAP (no premium at inception):
    Day 1 MTM = CAD 0 (fairly priced at trade date, Module 10 §4a)
    Current Exposure = CAD 0 on day 1 -- but PFE is NOT zero (Module
    11 §2's own IRS example: PFE estimated at roughly CAD 4,500,000,
    2 years into a 5Y CAD 100,000,000 IRS, at a 95% confidence level)
    -&gt; RISK BUILDS UP GRADUALLY as rates/prices move, in BOTH
       directions -- either counterparty can end up owing the other

  OPTION (premium paid upfront):
    Day 1: BUYER has ALREADY PAID the premium (e.g. Section 4's
    CAD 90,000 FX Option premium) -- the buyer's maximum possible
    loss is fully known and fully realized in exposure terms from
    day one; it can only go DOWN from there (toward zero, at expiry)
    -&gt; for the SELLER (writer), exposure runs the OPPOSITE way --
       having COLLECTED the premium, the seller's risk is entirely
       in the FUTURE, potentially large and one-directional (the
       option only gets exercised against the seller if it's
       in-the-money for the buyer)
```

**Takeaway:** this is precisely why a bank's credit-limit and Initial Margin calculations ([Module 12](./module-12-collateral-margin.md)) treat a Forward/Swap position and an Option position differently even at identical notional — a Forward or Swap's risk is symmetric and back-loaded (grows over the trade's life, in either direction), while an Option's risk is asymmetric and front-loaded for the buyer (paid and capped immediately) but back-loaded and one-directional for the seller. A risk system that applies the same exposure-profile shape to both product families will misprice one side of every option book it holds.

---

## 7. ISDA Taxonomy Tie-In — Forward, Swap &amp; Option as Transaction Type Values

**Definition:** closing the loop back to the [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md), whose §2 four-level hierarchy (`Asset Class / Base Product / Sub-Product / Transaction Type`) uses these same three words as controlled values at its final level.

```text
WHERE FORWARD / SWAP / OPTION SIT IN THE ISDA TAXONOMY
========================================================================

  Interest Rate  -> IRSwap     -> FixedFloat   -> SWAP
  Credit         -> SingleName -> Corporate    -> CREDITDEFAULTSWAP
                                                   (named "Swap," Section 3's
                                                   dual-natured exception)
  Foreign Exch.  -> Forward    -> Deliverable  -> FORWARD
  Equity         -> Swap       -> PriceReturn  -> SWAP
  Commodity      -> Energy     -> Oil          -> SWAP

  -&gt; Notice OPTION doesn't appear as a Transaction Type value in any
     of this module's five worked ISDA-taxonomy paths above, because
     none of Sections 2-3's SWAP/FORWARD examples were Options --
     Section 4's Swaption, FX Option, Commodity Option, and Equity
     Option trades would each carry their OWN separate taxonomy
     path with Transaction Type = OPTION (or a Call/Put-specific
     variant), distinct from their underlying Forward/Swap cousins.
```

**Takeaway:** this is the concrete answer to "where does the Forward/Swap/Option distinction actually show up in a trade record" — it's not just descriptive language, it's a literal, regulator-facing field value on every OTC derivative's ISDA taxonomy path, which in turn (per the [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) §5) feeds directly into the trade's UPI reference data. Getting Section 1's theory right isn't academic — it's the difference between a correctly and incorrectly populated regulatory report.

---

## 8. Official Sources &amp; Links

**Definition:** as with this curriculum's other reference modules, pointing to primary sources rather than restating technical definitions that get revised periodically.

| Source | What It Covers | Link |
|---|---|---|
| **ISDA** | The **2006 ISDA Definitions** (and product-specific supplements) — the standard legal/economic definitions used to document Forwards, Swaps, and Options across every asset class in this module | [www.isda.org](https://www.isda.org) |
| **ISDA Product Taxonomy** | Where Forward/Swap/Option formally sit as Transaction Type values — see the [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) | [www.isda.org](https://www.isda.org) |
| **FpML** | The XML messaging standard used to actually transmit Forward/Swap/Option trade economics between systems, structurally aligned with ISDA's taxonomy | [www.fpml.org](https://www.fpml.org) |

**Takeaway:** treat the 2006 ISDA Definitions (and its asset-class-specific supplements) as the source of truth for exactly how each product's cash flows and triggering events are legally defined; the ISDA Product Taxonomy as the source of truth for how those products get classified on a regulatory report; and FpML as the source of truth for how the same economic terms are actually represented in a trade message between two systems.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Party to every worked example across all three sections — FX/Rates/Commodity Forwards, IRS/CDS/TRS/Commodity Swaps, and every Option |
| Goldman Sachs | Counterparty on the FX Forward, FRA, CDS protection sale, and Swaption |
| Shell Trading | Counterparty on the Brent Crude Oil Swap (Section 3) |
| Maple Leaf Pension Fund | Holder of the KO position hedged with the OTC put (Section 4) |
| Northern Rail Corp | Reference entity for the CDS (Section 3) |
| ISDA / FpML | Standard-setters for how these three structures are legally defined and technically transmitted (Section 8) |

---

## Recap Quiz

1. Using Section 1, explain in one sentence why a Swap is often described as "a strip of Forwards" and why an Option is described as "the right to enter a Forward."
2. Using Section 2, why does the FRA settle its cash flow discounted at the *start* of its reference period, while the IR Forward Debt example settles at the *end* — and why doesn't this reflect a genuine structural difference between the two?
3. Using Section 2's callout, why doesn't Credit have a standalone "Forward" product the way FX, Rates, Equity, and Commodity each do?
4. Using Section 3, in what specific way does the CDS's premium leg behave like a Swap while its default leg behaves like an Option?
5. Using Section 3, why is a Cross-Currency Swap classified under Interest Rate rather than Foreign Exchange, despite literally exchanging two currencies?
6. Using Section 4, what does the Swaption prove about how far Section 1's Forward/Option relationship generalizes?
7. Using Section 6, why does a Forward or Swap's counterparty credit exposure grow gradually over the trade's life, while an Option buyer's maximum exposure is already fully known on day one?
8. Using Section 7, if a trade's ISDA taxonomy path ends in Transaction Type = OPTION, does that tell you which of the five asset classes it belongs to? What WOULD tell you that instead?

---

## Answers — Recap Quiz, with Explanations

**1. A Swap repeats a Forward-style fixed-vs-floating exchange on a schedule instead of negotiating one exchange once — literally many Forwards bundled into a single contract. An Option is the right, not the obligation, to enter that same kind of fixed-price exchange — strip away the optionality and the premium, and it collapses back into a plain Forward.**

Section 1's structural-relationship diagram states both halves of this directly, and Section 3's IRS example (20 quarterly settlements, each its own isolated fixed-vs-floating comparison) makes the "strip of Forwards" claim concrete.

**2. The FRA settles at the start of its reference period because the rate is already known by then, so paying immediately (once discounted back to present value) is more natural than waiting for the period to end; IR Forward Debt settles at the end (its one and only settlement date) because there's no earlier "already known" point — the whole point of a Forward is that nothing is known or exchanged until that single future date. This isn't a structural difference between Forward types — it's just a timing convention specific to how a *rate-referencing* forward (the FRA) happens to be documented, versus a *price-referencing* forward (IR Forward Debt) that only ever has one settlement date to begin with.**

Section 2's FRA and IR Forward Debt worked examples both state their settlement timing explicitly, and Module 8's own Q2 answer (cross-referenced in Section 3) makes the same point about discounting conventions differing without reflecting a deeper economic difference.

**3. Because Credit's core OTC product (the CDS) is already structured as a periodic-premium, contingent-payout contract rather than a one-off fixed-price exchange — there's no natural "Credit Forward" the way a currency pair, a rate, a bond price, or a commodity price each have one. A forward-starting CDS exists, but it's a variant of the Swap-family CDS, not a distinct Forward product.**

Section 2's callout states this directly as a genuine asset-class-specific exception, not an oversight in the curriculum.

**4. The premium leg is paid periodically (quarterly, CAD 59,836 each time) regardless of whether a default ever happens — the same repeated, formula-driven exchange shape as any other Swap's periodic leg. The default leg pays out only ONCE, only IF a specific contingency (default) is triggered, with a payout capped by the notional and recovery rate — exactly Section 4's Option payoff shape (premium paid regardless, one contingent, capped payout).**

Section 3's CDS worked example and takeaway state this dual-nature explicitly, explaining why a CDS is documented as a Swap by name while behaving like an Option on one of its two legs.

**5. Because what makes it a "Swap" at all — the periodic exchange of interest payments over a multi-year term — is the same Interest-Rate-family mechanic the IRS uses, just running in two currencies instead of one. FX's own products (Spot, Forward, and the short-dated FX Swap) never have periodic interest payments at all — only principal exchanges — so a Cross-Currency Swap's defining feature belongs structurally to Rates, not FX, even though currencies are obviously involved.**

Section 3's "naming trap" callout states this directly, cross-referencing Module 6 §2's own explicit warning about the two products' different classifications.

**6. It proves the Forward-to-Option relationship isn't limited to a single exchange — an Option can be written on an entire Swap (a multi-period contract) just as easily as on one Forward-style exchange. The Swaption is literally "the right to enter Section 3's IRS," one layer of abstraction up from a plain vanilla Option on a single future exchange.**

Section 4's Swaption worked example states this explicitly, calling it "Section 1's Forward-vs-Option relationship applied one layer up, to an entire Swap instead of a single exchange."

**7. A Forward or Swap starts with zero MTM (fairly priced at inception) but has no premium locking in a maximum cost either way — as rates/prices move over the trade's life, either counterparty's exposure can grow, in either direction, which is exactly what Potential Future Exposure estimates. An Option buyer, by contrast, pays the full premium upfront on day one — that IS the buyer's maximum possible loss, fully realized and already known immediately, and it can only shrink toward zero as expiry approaches.**

Section 6's exposure-profile diagram states both halves of this directly, reusing Module 11 §2's own PFE example for the Forward/Swap side.

**8. No — Transaction Type = OPTION alone doesn't tell you the asset class, because every one of the five asset classes has its own Option variant (FX Option, Commodity Option, Swaption, Equity Option; Credit's is embedded in the CDS rather than standalone). What WOULD tell you is the taxonomy path's first level, Asset Class, read together with Base Product and Sub-Product — exactly the same point Section 2 of the ISDA Product Taxonomy Deep Dive already made about no single level of the four-level hierarchy being sufficient alone.**

Section 7's diagram and takeaway state this directly, cross-referencing the ISDA Product Taxonomy Deep Dive's own point that Transaction Type alone is ambiguous across asset classes.

---

[← Back to Index](../../index.md)
