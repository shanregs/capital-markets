# Module 24 — Basis Points Deep Dive — Theory, Worked Examples &amp; Impact on Valuation, MTM &amp; YTM

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Reference module**, in the same family as the [Hedging Deep Dive](./module-22-hedging-deep-dive.md) and the [Interest Rate Swap Deep Dive](./module-19-interest-rate-swap-deep-dive.md). Basis points already appear scattered across [Module 5](./module-05-fixed-income.md) §1 (Duration), [Module 8](./module-08-rates.md) §1 (DV01 preview), [Module 9](./module-09-credit.md) (CDS spreads), and [Module 10](./module-10-valuation.md) §3–4 (DV01/Spread DV01 valuation) — this module pulls every one of them together under a single theory, adds Equity and FX worked examples to complete the six-asset-class set, and answers the practical "how often does it change, and what does it actually move" question this curriculum keeps returning to.

---

## 1. What Is a Basis Point? — Definition &amp; Why Percent Isn't Precise Enough

**Definition:** a **basis point (bp, plural bps)** is **one hundredth of one percent** — 0.01%, or 0.0001 in decimal form. It's the standard unit capital markets uses whenever a percentage move is too small, or too consequential, to describe usefully in whole percentage points.

```text
THE CONVERSION EVERY OTHER SECTION IN THIS MODULE ASSUMES
========================================================================

  1 basis point (1 bp)   = 0.01%   = 0.0001
  10 basis points         = 0.10%   = 0.0010
  25 basis points         = 0.25%   = 0.0025
  100 basis points        = 1.00%   = 0.0100
  250 basis points        = 2.50%   = 0.0250

  -> "bps" is always ADDITIVE on a percentage/rate scale, never
     multiplicative -- a rate moving from 4.00% to 4.25% moved
     "up 25bps," not "up 6.25%" (which would be the PERCENTAGE
     CHANGE of 4.00 relative to itself -- a different, and much
     less useful, number in this context).
```

**Why not just say "0.25%"?** Two reasons this curriculum's other modules already lean on without spelling out until now:

- **Precision without clutter.** A rate move from 4.00% to 4.03% is "0.03%" in percent — awkward to say, easy to misread as 3%. "3 basis points" is unambiguous at a glance, and every professional in the market reads it identically.
- **A common unit across wildly different base numbers.** A 25bp move means the same *thing* — 0.0025 added to a rate or yield — whether the starting rate is 0.50% or 12.00%. Percent-of-percent comparisons ("a quarter of a percent," "half a percent") get confusing fast once the base rate itself is small; bps sidestep that entirely by being an absolute, not relative, unit of rate change.

**Takeaway:** every worked example in this module — Fixed Income, Rates, Credit, Equity, FX — reduces to the same arithmetic fact: **bps × 0.0001 × exposure = dollar impact**, just with a different "exposure" plugged in each time (DV01, Spread DV01, notional, fee base). Section 2 maps exactly where each asset class plugs bps in.

---

## 2. Basis Points Across Asset Classes — Where They Show Up

**Definition:** bps aren't a Fixed-Income-only concept — every asset class this curriculum's roadmap covers uses them somewhere, but for genuinely different purposes. Confusing *which* use is in play for a given asset class is a common source of reporting-system errors.

| Asset class | Where bps show up | What moves per bp | This module's example |
|---|---|---|---|
| **Fixed Income** | Bond **yield** (YTM) and price sensitivity | Price, via DV01 (Section 3) | Section 3 |
| **Rates (IRS/derivatives)** | The floating/fixed **rate** itself, and swap valuation | Swap MTM, via Swap DV01 (Section 4) | Section 4 |
| **Credit** | CDS **spread**, bond credit spread over a benchmark | Protection value, via Spread DV01 (Section 5) | Section 5 |
| **Equity** | Management/advisory **fees**, bid-ask **spreads**, index-level moves expressed in points (not usually bps) | Fee cost, execution cost (Section 6) | Section 6 |
| **FX** | Rarely used directly — FX moves are quoted in **pips**, a related but distinct unit (Section 7) | Exchange rate, in pip terms | Section 7 |
| **Commodities** | Less common directly; futures tick sizes are usually quoted in price units, not bps | — | Not separately worked here — see [Module 7](./module-07-commodities.md) for tick-size conventions |

