# UK DMO Reporting Deep Dive — Gilt Market Reporting by GEMMs

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only), acting here as a **GEMM** (Gilt-Edged Market Maker) — the UK gilt market's version of a primary dealer.
> This is a **reference module**, in the same family as [sftr-reporting](./sftr-reporting.md). [Module 5](../1-capital-markets/module-05-fixed-income.md) §8 first named the DMO as the UK's gilt-issuing body; this module goes deep on what a GEMM actually reports to the DMO, how often, and with what data.

---

## 1. What Is DMO Reporting? — Purpose &amp; Institutional Basis

**Definition:** the **UK Debt Management Office (DMO)** is an executive agency of HM Treasury, established in 1998, responsible for issuing and managing UK government debt — **gilts** (UK government bonds) and **Treasury bills** — and for the government's day-to-day cash management. **"DMO reporting"** is the operational and commercial reporting a small panel of appointed banks, the **GEMMs (Gilt-Edged Market Makers)**, must submit to the DMO as a condition of their GEMM status: their gilt-auction bidding activity and their ongoing secondary-market gilt trading.

**Important distinction up front:** unlike [SFTR](./sftr-reporting.md) or EMIR ([Module 14](./module-14-regulatory-reporting.md)), DMO reporting is **not a statutory regulatory reporting regime**. There's no Trade Repository, no EU/UK regulation number, no fine from a conduct regulator for missing a deadline. It's reporting owed under a **commercial agreement** (the GEMM agreement) between each bank and the DMO — closer in spirit to a supplier scorecard than to a regulatory filing, even though the consequences of falling short (losing GEMM status) are commercially serious. Section 9 draws this distinction out fully, alongside the *statutory* UK MiFIR reporting obligation that separately applies to the same gilt trades.

**Why it exists:** two purposes:

1. **Reliable, well-subscribed auctions.** The DMO needs to know it can sell tens of billions of pounds of gilts, every week, without fail. That requires a panel of dealers *committed in advance* to showing up and bidding meaningfully at every auction — auction reporting is how the DMO confirms that commitment is real, auction by auction.
2. **Monitoring genuine market-making.** GEMM status carries real privileges (Section 2). The DMO needs ongoing evidence that a GEMM is actually making markets — continuously quoting, genuinely trading — not just holding the title. Daily/weekly/aggregated turnover reporting is that evidence.

```text
UK GOVERNMENT DEBT ISSUANCE — WHERE DMO REPORTING SITS
========================================================================

  HM Treasury sets the annual financing "Remit" (how much to raise,
  gilts vs T-bills, maturity mix)
         |
         v
  DMO executes the Remit -- gilt auctions (mostly), syndications
  (for new/large lines), T-bill tenders
         |
         v
  GEMMs are the DMO's designated primary-dealer counterparties --
  bid at every auction, make continuous secondary-market prices
         |
         v
  GEMMs report BACK to the DMO: auction bids/results, daily/weekly
  secondary turnover -- THIS MODULE
         |
         v
  DMO monitors compliance, publishes aggregate market statistics,
  and calculates each GEMM's rolling market share (Section 7)
```

**Takeaway:** DMO reporting exists to keep a two-sided bargain honest — the DMO grants a small panel of banks privileged, exclusive access to UK government debt issuance; in exchange, those banks owe the DMO continuous proof, at four different time horizons (Sections 4–7), that they're holding up their end.

---

## 2. Who Reports — GEMMs &amp; the GEMM Framework

**Definition:** **GEMMs (Gilt-Edged Market Makers)** are a panel of banks and broker-dealers formally recognized by the DMO, under a bilateral **GEMM agreement**, as primary dealers in the gilt market. Bank Of Toronto's London entity, acting as **Bank Of Toronto UK — GEMM code BOT-GEMM-07** (illustrative), is one such firm throughout this module.

