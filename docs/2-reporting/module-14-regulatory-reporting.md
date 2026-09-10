# Module 14 — Regulatory Reporting + IT View

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Cross-cutting module — the roadmap's final one.** Closes every thread from Modules 1–13 and 15 into a single annotated trade record, setting up the curriculum's own "Final End-to-End Case Study" that follows this module.

---

## 1. What Is Regulatory Reporting? — Five Obligations, Not One

**Definition:** "regulatory reporting" gets used as one catch-all phrase, but this roadmap has actually touched **five distinct reporting obligations**, each answering a different regulatory question, going to different destinations, and triggered by different events.

| Obligation | What Gets Reported | Answers | First Seen |
|---|---|---|---|
| **Trade Reporting** (generic term) | Loosely used for any of the below — not a precise regulatory category itself | — | Throughout |
| **Transaction Reporting** (MiFIR) | EVERY execution in scope — equities, bonds, listed derivatives, OTC derivatives alike | "Did market abuse happen?" — surveillance-focused | [Module 4](../1-capital-markets/module-04-equity.md) §8 |
| **Position Reporting** | STANDING positions, periodically, not individual trades | "Is any one participant concentrating too much risk in one product?" | [Module 7](../1-capital-markets/module-07-commodities.md) §8's position limits |
| **Derivatives Reporting** (EMIR/Dodd-Frank) | OTC derivative trade economics, to a Trade Repository | "What's the systemic OTC derivatives exposure across the whole market?" | [Module 2](../1-capital-markets/module-02-hierarchy.md) §7 |
| **Securities Financing Reporting** (SFTR) | Repo, securities lending, and other financing transactions | "How much collateral re-use and financing leverage exists in the system?" | [Module 5](../1-capital-markets/module-05-fixed-income.md) §8 |

```text
WHY ONE TRADE CAN TRIGGER MULTIPLE, GENUINELY DIFFERENT REPORTS
========================================================================

  Bank Of Toronto's 5Y CAD IRS (Module 8 §4a) with Goldman Sachs:

  -&gt; DERIVATIVES REPORTING (EMIR/Dodd-Frank): YES, reportable --
     it's an OTC derivative (Module 2 §07)

  -&gt; TRANSACTION REPORTING (MiFIR): potentially YES if either
     party is EU/UK-regulated and the swap is deemed
     MiFID-in-scope (Module 6 §08's boundary discussion, applied
     to Rates instead of FX)

  -&gt; POSITION REPORTING: only if Bank Of Toronto's cumulative
     position in a POSITION-LIMIT-SUBJECT product (Module 7 §08's
     commodity derivative limits) crosses a threshold -- NOT
     applicable to a plain CAD IRS

  -&gt; SECURITIES FINANCING REPORTING (SFTR): NO -- an IRS is a
     derivative, not a financing transaction (Module 5 §08's
     distinction, restated)

  -&gt; Same trade, evaluated against FOUR different reporting
     regimes, with genuinely different YES/NO/MAYBE answers for
     each -- exactly the kind of decision matrix a reporting
     system's rules engine has to encode correctly.
```

**Takeaway:** "is this trade reportable?" is never a single yes/no question — it's five separate yes/no questions against five separate regimes, and a trade can legitimately be reportable under some and exempt under others simultaneously. [Module 13](../1-capital-markets/module-13-parties-leis.md) §6 already showed this same multiplicity for WHO reports; this section shows it for WHAT gets reported at all.

> **Deep dive available.** Position Reporting and Derivatives/SFT Reporting run on genuinely different clocks — one periodic and concentration-focused, the others a **daily** valuation-update obligation for as long as a position stays open. For the full breakdown of how often each regime actually reports, with worked EMIR and SFTR daily-valuation examples, see [Module 26 — Position Deep Dive](../1-capital-markets/module-26-position-deep-dive.md) §11.

---

## 2. What Data, and Why — Completing the Curriculum's Own Question

**Definition:** this module's stated goal (per the curriculum) is answering **"what happened in the trade → what data describes it → why does the regulator need that data?"** — Section 1 answered the first half; this section answers the second.

