# Module 11 — Risk &amp; Exposure

[← Back to Index](../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Cross-cutting module** — builds directly on [Module 10](./module-10-valuation.md) §1's Market Value vs Exposure distinction, using positions already established in Modules 6, 8, and 9. Not organized around the eight-part asset-class pattern.

---

## 1. What Is Risk &amp; Exposure? — MTM ≠ Exposure

**Definition:** [Module 10](./module-10-valuation.md) §1 first flagged that Market Value and Exposure are not the same number. This module makes that distinction precise. **MTM** tells you what a position is worth *right now*. **Exposure** tells you how much you stand to *lose* if your counterparty fails to pay — and those are only the same number in one specific direction.

| Term | Meaning |
|---|---|
| Market Value / MTM | The position's current worth ([Module 10](./module-10-valuation.md) §1) — can be positive OR negative |
| Current Exposure (CE) | The loss you'd suffer TODAY if your counterparty defaulted right now — never negative, because you can't "lose" a negative amount |
| Potential Future Exposure (PFE) | An estimate of how large Current Exposure could grow by some future date, given how much the position's value could still move |
| Net Exposure | Exposure after netting multiple trades with the same counterparty under one legal agreement (Section 3) |
| Counterparty Risk | The general risk that the party on the other side of a trade fails to perform its obligations |
| Credit Exposure | Often used interchangeably with Counterparty Risk's *dollar amount* — how much is actually at stake if that failure happens |

```text
WHY MTM AND EXPOSURE AREN'T THE SAME NUMBER
========================================================================

  Current Exposure = MAX(MTM, 0)

  5Y CAD IRS (Module 8 §4a), Bank Of Toronto pays fixed 4.00%:

  Scenario A — 6 months later, MTM = +CAD 1,230,000 (Module 10 §4a)
    -&gt; If Goldman Sachs defaulted RIGHT NOW, Bank Of Toronto would
       lose the CAD 1,230,000 it's owed. Current Exposure = CAD 1,230,000.

  Scenario B — market instead moved the OTHER way, MTM = -CAD 1,230,000
    (Bank Of Toronto now owes Goldman Sachs, not the reverse)
    -&gt; If Goldman Sachs defaulted RIGHT NOW, Bank Of Toronto loses
       NOTHING from this trade -- Bank Of Toronto still owes the
       money. Current Exposure = CAD 0, even though MTM is very
       much non-zero.

  -&gt; A negative MTM is a real number on a P&amp;L report (Module 10 §1)
     but contributes ZERO to Current Exposure. This asymmetry is
     the entire reason the two fields can never be treated as
     synonyms on a risk report.
```

**Takeaway:** Exposure is fundamentally a *one-sided* concept — it only counts the scenarios where you're the one who'd lose money, never the scenarios where your counterparty would. MTM, by contrast, is symmetric: it's exactly as negative for you as it is positive for your counterparty. Confusing the two means either wildly overstating risk (treating every negative MTM as if it were exposure) or, worse, understating it.

---

## 2. Current Exposure vs Potential Future Exposure

**Definition:** Current Exposure answers "what's at risk today." Potential Future Exposure (PFE) answers "what could be at risk at some point before this trade matures" — and for a long-dated derivative, PFE is very often the larger, more consequential number.

```text
PFE — WHY A ZERO-MTM TRADE STILL CARRIES REAL RISK
========================================================================

  5Y CAD IRS, Notional CAD 100,000,000, AT INCEPTION:
    MTM = 0 (Module 10 §4a — a fairly-priced swap starts at zero)
    Current Exposure = MAX(0, 0) = CAD 0

  -&gt; A risk report showing "Current Exposure: CAD 0" on trade date
     is CORRECT, not a broken feed. But it does NOT mean this trade
     is risk-free -- over the next 5 years, rates can move a great
     deal, and Module 10 §4a already showed a 30bp move alone
     producing roughly CAD 1,230,000 of MTM swing after just 6 months.

  POTENTIAL FUTURE EXPOSURE (illustrative, using a standard
  percentile-of-simulated-outcomes approach real risk systems use):
    At a 95% confidence level, 2 years into the trade, PFE might be
    estimated at roughly CAD 4,500,000 -- the swap's notional,
    remaining term, and rate volatility all feed this number, not
    just today's zero MTM.

  -&gt; PFE is why a bank extends a credit limit to a counterparty
     BEFORE trading with them, sized off the WORST plausible future
     exposure, not today's (currently zero) exposure.
```

**Takeaway:** Current Exposure is a snapshot; PFE is a forward-looking estimate that has to account for everything that could still happen to a trade's value before it matures — which is exactly why PFE, not Current Exposure, is the number that drives how much Initial Margin gets posted ([Module 12](./module-12-collateral-margin.md)) and how large a credit limit a bank extends to a given counterparty in the first place.

---

## 3. Netting — Why Gross Exposure Overstates the Real Risk

**Definition:** [Module 2](./module-02-hierarchy.md) §7 introduced the ISDA Master Agreement as the legal contract governing every OTC derivative trade between two counterparties. Its single most consequential feature for risk purposes: **close-out netting** — if a counterparty defaults, every trade under that one Master Agreement collapses into a SINGLE net obligation, not a pile of separate winners and losers.

```text
GROSS EXPOSURE  vs  NET EXPOSURE — Bank Of Toronto &amp; Goldman Sachs,
one ISDA Master Agreement, three outstanding trades
========================================================================

  Trade 1 — 5Y CAD IRS (Module 8 §4a):        MTM = +CAD 1,500,000
  Trade 2 — FX Forward (Module 6 §4b):         MTM = +CAD   900,000
  Trade 3 — CDS, Goldman Sachs as protection
            seller (Module 9 §4a):              MTM = -CAD   400,000

  GROSS POSITIVE EXPOSURE (sum only the trades where Bank Of
  Toronto is owed money -- i.e., pretend netting doesn't exist):
    1,500,000 + 900,000 = CAD 2,400,000

  NET EXPOSURE (sum ALL three MTMs together, exactly what close-out
  netting under the ISDA Master Agreement actually produces if
  Goldman Sachs defaults):
    1,500,000 + 900,000 - 400,000 = CAD 2,000,000

  -&gt; Without netting, a risk system would overstate Bank Of
     Toronto's exposure to Goldman Sachs by CAD 400,000 -- exactly
     the amount of Trade 3, which nets AGAINST the other two rather
     than adding on top of them.
```

> **Callout — a different scenario than Module 10 §4c.** [Module 10](./module-10-valuation.md) §4c revalues this exact same CDS under a scenario where Northstar Resources Inc.'s spread WIDENS, producing a GAIN to Bank Of Toronto's protection position. Here, Trade 3's -CAD 400,000 assumes the opposite illustrative scenario — Northstar Resources Inc.'s spread has instead NARROWED from its original level, leaving Bank Of Toronto's locked-in protection worth less than current market pricing would provide: a loss, not a gain. These are two independent snapshots of the same trade under different market moves, not a contradiction. Separately, the CDS's own notional is USD 10,000,000 (Module 9 §4a); Trade 3's MTM is shown here in CAD because a netting set's exposure is expressed in one common reporting currency across every trade under the Master Agreement, regardless of each trade's own underlying currency.

**IT/reporting angle:** this is why exposure has to be calculated and stored **per legal netting set** (all trades under one ISDA Master Agreement with one counterparty), not per individual trade, and not blindly summed across every counterparty a bank trades with. A trade record's "Netting Set ID" or "Master Agreement ID" field is what tells a risk system which other trades' MTMs it's legally allowed to offset against this one.

**Takeaway:** netting is the single biggest lever reducing real counterparty risk in the OTC derivatives market without a single trade changing hands — it's a pure legal/contractual mechanism, not a market or pricing effect, and it's exactly why the ISDA Master Agreement (not any individual trade confirmation) is the document a credit risk team cares about most.

---

## 4. Counterparty Credit Risk Deep Dive — CVA and Wrong-Way Risk

**Definition:** [Module 10](./module-10-valuation.md) §8's abbreviations table already defined **CVA (Credit Valuation Adjustment)** as an adjustment reflecting counterparty default risk. Here's the mechanism: CVA translates Net Exposure (Section 3) and a counterparty's own creditworthiness into a single dollar charge against the position's value.

```text
CVA — ILLUSTRATIVE CALCULATION
========================================================================

  Net Exposure to Goldman Sachs (Section 03): CAD 2,000,000
  Goldman Sachs' estimated 1-year Probability of Default: 0.50%
  Loss Given Default (i.e., 1 - Recovery Rate, Module 9 §1's
  Recovery Rate concept applied to a COUNTERPARTY rather than a
  CDS Reference Entity): 60%

  Simplified CVA ≈ Net Exposure x Probability of Default x Loss
                    Given Default
                  = 2,000,000 x 0.50% x 60%
                  = CAD 6,000

  -&gt; This CAD 6,000 is subtracted from the position's Fair Value
     (Module 10 §1) specifically because the counterparty MIGHT
     default -- a real accounting adjustment, not just an internal
     risk metric.
```

**Wrong-Way Risk:** the specific, dangerous case where a counterparty's likelihood of default is *correlated* with the exposure itself getting larger — [Module 9](./module-09-credit.md)'s Q4 answer already previewed this exact scenario: Goldman Sachs selling CDS protection on its *own* default risk. If Goldman Sachs starts to fail, Bank Of Toronto's exposure to that CDS position grows (Section 2's PFE effect, credit deteriorating) at the *exact same moment* Goldman Sachs' ability to pay is disappearing — the worst possible correlation a risk model can face, and exactly why "who is the counterparty" and "what does the trade reference" are never independent questions for a genuinely dangerous position.