| GEMM Obligation | GEMM Privilege (in return) |
|---|---|
| Continuous two-way price-making in gilts (quoting both a bid and an offer throughout the trading day) | Direct, exclusive access to the DMO's gilt auctions as a bidding counterparty |
| Minimum participation in every gilt auction (competitive bidding) | **Non-Competitive Allocation (NCA)** access at auctions (Section 4) |
| Minimum ongoing secondary-market turnover / market share, measured on a rolling basis (Section 7) | **Post Auction Option Facility (PAOF)** access (Section 4) |
| Regular reporting of bids, allocations &amp; turnover to the DMO (Sections 4–7) | Access to the DMO's secondary-market dealing facilities (e.g., a gilt-lending facility to cover short positions) |
| — | Inclusion on the published GEMM panel, and standing in the DMO's periodic market-share rankings |

> **Callout — verify current obligations before building against them.** The exact minimum bidding percentages, NCA/PAOF caps, and market-share thresholds are set out in the DMO's own published GEMM rules and operational notices, which get revised from time to time — treat every percentage and threshold in this module as an **illustrative teaching number**, not a current rule, and confirm the live figures against Section 11's official links before encoding them into a rules engine.

**Takeaway:** GEMM status is a privilege *conditional on continuous proof of performance* — it isn't granted once and held indefinitely regardless of behavior. That conditionality is exactly why four separate report types exist at four separate time horizons (Section 3): the DMO wants evidence at the auction level, the daily level, the weekly level, and the rolled-up periodic level, not just a single annual certification.

---

## 3. The Four Report Types — Overview

**Definition:** a GEMM's reporting to the DMO isn't four independent obligations — it's the **same underlying trading activity, rolled up at four different time horizons**, each one built from the one below it.

| Report Type | Frequency | What It Captures | Why the DMO Wants It |
|---|---|---|---|
| **Auction Report** | Per gilt auction (roughly weekly) | Competitive bids, NCA/PAOF applications, allocation received, price paid | Confirms the GEMM met its minimum auction-bidding obligation; feeds primary-market statistics |
| **Daily Trade Report** | Every trading day | Secondary-market gilt purchases/sales (turnover), by gilt, price, counterparty type | Raw input for the DMO's ongoing turnover monitoring |
| **Weekly Trade Report** | Weekly | Aggregated daily turnover for the week | A smoothed view of trading activity; feeds the rolling market-share calculation |
| **Aggregated / Periodic Report** | Rolling (e.g., calculated over a trailing 6-month window) | Cumulative auction participation + secondary turnover → **market share %** | Determines whether a GEMM keeps its status — the "league table" (Section 7) |

```text
FOUR REPORTS, ONE ROLL-UP CHAIN
========================================================================

  AUCTION REPORT (Wed 19-Aug-2026's gilt auction)
       |
       | contributes into
       v
  DAILY TRADE REPORT (Wed 19-Aug-2026's full day of activity,
  auction allocation PLUS ordinary secondary trading)
       |
       | contributes into
       v
  WEEKLY TRADE REPORT (Mon 17-Aug -- Fri 21-Aug-2026, five days
  of Daily Trade Reports summed)
       |
       | contributes into
       v
  AGGREGATED / PERIODIC REPORT (rolling 6-month window of Weekly
  Trade Reports, converted into a market-share percentage)
```

**Takeaway:** this is a different kind of multiplicity than [Module 14](./module-14-regulatory-reporting.md) §1's "one trade, five regimes" — there, the same trade was checked against five *independent* regimes with different scopes. Here, the same trading activity is rolled up through four *dependent* time horizons, each one an aggregation of the one before it. Get the daily figure wrong, and the weekly, and ultimately the periodic market-share figure, are wrong too — there's no independent recomputation at each level the way EMIR/SFTR's dual-sided reporting provides a built-in check (Section 9 draws this comparison out further).

---

## 4. Auction Reporting — Worked Example

**Definition:** a UK gilt auction runs as a **single-price (uniform-price) auction**: every successful *competitive* bidder pays the same **clearing price** (the lowest accepted price), regardless of what price they actually bid, as long as their bid was at or above that clearing price. Separately, GEMMs (and certain other eligible investors) can apply for a **Non-Competitive Allocation (NCA)** — a fixed nominal amount with no price specified, allotted at the **average accepted price** instead. Shortly after results are published, GEMMs may also apply through the **Post Auction Option Facility (PAOF)** for further stock at the average price.

