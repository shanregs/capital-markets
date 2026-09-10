# Module 25 — Position Deep Dive — Theory, Worked Examples &amp; Impact on Valuation, MTM, YTM &amp; Daily Regulatory Reporting

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Reference module**, in the same family as the [Basis Points Deep Dive](./module-24-basis-points-deep-dive.md) and the [Hedging Deep Dive](./module-22-hedging-deep-dive.md). "Position" already appears as its own lifecycle stage in every asset class module ([Module 4](./module-04-equity.md) §5, [Module 5](./module-05-fixed-income.md) §5, [Module 6](./module-06-fx.md) §5, [Module 7](./module-07-commodities.md) §5, [Module 8](./module-08-rates.md) §5) and drives [Module 10](./module-10-valuation.md)'s Valuation/MTM math and [Module 11](./module-11-risk-exposure.md)'s VaR — this module pulls every one of those together under a single theory, and adds the "how does a Position actually reach a regulator, and how often" answer this curriculum keeps returning to.

---

## 1. What Is a Position? — Definition &amp; the Three Things Every Position Has

**Definition:** a **Position** is the **net, cumulative holding** in a given instrument that a firm (or a specific book/desk within it) has at a point in time, built up from every trade executed in that instrument so far. A **Trade** is an *event* — it happens once, at one moment, and is immutable once confirmed. A **Position** is a *state* — it persists, changes value every day without any new trade happening, and can be added to, reduced, or closed out entirely by future trades.

```text
TRADE (an event) vs POSITION (a state) — THE DISTINCTION EVERY
OTHER SECTION IN THIS MODULE ASSUMES
========================================================================

  TRADE:     "Bank Of Toronto bought 10,000,000 face value of the
             Canada 5% Bond 2035 on 15-Jan-2026" -- happened once,
             at one price, never changes after Confirmation
             (Module 3 §4)

  POSITION:  "Bank Of Toronto HOLDS 10,000,000 face value of the
             Canada 5% Bond 2035" -- true every single day for as
             long as the bond is held, REVALUED daily even though
             no new trade occurs, and only changes when a NEW trade
             (a further purchase, a partial sale, or maturity)
             actually happens

  -&gt; A reporting system that treats "position" as just "the most
     recent trade" will get this wrong the moment a SECOND trade in
     the same instrument occurs -- Section 2's three position facts
     are what actually accumulate across trades.
```

**The three facts every position carries.** Whatever the asset class, a position is fully described by three things:

| Fact | Question it answers | Example ([Module 5](./module-05-fixed-income.md) §4a) |
|---|---|---|
| **Quantity / Notional** | How much do we hold? | CAD 10,000,000 face value |
| **Direction** | Are we long or short? | Long (we hold the bond; the issuer owes us) |
| **Cost Basis** | What did we pay for it, on average? | Clean Price 98.50 (CAD 9,850,000) |

**Takeaway:** every worked example in this module — Equity, Fixed Income, FX, Rates, Credit, Commodities — is a position built from exactly these three facts, just with a different unit for "Quantity" (shares, face value, currency amount, swap notional, protection notional, contract lots). Section 2 maps exactly where each asset class's position fact lives.

---

## 2. Position Across Asset Classes — Where the "Quantity" Lives

**Definition:** every asset class in this curriculum's roadmap already showed its own POSITION stage in its Trade Lifecycle worked example ([Module 3](./module-03-trade-lifecycle.md) §1's nine-stage model runs **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** for every asset class) — this section collects them side by side.

| Asset class | What "Quantity" means | Long vs. Short | Worked in |
|---|---|---|---|
| **Equity** | Number of shares held | Long = own the shares; Short = borrowed and sold, owe them back | Section 3 |
| **Fixed Income** | Face Value of bonds held | Long = hold the bond (a creditor); Short = rare, but possible via repo/borrow | Section 4 |
| **FX** | A currency BALANCE, not a single quantity — both sides of the pair | Long the base currency / short the quote currency, or vice versa | Section 5 |
| **Rates (IRS)** | Notional referenced by the swap (never itself exchanged) | Long/Short is really "pay fixed" vs "receive fixed" | Section 6 |
| **Credit (CDS)** | Notional of protection bought/sold | Long protection (benefits if credit worsens) vs Short protection (benefits if credit improves) | Section 6 |
| **Commodities** | Number of futures/forward contracts (lots) | Long = bought, benefits if price rises; Short = sold, benefits if price falls | Section 7 |

