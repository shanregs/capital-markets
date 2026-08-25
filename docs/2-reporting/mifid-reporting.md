# MiFID II / MiFIR Reporting Deep Dive — Transaction Reporting, Transparency &amp; the OTC Equity Rules

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> This is a **reference module**, in the same family as the [SFTR Reporting Deep Dive](./sftr-reporting.md), the [UK DMO Reporting Deep Dive](./dmo-uk-reporting.md), and the [UPI, UTI &amp; ANNA-DSB Deep Dive](./upi-uti-dsb-deep-dive.md). [Module 4](../1-capital-markets/module-04-equity.md) §8, [Module 6](../1-capital-markets/module-06-fx.md) §8, and [Module 14](./module-14-regulatory-reporting.md) §4 each touched MiFID II/MiFIR briefly as part of a broader story; this module is the single place that goes deep on the regime itself — exactly what makes an instrument "MiFID eligible," what a full transaction report contains, and — the question that trips up almost everyone new to this regime — what an **OTC-executed** equity trade is actually subject to.

---

## 1. What Is MiFID II / MiFIR? — Two Legal Instruments, One Regime

**Definition:** "MiFID II" is colloquial shorthand for **two separate pieces of EU law that work together**:

| Instrument | Type | What It Covers | How It Applies |
|---|---|---|---|
| **MiFID II** — Directive 2014/65/EU | Directive | Investment firm authorization, conduct-of-business rules, best execution (Section 7), market structure (the RM/MTF/OTF venue categories) | Transposed into each member state's own national law — provisions can vary slightly country to country |
| **MiFIR** — Regulation (EU) No 600/2014 | Regulation | Transparency (Section 6), the Share/Derivatives Trading Obligations (Section 7), **Transaction Reporting** (Article 26 — Section 3-5) | Directly applicable, identical text, in every EU member state — no national transposition step |

The practical point: the reporting and transparency obligations this module is actually about — the part a trade-capture/reporting system has to build against — live in **MiFIR**, a Regulation, not in the Directive that gives the whole regime its more famous name. **UK MiFIR** is the UK's onshored, retained-EU-law version post-Brexit, supervised by the **FCA**, with the old Directive-side conduct rules now living in the FCA's own Handbook (COBS, MAR).

**Why it exists:** MiFID II/MiFIR replaced the original 2007 MiFID I after the 2008 financial crisis exposed two specific gaps: growing **dark/OTC equity trading** with almost no public transparency, and near-total absence of any transparency regime for non-equity instruments (bonds, derivatives) at all. Alongside EMIR (systemic derivatives risk) and the G20's 2009 Pittsburgh commitments, MiFID II/MiFIR is the EU's answer to "regulators and the public need to actually see what's trading, at what price, and who's doing it" — a different angle on post-crisis reform than EMIR's "how much risk exists" question.

```text
WHERE MiFID II/MiFIR SITS AMONG THE REPORTING REGIMES
========================================================================

  EMIR    ->  reports OTC DERIVATIVE ECONOMICS to a Trade Repository —
              "how much systemic derivatives risk exists?" (Module 2 §7)

  SFTR    ->  reports SECURITIES FINANCING TRANSACTIONS to a Trade
              Repository — "how much collateral re-use/leverage
              exists?" (SFTR Deep Dive §1)

  MiFIR   ->  TWO separate obligations, THIS MODULE:
              (a) Transaction Reporting -> to the National Competent
                  Authority (NCA) directly, PRIVATE — "did anyone
                  behave abusively on this execution?" (Section 2-5)
              (b) Transparency -> to the PUBLIC market via the venue
                  or an APA — "what did this actually trade at?"
                  (Section 6-7)

  All three can fire off the SAME single trade, answering three
  genuinely different questions, to three different destinations,
  under three different legal texts (Module 14 §1's five-obligation
  framing, now with MiFIR's own internal two-way split added).
```

**Takeaway:** the single biggest source of confusion in this regime is treating "MiFID reporting" as one obligation. It is at minimum **two**, covered separately in Sections 2, 3-5, and 6-7 below — conflating them is the most common design mistake in a reporting build.

---

## 2. Three Distinct Obligations Under One Umbrella — Don't Conflate Them

**Definition:** expanding [Module 14](./module-14-regulatory-reporting.md) §4's comparison table, MiFID II/MiFIR actually bundles **three** obligations that a firm executing trades in scope must satisfy independently. Getting any one of them right does not satisfy the other two.