**Takeaway:** the single unifying thread is that **bps measure a rate, yield, spread, or fee** — never a raw price. Fixed Income and Rates use bps because their core quoted number (yield, swap rate) already *is* a percentage; Credit uses bps because a CDS spread is itself quoted as a rate; Equity's use is narrower (fees and trading costs, not the stock price itself); FX mostly opts out in favor of pips (Section 7 explains why). A trade or reference-data system that tags a raw equity price move "up 150bps" is almost certainly using the term loosely, not precisely — Section 6 draws that line explicitly.

---

## 3. Worked Example — Fixed Income: DV01 &amp; Price Impact From a Yield Move

**Definition:** reusing [Module 5](./module-05-fixed-income.md) §4a's Canada 5% Bond 2035 position and [Module 10](./module-10-valuation.md) §3's full DV01 math in full — the cleanest, most direct bps-to-dollars translation in the curriculum, because a bond's price and yield move in lockstep, one basis point at a time.

```text
DV01 — CANADA 5% BOND 2035  (Module 5 §4a position, Module 10 §3 math)
========================================================================

  Modified Duration: 7.2 | Market Value: CAD 9,850,000

  DV01 = Modified Duration x Market Value x 0.0001
       = 7.2 x 9,850,000 x 0.0001
       = CAD 7,092 per basis point

  SCENARIO A -- yields RISE 25 basis points (4.75% -> 5.00% YTM area):
    Approx. Price Impact = -DV01 x Basis Points Moved
                          = -7,092 x 25
                          = -CAD 177,300  (bond LOSES value)

  SCENARIO B -- yields FALL 40 basis points:
    Approx. Price Impact = +7,092 x 40 = +CAD 283,680 (bond GAINS value)

  -> Bond prices and yields move INVERSELY (Module 1 §1) -- DV01
     is symmetric in size but the SIGN flips depending on direction.
     A reporting system computing DV01 once and reusing the same
     sign for both a rate rise and a rate fall will get one of the
     two scenarios backwards.
```

**Why YTM itself is quoted to the basis point.** A bond's Yield to Maturity ([Module 5](./module-05-fixed-income.md) §1, [Module 10](./module-10-valuation.md) §3) is conventionally quoted to two decimal places as a percentage — this bond's own YTM, 5.21% ([Module 10](./module-10-valuation.md) §3), is exactly "521 basis points" said a different way. Every subsequent DV01 calculation is measuring the price impact of that YTM figure ticking by single bps, which is precisely why DV01 is defined "per basis point" rather than "per percent" — a whole percentage point of yield movement is a large, relatively rare event; single-basis-point ticks are the everyday unit the bond market actually trades in.

**Takeaway:** DV01 (Dollar Value of 01, meaning 0.01% = 1bp) is the FIxed Income asset class's core bps-to-dollars converter, and it plugs directly into two of the curriculum's three central questions: **valuation** (what is this bond worth right now, off today's YTM) and **MTM** (how much did that worth change since yesterday, expressed as bps moved × DV01). Section 9 generalizes this exact mechanic — a sensitivity measure "per basis point" — across every other asset class in this module.

---

## 4. Worked Example — Rates: Swap DV01 &amp; MTM on an Interest Rate Swap

**Definition:** reusing [Module 8](./module-08-rates.md) §4a's 5Y CAD IRS (Bank Of Toronto pays fixed 4.00%, receives floating CORRA, Notional CAD 100,000,000) and [Module 10](./module-10-valuation.md) §4a's MTM math — the same DV01 concept as Section 3, just computed off a swap's cash flow schedule instead of a bond's.

```text
SWAP DV01 &amp; MTM — 5Y CAD IRS  (Module 8 §4a trade, Module 10 §4a math)
========================================================================

  Trade ID BOT-IRS-2026-034410
  Bank Of Toronto: pays FIXED 4.00%, receives FLOATING (CORRA)
  Notional (reference only, never exchanged): CAD 100,000,000

  Swap DV01 (approx., 4.5Y remaining): ~CAD 41,000 per basis point

  RATE MOVES from 4.00% to 4.30% (a 30bp RISE in the swap curve):
    Approx. MTM Gain to Bank Of Toronto = 41,000 x 30
                                         = CAD 1,230,000

  -> Bank Of Toronto pays a FIXED rate that is now BELOW the market
     rate for an equivalent new swap -- its existing position is
     worth MORE than it was, because it locked in a now-cheap fixed
     rate. The fixed-rate PAYER gains when rates RISE; the fixed-rate
     RECEIVER (the counterparty) loses the same CAD 1,230,000.
```

**Why the same DV01 concept, a different number.** Section 3's bond DV01 (CAD 7,092/bp) and this swap's DV01 (CAD 41,000/bp) aren't comparable in isolation — the swap's Notional is roughly 10x the bond's Market Value, but its DV01 is roughly 5.8x, because a swap's DV01 also depends on the shape of its remaining cash flow schedule (4.5 years of quarterly resets), not Notional alone. The lesson: **DV01 is never inferred from Notional/Market Value by a rule of thumb** — it's always the output of a full revaluation (or a documented approximation of one), which is exactly [Module 10](./module-10-valuation.md) §4a's own closing caveat about the DV01 shortcut vs. a full curve-based DCF.

**Takeaway:** a swap's MTM is, in practice, almost always reported as "bps moved × Swap DV01" between full revaluations — the same arithmetic shape as Section 3's bond, confirming Section 1's claim that every asset class reduces to the same `bps × 0.0001 × exposure` formula, just with Swap DV01 standing in for bond DV01.

---

## 5. Worked Example — Credit: CDS Spread in Basis Points &amp; Spread DV01

**Definition:** reusing [Module 9](./module-09-credit.md) §4a-b's 5Y CDS on Northern Rail Corp / Northstar Resources Inc. (illustrative reference entities vary slightly between modules — see each module's own cast) and [Module 10](./module-10-valuation.md) §4c's spread-driven MTM math — the asset class where basis points quote the *risk premium itself*, not a benchmark rate.