**Takeaway:** "position" is never a single universal field type — a reporting system's Position table needs an asset-class-aware schema, because Equity/Fixed Income/Commodities positions are genuinely **held quantities of something**, while Rates/Credit positions are **notional references to a contract's terms**, and FX positions are **two linked currency balances, not one number**. Confusing these categories is a common source of "what does Quantity even mean for this row" bugs.

---

## 3. Worked Example — Equity: A Share Position, Marked Daily

**Definition:** reusing [Module 4](./module-04-equity.md) §5's full lifecycle for Maple Leaf Pension Fund's Coca-Cola (KO) trade — the simplest possible position, because a share position is just a running count.

```text
EQUITY POSITION — MAPLE LEAF PENSION FUND, KO  (Module 4 §5)
========================================================================

  TRADE: buy 10,000 shares KO @ USD 68.40 (09:31:04, Module 4 §4a)

  POSITION (after Settlement, T+1): Maple Leaf Pension Fund's
  holding of KO increases by 10,000 shares in the position-keeping
  system -- this is what Module 3 §8's reconciliation checks against
  the custodian (DTC) and CSD records

  VALUATION / P&amp;L -- marked to the current market price EVERY DAY
  after settlement, whether or not any new trade happens:
    Day of purchase:  10,000 x 68.40 = USD 684,000 (cost basis)
    One week later:    10,000 x 71.10 = USD 711,000 (market value)
    Unrealized P&amp;L =  711,000 - 684,000 = +USD 27,000

  -&gt; Note the position ITSELF didn't change all week (still 10,000
     shares) -- only its VALUATION changed, because the position is
     revalued daily against a moving market price. If Maple Leaf
     Pension Fund had instead sold 4,000 shares on day 3, the
     POSITION would drop to 6,000 shares from that day forward, and
     every subsequent valuation would apply to the new, smaller
     quantity.
```

**Takeaway:** an equity position is the cleanest illustration of Section 1's Trade-vs-Position distinction — the 10,000-share Trade happened once; the 10,000-share Position persists and gets revalued every single trading day until a further trade changes the quantity. [Module 4](./module-04-equity.md) §5's own note that "the ongoing POSITION is separately subject to periodic position reporting requirements in some jurisdictions" is exactly Section 9's regulatory thread, picked up in full there.

---

## 4. Worked Example — Fixed Income: A Bond Position, Marked Daily With Accrual

**Definition:** reusing [Module 5](./module-05-fixed-income.md) §5's Canada 5% Bond 2035 position — one layer more complex than Equity, because a bond position accrues interest daily on top of its price moving.

```text
FIXED INCOME POSITION — BANK OF TORONTO, CANADA 5% BOND 2035
(Module 5 §5)
========================================================================

  TRADE: buy CAD 10,000,000 face value @ Clean Price 98.50
         (Dirty Price CAD 9,932,418, Module 1 §1's accrued interest)

  POSITION (after Settlement, T+2): Bank Of Toronto's Fixed Income
  book increases by CAD 10,000,000 face value of this bond

  VALUATION / P&amp;L -- marked daily to the current market Clean
  Price, PLUS newly-accruing interest each day:
    Settlement day:  Clean 98.50, Accrued 82,418
    One week later:  Clean 98.65, Accrued 106,164
      Price P&amp;L   = (98.65 - 98.50)/100 x 10,000,000 = +CAD 15,000
      Accrual P&amp;L = 106,164 - 82,418                = +CAD 23,746

  -&gt; The bond POSITION (CAD 10,000,000 face value) is unchanged all
     week -- exactly like Section 3's equity example -- but its
     valuation has TWO moving parts instead of one: the market's
     changing view of the Clean Price, and the mechanical daily
     accrual of Coupon Rate x days elapsed (Module 5 §1).
```