```text
GILT AUCTION — 4¼% TREASURY GILT 2036, GBP 3,000,000,000 ON OFFER
========================================================================

  Auction date: Wed 19-Aug-2026     Competitive bidding closes: 10:30am
  Settlement (T+1): Thu 20-Aug-2026 (gilts settle T+1 via CREST --
                     systems-landscape §03's UK gilt settlement cycle)

  BANK OF TORONTO'S COMPETITIVE BIDS:
    Bid 1: GBP 400,000,000 @ price 98.75 (yield ~4.42%)
    Bid 2: GBP 300,000,000 @ price 98.70 (yield ~4.43%)
    Bid 3: GBP 200,000,000 @ price 98.60 (yield ~4.45%)
    Total competitive bid submitted: GBP 900,000,000

  BANK OF TORONTO'S NCA APPLICATION:
    GBP 15,000,000 (illustrative 0.5% of the GBP 3,000,000,000
    on offer -- the typical per-GEMM NCA cap)

  AUCTION-WIDE RESULT (published by the DMO same day):
    Total bids received (all GEMMs): GBP 8,100,000,000
    Bid-to-cover ratio = 8,100,000,000 / 3,000,000,000 = 2.70
    Lowest accepted (clearing) price: 98.62 (yield 4.44%)
    Average accepted price:            98.71 (yield 4.43%)
    Tail = average price - clearing price = 98.71 - 98.62 = 0.09
           (~1 basis point of yield -- a "tight" auction)

  BANK OF TORONTO'S ALLOCATION:
    Bid 1 (98.75 >= clearing 98.62): FILLED  -- 400,000,000
    Bid 2 (98.70 >= clearing 98.62): FILLED  -- 300,000,000
    Bid 3 (98.60 <  clearing 98.62): REJECTED -- 0
    Competitive allocation = 700,000,000 nominal @ 98.62
      = GBP 690,340,000 consideration

    NCA allocation = 15,000,000 nominal @ average price 98.71
      = GBP 14,806,500 consideration

  PAOF (illustrative cap: 10% of own competitive allocation):
    Entitlement = 10% x 700,000,000 = GBP 70,000,000 nominal
    Bank Of Toronto exercises PAOF in full @ average price 98.71
      = GBP 69,097,000 consideration

  TOTAL FROM THIS AUCTION: 715,000,000 + 70,000,000
    = GBP 785,000,000 nominal
    = GBP 690,340,000 + 14,806,500 + 69,097,000
    = GBP 774,243,500 total consideration
```

**Auction Report fields captured for this one auction:** GEMM ID, auction date, gilt identifier, every competitive bid (price, yield, nominal), the NCA application, the PAOF exercise, the clearing price, the average price, and the final allocation/consideration at each layer — see Section 8's full field reference.

**Takeaway:** a single auction generates three economically distinct allocations at three different prices (clearing price for competitive, average price for both NCA and PAOF) — a reporting system that only captures "GEMM won GBP 785,000,000 of the gilt" without preserving which layer each slice came from would be unable to reconcile the GBP 774,243,500 total consideration back to its components, or prove the GEMM's Bid 3 was correctly rejected.

---

## 5. Daily Trade Reporting — Worked Example

**Definition:** every trading day, a GEMM reports its **secondary-market gilt turnover** — every buy and every sell, across every gilt, regardless of counterparty — to the DMO. This is the raw activity feed everything in Sections 6–7 gets built from.