**Takeaway:** CVA turns Section 1-3's Current/Net Exposure concepts into an actual dollar cost embedded in a position's value; Wrong-Way Risk is the reminder that the *probability* half of that calculation (Goldman Sachs' Probability of Default) isn't always a fixed, independent number — sometimes it moves in lockstep with the very exposure it's being multiplied against.

---

## 5. Cross-Asset-Class Exposure Reference — Bilateral vs CCP

**Definition:** Section 7 in every asset-class module (Modules 4–9, 15) already established which products are bilateral and which are centrally cleared. That same split determines WHO bears the counterparty risk this module has been calculating.

| Asset Class / Product | Counterparty Risk Sits With... | Why |
|---|---|---|
| Cash Equity / Bonds (mostly) | CCP (NSCC, CDCC) for equities; bilateral for most bonds | [Module 4](./module-04-equity.md) §7, [Module 5](./module-05-fixed-income.md) §7 |
| FX Spot/Forward/Swap | Bilateral, mitigated by CLS's PvP (settlement risk, not counterparty risk — [Module 6](./module-06-fx.md) §5) | [Module 6](./module-06-fx.md) §7 |
| Standardized IRS | CCP (LCH SwapClear) — Section 3-4's netting/CVA math still applies, just with the CCP as counterparty instead of Goldman Sachs | [Module 8](./module-08-rates.md) §7 |
| FRA / Cap-Floor / Swaption / IR Forward Debt | Bilateral (mostly) | [Module 8](./module-08-rates.md) §7 |
| CDS Index / liquid Single-Name CDS | CCP (ICE Clear Credit, LCH CDSClear) | [Module 9](./module-09-credit.md) §7 |
| Bespoke Single-Name CDS | Bilateral — Section 4's CVA/wrong-way-risk math applies directly | [Module 9](./module-09-credit.md) §7 |
| Futures / Listed Options | **Always** CCP, from execution ([Module 15](./module-15-listed-derivatives.md) §7) | [Module 15](./module-15-listed-derivatives.md) §7 |

```text
CCP EXPOSURE ISN'T ZERO -- IT'S REDIRECTED
========================================================================

  Bilateral trade: Bank Of Toronto's counterparty risk -&gt; Goldman
                    Sachs directly. CVA math (Section 04) applies
                    to Goldman Sachs' own creditworthiness.

  Centrally cleared trade: Bank Of Toronto's counterparty risk -&gt;
                    the CCP (e.g. LCH SwapClear). Novation (Module
                    3 §6) legally substitutes the CCP for Goldman
                    Sachs -- but Bank Of Toronto STILL has residual
                    exposure to the CCP itself failing, mitigated
                    by the CCP's own layered "default waterfall":
                    the defaulting member's own Initial Margin,
                    then its Default Fund contribution, then other
                    members' mutualized Default Fund contributions,
                    then the CCP's own capital.
```

**Takeaway:** central clearing doesn't make counterparty risk disappear — it concentrates and mutualizes it inside one entity (the CCP) that's specifically structured, capitalized, and regulated to absorb member defaults, which is a fundamentally different (and, for standardized products, generally safer) risk profile than a web of bilateral exposures to every individual trading counterparty, but not literally "zero risk."

---

## 6. Portfolio-Level Risk — Value at Risk (VaR)

**Definition:** [Module 10](./module-10-valuation.md) §8's abbreviations table already defined VaR as a statistical estimate of maximum expected loss over a time horizon at a confidence level. Here's the calculation, reusing [Module 10](./module-10-valuation.md) §3's bond DV01.

```text
1-DAY 99% VaR — Canada 5% Bond 2035 Position (Module 10 §3)
========================================================================

  DV01: CAD 7,092 per basis point (Module 10 §3)
  Assumed daily yield volatility (1 standard deviation): 8 basis points
  99% confidence level ≈ 2.33 standard deviations (a standard
  statistical constant for a normal distribution's 99th percentile)

  1-Day 99% VaR = DV01 x Confidence Multiplier x Daily Volatility (bps)
                = 7,092 x 2.33 x 8
                = CAD 132,235

  -&gt; Interpretation: on 99 out of 100 trading days, this bond
     position should NOT lose more than approximately CAD 132,235
     in a single day, based purely on interest rate moves. On the
     1-in-100 "tail" day, it could lose more -- VaR explicitly does
     NOT bound the worst case, only the 99th-percentile case.
```

**IT/reporting angle:** a single position's VaR (like the bond above) is a useful sensitivity check, but real risk systems compute **portfolio-level** VaR across every position a desk (or the whole bank) holds — and because different positions' value changes aren't perfectly correlated, portfolio VaR is almost always *less* than the simple sum of every individual position's VaR. That diversification benefit is a genuine, calculable number, not just a qualitative "diversification helps" statement — and it's exactly why risk systems need full correlation data across every asset class in this roadmap, not just each position's own volatility.

**Takeaway:** VaR complements, but doesn't replace, Sections 1-5's counterparty-specific Exposure/CVA framework — VaR measures how much a position's *own value* could move against the *market*, while Exposure and CVA measure how much you'd lose if a specific *counterparty* failed to pay. A portfolio can have low VaR and still carry dangerous concentrated counterparty exposure to one name, or vice versa — they're answering different questions, exactly like [Module 10](./module-10-valuation.md) §1 already established for Market Value vs Exposure generally.

---

## 7. IT/Reporting Angle — Exposure Fields, SIMM, and Real-Time vs End-of-Day Risk

**Definition:** three practical system-design realities this module's concepts create.

**Exposure fields aren't Valuation fields:** [Module 10](./module-10-valuation.md) §7 already separated Price/Market Value/Unrealized P&amp;L as three distinct fields. Add three more here — Current Exposure, PFE, and Net Exposure (per netting set) — and none of them should be derived by a downstream consumer guessing from Market Value alone; Current Exposure specifically requires the MAX(MTM, 0) floor from Section 1, which a naive "just show me the MTM" report will get wrong on every trade with negative MTM.

**SIMM (ISDA Standard Initial Margin Model):** a standardized, industry-wide model (built by ISDA, the same body from [Module 2](./module-02-hierarchy.md) §7's Master Agreement) that calculates Initial Margin for non-centrally-cleared derivatives directly from a position's risk *sensitivities* (its DV01-like Greeks, per risk factor) rather than a simple percentage-of-notional estimate — the full mechanics belong to [Module 12](./module-12-collateral-margin.md) (Collateral &amp; Margin), but SIMM is fundamentally a PFE-family calculation (Section 2), reused for a margin-sizing purpose instead of a pure risk-reporting one.

**Real-time vs end-of-day risk:** a Front Office trading desk needs pre-trade risk limit checks in milliseconds (can this NEW trade be added without breaching a limit?) — necessarily a simplified, fast approximation. Overnight, a full portfolio revaluation recomputes VaR, PFE, and CVA precisely, incorporating every position and full correlation data. **These two numbers can legitimately disagree** for the same portfolio at different points in the day, and a reporting system that doesn't clearly label which risk run (real-time approximate vs. end-of-day precise) a number came from will cause genuine confusion when they don't match.

**Takeaway:** every concept in this module — Current Exposure, PFE, Net Exposure, CVA, VaR — needs its own clearly-labeled field, computed on its own cadence (some real-time, some end-of-day), because collapsing them into a single "Risk" number for a trade record destroys exactly the information this module spent seven sections building up.

---

## 8. Regulatory Perspective — SA-CCR, FRTB, CCAR &amp; Uncleared Margin Rules

| Regulation / Body | How it touches Risk &amp; Exposure specifically |
|---|---|
| **BCBS SA-CCR** (Standardized Approach for Counterparty Credit Risk) | The current Basel methodology banks use to calculate regulatory capital against counterparty credit risk on derivatives — directly formalizes Section 1-3's Current Exposure/PFE/netting concepts into a regulator-mandated formula, replacing the older, cruder "Current Exposure Method" |
| **BCBS FRTB** | Already introduced in [Module 10](./module-10-valuation.md) §8 for its valuation-observability capital charge; it separately also sets capital requirements for **market risk** (VaR-family measures, Section 06) on the trading book — the same standard covers two different risk dimensions this roadmap has now met in two different modules |
| **Federal Reserve CCAR** (Comprehensive Capital Analysis and Review) | The US Federal Reserve's annual stress-testing regime for large banks — forces institutions to model portfolio-level losses (Section 06's VaR logic, extended to extreme hypothetical scenarios) and hold enough capital to survive them, a US-specific supervisory exercise without a direct EU/UK equivalent under the same name |
| **EMIR / Dodd-Frank** | Both regimes require OTC derivative reports to a Trade Repository ([Module 2](./module-02-hierarchy.md) §07) to include exposure-related fields alongside valuation fields ([Module 10](./module-10-valuation.md) §08) — Current Exposure and collateral posted are frequently among the mandatory reportable fields, not just the trade's static economics |
| **IOSCO / BCBS Uncleared Margin Rules (UMR)** | The joint framework requiring Initial and Variation Margin on non-centrally-cleared derivatives above certain thresholds — directly consumes this module's PFE (Section 02) and SIMM (Section 07) concepts as its calculation inputs, with the full posting mechanics covered in [Module 12](./module-12-collateral-margin.md) |

**Takeaway:** this module's regulatory section is where market-risk regulation (FRTB, CCAR) and counterparty-credit-risk regulation (SA-CCR, UMR) meet — two genuinely different regulatory concerns that both ultimately trace back to the same underlying question this module opened with: how much could actually be lost, and under what circumstances, from a position whose Market Value alone ([Module 10](./module-10-valuation.md)) never tells the whole story.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Holder of Net Exposure to Goldman Sachs across the netting set (Section 03); bears CVA charge and computes VaR on its bond position |
| Goldman Sachs | Counterparty whose creditworthiness drives the CVA and Wrong-Way Risk discussion (Section 04) |
| LCH SwapClear | CCP absorbing counterparty risk for standardized IRS via its default waterfall (Section 05) |
| ISDA | Author of both the Master Agreement enabling netting (Section 03) and the SIMM methodology (Section 07) |
| BCBS / Federal Reserve | Standard-setters behind SA-CCR, FRTB's market-risk capital charge, and CCAR stress testing (Section 08) |

---

## Recap Quiz — Numbers Edition

1. Using Section 01's formula, if the 5Y CAD IRS's MTM were instead exactly CAD 0 (neither party owing the other), what would Current Exposure be, and does that mean the trade carries zero risk?
2. On Section 03's three-trade netting set, if Trade 3's CDS MTM had instead been -CAD 2,600,000 (a much larger loss to Bank Of Toronto) rather than -CAD 400,000, what would Net Exposure be — and what does a NEGATIVE Net Exposure number actually mean?
3. Using Section 04's CVA formula, if Goldman Sachs' estimated Probability of Default doubled to 1.00% (credit conditions worsened) while Net Exposure and Loss Given Default stayed the same, what would the new CVA be?
4. Why does Section 05 say central clearing "redirects" counterparty risk rather than eliminating it — what specific residual risk remains even after novation to a CCP?
5. Using Section 06's VaR formula, if the assumed daily yield volatility doubled to 16 basis points (a much more volatile market), what would the new 1-Day 99% VaR be for the same bond position?
6. A risk report shows a position's real-time (intraday) VaR and its end-of-day VaR disagreeing by a meaningful amount. Using Section 07, is this necessarily a system bug?
7. Contrast what BCBS SA-CCR and Federal Reserve CCAR each specifically measure, using Section 08 — do they answer the same question about a bank's risk, or different ones?

Answer whenever ready — then move to **[Module 12](./module-12-collateral-margin.md): Collateral &amp; Margin**, which turns this module's PFE and SIMM concepts into the actual mechanics of what gets posted, to whom, and when.

---

## Answers — Recap Quiz, with Explanations

**1. Current Exposure = MAX(0, 0) = CAD 0. This does NOT mean the trade carries zero risk — it means TODAY'S snapshot shows no loss-if-default-right-now, but Potential Future Exposure (Section 02) is still very real for a 5-year trade whose rates can move substantially before maturity.**

Section 02's takeaway states this directly: "a risk report showing 'Current Exposure: CAD 0' on trade date is CORRECT, not a broken feed... it does NOT mean this trade is risk-free." PFE, not Current Exposure, is the number a bank should size credit limits against for exactly this reason.

**2. Net Exposure = 1,500,000 + 900,000 - 2,600,000 = -CAD 200,000. A negative Net Exposure means Bank Of Toronto would actually OWE Goldman Sachs money on close-out, not the reverse — so Bank Of Toronto's Current Exposure to Goldman Sachs (Section 01's MAX(MTM, 0) rule) would be CAD 0, even though individual trades within the netting set still show large positive MTMs.**

