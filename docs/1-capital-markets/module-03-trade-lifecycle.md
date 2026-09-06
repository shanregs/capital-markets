# Module 3 — Trade Lifecycle

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).

---

## 1. The Trade Lifecycle — Nine Stages, One Picture

**Definition:** Every trade you'll ever see in a reporting system passes through the same nine stages, in the same order, regardless of asset class. What differs per asset class (bond vs equity vs OTC derivative) is *how* each stage is implemented — which systems, which messages, which extra steps (e.g. "Clearing" is a no-op for a plain bond trade but critical for a cleared swap). The sequence itself never changes.

```text
THE NINE-STAGE TRADE LIFECYCLE
====================================================================

  1. ORDER            "I want to buy/sell X" — intent, not yet a trade
        |
  2. EXECUTION         Order meets a counterparty at an agreed price -> a TRADE now exists
        |
  3. TRADE CAPTURE      The trade is recorded as data (Trade ID assigned)
        |
  4. CONFIRMATION       Both sides formally state what they believe was agreed
        |
  5. MATCHING           Automated comparison: do both sides' records agree exactly?
        |
  6. CLEARING           (derivatives/some cash products) CCP steps in as buyer-to-
        |                every-seller and seller-to-every-buyer; margin is posted
        |
  7. SETTLEMENT         Securities and cash actually change hands (DvP)
        |
  8. RECONCILIATION     Internal books vs custodian/CCP records are tied out
        |
  9. REGULATORY         Trade details submitted to a Trade Repository / regulator
     REPORTING
```

| Stage | Which office owns it | Produces |
|---|---|---|
| Order | Front Office | An instruction, not yet a trade |
| Execution | Front Office | A legally agreed trade (price, quantity, counterparty, time) |
| Trade Capture | Front Office / Middle Office | The first electronic Trade record, with a Trade ID |
| Confirmation | Middle Office | A formal statement of trade economics sent to the counterparty |
| Matching | Middle Office | A MATCHED or BROKEN status |
| Clearing | Middle Office / CCP | Two novated trades (vs CCP) + margin requirement, where applicable |
| Settlement | Back Office | Securities delivered, cash paid, ledger entries booked |
| Reconciliation | Back Office | A tied-out position, or a flagged break |
| Regulatory Reporting | Back Office / Compliance | A submitted report to a Trade Repository / regulator |

The systems sitting behind those three offices are **not** the same system for every trade — they differ by asset class. The table below is a Bank Of Toronto-wide snapshot, pulling together each asset class's own Front/Middle/Back Office systems (already introduced in that asset class's own module §9) into one side-by-side view; see [systems-landscape](./systems-landscape.md) for the fuller per-country picture these are drawn from.

| Capital Market Entity | Front Office Systems | Middle Office Systems | Back Office / Clearing Systems |
|---|---|---|---|
| **Equity** ([Module 4](./module-04-equity.md) §9) | OMS/EMS (e.g. Bloomberg AIM, Charles River IMS, or Eze OMS — illustrative), FIX-connected smart order router | Trade capture/confirmation; CIRO market-surveillance feed under UMIR; Murex/Calypso for any equity-derivative overlay | **CDS/CDSX** (CNS, DvP book-entry settlement, T+1) |
| **Fixed Income** ([Module 5](./module-05-fixed-income.md) §9) | **CanDeal**; Bloomberg ALLQ/BondTrader (illustrative); the Bank of Canada's own auction system for new-issue Government of Canada bonds | Bilateral/platform trade confirmation; valuation &amp; risk systems (Murex, Calypso, Summit — illustrative) | **CDSX** (bond DvP settlement, T+2); **CDCC** (fixed income repo clearing) |
| **FX** ([Module 6](./module-06-fx.md) §9) | **EBS**, **Refinitiv Matching/FXall**, **360T**; Bank Of Toronto's own eFX pricing/aggregation platform | Trade capture and confirmation via SWIFT MT300-type messages; risk systems (Murex, Calypso FX module) | **CLS** (PvP settlement for CLS-eligible pairs); **Lynx** carries the CAD leg into CLS; correspondent banking/SWIFT payment messages for non-CLS-eligible pairs |
| **Commodities** ([Module 7](./module-07-commodities.md) §9) | **NGX** (Calgary — Canadian-domiciled physical/financial AECO gas, power, crude) *or* **CME Globex / ICE** (global benchmark venues, via a Futures Commission Merchant relationship) | Commodity risk/confirmation system (Murex/Calypso-class); ICE eConfirm-style electronic confirmation | **NGX Clearing** (part of ICE) *or* **CME Clearing / ICE Clear** |
| **Rates** ([Module 8](./module-08-rates.md) §9) | Bloomberg; interdealer-broker screens (illustrative: ICAP, Tradition) for OTC; **Montreal Exchange (MX)**'s "SOLA" engine for exchange-listed CGB/CORRA futures | **MarkitWire / ICE Link** (OTC confirmation/affirmation); Calypso/Murex (swap valuation, DV01/curve risk) | **LCH SwapClear** (mandatory central clearing for standardized CAD IRS); **CDCC** (clears MX-listed CAD rate futures) |
| **Credit** ([Module 9](./module-09-credit.md) §9) | **MarkitWire / ICE Link**, Bloomberg | The SAME MarkitWire/ICE Link platform also handles confirmation/affirmation here — a genuine contrast with Rates; Calypso/Murex handle ongoing valuation (upfront/running-coupon calculations) | **ICE Clear Credit** (if centrally cleared) *or* bilateral settlement; **DTCC's Trade Information Warehouse (TIW)** holds the lifecycle record either way |