| Dimension | Transaction Reporting (MiFIR Art. 26 / RTS 22) | Transparency Regime (MiFIR Arts. 3-13 / RTS 1, RTS 2) | Best Execution (MiFID II Art. 27) |
|---|---|---|---|
| **Destination** | National Competent Authority (NCA), directly or via an ARM — never public | The public market, via the trading venue itself or an APA (Approved Publication Arrangement) | The client, on request/periodic disclosure — not a regulator at all |
| **Purpose** | Market abuse detection (insider dealing, manipulation) | Price discovery — letting the whole market see what actually traded | Client protection — proving the firm got the client the best available outcome |
| **What's published** | Nothing public — full economic + party detail, restricted to the regulator | Price, volume, time — NOT counterparty identity | Execution-quality summaries (largely reduced in scope since a 2021 MiFID II "Quick Fix" amendment) |
| **Sides reporting** | Generally single-sided — the executing investment firm reports its own execution | Single publication per trade (by whichever side is designated the "reporting party" for transparency purposes) | Owed by the firm to its own client only |
| **Deadline** | T+1 (Section 9) | As close to real-time as technically possible, subject to deferrals (Section 6) | Ongoing obligation, not event-driven |
| **First seen** | [Module 4](../1-capital-markets/module-04-equity.md) §8 | [Module 4](../1-capital-markets/module-04-equity.md) §7's Exchange vs OTC framing | [Module 4](../1-capital-markets/module-04-equity.md) §8 (Systematic Internalizer mention) |

```text
SAME TRADE, TWO DIFFERENT MiFIR OUTPUTS -- NEITHER SUBSTITUTES THE OTHER
========================================================================

  Maple Leaf Pension Fund buys SAP SE shares via Bank Of Toronto,
  executed OTC (bilaterally, off any venue's order book):

  TRANSACTION REPORT (Section 3-5)         TRANSPARENCY PUBLICATION (Section 6)
  ---------------------------------        ------------------------------------
  -> Goes to Bank Of Toronto's NCA          -> Goes PUBLIC, via an APA
     (e.g. the FCA, or BaFin)                  (Section 6)
  -> Full detail: both parties' LEIs,       -> Price, volume, timestamp ONLY --
     trader ID, algo flags, price,             no counterparty names at all
     quantity, venue code "XOFF"            -> Possibly DELAYED if the trade
  -> NEVER visible to the public               qualifies as Large In Scale
     or to Goldman Sachs, a competitor          (Section 6)
  -> Required EVERY time, no exceptions     -> Required EVERY time too --
     for size (Section 3)                      size only changes the TIMING,
                                                never whether it's required

  -> Building only ONE of these two pipelines satisfies HALF the
     regime. A system that publishes to an APA but never files a
     transaction report (or vice versa) is non-compliant either way.
```

**Takeaway:** a transaction report and a transparency publication are not two names for the same document — they carry almost entirely different fields, go to entirely different recipients, and exist to answer entirely different regulatory questions. Section 3 covers who has to file a transaction report at all; Section 6-7 cover the separate transparency obligation, including the specific OTC-equity rules the rest of this module builds toward.

---

## 3. What Makes an Instrument "MiFID Eligible"? — The Article 26(2) Scope Test

**Definition:** MiFIR Article 26(2) defines exactly which financial instruments trigger the Transaction Reporting obligation. An instrument is **MiFID eligible** (i.e., transaction-reportable) if **any one** of these three tests is true:

```text
ARTICLE 26(2) SCOPE TEST -- ANY ONE OF THREE CONDITIONS MAKES IT ELIGIBLE
========================================================================

  (a) THE INSTRUMENT ITSELF is admitted to trading, or actually
      traded, on an EU/UK trading venue (a Regulated Market, MTF,
      or OTF -- Module 1 §5's venue categories) -- OR a request for
      its admission has been made

  (b) THE INSTRUMENT'S UNDERLYING is a financial instrument that is
      admitted to trading or traded on a trading venue (covers a
      derivative referencing a venue-listed share, bond, or index
      constituent)

  (c) THE INSTRUMENT'S UNDERLYING is an INDEX OR BASKET composed of
      financial instruments that are admitted to trading or traded
      on a trading venue (covers, e.g., a derivative on the
      EURO STOXX 50)
```

