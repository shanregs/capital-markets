# Module 22 — Hedging Deep Dive — Theory, Worked Examples &amp; Where It's Actually Used

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Reference module**, in the same family as the [Interest Rate Swap Deep Dive](./module-19-interest-rate-swap-deep-dive.md) and the [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md). Hedging examples are scattered across [Module 2](./module-02-hierarchy.md) §9–10, [Module 6](./module-06-fx.md) §4d, [Module 7](./module-07-commodities.md) §4d, and [Module 15](./module-15-listed-derivatives.md) §4c — this module pulls every one of them together under a single theory, adds an Equity worked example to complete the set, and answers the practical "how/where is this actually used" question this curriculum keeps returning to.

---

## 1. What Is Hedging? — Theory &amp; the Three Trading Motivations

**Definition:** **hedging** means deliberately taking on a **second position whose value moves in the opposite direction to an existing risk**, so that a loss on one side is offset — partially or fully — by a gain on the other. The point isn't to make money on the hedge itself; it's to **narrow the range of possible outcomes** on the *combined* position. A perfectly hedged position, by construction, makes roughly the same net result whether the market goes up or down — which sounds unglamorous, and is exactly the point.

The confusing part for a newcomer is that hedging, speculation, and arbitrage all use the **same instruments** — the same swaps, futures, options, and forwards this curriculum has already built. What distinguishes them isn't the trade's shape, it's the trader's starting position:

| Motivation | Does the trader already have an offsetting exposure? | Goal | Example already in this curriculum |
|---|---|---|---|
| **Hedging** | **Yes** — an existing position or known future need | Reduce the *range* of outcomes on the combined position | [Module 15](./module-15-listed-derivatives.md) §4c: Bank Of Toronto sells T-Note futures against bonds it **already owns** |
| **Speculation** | No — a brand-new directional bet, nothing being offset | Profit from a *view* on where price is headed | [Module 4](./module-04-equity.md) §4c/§4d: Maple Leaf Pension Fund buys E-mini futures and an AAPL call to **gain** new market exposure, not offset one |
| **Arbitrage** | N/A — exploits a temporary price gap between two economically equivalent things | Near risk-free profit from mispricing, closed out fast | Not covered elsewhere in this curriculum; mentioned here only to complete the contrast |

```text
SAME INSTRUMENT, OPPOSITE MOTIVATION — THE SHARPEST ILLUSTRATION
========================================================================

  Module 15 §4c:  Bank Of Toronto SELLS 100 CME 10-Year T-Note
                  futures -- because it already OWNS bonds whose
                  value falls when rates rise. The future's loss/gain
                  is designed to CANCEL OUT the bond book's gain/loss.
                  -> THIS IS A HEDGE.

  Module 4 §4c:   Maple Leaf Pension Fund BUYS 20 E-mini S&amp;P 500
                  futures -- with no existing short equity position
                  anywhere being offset. It's taking on NEW market
                  exposure on purpose.
                  -> THIS IS SPECULATION (or more precisely, deliberate
                     exposure-taking -- see Section 10's nuance on
                     "return-seeking" vs. purely directional bets).

  -> Both are Interest-Rate-or-Equity FUTURES. Nothing about the
     CONTRACT TYPE tells you which one is the hedge -- only knowing
     what ELSE the trader holds does.
```

**Takeaway:** if a reporting or risk system tries to infer "is this a hedge?" purely from the instrument type, it will get this wrong every time — hedging is a *relationship between two positions*, not a property of one trade in isolation. Section 9 covers how (and why) systems actually capture that relationship formally.

---

## 2. The Core Mechanic — Offsetting P&amp;L, Not Eliminating Risk

**Definition:** every hedge works by pairing two positions whose profit-and-loss move in opposite directions when the same underlying risk factor (a rate, a price, a spread) moves. The combined P&amp;L line is **flatter** than either position's P&amp;L alone — that flattening *is* the hedge.