**Why position SIZE is what turns a basis-point move into a dollar figure.** [Module 24](./module-24-basis-points-deep-dive.md) §3's DV01 (CAD 7,092 per basis point) is computed directly off this exact position's Market Value (CAD 9,850,000) — DV01 = Modified Duration × **Market Value** × 0.0001. If Bank Of Toronto instead held CAD 20,000,000 face value of the identical bond, DV01 would double to roughly CAD 14,184/bp, for the exact same 25bp yield move. **Position size is the multiplier that turns a per-unit sensitivity into an actual dollar P&amp;L** — the yield itself (and the bond's Modified Duration, a property of its cash flow schedule) doesn't depend on how much of it any one firm holds, but the total DV01 in dollars absolutely does.

**Takeaway:** Fixed Income makes explicit something Section 3's equity example only implied — a position's valuation depends on both a per-unit market observation (Clean Price, or YTM) and the position's own size, and those two inputs change on entirely different logic: price/yield is set by the whole market; size only changes when Bank Of Toronto itself trades.

---

## 5. Worked Example — FX: A Position Is Actually Two Linked Currency Balances

**Definition:** reusing [Module 6](./module-06-fx.md) §5's USD/CAD Spot trade — the asset class where "position" genuinely isn't a single number, unlike Sections 3–4.

```text
FX POSITION — BANK OF TORONTO, USD/CAD  (Module 6 §5)
========================================================================

  TRADE: buy USD 10,000,000 @ Spot Rate 1.3500 (pay CAD 13,500,000)

  POSITION (after Settlement, T+1): Bank Of Toronto's USD cash
  position INCREASES by USD 10,000,000; its CAD cash position
  DECREASES by CAD 13,500,000 -- ONE trade, TWO position changes,
  in two different currencies

  VALUATION / P&amp;L -- the open FX position is marked to the current
  spot rate daily, until the underlying USD is used or sold:
    Trade day:       USD 10,000,000 @ 1.3500 = CAD 13,500,000
    One week later:  USD 10,000,000 @ 1.3550 = CAD 13,550,000
    Unrealized P&amp;L = 13,550,000 - 13,500,000 = +CAD 50,000

  -&gt; Unlike Sections 3-4's single-instrument holding, an FX
     position is a PAIR of balances that must always be read
     together -- a "USD 10,000,000 position" only means something
     once you also know it's funded by a CAD 13,500,000 short
     position on the other side. A reporting system that shows FX
     positions per-currency, in isolation, loses the fact that
     they're economically one single trade's two halves.
```

**Takeaway:** FX is the asset class that breaks the assumption "one position = one number" hardest — every FX position is really a currency pair's worth of two simultaneous, opposite-signed balances, both of which need their own daily revaluation (Section 9's SFTR/EMIR daily-valuation obligations apply the same underlying logic here as to a single-instrument bond or swap position).

---

## 6. Worked Example — Rates &amp; Credit: A Position That's Pure Notional, Not a Held Quantity

**Definition:** reusing [Module 8](./module-08-rates.md) §5's 5Y CAD IRS and [Module 9](./module-09-credit.md) §5's CDS on Northstar Resources Inc. — the two asset classes where a "position" isn't a quantity of something owned at all, just a notional reference inside a live contract.

```text
RATES POSITION — BANK OF TORONTO, 5Y CAD IRS  (Module 8 §5)
========================================================================

  TRADE: pay FIXED 4.00%, receive FLOATING (CORRA), Notional
         CAD 100,000,000, Trade ID BOT-IRS-2026-034410

  POSITION: Bank Of Toronto's Rates book shows ONE 5Y IRS, CAD
  100,000,000 notional -- there is no "quantity of swaps" the way
  there's a quantity of shares; the position IS the contract's
  remaining terms (notional, fixed rate, maturity, reset schedule)

  VALUATION / P&amp;L -- marked-to-market between quarterly
  settlements using a discounted cash flow model referencing the
  current CORRA curve (Module 10 §4a):
    Rate moves 4.00% -&gt; 4.30% (30bp rise)
    Approx. MTM Gain = Swap DV01 x bps moved = 41,000 x 30
                      = CAD 1,230,000 (Module 24 §4)
```