**The critical point most people new to this regime miss: none of these three tests asks *where the specific trade in question was executed*.** They ask only whether the **instrument** (or its underlying, or its underlying's constituents) is venue-admitted somewhere in the EU/UK. Once that's true, **every single trade in that instrument is transaction-reportable — on-venue, OTC, bilateral, block, anonymous, all of it** — for as long as the instrument stays admitted. This is the single fact that makes an "OTC equity" transaction-reportable at all (Section 7 builds directly on this).

**FIRDS — the operational mechanism.** In practice, no reporting system re-derives Section 3's three tests from first principles for every trade. ESMA operates the **Financial Instruments Reference Data System (FIRDS)**: EU/UK trading venues submit reference data for every instrument they admit, and ESMA (and, separately post-Brexit, the FCA for UK venues) publishes the resulting consolidated instrument list. A reporting engine checks a trade's ISIN against FIRDS (directly, or via a market-data vendor's FIRDS-derived feed) — a simple lookup that encodes Section 3's legal test without re-implementing it.

### Worked example — three trades, three different eligibility answers

```text
TRADE 1 -- BANK OF TORONTO BUYS SAP SE SHARES FOR MAPLE LEAF
PENSION FUND, EXECUTED OTC (BILATERALLY WITH GOLDMAN SACHS)
========================================================================
  Instrument: SAP SE ordinary shares
  Admitted to trading on: Deutsche Börse Xetra (an EU Regulated
    Market) -- present on FIRDS

  -> Test (a): YES, admitted on an EU trading venue
  -> MiFID ELIGIBLE, even though THIS specific trade never touched
     Xetra's order book at all -- it was negotiated bilaterally
     (Section 7 covers exactly what that OTC execution is then
     subject to)

TRADE 2 -- BANK OF TORONTO WRITES MAPLE LEAF PENSION FUND A
BESPOKE OTC EQUITY OPTION ON SAP SE SHARES
========================================================================
  Instrument: bilateral OTC Equity Option (Module 4 §4d's product
    family), Underlying: SAP SE shares

  -> Test (a): NO -- the OPTION itself is never admitted to any
     venue; it's a private bilateral contract
  -> Test (b): YES -- its UNDERLYING (SAP SE shares) IS admitted to
     trading on Xetra
  -> MiFID ELIGIBLE via Test (b) -- the derivative inherits its
     underlying's venue-admitted status, even though the option
     contract itself is entirely bespoke and bilateral

TRADE 3 -- BANK OF TORONTO'S CORPORATE DESK ARRANGES A PRIVATE
SHARE SALE IN AN UNLISTED FAMILY BUSINESS FOR A WEALTH CLIENT
========================================================================
  Instrument: ordinary shares of a private (unlisted) company

  -> Test (a): NO -- not admitted to, or traded on, any trading
     venue anywhere, and no admission request has been made
  -> Test (b) / (c): N/A -- this IS the underlying, there's nothing
     further to check
  -> NOT MiFID eligible -- no MiFIR Transaction Report is required,
     regardless of trade size or how "professionally" it's executed
```

**Takeaway:** "MiFID eligible" is a property of the **instrument** (checked once, via FIRDS), not a property of any individual **execution**. Section 4's "who reports" and Section 5's "what gets reported" only ever apply once Section 3's test has already returned YES for the instrument in question.

---

## 4. Who Must Report, and How — Investment Firms, ARMs &amp; Venues

**Definition:** the Transaction Reporting obligation falls on the **investment firm** executing the transaction — an entity authorized under MiFID II (a bank, broker-dealer, or portfolio manager dealing on own account or executing client orders) — not on the end client or counterparty the way EMIR's dual-sided FC/NFC framework works ([Module 13](../1-capital-markets/module-13-parties-leis.md) §6).

**Generally single-sided, but not the way it first looks.** If a trade is between two MiFID investment firms (Bank Of Toronto and Goldman Sachs, say), **both** independently submit a transaction report — but this is *not* EMIR's dual-sided, paired-on-UTI model. Each firm reports its **own** execution, on its **own** book, to its **own** (possibly different) NCA; there is no Trade Repository pairing/reconciliation step matching the two reports against each other the way [Module 14](./module-14-regulatory-reporting.md) §3 described for EMIR. Two firms' reports for what is economically the same trade simply exist independently, each valid on its own.

| Reporting Channel | How It Works |
|---|---|
| **Direct submission** | The investment firm builds and submits its own reports straight to its NCA |
| **ARM (Approved Reporting Mechanism)** | A regulator-authorized third party that validates, formats, and transmits reports on the firm's behalf — the MiFIR-world counterpart to a Trade Repository, but for a fundamentally different report type and destination |
| **Trading venue transmission** | For trades actually executed on a venue, the venue itself may transmit certain order/execution details on the firm's behalf under specific conditions, reducing (not eliminating) the firm's own reporting burden |

**Takeaway:** don't reuse EMIR's dual-sided pairing mental model here — a MiFIR transaction report is a private, single-firm submission with no counterparty-matching step at the regulator's end, which is exactly why Section 5's report has to be self-sufficiently complete (there's no reconciliation process to catch a field-level error the way [Module 14](./module-14-regulatory-reporting.md) §3's EMIR pairing does).

---

## 5. Transaction Report Content — RTS 22 Field Groups, Worked

**Definition:** MiFIR's technical standard **RTS 22** defines roughly **65 reportable fields**, grouped by what they describe:

| Field Group | Contains |
|---|---|
| **Report &amp; venue identification** | Report status, executing entity's LEI, trading venue code (or `XOFF` for OTC — Section 5's worked example uses this) |
| **Buyer/seller identification** | LEI (for a legal entity) or a national identifier (for a natural person), plus the investment decision maker and executing trader/algo IDs |
| **Instrument identification** | ISIN ([Module 2](../1-capital-markets/module-02-hierarchy.md) §2), CFI code ([Module 2](../1-capital-markets/module-02-hierarchy.md) §6) |
| **Transaction details** | Trading date/time (to the microsecond, for venue-executed trades), price, quantity, currency |
| **Flags** | Short sale indicator, waiver indicator (which pre-trade transparency waiver applied, if any — Section 6), commodity derivative position-limit flag ([Module 7](../1-capital-markets/module-07-commodities.md) §8), OTC post-trade indicator |

### Worked example — Trade 1's SAP SE purchase, transaction-reported

```text
RTS 22 TRANSACTION REPORT -- BANK OF TORONTO'S SIDE
========================================================================
  Executing entity LEI:      Bank Of Toronto's LEI
  Buyer:                     Maple Leaf Pension Fund's LEI
  Seller:                    Goldman Sachs' LEI
  Investment decision maker: Maple Leaf Pension Fund's portfolio
                              manager (national ID, per RTS 22)
  Executing trader:          Bank Of Toronto desk trader ID

  Instrument (ISIN):         DE0007164600 (SAP SE)
  CFI Code:                  ESVUFR (equity, common share)

  Trading date/time:         2026-08-25T14:32:07.219Z
  Venue:                     XOFF (executed OTC -- Section 3's Trade 1)
  Price:                     EUR 190.00 per share
  Quantity:                  10,000 shares
  Trade value:                EUR 1,900,000.00

  Short sale indicator:      No
  Waiver indicator:          Not applicable (Section 6 waivers apply
                              to venue PRE-trade transparency, not to
                              an off-venue OTC execution)

  -> Submitted to Bank Of Toronto's NCA by T+1, NEVER made public
     (Section 2's comparison table)
```

**Takeaway:** notice `XOFF` — the standard venue code meaning "executed off any trading venue" — appears directly in the transaction report itself. An OTC execution isn't hidden from the regulator by being off-venue; it's explicitly flagged as such and reported with exactly the same field completeness as an on-venue trade would carry.

---

## 6. The Transparency Regime — Pre-Trade &amp; Post-Trade

**Definition:** entirely separate from Section 3-5's private Transaction Report, MiFIR's transparency regime (Articles 3-13, RTS 1 for equity-like instruments) requires certain trading activity in MiFID-eligible equities to be made **public**.

| Transparency Type | What It Requires | Applies To |
|---|---|---|
| **Pre-trade transparency** | Publishing current firm bid/offer quotes and order-book depth, in real time | Trading venues always; a firm dealing OTC only if it's a **Systematic Internaliser** for that instrument (Section 7) |
| **Post-trade transparency** | Publishing price, volume, and time for every executed trade, "as close to real-time as technically possible" | On-venue trades (via the venue itself); OTC trades (via an **APA**, Approved Publication Arrangement) |

**Deferred publication — the operationally important nuance.** A trade that qualifies as **Large In Scale (LIS)** relative to the instrument's normal trading size can have its post-trade publication **delayed** — anywhere from minutes to end-of-day (or longer, for the least liquid instruments), rather than published immediately. This protects a firm executing a large block from adverse market impact while it's still working the position, and it's why a genuinely huge OTC block trade and a small OTC trade in the same share can be transparency-compliant on wildly different publication timelines despite an identical transaction-reporting obligation (Section 2's diagram already flagged this: size changes the *timing*, never the *requirement*).