```text
THE GENERIC SHAPE OF EVERY HEDGE IN THIS MODULE
========================================================================

  UNDERLYING POSITION P&amp;L:   loses money as risk factor moves one way,
                              gains as it moves the other way
                    +                                    -
                    |                                    |
       (e.g. bond value falls as rates rise)

  HEDGE POSITION P&amp;L:        moves the OPPOSITE way to the underlying
                    -                                    +
                    |                                    |
       (e.g. short future GAINS as rates rise)

  COMBINED P&amp;L:              flatter than either leg alone -- some
                              residual movement usually remains
                              (Section 7's basis risk), but the SWINGS
                              are smaller than holding the underlying
                              position unhedged
```

**Hedging is never free.** The cost shows up in one of two forms, depending on which family of instrument is used (Section 3 develops this fully):

- **Opportunity cost** — a symmetric hedge (forward, future, swap) locks in an outcome, which means giving up the *upside* just as surely as it removes the downside.
- **Explicit premium** — an asymmetric hedge (an option) costs money upfront, like an insurance premium, in exchange for protecting only the downside while keeping the upside.

**Takeaway:** "we hedged the position" does not mean "we made the risk disappear" — it means the range of possible outcomes narrowed, at a cost (either forgone upside or a paid premium). A reporting system that shows a hedged position's P&amp;L as flat-lining to exactly zero, every time, is almost certainly wrong — Section 7's basis risk is the normal case, a perfectly flat combined P&amp;L is the rare exception.

---

## 3. Two Families of Hedge Instruments — Symmetric vs. Asymmetric

**Definition:** every hedge instrument this curriculum has built falls into one of two families, and the choice between them is the single most important practical decision a hedger makes.

| | **Symmetric hedges** | **Asymmetric hedges** |
|---|---|---|
| **Instruments** | Forwards, Futures, Swaps | Options |
| **Upfront cost** | None (or near-none — futures need margin, not premium) | A premium, paid upfront |
| **Payoff shape** | Locks in one exact outcome — dollar-for-dollar offset in *either* direction | One-sided — protects the bad direction, keeps the good direction |
| **Max loss on the hedge itself** | Unbounded in the "wrong" direction (that's the whole point — it's meant to move against the trader when the underlying gains) | Capped at the premium paid |
| **Best analogy** | A fixed-price contract — you're locked in, for better or worse | An insurance policy — you pay a premium, you're only protected against loss |
| **Curriculum examples** | [Module 2](./module-02-hierarchy.md) §10's FX Forward, [Module 15](./module-15-listed-derivatives.md) §4c's IR Future, [Module 2](./module-02-hierarchy.md) §9's CDS (protection premium is periodic, but the *notional* payout structure is fixed by the contract, not optional) | [Module 6](./module-06-fx.md) §4d's FX Option, [Module 7](./module-07-commodities.md) §4d's Commodity Option |

