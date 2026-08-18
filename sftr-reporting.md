# SFTR Reporting Deep Dive — What Is It, and Which Trades Must Be Reported

[← Back to Index](./index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> This is a **reference module**. [Module 5](./module-05-fixed-income.md) §8, [Module 14](./module-14-regulatory-reporting.md) §5, and [systems-landscape](./systems-landscape.md) §12 each touched SFTR briefly as part of a broader story; this module is the single place that goes deep on SFTR itself — its legal scope, exactly which trades trigger it, its lifecycle-event reporting model, and links to the actual regulatory sources.

---

## 1. What Is SFTR? — Purpose &amp; Legal Basis

**Definition:** SFTR is **Regulation (EU) 2015/2365 on Securities Financing Transactions**, a post-2008-crisis EU regulation requiring firms to report **Securities Financing Transactions (SFTs)** — repo, securities lending, and similar collateralized-financing trades — to an EU-authorized Trade Repository. The UK runs its own onshored equivalent, **UK SFTR**, administered by the FCA since Brexit.

**Why it exists:** the 2008 financial crisis (and, more specifically, the run on the "shadow banking" system in 2007–2009) showed regulators had almost no visibility into how much collateral was being financed, re-used, and re-hypothecated across the system. A bank could pledge the same security as collateral in one repo, receive it back, and re-pledge it again elsewhere — chains of reuse that amplified contagion when confidence broke, and that no single regulator could see end-to-end. The **Financial Stability Board (FSB)** identified securities financing markets as a specific shadow-banking risk in its post-crisis workstream, and SFTR is the EU's implementation of that FSB recommendation — the same root cause [Module 6](./module-06-fx.md) §8, [Module 8](./module-08-rates.md) §8, and [systems-landscape](./systems-landscape.md) §01 already flag for EMIR and MiFID II.

```text
SFTR'S PLACE AMONG THE REPORTING REGIMES
========================================================================

  MiFIR   ->  reports EXECUTED TRANSACTIONS (cash securities, some
              derivatives) — Module 3 §09

  EMIR    ->  reports OTC DERIVATIVES specifically — Module 2 §07

  SFTR    ->  reports SECURITIES FINANCING TRANSACTIONS specifically —
              repo, reverse repo, buy-sell back, securities/commodities
              lending &amp; borrowing, margin lending — THIS MODULE

  All three often flow to the SAME Trade Repository (REGIS-TR, DTCC,
  UnaVista all accept EMIR AND SFTR reports) — but each is a legally
  distinct obligation, with its own schema, scope, and deadline.
```

**Takeaway:** SFTR isn't "EMIR for repo" bolted on as an afterthought — it's a legally separate regulation, answering a different systemic question ("how much collateral re-use and financing leverage exists?") than EMIR's ("how much derivatives risk exists?"), even though [Module 14](./module-14-regulatory-reporting.md) §5 already showed the two report *shapes* converge on a similar counterparty/economic-terms/collateral pattern.

---

## 2. The Five SFT Types — What Actually Falls In Scope, With Worked Numbers

**Definition:** SFTR defines exactly **five transaction types** as "Securities Financing Transactions." If a trade isn't one of these five, SFTR does not apply to it, no matter how similar it looks economically.

| SFT Type | What It Is | Example |
|---|---|---|
| **Repurchase transaction** (repo / reverse repo) | Sell a security now, agree to buy it back later at a fixed price — economically a collateralized cash loan | Section 2a below |
| **Buy-sell back / sell-buy back** | Same economic effect as repo, but structured as two separate spot/forward trades rather than one repo contract | Section 2b below |
| **Securities or commodities lending / borrowing** | Lend a security (or commodity) against collateral (cash or non-cash) for a fee | Section 2c below |
| **Margin lending transaction** | A broker extends credit to a client to buy/hold securities, collateralized by the securities themselves | Section 2d below |
| *(Not an SFT)* | Anything else that merely **uses** securities as collateral without one of the four structures above — e.g. an IRS backed by a CSA (that's EMIR + [Module 12](./module-12-collateral-margin.md) collateral, not SFTR) | [Module 5](./module-05-fixed-income.md) §3's exact distinction: a repo is reported under SFTR *because* it's a financing contract, not a derivative |

**Takeaway:** the test is structural, not economic. A CSA-collateralized swap and a repo can both involve the same German Bund moving between two parties, but only the repo is an SFT — the swap's collateral movement is governed by [Module 12](./module-12-collateral-margin.md)'s CSA/margin mechanics and reported (if at all) under EMIR, never SFTR.

> **Term note — "REPO" and "SLEB."** These aren't just trading-desk slang — they're the literal enumerated values SFTR's technical standards use in the **"Type of SFT"** field of every Loan Data report:

| Product (Section 2's table) | SFTR "Type of SFT" Code |
|---|---|
| Repurchase transaction | **REPO** |
| Buy-sell back / sell-buy back | **SBSC** |
| Securities or commodities lending/borrowing | **SLEB** |
| Margin lending transaction | **MGLD** |

> So when a colleague says "that's a SLEB trade," they mean it's reportable under SFTR with `Type of SFT = SLEB` — the exact same underlying transaction the desk calls "securities lending" or "stock loan." Confirm the current code list against ESMA's/FCA's validation rules (Section 9) before hard-coding it, since technical standards get revised (Section 7's callout).

### 2a. Worked example — Repo &amp; Reverse Repo (the same trade, two labels)

**The key idea:** "repo" and "reverse repo" are not two different products — they're the *same transaction*, described from each counterparty's own side. Both sides report the same UTI under SFTR; only which party is the collateral-giver/cash-borrower vs. collateral-taker/cash-lender differs.

```text
REPO / REVERSE REPO — BANK OF TORONTO <-> GOLDMAN SACHS
========================================================================

  Instrument: UK Gilt 4% 2032, GBP 20,000,000 face value
  Collateral market value (dirty, incl. accrued): GBP 20,250,000
  Haircut: 2%
  Term: 7 days     Repo rate: 5.10% (GBP), actual/365

  Cash lent  = Market Value x (1 - Haircut)
             = 20,250,000 x 0.98 = GBP 19,845,000

  Interest   = 19,845,000 x 5.10% x (7/365) = GBP 19,410

  Repurchase price (Day 7) = 19,845,000 + 19,410 = GBP 19,864,410

  BANK OF TORONTO'S SIDE: sells the Gilt, receives GBP 19,845,000
  cash, agrees to repurchase -- this is a REPO for Bank Of Toronto
  (it is BORROWING cash, POSTING the Gilt as collateral)

  GOLDMAN SACHS' SIDE: buys the Gilt, pays GBP 19,845,000 cash,
  agrees to resell -- this is a REVERSE REPO for Goldman Sachs
  (it is LENDING cash, RECEIVING the Gilt as collateral)

  -> ONE trade, ONE UTI, TWO SFTR reports (Section 4's dual-sided
     obligation) -- each counterparty's own report simply reflects
     which side of the cash/collateral exchange it sat on
```

**Why it gets reported:** a repo is exactly the kind of short-term, collateralized funding transaction the FSB's shadow-banking workstream (Section 1) singled out — regulators need to see the volume and rate of this financing activity system-wide, because a sudden refusal to roll over short-term repo funding is precisely the mechanic that froze bank balance sheets in 2008.

### 2b. Worked example — Buy-sell back / sell-buy back

**The key idea:** economically identical to a repo, but structured as **two separate trades** (a spot sale + a forward repurchase) rather than one repo contract with an explicit rate — the financing cost is embedded in the difference between the spot and forward prices instead of a stated repo rate.

```text
BUY-SELL BACK — BANK OF TORONTO SELLS, MIZUHO BANK BUYS
========================================================================

  Instrument: JGB 10Y 0.4% 2036, JPY 1,000,000,000 face value
  Spot leg:     sell today at clean price 99.50
  Forward leg:  repurchase in 30 days at clean price 99.53

  Spot proceeds       = 1,000,000,000 x 99.50% = JPY 995,000,000
  Forward repurchase  = 1,000,000,000 x 99.53% = JPY 995,300,000
  Implied financing cost (30 days) = JPY 300,000

  Implied annualized repo rate
    = (300,000 / 995,000,000) x (365/30) ~ 0.37%

  -> Two trade tickets (spot sale, forward purchase), but SFTR looks
     through the two-leg STRUCTURE to the underlying ECONOMIC
     substance and reports it as ONE SFT, Type of SFT = SBSC --
     the same reporting treatment a repo gets, just a different code
```

**Why it gets reported:** SFTR is deliberately structure-agnostic within its five types — if buy-sell backs were left unreported just because they use two trade tickets instead of one repo contract, firms could economically replicate repo financing while evading the same transparency obligation the regulation exists to create (Section 1).

### 2c. Worked example — Securities Lending / Borrowing (SLEB)

**The key idea:** one party needs to *borrow* a specific security (commonly to cover a short sale or a settlement fail), the other *lends* it against collateral, for a fee — no cash changes hands as the "loan" itself, only as collateral.

```text
SECURITIES LENDING / BORROWING (SLEB) — MAPLE LEAF PENSION FUND LENDS,
BANK OF TORONTO BORROWS (VIA AN AGENT LENDER)
========================================================================

  Security on loan: 100,000 shares of Apple Inc (AAPL) @ USD 225.00
  Market value of loan = 100,000 x 225.00 = USD 22,500,000

  Collateralization requirement: 102%
  Required collateral value = 22,500,000 x 1.02 = USD 22,950,000
  Collateral type: cash, USD

  Lending fee: 30 bps (0.30%) annualized on loan value
  Term: 30 days, actual/360

  Fee = 22,500,000 x 0.30% x (30/360) = USD 5,625

  -> Bank Of Toronto BORROWS the shares (to cover a client short
     sale) and POSTS USD 22,950,000 cash collateral; Maple Leaf
     Pension Fund LENDS the shares and RECEIVES the collateral,
     earning the USD 5,625 fee for the 30-day term
  -> Reported by both sides, Type of SFT = SLEB
```

**Why it gets reported:** securities lending is how short-selling and settlement-fail coverage actually get funded in practice — regulators need visibility into how much of the free float of a given security is out on loan (and against what collateral) to understand short-selling capacity and the knock-on liquidity risk if lenders recalled their shares en masse.

### 2d. Worked example — Margin lending

**The key idea:** a broker (often a prime broker) extends credit secured by the client's own securities portfolio — distinct from repo/SLEB because the *loan* itself is cash-for-securities-portfolio credit, not a security-for-security or security-for-cash financing trade.

```text
MARGIN LENDING — BANK OF TORONTO PRIME SERVICES LENDS TO
NORTHWIND CAPITAL (HEDGE FUND CLIENT)
========================================================================

  Client buys USD 10,000,000 of listed equities
  Client's own cash (initial margin, 50%): USD 5,000,000
  Margin loan from Bank Of Toronto:        USD 5,000,000

  Collateral: the full USD 10,000,000 equity portfolio
  Loan rate: SOFR + 150bps (illustrative SOFR 5.30% -> 6.80% all-in)

  Initial Loan-to-Value (LTV) = 5,000,000 / 10,000,000 = 50%

  -> Reported by Bank Of Toronto, Type of SFT = MGLD -- see
     Section 6's worked margin-call example for what happens when
     the portfolio's value moves against this loan
```

**Why it gets reported:** margin lending is leveraged credit secured by securities that can itself move in value — exactly the kind of leverage-plus-collateral-quality combination that amplifies losses in a falling market (a "margin spiral"), which is why SFTR tracks it alongside repo and securities lending rather than treating it as ordinary bank lending.

---

## 3. Which Trades Must Be Reported — The Scope Test

**Definition:** a trade is SFTR-reportable only if it passes **both** of the following tests. Get either test wrong and a reporting system will either under-report (missed obligations, regulatory fines) or over-report (noise, wasted reconciliation effort).

**Test 1 — Product scope:** is the trade one of the five SFT types from Section 2?

**Test 2 — Counterparty/branch scope:** does at least one side of the trade have an EU (or UK, under UK SFTR) nexus? SFTR's territorial scope catches:

| In Scope | Out of Scope |
|---|---|
| Any counterparty **established in the EU** (bank, investment firm, insurer, UCITS/AIF, pension fund, non-financial counterparty — an "FC" or "NFC" in EMIR's own terminology, reused by SFTR) | Two counterparties, both established outside the EU/UK, trading entirely outside the EU/UK, with no EU/UK branch involved |
| An **EU branch of a non-EU firm**, for SFTs entered into by that branch | A non-EU branch of a non-EU firm |
| A **non-EU branch of an EU firm**, for SFTs entered into by that branch (the EU parent's obligation follows its branches globally) | — |
| **Collateral reuse** by an EU counterparty (or an EU branch), reported separately regardless of where the reused security itself sits | — |

**Exemptions:** central banks, the ESCB (European System of Central Banks), the Bank for International Settlements (BIS), and certain public-sector debt-management bodies are exempt from SFTR reporting, mirroring EMIR's own central-bank carve-out ([Module 2](./module-02-hierarchy.md) §7).

```text
WORKED SCOPE TEST — THREE TRADES, THREE ANSWERS
========================================================================

  1) Bank Of Toronto (EU-branch-booked) repos a German Bund with
     Goldman Sachs (US entity, no EU branch involved in this trade)
     -> Test 1: YES, it's a repo (Section 02)
     -> Test 2: YES, Bank Of Toronto's EU branch is a party
     -> REPORTABLE under SFTR (Bank Of Toronto's side); Goldman
        Sachs' side is reportable too if a Goldman Sachs EU/UK
        entity or branch was the actual booking entity

  2) Bank Of Toronto's Toronto desk (no EU/UK branch anywhere in the
     chain) repos a Canadian bond with a Canadian pension fund
     -> Test 1: YES, it's a repo
     -> Test 2: NO EU/UK nexus at all
     -> NOT reportable under SFTR (Canada has no SFTR-equivalent as
        of 2026 — systems-landscape §12)

  3) Bank Of Toronto's 5Y CAD IRS with Goldman Sachs, collateralized
     under a CSA (Module 12)
     -> Test 1: NO -- an IRS is a derivative, not one of the five
        SFT types, regardless of EU nexus
     -> NOT reportable under SFTR (it's an EMIR trade instead --
        Module 14 §1's decision-matrix framing)
```

**Takeaway:** "does this trade need an SFTR report?" always requires checking *both* tests independently — a repo with no EU/UK nexus is out of scope on Test 2 even though it passes Test 1, and a CSA-collateralized swap fails Test 1 regardless of how EU-heavy the counterparties are. This is exactly the kind of two-part rule a reporting rules engine has to encode as two separate boolean checks, not one combined heuristic.

---

## 4. Dual-Sided Reporting &amp; Who Reports

**Definition:** like EMIR ([Module 13](./module-13-parties-leis.md) §6), SFTR is a **dual-sided** reporting obligation — both counterparties to an in-scope SFT must independently submit their own report, each carrying the same **UTI (Unique Transaction Identifier)** so the Trade Repository can pair and reconcile the two submissions ([Module 14](./module-14-regulatory-reporting.md) §3's pairing mechanism, reused unchanged by SFTR).

**Delegated reporting:** SFTR allows one counterparty to report on behalf of the other (a common arrangement between a large bank and a smaller buy-side client), and — mirroring EMIR's own small-counterparty relief — a **Financial Counterparty (FC)** trading with a **small Non-Financial Counterparty (NFC)** below defined size thresholds becomes legally responsible for reporting *both* sides, so the smaller counterparty doesn't need to build its own reporting pipeline at all.

| Reporting Party | Responsibility |
|---|---|
| Both counterparties (default) | Each submits its own report, independently, tagged with the shared UTI |
| Larger FC vs. small NFC | The FC reports for both sides (delegated by law, not just by choice) |
| CCP-cleared SFTs | The CCP itself may take on reporting responsibility for the cleared leg |
| Agent lender (securities lending) | Often reports on behalf of the underlying lending clients under a delegation arrangement |

**Takeaway:** dual-sided reporting is what makes the Trade Repository's pairing/reconciliation control possible ([Module 14](./module-14-regulatory-reporting.md) §3) — but the *delegation* options mean the entity legally on the hook for getting the report right is not always the entity that actually built the trade capture system, a genuine operational-risk point for any firm relying on a counterparty's delegated reporting.

---

## 5. Lifecycle Events — Not Just Trade Inception

**Definition:** this is SFTR's most IT-relevant difference from EMIR. EMIR largely reports trade inception plus periodic valuation updates. **SFTR reports discrete *action types* across the full life of an SFT** — every one of the events below generates its own report, referencing the same UTI.

| Action Type | Triggered By | Example |
|---|---|---|
| **New** | Trade execution | Bank Of Toronto's overnight repo starts |
| **Modification** | Any change to previously reported terms | Rate renegotiated on an open repo |
| **Valuation update** | Daily mark-to-market of the SFT and its collateral | Collateral market value moves overnight |
| **Collateral update** | Collateral posted, substituted, or returned | Bund substituted for a different bond as collateral |
| **Margin update** | Initial/variation margin posted for margin lending SFTs | Prime brokerage margin call |
| **Reuse (rehypothecation)** | Collateral received in one SFT is re-used in another transaction | Goldman Sachs re-pledges the Bund it received from Bank Of Toronto |
| **Termination / early termination** | SFT matures or is unwound early | Overnight repo settles back the next business day |
| **Error / correction** | A previously submitted report contained a mistake | UTI mismatch correction ([Module 14](./module-14-regulatory-reporting.md) §3's pairing-break scenario) |

**Takeaway:** an EMIR-style reporting pipeline built to report "trade booked, then periodic valuation" will structurally under-report an SFT — SFTR expects an event-driven reporting model tracking every lifecycle state change ([Module 3](./module-03-trade-lifecycle.md)'s lifecycle stages, but reported individually rather than summarized at the end), which is exactly why [systems-landscape](./systems-landscape.md) §12 flags "do not simply extend an existing EMIR pipeline" as the practical warning for anyone building this.

---

## 6. Data Fields, UTI &amp; Report Types

**Definition:** SFTR's technical standards define roughly **155 reportable data fields**, split across **four report tables**, each corresponding to a different economic dimension of the SFT:

| Report Table | Contains |
|---|---|
| **Loan data** | Counterparty identifiers (LEIs), UTI, trade/value/maturity dates, principal amount, rate/spread, SFT type |
| **Collateral data** | Collateral instrument (ISIN), collateral quantity/value, haircut ([Module 12](./module-12-collateral-margin.md) §3's exact mechanic), collateral type (cash vs. non-cash) |
| **Margin data** | Initial margin, variation margin, currency, for margin lending / CCP-cleared SFTs |
| **Reuse data** | Reused collateral value, reinvestment of cash collateral, aggregated across a reporting counterparty's whole book (not trade-by-trade) |

Every report carries a **UTI**, generated by the same waterfall logic EMIR uses ([Module 2](./module-02-hierarchy.md) §12) — whichever counterparty is designated as the UTI-generator produces it at execution, and both sides' reports must carry the identical value for the Trade Repository to pair them.

**Takeaway:** the loan/collateral/margin/reuse four-way split mirrors [Module 14](./module-14-regulatory-reporting.md) §3's EMIR field groups (counterparty/common/valuation/collateral data) closely enough that a reporting engine built for one regime can reuse much of its data model for the other — but reuse data has no EMIR equivalent at all, since rehypothecation reporting is an SFTR-specific concept (Section 1's core rationale for the regulation existing).

### 6a. Collateral data, worked — the haircut mechanic

Reusing Section 2a's repo: Bank Of Toronto posts the Gilt, Goldman Sachs lends cash.

```text
COLLATERAL DATA REPORT — DAY 1 (NEW)
========================================================================
  Collateral instrument: UK Gilt 4% 2032
  Collateral quantity (face value): GBP 20,000,000
  Collateral market value (dirty):  GBP 20,250,000
  Haircut:                          2%
  Collateral value post-haircut:    20,250,000 x 0.98 = GBP 19,845,000
  ^ this equals the cash lent BY CONSTRUCTION -- the haircut is
    sized so the discounted collateral value matches the cash loan
```

**Why it gets reported:** the haircut is the market's own real-time read of how much price risk a lender is willing to absorb on a given collateral type — regulators tracking haircuts across the system can spot exactly the kind of system-wide haircut tightening ("collateral crunch") that turned an initial 2007 subprime shock into a broader funding freeze.

### 6b. Valuation data, worked — the daily mark-to-market

Same repo, now on Day 2 of its 7-day term: the Gilt's price falls 0.5% overnight.

```text
VALUATION UPDATE REPORT — DAY 2
========================================================================
  Collateral market value, Day 1: GBP 20,250,000
  Price move:                     -0.5%
  Collateral market value, Day 2: 20,250,000 x 0.995 = GBP 20,148,750
                                   (down GBP 101,250)

  Loan value (principal + 1 day's accrued interest):
    19,845,000 + (19,845,000 x 5.10% x 1/365)
    = 19,845,000 + 2,773 = GBP 19,847,773
```

**Why it gets reported:** daily valuation is what lets a regulator (or the counterparties themselves) see whether collateral coverage is still adequate as prices move — without it, a repo's reported terms would freeze at Day 1's numbers even as the real economic exposure drifted, exactly the kind of stale-data risk [Module 10](./module-10-valuation.md) §1 flags for any Mark-to-Market process.

### 6c. Margin data, worked — when the collateral call actually triggers

**Repo margin call.** Continuing 6b: the required collateral value (set on Day 1) stays GBP 20,250,000 (cash lent ÷ (1 − haircut) = 19,845,000 / 0.98). Day 2's actual collateral value has fallen to GBP 20,148,750.

```text
MARGIN CALL — REPO COLLATERAL SHORTFALL
========================================================================
  Required collateral value: GBP 20,250,000
  Actual collateral value:   GBP 20,148,750
  Shortfall:                 GBP 101,250

  -> Goldman Sachs (cash lender) calls Bank Of Toronto for GBP
     101,250 of additional collateral (cash or more Gilts) to
     restore the required ratio -- reported as a Collateral update
     (Section 5's action types), same UTI
```

**Margin lending call.** Section 2d's Northwind Capital loan: portfolio value falls from USD 10,000,000 to USD 7,500,000 (a sharp drawdown), loan amount unchanged at USD 5,000,000.

```text
MARGIN CALL — MARGIN LENDING LTV BREACH
========================================================================
  Loan amount (fixed):        USD 5,000,000
  Portfolio value, Day 1:     USD 10,000,000  (LTV 50%)
  Portfolio value, after drop: USD 7,500,000  (LTV = 5.0M/7.5M = 66.7%)
  Maintenance LTV threshold:   65%

  66.7% > 65% -> MARGIN CALL TRIGGERED

  Collateral needed to restore LTV to 50% = Loan / 50% = 10,000,000
  Additional collateral required = 10,000,000 - 7,500,000
                                  = USD 2,500,000
```

**Why it gets reported:** margin/collateral calls are the exact mechanism by which falling asset prices force firms to raise cash or sell assets on short notice — system-wide visibility into how often and how large these calls are running lets regulators see a "margin spiral" building before it forces synchronized selling across many firms at once.

### 6d. Reuse data, worked — the rehypothecation chain

Continuing Section 2a's repo: Goldman Sachs has received GBP 20,250,000 of Gilt collateral from Bank Of Toronto. It then uses part of that same collateral in its own, separate financing trade.

```text
REUSE — GOLDMAN SACHS RE-PLEDGES BANK OF TORONTO'S COLLATERAL
========================================================================
  Collateral received from Bank Of Toronto: GBP 20,250,000 (Gilts)

  Goldman Sachs enters its OWN repo with Deutsche Bank, posting
  GBP 15,000,000 face value of THOSE SAME Gilts as its collateral
  to borrow additional cash

  Reuse rate (for this collateral type)
    = value reused / value received
    = 15,000,000 / 20,250,000 ~ 74.1%

  -> Goldman Sachs reports this as REUSE DATA -- an AGGREGATE figure
     across its whole book for this collateral type/currency
     (Section 6's table), NOT tied line-by-line to Bank Of Toronto's
     original repo UTI the way a Modification report would be
```

**Why it gets reported:** this is the precise mechanic Section 1's "chains of reuse... no single regulator could see end-to-end" describes — the same GBP 20,250,000 of Gilts is now backing financing for *two* separate cash loans (Bank Of Toronto's original repo and Goldman Sachs' repo with Deutsche Bank) simultaneously, a form of collateral-driven leverage that's invisible unless reuse is reported on its own.

---

## 7. Reporting Deadline, Phase-In &amp; Backloading

**Definition:** SFTR runs the same **T+1** discipline as EMIR/MiFIR ([Module 3](./module-03-trade-lifecycle.md) §9) — a reportable event must be submitted to a Trade Repository by the end of the next business day.

**Phase-in (EU, 2020–2021):** SFTR go-live was staged by counterparty type rather than applying to everyone on a single date — banks and investment firms went live first (April 2020), followed by CCPs and CSDs (July 2020), then insurers, UCITS/AIFs and pension funds (October 2020), and finally non-financial counterparties (January 2021). The UK carried this same staged model forward into UK SFTR after Brexit.

**Backloading:** SFTs that were still outstanding (not yet matured) on a counterparty's own go-live date had to be **backloaded** — reported retroactively — within a defined window after go-live, so the Trade Repository would hold a complete picture of live exposure from day one, not just newly-executed trades.

> **Callout — verify current dates before building against them.** Exact thresholds (e.g. the small-NFC size test in Section 4), phase-in dates, and field counts are set by ESMA/FCA **technical standards (RTS/ITS)**, which get periodically revised (an SFTR "refit," analogous to [Module 14](./module-14-regulatory-reporting.md) §8's EMIR REFIT). Treat the numbers in this module as the conceptual shape, and confirm current figures against Section 9's official links before encoding them into a rules engine.

**Takeaway:** the phased go-live means a real Trade Repository's SFTR data didn't reach full market coverage instantly in 2020 — a firm reconciling its own book against aggregate SFTR statistics from that period needs to account for which counterparty types were even required to be reporting yet.

---

## 8. Worked Example — Bank Of Toronto's Repo, Full Lifecycle

**Definition:** extending [systems-landscape](./systems-landscape.md) §12's single-report worked example to show what a *complete* SFTR lifecycle actually generates, per Section 5's action types.

```text
BANK OF TORONTO'S REPO WITH GOLDMAN SACHS — FULL SFTR REPORT TRAIL
========================================================================

  Trade: Bank Of Toronto lends EUR 50,000,000 cash overnight to
         Goldman Sachs, collateralized by Germany 3% Bund 2033
  Repo Rate: 3.15%   Trade Date: Mon 17-Aug-2026
  Overnight interest = 50,000,000 x 3.15% x (1/365) = EUR 4,315

  Report 1 — NEW (Mon 17-Aug, by Tue 18-Aug T+1)
    Both counterparties submit Loan data + Collateral data,
    same UTI, to REGIS-TR (Section 4's dual-sided obligation)

  Report 2 — VALUATION UPDATE (daily, for as long as the repo is
    open -- for an overnight repo this may be the same day it
    terminates)
    Collateral mark-to-market vs. the agreed haircut (Module 12 §3)

  Report 3 — REUSE (if triggered)
    Goldman Sachs re-pledges the received Bund into a DIFFERENT
    repo with a third counterparty before returning it -- its own
    separate report, aggregated collateral-reuse data (Section 6),
    NOT linked to the original UTI the same way a Modification is

  Report 4 — TERMINATION (Tue 18-Aug, by Wed 19-Aug T+1)
    SFT matures, cash + interest returned, Bund returned; both
    counterparties submit a Termination report closing out the UTI
```

**Takeaway:** even a plain overnight repo — the simplest possible SFT — generates at minimum a New and a Termination report, and potentially a Valuation Update and a Reuse report depending on what Goldman Sachs does with the collateral before returning it. A month-long term repo with several collateral substitutions would generate many more discrete reports across that period, all tied together by one UTI (except the Reuse report, which stands alone).

---

## 9. Regulatory Perspective — Official Sources &amp; Links

**Definition:** this module deliberately points to primary regulatory sources rather than restating them, since technical standards and thresholds get revised over time (Section 7's callout). If a specific deep link below has moved, search the named site directly — the domain itself is the stable, authoritative reference.

| Source | What It Covers | Link |
|---|---|---|
| **EUR-Lex — Regulation (EU) 2015/2365** | The full legal text of SFTR itself | [eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32015R2365](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32015R2365) |
| **ESMA** | EU-level technical standards (RTS/ITS), Q&amp;As, validation rules, guidance for SFTR | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **FCA — UK SFTR** | UK's onshored regime, UK-specific technical standards and guidance | [www.fca.org.uk](https://www.fca.org.uk) |
| **DTCC** | One of the authorized Trade Repositories accepting SFTR (and EMIR) reports | [www.dtcc.com](https://www.dtcc.com) |
| **LSEG UnaVista** | Another authorized Trade Repository for SFTR/EMIR/MiFIR reporting | [www.lseg.com](https://www.lseg.com) |
| **REGIS-TR** | The Trade Repository used in this module's worked examples | [www.regis-tr.com](https://www.regis-tr.com) |
| **FSB (Financial Stability Board)** | The global post-crisis shadow-banking workstream SFTR implements | [www.fsb.org](https://www.fsb.org) |

**Takeaway:** for a build task, treat EUR-Lex (or the FCA equivalent for UK SFTR) as the legal source of truth for scope and obligations, ESMA/FCA technical standards as the source of truth for exact field lists and validation rules, and the Trade Repository's own published schema/reporting manual as the source of truth for the literal message format your system needs to produce — three different documents answering three different "what exactly do I build" questions.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Repo seller / cash borrower (Section 2a, 6a-c); margin lender to Northwind Capital (Section 2d, 6c); cash lender in the systems-landscape Bund repo (Section 8) |
| Goldman Sachs | Reverse repo buyer / cash lender (Section 2a); receives and re-uses (rehypothecates) Gilt/Bund collateral (Section 6d, Section 8) |
| Mizuho Bank | Counterparty on the JGB buy-sell back (Section 2b) |
| Maple Leaf Pension Fund | Securities lender in the SLEB example, via an agent lender (Section 2c) |
| Northwind Capital | Hedge fund client borrowing on margin from Bank Of Toronto Prime Services (Section 2d, 6c) |
| Deutsche Bank | Third-party counterparty receiving Goldman Sachs' re-pledged Gilt collateral (Section 6d) |
| REGIS-TR | Trade Repository receiving both counterparties' reports, pairing on UTI |
| ESMA / FCA | Technical-standard setters for EU SFTR and UK SFTR respectively |
| FSB | Originating global standard-setter whose shadow-banking workstream SFTR implements |

---

## Recap Quiz

1. Using Section 2, is a CSA-collateralized interest rate swap ever reportable under SFTR? Why or why not, independent of where either counterparty is based?
2. Using Section 3's two-test framework, construct a trade that passes Test 1 (product scope) but fails Test 2 (counterparty/branch scope). Is it SFTR-reportable?
3. Using Section 4, if Bank Of Toronto (a large FC) trades with a small NFC client below the defined size thresholds, who is legally responsible for submitting the NFC's side of the SFTR report?
4. Using Section 5, name two action types an EMIR-style "inception + periodic valuation" reporting pipeline would likely miss if naively reused for SFTR.
5. Using Section 6, which of the four SFTR report tables has no direct EMIR equivalent, and why — tie your answer back to Section 1's stated purpose of the regulation.
6. Using Section 8's worked example, if Goldman Sachs never re-uses the Bund it receives as collateral, which report from the four-report trail simply never gets generated?
7. Using Section 9, if you needed the exact current list of SFTR reportable fields to build a reporting engine, which type of source should you consult — the EUR-Lex regulation text, or ESMA's technical standards — and why?
8. Using Section 2a, is a "repo" and a "reverse repo" ever two economically different trades, or are they always the same trade seen from opposite sides? What SFTR "Type of SFT" code do both sides of the Bank Of Toronto / Goldman Sachs Gilt repo actually report?
9. Using Section 2c, what does the SFTR code `SLEB` stand for, and what specific transaction from this module would be reported with that code?
10. Using Section 6c's margin lending example, if Northwind Capital's portfolio instead fell to USD 8,500,000 (not 7,500,000), would a margin call trigger under the stated 65% maintenance LTV threshold? Show the LTV calculation.
11. Using Section 6d, why is Goldman Sachs' reuse of Bank Of Toronto's Gilt collateral reported as an aggregate figure rather than being linked directly to Bank Of Toronto's original repo UTI?

---

## Answers — Recap Quiz, with Explanations

**1. No — an IRS is a derivative, not one of the five SFT types (repo, buy-sell back, securities/commodities lending, margin lending), so it fails Test 1 regardless of counterparty location or how it's collateralized.**

Section 2 states this test is structural, not economic: a CSA-collateralized swap and a repo can move the identical security as collateral, but only the repo is one of the five defined SFT structures. The swap's collateral is governed by [Module 12](./module-12-collateral-margin.md)'s CSA mechanics and reported (if at all) under EMIR, never SFTR — restated directly from [Module 5](./module-05-fixed-income.md) §8.

**2. Example: a repo between two US entities with no EU or UK branch anywhere in the chain (Section 3's Worked Scope Test #2). It passes Test 1 (it's a repo) but fails Test 2 (no EU/UK nexus at all) — NOT reportable under SFTR.**

Section 3's framing is explicit that both tests must be checked independently; a product-in-scope trade with zero territorial nexus is exactly the case that trips up a rules engine checking only product type.

**3. The FC (Bank Of Toronto) is legally responsible for reporting BOTH sides — this delegated-reporting relief mirrors EMIR's own small-counterparty accommodation, so the smaller NFC doesn't need its own reporting pipeline.**

Section 4's table states this directly under "Larger FC vs. small NFC" — the responsibility is assigned by law, not by voluntary agreement between the two firms.

**4. Reuse (rehypothecation) reporting and Collateral update reporting — neither has a natural equivalent in an EMIR-style pipeline built around trade inception plus periodic MTM updates, since EMIR has no rehypothecation-reporting concept at all (Section 1).**

Section 5's takeaway states this directly, and [systems-landscape](./systems-landscape.md) §12 independently flags the same warning: "do not simply extend an existing EMIR pipeline" when building SFTR reporting.

**5. Reuse data has no EMIR equivalent — because rehypothecation/collateral-reuse transparency is the specific systemic-risk gap SFTR was created to close (Section 1's FSB shadow-banking rationale), a concern EMIR's OTC-derivatives-risk mandate never needed to cover.**

Section 6's takeaway draws this connection explicitly: Loan/Collateral/Margin data all mirror EMIR's own field groups closely, but Reuse data is the one genuinely new category, tracing directly back to Section 1's "chains of reuse... no single regulator could see end-to-end" root cause.

**6. Report 3 (REUSE) — the New, Valuation Update, and Termination reports would still all be generated regardless of what Goldman Sachs does with the collateral, since those track the SFT itself, not what happens to the collateral afterward.**

Section 8's worked example marks Report 3 explicitly as "(if triggered)" — it's the one report in the four-report trail that depends on a counterparty's downstream behavior with the collateral, not on the SFT's own lifecycle events.

**7. ESMA's (or the FCA's, for UK SFTR) technical standards (RTS/ITS) — the EUR-Lex regulation text establishes the legal scope and obligations, but the literal field-by-field reportable data list lives in the technical standards, which are revised independently of (and more frequently than) the underlying regulation itself.**

Section 9's takeaway states this three-way split directly: the regulation is the source of truth for scope/obligations, the technical standards are the source of truth for exact field lists and validation rules, and the Trade Repository's own reporting manual is the source of truth for literal message format — three different documents for three different build questions.

**8. Always the same trade seen from opposite sides — "repo" and "reverse repo" are not different products. Both Bank Of Toronto and Goldman Sachs report `Type of SFT = REPO` for the identical UTI; only the collateral-giver/cash-borrower vs. collateral-taker/cash-lender roles differ between their two reports.**

Section 2a states this directly: the GBP 19,845,000 cash, GBP 20,250,000 collateral, and 5.10% rate are identical numbers on both sides — the label "repo" vs. "reverse repo" just describes which end of the same transaction a given counterparty sat on.

**9. `SLEB` stands for Securities Lending and Borrowing (also colloquially "sec lending" or "stock loan"). Section 2c's Maple Leaf Pension Fund → Bank Of Toronto AAPL share loan (USD 22,500,000 market value, USD 22,950,000 collateral, USD 5,625 fee) is reported with `Type of SFT = SLEB`.**

The term-note table right after Section 2's main table lists `SLEB` as the literal SFTR "Type of SFT" enumeration value for this product family — the same code appears on both Bank Of Toronto's (borrower) and Maple Leaf Pension Fund's (lender) reports.

**10. Yes — LTV = 5,000,000 / 8,500,000 = 58.8%, which is BELOW the 65% maintenance threshold, so NO margin call would trigger.**

Section 6c's worked formula (Loan / Portfolio Value = LTV) applies directly: 58.8% sits under the 65% threshold, unlike the actual worked example's 66.7% (from an USD 7,500,000 portfolio), which does breach it. This shows the threshold is a hard cutoff, not a gradual scale — a portfolio drop that stays under it triggers nothing at all under Section 6c's stated rule.

**11. Because reuse is reported as an aggregate figure across the reusing counterparty's WHOLE book for that collateral type/currency — not trade-by-trade — since the specific units of collateral being re-pledged aren't segregated by which original SFT they came from once they're back in Goldman Sachs' general collateral pool.**

Section 6's report-table definition already states reuse data is "aggregated across a reporting counterparty's whole book (not trade-by-trade)," and Section 6d's takeaway explains why that matters: the whole point is visibility into how much of a *collateral type* is circulating through reuse chains system-wide, not a precise link back to one specific origin trade's UTI.

---

[← Back to Index](./index.md)