### Worked example — Trade 1, published for transparency

```text
POST-TRADE TRANSPARENCY -- TRADE 1'S SAP SE PURCHASE
========================================================================
  Illustrative LIS threshold for SAP SE's liquidity band: EUR 650,000

  Trade value: EUR 1,900,000.00  (Section 5's report)
  1,900,000 > 650,000  ->  QUALIFIES AS LARGE IN SCALE

  -> Bank Of Toronto (as the designated reporting side) publishes
     via an APA: price EUR 190.00, quantity 10,000 shares, timestamp
     -- but DEFERRED, e.g. until end of trading day, rather than
     immediately
  -> NO counterparty names published at any point -- contrast
     Section 5's transaction report, which names both LEIs privately
     to the NCA
```

**Takeaway:** the same trade generates two outputs on two entirely different clocks — a private, undeferrable, T+1 transaction report (Section 5), and a public, potentially-deferred transparency publication (this section) — and mixing up which deadline applies to which output is a genuine, common build mistake.

---

## 7. OTC Equities — What They're Actually Subject To

**This is the question this module exists to answer directly.** "Traded OTC" does **not** mean "outside MiFID." An OTC-executed trade in a MiFID-eligible equity (Section 3) is simultaneously subject to **up to four separate obligations**:

```text
AN OTC EQUITY TRADE'S FOUR POTENTIAL OBLIGATIONS
========================================================================

  1. TRANSACTION REPORTING (Section 3-5)
     -> ALWAYS required if the instrument is MiFID eligible.
        No exceptions for size, venue, or counterparty type.

  2. POST-TRADE TRANSPARENCY (Section 6)
     -> ALWAYS required too -- publication via an APA, with
        possible LIS deferral (Section 6). Size changes WHEN,
        never WHETHER.

  3. BEST EXECUTION (MiFID II Art. 27)
     -> The firm must still take "all sufficient steps" to get the
        client the best possible result, even with no venue order
        book to benchmark against directly -- an ongoing conduct
        obligation, not a per-trade filing.

  4. THE SHARE TRADING OBLIGATION -- STO (MiFIR Art. 23)
     -> THE RULE THAT ACTUALLY RESTRICTS whether a trade can be
        purely bilateral OTC in the first place (below).
```