This is Section 01's Current Exposure logic applied at the netting-set level instead of the single-trade level — a negative net number, exactly like a negative single-trade MTM, contributes zero to Current Exposure, because Bank Of Toronto isn't the party who stands to lose if Goldman Sachs defaults in this scenario; Bank Of Toronto is the net debtor.

**3. New CVA = 2,000,000 x 1.00% x 60% = CAD 12,000 — exactly double the original CAD 6,000.**

Section 04's CVA formula is a simple product of three factors — Net Exposure × Probability of Default × Loss Given Default — so doubling any single input while holding the other two constant doubles the result linearly. This linearity is exactly why Wrong-Way Risk (Section 04) is so dangerous: it's not just that Probability of Default can rise, it's that it can rise AT THE SAME TIME Net Exposure rises too, compounding rather than just doubling the CVA impact.

**4. Because Bank Of Toronto still has residual exposure to the CCP itself failing — the CCP's own layered default waterfall (the defaulting member's Initial Margin, its Default Fund contribution, other members' mutualized Default Fund contributions, then the CCP's own capital) has to be exhausted before Bank Of Toronto would actually suffer a loss, but that chain of protection isn't infinite.**

Section 05's diagram states this precisely: novation legally substitutes the CCP for the original bilateral counterparty, but Bank Of Toronto's counterparty risk simply moves to a new, differently-structured counterparty (the CCP) rather than vanishing — concentrated and mutualized, and for standardized products generally much safer, but "not literally zero risk."