```text
CREDIT POSITION — BANK OF TORONTO, CDS ON NORTHSTAR RESOURCES INC.
(Module 9 §5)
========================================================================

  TRADE: BUY 5Y protection, USD 10,000,000 notional, Running
         Coupon 100bps, vs Goldman Sachs

  POSITION: Bank Of Toronto's Credit book shows ONE CDS, USD
  10,000,000 notional of protection BOUGHT -- again, notional
  referenced inside a live contract, not a quantity physically held

  VALUATION / P&amp;L -- marked-to-market as the market spread moves
  away from the fixed Running Coupon (Module 24 §5):
    Market spread widens 250bps -&gt; 400bps over one year
    Unrealized Gain = USD 525,000 to Bank Of Toronto (protection
                       buyer benefits as credit worsens)
```

**Why this matters for a position-keeping system's data model.** A Rates or Credit "position" table cannot reuse an Equity/Fixed Income position table's assumption that Quantity is a simple running total of buys minus sells of an identical, fungible unit (a share, a bond). An IRS or CDS position is instead **one row per live contract**, carrying that contract's own notional, rate/spread terms, and remaining schedule — closing out the position means terminating or offsetting the specific contract, not "selling some quantity" the way a share position works.

**Takeaway:** Sections 3–5 (Equity, Fixed Income, FX) and this section (Rates, Credit) represent the two fundamentally different position shapes this curriculum's six asset classes produce — **quantity-held positions** (fungible units accumulate) versus **contract-referencing positions** (one row per live derivative, valued off its own notional and terms) — and [Module 24](./module-24-basis-points-deep-dive.md) §4–5's Swap DV01 / Spread DV01 are exactly the tools that convert the second kind into a dollar MTM figure the same way DV01 does for the first kind.

---

## 7. Worked Example — Commodities: A Position That's Cash-Settled Every Single Day

**Definition:** reusing [Module 7](./module-07-commodities.md) §5's CME WTI Crude Future — the sharpest illustration in this curriculum of a position whose valuation change is realized in cash daily, not just tracked on paper.

```text
COMMODITY FUTURES POSITION — BANK OF TORONTO, 50 WTI CONTRACTS
(Module 7 §5)
========================================================================

  TRADE: buy 50 front-month WTI contracts @ USD 75.50/bbl,
         novated to CME Clearing immediately at execution

  POSITION: Bank Of Toronto's Commodity book shows 50 WTI
  contracts, front-month, until rolled or closed

  DAILY VARIATION MARGIN -- CME Clearing marks the position to the
  daily settlement price and moves CASH every day, not just at expiry:
    Day 1 settle USD 76.00: Bank Of Toronto RECEIVES
      50 x 1,000 x (76.00 - 75.50) = USD 25,000
    Day 2 settle USD 75.20: Bank Of Toronto PAYS
      50 x 1,000 x (76.00 - 75.20) = USD 40,000

  -&gt; Because variation margin already moved the cash, P&amp;L and cash
     flow are the SAME number every day for an exchange-traded
     future -- unlike Section 4's bond (paper P&amp;L, cash only moves
     at sale/maturity) or Section 6's OTC swap (paper P&amp;L between
     quarterly settlements, unless a CSA requires daily VM too,
     Module 12).
```

**Takeaway:** exchange-traded futures collapse Section 1's distinction between "the position's valuation changed" and "cash actually moved" into a single daily event — a genuinely different operational pattern from every other worked example in this module, and the reason futures desks watch daily cash movements as closely as they watch the position's paper MTM.

---

## 8. How Often Does a Position Actually Change? — Three Different Clocks

**Definition:** "position" changes on three genuinely different clocks, and conflating them is a common source of confusion for a reporting system trying to answer "is this position's data stale?"

| What changes | Trigger | Frequency | Example |
|---|---|---|---|
| **Position quantity/notional** | A new trade (buy, sell, partial close, contract termination) | Only when a trade actually happens — could be seconds apart or months apart | Section 3's KO position stays 10,000 shares until Maple Leaf Pension Fund trades again |
| **Position valuation (MTM)** | The market moving — a new price, yield, rate, or spread observed | Continuously during market hours; formally snapshotted at End of Day for the official book of record | Sections 3–7's daily valuation examples |
| **Corporate/contractual events** | An event initiated by the issuer or the contract's own terms, not a trade | Irregular, event-driven — a stock split, a bond coupon payment, a swap's quarterly reset, a future's expiry/roll | [Module 4](./module-04-equity.md)'s Corporate Actions; Section 6's quarterly IRS reset; Section 7's futures roll |