**Takeaway:** Section 6 puts both families side by side against the *exact same underlying need* (Bank Of Toronto's client needing USD in three months) — the clearest possible illustration that this is a real choice with real trade-offs, not just two different products that happen to both be called "hedges."

---

## 4. Worked Example — Interest Rate Hedging (Duration/DV01)

**Definition:** reusing two facts already established elsewhere in this curriculum — [Module 10](./module-10-valuation.md) §3's **DV01** (Dollar Value of 1 Basis Point) for Bank Of Toronto's Canada 5% Bond 2035 position, and [Module 15](./module-15-listed-derivatives.md) §4c's short T-Note futures hedge against that same Fixed Income book.

```text
INTEREST RATE HEDGE — BANK OF TORONTO'S BOND BOOK vs. T-NOTE FUTURES
========================================================================

  THE EXPOSURE (Module 10 §3):
    Canada 5% Bond 2035 position
    Modified Duration: 7.2 | Market Value: CAD 9,850,000
    DV01 = CAD 7,092 per basis point
    -> if rates RISE, this bond position LOSES roughly CAD 7,092
       for every 1bp of rate increase (Module 10 §3's -CAD 177,300
       for a 25bp move is this same DV01 scaled up)

  THE HEDGE (Module 15 §4c):
    Bank Of Toronto SELLS 100 CME 10-Year T-Note futures @ 112'16
    -> if rates RISE, bond futures prices FALL, and a SHORT position
       GAINS -- moving opposite to the bond book's loss
    Next business day, price falls to 111'24: Variation Margin
    GAIN = USD 75,000, credited to Bank Of Toronto's margin account,
    "offsetting the loss on the actual bond portfolio the future is
    hedging" (Module 15 §4c's own wording)
```

**Sizing the hedge — the "hedge ratio" question.** How many futures contracts is the *right* number? Not a notional-matching exercise — matching CAD 9,850,000 of bonds to CAD 9,850,000 of futures notional would be a coincidence, not a hedge, because a bond and a bond future don't have identical price sensitivity to a rate move. The correct approach matches **risk sensitivities**, not face amounts:

```text
HEDGE RATIO — THE GENERAL FORMULA (conceptual, not literally solved
here — Module 15 §4c's 100-contract trade is illustrative, not
derived from this formula against the exact bond position above)
========================================================================

  Number of futures contracts needed  ≈  Exposure's DV01
                                          -----------------------
                                          Future's DV01 per contract

  -> the goal is for the FUTURES POSITION'S total DV01 to
     approximately match the BOND POSITION'S DV01 (CAD 7,092/bp
     here), so a given rate move produces roughly equal and
     opposite dollar moves on each side.
```

**Takeaway:** interest rate hedging is **risk-sensitivity matching**, not notional matching — this is the conceptual leap from [Module 1](./module-01-fundamentals.md) §8's "notional is a reference number, not what moves" applied specifically to hedge sizing. Getting the hedge ratio wrong (using face value instead of DV01) is a common real-world mistake that leaves a position **under-hedged or over-hedged** without anyone realizing it until a large rate move exposes the gap.

---

## 5. Worked Example — Credit Hedging (CDS Against Bond Credit Risk)

**Definition:** reusing [Module 2](./module-02-hierarchy.md) §9's Credit Default Swap in full — the cleanest hedge relationship in this curriculum, because the hedge instrument (a CDS) references the **exact same reference entity** as the exposure being hedged, rather than an approximation.

```text
CREDIT HEDGE — CDS ON NORTHERN RAIL CORP, AGAINST A BOND POSITION
========================================================================

  THE HYPOTHETICAL EXPOSURE (Module 2 §9's own framing): Bank Of
  Toronto holds Northern Rail Corp bonds (or has lent it money
  directly) -- if Northern Rail Corp defaults, the bond loses value.

  THE HEDGE (Module 2 §9, fully worked):
    Bank Of Toronto BUYS PROTECTION from Goldman Sachs
    Notional: CAD 20,000,000 | Spread: 120bps | Maturity: 5 years

    NO DEFAULT (normal case) -- Bank Of Toronto PAYS a quarterly
    premium: CAD 59,836 (the "cost of the hedge" -- this is money
    spent whether or not the bond ever defaults, the same "premium
    even if never used" logic as an insurance policy)

    DEFAULT OCCURS -- Goldman Sachs PAYS Bank Of Toronto:
      Loss Given Default = 20,000,000 x (1 - 40% recovery)
                          = CAD 12,000,000
    -> this payout is designed to roughly OFFSET the loss Bank Of
       Toronto simultaneously takes on the defaulted bond itself
```

**Takeaway:** this is the closest thing to a "perfect" hedge in this module, because the reference entity match is exact — but [Module 2](./module-02-hierarchy.md) §9 already flags the catch: the CDS notional (CAD 20,000,000) has to be sized against the bond exposure's actual size, and — as [Module 2](./module-02-hierarchy.md) §9's own cross-reference notes — if Goldman Sachs (the protection *seller*) is itself more likely to fail exactly when Northern Rail Corp does, that's **wrong-way risk**: the hedge is least reliable exactly when it's needed most. A reference-entity match doesn't automatically mean a counterparty-risk-free hedge.

---

## 6. Worked Example — FX Hedging, Two Ways (Forward vs. Option)

**Definition:** the same underlying need — Bank Of Toronto's client must buy USD in three months — hedged two structurally different ways, reusing [Module 2](./module-02-hierarchy.md) §10's FX Forward and [Module 6](./module-06-fx.md) §4d's FX Option. This is Section 3's symmetric-vs-asymmetric theory made completely concrete.

```text
SAME NEED, TWO HEDGE CHOICES — BUY USD IN 3 MONTHS
========================================================================

  OPTION A -- FX FORWARD (Module 2 §10, symmetric hedge, no premium)
    Locks in TODAY: pay CAD 13,545,000, receive USD 10,000,000,
    in 3 months, no matter what
    -> Spot moves to 1.3700 (USD costlier): Forward SAVED CAD 155,000
    -> Spot moves to 1.3400 (USD cheaper):  Forward COST CAD 145,000
       (compared to just buying at spot on the day)
    LOCKED IN EITHER WAY -- no premium paid, but also NO chance of
    benefiting from a favorable rate move

  OPTION B -- FX OPTION (Module 6 §4d, asymmetric hedge, premium paid)
    USD Call / CAD Put, Strike 1.3600, Notional USD 5,000,000
    Premium Paid: 5,000,000 x 0.0180 = CAD 90,000 (paid regardless
    of outcome -- the cost of the hedge)
    -> Spot moves to 1.3800 (USD costlier): EXERCISE, saves CAD
       100,000 vs. spot; net benefit after premium = CAD 10,000
    -> Spot moves to 1.3400 (USD cheaper): let it EXPIRE, buy USD
       at the cheaper spot rate instead -- maximum loss is the
       CAD 90,000 premium, capped, no matter how far spot falls
```

| | Forward (symmetric) | Option (asymmetric) |
|---|---|---|
| Upfront cost | None | CAD 90,000 premium |
| If rate moves favorably | Locked out of the benefit | Keeps the benefit (minus premium) |
| If rate moves unfavorably | Fully protected, dollar-for-dollar | Fully protected, capped loss = premium |
| Worst case | Gave up upside for nothing (in hindsight) | Lost the premium for nothing (in hindsight) |

**Takeaway:** neither choice is objectively "better" — a Forward is the cheaper, simpler hedge when the hedger is fully certain about the future need and just wants price certainty; an Option costs more but is the right tool when the hedger wants protection *and* is unwilling to give up the chance of a favorable move. This exact trade-off — certainty-with-no-upside vs. protection-with-preserved-upside — recurs identically in Section 8's Equity example, just with a different underlying.

---

## 7. Worked Example — Commodity Hedging &amp; Basis Risk (Imperfect Hedges)

**Definition:** reusing [Module 7](./module-07-commodities.md) §4d's Commodity Option — the curriculum's clearest illustration that a hedge doesn't need to reference the *exact* thing being hedged to be useful, but that shortcut comes with a real cost: **basis risk**.

```text
COMMODITY HEDGE WITH A MISMATCHED UNDERLYING — JET FUEL VIA WTI
========================================================================

  THE ACTUAL EXPOSURE: an airline (Bank Of Toronto's client) is
  exposed to JET FUEL prices rising -- its real operating cost.

  THE HEDGE INSTRUMENT: a WTI Crude Oil Call option (Module 7 §4d)
    Strike USD 80.00/bbl | Notional 100,000 bbl | Premium USD 3.20/bbl
    Premium Paid = USD 320,000

  WHY WTI, NOT JET FUEL? There's no sufficiently liquid, exchange-
  listed jet fuel derivative for the airline to use directly -- WTI
  crude is used as a PROXY, because jet fuel and crude oil prices are
  highly correlated (crude is a major input cost to refining jet
  fuel) but NOT identical.

  THE GAP -- BASIS RISK:
    If WTI rises 10% AND jet fuel also rises roughly 10%, the WTI
    hedge offsets the airline's higher fuel cost well.

    If WTI rises 10% but jet fuel rises only 4% (or falls, e.g. a
    refinery-capacity shock specific to jet fuel, unrelated to crude)
    -- the WTI Call still pays off as if fuel costs rose 10%, but the
    airline's ACTUAL cost only rose 4%. The hedge OVER-corrects.

    Equally, if jet fuel rises MORE than WTI (a widening "crack
    spread" -- the refining margin between crude and refined
    products), the hedge UNDER-corrects, and the airline is still
    left exposed to part of the increase.
```

**Takeaway:** contrast this directly with Section 5's CDS example, where the hedge instrument references the *exact same* reference entity as the exposure — here, the hedge instrument (WTI) and the exposure (jet fuel) are **related but distinct** risk factors, so some risk always survives the hedge. Basis risk isn't a mistake or a flaw in the hedge — it's the unavoidable cost of hedging with the most liquid available instrument instead of a bespoke one that may not exist or may be far more expensive to trade. [Module 7](./module-07-commodities.md) §4d's own framing ("using WTI as a proxy hedge") names this exact trade-off.

---

## 8. Worked Example — Equity Hedging (Protective Put)

**Definition:** a new worked example, built on a position [Module 4](./module-04-equity.md) §4a already established — Maple Leaf Pension Fund's 10,000-share Coca-Cola (KO) holding, bought at USD 68.40 — to complete the asset-class set with the same asymmetric-hedge shape Section 6 already showed for FX.

```text
PROTECTIVE PUT — MAPLE LEAF PENSION FUND HEDGES ITS KO HOLDING
========================================================================
(illustrative strike/premium; the 10,000-share KO position itself is
Module 4 §4a's real worked trade)

  EXISTING POSITION: 10,000 shares KO, bought @ USD 68.40
                      (ISIN US1912161007, Module 4 §4a)

  Ahead of an uncertain earnings announcement, Maple Leaf Pension
  Fund BUYS a protective put instead of selling the shares outright
  -- it wants to keep the position (and any dividend/upside) but cap
  its downside:

  Trade: KO Put, Strike USD 65.00, Notional 10,000 shares, Expiry
         1 month
  Premium: USD 1.20/share
  Premium Paid = 10,000 x 1.20 = USD 12,000

  AT EXPIRY -- Scenario A: KO falls to USD 58.00 (bad earnings)
    Put is in-the-money: EXERCISE, sell at strike 65.00 instead of
    market 58.00 -- avoided loss = (65.00 - 58.00) x 10,000 = USD 70,000
    Net position result vs. holding unhedged: USD 70,000 avoided
    loss, minus the USD 12,000 premium already paid
    -> the STOCK can still be sold at 65.00 regardless of how far
       below that price the market has fallen -- the floor is fixed

  AT EXPIRY -- Scenario B: KO rises to USD 75.00 (good earnings)
    Put expires WORTHLESS -- selling at market (75.00) beats
    exercising at strike (65.00)
    Maple Leaf Pension Fund keeps the FULL upside on its 10,000
    shares, out only the USD 12,000 premium already paid -- exactly
    Module 6 §4d's FX Option pattern, same asymmetric shape
```

**Takeaway:** a protective put is economically identical in *shape* to Section 6's FX Option hedge — pay a premium, keep the upside, cap the downside at the strike — just applied to an equity position instead of a currency payable. Notice also what makes this different from Section 4's futures hedge: Maple Leaf Pension Fund could instead have **sold** E-mini S&amp;P 500 futures against its equity exposure (a symmetric hedge, no premium, but giving up upside too) — the *same* choice between symmetric and asymmetric hedging (Section 3) applies to equities exactly as it does to FX.

---

## 9. Hedge Accounting &amp; the Regulatory Angle — "Hedge" Is a Legal/Accounting Label, Not Just a Trading Strategy

**Definition:** this is the section most relevant to an IT/reporting architect, and the reason "is this trade a hedge?" can't just be answered by eyeballing the P&amp;L pattern. Under **accounting standards** (IFRS 9 internationally, ASC 815 in the US) and under **derivatives regulation** (EMIR, Dodd-Frank), a trade genuinely reducing an existing risk can qualify for materially different treatment than an equivalent speculative trade — but only if it's **formally documented as a hedge at inception**, not just informally understood to be one.

| Consequence of hedge status | What changes | Why it matters operationally |
|---|---|---|
| **Hedge accounting** (IFRS 9 / ASC 815) | The hedge instrument's gain/loss can be recognized in the *same accounting period* as the hedged item's offsetting loss/gain, instead of the two hitting the P&amp;L statement at different times | Without it, a genuinely risk-reducing trade can make *reported* earnings look MORE volatile, not less — because the hedge's mark-to-market moves show up immediately while the hedged item's economics play out over time |
| **EMIR clearing exemption** ([Module 8](./module-08-rates.md) §5's clearing mandate) | A non-financial counterparty using a derivative to hedge a genuine *commercial* risk (not a financial-market bet) can count that trade differently against the NFC+/NFC- clearing threshold test | Determines whether the trade is subject to the mandatory central clearing obligation at all |
| **Dodd-Frank "end-user exception"** | A US non-financial end-user hedging a real commercial risk can be exempt from Dodd-Frank's mandatory clearing requirement for that swap | Same purpose as the EMIR exemption, different jurisdiction, similar documentation burden |

> **Callout — this is a simplified illustration, not the literal current rule text.** Exactly which tests a trade must pass (effectiveness testing thresholds, the precise NFC+/NFC- notional calculation, end-user exception eligibility criteria) are technical, jurisdiction-specific, and revised periodically — the same hedge this curriculum's other reference modules already apply to their own regulatory specifics ([UPI/UTI](../2-reporting/module-18-upi-uti-dsb-deep-dive.md) §2, [SFTR](../2-reporting/module-16-sftr-reporting.md) §7).

```text
WHY THIS MATTERS FOR A TRADE/BOOKING SYSTEM'S DATA MODEL
========================================================================

  A trade being ECONOMICALLY a hedge (its P&amp;L happens to move
  opposite to another position) is NOT the same fact as a trade
  being FORMALLY DESIGNATED a hedge for accounting/regulatory
  purposes -- the second requires:

    -> a "Hedge Designation" flag on the trade record, set AT
       INCEPTION (not retroactively)
    -> a documented link to the SPECIFIC hedged item/exposure
       (which bond, which forecasted cash flow, which portfolio)
    -> ongoing EFFECTIVENESS TESTING data (is the hedge still
       working as designed, checked periodically)

  Without all three captured, a trade that is economically a
  perfectly good hedge (like Section 4's futures trade) still gets
  TREATED as a plain speculative position by accounting and,
  potentially, by the clearing-obligation rules -- an expensive
  gap if the documentation was simply never captured in the system.
```

**Takeaway:** "hedge" carries real legal and accounting weight, not just an informal trading label — a well-designed trade capture system needs an explicit hedge-designation data model (flag, linked exposure, effectiveness-test history) sitting alongside the trade's plain economic terms, precisely because Section 1 already established that the same instrument can be a hedge or speculation depending on facts the trade record itself must capture, not infer.

---

## 10. Where Hedging Is Actually Used — Desk-by-Desk Summary

**Definition:** pulling every example in this module into one map of *who* hedges *what* with *which* instruments across a typical bank and its clients.

| Business function | Typical exposure being hedged | Typical hedge instrument(s) | This module |
|---|---|---|---|
| **Treasury / ALM** (Asset-Liability Management) | Interest rate risk on the bank's own loan/bond book | IR Swaps, IR Futures | Section 4 |
| **Corporate Treasury** (bank's client) | FX payables/receivables from international trade | FX Forwards, FX Options | Section 6 |
| **Corporate Treasury** (energy-consuming client, e.g. an airline) | Commodity input cost (fuel) | Commodity Futures/Options/Swaps | Section 7 |
| **Credit / Loan Portfolio Management** | Credit/default risk on bonds or loans held | CDS (single-name or index) | Section 5 |
| **Asset Managers / Pension Funds** | Portfolio market-value risk on an existing holding | Equity Index Futures (symmetric), Protective Puts (asymmetric) | Section 8 |
| **Dealers / Market Makers** | Risk absorbed from client option flow (a dealer who just sold a client an option now has an unwanted exposure of its own) | Delta-hedging with the underlying instrument, or offsetting options | Not separately worked here — the same offsetting logic as every other section, just run continuously as client flow arrives, rather than once per position |

**Takeaway:** notice every single row reuses instruments this curriculum already built for entirely different purposes (raising financing, expressing a market view, gaining exposure) — hedging isn't a separate product category, it's a **use** of the same product set, distinguished only by the existence of an offsetting exposure (Section 1) and, where it matters legally, by formal hedge documentation (Section 9).

---

## 11. Official Sources &amp; Links

**Definition:** as with this curriculum's other reference modules, pointing to primary sources rather than restating technical rules that get revised periodically.

| Source | What It Covers | Link |
|---|---|---|
| **ISDA** | Standard derivative documentation used to execute most OTC hedges (Master Agreement, CSA) — see the [ISDA Product Taxonomy Deep Dive](../2-reporting/module-21-isda-product-taxonomy-deep-dive.md) | [www.isda.org](https://www.isda.org) |
| **IFRS Foundation** | IFRS 9 — international hedge accounting requirements (designation, effectiveness testing) | [www.ifrs.org](https://www.ifrs.org) |
| **FASB** | ASC 815 — US GAAP's equivalent hedge accounting standard | [www.fasb.org](https://www.fasb.org) |
| **CFTC** | Dodd-Frank's end-user exception for commercial hedgers | [www.cftc.gov](https://www.cftc.gov) |
| **ESMA** | EMIR Article 10 — the NFC+/NFC- clearing-threshold test and its hedging exemption | [www.esma.europa.eu](https://www.esma.europa.eu) |

**Takeaway:** treat ISDA's documentation as the source of truth for how a hedge is actually papered and executed; IFRS/FASB as the source of truth for how a hedge's P&amp;L is recognized in financial statements; and CFTC/ESMA as the source of truth for exactly when a hedging purpose changes a trade's regulatory clearing obligation — the same three-way split (documentation standard, accounting mandate, regulatory mandate) this curriculum's other reference modules already apply to their own areas.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Hedges its bond book with T-Note futures (Section 4); buys CDS protection on Northern Rail Corp (Section 5); hedges a client's FX payable and a client's jet fuel cost as the dealer counterparty (Sections 6–7) |
| Goldman Sachs | Protection seller on the Northern Rail Corp CDS (Section 5); counterparty on the FX Forward (Section 6) |
| Northern Rail Corp | Reference entity for the CDS hedge (Section 5) |
| Maple Leaf Pension Fund | Buys the E-mini S&amp;P 500 future speculatively (Section 1's contrast); hedges its existing KO equity position with a protective put (Section 8) |
| Bank Of Toronto's client (FX) | Hedges a future USD payable, two ways — Forward and Option (Section 6) |
| Bank Of Toronto's client (airline) | Hedges jet fuel cost exposure via a WTI proxy hedge, accepting basis risk (Section 7) |
| ISDA / IFRS Foundation / FASB / CFTC / ESMA | Documentation and accounting/regulatory standard-setters governing how a hedge is executed, recognized, and (where relevant) exempted from clearing (Sections 9, 11) |

---

## Recap Quiz

1. Using Section 1, what single fact distinguishes a hedge from a speculative trade, even when both use the exact same instrument type?
2. Using Section 2, why is it wrong to describe a well-hedged position's combined P&amp;L as "risk-free"?
3. Using Section 3, what does a hedger give up by choosing a symmetric hedge (a Forward) instead of an asymmetric one (an Option), and what do they give up by choosing the Option instead?
4. Using Section 4, why is matching notional amounts (bond market value to futures notional) the wrong way to size an interest rate hedge, and what should be matched instead?
5. Using Section 5, why is the CDS-on-Northern-Rail-Corp hedge described as close to a "perfect" reference-entity match, and what risk does it still carry even so?
6. Using Section 7, what is basis risk, and why does the airline's WTI-based jet fuel hedge carry it while Section 5's CDS hedge (mostly) doesn't?
7. Using Section 8, in what specific way is the KO protective put's payoff shape identical to Section 6's FX Option hedge?
8. Using Section 9, why can a trade be economically a perfectly good hedge and still fail to get hedge accounting or a clearing exemption?

---

## Answers — Recap Quiz, with Explanations

**1. Whether the trader already holds an existing exposure that the new trade offsets. A hedge exists to reduce the range of outcomes on a position the trader already has (or a known future need); speculation is a brand-new directional bet with nothing being offset. The instrument itself (a future, an option, a swap) never reveals which one it is.**

Section 1's side-by-side example makes this explicit: Bank Of Toronto's short T-Note futures (against bonds it already owns) and Maple Leaf Pension Fund's long E-mini futures (with no offsetting position) are the same contract type, opposite motivations.

**2. Because some residual movement almost always remains — basis risk (Section 7), imperfect hedge-ratio sizing (Section 4), or counterparty/wrong-way risk on the hedge itself (Section 5) all mean the combined position's P&amp;L is flatter than either leg alone, not perfectly flat. Hedging narrows the range of outcomes; it doesn't erase it.**

Section 2's takeaway states this directly — "hedging is never free," and the combined P&amp;L diagram shows a flatter, not flat, line.

**3. Choosing the Forward gives up any chance of benefiting from a favorable rate/price move (locked in either way, no premium). Choosing the Option instead costs an upfront premium (paid regardless of outcome) in exchange for keeping that upside.**

Section 3's table and Section 6's worked forward-vs-option comparison state this trade-off directly, with real numbers on both sides (CAD 155,000 forgone upside on the Forward side vs. CAD 90,000 premium on the Option side).

**4. Because a bond and a bond future don't have identical price sensitivity to a given rate move — matching face values would be a coincidence, not a hedge. The correct approach matches risk sensitivities (DV01), so a given rate move produces roughly equal and opposite dollar moves on both the bond position and the futures position.**

Section 4's hedge-ratio formula states this directly: contracts needed ≈ exposure's DV01 ÷ future's DV01 per contract, restating Module 1 §8's "notional is a reference number, not what moves" specifically applied to hedge sizing.

**5. Because the CDS references the exact same reference entity (Northern Rail Corp) as the bond being hedged, rather than an approximation or proxy — unlike Section 7's WTI/jet fuel mismatch. It still carries wrong-way risk: if the protection seller (Goldman Sachs) is more likely to fail exactly when Northern Rail Corp does, the hedge is least reliable exactly when it's needed most.**

Section 5's takeaway states this directly, cross-referencing Module 2 §9's own wrong-way-risk flag.

**6. Basis risk is the residual exposure that survives a hedge because the hedge instrument's price driver is related to, but not identical to, the actual exposure's price driver — here, WTI crude oil vs. jet fuel, which move together but not perfectly (the "crack spread" can widen or narrow). The CDS hedge mostly avoids this because it references the identical reference entity, not a correlated proxy.**

Section 7's worked example walks through both the good-correlation and mismatched-correlation scenarios explicitly, and its takeaway draws the direct contrast with Section 5's exact-match CDS case.

**7. Both pay a premium upfront (CAD 90,000 for the FX Call, USD 12,000 for the KO Put) in exchange for a capped, known maximum loss if the market moves unfavorably, while keeping the full benefit if the market moves favorably — the option simply expires worthless and the premium is the only cost.**

Section 8's takeaway states this explicitly: "economically identical in shape" to the FX Option hedge, just applied to an equity position instead of a currency payable.

**8. Because hedge accounting and clearing exemptions require FORMAL documentation captured at trade inception — a hedge designation, a documented link to the specific hedged item, and effectiveness-testing data — not just the fact that the trade's P&amp;L happens to move opposite to another position. A system that never captures that documentation leaves an economically sound hedge treated as a plain speculative trade for accounting and regulatory purposes.**

Section 9's diagram states this directly: economic hedging and formal hedge designation are two different facts, and only the second one changes accounting/regulatory treatment.

---

[← Back to Index](../../index.md)