### 7a. The Share Trading Obligation (STO) — the rule that actually limits OTC equity trading

**Definition:** MiFIR Article 23 requires that shares admitted to trading on an EU Regulated Market generally be traded **on** a venue (a Regulated Market, MTF, OTF, or an equivalent third-country venue) **or through a Systematic Internaliser (Section 7b)** — not purely bilaterally. Genuine, unstructured bilateral OTC trading in a liquid, EU-RM-admitted share is the **exception**, permitted only where the trade is:

- **"Non-systematic, ad-hoc, irregular and infrequent,"** or
- Carried out between eligible/professional counterparties and **does not contribute to price discovery** (e.g. a portfolio transfer, an internal reallocation, or a trade priced by reference to a venue price rather than negotiated fresh).

```text
STO WORKED EXAMPLE -- CAN THIS SAP SE TRADE BE PURE BILATERAL OTC?
========================================================================
  SAP SE: admitted to trading on Deutsche Börse Xetra (Section 3),
  a liquid, actively-traded EU blue-chip

  Bank Of Toronto wants to sell 10,000 SAP SE shares to Goldman
  Sachs via a one-off negotiated bilateral trade, priced off
  Xetra's current quote, as an internal portfolio rebalancing --
  NOT part of a regular OTC trading pattern in this name

  -> Does the trade contribute to price discovery? NO -- it's
     referenced to an existing venue price, not independently
     negotiated
  -> STO carve-out APPLIES -> this specific trade CAN be executed
     bilaterally OTC

  Contrast: if Bank Of Toronto ran a REGULAR, ongoing bilateral
  OTC dealing business in SAP SE shares against client flow
  (systematic, not ad-hoc) -> the carve-out does NOT apply -> Bank
  Of Toronto must either route the flow through a venue, OR
  register as a Systematic Internaliser for SAP SE and trade it
  through that regime instead (Section 7b)
```

**This is the crux of "what are OTC equities subject to":** for the small minority of genuinely ad-hoc, non-price-forming bilateral trades, Sections 1-3 above still apply in full (transaction reporting, transparency, best execution) — but for anything resembling a **regular OTC dealing business** in a liquid EU share, the STO pushes that activity into the **Systematic Internaliser** regime instead of leaving it as unstructured bilateral OTC at all.

### 7b. The Systematic Internaliser (SI) regime

**Definition:** a firm that deals on its own account executing client orders OTC on an **organized, frequent, systematic, and substantial basis** in a given instrument — above quantitative thresholds ESMA calibrates and publishes periodically — **must register as a Systematic Internaliser** for that instrument. Once registered, an SI takes on obligations that make its "OTC" dealing behave much more like a mini trading venue:

| SI Obligation | What It Means |
|---|---|
| **Pre-trade transparency (Section 6)** | Must publish firm, executable quotes, up to a defined **Standard Market Size (SMS)**, that any client can deal on |
| **Execution at quoted prices** | Must generally execute client orders up to SMS at its published quote (subject to legitimate price improvement) |
| **Post-trade transparency (Section 6)** | Must itself publish the trade via an APA, rather than leaving that solely to the client's own investment firm |
| **Transaction reporting (Section 3-5)** | Unchanged — still required, exactly as for any other MiFID-eligible instrument |