```text
TWO GENUINELY DIFFERENT "HOW OFTEN" QUESTIONS -- THE SAME SHAPE
AS MODULE 25 §8's BPS-FREQUENCY DISTINCTION
========================================================================

  "How often does the POSITION QUANTITY change?"
    -&gt; Only when Bank Of Toronto (or Maple Leaf Pension Fund) itself
       trades -- an ADMINISTRATIVE/BUSINESS decision, not a
       continuously observed number.

  "How often does the POSITION'S VALUE change?"
    -&gt; Continuously, every time the market moves -- and, separately,
       FORMALLY at End of Day, when the position-keeping system
       takes one authoritative snapshot that becomes "today's
       official position" for every downstream process (risk, P&amp;L,
       regulatory reporting, Section 9).
```

**Intraday (real-time) position vs. End-of-Day (official) position.** A trading desk's front-office system typically shows a **real-time** running position throughout the day, updating with every fill. But the position that feeds risk, P&amp;L sign-off, and regulatory reporting is almost always the **End-of-Day (EOD) snapshot** — a single, reconciled, official number captured once per business day ([Module 3](./module-03-trade-lifecycle.md) §8's reconciliation is exactly the control that certifies this snapshot is correct). The two can legitimately disagree mid-day without either being "wrong" — they're answering different questions, the same way [Module 24](./module-24-basis-points-deep-dive.md) §8 distinguished a scheduled policy rate from a continuously observed market yield.

**Takeaway:** a position's *quantity* changes only on trade events; its *valuation* changes continuously but is officially captured once a day; and *corporate/contractual events* change it on their own, event-specific schedule unrelated to trading activity at all. A system's "as-of" timestamp on any position figure needs to say which of these three clocks it's reporting against.

---

## 9. Position's Impact on Valuation, MTM &amp; YTM

**Definition:** Position is not a passive label sitting next to a valuation number — it is one of the two direct multiplicative inputs (alongside a market-observed price/yield/rate/spread) that *produces* Valuation and MTM, and it interacts with YTM in a specific, easy-to-get-backwards way.

| Concept | What it answers | How Position drives it |
|---|---|---|
| **Valuation** | What is this holding worth right now? | Valuation = Position Quantity/Notional × current market Price/Rate/Spread — Sections 3–7 all compute exactly this |
| **MTM** | How much did that worth change since the last valuation? | MTM Change = Position size × (bps moved × per-unit sensitivity) — [Module 24](./module-24-basis-points-deep-dive.md) §9's DV01/Swap DV01/Spread DV01 formulas, scaled by Section 4's position-size multiplier |
| **YTM** | What am I earning if I hold this bond to maturity? | YTM is a **per-unit** figure — it does not change with position size at all; only the *total dollar* P&amp;L impact of a YTM move scales with how much face value is held |

```text
THE KEY NUANCE -- YTM DOESN'T CARE HOW BIG THE POSITION IS,
BUT THE DOLLAR IMPACT OF A YTM MOVE DOES
========================================================================

  Bank Of Toronto holds CAD 10,000,000 face value, YTM 5.21%
  (Module 10 §3) -- a SECOND desk holding CAD 100,000,000 face
  value of the IDENTICAL bond has the EXACT SAME YTM, 5.21%. YTM
  is a property of the bond's price/coupon/maturity, not of who
  holds how much.

  But a 25bp YTM rise costs:
    Bank Of Toronto's CAD 10,000,000 position:  -CAD 177,300
    The other desk's CAD 100,000,000 position:  -CAD 1,773,000
      (10x the position size = 10x the dollar impact, same YTM
      move, same DV01-per-unit)

  -&gt; Confusing "the position's YTM" with "the position's dollar
     sensitivity" is a common reporting-system mix-up: YTM is a
     RATE (unaffected by size); DV01 in dollars is a POSITION-SIZED
     figure (directly proportional to size).
```

**Takeaway:** every valuation and MTM figure in this curriculum reduces to "position size × a market-observed rate or price," and getting position size wrong (a stale trade missed, a partial close not applied) produces a valuation error that scales linearly with the size of the mistake — exactly why [Module 3](./module-03-trade-lifecycle.md) §8's reconciliation (position tie-out against custodian/CCP records) is treated as a hard control, not an optional check.