```text
DAILY TRADE REPORT — BANK OF TORONTO, THU 20-AUG-2026
========================================================================
  (the day after Section 4's auction settles)

  Client business:
    Buys from clients:  GBP 150,000,000 nominal (various gilts)
    Sells to clients:   GBP 210,000,000 nominal (various gilts)

  Interdealer business (with other GEMMs):
    Buys:  GBP  40,000,000 nominal
    Sells: GBP  60,000,000 nominal

  GROSS DAILY TURNOVER REPORTED
    = 150,000,000 + 210,000,000 + 40,000,000 + 60,000,000
    = GBP 460,000,000

  (reported as GROSS two-way turnover -- a GBP 150,000,000 buy and
  a GBP 210,000,000 sell are NOT netted into a GBP 60,000,000 net
  figure; the DMO wants to see the level of ACTIVITY, not just the
  net position change)
```

**Takeaway:** "gross, not net" is the single most important convention in this report — the same design choice [Module 3](../1-capital-markets/module-03-trade-lifecycle.md) flags for settlement-level activity (gross settlement vs. netted positions) shows up again here for a different reason: the DMO is measuring how actively a GEMM is *making a market*, and a market-maker who buys GBP 150,000,000 and sells GBP 210,000,000 in one day did meaningfully more market-making work than one who simply held a static GBP 60,000,000 short position all day, even though the *net* effect on their book looks similar.

---

## 6. Weekly Trade Reporting — Worked Example

**Definition:** the **Weekly Trade Report** is Section 5's daily figures, summed across the trading week — a smoothed view that dampens single-day noise (like an auction day's elevated volume) before the data feeds into the rolling market-share calculation (Section 7).

```text
WEEKLY TRADE REPORT — BANK OF TORONTO, WEEK OF MON 17-AUG TO FRI 21-AUG-2026
========================================================================
  Mon 17-Aug:                    GBP   320,000,000
  Tue 18-Aug:                    GBP   410,000,000
  Wed 19-Aug (auction day --
    Section 4's auction settlement
    flow plus ordinary secondary
    trading):                    GBP   900,000,000
  Thu 20-Aug (Section 5's figure): GBP  460,000,000
  Fri 21-Aug:                    GBP   350,000,000
  ---------------------------------------------------
  TOTAL WEEKLY TURNOVER:         GBP 2,440,000,000
```

**Takeaway:** notice Wednesday's turnover (GBP 900,000,000) is more than double an ordinary day — auction days are structurally higher-volume, since allocation flows, PAOF exercises, and the ordinary secondary-market activity that continues alongside the auction all land on the same reporting day. A weekly aggregate absorbs that spike into a fuller picture; a daily-only view would make Wednesday look anomalous rather than routine.

---

## 7. Aggregated / Periodic Reporting — Market Share &amp; the GEMM "League Table"

**Definition:** the DMO calculates each GEMM's **market share** on a rolling basis (illustrative: a trailing 6-month window), combining secondary-market turnover (Section 6's weekly figures, summed) against the **total gilt market turnover across all GEMMs combined**. This is the figure that determines whether a GEMM keeps its status.

```text
ROLLING 6-MONTH MARKET SHARE — BANK OF TORONTO
========================================================================
  Total secondary gilt turnover, ALL GEMMs combined,
  rolling 6-month window ending 21-Aug-2026:    GBP 900,000,000,000

  Bank Of Toronto's own turnover, same window:  GBP  40,500,000,000

  Market share = 40,500,000,000 / 900,000,000,000 = 4.5%

  Illustrative minimum required share to retain GEMM status: 2.0%
  -> Bank Of Toronto is comfortably ABOVE the minimum

  Illustrative GEMM league table (by rolling market share):
    1. [Other GEMM]        7.8%
    2. [Other GEMM]        6.9%
    ...
    6. Bank Of Toronto     4.5%
    ...
   16. [Other GEMM]        1.6%  <- BELOW the 2.0% minimum -> under review
```

**Consequences of falling below the minimum:** the DMO places the underperforming GEMM under review; a persistent shortfall can lead to removal from the GEMM panel — losing auction access, NCA/PAOF privileges (Section 4), and access to the DMO's secondary-market dealing facilities (Section 2's privilege table) all at once, not gradually.