```text
SI vs NON-SI OTC DEALER -- SAME INSTRUMENT, DIFFERENT OBLIGATIONS
========================================================================

  Bank Of Toronto deals SAP SE shares OTC against client flow
  frequently enough to cross ESMA's SI threshold for this name:

  -> REGISTERED SI for SAP SE
  -> Must publish firm bid/offer quotes up to SMS (illustrative:
     SMS = 7,500 shares for this liquidity band)
  -> A client order for 5,000 shares (under SMS) -> Bank Of Toronto
     MUST deal at (or better than) its published quote
  -> A client order for 50,000 shares (well over SMS) -> SI quoting
     obligation doesn't bind at that size; negotiated bilaterally,
     LIS deferral (Section 6) likely available given the size

  A SMALLER firm dealing SAP SE OTC only occasionally, below the
  threshold:

  -> NOT an SI for SAP SE
  -> No pre-trade quote-publication obligation
  -> STILL owes transaction reporting (Section 3-5) and post-trade
     transparency (Section 6) on every trade, and its bilateral
     dealing pattern must stay within the STO's "ad-hoc, irregular,
     infrequent" carve-out (Section 7a) or it risks needing to
     become an SI (or route to a venue) itself
```

**Takeaway:** the SI regime is what MiFID II uses to bring transparency to the OTC equity market without forcing every OTC trade onto a formal exchange — a firm crosses from "occasional bilateral dealer" (light obligations, but STO-constrained in how often it can do this) to "Systematic Internaliser" (heavier, venue-like pre-trade quoting obligations) once its own OTC dealing activity in a name becomes big and frequent enough to functionally *be* a trading venue for that instrument.

### 7c. Full decision framework — is this OTC equity trade compliant?

```text
Q1. Is the instrument MiFID eligible?                 -> Section 3
    NO  -> stop, no MiFIR obligations apply at all
    YES -> continue

Q2. Is a Transaction Report being filed, T+1?          -> Section 5
    Required regardless of every answer below

Q3. Is post-trade transparency being published,
    via the venue or an APA (with LIS deferral if
    applicable)?                                       -> Section 6
    Required regardless of every answer below

Q4. Is this trade genuinely ad-hoc/non-price-forming,
    OR is it part of a regular OTC dealing pattern?     -> Section 7a
    Ad-hoc      -> STO carve-out applies, stays bilateral OTC
    Regular     -> continue to Q5

Q5. Does the firm's OTC dealing volume in this
    instrument cross the SI threshold?                  -> Section 7b
    NO  -> must route to a venue instead of continuing
           informal bilateral OTC dealing
    YES -> firm must be registered as an SI and quote
           accordingly
```

---

## 8. Worked End-to-End Example — One OTC Equity Trade, Every Obligation Together

```text
BANK OF TORONTO (REGISTERED SI FOR SAP SE), SELLS 5,000 SAP SE
SHARES OTC TO A RETAIL-SIZE CLIENT ORDER
========================================================================
  Instrument: SAP SE (ISIN DE0007164600) -- Section 3's Test (a),
              MiFID eligible

  Client:     Maple Leaf Pension Fund, order for 5,000 shares
  SI quote:   Bank Of Toronto's published bid/offer for SAP SE,
              valid up to SMS = 7,500 shares (Section 7b)
  5,000 < 7,500 -> WITHIN SMS -> Bank Of Toronto must deal at (or
              better than) its own published quote: EUR 190.05
  Trade value: 5,000 x 190.05 = EUR 950,250.00

  1) TRANSACTION REPORT (Section 5)
     -> Filed by Bank Of Toronto to its NCA, T+1, full detail,
        venue code XOFF, never public

  2) POST-TRADE TRANSPARENCY (Section 6)
     -> EUR 950,250 vs. an illustrative LIS threshold of EUR
        650,000 for this band -> 950,250 &gt; 650,000 -> QUALIFIES
        FOR DEFERRAL -- published via APA, price/volume only, but
        not necessarily instantaneous
     -> As the registered SI, Bank Of Toronto itself is the
        publishing party (Section 7b), not the client's own firm

  3) BEST EXECUTION (Section 7)
     -> Bank Of Toronto must be able to demonstrate EUR 190.05 met
        or bettered prevailing market conditions for this size and
        instrument -- an ongoing conduct obligation, evidenced on
        request, not a per-trade filing

  4) STO / SI STATUS (Section 7a-b)
     -> Compliant BY CONSTRUCTION: trading through a registered SI
        at its published quote is one of the STO's own accepted
        routes for share trading, not a carve-out exception --
        Bank Of Toronto's SI registration is precisely what makes
        this OTC-labeled trade legally equivalent to an on-venue
        execution for STO purposes
```

**Takeaway:** a single OTC equity trade at a registered SI generates a private transaction report, a public (possibly deferred) transparency publication, an implicit best-execution obligation, and satisfies the Share Trading Obligation all at once, entirely without ever touching a formal exchange order book — proof that "OTC" in MiFID II's world describes *where* a trade was negotiated, never *whether* it's regulated.

---