---

## 10. Position in Risk Systems — VaR, Exposure &amp; the Reconciliation Control

**Definition:** [Module 11](./module-11-risk-exposure.md) §6 already computes Value at Risk directly off a position — this section makes explicit that VaR, like MTM, is fundamentally a position-sized calculation.

```text
1-DAY 99% VaR — CANADA 5% BOND 2035 POSITION  (Module 11 §6)
========================================================================

  Position: CAD 10,000,000 face value (Section 4 here)
  DV01: CAD 7,092 per basis point (position-sized, Module 24 §3)
  Assumed daily yield volatility: 8 basis points
  99% confidence multiplier: ~2.33

  1-Day 99% VaR = DV01 x Confidence Multiplier x Daily Volatility (bps)
                = 7,092 x 2.33 x 8
                = CAD 132,235

  -&gt; Note this ENTIRE calculation is downstream of the position's
     size -- double the position, double the DV01, double the VaR,
     for the identical bond and identical market volatility
     assumption.
```

**Why position reconciliation is a risk control, not just an operations task.** If the position feeding this VaR calculation were wrong — say, understated by a missed trade — every downstream number (VaR, MTM, regulatory reporting, Section 9) would be wrong in exactly the same proportion, silently. [Module 3](./module-03-trade-lifecycle.md) §8's three-way reconciliation (internal books vs. custodian vs. CCP/Trade Repository records) exists specifically to catch this class of error before it propagates into every position-derived number a firm produces.

**Takeaway:** a position isn't just a number sitting in a database row — it's the shared input every downstream valuation, MTM, VaR, and regulatory calculation in this curriculum depends on, which is exactly why getting it reconciled and correct is treated as a first-class control, not an afterthought.

---

## 11. Position Reporting to Regulators — Daily, Periodic &amp; Real-Time

**Definition:** [Module 14](../2-reporting/module-14-regulatory-reporting.md) §1 already establishes that "regulatory reporting" is five distinct obligations, not one — this section focuses specifically on the ones that report a **Position** (a standing state) rather than a **Trade** (a one-time event), and on how often each one actually runs.

| Regime | What gets reported about the Position | Frequency | This curriculum's coverage |
|---|---|---|---|
| **EMIR / Dodd-Frank Derivatives Reporting** | Daily valuation updates for every OTC derivative position that remains open — not just the trade at inception | **Daily**, for the life of the position | [Module 8](./module-08-rates.md) §5's daily Variation Margin; [Module 10](./module-10-valuation.md) §4a |
| **SFTR (Securities Financing)** | Daily valuation/collateral updates for every open repo/securities-lending position | **Daily**, for the life of the position | [Module 16 — SFTR](../2-reporting/module-16-sftr-reporting.md) §6b's worked Day 1 → Day 2 example |
| **MiFID II Position Reporting (commodity derivatives)** | STANDING positions in position-limit-subject products, to spot concentration risk | **Periodic** (typically weekly to the trading venue, which reports on to ESMA/regulators) — a genuinely different cadence from trade-level reporting | [Module 7](./module-07-commodities.md) §8's position limits; [Module 14](../2-reporting/module-14-regulatory-reporting.md) §1's Position Reporting row |
| **CFTC Large Trader Reporting** (US) | Large concentrated positions in futures/options above a reporting threshold | **Daily** submission by clearing members once a trader crosses the threshold | Parallel US regime to MiFID's EU position-limit reporting |
| **Exchange-traded futures margining** (CME/ICE/NGX) | The position's daily settlement-price revaluation, driving Variation Margin cash movements | **Daily**, automatically, as part of clearing | [Module 7](./module-07-commodities.md) §5 |

```text
WORKED EXAMPLE -- SFTR's DAILY VALUATION UPDATE, DAY 1 -&gt; DAY 2
(Module 16 §6b, same repo position, Gilt collateral)
========================================================================

  Day 1: Collateral market value = GBP 20,250,000
  Overnight: Gilt price falls 0.5%
  Day 2: Collateral market value = 20,250,000 x 0.995
                                  = GBP 20,148,750  (down GBP 101,250)

  -&gt; A NEW valuation-update report is submitted for THIS SAME
     POSITION on Day 2, even though NO NEW TRADE happened -- the
     position itself (the repo's principal, term, and rate) is
     unchanged; only its market-observed collateral value moved,
     and SFTR requires that moved value to be reported, daily, for
     as long as the position stays open (Section 8's "position
     valuation changes continuously, snapshotted daily" clock).
```