```text
CDS SPREAD IN BPS — 5Y CDS, BANK OF TORONTO (PROTECTION BUYER) vs.
GOLDMAN SACHS (PROTECTION SELLER)  (Module 9 §4a-b, Module 10 §4c)
========================================================================

  AT INCEPTION:
    Notional: USD 10,000,000
    Running Coupon: 100 bps (the CONTRACTUAL premium Bank Of Toronto
                    pays quarterly, fixed for the trade's life)
    Market Spread at inception: 250 bps (what the market judged the
                    reference entity's ACTUAL credit risk to be worth)

    Because the contractual coupon (100bps) is below the market
    spread (250bps) at trade date, Bank Of Toronto pays an upfront:
      Upfront ~ (2.50% - 1.00%) x ~4.5 (Risky Duration) x 10,000,000
              = USD 675,000

  ONE YEAR LATER -- credit deteriorates, market spread WIDENS to
  400 bps. Remaining Risky Duration ~ 4.0:

    Current Approx. Value = (Market Spread - Running Coupon)
                              x Risky Duration x Notional
                           = (4.00% - 1.00%) x 4.0 x 10,000,000
                           = USD 1,200,000

    Unrealized Gain to Bank Of Toronto (protection buyer)
      = 1,200,000 - 675,000 = USD 525,000

  -> "Spread DV01" (or "CS01," Credit Spread DV01) is the Section 3/4
     DV01 concept applied to a 1bp move in the CREDIT SPREAD instead
     of a benchmark rate -- Risky Duration x Notional x 0.0001 gives
     the dollar value of a 1bp spread move, exactly parallel to bond
     Modified Duration x Market Value x 0.0001.
```

**Why credit spreads move in bps, but so much faster/wider than rates.** A benchmark rate (Section 4) typically moves in 25bp increments at scheduled central bank meetings (Section 8). A credit spread has no such schedule — it can widen or narrow by dozens or hundreds of bps within days on company-specific news (an earnings miss, a ratings downgrade, a covenant breach), because it's pricing a single entity's default risk, not a policy rate shared across the whole market. This is why Credit desks watch spread moves far more continuously than Rates desks watch benchmark-rate moves — the risk factor itself is inherently noisier.

**Takeaway:** a CDS's Running Coupon is fixed at inception and never changes over the trade's life; its *market value* changes entirely because the *market spread* — quoted in bps, moving continuously — diverges from that fixed coupon. This is the same "locked in at trade date vs. what the market offers today" mechanic Section 3 and Section 4 both use, just with Spread DV01 replacing bond/swap DV01, exactly as [Module 10](./module-10-valuation.md) §4c's own takeaway already generalized.

---

## 6. Worked Example — Equity: Basis Points in Fees &amp; Trading Costs (Not the Stock Price)