**Takeaway:** the periodic/aggregated report is the only one of the four report types that produces a **relative** figure (share of a market total) rather than an **absolute** one (pounds of turnover) — which means Bank Of Toronto's own reported numbers alone can't tell it whether it's compliant; it depends equally on how much every *other* GEMM reported over the same window, a genuinely different kind of dependency than anything in Sections 4–6.

---

## 8. What Data Needs to Be Captured — Field Reference by Report Type

**Definition:** the fields below are what a GEMM's own trade-capture and reporting systems need to hold to actually produce Sections 4–7's reports, grouped by report type the same way [SFTR](./sftr-reporting.md) §6 grouped its four report tables.

| Report Type | Core Fields |
|---|---|
| **Auction Report** | GEMM ID · Auction date · Gilt identifier (name/ISIN) · Bid type (Competitive / NCA / PAOF) · Bid price · Bid yield · Bid nominal amount · Clearing price · Average accepted price · Allocation nominal amount · Allocation price · Consideration amount · Settlement date (T+1) |
| **Daily Trade Report** | GEMM ID · Trade date · Gilt identifier · Gilt type (conventional / index-linked / green) · Direction (buy/sell) · Nominal amount · Price · Yield · Counterparty type (client / other GEMM / interdealer broker) · Venue (OTC bilateral / electronic platform) · Execution time · Settlement date · CREST settlement reference |
| **Weekly Trade Report** | GEMM ID · Week-ending date · Aggregated buy nominal · Aggregated sell nominal · Gross weekly turnover · Breakdown by gilt type (conventional vs. index-linked vs. green) |
| **Aggregated / Periodic Report** | GEMM ID · Reporting period (rolling window start/end) · Total auction bid nominal (period) · Total auction allocation nominal (period) · Total secondary turnover (period) · Market-wide total turnover (period) · Computed market share % · League-table rank · Compliance flag (above/below minimum) |

**Takeaway:** every field in the Weekly and Aggregated rows is a **derived** field — computable entirely from the Daily Trade Report's raw data plus the market-wide total the DMO itself publishes — while the Auction and Daily rows contain the only genuinely **primary** data a GEMM's systems capture at the point of trade. A well-designed reporting pipeline stores Auction and Daily data as the source of truth and computes Weekly/Aggregated as views over it, rather than maintaining four independently-entered datasets that can drift out of reconciliation with each other.

---

## 9. DMO Reporting vs. Other Regimes — Same Trade, Different Destinations

**Definition:** a single gilt trade routinely triggers **two entirely separate reports**, to two entirely separate recipients, for two entirely separate reasons — exactly the pattern [Module 14](./module-14-regulatory-reporting.md) §1 established for OTC derivatives, now shown for a cash government bond.

```text
ONE GILT TRADE, TWO REPORTS, TWO DESTINATIONS
========================================================================

  Bank Of Toronto sells GBP 10,000,000 nominal of a gilt to a client
  on Thu 20-Aug-2026 (part of Section 5's GBP 210,000,000 client-sell
  total):

  -> UK MiFIR TRANSACTION REPORTING (statutory, to the FCA):
     every execution in an in-scope instrument -- gilts included --
     gets reported for MARKET ABUSE SURVEILLANCE, the same purpose
     Module 14 §4 describes for MiFID II Transaction Reporting, just
     under the UK's onshored MiFIR regime (systems-landscape §10)

  -> DMO DAILY TRADE REPORT (commercial/contractual, to the DMO):
     the SAME GBP 10,000,000 sale is one line inside that day's
     GBP 210,000,000 client-sell total (Section 5) -- reported for
     GEMM PERFORMANCE MONITORING, an entirely different purpose,
     to an entirely different recipient, under NO statute at all
```

| Dimension | UK MiFIR Transaction Reporting | DMO Reporting |
|---|---|---|
| Legal basis | Statutory (onshored UK MiFIR) | Commercial (the GEMM agreement) |
| Recipient | FCA (via an Approved Reporting Mechanism) | The DMO directly |
| Purpose | Market abuse detection | GEMM performance / market-making monitoring |
| Consequence of failure | Regulatory enforcement, fines ([Module 14](./module-14-regulatory-reporting.md) §8) | Loss of GEMM status (Section 7) — a commercial, not regulatory, consequence |
| Scope | Every in-scope instrument execution, all firms | Gilts only, GEMMs only |