```text
TRADE-LEVEL REPORTING vs POSITION-LEVEL REPORTING -- A GENUINE
FORK IN THE ROAD (Module 14 §1)
========================================================================

  Bank Of Toronto's 5Y CAD IRS (Module 8 §5) with Goldman Sachs:

  TRADE-LEVEL (EMIR/Dodd-Frank Derivatives Reporting):
    -&gt; ONE initial report at execution (new UTI), THEN a fresh
       valuation-update report EVERY DAY the position stays open --
       genuinely a mix of "event-driven" (the initial trade) and
       "position-driven" (every day after)

  POSITION-LEVEL (MiFID Position Reporting):
    -&gt; NOT triggered at all for a plain IRS -- only relevant if
       Bank Of Toronto's cumulative position in a POSITION-LIMIT-
       SUBJECT product (a commodity derivative, Module 7 §8) crosses
       a regulatory threshold. A single trade's size is irrelevant;
       only the STANDING, cumulative position across many trades
       matters here.
```

**Takeaway:** "how often does a position get reported to a regulator" has no single answer — EMIR/SFTR effectively require a **daily** valuation report for as long as a derivative or SFT position stays open (turning Section 8's continuous MTM clock into a formal daily regulatory obligation), while MiFID's commodity Position Reporting is a **periodic, concentration-focused** check on a completely different cadence and a completely different question ("is one participant too big in one product?" rather than "what is this position worth today?"). A reporting system's rules engine needs both clocks wired in separately — one does not substitute for the other.

---

## 12. Official Sources &amp; Links

**Definition:** as with this curriculum's other reference modules, pointing to primary sources rather than restating technical rules that get revised periodically.

| Source | What It Covers | Link |
|---|---|---|
| **ESMA** | EMIR daily valuation reporting requirements (RTS on reporting); MiFID II Article 57/58 commodity derivative position limits and position reporting | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **CFTC** | Dodd-Frank swap data reporting (including daily valuation updates); Large Trader Reporting for futures/options | [www.cftc.gov](https://www.cftc.gov) |
| **DTCC** | Trade Repository infrastructure (GTR) accepting the daily position-valuation updates this module's Section 11 describes | [www.dtcc.com](https://www.dtcc.com) |
| **ISDA** | Standard derivative documentation underlying the positions this module's Rates/Credit examples reference | [www.isda.org](https://www.isda.org) |
| **ICE / CME** | Exchange clearing-house rulebooks governing daily futures position marking and Variation Margin | [www.theice.com](https://www.theice.com), [www.cmegroup.com](https://www.cmegroup.com) |

**Takeaway:** treat ESMA and the CFTC as the source of truth for exactly which positions must be reported, how often, and under which regime; DTCC/ICE/CME as the source of truth for the actual infrastructure a position-valuation report or a Variation Margin call physically flows through — the same split this curriculum's other reference modules already apply to their own areas.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Holds the bond, IRS, CDS, WTI futures, and USD/CAD FX positions used throughout (Sections 4–7, 10–11) |
| Maple Leaf Pension Fund | Holds the KO equity position (Section 3) |
| Goldman Sachs | FX/IRS/CDS counterparty across several worked examples (Sections 5–6) |
| Northstar Resources Inc. | Reference entity for the CDS position (Section 6) |
| CME Clearing | Marks the WTI futures position daily and moves Variation Margin cash (Section 7) |
| ESMA / CFTC | Set the daily (EMIR/SFTR/Dodd-Frank) and periodic (MiFID position limits) regulatory position-reporting clocks (Section 11) |

---

## Recap Quiz

1. Using Section 1, what is the fundamental difference between a Trade and a Position, and why does a system that treats "position" as just "the latest trade" break the moment a second trade occurs?
2. Using Section 2, why can't a Rates or Credit position table reuse the same "Quantity = running total of buys minus sells" logic that works for Equity or Fixed Income?
3. Using Section 4, why does doubling a bond position's face value double its DV01, even though the bond's YTM and Modified Duration stay exactly the same?
4. Using Section 5, why is an FX position described as "two linked currency balances" rather than a single number?
5. Using Section 7, what makes an exchange-traded futures position's daily P&amp;L and daily cash flow the *same* number, unlike a bond or an OTC swap position?
6. Using Section 8, what are the three different "clocks" a position changes on, and which one does a stock split fall under?
7. Using Section 9, why can two different desks holding the same bond in different quantities have an identical YTM but a very different dollar P&amp;L from the same yield move?
8. Using Section 11, why does a single 5Y IRS trigger EMIR daily valuation reporting but NOT MiFID Position Reporting, and what would have to be true for MiFID Position Reporting to apply instead?

---

## Answers — Recap Quiz, with Explanations

**1. A Trade is a one-time event that never changes once confirmed; a Position is a persisting state, built cumulatively from every trade in an instrument, that gets revalued continuously even when no new trade happens. A system treating "position" as "the latest trade" loses the cumulative running total the moment a second trade needs to be added to (or netted against) the first.**

Section 1's Trade-vs-Position diagram states this directly, and flags exactly this failure mode.

**2. Because an Equity/Fixed Income position accumulates a running total of a fungible, identical unit (a share, a bond), while a Rates or Credit position is one row per live contract, valued off that specific contract's own notional and terms — there's no "quantity of swaps" to sum the way there's a quantity of shares.**

Section 6's "why this matters for a position-keeping system's data model" discussion states this explicitly, contrasting quantity-held positions with contract-referencing positions.

**3. Because DV01 = Modified Duration × Market Value × 0.0001 — Modified Duration and YTM are properties of the bond's own cash flow schedule and price, unaffected by how much any one firm holds, but Market Value (and therefore DV01 in dollars) scales directly with position size.**

Section 4's "why position size is what turns a basis-point move into a dollar figure" discussion, and Section 9's worked CAD 10,000,000 vs. CAD 100,000,000 comparison.

**4. Because a single FX trade always changes TWO currency balances simultaneously and in opposite directions (buying USD necessarily means selling CAD) — reading either balance in isolation loses the fact that they're economically one trade's two linked halves.**

Section 5's worked USD/CAD example: buying USD 10,000,000 increases the USD position AND decreases the CAD position by CAD 13,500,000, from the same single trade.

**5. Because the position is novated to a CCP (CME Clearing) that marks it to the daily settlement price and moves Variation Margin CASH every single day — so the paper valuation change and the actual cash flow are identical, unlike a bond (cash only moves at sale/maturity) or an OTC swap (cash only moves at quarterly settlement, absent a CSA requiring daily VM).**

Section 7's worked WTI futures example states this directly, contrasting it with Sections 4 and 6.

**6. Position quantity/notional (changes only when a trade happens), position valuation/MTM (changes continuously, snapshotted formally at End of Day), and corporate/contractual events (irregular, event-driven, not trade-driven). A stock split falls under the third clock — it changes the position's share count without any trade occurring at all.**

Section 8's three-clock table, with corporate actions given as the explicit example of the third category.

**7. Because YTM is a per-unit rate, entirely a property of the bond's price/coupon/maturity — it's identical regardless of position size. But the DOLLAR impact of a YTM move is Position Size × DV01-per-unit, which scales directly with how much face value each desk holds — same rate move, same per-unit sensitivity, but a 10x larger position produces a 10x larger dollar P&amp;L.**

Section 9's worked CAD 10,000,000 vs. CAD 100,000,000 comparison states this explicitly as "the key nuance."

**8. EMIR's Derivatives Reporting obligation applies to essentially every OTC derivative position, including a plain IRS, requiring daily valuation updates for as long as it's open — so the IRS is always in scope there. MiFID Position Reporting, by contrast, only applies to position-limit-subject products (commodity derivatives under Module 7 §8) once a firm's CUMULATIVE position in that specific product crosses a regulatory threshold — a plain CAD IRS is never a position-limit-subject product at all, so MiFID Position Reporting would only apply if Bank Of Toronto instead held a large, threshold-crossing position in an in-scope commodity derivative.**

Section 11's "trade-level reporting vs. position-level reporting" diagram, reusing Module 14 §1's own IRS example against all four regimes.

---

[← Back to Index](../../index.md)