## 9. Reporting Deadline &amp; Record-Keeping

**Transaction Reporting deadline:** **T+1** — the same close-of-next-business-day discipline as EMIR and SFTR ([Module 3](../1-capital-markets/module-03-trade-lifecycle.md) §9).

**Transparency publication deadline:** "as close to real-time as technically possible" for non-deferred trades — materially faster than T+1 — with the LIS deferral windows (Section 6) as the only permitted exception, and those windows are themselves set by ESMA/FCA technical standards (RTS 1) that get periodically recalibrated, exactly the kind of number [systems-landscape](../1-capital-markets/systems-landscape.md) §12's "confirm current figures before hard-coding them" caution applies to.

**Order record-keeping (MiFID II Article 25):** separately from both reporting obligations above, investment firms must retain records of orders and transactions (not just executed trades — unexecuted/cancelled orders too) for a minimum retention period (five years under the current standard), so an NCA can reconstruct trading activity retrospectively during an investigation, not just review what was proactively reported at the time.

> **Callout — verify current thresholds before building against them.** LIS bands, SMS calibrations, and the specific quantitative SI thresholds are all set (and periodically revised) by ESMA/FCA technical standards, not by MiFIR's own legal text. Treat every specific number in Sections 6-8 as illustrative, and confirm current figures against Section 10's official sources before encoding them into a rules engine — the same caution the [SFTR Reporting Deep Dive](./sftr-reporting.md) §7 gives for its own phase-in dates and field counts.

---

## 10. Regulatory Perspective — Official Sources &amp; Links