**Takeaway:** treat these as two independent pipelines even though they consume nearly identical trade data — a firm that gets its UK MiFIR transaction reporting perfectly right can still lose its GEMM status entirely on a DMO reporting failure, and vice versa, because the two obligations answer to different masters (a statutory regulator vs. a counterparty to a commercial agreement) with no shared enforcement mechanism between them.

---

## 10. Settlement Tie-In — CREST &amp; T+1

**Definition:** every gilt trade referenced in this module — auction allocations (Section 4) and secondary-market trades alike (Sections 5–6) — settles through **CREST**, the UK's Central Securities Depository operated by Euroclear UK &amp; International, on a **T+1** cycle ([systems-landscape](../1-capital-markets/systems-landscape.md) §3's settlement-cycle table: "United Kingdom | Gilts (government bonds) | T+1").

**Takeaway:** gilts have settled T+1 for a long time already — unlike UK *equities*, which still run T+2 with a targeted move to T+1 only in October 2027 ([systems-landscape](../1-capital-markets/systems-landscape.md) §3, §6) — so a reporting system correctly computing "Trade Date + 1" for a gilt auction allocation must NOT reuse that same "+1" offset for a UK equity trade executing on the very same desk, the same per-market-per-asset-class trap [systems-landscape](../1-capital-markets/systems-landscape.md) §3's takeaway already flags.

---

## 11. Official Sources &amp; Links

**Definition:** as with [SFTR](./sftr-reporting.md) §9, this module points to primary institutional sources rather than restating rules that get periodically revised. If a specific deep link has moved, search the named site directly.