**Takeaway:** if you're ever handed an unfamiliar field in a trade record and asked "where does this come from?", the fastest diagnostic is to place it on this nine-stage timeline — the stage tells you which office produced it and which upstream system owns the source of truth. Layer the systems table on top of that, and the same diagnostic extends one step further: the *asset class* tells you which specific platform that office was actually looking at when the field was created.

---

## 2. Order vs Execution — where "the trade" actually begins

**Definition:** An **Order** is an instruction to buy or sell — it carries no counterparty commitment and can be partially filled, amended, or cancelled with zero consequence. An **Execution** is the moment an order meets a matching counterparty at an agreed price — from that instant, a legally binding trade exists, even before any system has recorded it.

### Worked example — Maple Leaf Pension Fund routes an order through Bank Of Toronto

```text
ORDER  ->  EXECUTION
========================================================================

09:02:00  ORDER placed
  Maple Leaf Pension Fund instructs Bank Of Toronto:
    "BUY CAD 50,000,000 face value, Canada 5% Bond 2035,
     LIMIT price 98.60 or better"
  -> No trade exists yet. This is pure intent.

09:03:12  EXECUTION
  Bank Of Toronto's trading desk finds a seller in the interbank
  market willing to sell at 98.55 (better than the fund's 98.60 limit)
  -> A TRADE now exists, legally, at 09:03:12 — before any
     system has captured it as a data record.

  Trade economics agreed at execution:
    Instrument : Canada 5% Bond 2035 (ISIN CA1358XXXXX)
    Quantity   : CAD 50,000,000 face value
    Price      : 98.55
    Counterparty: Maple Leaf Pension Fund
    Side       : Bank Of Toronto sells / Maple Leaf Pension Fund buys
```

An order can also be **partially filled** — e.g. the desk might only find CAD 30,000,000 face value available at or better than 98.60 in the first pass, execute that slice immediately, and leave the remaining CAD 20,000,000 as a **working order** until more supply appears. Each fill that occurs is its own Execution and, downstream, its own Trade record — one Order can produce many Trades.

**Takeaway:** "Order" and "Trade" are not the same object, and a reporting system that conflates them will show phantom trades for cancelled/unfilled orders, or miss that one order became three separate trade records. The Execution timestamp — not the Order timestamp, and not the Trade Capture timestamp — is the legally binding moment the deal was struck.

---

## 3. Trade Capture — Anatomy of a Trade Record

**Definition:** **Trade Capture** is the moment the Execution becomes an electronic record — a Trade ID is assigned, and every field needed downstream (settlement, valuation, regulatory reporting) is populated for the first time. This usually happens automatically, within milliseconds of execution, but it is a distinct stage: the trade legally exists at Execution; it becomes *data* at Trade Capture.

### Worked example — the captured record for the trade above

```text
TRADE CAPTURE  —  BOT-2026-000901
========================================================================

  Trade ID          : BOT-2026-000901
  Execution Time     : 2026-08-17 09:03:12.184 UTC
  Trade Date         : 2026-08-17  (Monday)
  Settlement Date     : 2026-08-19  (T+2 — see Module 1 §6)
  Instrument         : Canada 5% Bond 2035
  ISIN               : CA1358XXXXX
  Quantity            : CAD 50,000,000 face value
  Price              : 98.55
  Currency            : CAD
  Counterparty        : Maple Leaf Pension Fund
  Side (Bank Of Toronto): SELL
  Trader ID           : T-4471
  Desk/Book           : Government Bonds Flow Desk
  Gross Consideration : 50,000,000 x 98.55 / 100 = CAD 49,275,000
  Status              : NEW
```

Two fields deserve special attention because they trip up reporting systems constantly:

- **Trade Date vs Execution Time** — Trade Date is a *calendar day*; Execution Time is a *precise timestamp*, needed for best-execution analysis and for regulatory reporting deadlines that are measured in minutes, not days (Section 9).
- **Status = NEW** — every trade record starts life with a status, and that status field is what the rest of this module (Sections 4–9) advances through. Section 12 lays out the full state machine.

**Takeaway:** if a Trade record and its parent Order have different quantities, that is not automatically a bug — it may simply mean the order was partially filled across several separate Trade Capture events, each with its own Trade ID.

---

## 4. Confirmation & Affirmation — putting it in writing

**Definition:** **Confirmation** is the message one party sends stating what it believes was agreed (all the economic terms). **Affirmation** is the counterparty's formal agreement that those terms match its own understanding. Only after both steps complete is a trade considered legally settled-in-principle — this is the industry's safeguard against the classic "we both think we agreed to something slightly different" problem.

```text
CONFIRMATION & AFFIRMATION
========================================================================

  Bank Of Toronto                                Maple Leaf Pension Fund
       |                                                    |
       | 09:15  Sends CONFIRMATION                            |
       |   Trade ID BOT-2026-000901                            |
       |   Canada 5% Bond 2035, CAD 50,000,000 @ 98.55         |
       |   Settlement Date 19-Aug-2026                        |
       |------------------------------------------------------>|
       |                                                    |
       |                                    10:30  Reviews terms
       |                                    against its own order
       |                                    record -> MATCHES
       |                                                    |
       |                              10:31  Sends AFFIRMATION|
       |<------------------------------------------------------|
       |                                                    |
  Status: CONFIRMED -> AFFIRMED
```