**5. New 1-Day 99% VaR = 7,092 x 2.33 x 16 = CAD 264,470 — exactly double the original CAD 132,235.**

Section 06's VaR formula scales linearly with daily volatility (holding DV01 and the confidence multiplier constant), the same linear-scaling property Q3's CVA answer demonstrated for a different formula — doubling the volatility input doubles the resulting VaR estimate directly.

**6. No, not necessarily a system bug — Section 07 explicitly states these two numbers "can legitimately disagree" because they're computed on different cadences with different levels of precision: real-time risk uses fast, simplified approximations for pre-trade limit checks, while end-of-day risk recomputes precisely with full portfolio and correlation data.**

Section 07's takeaway calls out the real risk here directly: it's not that the disagreement itself is a bug, it's that a reporting system failing to clearly LABEL which risk run (real-time approximate vs. end-of-day precise) a number came from is what causes genuine, avoidable confusion when the two don't match.

**7. Different questions — BCBS SA-CCR measures how much regulatory capital a bank must hold against COUNTERPARTY CREDIT RISK on its derivatives book (the risk a specific trading partner fails to pay, Sections 01-04's concern); Federal Reserve CCAR measures whether a bank could survive an entire hypothetical MACROECONOMIC STRESS SCENARIO (a broader, portfolio-and-balance-sheet-wide solvency question, extending Section 06's VaR logic to extreme, regulator-designed scenarios rather than statistical percentiles of normal market moves).**

Section 08 distinguishes these directly: SA-CCR formalizes this module's Current Exposure/PFE/netting concepts (Sections 01-03) into a capital formula, while CCAR is a supervisory stress-testing exercise extending Section 06's portfolio-risk logic to hypothetical extreme scenarios — related in spirit (both ultimately protect against a bank failing) but measuring genuinely different things at genuinely different scopes.

---

[← Back to Index](../index.md)
