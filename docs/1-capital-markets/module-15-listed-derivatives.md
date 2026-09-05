# Module 15 — Listed Derivatives

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Not part of the six-asset-class roadmap** (Equity → Fixed Income → FX → Commodities → Rates → Credit). Listed Derivatives is cross-cutting: a Future or Listed Option can reference *any* underlying from any of those six.
> Work data model: this module maps to the taxonomy's **"7. Listed Derivatives"** node — the only node that isn't an asset class at all, but a *venue/clearing category* spanning all of them.

---

## 1. What Is a Listed Derivative? — Theory &amp; Terminology

**Definition:** A Listed Derivative is a Future or Option that trades on a regulated **exchange**, with **standardized** contract terms (size, expiry dates, tick size), and clears through that exchange's own CCP **from the moment of execution**. This module already showed up piecemeal across the roadmap — [Module 4](./module-04-equity.md) §4c's E-mini S&amp;P 500 future, [Module 4](./module-04-equity.md) §4d's AAPL listed call, [Module 7](./module-07-commodities.md) §4b's WTI crude future — each flagged at the time as "not actually a Commodity- or Equity-specific schema, but a Listed Derivatives one" ([Module 4](./module-04-equity.md) §4c, [Module 7](./module-07-commodities.md) §4b's schema callouts). This module is where that recurring cross-reference finally gets its own treatment.

| Term | Meaning |
|---|---|
| Contract Specification | The exchange-defined, standardized terms of one contract — size, quality/grade (for physical underlyings), expiry cycle, tick size — identical for every trade in that contract, unlike an OTC trade's freely negotiated terms |
| Tick Size / Tick Value | The smallest allowed price increment, and the cash value of one tick — e.g. [Module 4](./module-04-equity.md) §4c's E-mini S&amp;P 500 moves in 0.25-point ticks worth USD 12.50 each (0.25 x USD 50 multiplier) |
| Contract Month Code | A single-letter code identifying the delivery/expiry month (F=Jan, G=Feb, H=Mar, J=Apr, K=May, M=Jun, N=Jul, Q=Aug, U=Sep, V=Oct, X=Nov, Z=Dec) — embedded directly in the exchange contract code (Section 6) |
| Open Interest | The total number of contracts currently outstanding for a given contract — a liquidity/activity metric with no OTC equivalent, since OTC trades aren't centrally tallied this way |
| Novation at Execution | The CCP becomes the buyer to every seller and the seller to every buyer **the instant a trade executes** ([Module 3](./module-03-trade-lifecycle.md) §6, [Module 7](./module-07-commodities.md) §5) — not a step that happens later, the way it can for some other centrally-cleared products |

**Takeaway:** every defining feature of a Listed Derivative — standardized terms, a tick size instead of a freely quoted price, a contract month code, instantaneous novation — exists for exactly one reason: to make the contract **fungible**, so that any buyer's position can net against any seller's position on the exchange's books, which is precisely what an OTC-negotiated bespoke contract can never do.

---

## 2. What Are The Products? — Futures and Listed Options, On Anything

```text
LISTED DERIVATIVES
│
├── Futures          (standardized obligation to buy/sell at a future date, any underlying)
└── Listed Options    (standardized right, not obligation — on a future OR on the cash instrument)
```

| Product | What it is |
|---|---|
| **Futures** | A standardized, exchange-traded obligation to buy/sell a fixed quantity of *something* at a fixed price on a fixed date — the "something" can be an equity index ([Module 4](./module-04-equity.md) §4c), a commodity ([Module 7](./module-07-commodities.md) §4b), a bond (Section 4c below), a currency pair, or an interest rate |
| **Listed Options** | A standardized, exchange-traded right (not obligation) to buy (call) or sell (put) at a fixed strike — either on the cash instrument directly ([Module 4](./module-04-equity.md) §4d's AAPL shares) or on a Future itself (an option on the T-Note future, Section 4d) |

```text
ONE PRODUCT PAIR, EVERY UNDERLYING
========================================================================

  Equity Index      -&gt;  E-mini S&amp;P 500 Future        (Module 4 §4c)
  Single Stock       -&gt;  AAPL Listed Call              (Module 4 §4d)
  Commodity          -&gt;  CME WTI Crude Future           (Module 7 §4b)
  Interest Rate       -&gt;  CME 10-Year T-Note Future     (Section 4c, new)
  FX                  -&gt;  CME EUR/USD Future             (not worked here,
                                                            but exists on the
                                                            same model)

  -&gt; Five different asset classes, ONE Futures product mechanic and
     ONE Listed Options product mechanic, each time. This is the
     concrete meaning of "cross-cutting" from this module's masthead.
```

**Takeaway:** by this point in the roadmap, five different underlyings have each already produced a Future or Listed Option example, and every single one used the same mechanics — standardized contract, CCP novation at execution, daily variation margin. That repetition across six modules is the whole reason this taxonomy pulls Listed Derivatives out as its own node instead of duplicating the Future/Listed Option mechanic six times.

---

## 3. What Exactly Is Being Traded? — A Contract Referencing Anything, Standardized For Fungibility

**Definition:** like every derivative in this roadmap, a Future or Listed Option is a contract, not a security — but unlike an OTC contract ([Module 6](./module-06-fx.md) §3's FX, [Module 8](./module-08-rates.md) §3's Rates), a Listed Derivative's terms are **fixed by the exchange**, not negotiated between two counterparties. Two traders executing the same contract an hour apart get *identical* terms, down to the tick.

```text
OTC CONTRACT (NEGOTIATED)  vs  LISTED CONTRACT (STANDARDIZED)
========================================================================

  OTC FRA (Module 8 §4b):
    Bank Of Toronto and Goldman Sachs negotiate a bespoke Notional
    (CAD 20,000,000), a bespoke FRA Rate (4.85%), a bespoke period
    (3x6) -- no two FRAs need look alike.

  Listed Future (this module):
    Every E-mini S&amp;P 500 future traded on CME today has the SAME
    multiplier (USD 50/point), the SAME tick size (0.25 points),
    and expires on ONE of a small set of standardized quarterly
    dates -- the only thing that varies trade-to-trade is price
    and quantity.

  -&gt; This is WHY a Listed Derivative can be netted, closed out, or
     rolled (Module 7 §4b) against ANY other market participant's
     opposite position in the same contract -- an OTC trade can
     only ever be unwound against the SAME original counterparty
     (or novated via a separate compression exercise), because no
     two OTC trades are guaranteed to share identical terms.
```

**Takeaway:** this is the real answer to "why does this taxonomy split Futures and Listed Options out from Equity/Commodities/Rates/FX instead of leaving them where the underlying naturally sits?" — standardization is a *venue* property, not an *asset class* property, so a schema organized around "how is this contract structured and cleared" genuinely needs a different axis than a schema organized around "what does its value depend on."

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

> **Complete worked example available.** See [Listed Futures & Listed Options — Complete Example](../../examples/Listed_Futures_and_Options_Complete_Example.md) ([HTML](../../examples/Listed_Futures_and_Options_Complete_Example.html)) for the full CME 10-Year T-Note Future hedge and the AAPL listed call option, both taken through the complete trade lifecycle.

### 4a. Equity Index Future — recap

Full worked example in **[Module 4](./module-04-equity.md) §4c**: Maple Leaf Pension Fund buys 20 E-mini S&amp;P 500 futures @ 5,920.00, notional USD 5,920,000, USD 50/point multiplier, daily variation margin against CME Clearing.

### 4b. Commodity Future — recap

Full worked example in **[Module 7](./module-07-commodities.md) §4b**: Bank Of Toronto buys 50 CME WTI Crude Oil futures @ USD 75.50/bbl, then rolls the position forward a month later, realizing the contango cost.

### 4c. Interest Rate Future — new worked example

```text
INTEREST RATE FUTURE  —  CME 10-Year Treasury Note Future (ZN)
========================================================================

  Seller: Bank Of Toronto (hedging duration risk on its Fixed
          Income book — Module 5 §1's Duration preview, made
          concrete here)
  Buyer:  Counterparty on the CME order book (anonymous, novated
          to CME Clearing at execution — Section 07)

  Contract: USD 100,000 face value of a notional 6% coupon 10-Year
            Treasury Note (the "notional" note is a standardized
            fiction — Section 5 explains what ACTUALLY delivers)
  Price Quotation: in points and 32nds -- 112'16 means 112 + 16/32
                   = 112.500 (a bond-market convention, distinct
                   from Module 4 §4c's decimal index points)

  Trade: SELL 100 contracts @ 112'16 (112.500)
  Notional = 100 x 100,000 x 112.500/100 = USD 11,250,000

  NEXT BUSINESS DAY, price falls to 111'24 (111.750) — rates rose,
  bond prices fell (Module 1 §1's price-vs-rates mechanic):
    Variation Margin = (112.500 - 111.750)/100 x 100,000 x 100
                      = 0.0075 x 100,000 x 100
                      = USD 75,000
    -&gt; Bank Of Toronto is SHORT, so a price FALL is a GAIN --
       credited to Bank Of Toronto's margin account, offsetting
       the loss on the actual bond portfolio the future is hedging
```

**IT/reporting angle:** the fact that Bank Of Toronto is *selling* futures to hedge a position it *already holds* (bonds it owns, from [Module 5](./module-05-fixed-income.md)) rather than to speculate is the single most common real-world use of an Interest Rate Future inside a bank — a reporting system that only models futures as directional speculative positions will misclassify exactly this kind of hedge overlay.

### 4d. Listed Option — recap

Full worked example in **[Module 4](./module-04-equity.md) §4d**: Maple Leaf Pension Fund buys 100 AAPL 15-Jan-2027 250 Call contracts, premium USD 185,000, clears via the OCC.

**Takeaway:** four examples, four different underlyings (an index, a commodity, a bond future, a single stock), yet only two contract types across all of them (Futures and Listed Options) — the sharpest possible illustration of Section 3's "standardization is a venue property, not an asset-class property" finding.

---

## 5. Trade Lifecycle — What's Genuinely New: Physical Delivery via a Basket

Sections 5 in [Module 4](./module-04-equity.md) (§5) and [Module 7](./module-07-commodities.md) (§5) already walked through the Futures lifecycle in full — instantaneous CCP novation, daily variation margin, expiry-or-roll. What's genuinely new here is what happens at **expiry** for a bond future specifically, which behaves differently from every other Future example in this roadmap.

```text
CME 10-YEAR T-NOTE FUTURE  —  WHAT HAPPENS AT EXPIRY (if not rolled/closed)
========================================================================

  E-mini S&amp;P 500 Future (Module 4 §4c) at expiry:
    CASH-SETTLED against the index level -- no security moves

  WTI Crude Future (Module 7 §4b) at expiry:
    PHYSICALLY SETTLED into ONE specific grade/location of crude
    (if not rolled -- almost never allowed to reach this point)

  10-Year T-Note Future (this section) at expiry:
    PHYSICALLY SETTLED, but into ANY ELIGIBLE BOND from a defined
    DELIVERY BASKET (a range of Treasury Notes with maturities in
    a specified window) -- the SHORT (Bank Of Toronto, Section 4c)
    CHOOSES which eligible bond to deliver, using a published
    CONVERSION FACTOR to adjust for that bond's specific coupon
    and maturity versus the contract's notional 6% coupon

  -&gt; The bond the short chooses to deliver is called the CHEAPEST
     TO DELIVER (CTD) -- the eligible bond that's most economical
     for the short to source and deliver, a genuinely unique
     mechanic that neither an index future (no delivery at all)
     nor a commodity future (one fixed grade) has an equivalent of.
```

**Takeaway:** "physically settled" doesn't mean the same thing across every Future in this module — an index future never delivers anything, a commodity future delivers one specific thing, and a bond future delivers the short's *choice* from a defined menu. A reporting or settlement system that assumes "physical settlement" always means "one predetermined instrument moves" will handle a bond future's delivery notice incorrectly.

---

## 6. Identifiers — Exchange Contract Codes, No Bilateral UTI

Listed Derivatives is the first product family in this roadmap where the **UPI/UTI pair that's appeared in every OTC module since [Module 2](./module-02-hierarchy.md) §12 doesn't apply at all** — because UPI/UTI exist specifically to identify *bilaterally negotiated* OTC derivative trades for regulatory reporting, and a Listed Derivative was never bilaterally negotiated in the first place.

| Identifier | Applies to Listed Derivatives? | Example |
|---|---|---|
| ISIN / CUSIP / Ticker | **No** — not securities (Section 03) | — |
| **Exchange Contract Code** | Yes — the primary identifier, encoding product + expiry month + year | `ESZ26` (E-mini S&amp;P 500, Dec 2026); `CLZ26` (WTI Crude, Dec 2026 — [Module 7](./module-07-commodities.md) §6); `ZNZ26` (10Y T-Note, Dec 2026) |
| Contract Month Code | Embedded inside the exchange contract code (Section 1's F/G/H/.../Z table) | `Z` = December, in all three codes above |
| **UPI / UTI** | **No** — these identify bilaterally negotiated OTC derivatives ([Module 2](./module-02-hierarchy.md) §12); a Listed Derivative's fungibility (Section 03) makes per-trade bilateral identifiers meaningless — the exchange's own trade/clearing IDs serve that role instead | — |
| **LEI** | Yes — identifies Bank Of Toronto and any other counterparty, same role as every prior module | Entity, not instrument ([Module 4](./module-04-equity.md) §6) |

**Takeaway:** a Listed Derivative trade record with an exchange contract code but no UPI/UTI is exactly correct — the reverse of the diagnostic [Module 6](./module-06-fx.md) §6 and [Module 7](./module-07-commodities.md) §6 already established for OTC records. Seeing a UPI/UTI populated on what's otherwise clearly a listed Futures/Options record would itself be the anomaly here.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** every other module in this roadmap turned Section 7 into a genuine two-axis question with multiple answers across a table. Listed Derivatives is the one node where the question has a **single, unconditional answer** for every product in it — by definition.

| Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|
| Futures (any underlying) | **Always exchange-traded** | **Always centrally cleared**, from the instant of execution (Section 01) |
| Listed Options (any underlying) | **Always exchange-traded** | **Always centrally cleared** (OCC for US equity options, CME Clearing for futures options) |

**Takeaway:** every prior module's Section 7 existed to show WHERE a given product sits on this two-axis grid, because the answer genuinely varied by product. Here, the two-axis grid collapses to one cell — which is the whole point of this module existing as a separate node: "Listed Derivatives" isn't defined by what it references, it's defined by *always landing in the exchange-traded, centrally-cleared cell*, no matter which of the five underlyings from Section 2 it's built on.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | How it touches Listed Derivatives specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Sets pre/post-trade transparency for listed derivatives trading venues directly, plus **position limits** on certain listed commodity derivatives ([Module 7](./module-07-commodities.md) §08) — reused here since the mechanism is the same regardless of which underlying the future references |
| **EMIR** | Generally **does not apply** to exchange-traded, centrally-cleared Futures/Listed Options the way it does to OTC derivatives ([Module 2](./module-02-hierarchy.md) §07) — the reporting obligation still technically exists, but the *mandatory clearing* half of EMIR (the headline fact in [Module 8](./module-08-rates.md) §08 and [Module 9](./module-09-credit.md) §08) is moot here, since these products were never going to be anything BUT centrally cleared |
| **SFTR** | Not applicable — no securities financing involved |
| **FCA** (UK) | Regulates UK listed derivatives exchanges and their members' conduct |
| **CIRO** (Canada) | SRO overseeing Canadian listed derivatives dealer conduct, alongside the Montreal Exchange (Canada's own listed derivatives exchange) |
| **HKMA / SFC** (Hong Kong) | The SFC ([Module 4](./module-04-equity.md) §08's distinction from HKMA) is the relevant body for HKEX-listed futures/options specifically — HKMA itself stays a step removed here, similar to its role in Equity ([Module 4](./module-04-equity.md) §08) rather than its direct roles in FX ([Module 6](./module-06-fx.md) §08) or Fixed Income ([Module 5](./module-05-fixed-income.md) §08) |
| **CFTC / SEC** (US) | The CFTC regulates most listed futures (commodity, rate, FX, broad index); the SEC and CFTC **jointly** regulate "Security Futures Products" (futures on a single stock or a narrow-based index) — a second CFTC/SEC split alongside [Module 9](./module-09-credit.md) §08's Credit split, but reached for a different reason: here it's about the underlying being a *single security* specifically, not about swaps-vs-security-based-swaps classification |
| **DTCC / OCC** | The **OCC (Options Clearing Corporation)**, introduced in [Module 4](./module-04-equity.md) §5, is this module's dedicated CCP for all US-listed equity options — worth restating here as the module where its full scope (any listed option, not just equity ones in some product setups) belongs |
| **DMO** (UK Debt Management Office) | Not directly relevant — issues the cash Gilts ([Module 5](./module-05-fixed-income.md) §08) that a UK Gilt future would reference, but the DMO itself doesn't touch the listed derivative market |

**Takeaway:** the regulatory story here is almost the inverse of every prior module's — instead of asking "which specific products does this regulation's mandate reach into," most of Section 8's rows here are really saying "this regulation's OTC-focused machinery mostly doesn't need to reach into this corner of the market, because Section 7 already guarantees central clearing without a mandate." The CFTC/SEC Security Futures Product split is the one genuine exception — a place where regulatory attention specifically increases because a listed derivative's underlying happens to be a single security.

---

## 9. Canadian Systems Landscape — Front, Middle &amp; Back Office to Regulatory Reporting

**Definition:** [systems-landscape](./systems-landscape.md) §2 already named the *generic* Front/Middle/Back Office architecture for Canada. This section specializes that architecture to Listed Derivatives specifically — and, because this module's own theme is that every underlying (Section 2) funnels through the same mechanic, Canada's listed-derivatives stack is unusually simple to draw: there is genuinely only **one** venue and **one** CCP for exchange-listed derivatives in Canada, unlike the several competing venues [systems-landscape](./systems-landscape.md) §2 lists for cash equities.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | Order/Execution Management (Bloomberg EMSX, FlexTrade, or a bank's own DMA gateway — illustrative, same vendor family [systems-landscape](./systems-landscape.md) §2 names) → **Montreal Exchange (MX)**, TMX Group's derivatives exchange, matched on its electronic trading engine, commonly referred to as **SOLA** | Order entry, execution, price discovery |
| **Middle Office** | Give-up/allocation processing (routes an executing broker's trade to the correct clearing member); risk/valuation engines (Murex/Calypso — illustrative) | Confirms the trade, marks intraday risk, routes it to the right clearing member |
| **Back Office / CCP** | **CDCC (Canadian Derivatives Clearing Corporation)**, also a TMX Group company | Novates every MX-listed trade the instant it executes (Section 1's Novation-at-Execution), collects Initial Margin, calculates daily Variation Margin (a SPAN-style methodology — illustrative) |
| **Cash Settlement** | **LVTS (Large Value Transfer System)**, Canada's high-value payment rail ([systems-landscape](./systems-landscape.md) §8's cash-settlement-rail table — the Canadian counterpart to Fedwire/CHAPS) | Moves the actual variation-margin and settlement cash between Bank Of Toronto's settlement bank and CDCC |
| **Regulatory / Oversight** | **CIRO** (dealer conduct, trade/market surveillance); the **CSA** (recognizes MX as an exchange, CDCC as a clearing agency); the **Bank of Canada** (oversees CDCC as a systemically important Financial Market Infrastructure, alongside CDS/CDSX — [systems-landscape](./systems-landscape.md) §10) | Conduct oversight and systemic-risk monitoring — **not** a Trade Repository submission (see takeaway) |

```text
CANADIAN LISTED DERIVATIVES — FRONT TO BACK, ONE TRADE
========================================================================

  FRONT OFFICE
    Bank Of Toronto's derivatives desk enters the order via an
    OMS/EMS (Bloomberg EMSX / FlexTrade / a bank DMA gateway --
    illustrative) -&gt; routed to MONTREAL EXCHANGE (MX), matched on
    its electronic trading engine, SOLA
         |
         v
  MIDDLE OFFICE
    Give-up/allocation processing routes the executed trade to the
    correct clearing member; risk/valuation systems (Murex/Calypso
    -- illustrative) mark the position and compute intraday risk
         |
         v
  BACK OFFICE / CCP
    CDCC (Canadian Derivatives Clearing Corporation) NOVATES the
    trade the instant it executes (Section 1's Novation-at-
    Execution) -- becomes buyer to the seller and seller to the
    buyer, collects Initial Margin, and calculates daily Variation
    Margin (a SPAN-style methodology -- illustrative)
         |
         v
  CASH SETTLEMENT
    Variation margin cash moves between Bank Of Toronto's
    settlement bank and CDCC over LVTS, Canada's high-value
    payment rail (systems-landscape §8 -- the Canadian counterpart
    to Fedwire/CHAPS)
         |
         v
  REGULATORY / OVERSIGHT -- NO TRADE REPOSITORY IN THIS PATH
    CIRO oversees Bank Of Toronto's trading conduct on MX; the CSA
    recognizes MX as an exchange and CDCC as a clearing agency; the
    BANK OF CANADA oversees CDCC itself as a systemically important
    Financial Market Infrastructure (FMI), given how much of
    Canada's listed-derivatives risk concentrates through this one
    CCP
```

**Worked walkthrough — Bank Of Toronto buys S&amp;P/TSX 60 Index Futures (SXF) on MX:**

```text
S&amp;P/TSX 60 INDEX FUTURE (SXF)  —  MONTREAL EXCHANGE
========================================================================

  Buyer: Bank Of Toronto     Multiplier: CAD 200 / index point
  Trade: BUY 15 contracts @ 1,285.00 (index points)
  Notional = 15 x 200 x 1,285.00 = CAD 3,855,000

  Next business day, index rises to 1,291.50:
    Variation Margin = (1,291.50 - 1,285.00) x 200 x 15
                      = 6.50 x 200 x 15
                      = CAD 19,500
    -&gt; credited to Bank Of Toronto (long position, index rose --
       the SAME mechanic as Module 4 §4c's E-mini S&amp;P 500 example,
       just a different index, multiplier, and currency)

  SYSTEM PATH: OMS/EMS -&gt; MX/SOLA (execution) -&gt; clearing-member
  risk engine (middle office) -&gt; CDCC (novation, margin) -&gt; LVTS
  (cash) -&gt; CIRO/CSA/Bank of Canada (oversight, no TR submission)
```

> **Callout — illustrative, not prescriptive.** System/vendor names above (Bloomberg EMSX, FlexTrade, Murex, Calypso) are representative of what Canadian bank derivatives desks commonly run, not a claim about Bank Of Toronto's actual internal technology stack — consistent with how [systems-landscape](./systems-landscape.md) §2 already hedges its own vendor list. MX, CDCC, CIRO, the CSA, the Bank of Canada, and LVTS, by contrast, are real, specific Canadian market infrastructure: there is only **one** CCP for MX-listed products, not a choice of vendors, unlike the front/middle-office layer above it.

**Takeaway:** Section 8 already flagged that EMIR's mandatory-clearing machinery is "moot" for Listed Derivatives because central clearing was never optional here — this section shows the Canadian-specific consequence of that same fact: because every MX-listed trade is centrally cleared through CDCC from execution, there's no CSA Multilateral Instrument 96-101 Trade Repository submission the way an uncleared CAD IRS ([Module 8](./module-08-rates.md)) or a CDS ([Module 9](./module-09-credit.md)) would require. CIRO's own market/trade surveillance — not a Trade Repository — is the oversight layer that answers [Module 14](../2-reporting/module-14-regulatory-reporting.md) §1's "did this trade get seen by a regulator?" question for this corner of the Canadian market.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Seller of 10-Year T-Note futures, hedging its Fixed Income book's duration |
| Maple Leaf Pension Fund | Buyer of the E-mini S&amp;P 500 futures ([Module 4](./module-04-equity.md) §4c) and the AAPL listed call ([Module 4](./module-04-equity.md) §4d) |
| CME / CME Clearing | Exchange and CCP for the E-mini, WTI, and 10-Year T-Note futures |
| OCC (Options Clearing Corporation) | CCP for US-listed equity options, including the AAPL call |
| CFTC / SEC | Joint regulators of Security Futures Products (futures on single stocks/narrow indices) |
| Montreal Exchange (MX) / CDCC | Canada's listed-derivatives exchange and its dedicated CCP — executes and clears the SXF trade (Section 9) |
| CIRO / Bank of Canada | CIRO oversees Canadian dealer conduct on MX; the Bank of Canada oversees CDCC as a systemically important FMI (Section 9) |

---

## Recap Quiz — Numbers Edition

1. On the 10-Year T-Note future in Section 4c, if the price instead ROSE to 113'08 (113.250) the next business day, what is the Variation Margin amount, and does Bank Of Toronto gain or lose (remember: Bank Of Toronto is SHORT)?
2. Why does Section 3 say an OTC FRA "can only ever be unwound against the same original counterparty," while a Listed Future can be closed out against any market participant?
3. A bond future reaches expiry without being rolled or closed. Using Section 5, does the buyer receive one specific, predetermined bond — and if not, who decides what gets delivered?
4. Using Section 6, would you expect to find a UTI field populated on a CME WTI Crude Future trade record? Why or why not, and how does this contrast with the same product's OTC cousin, the Commodity Swap ([Module 7](./module-07-commodities.md) §6)?
5. Why does Section 7 describe this module's Exchange-vs-OTC table as "collapsing to one cell," and how is that different from every other module's version of that same table?
6. What specifically causes a Security Futures Product (Section 08) to fall under BOTH CFTC and SEC jurisdiction, and how does that reason differ from the reason [Module 9](./module-09-credit.md) §08 gave for Credit's CFTC/SEC split?
7. Why does this module's masthead describe Listed Derivatives as sitting outside the six-asset-class roadmap rather than being folded into, say, [Module 4](./module-04-equity.md) (Equity) or [Module 7](./module-07-commodities.md) (Commodities)?
8. Using Section 9, why does a CAD-denominated, CDCC-cleared SXF future NOT generate a CSA Multilateral Instrument 96-101 Trade Repository report, while an uncleared CAD IRS ([Module 8](./module-08-rates.md)) would? Which body still provides regulatory oversight of the SXF trade if not a Trade Repository?

Answer whenever ready — this closes both the Asset Class Roadmap (Modules 4-9) and its cross-cutting companion. Modules 10-14 build on all of this: Valuation, Risk &amp; Exposure, Collateral &amp; Margin, Parties/LEIs, and Regulatory Reporting.

---

## Answers — Recap Quiz, with Explanations

**1. Variation Margin = USD 75,000, and Bank Of Toronto LOSES (pays out), the mirror image of Section 4c's worked example.**

```text
Variation Margin = (113.250 - 112.500)/100 x 100,000 x 100
                  = 0.0075 x 100,000 x 100
                  = USD 75,000
```

Since Bank Of Toronto is SHORT (sold the future), a price RISE moves against the position — Bank Of Toronto now owes USD 75,000 in variation margin, the opposite of Section 4c's price-fall scenario where the short gained. This is exactly the hedge working as intended: if rates fell instead of rose, Bank Of Toronto's actual bond portfolio would have GAINED value, offsetting this futures loss — the whole reason it sold the future in the first place.

**2. Because an OTC FRA's terms (Notional, FRA Rate, period) are bespoke to that one negotiation between Bank Of Toronto and Goldman Sachs ([Module 8](./module-08-rates.md) §4b) — no other market participant's FRA is guaranteed to have identical terms, so there's no fungible "opposite position" to net against except the original counterparty. A Listed Future's terms are identical for every trade in that contract (Section 3), so ANY buyer's long position can net against ANY seller's short position on the exchange's books.**

Section 3's takeaway states this as the core reason standardization exists at all: fungibility is what makes closing out or rolling ([Module 7](./module-07-commodities.md) §4b) possible against a completely different counterparty than the one you originally traded with.

**3. No — the buyer does NOT receive one predetermined bond. The SELLER (the short) chooses which eligible bond from the delivery basket to deliver, using the published conversion factor to adjust for that bond's specific coupon/maturity versus the contract's notional 6% coupon — the "Cheapest to Deliver" (CTD) bond.**

Section 5 states this directly, contrasting it with both the E-mini future (no delivery at all, cash-settled) and the WTI future (one fixed grade/location) — the bond future is the only one of the three where the party making delivery has a genuine choice of what to deliver.

**4. No — you would NOT expect a UTI on a CME WTI Crude Future trade record, because Section 6 establishes that UPI/UTI only apply to bilaterally negotiated OTC derivatives, and a Listed Future was never bilaterally negotiated. This directly contrasts with the OTC Commodity Swap ([Module 7](./module-07-commodities.md) §6), which DOES carry a UPI/UTI precisely because it IS bilaterally negotiated — the exact same underlying commodity (crude oil) produces two structurally different identifier patterns purely based on which venue/product type it's traded through.**

Section 6's takeaway makes this the diagnostic check for this module: an exchange contract code with no UPI/UTI is correct for a Listed Derivative; seeing UPI/UTI populated instead would be the anomaly — the reverse of the checks [Module 6](./module-06-fx.md) §6 and [Module 7](./module-07-commodities.md) §6 established for OTC products.

**5. Because every product in this module lands in the SAME cell (exchange-traded, centrally cleared) by definition — Section 7's grid has only one populated cell instead of the multiple cells every prior module's version of that table needed, since "Listed Derivatives" is defined precisely as the products for which that question has one fixed answer, unlike Equity, Fixed Income, FX, Commodities, Rates, and Credit, where the same question genuinely varied by specific product within the asset class.**

Every prior module needed Section 7 to show WHERE within a 2x2 (or larger) grid each of that asset class's several products landed. Here, there's nothing to differentiate — which is exactly the point Section 7's takeaway makes about why "Listed Derivatives" earns its own module instead of being an asset-class-organized concept.

**6. A Security Futures Product falls under both CFTC and SEC jurisdiction specifically because its underlying IS a single security (or a narrow-based index close to one) — a direct securities-law hook, the same underlying-security trigger [Module 9](./module-09-credit.md) §08 identified for single-name CDS. The Credit split ([Module 9](./module-09-credit.md) §08), however, was about swaps-vs-security-based-swaps CLASSIFICATION under Dodd-Frank's OTC derivatives framework; this module's split is about which listed-derivatives regulator (CFTC for futures/options generally, SEC for security-linked ones specifically) has jurisdiction over an exchange-traded product — a parallel outcome (joint CFTC/SEC oversight) reached through two different legal frameworks (OTC swap classification vs. listed product classification).**

Section 8 flags this explicitly as "a second CFTC/SEC split... reached for a different reason" — both splits trace back to the same root cause (a single security's involvement triggering SEC interest) but apply to genuinely different product categories (OTC swaps vs. listed futures/options).

**7. Because Section 3's finding is that standardization/central clearing is a VENUE property, not an ASSET CLASS property — a Future or Listed Option can reference literally any of the six asset classes' underlyings (Section 2's five-example table), so folding it into just one asset class module (say, Equity) would misrepresent every Future/Listed Option built on a different underlying (a WTI future, a T-Note future) as if it somehow belonged to Equity instead.**

The masthead's framing and Section 1-3's arguments both converge on this: the work data model taxonomy's own structure (Listed Derivatives as taxonomy node "7," separate from asset-class nodes "1" through "6") reflects a real organizing principle, not an arbitrary schema-design choice — this module exists specifically because the mechanic (Section 1, Section 3) is genuinely orthogonal to which asset class supplies the underlying.

**8. Because the SXF future is centrally cleared through CDCC from the instant of execution (Section 1's Novation-at-Execution, restated for Canada in Section 9) — MI 96-101's Trade Repository obligation exists specifically to give regulators visibility into OTC derivative risk that would otherwise be invisible, and a CDCC-cleared listed future is never invisible in that sense: CDCC itself already holds the complete, centralized record of every position. An uncleared CAD IRS has no such centralized record, which is exactly the gap MI 96-101 exists to close. CIRO's own market/trade surveillance function provides the regulatory oversight for the SXF trade instead of a Trade Repository.**

Section 9's takeaway states this directly, tying back to Section 8's observation that EMIR's mandatory-clearing machinery is "moot" here — the same logic applies to Canada's own OTC trade-reporting regime: it targets exactly the visibility gap that central clearing through a single national CCP (CDCC) already closes for every MX-listed product.

---

[← Back to Index](../../index.md)