**Definition:** a new worked example completing the asset-class set — Equity is the clearest illustration that bps do **not** automatically apply to a raw price the way they do in Fixed Income, Rates, and Credit. Where bps genuinely show up in equities is **cost**: management fees and bid-ask spreads, both built on [Module 4](./module-04-equity.md)'s established Maple Leaf Pension Fund positions.

```text
MANAGEMENT FEE IN BASIS POINTS — MAPLE LEAF PENSION FUND'S EQUITY
ALLOCATION  (illustrative, built on Module 4's own KO/AAPL positions)
========================================================================

  Maple Leaf Pension Fund's total equity allocation under management:
  CAD 500,000,000 (illustrative, aggregate across many positions
  including the 10,000-share KO holding from Module 4 §4a)

  Annual Management Fee: 45 basis points (0.45%) of assets under
  management -- the standard way an asset manager, index fund, or
  ETF quotes its fee (an MER, "Management Expense Ratio," is the
  fund-industry name for exactly this bps figure)

  Annual Fee Cost = 500,000,000 x 0.0045 = CAD 2,250,000

  -> Note this is the SAME "bps x 0.0001 x exposure" formula as every
     other section -- here "exposure" is Assets Under Management, not
     a DV01-style sensitivity, and the bps figure is a FEE RATE, not
     a market-observed yield or spread.
```

```text
BID-ASK SPREAD IN BASIS POINTS — AAPL, A LIQUID LARGE-CAP STOCK
========================================================================

  AAPL Bid: USD 259.98 | Ask: USD 260.02
  Absolute Spread = 260.02 - 259.98 = USD 0.04

  Spread in bps = (Absolute Spread / Mid Price) x 10,000
                = (0.04 / 260.00) x 10,000
                = 1.5 bps

  -> Quoting the spread in bps (not just cents) lets a trading desk
     compare execution cost ACROSS stocks with wildly different price
     levels on a common scale -- 1.5bps on AAPL at USD 260 is a
     TIGHTER (cheaper to trade) market than 1.5bps would represent on
     a USD 5 penny stock, in absolute cents, but the BPS figure itself
     is directly comparable across both.
```

**Why the stock price itself usually isn't quoted in bps.** AAPL moving from USD 260.00 to USD 262.60 is a 1.00% move — market commentary calls this "up a percent" or "up 260 points" (for an index) far more often than "up 100bps," even though the arithmetic is identical. This is convention, not a hard rule — some equity derivatives desks (e.g. quoting equity-linked note fees, or dividend-swap spreads) do use bps on equity figures — but the *default* unit for a raw equity price move is percent or index points, not bps. Contrast this directly with Section 3–5: a bond yield, a swap rate, and a CDS spread are **already** percentage-shaped numbers before any move happens, which is why bps attach to them naturally; a stock price is a dollar figure, not a rate, so bps are a deliberate choice (as in the fee and spread examples above) rather than the default.