**Definition:** this module deliberately points to primary regulatory sources rather than restating them, since technical standards and thresholds get revised over time (Section 9's callout).

| Source | What It Covers | Link |
|---|---|---|
| **EUR-Lex — Directive 2014/65/EU (MiFID II)** | The full legal text of the Directive: authorization, conduct, market structure | [eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014L0065](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014L0065) |
| **EUR-Lex — Regulation (EU) 600/2014 (MiFIR)** | The full legal text of MiFIR: transparency, trading obligations, transaction reporting | [eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014R0600](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014R0600) |
| **ESMA** | RTS 22 (transaction reporting), RTS 1/RTS 2 (transparency), FIRDS, SI/LIS threshold calibrations, Q&amp;As | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **FCA — UK MiFIR** | UK's onshored regime, UK-specific technical standards, the COBS/MAR Handbook chapters | [www.fca.org.uk](https://www.fca.org.uk) |
| **ESMA FIRDS** | The consolidated instrument reference-data system underlying Section 3's eligibility test | [registers.esma.europa.eu/publication/searchRegister?core=esma_registers_firds](https://registers.esma.europa.eu/publication/searchRegister?core=esma_registers_firds) |

**Takeaway:** for a build task, treat the Directive/Regulation texts as the legal source of truth for scope and obligations, ESMA/FCA technical standards (RTS 22, RTS 1, RTS 2) as the source of truth for exact field lists, thresholds, and deferral windows, and FIRDS itself as the operational source of truth for "is this specific ISIN in scope today" — the same three-way split the [SFTR Reporting Deep Dive](./sftr-reporting.md) §9 draws for its own regime.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Executing investment firm throughout; registered Systematic Internaliser for SAP SE (Section 7b-8) |
| Maple Leaf Pension Fund | Buy-side client on the SAP SE trades (Sections 3, 5, 8) |
| Goldman Sachs | Bilateral OTC counterparty on Trade 1 (Section 3) |
| SAP SE | The venue-admitted equity used throughout as the MiFID-eligible worked example (Section 3 onward) |
| Deutsche Börse Xetra | The EU Regulated Market SAP SE is admitted to trading on — the fact that makes every SAP SE trade MiFID eligible (Section 3) |
| ESMA | Sets RTS 22/RTS 1/RTS 2 technical standards; operates FIRDS |
| FCA | UK's National Competent Authority; supervises UK MiFIR and Systematic Internalisers |
| APA (Approved Publication Arrangement) | Publishes OTC post-trade transparency data to the public market (Section 6-8) |
| ARM (Approved Reporting Mechanism) | Optional intermediary transmitting transaction reports to the NCA on a firm's behalf (Section 4) |

---

## Recap Quiz — Numbers Edition

1. Using Section 3, is Trade 2's bespoke bilateral OTC equity option on SAP SE shares MiFID eligible? Which specific test does it pass, and why does Test (a) fail for that same instrument?
2. Using Section 2, name one field that appears on a Transaction Report but never on a Transparency publication, and one that appears on a Transparency publication but never (in identifiable form) on the public side of a Transaction Report.
3. Using Section 7a, could Bank Of Toronto run an ongoing, regular bilateral OTC dealing business in SAP SE shares against client flow without ever registering as a Systematic Internaliser? Why or why not?
4. Using Section 6, if Bank Of Toronto executes a EUR 400,000 OTC trade in a liquidity band with an LIS threshold of EUR 650,000, is deferred publication available? What's the practical consequence for how quickly this trade becomes public?
5. Using Section 8's worked example, name all four obligations that trade triggers, and identify which one of the four is an ongoing conduct standard rather than a specific, dated filing.
6. Using Section 4, if Bank Of Toronto and Goldman Sachs are on opposite sides of the same MiFID-eligible trade, does a regulator ever pair and reconcile their two transaction reports against each other the way an EMIR Trade Repository does? Why does this matter for how complete each individual report needs to be?

Answer whenever ready — then return to **[Module 14](./module-14-regulatory-reporting.md): Regulatory Reporting**, or continue with the **[SFTR Reporting Deep Dive](./sftr-reporting.md)**.

---

## Answers — Recap Quiz, with Explanations

**1. Yes, eligible via Test (b) — its underlying (SAP SE shares) is admitted to trading on Deutsche Börse Xetra. Test (a) fails because the OPTION CONTRACT ITSELF is never admitted to, or traded on, any venue — it's a bespoke bilateral instrument that only exists between Bank Of Toronto and Maple Leaf Pension Fund.**

Section 3's Trade 2 states this distinction directly: Test (a) asks about the instrument being reported (the option), while Test (b) asks about that instrument's underlying (the shares) — the option inherits eligibility through the underlying's venue-admitted status even though the option contract has no venue presence of its own.

**2. Buyer/seller LEI (and the investment decision maker's identity) appears on the Transaction Report (Section 5) but never on a Transparency publication (Section 6), which strips counterparty identity entirely. Conversely, nothing on the Transparency side is unique to it that isn't also present on the Transaction Report — the Transaction Report is a strict superset of economic detail, just restricted to the regulator instead of published.**

Section 2's side-by-side diagram states this explicitly: the Transparency publication carries "price, volume, timestamp ONLY — no counterparty names at all," while the Transaction Report carries "full detail: both parties' LEIs, trader ID, algo flags, price, quantity, venue code."

**3. No — not without either routing that flow through a venue or registering as a Systematic Internaliser once its dealing volume crosses ESMA's threshold. The STO's ad-hoc/irregular/infrequent carve-out (Section 7a) only protects genuinely occasional, non-price-forming bilateral trades; a regular ongoing OTC dealing business against client flow is precisely the pattern the carve-out excludes, pushing the firm into the SI regime (Section 7b) instead.**

Section 7a's worked example states this directly in its "Contrast" paragraph: systematic, ongoing bilateral dealing in a liquid EU share is exactly the scenario the STO doesn't allow to remain informal OTC — it has to become either venue-routed or SI-quoted.

**4. Yes, deferred publication is available — EUR 400,000 is below the EUR 650,000 illustrative LIS threshold for that band is actually the WRONG direction to check: LIS deferral requires being large relative to the threshold, and 400,000 &lt; 650,000 means this trade does NOT qualify as Large In Scale. Practical consequence: this trade must be published as close to real-time as technically possible — no deferral — the opposite of Trade 1's EUR 1,900,000 (Section 6), which exceeded the threshold and did qualify.**

Section 6's worked example shows the comparison the right way round: 1,900,000 &gt; 650,000 qualified Trade 1 for deferral. A smaller EUR 400,000 trade sits under that same threshold, so it publishes promptly instead — this question is designed to catch reversing the inequality.

**5. All four of Section 7's obligations: (1) Transaction Reporting, (2) Post-Trade Transparency, (3) Best Execution, and (4) STO/SI compliance. Best Execution is the ongoing conduct standard — evidenced on request rather than filed on a specific date — while the other three are each triggered, dated obligations tied to this specific trade's execution.**

Section 8's worked example numbers each of the four explicitly, and its own framing of Best Execution as "an ongoing conduct obligation, evidenced on request, not a per-trade filing" is the direct textual answer to the second half of the question.

**6. No — MiFIR transaction reports are never paired or reconciled against each other the way an EMIR Trade Repository pairs two counterparties' reports on a shared UTI. This matters because each firm's report has to be self-sufficiently correct and complete on its own — there's no regulator-side cross-check that would catch a field-level discrepancy between Bank Of Toronto's and Goldman Sachs' independent reports the way Module 14 §3's EMIR pairing/reconciliation process does.**

Section 4's takeaway states this directly: "there's no reconciliation process to catch a field-level error the way Module 14 §3's EMIR pairing does" — a design consequence of MiFIR's single-sided-per-firm reporting model rather than EMIR's genuinely dual-sided, matched-on-UTI one.

---

[← Back to Index](../../index.md)