| Source | What It Covers | Link |
|---|---|---|
| **UK Debt Management Office (DMO)** | Official gilt auction calendar, results, GEMM panel list, published GEMM rules/operational notices | [www.dmo.gov.uk](https://www.dmo.gov.uk) |
| **HM Treasury** | Sets the annual financing "Remit" the DMO executes (Section 1) | [www.gov.uk/government/organisations/hm-treasury](https://www.gov.uk/government/organisations/hm-treasury) |
| **FCA** | UK MiFIR transaction reporting (Section 9), the statutory regime running alongside DMO reporting | [www.fca.org.uk](https://www.fca.org.uk) |
| **Euroclear UK &amp; International (CREST)** | The CSD settling every gilt trade in this module (Section 10) | [www.euroclear.com](https://www.euroclear.com) |
| **Bank of England** | Oversight of CREST as a systemically important market infrastructure ([systems-landscape](../1-capital-markets/systems-landscape.md) §10) | [www.bankofengland.co.uk](https://www.bankofengland.co.uk) |

**Takeaway:** for a build task, treat the DMO's own published GEMM rules as the source of truth for exact bidding minimums, NCA/PAOF caps, and market-share thresholds (Section 2's callout); the DMO's auction calendar/results pages as the source of truth for live auction data; and the FCA's UK MiFIR guidance as the entirely separate source of truth for Section 9's statutory reporting obligation.

---

## 12. Worked Example — Bank Of Toronto's Full Week, End to End

**Definition:** pulling Sections 4–7 together into one continuous trail, so the four report types can be seen as one connected data flow rather than four separate examples.

```text
BANK OF TORONTO — ONE WEEK, FOUR REPORTS
========================================================================

  WED 19-AUG-2026 -- AUCTION REPORT (Section 4)
    Competitive: 700,000,000 filled @ 98.62 = GBP 690,340,000
    NCA:          15,000,000 filled @ 98.71 = GBP  14,806,500
    PAOF:         70,000,000 filled @ 98.71 = GBP  69,097,000
    Total: GBP 785,000,000 nominal / GBP 774,243,500 consideration

  MON 17 -- FRI 21-AUG-2026 -- DAILY TRADE REPORTS, ROLLED INTO
  THE WEEKLY TRADE REPORT (Sections 5-6)
    Mon 320,000,000 + Tue 410,000,000 + Wed 900,000,000
      + Thu 460,000,000 + Fri 350,000,000
    = GBP 2,440,000,000 weekly turnover

  ROLLING 6-MONTH WINDOW ENDING 21-AUG-2026 -- AGGREGATED /
  PERIODIC REPORT (Section 7)
    This week's GBP 2,440,000,000 is one of roughly 26 weekly
    figures summed into Bank Of Toronto's GBP 40,500,000,000
    rolling-window total -> 4.5% market share -> comfortably
    above the illustrative 2.0% minimum -> GEMM status retained

  ALONGSIDE ALL OF THE ABOVE -- Section 9's SEPARATE obligation
    Every individual client trade inside Mon-Fri's totals also
    generates its own UK MiFIR transaction report to the FCA,
    entirely independently of this DMO report trail
```

**Takeaway:** four reports, one underlying week of trading, and a fifth *entirely separate* reporting obligation (UK MiFIR) running in parallel over the same trades — this is the complete picture a GEMM's reporting infrastructure has to produce correctly, continuously, for every single trading week, not just the illustrative one worked through in this module.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto (UK, GEMM code BOT-GEMM-07) | The GEMM whose auction bidding, daily/weekly turnover, and rolling market share are worked through in Sections 4–7, 12 |
| UK Debt Management Office (DMO) | Executive agency of HM Treasury; runs gilt auctions, receives all four report types, calculates market share and the league table |
| HM Treasury | Sets the annual financing Remit the DMO executes (Section 1) |
| Other GEMMs (unnamed) | Bid alongside Bank Of Toronto at the auction (Section 4) and appear in the illustrative league table (Section 7) |
| FCA | Recipient of the entirely separate, statutory UK MiFIR transaction report (Section 9) |
| Euroclear UK &amp; International (CREST) | Settles every trade in this module, T+1 (Section 10) |

---

## Recap Quiz

1. Using Section 1, why is a GEMM's failure to submit a Daily Trade Report a *commercial* problem rather than a *regulatory* one, in a way that a missed EMIR or SFTR report ([Module 14](./module-14-regulatory-reporting.md), [SFTR module](./sftr-reporting.md)) is not?
2. Using Section 4, why does Bank Of Toronto's Bid 3 (GBP 200,000,000 @ 98.60) get rejected while Bids 1 and 2 are filled — and at what price do the filled bids actually settle?
3. Using Section 4, why are the NCA and PAOF allocations priced at the average accepted price (98.71) rather than the clearing price (98.62) that competitive bidders pay?
4. Using Section 5, why does the DMO want GROSS daily turnover (buys and sells reported separately) rather than Bank Of Toronto's NET position change for the day?
5. Using Section 7, why can Bank Of Toronto not determine on its own, from its own reported numbers alone, whether it is compliant with the minimum market-share threshold?
6. Using Section 8, which two of the four report types contain genuinely PRIMARY data, and which two are DERIVED entirely from those two — why does that distinction matter for how a reporting pipeline should be built?
7. Using Section 9, could Bank Of Toronto have a perfect UK MiFIR transaction-reporting record and still lose its GEMM status? Explain why, tying back to the two obligations' different legal bases.
8. Using Section 10, why can a reporting system not safely reuse the same "Trade Date + 1" settlement-offset logic for a UK gilt trade and a UK equity trade executing on the same desk on the same day?

---

## Answers — Recap Quiz, with Explanations

**1. Because DMO reporting arises from a private commercial agreement (the GEMM agreement) between Bank Of Toronto and the DMO, not from a statute — the consequence of failure is losing GEMM status (a commercial privilege), not a regulatory fine from a conduct authority, unlike EMIR/SFTR where the Trade Repository and the regulator sit outside the two trading counterparties' own agreement entirely.**

Section 1's "Important distinction up front" states this directly: there's no Trade Repository, no regulation number, and no regulator-imposed fine for a late DMO report — it's closer to a supplier scorecard than a regulatory filing, even though losing GEMM status is a serious commercial outcome.

**2. Bid 3's price (98.60) is BELOW the auction's clearing price (98.62), so it's rejected; Bids 1 (98.75) and 2 (98.70) are both at or above the clearing price, so both are filled — and crucially, they settle at the CLEARING price (98.62), not at the price Bank Of Toronto actually bid.**

Section 4 states this is a single-price (uniform-price) auction: "every successful competitive bidder pays the same clearing price... regardless of what price they actually bid, as long as their bid was at or above that clearing price." Bank Of Toronto's higher bid prices (98.75, 98.70) only mattered for determining that its bids would clear, not for what it ultimately pays.

**3. Because NCA and PAOF are, by design, mechanisms for participants who did NOT specify a competitive price — they're allocated at the average accepted price specifically so they don't get the potentially more favorable clearing price competitive bidders earn by actually taking on price risk in their bid.**

Section 4's definition states NCA is "a fixed nominal amount with no price specified, allotted at the average accepted price instead," and PAOF is applied for "at the average price" as well — both are priced worse (from the buyer's perspective) than the clearing price precisely because neither required the participant to commit to a specific price ahead of the result.

**4. Because the DMO is measuring how much genuine market-making ACTIVITY a GEMM performed, not just its end-of-day position change — a GEMM that bought GBP 150,000,000 and sold GBP 210,000,000 (Section 5) did meaningfully more two-way price-making work than one that simply held a static net short position all day, even if the net effect on their book looks similar.**

Section 5's takeaway states this directly, drawing the parallel to [Module 3](../1-capital-markets/module-03-trade-lifecycle.md)'s gross-vs-net settlement distinction: gross turnover reflects genuine dealing activity, while net position change would hide exactly the market-making behavior the DMO is trying to verify.

**5. Because market share is a RELATIVE figure — Bank Of Toronto's turnover divided by the TOTAL turnover of ALL GEMMs combined — so its own reported number alone says nothing about compliance without also knowing what every other GEMM reported over the same rolling window.**

Section 7's takeaway states this explicitly: the periodic/aggregated report "produces a relative figure... rather than an absolute one," which means Bank Of Toronto's compliance depends equally on figures it doesn't control or see until the DMO publishes the aggregate.

**6. The Auction Report and the Daily Trade Report contain PRIMARY data (captured directly at the point of trade); the Weekly and Aggregated/Periodic reports are entirely DERIVED — computable from Daily Trade Report data (plus, for the Aggregated report, the DMO's own published market-wide total). This matters because a pipeline that stores Auction/Daily data as the single source of truth and computes Weekly/Aggregated as views over it avoids the risk of four independently-entered datasets drifting out of reconciliation with each other.**

Section 8's takeaway states this distinction directly, and recommends exactly this "source of truth plus derived views" architecture as the correct design.

**7. Yes — because UK MiFIR transaction reporting and DMO reporting have entirely different legal bases (statutory vs. commercial, Section 9's comparison table) and no shared enforcement mechanism between them. Perfect performance on one obligation says nothing about the other; a GEMM could satisfy the FCA completely while still failing the DMO's separate, contractual reporting or market-share requirements.**

Section 9's takeaway states this directly: "a firm that gets its UK MiFIR transaction reporting perfectly right can still lose its GEMM status entirely on a DMO reporting failure, and vice versa, because the two obligations answer to different masters... with no shared enforcement mechanism between them."

**8. Because gilts settle T+1 while UK equities still settle T+2 (with a move to T+1 only targeted for October 2027) — a system that hard-codes "+1" for every UK trade would compute the WRONG settlement date for every UK equity trade until that 2027 change actually happens, exactly the per-market, per-asset-class trap [systems-landscape](../1-capital-markets/systems-landscape.md) §3 already warns against.**

Section 10's takeaway states this directly, citing the exact same underlying warning from [systems-landscape](../1-capital-markets/systems-landscape.md) §3 that a global settlement-offset assumption breaks the moment it's applied across different asset classes within the very same market.

---

[← Back to Index](../../index.md)