**Takeaway:** Equity is the asset class that draws the sharpest line in this module between "bps describes a rate/yield/spread/fee" (Sections 3–5, and this section's fee/spread examples) and "bps does NOT automatically describe a raw price" — a distinction a reporting or analytics system needs to get right, because mislabeling a 1% equity price move as "100bps" without qualifying *what* is measured in bps invites confusion with the yield- and spread-based sensitivities every other asset class in this module actually uses bps for.

---

## 7. Worked Example — FX: Basis Points vs. Pips — A Deliberately Different Unit

**Definition:** reusing [Module 6](./module-06-fx.md) §4a's USD/CAD Spot trade (Bank Of Toronto buys USD 10,000,000 @ 1.3500) to introduce **pips**, the unit FX actually quotes moves in — related to bps, but not the same thing, and a genuine source of confusion for anyone assuming every asset class uses bps identically.

```text
PIPS vs. BASIS POINTS — USD/CAD  (Module 6 §4a spot trade)
========================================================================

  USD/CAD Spot Rate: 1.3500 (quoted to 4 decimal places)
  1 PIP = the smallest standard quoted move = 0.0001 -- i.e. the LAST
          decimal place of the quoted rate

  Rate moves from 1.3500 to 1.3520 -- a move of 0.0020
    = 20 PIPS (the FX-market-standard way to describe this move)

  IS THIS "20 BASIS POINTS"? -- Only by COINCIDENCE for this pair,
  and only approximately:
    20 pips / 1.3500 x 10,000 = 14.8 bps of the RATE ITSELF

  -> A pip is a FIXED decimal increment (0.0001 for most pairs); a
     basis point is 0.01% of whatever number it's attached to. For
     USD/CAD near 1.3500, a 1-pip move happens to be close to a
     1bp-of-rate move only because the rate itself is close to 1.00 --
     for a pair trading well above or below 1.00, the two diverge
     more visibly (a JPY pair like USD/JPY quotes to 2 decimals, so
     1 pip = 0.01, a much bigger fraction of a ~150.00 rate).
```

**Why FX doesn't just use bps.** Unlike a bond yield or a swap rate, an FX spot rate isn't inherently a percentage — it's a ratio of two currencies' values, with a decimal convention that varies by pair (4 decimals for most pairs, 2 for JPY crosses). Pips give FX traders a **fixed, pair-specific tick size** to quote moves in, independent of what percentage that tick happens to represent for any given pair — exactly the opposite design choice from bps, which is *always* a percentage-relative unit by definition. Where FX genuinely does use bps directly is **FX Forward points and cross-currency basis** ([Module 6](./module-06-fx.md) §4b's forward points, and [the Cross-Currency Swap example](../../examples/Cross_Currency_Swap_Complete_Example.md)'s basis spread) — both of those are rate-shaped numbers, not spot-rate ticks, which is exactly the pattern Section 6 already established: bps attach to rates, spreads, and fees, not raw price/exchange-rate levels.

**Takeaway:** treating "pips" and "bps" as interchangeable is a common, and usually harmless-for-USD/CAD, simplification that breaks visibly for other pairs — a well-designed FX risk or reporting system should never silently convert one to the other; it should track pips for spot/forward rate moves natively, and only compute a "bps of rate" figure explicitly, and separately, when that's genuinely the number being asked for (as in the cross-currency basis case above).

---

## 8. How Often Do Basis Points Actually Change? — Scheduled Policy Moves vs. Continuous Market Pricing

**Definition:** "basis points" describes a *unit*, not a *frequency* — but the things measured in bps change on very different clocks depending on which one it is, and conflating them is a common source of confusion for someone new to how often a "rate" actually moves.

| What moves | Typical frequency | Typical size per move | Example from this module |
|---|---|---|---|
| **Central bank policy rate** (the rate a benchmark like CORRA/SOFR ultimately tracks) | Scheduled meetings — roughly 8/year for the US Fed, Bank of Canada, ECB, BoE | 25bp increments are the historical norm; 50bp or 75bp moves happen in faster-moving cycles; 0bp ("hold") is common too | Section 4's swap curve moving 4.00% → 4.30% would typically reflect several such moves priced in advance, not one single meeting |
| **Bond yields / YTM** | Continuously, every trading session (often intraday, tick by tick) | Single-digit bps on a quiet day; tens of bps on a volatile one | Section 3's 25bp and 40bp scenarios |
| **Swap rates** | Continuously, tracking the market's evolving expectation of the future path of the policy rate | Similar to bond yields — continuous, small moves most days | Section 4 |
| **Credit spreads** | Continuously, but with sharp company-specific jumps on news (much noisier than rates) | Can move 100+ bps in a single day on a ratings action or earnings shock | Section 5's 250bp → 400bp move over a year |
| **Management fees** | Rarely — set contractually, revisited annually or at fund-restructuring events, not market-driven | N/A — not a market price at all | Section 6 |
| **Bid-ask spreads** | Continuously, widening/narrowing with liquidity conditions minute to minute | Can double or triple in stressed markets even with no price move at all | Section 6 |
| **Collateral / margin haircuts** ([Module 12](./module-12-collateral-margin.md)) | Reviewed periodically by CCPs/counterparties, not continuously | Adjustments often expressed and communicated in bps or percentage points | See [Module 16 — SFTR](../2-reporting/module-16-sftr-reporting.md) for haircut fields |

```text
TWO GENUINELY DIFFERENT "HOW OFTEN" QUESTIONS
========================================================================

  "How often does the POLICY RATE change?"
    -> Roughly 8 scheduled opportunities a year per major central
       bank, and it can also simply NOT move at any given meeting.
       A policy rate is a DECISION, not a continuously observed price.

  "How often does a bond's YIELD / a swap's RATE / a CDS's SPREAD
  change?"
    -> Continuously, every time the market trades -- because these
       are OBSERVED MARKET PRICES reacting to everything (including,
       but not limited to, expectations about the next policy
       decision), not administrative decisions themselves.
```

**Takeaway:** a policy rate changing 25bps eight times a year and a bond's YTM ticking by fractions of a basis point every few seconds during a trading session are **both** "basis point moves," but they answer entirely different questions — one is a scheduled administrative decision, the other is continuous market price discovery that is *partly, but not entirely,* driven by expectations about the first. A risk system's "how stale is this rate" check needs to know which kind of bps figure it's holding.

---

## 9. Basis Points' Impact on Valuation, MTM &amp; YTM — Tying the Mechanism Together

**Definition:** the question this module keeps circling back to — does a basis-point move actually change what a position is worth, what its MTM shows, and what its YTM reads? The answer, consolidated across Sections 3–5: **yes, directly and mechanically, via a "per-basis-point sensitivity" measure that's structurally identical across asset classes.**

| Concept | What it answers | The bps mechanism | Worked in |
|---|---|---|---|
| **Valuation** | What is this position worth right now? | Current Value = f(current YTM / swap rate / spread) — a full DCF or model revaluation off today's curve/spread, expressed to the basis point | [Module 10](./module-10-valuation.md) §3–4; Sections 3–5 here |
| **MTM** (Mark-to-Market) | How much did that worth change since the last valuation? | MTM Change ≈ Basis Points Moved × [DV01 / Swap DV01 / Spread DV01] — the *shortcut* used between full revaluations | Sections 3–5 here |
| **YTM** (Yield to Maturity) | What am I actually earning if I hold this bond to maturity? | YTM itself is quoted to the basis point (Section 3's bond: "5.21%" = 521bps); a *change* in market YTM is what drives the price impact DV01 measures | [Module 5](./module-05-fixed-income.md) §1; Section 3 here |

```text
THE SAME SHAPE, THREE TIMES OVER
========================================================================

  FIXED INCOME:  Price Impact  = -DV01        x Basis Points Moved
  RATES (SWAP):  MTM Change    =  Swap DV01   x Basis Points Moved
  CREDIT (CDS):  Value Change  =  Spread DV01 x Basis Points Moved

  -> Every one of these is "exposure's per-basis-point sensitivity,
     times how many basis points actually moved." Only the sign
     convention (Fixed Income price FALLS as yield RISES; a CDS
     protection buyer's value RISES as spread RISES) and which
     sensitivity measure applies differ by asset class -- the
     arithmetic shape is identical, and it's the same shape
     Section 1 introduced as this module's central formula.
```

**One important nuance — YTM changing does not always mean the coupon changed.** [Module 5](./module-05-fixed-income.md) §1 already establishes that a bond's Coupon Rate is fixed at issuance; YTM moves purely because the bond's *market Price* moves relative to that fixed coupon (or, equivalently, because the price a buyer is willing to pay for that fixed coupon stream has changed). A 25bp YTM increase in Section 3 reflects the *market* re-pricing the same fixed CAD 50/year coupon stream at a less generous price — YTM is a derived, market-driven number recomputed continuously off Price, never a contractual term that itself gets "set" the way a Coupon Rate is.

**Takeaway:** basis points aren't a side detail attached to valuation, MTM, and YTM — they're the **unit these three concepts are measured and moved in**, and the DV01-family sensitivities (Sections 3–5) are the mechanical bridge between "the market moved by this many bps" and "my position's dollar value changed by this many dollars." Getting this mechanism right is precisely why [Module 10](./module-10-valuation.md) exists as this curriculum's highest-priority module.

---

## 10. Basis Points in Regulatory, Collateral &amp; Reporting Context

**Definition:** basis points aren't just a trading-desk convenience — several fields this curriculum's reporting modules already require are, at their core, bps figures, and getting the unit wrong in a reported field is a genuine, recurring operational error.

| Context | Where bps appears | This curriculum's coverage |
|---|---|---|
| **CDS spread reporting** | The "Spread" field on an OTC derivative trade report (EMIR/Dodd-Frank), quoted in bps | [Module 9](./module-09-credit.md), [Module 2](./module-02-hierarchy.md) §9 |
| **Interest rate benchmark resets** | CORRA/SOFR/€STR/SONIA fixings, and swap "spread over benchmark" legs, quoted in bps | [Module 8](./module-08-rates.md) §1 |
| **Collateral/margin haircuts** | Haircut percentages on posted collateral, often communicated and adjusted in bps or percentage points | [Module 12](./module-12-collateral-margin.md), [Module 16 — SFTR](../2-reporting/module-16-sftr-reporting.md) |
| **Repo rates** | The repo rate itself, and the "repo spread" over a benchmark, quoted in bps | [the Repo/SFT worked example](../../examples/Repo_Securities_Financing_Complete_Example.md) |
| **MiFID II / MiFIR price/yield fields** | Reported price and yield fields on Fixed Income and Rates transactions carry bps-level precision requirements | [Module 20 — MiFID Reporting](../2-reporting/module-20-mifid-reporting.md) |

> **Callout — a genuine operational failure mode.** A field that should hold "523" meaning 523 basis points (5.23%) being mistakenly populated with "5.23" (interpreted downstream as 5.23 basis points, i.e. 0.0523%) is a **two-order-of-magnitude** error — exactly the kind of unit-of-measure bug this curriculum's reporting modules ([Module 14](../2-reporting/module-14-regulatory-reporting.md), [Module 20](../2-reporting/module-20-mifid-reporting.md)) flag as a recurring real-world data-quality issue. A reporting system's field-level documentation should always state explicitly whether a numeric field is "in basis points," "in percent," or "in decimal" — never leave it implied.

**Takeaway:** basis points show up as literal reported fields, not just desk shorthand — which means the unit-conversion discipline Section 1 opened with (bps are always additive, always ×0.0001 to decimal) isn't just useful for mental math, it's a genuine data-quality control point in any trade capture or reporting system that touches rate-, spread-, or yield-denominated fields.

---

## 11. Official Sources &amp; Links

**Definition:** as with this curriculum's other reference modules, pointing to primary sources rather than restating technical rules that get revised periodically.

| Source | What It Covers | Link |
|---|---|---|
| **Bank of Canada** | CORRA and Canadian policy rate decisions, scheduled meeting dates | [www.bankofcanada.ca](https://www.bankofcanada.ca) |
| **Federal Reserve (FOMC)** | US policy rate (Fed Funds Rate) decisions and SOFR | [www.federalreserve.gov](https://www.federalreserve.gov) |
| **European Central Bank (ECB)** | Eurozone policy rate decisions and €STR | [www.ecb.europa.eu](https://www.ecb.europa.eu) |
| **Bank of England (BoE)** | UK policy rate (Bank Rate) decisions and SONIA | [www.bankofengland.co.uk](https://www.bankofengland.co.uk) |
| **ISDA** | Standard CDS documentation and running coupon/spread conventions ([ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md)) | [www.isda.org](https://www.isda.org) |
| **ESMA** | MiFID II/MiFIR reporting field precision requirements | [www.esma.europa.eu](https://www.esma.europa.eu) |

**Takeaway:** treat each central bank as the source of truth for scheduled policy-rate moves (Section 8's "administrative decision" clock); ISDA as the source of truth for how a credit spread and running coupon are papered; and ESMA/MiFID as the source of truth for exactly what precision a reported price/yield/spread field must carry — the same three-way split this curriculum's other reference modules already apply to their own areas.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Holds the Canada 5% Bond 2035 (Section 3); pays fixed on the 5Y CAD IRS (Section 4); buys CDS protection on the reference entity (Section 5); executes the USD/CAD spot trade (Section 7) |
| Goldman Sachs | CDS protection seller (Section 5); FX spot counterparty (Section 7) |
| Maple Leaf Pension Fund | Holds the equity allocation subject to the management fee, and the KO/AAPL positions used in the bid-ask spread illustration (Section 6) |
| Bank of Canada / Federal Reserve / ECB / Bank of England | Set scheduled policy rates that anchor CORRA/SOFR/€STR/SONIA and, indirectly, every benchmark rate this module's Rates examples reference (Section 8) |
| ISDA / ESMA | Documentation and reporting-precision standard-setters governing how spread, rate, and yield fields are papered and reported (Sections 10–11) |

---

## Recap Quiz

1. Using Section 1, why is "25 basis points" a more precise, less ambiguous way to describe a rate move than "0.25%," even though they mean the same thing?
2. Using Section 2, name one asset class where basis points do **not** typically describe the instrument's own price, and explain what they measure instead there.
3. Using Section 3, why does the sign of a DV01-based price impact flip depending on whether yields rise or fall, even though the DV01 dollar figure itself stays the same?
4. Using Section 4, why can't a swap's DV01 be reliably estimated as a simple fraction of its Notional, the way one might be tempted to do?
5. Using Section 5, why do credit spreads typically move faster and by larger amounts than benchmark interest rates?
6. Using Section 6, what is the key difference between how bps apply to AAPL's bid-ask spread versus how they'd apply (if at all) to AAPL's raw share price move?
7. Using Section 7, why isn't "20 pips" on USD/CAD exactly the same thing as "20 basis points" of the USD/CAD rate?
8. Using Section 8, what's the difference between "how often does the policy rate change" and "how often does a bond's YTM change," and why do they have such different answers?
9. Using Section 9, what single formula shape connects DV01, Swap DV01, and Spread DV01 across Fixed Income, Rates, and Credit?

---

## Answers — Recap Quiz, with Explanations

**1. Because "0.25%" describes a move of one-quarter of one percentage point, which is easy to misread as "25%" or to confuse with a percentage-of-the-original-value change; "25 basis points" is an absolute, additive unit that every market professional reads identically regardless of the starting rate.**

Section 1's conversion table and "why not just say 0.25%" discussion state this directly — bps are additive, not multiplicative, and unambiguous across different base rates.

**2. Equity — basis points there typically measure management/advisory fees (as a percentage of assets under management) or bid-ask trading spreads, not the stock's own price level, which is conventionally quoted in percent or index points instead.**

Section 6's fee and bid-ask worked examples, and its explicit contrast with Sections 3–5's rate/yield/spread-based uses.

**3. Because bond prices and yields move inversely (Module 1 §1) — DV01 measures the MAGNITUDE of the price impact per basis point, which is the same regardless of direction, but whether that impact is a gain or a loss depends on which way yields actually moved.**

Section 3's Scenario A (yields rise, bond loses CAD 177,300) vs. Scenario B (yields fall, bond gains CAD 283,680) — same CAD 7,092/bp DV01, opposite signs.

**4. Because a swap's DV01 depends on the shape and remaining term of its full cash flow schedule (in this case, 4.5 years of quarterly resets), not just its Notional — Section 4's swap has roughly 10x the bond's Market Value in Notional but only about 5.8x its DV01, showing the relationship isn't a fixed ratio.**

Section 4's "why the same DV01 concept, a different number" discussion, and Module 10 §4a's own caveat that DV01 is the output of a revaluation, never inferred from Notional by a rule of thumb.

**5. Because a credit spread prices a single entity's default risk, which can be driven by company-specific news (earnings, ratings actions, covenant breaches) with no fixed schedule, whereas a benchmark rate is anchored to a policy rate that only moves at roughly 8 scheduled central bank meetings a year.**

Section 5's "why credit spreads move in bps, but so much faster/wider than rates" discussion, and Section 8's table contrasting scheduled policy moves against continuous, sometimes sharp, credit-spread moves.

**6. The bid-ask spread (a cost of trading) is a genuine, deliberate bps figure — computed as the absolute spread divided by mid price — used to compare trading costs across stocks with different price levels. AAPL's raw share price move (e.g. up 1%) is conventionally described in percent or dollars, not bps, even though the same arithmetic could technically produce a bps figure for it.**

Section 6's bid-ask spread worked example, and its "why the stock price itself usually isn't quoted in bps" discussion.

**7. Because a pip is a fixed decimal increment (0.0001 for most pairs, including USD/CAD), while a basis point is always 0.01% of whatever number it's attached to. For USD/CAD near 1.3500 the two happen to be numerically close, but they diverge for pairs quoted at very different rate levels (e.g. USD/JPY, where 1 pip = 0.01, a much smaller percentage of a ~150.00 rate) or different decimal conventions.**

Section 7's worked example computes both figures explicitly (20 pips vs. 14.8bps of rate for the same USD/CAD move) and its "why FX doesn't just use bps" discussion explains the underlying reason.

**8. A policy rate changes only at scheduled central bank meetings (roughly 8/year for major central banks) and is an administrative decision that can also simply not move; a bond's YTM is a continuously observed market price, recomputed every time the bond trades, reacting to everything the market is pricing in — including, but not limited to, expectations about the next policy decision.**

Section 8's two-question breakdown and its table distinguishing "decision" clocks from "continuous market pricing" clocks.

**9. "Exposure's per-basis-point sensitivity, times how many basis points actually moved" — DV01 × bps moved for a bond, Swap DV01 × bps moved for a swap, Spread DV01 × bps moved for a CDS. Only the sign convention and which specific sensitivity measure applies differ; the arithmetic shape is identical across all three.**

Section 9's "the same shape, three times over" diagram states this formula explicitly for all three asset classes side by side.

---

[← Back to Index](../../index.md)