For cash securities (bonds, equities), this typically happens through platforms such as **CTM** (Central Trade Manager, DTCC) or **Omgeo/Alert**. For OTC derivatives, the equivalent step usually runs through **MarkitWire** or direct **FpML** message exchange between the two counterparties' systems, and is what feeds the ISDA Master Agreement's legal framework referenced in [Module 2](./module-02-hierarchy.md), Section 7.

**Takeaway:** a trade sitting in "CONFIRMED" but not yet "AFFIRMED" status for an unusually long time is a strong signal something's wrong — either the counterparty disagrees with a term (a break, Section 5) or an operational delay is quietly eating into the T+2 settlement runway.

---

## 5. Matching — bilateral matching and trade breaks

**Definition:** **Matching** is the automated, field-by-field comparison of both counterparties' trade records. If every economic field lines up exactly, the trade is **MATCHED** and flows straight through to settlement/clearing. If even one field disagrees, it's a **BREAK** — the trade is held up until a human resolves the discrepancy.

### Worked example — a clean match vs a broken match

```text
MATCHING  —  side-by-side field comparison
========================================================================

FIELD                 BANK OF TORONTO SIDE       PENSION FUND SIDE
-------------------------------------------------------------------
Trade ID (internal)   BOT-2026-000901            MLPF-77213
Instrument (ISIN)     CA1358XXXXX                CA1358XXXXX          OK
Quantity               50,000,000                 50,000,000           OK
Price                  98.55                       98.55                OK
Settlement Date        2026-08-19                  2026-08-19           OK
Currency               CAD                          CAD                  OK
-------------------------------------------------------------------
RESULT: MATCHED  ->  proceeds straight to Settlement (Section 7)


A DIFFERENT TRADE, SAME DAY — a break
========================================================================
FIELD                 BANK OF TORONTO SIDE       COUNTERPARTY SIDE
-------------------------------------------------------------------
Instrument (ISIN)     CA1358XXXXX                CA1358XXXXX          OK
Quantity               10,000,000                  10,000,000          OK
Price                  98.50                        98.65               MISMATCH (15bp)
Settlement Date        2026-08-19                   2026-08-19          OK
-------------------------------------------------------------------
RESULT: BROKEN  ->  held, routed to an Ops queue for investigation

  Root cause found: the counterparty's trader fat-fingered 98.65
  instead of 98.50 when keying the trade into their own system.
  Corrected, re-submitted, re-matched -> MATCHED at 98.50, one
  business hour later than a clean trade would have taken.
```

A 15-basis-point price break on CAD 10,000,000 face value is a difference of `10,000,000 x 0.15 / 100 = CAD 15,000` in consideration — small enough to look trivial in isolation, but exactly the kind of discrepancy that, left unresolved, cascades into a failed settlement (Section 13) two days later.

**Takeaway:** "Matched" and "Confirmed" are not synonyms — Confirmation is one party stating terms; Matching is the automated proof that *both* parties' independently-keyed records agree. A trade can be Confirmed and still fail to Match if the counterparty's own capture had an error.

---

## 6. Clearing — novation through a CCP