| Data Category | Example Fields | Why the Regulator Needs It |
|---|---|---|
| Economic Terms | Notional, Fixed Rate, Maturity ([Module 1](../1-capital-markets/module-01-fundamentals.md) §8) | Baseline "what is this trade" — without it, nothing else is interpretable |
| Product Classification | UPI, ISDA Taxonomy ([Module 2](../1-capital-markets/module-02-hierarchy.md) §7, §12) | Lets a regulator bucket millions of trades into comparable categories without reading each contract |
| Counterparty Identity | LEI, in all its [Module 13](../1-capital-markets/module-13-parties-leis.md) §4 roles | Who's exposed to whom — the raw material for systemic concentration analysis |
| Valuation | MTM, Fair Value Level ([Module 10](../1-capital-markets/module-10-valuation.md)) | Is the reported exposure real and current, or stale? |
| Exposure &amp; Collateral | Current Exposure, Collateral Held ([Module 11](../1-capital-markets/module-11-risk-exposure.md)-12) | Is risk actually being managed, or is a firm under-collateralized and one default away from trouble? |
| Venue &amp; Clearing | Exchange vs OTC, CCP vs bilateral (every asset-class module's §7) | Is risk concentrated in opaque bilateral relationships or absorbed by a regulated CCP? |

**Takeaway:** every single field category in this table maps to a section this roadmap has already built in detail — regulatory reporting isn't a new set of concepts bolted on at the end, it's a repackaging of everything from Modules 1-13 and 15 into the specific shape five different regulators each independently decided they need.

---

## 3. EMIR Deep Dive — What an OTC Derivative Report Actually Contains

**Definition:** [Module 2](../1-capital-markets/module-02-hierarchy.md) §7 introduced EMIR's Trade Repository obligation; [Module 13](../1-capital-markets/module-13-parties-leis.md) §6 introduced dual-sided reporting. Here's what actually gets submitted, and a genuinely new concept: **pairing and reconciliation** at the Trade Repository itself.

```text
EMIR REPORT — FIELD GROUPS, USING BANK OF TORONTO'S 5Y CAD IRS
========================================================================

  Counterparty data: Bank Of Toronto's LEI, Goldman Sachs' LEI
                      (Module 4 §6, Module 13 §4)
  Common data:        UPI (product template), UTI (this specific
                      trade), Notional, Fixed Rate, Maturity
                      (Module 2 §12)
  Valuation data:      MTM, valuation timestamp, Fair Value Level
                      (Module 10 §6)
  Collateral data:     Collateral posted, CSA reference (Module 12)

  PAIRING &amp; RECONCILIATION (new): because EMIR is dual-sided
  (Module 13 §6), BOTH Bank Of Toronto's and Goldman Sachs'
  independently-submitted reports for this SAME trade land at the
  Trade Repository. The Trade Repository automatically attempts to
  PAIR them (matching on UTI) and RECONCILE the economic fields --
  if Bank Of Toronto reports Notional CAD 100,000,000 and Goldman
  Sachs reports CAD 100,500,000 for the same UTI, that's a
  reconciliation BREAK, flagged back to both firms for correction.
```

**Takeaway:** EMIR's dual-sided design isn't just about redundancy — the Trade Repository's pairing/reconciliation process is itself a data-quality control, catching exactly the kind of [Module 12](../1-capital-markets/module-12-collateral-margin.md) §7-style dispute (two firms computing different numbers for the same trade) at the regulatory level, not just the bilateral collateral-call level.

---

## 4. MiFID II Transaction Reporting Deep Dive — A Different Regime, A Different Purpose

**Definition:** MiFID II Transaction Reporting (under its technical standard, RTS 22) is frequently confused with EMIR, but the two regimes differ on almost every dimension that matters.

| Dimension | EMIR | MiFID II Transaction Reporting |
|---|---|---|
| Scope | OTC derivatives only ([Module 2](../1-capital-markets/module-02-hierarchy.md) §07) | ALL in-scope financial instruments — equities, bonds, listed AND OTC derivatives |
| Destination | Trade Repository ([Module 2](../1-capital-markets/module-02-hierarchy.md) §07) | National Competent Authority directly (no Trade Repository intermediary) |
| Purpose | Systemic risk monitoring (Section 01) | Market abuse detection (insider dealing, market manipulation) |
| Sides Reporting | Dual-sided ([Module 13](../1-capital-markets/module-13-parties-leis.md) §6) | Generally single-sided, investment-firm-reports-for-client model |
| Field Count (illustrative) | ~85 fields under EMIR REFIT | ~65 fields under RTS 22 |
| Deadline | T+1 | T+1 |

```text
THE SAME EQUITY TRADE, TWO DIFFERENT REGIMES, ZERO OVERLAP IN PURPOSE
========================================================================

  Maple Leaf Pension Fund buys Coca-Cola stock via Bank Of Toronto
  (Module 4 §4a):

  EMIR: NOT APPLICABLE -- Coca-Cola stock is a cash security, not
        an OTC derivative (Module 4 §08's exact scoping rule)

  MiFID II Transaction Reporting: APPLICABLE -- Bank Of Toronto
        reports the execution to its National Competent Authority,
        specifically so regulators can screen for suspicious
        trading patterns (e.g., trading ahead of undisclosed news)

  -&gt; A cash equity trade that NEVER touches EMIR still generates
     a full MiFID II transaction report -- proof that "regulatory
     reporting" genuinely means different things depending on
     which regime is asking.
```

**Takeaway:** EMIR asks "how much systemic derivatives risk exists in the market"; MiFID II Transaction Reporting asks "did anyone behave abusively on this specific execution" — two regimes that happen to share some infrastructure concepts (T+1 deadlines, LEIs) but serve completely different regulatory missions, which is exactly why a cash equity trade needs one and not the other.

---

## 5. SFTR Deep Dive — Completing the Repo Reporting Picture

**Definition:** [Module 5](../1-capital-markets/module-05-fixed-income.md) §8 first introduced SFTR as covering repo; [systems-landscape](../1-capital-markets/systems-landscape.md) §12 walked through Bank Of Toronto's overnight repo worked example. Here's how that connects to this module's broader reporting framework.

```text
SFTR REPORT — Bank Of Toronto's overnight repo (systems-landscape §12)
========================================================================

  Loan data:      EUR 50,000,000 cash lent, overnight, 3.15% rate
  Collateral data: Germany 3% Bund 2033, Market Value, Haircut
                   (Module 12 §3's exact haircut mechanic, applied
                   to THIS specific collateral)
  Counterparty:    Bank Of Toronto's LEI, Goldman Sachs' LEI

  -&gt; Notice SFTR's report shape genuinely mirrors EMIR's (Section
     03) -- counterparty data, economic terms, collateral data --
     but answers a DIFFERENT systemic question (Section 01's
     "how much collateral re-use and financing leverage exists,"
     not "how much derivatives risk exists").
```

**Takeaway:** SFTR is this roadmap's proof that the reporting-field PATTERN (who, what, valuation, collateral) is more universal than any single regime — the same shape of data serves EMIR's derivatives-risk question and SFTR's financing-leverage question equally well, because both are fundamentally asking "who owes what to whom, backed by what."

---

## 6. Cross-Regime Comparison — The Full Picture

| Regime | Covers | Destination | Core Question |
|---|---|---|---|
| **EMIR** (EU/UK) | OTC derivatives | Trade Repository | Systemic derivatives risk |
| **Dodd-Frank** (US) | OTC derivatives (swaps + security-based swaps, [Module 9](../1-capital-markets/module-09-credit.md) §08's split) | Swap Data Repository | Systemic derivatives risk (US equivalent of EMIR) |
| **MiFID II / MiFIR** (EU/UK) | All in-scope instruments | National Competent Authority | Market abuse detection |
| **SFTR** (EU/UK) | Repo, securities lending | Trade Repository | Financing leverage / collateral re-use |
| **Position Reporting** (various) | Commodity derivatives, some others | Exchange / regulator | Concentration risk in one product |

**[Systems-landscape](../1-capital-markets/systems-landscape.md) cross-reference:** exactly which regulator and which local Trade Repository/NCA a given trade reports to depends on the jurisdiction — [systems-landscape](../1-capital-markets/systems-landscape.md) §09-10 already maps this out country by country (Canada, US, UK, EU, Hong Kong) in full, including the specific local regulator and repository names; this module deliberately doesn't repeat that mapping, since it's a lookup table, not a concept.

**Takeaway:** five regimes, five destinations, five core questions — but notice every regime in this table ultimately traces back to the same post-2008 root cause [Module 6](../1-capital-markets/module-06-fx.md) §8, [Module 8](../1-capital-markets/module-08-rates.md) §8, and [systems-landscape](../1-capital-markets/systems-landscape.md) §01 have all independently flagged: regulators couldn't see enough of what was happening in OTC and financing markets before the crisis, and every regime here exists to close some version of that same visibility gap.

---

## 7. IT/Reporting Angle — Annotating the Curriculum's Own Capstone Record

**This is the moment the entire roadmap has been building toward.** The curriculum's own "Final End-to-End Case Study" (which follows this module) presents exactly this record:

```text
Trade ID: IRS12345
Asset Class: Derivative
Product: Interest Rate Swap
Notional: CAD 100M
Party A: Bank Of Toronto
Party B: Goldman Sachs
Maturity: 5Y
MTM: +CAD 2.4M
Exposure: CAD 2.0M
Collateral: CAD 1.8M
Custodian: XYZ
Settlement: ...
Reporting: ...
```

Every single field, fully annotated against where this roadmap built it:

```text
Trade ID: IRS12345
  -&gt; the human-readable trade identifier; the actual regulatory
     identifier reported under EMIR/Dodd-Frank is the UTI (Module
     2 §12), generated at execution, globally unique

Asset Class: Derivative / Product: Interest Rate Swap
  -&gt; Module 1 §2's Asset Class -&gt; Product hierarchy, refined by
     the ISDA Taxonomy (Module 2 §7) into the UPI that actually
     gets reported

Notional: CAD 100M
  -&gt; Module 1 §8's foundational point: a REFERENCE amount for
     calculating cash flows, matching Module 8 §4a's exact IRS --
     this notional itself never settles

Party A: Bank Of Toronto / Party B: Goldman Sachs
  -&gt; Module 13 §1's Counterparty terminology (not Buyer/Seller --
     a swap has no natural buy/sell direction), each identified
     by their own LEI (Module 4 §6, Module 13 §4) on the actual
     report, not just a name

Maturity: 5Y
  -&gt; static economic term, Module 1 §8

MTM: +CAD 2.4M
  -&gt; Module 10 §1's Mark-to-Market concept -- and specifically,
     this figure matches Module 11 §3's GROSS POSITIVE MTM across
     Bank Of Toronto's full netting set with Goldman Sachs (the
     IRS's own +1,500,000 plus the FX Forward's +900,000). This
     is a genuinely important real-world ambiguity: is "MTM" on
     this record describing Trade ID IRS12345 ALONE, or the whole
     NETTING SET? Module 11 §3's IT/reporting angle flagged
     exactly this -- a record needs an explicit Netting Set ID
     field to disambiguate, because in practice systems often DO
     show netting-set-level Exposure/Collateral fields attached to
     one representative Trade ID, exactly as this capstone record does

Exposure: CAD 2.0M
  -&gt; Module 11 §3's NET Exposure after ISDA close-out netting --
     LOWER than MTM specifically because Trade 3 (the CDS, -CAD
     400,000) nets against the other two

Collateral: CAD 1.8M
  -&gt; Module 12 §5's Required Collateral, driven by a CAD 200,000
     CSA Threshold -- NOT collateral posted "at random," a
     specific negotiated number

Custodian: XYZ
  -&gt; Module 13 §2's Custodian role -- specifically Bank Of
     Toronto's OWN custodian, holding Bank Of Toronto's side of
     posted/received collateral (this record is Bank Of Toronto's
     own trade capture, so every field on it is Bank Of Toronto's
     view). Goldman Sachs would separately have its own custodian
     on its own books, not shown here. Logically distinct from the
     CCP (if this were centrally cleared) or the CSD (Module 13
     §2's full role table)

Settlement: ...
  -&gt; Module 1 §7's DvP mechanics for the quarterly net cash flows
     (Module 8 §4a), or Module 3's full lifecycle if referring to
     the trade's own confirmation/clearing process

Reporting: ...
  -&gt; Section 03 of THIS module -- an EMIR (or Dodd-Frank) report,
     dual-sided (Module 13 §6), reconciled against Goldman Sachs'
     own independently-submitted report at the Trade Repository
```

**Takeaway:** every field on this record — the exact record the curriculum set out to make understandable from the very first page — now has a specific module, section, and worked-number origin behind it. Nothing on this record is arbitrary, and nothing on it needs to be memorized as an isolated fact; each field is the concrete output of a concept this roadmap built with real numbers, from [Module 1](../1-capital-markets/module-01-fundamentals.md) all the way through [Module 15](../1-capital-markets/module-15-listed-derivatives.md).

---

## 8. Regulatory Perspective — Reporting Accuracy, ISO 20022 &amp; Enforcement

| Regulation / Body | How it touches Regulatory Reporting specifically |
|---|---|
| **EMIR REFIT** (2024 update) | A significant overhaul standardizing EMIR's reporting fields onto the **ISO 20022 XML** message format (replacing older, less structured formats) — a genuinely major IT migration event for every EU/UK derivatives desk's reporting pipeline, similar in scale to [Module 8](../1-capital-markets/module-08-rates.md) §1's LIBOR-to-RFR transition |
| **National Competent Authorities** (enforcement) | Regulators actively fine firms for reporting FAILURES, not just missing data — late reports, unreconciled breaks (Section 03), and incorrect field population have all drawn real, material regulatory fines across EMIR, Dodd-Frank, and MiFID II regimes |
| **Firms' own control functions** | Most large banks run a dedicated **Regulatory Reporting Control** function (adjacent to, but distinct from, the Product Control/IPV function from [Module 10](../1-capital-markets/module-10-valuation.md) §7) whose entire job is verifying reportable trades were actually reported, correctly, on time — a control layer this roadmap hasn't needed until reporting itself became the final topic |
| **IOSCO / CPMI** (already established, [Module 13](../1-capital-markets/module-13-parties-leis.md) §8) | Continue setting the global principles every regime in Section 06 ultimately implements locally — the same standard-setters from [Module 10](../1-capital-markets/module-10-valuation.md)-13 close the loop here as well |

**Takeaway:** regulatory reporting isn't a "submit and forget" process — it's an ongoing control discipline with its own dedicated function, its own data-format migrations (ISO 20022), and its own enforcement risk, exactly mirroring the Independent Price Verification control [Module 10](../1-capital-markets/module-10-valuation.md) §7 introduced for valuation. Getting the report right the first time is cheaper, in every sense, than fixing a reconciliation break or a regulatory fine after the fact.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Party A on the capstone IRS12345 record; dual-sided EMIR reporter |
| Goldman Sachs | Party B; independently submits its own matching (or mismatching) EMIR report |
| Trade Repository | Pairs and reconciles both counterparties' EMIR reports (Section 03) |
| National Competent Authority | Direct recipient of MiFID II Transaction Reports (Section 04) |
| IOSCO / CPMI | Global standard-setters underpinning every regime in Section 06 |

---

## Recap Quiz — Numbers Edition

1. Using Section 01, is Bank Of Toronto's Coca-Cola stock trade ([Module 4](../1-capital-markets/module-04-equity.md) §4a) reportable under SFTR? Under EMIR? Under MiFID II Transaction Reporting? Give a yes/no for each with a one-sentence reason.
2. On Section 03's EMIR pairing example, if Bank Of Toronto reports UTI "ABC123" and Goldman Sachs reports UTI "ABC124" for what both firms believe is the SAME trade, what would the Trade Repository's pairing process most likely flag?
3. Using Section 04's comparison table, why does MiFID II Transaction Reporting apply to the Coca-Cola trade while EMIR does not, even though both regimes exist "to see more of what's happening in the market"?
4. Using Section 05, what's the ONE thing SFTR's report structure and EMIR's report structure have genuinely in common, despite covering completely different products?
5. On Section 07's capstone record, why does the module say the CAD 2.4M MTM figure might describe the NETTING SET rather than Trade ID IRS12345 alone — what specific field, if added to the record, would resolve that ambiguity?
6. Using Section 07, which single module's content explains why "Collateral: CAD 1.8M" is not simply "whatever Bank Of Toronto and Goldman Sachs felt like posting"?
7. Using Section 08, name one reason a firm might face a regulatory fine for reporting EVEN IF every one of its trades was, eventually, correctly reported.

Congratulations — this completes the Asset Class Roadmap (Modules 4-9), its cross-cutting companion ([Module 15](../1-capital-markets/module-15-listed-derivatives.md)), and all five cross-cutting topics (Modules 10-14). Continue to the curriculum's own **Final End-to-End Case Study** to test everything against the IRS12345 record one more time, cold.

---

## Answers — Recap Quiz, with Explanations

**1. SFTR: NO — a cash equity purchase is not a securities financing transaction (no repo/lending structure, [Module 5](../1-capital-markets/module-05-fixed-income.md) §08). EMIR: NO — Coca-Cola stock is a cash security, not an OTC derivative ([Module 4](../1-capital-markets/module-04-equity.md) §08). MiFID II Transaction Reporting: YES — it's an in-scope financial instrument execution, reportable for market abuse surveillance regardless of asset class.**

Section 01's decision-matrix framing applies directly: each regime's applicability has to be checked independently, and a trade can be simultaneously out-of-scope for two regimes while being squarely in-scope for a third — exactly what Section 04's worked example demonstrates explicitly for this same Coca-Cola trade.

**2. A pairing/reconciliation BREAK — the Trade Repository would be unable to match the two reports to each other at all (mismatched UTIs), which is actually a MORE severe flag than an economic-field mismatch (like Section 03's notional example), since the repository can't even confirm both firms are describing the same trade.**

Section 03 states the pairing mechanism matches "on UTI" specifically — a UTI mismatch means the fundamental linking key itself is wrong, likely because one firm generated its own UTI instead of using the one the UTI-generating counterparty (per [Module 2](../1-capital-markets/module-02-hierarchy.md) §12's rules) actually produced, a more foundational data problem than a simple field discrepancy.

**3. Because the two regimes have entirely different SCOPES by design: EMIR is scoped specifically to OTC derivatives ([Module 2](../1-capital-markets/module-02-hierarchy.md) §07), so a cash equity trade structurally falls outside it regardless of purpose; MiFID II Transaction Reporting is scoped to ALL in-scope financial instruments specifically because market abuse can happen on ANY tradable instrument, not just derivatives.**

Section 04's worked example states this precisely — both regimes ultimately serve "seeing more of the market," but EMIR's product scope (derivatives only) and MiFID II's much broader instrument scope are independently defined, not derived from a shared underlying principle that would apply them identically.

**4. Both report structures share the same underlying FIELD PATTERN — counterparty data (who), economic/loan terms (what), and collateral data (backed by what) — because both regimes are fundamentally asking a version of the same systemic question: who owes what to whom, and how well is that obligation collateralized.**

Section 05's takeaway states this directly: "the reporting-field PATTERN... is more universal than any single regime" — SFTR and EMIR cover completely different products (financing transactions vs. derivatives) but converge on nearly identical report SHAPES because the underlying regulatory concern (systemic exposure, backed by what collateral) is structurally the same question either way.

**5. Because [Module 11](../1-capital-markets/module-11-risk-exposure.md) §3 already established that Exposure and Collateral are calculated and legally meaningful at the NETTING SET level (all trades under one ISDA Master Agreement), not per individual trade — a "Netting Set ID" field, distinct from "Trade ID," would let a system (and a reader) know definitively whether MTM/Exposure/Collateral describe just IRS12345 or the whole netting set it belongs to.**

Section 07 states this explicitly, citing [Module 11](../1-capital-markets/module-11-risk-exposure.md) §3's IT/reporting angle callout directly: exposure/collateral fields commonly get attached to netting-set-level aggregates in real systems even when displayed alongside a single representative Trade ID, which is exactly the ambiguity a dedicated Netting Set ID field resolves.

**6. [Module 12](../1-capital-markets/module-12-collateral-margin.md) (Collateral &amp; Margin) — specifically Section 05's Threshold-driven Required Collateral calculation, which showed CAD 1.8M is the deliberate, negotiated result of a CAD 200,000 CSA Threshold applied against CAD 2,000,000 of Net Exposure, not an arbitrary or ad-hoc figure.**

Section 07's annotation states this directly, pointing back to "[Module 12](../1-capital-markets/module-12-collateral-margin.md) §5's Required Collateral, driven by a CAD 200,000 CSA Threshold" — the CAD 1.8M figure is fully reconstructable from Net Exposure and the Threshold, both legally documented CSA terms, not a number either party could unilaterally change on a whim.

**7. Reporting FAILURES aren't only about missing data — LATE reporting (missing the T+1 deadline, Section 04's table) or reports that were initially incorrect and only fixed after a reconciliation break was flagged (Section 03) can both draw regulatory fines, even if the trade's data is fully accurate by the time anyone checks it after the fact.**

Section 08 states this directly: regulators "fine firms for reporting FAILURES, not just missing data — late reports, unreconciled breaks, and incorrect field population have all drawn real, material regulatory fines" — timeliness and first-time accuracy are independently enforceable obligations, not just an eventual-consistency nice-to-have.

---

[← Back to Index](../../index.md)