**Definition:** **Clearing** only applies to products that are routed through a **CCP (Central Counterparty)** — mandatory for most standardized OTC derivatives since the post-2008 reforms, and standard practice for exchange-traded products. The CCP performs **novation**: a specific legal mechanism (governed by the CCP's own rulebook, not just market convention) that **extinguishes** the original bilateral contract and **replaces** it with two brand-new contracts — one between each original party and the CCP. This is *not* the same as an assignment (where the original contract just changes hands); novation kills the old contract outright and creates two new ones in its place. Once novation completes, the two original counterparties end up with no direct exposure to each other at all — only to the CCP.

> **What is LCH / LCH SwapClear?** **LCH (London Clearing House)** is a CCP group headquartered in London, part of the **London Stock Exchange Group (LSEG)** — one of the world's largest clearing houses. **SwapClear** is LCH's specific clearing *service* for interest rate swaps: the actual named CCP that novates the vast majority of the world's centrally-cleared IRS volume, across many currencies (CAD included, as this worked example uses). "LCH" is the legal entity/group; "SwapClear" is the product line within it — the same relationship as "Bank Of Toronto" (the bank) vs. "Bank Of Toronto's mortgage desk" (a business line inside it). Full market-infrastructure detail in [Systems Landscape](./systems-landscape.md) §6 and §11.

### Worked example — Bank Of Toronto's 5Y CAD IRS from [Module 2](./module-02-hierarchy.md) §12, cleared via LCH SwapClear

```text
BEFORE CLEARING  —  one bilateral trade
========================================================================

  Bank Of Toronto  <====  5Y CAD IRS, Notional CAD 100,000,000  ====>  Goldman Sachs
       (pays fixed 4.00% / receives floating CORRA)

AFTER NOVATION  —  LCH SwapClear steps in; TWO trades now exist
========================================================================

  Bank Of Toronto  <====  IRS #1, CAD 100,000,000  ====>   LCH SwapClear (CCP)
       (pays fixed 4.00% / receives floating)

  LCH SwapClear (CCP)  <====  IRS #2, CAD 100,000,000  ====>   Goldman Sachs
       (pays fixed 4.00% / receives floating — mirror of IRS #1)

  -> The original Bank Of Toronto <-> Goldman Sachs contract no longer
     exists; it was legally extinguished by novation. Bank Of Toronto's
     counterparty risk is now LCH SwapClear, not Goldman Sachs. If
     Goldman Sachs defaulted, LCH SwapClear absorbs the loss and Bank
     Of Toronto's trade with LCH SwapClear is unaffected.
```

Novation is not free — both sides must post **Initial Margin (IM)** to the CCP to cover potential future losses, plus daily **Variation Margin (VM)** as the trade's value moves. Full mechanics of margin calculation belong to [Module 12](./module-12-collateral-margin.md) (Collateral & Margin); here's the shape of the numbers just to make "clearing has a cost" concrete:

```text
CLEARING — ILLUSTRATIVE MARGIN ON THE CAD 100,000,000 IRS
========================================================================

  Initial Margin (IM) — illustrative rate ~2% of notional for a 5Y IRS:
    100,000,000 x 2.00% = CAD 2,000,000
    Bank Of Toronto posts CAD 2,000,000 to the CCP at trade inception.
    Goldman Sachs separately posts its own CAD 2,000,000 to the CCP.

  Variation Margin (VM) — next business day, market rates move,
  the swap's mark-to-market value shifts by CAD 180,000 in
  Bank Of Toronto's favor:
    CCP collects CAD 180,000 from Goldman Sachs' VM account
    CCP pays CAD 180,000 to Bank Of Toronto's VM account
    (VM is exchanged DAILY for the life of the trade — Module 12)
```

**Takeaway:** the reason a cleared trade's record shows a **CCP as counterparty**, not the original bank, is novation — it's not a data error or a proxy field, it's what legally happened at the Clearing stage. Bilateral (uncleared) products, like the plain bond trade in Sections 2–5, simply skip this stage entirely and go straight from Matching to Settlement.

---

## 7. Settlement — DvP mechanics, revisited with real numbers

**Definition:** **Settlement** is where the trade stops being a promise and becomes reality — securities move from seller to buyer, cash moves from buyer to seller, ideally simultaneously via **DvP (Delivery versus Payment)** so neither side is ever left holding one leg without the other. [Module 1](./module-01-fundamentals.md), Sections 6 and 7 covered the T+2 timing and the custodian/CSD chain; here's that mechanism applied to the actual trade this module has been following.

### Worked example — BOT-2026-000901 settles

```text
SETTLEMENT  —  Trade BOT-2026-000901, Settlement Date 2026-08-19 (T+2)
========================================================================

  Bank Of Toronto (Seller)                Maple Leaf Pension Fund (Buyer)
  Custodian: BNY Mellon                    Custodian: State Street
       |                                                    |
       | Instructs BNY Mellon to deliver                     |
       | CAD 50,000,000 face value                          |
       | Canada 5% Bond 2035 (ISIN CA1358XXXXX)               |
       |                                                    |
       |         Both custodians instruct the CSD (CDS)      |
       |         to move bond + cash SIMULTANEOUSLY (DvP)    |
       v                                                    v
  --------------------------------------------------------------------
  |                    CDS — Central Securities Depository            |
  |  09:00:14 (T+2)  Bond: CAD 50,000,000 face value moves from        |
  |                   Bank Of Toronto's account to Maple Leaf's        |
  |  09:00:14 (T+2)  Cash: CAD 49,275,000 moves from Maple Leaf's      |
  |                   cash account to Bank Of Toronto's cash account   |
  |                   (50,000,000 x 98.55 / 100)                       |
  --------------------------------------------------------------------

  RESULT: both legs move in the same instant. Neither party is ever
  exposed to "I delivered/paid and the other side didn't."
  Status: MATCHED -> SETTLED
```

**Takeaway:** DvP is the whole reason "settlement risk" (also called Herstatt risk) is manageable for securities in modern markets — the two legs are mechanically tied together at the CSD so a partial failure (bond moves, cash doesn't) simply cannot happen for products settling through this rail. Section 13 covers what happens instead when the *whole trade* fails to settle by T+2.

---

## 8. Reconciliation — the three-way tie-out

**Definition:** **Reconciliation** is the process of comparing the same trade/position across multiple independent records — the firm's own internal books, the custodian's statement, and (where relevant) the CCP's records — to prove they all agree. It happens continuously, but the most important checkpoint is end-of-day after settlement.

### Worked example — a clean tie-out vs a break

```text
THREE-WAY RECONCILIATION  —  End of Day, 19-Aug-2026
========================================================================

                    Internal Books      Custodian (BNY Mellon)   CDS Record
Position in Canada
5% Bond 2035 (CAD)  450,000,000         450,000,000               450,000,000

RESULT: TIED OUT — all three sources agree. No action needed.


A DIFFERENT POSITION, SAME DAY — a break
========================================================================
                    Internal Books      Custodian (BNY Mellon)   CDS Record
Position in Bank Of
Toronto 4.5% Bond
2030 (CAD)          120,000,000         119,000,000               119,000,000

RESULT: BREAK — internal books show CAD 1,000,000 more than the
custodian and CDS agree on.

  Investigation finds: a CAD 1,000,000 face value SELL trade settled
  correctly at the custodian two days ago, but the internal position-
  keeping system never received the settlement confirmation message
  that would have decremented the internal position.
  -> Internal books corrected to 119,000,000. Root cause: a dropped
     message between the settlement system and the position-keeping
     system — flagged for the platform team, not a real economic loss.
```

**Takeaway:** reconciliation breaks are not automatically "the custodian is wrong" or "we made a trading mistake" — as the example shows, the most common root cause in practice is an internal system simply failing to process a downstream event. This is precisely the class of bug an IT/reporting engineer gets pulled into: the money is fine, the *data* disagrees.

---

## 9. Regulatory Reporting — the T+1 deadline

**Definition:** Once a trade is executed, most jurisdictions require it to be reported to a regulator or Trade Repository within a strict deadline — commonly **T+1** (by the end of the next business day), though some in-scope products/venues require **same-day** or even **near-real-time** reporting. This is the final stage of the lifecycle and, as [Module 1](./module-01-fundamentals.md) §5 and [Module 2](./module-02-hierarchy.md) §12 established, applies differently depending on whether the trade is exchange-traded (the exchange itself is often treated as the report) or OTC (the firm must actively report).

### Worked example — reporting BOT-2026-000901 and the cleared IRS from Section 6

```text
REGULATORY REPORTING  —  T+1 deadline, two different trades
========================================================================

TRADE 1 — Bond, BOT-2026-000901
  Executed          : Mon 17-Aug-2026, 09:03:12 UTC
  Reporting Deadline : Tue 18-Aug-2026, 23:59:59 UTC   (T+1)
  Reported            : Mon 17-Aug-2026, 18:40:00 UTC   -> WELL WITHIN DEADLINE
  Report includes     : Trade ID, ISIN, Quantity, Price, Counterparty LEI,
                        Trade Date/Time, Venue (OTC)

TRADE 2 — Cleared 5Y CAD IRS (Section 6)
  Executed            : Mon 17-Aug-2026, 14:12:05 UTC
  Reporting Deadline   : Tue 18-Aug-2026, 23:59:59 UTC   (T+1)
  Reported              : Tue 18-Aug-2026, 08:15:00 UTC   -> WITHIN DEADLINE
  Report includes       : UTI (Section 12, Module 2), UPI (Module 2 §12),
                          Notional, Fixed Rate, Counterparty LEI,
                          CCP LEI (post-novation), ISDA Taxonomy fields

MISSED DEADLINE EXAMPLE
  If Trade 2 wasn't reported until Wed 19-Aug-2026, 09:00:00 UTC,
  that's a LATE report — a regulatory breach in itself, independent
  of whether the trade settled/cleared correctly. Firms track this
  as a control metric (% of trades reported within deadline), not
  just a per-trade pass/fail.
```

**Takeaway:** regulatory reporting deadlines are measured from **Execution Time** (Section 2), not Trade Capture time or Settlement Date — a trade that settles perfectly two days later can still represent a reporting breach if the report itself missed its T+1 window on day one.

---

## 10. End-to-End Timeline — One Bond Trade, Order to Report

Pulling every stage above together for the single trade this module has followed throughout, `BOT-2026-000901`:

```text
BOT-2026-000901  —  FULL LIFECYCLE TIMELINE
========================================================================

Mon 17-Aug-2026 (T)
  09:02:00   ORDER placed by Maple Leaf Pension Fund               [Sec 2]
  09:03:12   EXECUTION — trade agreed at 98.55                     [Sec 2]
  09:03:12   TRADE CAPTURE — Trade ID BOT-2026-000901 assigned      [Sec 3]
  09:15:00   CONFIRMATION sent to Maple Leaf Pension Fund           [Sec 4]
  10:31:00   AFFIRMATION received                                  [Sec 4]
  10:32:00   MATCHING — fields agree, status MATCHED                [Sec 5]
             (no Clearing stage — plain bond trade, bilateral)      [Sec 6]
  18:40:00   Reported to Trade Repository (well within T+1)        [Sec 9]

Wed 19-Aug-2026 (T+2)
  09:00:14   SETTLEMENT — DvP at the CDS, bond + cash both move      [Sec 7]
  17:00:00   RECONCILIATION — internal books tie out to custodian    [Sec 8]
             and CDS record. Status: SETTLED -> RECONCILED.

Total elapsed time, Order to fully reconciled: ~56 hours, spanning
one weekday plus the standard T+2 settlement cycle. Every stage
above ran cleanly — no breaks, no fails.
```

**Takeaway:** this is the "happy path" every reporting system is designed around. Sections 5, 8, and 13 show what each stage's *unhappy path* looks like — worth re-reading this timeline after those sections to see exactly where a break or fail would have inserted extra days.

---

## 11. End-to-End Timeline — One Cleared Interest Rate Swap

Same exercise, for the Section 6 IRS — notice Clearing inserts real extra steps (and extra days) that the plain bond trade above never needed.

```text
5Y CAD IRS, CAD 100,000,000 NOTIONAL  —  FULL LIFECYCLE TIMELINE
========================================================================

Mon 17-Aug-2026 (T)
  14:10:00   EXECUTION — Bank Of Toronto & Goldman Sachs agree      [Sec 2]
             terms bilaterally (fixed 4.00% vs floating CORRA)
  14:12:05   TRADE CAPTURE in both firms' systems                   [Sec 3]
  14:20:00   CONFIRMATION exchanged via FpML/MarkitWire              [Sec 4]
  14:45:00   AFFIRMATION received, MATCHED                          [Sec 5]
  15:00:00   Submitted to CCP (e.g. LCH) for clearing                [Sec 6]
  16:30:00   NOVATION completes — CCP is now legal counterparty
             to both sides. Two new trades exist (IRS #1, IRS #2).
  17:00:00   Initial Margin call: CAD 2,000,000 due from each side   [Sec 6]

Tue 18-Aug-2026 (T+1)
  09:00:00   Bank Of Toronto posts CAD 2,000,000 IM to the CCP
  08:15:00   Reported to Trade Repository — UTI + UPI included       [Sec 9]
             (within T+1 deadline)
  17:00:00   First Variation Margin call: CAD 180,000 moves from
             Goldman Sachs' account to Bank Of Toronto's account
             via the CCP (Section 6)

Every business day thereafter, for 5 years:
  - Daily VM exchanged based on the swap's mark-to-market movement
  - Quarterly net cash flow paid per Module 1 §8's mechanics
    (~CAD 187,192 per quarter in that worked example)

Total elapsed time, Execution to fully cleared + margined:
~27 hours — one extra full stage (Clearing) versus the bond trade,
plus an ongoing daily margin-exchange relationship that a plain
bond trade never has.
```

**Takeaway:** "settlement" for a cleared derivative doesn't mean one T+2 event like a bond — it means novation, an initial margin call, and then a recurring daily/quarterly cash-flow relationship with the CCP for the life of the trade. Any report showing a derivative trade as "settled" on a single date is almost certainly using the word loosely; the more accurate status is "cleared," with ongoing margin activity continuing underneath it.

---

## 12. Trade Status State Machine — Reference Table

Every stage in Sections 1–9 leaves behind a status value. This is the reference table to check whenever a trade record's `status` field doesn't make sense at first glance — the diagram below shows the same information as a flow, the table beneath it as a lookup.

```text
TRADE STATUS STATE MACHINE
========================================================================

  NEW                                                  [Trade Capture · Sec 3]
   |
   v
  CONFIRMED ------------------------+                   [Confirmation · Sec 4]
   |                                |
   v                                v
  AFFIRMED                      DISPUTED
   |                                |
   |                    (term corrected, re-sent)
   |                                |
   |                                +--> back to CONFIRMED
   v
  MATCHED <---------------------------+                      [Matching · Sec 5]
   |                                  |
   |                     (mismatch corrected)
   |                                  |
   |                               BROKEN <-- mismatch found
   |
   |-- cash product, no clearing --------------------+
   |                                                  |
   v  derivative / cleared product                    |
  PENDING CLEARING                                    |       [Clearing · Sec 6]
   |                                                   |
   v                                                   |
  CLEARED                                              |
   |                                                   |
   v                                                   v
  SETTLED <----------------------------------------------           [Settlement · Sec 7]
   |            ^     (CLEARED -> SETTLED is the rare "cleared cash"
   |            |      case; most cleared derivatives instead stay in
   |         FAILED    an ongoing CLEARED state with recurring margin
   |            |       activity, never reaching a discrete SETTLED)
   |    (re-attempted, resolved -- Sec 13)
   |            ^
   |            +-- Settlement Date passes, no completion
   v
  RECONCILED <----------------------------+               [Reconciliation · Sec 8]
   |                                      |
   |                     (investigated & resolved)
   |                                      |
   |                              BREAK (RECON) <-- books disagree
   v
  terminal (happy path)


  Off this spine, set independently once trade economics exist:
    REPORTED    filed to Trade Repository / regulator, terminal   [Reg. Reporting · Sec 9]

  From ANY status above:
    ---> CANCELLED   trade voided before Settlement is reached, terminal
    ---> AMENDED     a field is corrected; status reverts to whatever it held before
```

| Status | Meaning | Set at which stage | Typical next status |
|---|---|---|---|
| `NEW` | Trade captured, nothing verified yet | Trade Capture (Sec 3) | `CONFIRMED` |
| `CONFIRMED` | This firm's terms sent to counterparty | Confirmation (Sec 4) | `AFFIRMED` or `DISPUTED` |
| `AFFIRMED` | Counterparty agreed to the stated terms | Confirmation (Sec 4) | `MATCHED` |
| `DISPUTED` | Counterparty pushed back on a term | Confirmation (Sec 4) | `CONFIRMED` (after correction) |
| `MATCHED` | Both sides' independent records agree exactly | Matching (Sec 5) | `CLEARED` or `SETTLED` |
| `BROKEN` | A field mismatch was found during matching | Matching (Sec 5) | `MATCHED` (after correction) |
| `PENDING CLEARING` | Submitted to CCP, novation not yet complete | Clearing (Sec 6) | `CLEARED` |
| `CLEARED` | CCP has novated the trade; margin obligations active | Clearing (Sec 6) | `SETTLED` (rare, cleared cash) or ongoing |
| `SETTLED` | Securities and cash have exchanged (DvP complete) | Settlement (Sec 7) | `RECONCILED` |
| `FAILED` | Settlement Date passed without completion | Settlement (Sec 7) | `SETTLED` (once resolved) — see Sec 13 |
| `RECONCILED` | Internal books tie out to custodian/CCP records | Reconciliation (Sec 8) | terminal (happy path) |
| `BREAK (RECON)` | Internal books disagree with an external record | Reconciliation (Sec 8) | `RECONCILED` (after investigation) |
| `REPORTED` | Submitted to the Trade Repository/regulator | Reg. Reporting (Sec 9) | terminal |
| `CANCELLED` | Trade voided before it reached settlement | Any stage before Settlement | terminal |
| `AMENDED` | A field was corrected after initial capture, generating an audit trail entry | Any stage | reverts to the status it was amended from |

**Takeaway:** a healthy trade visits roughly seven of these statuses in sequence with zero detours (Sections 10–11's timelines). Every other status in this table — `DISPUTED`, `BROKEN`, `FAILED`, `BREAK (RECON)` — exists specifically to hold a trade that needs a human to intervene before it can continue down the happy path.

---

## 13. When Things Go Wrong — A Settlement Fail, With Numbers

**Definition:** A **settlement fail** occurs when a trade's Settlement Date arrives and one leg (usually the securities leg) cannot complete — most commonly because the seller doesn't actually hold enough of the security in its custody account to deliver. Because DvP (Section 7) mechanically prevents a *partial* failure, the whole settlement simply doesn't happen at all, and both legs remain outstanding until resolved.

### Worked example — Bank Of Toronto fails to deliver

```text
SETTLEMENT FAIL  —  Trade BOT-2026-000955
========================================================================

  Instrument  : Bank Of Toronto 4.5% Bond 2030
  Quantity     : CAD 10,000,000 face value
  Price        : 99.20
  Counterparty : Goldman Sachs (buyer)
  Settlement Date (T+2): Wed 19-Aug-2026

  19-Aug-2026, 09:00: DvP instruction sent to the CDS.
  19-Aug-2026, 09:00: FAILS — Bank Of Toronto's custody account only
                       holds CAD 9,000,000 face value of this bond,
                       CAD 1,000,000 short (a separate internal
                       position error, exactly the flavor of bug
                       described in Section 8).

  Status: SETTLED -> FAILED

  Trade re-attempted daily; actually settles 2 business days late,
  on Fri 21-Aug-2026.
```

Under settlement-discipline regimes (e.g. **CSDR** in the EU), a late settlement generates a **daily cash penalty**, charged to the failing party, for every business day the fail persists:

```text
CASH PENALTY CALCULATION  —  illustrative daily rate 0.10 bps for a
government/corporate bond, applied per business day late
========================================================================

  Daily Penalty = Reference Price x Quantity x (Penalty Rate / 10,000)

  Reference Price   = 99.20 (per 100 face value)
  Quantity           = CAD 10,000,000 face value
  Penalty Rate        = 0.10 bps = 0.0010%

  Market Value used for the calc = 10,000,000 x 99.20 / 100
                                  = CAD 9,920,000

  Daily Penalty = 9,920,000 x 0.0010 / 100 = CAD 99.20 per day late

  2 business days late (settled 21-Aug instead of 19-Aug):
    Total penalty = 99.20 x 2 = CAD 198.40

  -> Charged to Bank Of Toronto, the failing (delivering) party.
```

The cash penalty here looks small, but the real cost of a fail is rarely the penalty itself — it's the **downstream risk**: if Goldman Sachs needed those bonds to deliver into a *different* trade of its own, Bank Of Toronto's fail can cascade into a second fail elsewhere in the market (sometimes called a "fails chain"), and after a defined grace period, the buyer can exercise a **buy-in** — sourcing the securities elsewhere in the market and charging the original seller any price difference plus costs.

**Takeaway:** a `FAILED` status in a settlement system is a direct, traceable consequence of a break upstream — very often, as here, the exact same category of internal position-keeping error described in Section 8's reconciliation break. Chasing "why did this trade fail?" usually leads straight back to "why did our books show more than we actually held?" — which is precisely why Reconciliation (Section 8) and Settlement (Section 7) are adjacent stages, not distant ones, in the lifecycle.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Maple Leaf Pension Fund | Buy-side client placing the order that becomes Trade BOT-2026-000901 |
| Bank Of Toronto | Sell-side dealer executing, capturing, confirming, clearing, and settling trades |
| Goldman Sachs | Counterparty on the cleared IRS and on the failed bond trade example |
| BNY Mellon / State Street | Custodians instructing settlement on behalf of their respective clients |
| CDS (Canadian Depository for Securities) | CSD where the bond trade's DvP settlement actually occurs |
| CCP (e.g. LCH) | Central Counterparty that novates the cleared IRS and manages ongoing margin |
| Trade Repository | Destination for the T+1 regulatory reports in Section 9 |

---

## Recap Quiz — Numbers Edition

1. Maple Leaf Pension Fund places a limit order to buy at 98.60 or better, and Bank Of Toronto executes at 98.55. Whose favor did the fill land in, and by how much per CAD 100 of face value?
2. On Trade BOT-2026-000901, name the four fields that were still `null`/unknown at the moment the Order was placed (09:02:00) but were fully populated one second later at Execution/Trade Capture (09:03:12).
3. A trade is `CONFIRMED` but not yet `AFFIRMED` for six hours, well past the firm's usual turnaround. What does that specifically tell you happened (or didn't happen) — and which is the earlier stage, Confirmation or Matching?
4. Two firms capture the same trade with an ISIN mismatch (one system stored a stale ISIN after a bond's identifier was reissued). Will this trade reach `MATCHED` status? Why or why not?
5. On the CAD 100,000,000 cleared IRS, after novation, name the two new bilateral trades that exist and who the counterparty is on each, from Bank Of Toronto's perspective.
6. Why does a cleared derivative never reach a `SETTLED` status the same way the bond trade in Section 10 does — what status does it reach instead, and what keeps happening after that?
7. A reconciliation break shows internal books CAD 1,000,000 higher than the custodian and CSD, which agree with each other. Is this most likely a custodian error, a market-side loss, or something else — and what?
8. Trade 2 in Section 9 was executed Monday 14:12:05 UTC and reported Tuesday 08:15:00 UTC. Was this within the T+1 deadline? What UTC timestamp would have made it a missed deadline?
9. Bank Of Toronto's bond trade fails to settle by 3 business days instead of 2 (i.e., 1 day late) under the illustrative CSDR-style penalty in Section 13, on CAD 10,000,000 face value at reference price 99.20 and rate 0.10bps/day. What's the cash penalty for that 1 day?
10. Put these five statuses in the correct chronological order for a normal (no-break) bilateral bond trade: `SETTLED`, `NEW`, `RECONCILED`, `MATCHED`, `AFFIRMED`.

Answer whenever ready — then we move to **[Module 4](./module-04-equity.md): Fixed Income / Bonds**.

---

## Answers — Recap Quiz, with Explanations

**1. The fill landed in Maple Leaf Pension Fund's favor, by 0.05 (5 basis points) per CAD 100 face value.**

The fund's limit was "98.60 or better" (i.e., 98.60 or lower, since it's a buyer — lower price is better for a buyer). Bank Of Toronto executed at 98.55, which is 0.05 below the limit — the fund paid less than its worst-acceptable price, i.e., a better outcome for the buyer.

**2. Trade ID, Execution Time, Price, and Status (as `NEW`) were all unknown at 09:02:00 and populated by 09:03:12.**

At Order time (09:02:00), only intent existed — "buy CAD 50,000,000 at 98.60 or better" — with no confirmed counterparty, no agreed price, and certainly no Trade ID yet, since a Trade ID is only assigned once an actual Execution has occurred and Trade Capture runs (Section 3).

**3. It most likely means the counterparty hasn't reviewed/responded yet — an operational delay, or it's sitting in a dispute queue — and Confirmation is the earlier stage; Matching comes after Affirmation.**

Looking at the sequence in Section 1: Confirmation → Matching, with Affirmation happening as part of the Confirmation exchange (Section 4) before the trade can even be submitted for the automated Matching step (Section 5). A six-hour gap between Confirmed and Affirmed means the counterparty simply hasn't formally agreed yet — it hasn't even reached the point where an automated field-by-field comparison (Matching) could run.

**4. No — it will break, not match.**

Matching (Section 5) is an exact field-by-field comparison. An ISIN mismatch — even if every other field (quantity, price, settlement date) is identical — is exactly the kind of single-field disagreement the "broken match" example in Section 5 showed with price. The trade would be held in `BROKEN` status until someone identifies that one side is using a stale/reissued identifier and corrects it.

**5. IRS #1: Bank Of Toronto vs the CCP (e.g. LCH). IRS #2 exists between the CCP and Goldman Sachs — Bank Of Toronto has no direct exposure to it.**

From Bank Of Toronto's perspective specifically, its only remaining counterparty after novation is the CCP — the original Goldman Sachs relationship on this trade is fully replaced (Section 6). Bank Of Toronto is not a party to IRS #2 at all; that trade exists purely between the CCP and Goldman Sachs.

**6. It reaches `CLEARED`, not `SETTLED` — and what keeps happening afterward is daily Variation Margin exchange plus periodic (quarterly, in this example) net cash-flow payments, for the life of the trade.**

Unlike a bond, where one DvP event on T+2 fully discharges the trade (Section 7), a cleared swap has no notional to deliver ([Module 1](./module-01-fundamentals.md) §8) — there's nothing that constitutes a single "settlement" event. Instead, Section 11 shows the trade moving into an ongoing relationship with the CCP: margin calls the next day, and then a recurring cash-flow schedule for the full 5-year tenor.

**7. Most likely something else entirely — an internal processing/messaging error, not a real economic loss and not a custodian mistake.**

This mirrors the Section 8 worked example exactly: when the custodian and the CSD (two independent, external sources) agree with each other but disagree with internal books, the far more likely explanation is that the firm's own internal position-keeping system failed to process a downstream event (e.g., a dropped settlement confirmation message) — not that money actually went missing, and not that the custodian's own records are wrong.

**8. Yes, within the T+1 deadline. A UTC timestamp any time on or before Tuesday 18-Aug-2026, 23:59:59 UTC would still meet it; anything from Wednesday 19-Aug-2026, 00:00:00 UTC onward would be a missed deadline.**

Executed Monday 14:12:05 UTC means the T+1 deadline runs through the end of the *next* business day — Tuesday 23:59:59 UTC (Section 9). The actual report at Tuesday 08:15:00 UTC comfortably beat that cutoff; only crossing into Wednesday would have breached it.

**9. CAD 99.20.**

```text
Market Value = 10,000,000 x 99.20 / 100 = CAD 9,920,000
Daily Penalty = 9,920,000 x 0.0010 / 100 = CAD 99.20 per day
1 day late    = CAD 99.20 x 1 = CAD 99.20
```

The daily penalty rate itself doesn't change with how many days a trade is late — only the *total* scales with days late. One day late is exactly one day's penalty, CAD 99.20 — precisely half the CAD 198.40 total shown in Section 13 for two days late.

**10. `NEW` → `AFFIRMED` → `MATCHED` → `SETTLED` → `RECONCILED`.**

This follows the Section 1 stage order directly: Trade Capture sets `NEW` (Sec 3); Confirmation produces `AFFIRMED` once the counterparty agrees (Sec 4); Matching then confirms both independently-keyed records agree, `MATCHED` (Sec 5); Settlement moves it to `SETTLED` on T+2 (Sec 7); and the end-of-day tie-out finally lands it at `RECONCILED` (Sec 8) — exactly the sequence walked through in Section 10's end-to-end timeline.

---

[← Back to Index](../../index.md)
