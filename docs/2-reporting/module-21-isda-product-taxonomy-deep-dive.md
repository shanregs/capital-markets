# Module 21 — ISDA Product Taxonomy Deep Dive — Theory, Structure &amp; Where It's Actually Used

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Reference module**, in the same family as [SFTR](./module-16-sftr-reporting.md), [UK DMO Reporting](./module-17-dmo-uk-reporting.md), and [UPI, UTI &amp; ANNA-DSB](./module-18-upi-uti-dsb-deep-dive.md). [Module 2](../1-capital-markets/module-02-hierarchy.md) §7–§8 first introduced the ISDA Product Taxonomy's four-level hierarchy with one master table; this module goes deep — theory, full worked paths across every asset class, exactly which systems and regimes consume it, how it feeds the UPI, and how the values actually get configured on a trade.

---

## 1. What Is the ISDA Product Taxonomy? — Origin, Purpose &amp; the Two Things "ISDA" Means

**Definition:** **ISDA** (International Swaps and Derivatives Association) is the OTC derivatives industry's own trade body, and it standardizes two genuinely separate things that get conflated constantly:

| ISDA standardizes... | What it is | Covered in |
|---|---|---|
| **The ISDA Master Agreement** | A *legal contract template* two counterparties sign once, governing every OTC trade between them (netting, default, collateral) | [Module 12](../1-capital-markets/module-12-collateral-margin.md) |
| **The ISDA Product Taxonomy** | A *classification scheme* — a controlled vocabulary for labelling what KIND of derivative a trade is, so every firm, SDR, and regulator uses the same categories | This module |

The taxonomy's origin is specific and regulatory: in the aftermath of the 2008 crisis, the **Dodd-Frank Act (2010)** required, for the first time, **real-time public reporting** of swap transactions in the US — every swap had to be disseminated publicly (price, notional-size bucket) shortly after execution, subject to size-based masking for large ("block") trades. That created an immediate practical problem: to set a sensible reporting delay and notional-size threshold, regulators and Swap Data Repositories (SDRs) needed to bucket swaps into comparable categories — a 5Y USD interest rate swap and a 5Y Brent crude oil swap clearly shouldn't share the same block-trade threshold. Rather than have the CFTC, each SDR, and every reporting firm invent its own incompatible bucket scheme, **ISDA — as the industry's own standards body — published a taxonomy everyone could converge on**: freely available, not proprietary, distributed as reference documents/spreadsheets via [isda.org](https://www.isda.org).

**Takeaway:** the taxonomy **classifies**, it does not **identify** — this is the single most important distinction versus the [UPI](./module-18-upi-uti-dsb-deep-dive.md), which is easy to conflate since both sit at the "product" layer. A taxonomy path (e.g. `Interest Rate / IRSwap / FixedFloat / Swap`) is a *category label* shared by every trade of that shape; a UPI is a *specific issued code* for one exact combination of economic terms within that category (Section 5 connects the two precisely).

---

## 2. The Taxonomy Hierarchy — Four Levels, Five Asset Classes

**Definition:** restating and extending [Module 2](../1-capital-markets/module-02-hierarchy.md) §7 — the ISDA Product Taxonomy is a **four-level hierarchy**, deliberately one rung more granular than the "Product" layer in this curriculum's own Asset Class → Product → Instrument → Trade model ([Module 2](../1-capital-markets/module-02-hierarchy.md) §1):

```text
ISDA PRODUCT TAXONOMY — FOUR LEVELS
========================================================================

  Asset Class  ->  Base Product  ->  Sub-Product  ->  Transaction Type
  ======================================================================

  Interest Rate      -> IRSwap     -> FixedFloat   -> Swap
  Credit              -> SingleName -> Corporate    -> CreditDefaultSwap
  Foreign Exchange    -> Forward    -> Deliverable   -> Forward
  Equity              -> Swap       -> PriceReturn   -> Swap
  Commodity           -> Energy     -> Oil           -> Swap

  Each level narrows the category further left-to-right. A trade's
  full "taxonomy path" is all four values read together -- none of
  them alone is enough to identify the product.
```

There are exactly **five official ISDA Asset Classes** for derivatives — a short, closed list, distinct from the open-ended "Asset Class" values you'd see once cash instruments are included too ([Module 2](../1-capital-markets/module-02-hierarchy.md) §11's side-by-side table):

| ISDA Asset Class | Plain-English meaning | Example Base Products |
|---|---|---|
| **Interest Rate** | Cash flows depend on interest rates | IRSwap, FRA, Swaption, CapFloor, Cross-Currency Swap |
| **Credit** | Payout depends on a default/credit event | SingleName, Index (CDX/iTraxx), TotalReturnSwap |
| **Foreign Exchange** | Payout depends on currency rates | Forward, Swap, Option, NDF |
| **Equity** | Payout depends on a stock/index price | Swap, Option, VarianceSwap, Future |
| **Commodity** | Payout depends on a physical commodity price | Swap, Forward, Option (Energy, Metals, Agriculture sub-classes) |

> **Callout — v1.0 vs v2.0.** ISDA published an initial taxonomy (**v1.0**, ~2012) built specifically to satisfy Dodd-Frank's real-time reporting categories — coarse, oriented around what CFTC block-size rules needed. A more granular **v2.0** (~2016) followed, restructured to align field-by-field with **FpML** (the derivatives industry's own XML messaging standard), and added finer sub-categories in places (an extra qualifier beyond Transaction Type — e.g. a settlement-type or option-style flag — depending on the asset class). Treat exact category names and which version a given system implements as something to confirm per-system, the same hedge this curriculum already applies to the [UTI generation waterfall](./module-18-upi-uti-dsb-deep-dive.md) §2 and to [SFTR](./module-16-sftr-reporting.md) §7's specifics — these get revised, and a reporting record's taxonomy field is only meaningful alongside a stated version.

**Takeaway:** the taxonomy is a **classification tree, not a flat code list** — that's precisely why it needs four levels instead of one: "Swap" alone (Transaction Type) is ambiguous across all five asset classes, but `Interest Rate / IRSwap / FixedFloat / Swap` is not. This is the same principle [SFTR](./module-16-sftr-reporting.md) §2's small, closed `Type of SFT` code set uses at one level of granularity — the ISDA taxonomy solves the same "what kind of product is this" problem for a far larger, more varied universe of OTC derivatives, so it needs more levels to stay unambiguous.

---

## 3. Worked Examples — Full Taxonomy Path, Asset Class by Asset Class

**Definition:** pulling five trades already built elsewhere in this curriculum through the full four-level path, so the hierarchy can be seen assigned to real (illustrative) trades rather than abstractly.

```text
FIVE TRADES, FIVE FULL TAXONOMY PATHS
========================================================================

  INTEREST RATE  (Module 8's 5Y CAD IRS, Bank Of Toronto pays fixed)
    Asset Class: Interest Rate | Base Product: IRSwap
    Sub-Product: FixedFloat    | Transaction Type: Swap
    -> "Interest Rate / IRSwap / FixedFloat / Swap"

  CREDIT  (Module 2 §9's 5Y CDS on Northern Rail Corp, Bank Of Toronto
           buys protection from Goldman Sachs)
    Asset Class: Credit        | Base Product: SingleName
    Sub-Product: Corporate     | Transaction Type: CreditDefaultSwap
    -> "Credit / SingleName / Corporate / CreditDefaultSwap"
    (Module 9 §4a's Northstar Resources Inc. CDS, used throughout the
     UPI/UTI deep dive, carries this identical taxonomy path -- same
     product shape, different reference entity and counterparty)

  FOREIGN EXCHANGE  (Module 2 §10's 3M USD/CAD Forward, Bank Of
                     Toronto buys USD from Goldman Sachs)
    Asset Class: Foreign Exchange | Base Product: Forward
    Sub-Product: Deliverable      | Transaction Type: Forward
    -> "Foreign Exchange / Forward / Deliverable / Forward"

  EQUITY  (Module 2 §8's Total Return Swap on Apple Inc. shares)
    Asset Class: Equity        | Base Product: Swap
    Sub-Product: PriceReturn   | Transaction Type: Swap
    -> "Equity / Swap / PriceReturn / Swap"

  COMMODITY  (Module 7's 1Y Brent Crude Oil Swap, notional
             100,000 barrels)
    Asset Class: Commodity     | Base Product: Energy
    Sub-Product: Oil           | Transaction Type: Swap
    -> "Commodity / Energy / Oil / Swap"
```

**Takeaway:** notice **Transaction Type is "Swap" for three of these five trades** (Interest Rate, Equity, Commodity) — proof that no single level of this hierarchy is sufficient alone, and that "what kind of product is this" genuinely requires all four levels read together. Also notice the Credit path's Sub-Product ("Corporate") describes the *reference entity's type* (a corporate borrower, vs. e.g. a sovereign), while every other asset class's Sub-Product describes an *economic feature of the contract itself* (Fixed vs. Floating, Deliverable vs. Non-Deliverable, Price vs. Total Return) — the taxonomy's categories are genuinely asset-class-specific in what they choose to distinguish, echoing the same point the [UPI deep dive](./module-18-upi-uti-dsb-deep-dive.md) §3 makes about UPI reference-data templates.

---

## 4. Are Bonds and Cash Securities Ever Classified with ISDA Taxonomy?

**Definition:** No — restating and extending [Module 2](../1-capital-markets/module-02-hierarchy.md) §6's point directly, because it's a common category error for anyone coming from an IT/data background where "taxonomy" sounds like it should cover everything on a security master. The ISDA Product Taxonomy classifies **bilateral contracts** — instruments that exist only because two counterparties agreed to them, and have no existence independent of that agreement. A bond, a share, a unit of commercial paper — these are all things that exist and can be held on their own, the moment they're issued, regardless of who currently owns them. That structural difference is exactly why cash instruments use a completely different classification scheme:

| | Cash Instruments (bonds, equities, money market) | OTC Derivatives |
|---|---|---|
| **What's being classified** | A deliverable *thing* | A bilateral *contract* |
| **Classification scheme** | **CFI Code** (ISO 10962 — *Classification of Financial Instruments*), a 6-character code baked into most security masters | **ISDA Product Taxonomy** (this module) |
| **Instrument identifier** | ISIN / CUSIP | No ISIN — UPI instead ([UPI deep dive](./module-18-upi-uti-dsb-deep-dive.md)) |
| **Governing body** | ISO, via National Numbering Agencies | ISDA (industry trade body, Section 7) |

```text
A CDS ON NORTHERN RAIL CORP vs. A NORTHERN RAIL CORP BOND -- SAME
COMPANY, TWO COMPLETELY DIFFERENT CLASSIFICATION WORLDS
========================================================================

  Northern Rail Corp 4.5% Bond 2030 (a cash instrument):
    ISIN: (issued by a National Numbering Agency)
    CFI Code: DBFUFR (illustrative -- debt, straight bond,
              fixed rate, registered)
    -> NO ISDA taxonomy field. It's a deliverable security, full stop.

  5Y CDS on Northern Rail Corp (Module 2 §9, a derivative CONTRACT
  that merely references the same company):
    Taxonomy path: Credit / SingleName / Corporate / CreditDefaultSwap
    -> NO CFI code, NO ISIN. It's a bilateral contract, classified
       by ISDA taxonomy, identified by a UPI instead of an ISIN.
```

> **Nuance — a derivative ON a bond still gets ISDA-classified normally.** A bond option or bond future (e.g. an option to buy a specific government bond at a fixed price on a future date) is itself a derivative contract, and gets its own ISDA taxonomy path (typically under Interest Rate or Credit, depending on structure) exactly like any other OTC derivative — it's the *underlying cash bond itself* that never carries an ISDA classification, not "anything connected to a bond."

**Takeaway:** the fastest sanity check for "does this reporting record need a taxonomy field" — if the record has an ISIN and a CFI code, it's a cash instrument and the ISDA taxonomy fields should be empty/absent; if it has a UPI and no ISIN, it's a derivative and the taxonomy fields are mandatory. A rules engine that populates both, or neither, on the same record has a bug — [Module 2](../1-capital-markets/module-02-hierarchy.md) §11's side-by-side table already established this as the single quickest instrument-type discriminator in this whole curriculum.

---

## 5. How the Taxonomy Feeds Into the UPI — the DSB's Reference-Data Schema

**Definition:** this is the piece that makes the ISDA Product Taxonomy and the [UPI](./module-18-upi-uti-dsb-deep-dive.md) feel like the same thing even though Section 1 drew a hard line between them — because **they genuinely are related, just at different layers of the same lineage**. When CPMI-IOSCO built its 2017 Technical Guidance defining what a global UPI needed to classify, it didn't invent a brand-new classification framework from scratch — it started from ISDA's already-industry-adopted taxonomy (v2.0) as the base classification logic, then had the DSB operationalize it into a live reference-data system that could actually *issue* codes.

```text
FROM INDUSTRY TAXONOMY TO ISSUED IDENTIFIER — THE FULL LINEAGE
========================================================================

  ISDA PRODUCT TAXONOMY (this module)
    Industry-published classification scheme (v1.0 -> v2.0).
    A CONTROLLED VOCABULARY -- Asset Class / Base Product /
    Sub-Product / Transaction Type. No codes issued, no lookup
    system -- just the category definitions themselves.
         |
         v
  CPMI-IOSCO TECHNICAL GUIDANCE (2017)
    Global regulatory standard-setters adopt ISDA's taxonomy
    structure as the BASIS for a harmonized product-classification
    framework every G20 jurisdiction's regulators could converge on.
         |
         v
  DSB UPI REFERENCE-DATA SCHEMA (operated by ANNA-DSB, since 2022)
    Turns the taxonomy's categories into ACTUAL FIELDS on a live,
    queryable reference-data template per asset class (Asset Class,
    Instrument Type, Underlier, Notional Currency, Tenor, etc. --
    UPI deep dive Section 3) -- and, critically, ISSUES A CODE
    (the UPI itself) for each unique combination of those fields.
         |
         v
  EMIR / DODD-FRANK / OTHER REGIMES' TRADE REPORTS
    Consume the ISSUED UPI as a single mandatory field -- the
    reporting firm never re-derives the taxonomy path by hand; it
    looks up (or requests) the UPI that already encodes it.
```

**Takeaway:** think of the ISDA Product Taxonomy as the **grammar**, and the UPI reference-data template as the **dictionary built from that grammar** — the taxonomy defines what kinds of categories are even possible to express (Asset Class, Base Product, Sub-Product, Transaction Type), while the DSB's UPI system is the operational layer that actually assigns a permanent, lookup-able code to one specific combination of those categories plus a handful of additional economic fields (Underlier, Notional Currency, Tenor). A reporting engineer never populates "ISDA Taxonomy" and "UPI" as two independently-derived fields on the same trade — the UPI's reference data *already encodes* the taxonomy path; querying the UPI (Section 8's practical workflow) is how you'd recover it.

---

## 6. Where the Taxonomy Is Actually Used — Beyond the UPI

**Definition:** the taxonomy predates the UPI by roughly a decade and has several uses that have nothing to do with UPI reference data at all — it's genuinely foundational plumbing across US swap regulation, not just a historical stepping-stone to a newer identifier.

| Use | How the taxonomy is actually consumed | Notes |
|---|---|---|
| **Dodd-Frank real-time public reporting** (CFTC Part 43) | Sets **which block-trade notional threshold applies** — the CFTC publishes separate large-notional thresholds per taxonomy category, since a "large" 5Y USD IRS and a "large" Brent crude swap are completely different sizes. A swap's taxonomy path literally determines its public-dissemination delay and masking rule. | The taxonomy's original reason for existing (Section 1) |
| **Dodd-Frank swap data recordkeeping** (CFTC Part 45) &amp; CFTC's own published Swaps Report | SDRs (e.g. DTCC Data Repository) bucket submitted swap data by taxonomy category for the CFTC's own aggregate weekly/quarterly swaps market statistics | Same categories, different consumer (regulator's own market surveillance vs. public dissemination) |
| **SEF / MAT determinations** | A **Made-Available-to-Trade** determination is scoped in taxonomy terms — e.g. "this SEF's MAT determination covers Fixed-Float USD IRS in these tenor buckets" — so whether a given swap MUST trade on a SEF depends on matching its taxonomy path (plus tenor/currency) against a published MAT determination | Governs execution-venue obligations, not just reporting |
| **CCP clearing-mandate determinations** | Clearing mandates (which products a CCP like LCH SwapClear MUST clear, and which counterparties must clear through one) are published against taxonomy-shaped product descriptions | Complements — doesn't replace — [Module 8](../1-capital-markets/module-08-rates.md) §5's clearing mechanics |
| **UPI reference data** (Section 5) | Basis for the DSB's product classification fields | The newest, most recently added use |
| **Internal bank product masters** | Every OTC derivative product template gets tagged with a taxonomy path at onboarding, so risk/valuation/reporting systems can filter and aggregate consistently | Section 8's practical IT view |

> **Callout — EMIR doesn't literally name a field "ISDA Taxonomy."** This is worth flagging explicitly because it's easy to over-generalize from the US rules above: **EMIR's** own reporting technical standards (and the CPMI-IOSCO CDE fields it aligned to during REFIT) use their own product-classification fields — historically a CFI-style code, more recently the UPI itself — rather than a field literally labelled "ISDA Taxonomy." The *category logic* underneath still traces back to ISDA's work (Section 5's lineage), but if you're building an EMIR-specific reporting schema, don't go looking for a raw taxonomy-path field on the regulatory template itself; it's been absorbed into the UPI by the time it reaches an EU/UK report. The CFTC/Dodd-Frank rows above are where the taxonomy remains a directly-named, load-bearing regulatory concept in its own right.

**Takeaway:** the taxonomy's regulatory footprint is broadest and most explicit in the **US Dodd-Frank/CFTC regime** — it was built for that regime first, and still directly governs public-dissemination and MAT-determination scoping there — while in the **EU/UK EMIR world** it survives mainly as the ancestry behind the UPI (Section 5) rather than as a directly-cited field. Knowing which regime you're building for tells you whether "ISDA Taxonomy" needs to be a first-class field on your data model, or whether it only needs to inform how you map a product to its UPI.

---

## 7. Governance &amp; Versioning

**Definition:** ISDA is an **industry trade association**, not a regulator or a market-infrastructure utility like the [ANNA-DSB](./module-18-upi-uti-dsb-deep-dive.md) §4 — the same distinction that curriculum already draws for the DSB applies here even more directly, since ISDA doesn't operate any live system at all for the taxonomy; it publishes reference documents.

```text
WHO ACTUALLY CHANGES THE TAXONOMY, AND HOW
========================================================================

  ISDA's own asset-class-specific working groups/committees
  (member firms -- dealers, buy-side, infrastructure providers --
  volunteering expertise, the same governance model that maintains
  the ISDA Master Agreement itself, Module 12) periodically:

    -> propose new Base Products / Sub-Products / Transaction Types
       as new derivative structures emerge in the market
    -> consult industry members on proposed changes
    -> publish a revised taxonomy version (v1.0 -> v2.0, and
       incremental updates within a major version) as spreadsheets/
       reference documents on isda.org -- free, not proprietary

  -> ISDA does NOT mandate that anyone use the taxonomy. It becomes
     mandatory only where a REGULATOR (CFTC, under Dodd-Frank) or a
     downstream STANDARD (CPMI-IOSCO's UPI guidance, Section 5)
     requires its use -- ISDA supplies the vocabulary; regulators
     and standard-setters decide when it's compulsory.
```

**Takeaway:** exactly the same infrastructure-vs-mandate distinction the [UPI deep dive](./module-18-upi-uti-dsb-deep-dive.md) §4 draws for the DSB applies to ISDA here, one layer further upstream — "ISDA classifies this trade as X" is a statement about industry convention, not law; "the CFTC requires this trade's public report to use taxonomy category X to determine its block threshold" is the regulatory mandate that actually makes it matter. Confusing the two is the same category error [Module 13](../1-capital-markets/module-13-parties-leis.md) §8 already warns against for infrastructure providers generally.

---

## 8. Configuring Taxonomy Values in Practice — the Product-Master View

**Definition:** the practical IT question — exactly how does a taxonomy path actually get set on a real trade, for a bond, a swap, or anything else? The answer follows the same **template vs. instance** pattern the [UPI deep dive](./module-18-upi-uti-dsb-deep-dive.md) §3 already established for UPI itself: **the taxonomy path is a property of the product TEMPLATE, never re-derived or re-entered per individual trade.**

```text
WHERE THE TAXONOMY PATH ACTUALLY LIVES — PRODUCT MASTER, NOT TRADE RECORD
========================================================================

  BANK OF TORONTO'S PRODUCT MASTER (illustrative static reference
  data table -- one row per tradable PRODUCT SHAPE, not per trade)

  Product Name              | Asset Class | Base Product | Sub-Product | Txn Type
  ---------------------------------------------------------------------------------
  CAD Fixed-Float IRS       | Interest Rate | IRSwap     | FixedFloat  | Swap
  Single-Name CDS (Corp)    | Credit        | SingleName | Corporate   | CreditDefaultSwap
  USD/CAD Deliverable Fwd   | Foreign Exch. | Forward    | Deliverable | Forward
  Equity TRS (Price Return) | Equity        | Swap       | PriceReturn | Swap
  Brent Crude Swap          | Commodity     | Energy     | Oil         | Swap

  WHEN A TRADER BOOKS A NEW TRADE:
    1. Trade capture system asks "which product template?"
    2. Trader selects "CAD Fixed-Float IRS" (an EXISTING template --
       most trades reuse one)
    3. Taxonomy path (all four fields) flows onto the trade record
       AUTOMATICALLY from the product master row -- never manually
       typed by the trader, never re-classified per trade
    4. Trade-specific fields (notional, tenor, counterparty, rate)
       get entered separately -- these do NOT affect the taxonomy
       path at all (Section 1's classify-vs-identify distinction)

  IF THE DESK WANTS TO TRADE SOMETHING GENUINELY NEW (no existing
  template fits):
    -> Product Control / New Product Committee classifies it against
       the taxonomy FIRST (Section 7's ISDA reference documents are
       exactly what they'd consult), creates a NEW product-master row,
       and only then can trades against that new template be booked
    -> This is also the trigger for requesting a new UPI reference-
       data record from the DSB (Section 5) -- the taxonomy
       classification decision and the UPI request are typically the
       same new-product-onboarding event, not two separate ones
```

For a **bond**, the equivalent product-master row simply has no taxonomy columns populated at all — it carries a **CFI code** instead (Section 4), because it's a security master entry, not a derivative product template. A trade capture system built correctly treats "does this instrument type have taxonomy fields at all" as a property of the *asset class itself* (cash vs. derivative), not something every product template independently decides.

**Takeaway:** if you're designing a data model and wondering "should Taxonomy Path be a field on the Trade table or the Product table," the answer is **Product** — trades inherit it by reference, the same way [Module 2](../1-capital-markets/module-02-hierarchy.md) §1's Asset Class → Product → Instrument → Trade hierarchy already establishes that product-defining attributes live at the Product layer, not the Trade layer. A system that stores (and allows) a different taxonomy path per trade of the same product shape has a data-integrity bug, not a flexible feature.

---

## 9. Official Sources &amp; Links

**Definition:** as with every other reference module in this curriculum, this points to primary sources rather than restating rules that get periodically revised.

| Source | What It Covers | Link |
|---|---|---|
| **ISDA (International Swaps and Derivatives Association)** | Publishes the Product Taxonomy itself (v1.0, v2.0, and updates), the ISDA Master Agreement, and CSA documentation | [www.isda.org](https://www.isda.org) |
| **CFTC (Commodity Futures Trading Commission)** | Dodd-Frank real-time public reporting rules (Part 43), swap data recordkeeping (Part 45), block-trade thresholds by taxonomy category, MAT determinations | [www.cftc.gov](https://www.cftc.gov) |
| **CPMI (Committee on Payments and Market Infrastructures) / IOSCO** | 2017 Technical Guidance on UPI, which adopted ISDA's taxonomy as its classification basis (Section 5) | [www.bis.org/cpmi](https://www.bis.org/cpmi), [www.iosco.org](https://www.iosco.org) |
| **ANNA-DSB** | Operates the UPI reference-data system built on this taxonomy's lineage — see the [UPI, UTI &amp; ANNA-DSB Deep Dive](./module-18-upi-uti-dsb-deep-dive.md) | [www.anna-dsb.com](https://www.anna-dsb.com) |
| **ESMA** | EU/UK EMIR technical standards — where taxonomy-derived category logic reaches EU/UK reports via the UPI rather than a direct field (Section 6's callout) | [www.esma.europa.eu](https://www.esma.europa.eu) |

**Takeaway:** treat ISDA's own published taxonomy documents as the source of truth for the literal current category structure and version; the CFTC's rulebook as the source of truth for exactly how those categories drive US public-reporting obligations; and CPMI-IOSCO's Technical Guidance as the bridge explaining how the same categories became the basis for the globally-adopted UPI — the same three-way "standard, US mandate, global mandate" split this curriculum's other reference modules already apply to their own regimes.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Counterparty across every worked taxonomy path (Section 3); owner of the illustrative product master (Section 8) |
| Goldman Sachs | Counterparty across the Credit and FX worked examples (Section 3) |
| Northern Rail Corp | Reference entity for the Credit worked example (Section 3, [Module 2](../1-capital-markets/module-02-hierarchy.md) §9) |
| ISDA | Publishes and governs the Product Taxonomy itself, and the separate Master Agreement (Section 1, Section 7) |
| CFTC | US regulator whose Dodd-Frank rules make the taxonomy directly load-bearing for public reporting and MAT determinations (Section 6) |
| CPMI-IOSCO | Standard-setters who adopted ISDA's taxonomy as the classification basis for the global UPI (Section 5) |
| ANNA-DSB | Operates the UPI reference-data system built on the taxonomy's lineage (Section 5, cross-referenced from the [UPI deep dive](./module-18-upi-uti-dsb-deep-dive.md)) |
| ESMA | EU/UK regulator where the taxonomy's influence reaches reports indirectly, via the UPI (Section 6) |

---

## Recap Quiz

1. Using Section 1, what is the difference between what the ISDA Product Taxonomy does and what the UPI does, even though both sit at the "product" layer?
2. Using Section 2, why does the taxonomy need four levels instead of a single flat code — what would go wrong with just a Transaction Type value like "Swap" on its own?
3. Using Section 3, what is the full taxonomy path for Bank Of Toronto's 5Y CAD IRS, and which single level of that path differs between the IRS and the Equity TRS example?
4. Using Section 4, does a Northern Rail Corp bond ever get an ISDA taxonomy path? Does a CDS referencing Northern Rail Corp? Explain the distinction.
5. Using Section 5, describe the lineage from the ISDA Product Taxonomy to an actual issued UPI — what does each layer add that the one before it didn't have?
6. Using Section 6, name one use of the taxonomy in US Dodd-Frank/CFTC regulation that has nothing to do with the UPI at all.
7. Using Section 7, is ISDA a regulator? What actually makes using its taxonomy mandatory for a given trade?
8. Using Section 8, where does the taxonomy path live in a well-designed data model — on the Trade record or the Product record — and why does that matter operationally?

---

## Answers — Recap Quiz, with Explanations

**1. The taxonomy *classifies* — it's a category label (e.g. `Interest Rate / IRSwap / FixedFloat / Swap`) shared by every trade of that shape. The UPI *identifies* — it's a specific issued code for one exact combination of economic terms, built using the taxonomy's categories as its base classification logic (Section 5), but going further to include fields like Underlier, Notional Currency, and Tenor that the taxonomy itself doesn't capture.**

Section 1's takeaway states this directly as the most important distinction to hold onto, precisely because both sit at the "product" layer and get conflated.

**2. Because Transaction Type alone is ambiguous across asset classes — "Swap" by itself is the Transaction Type for the Interest Rate, Equity, AND Commodity worked examples in Section 3, so without the other three levels (Asset Class, Base Product, Sub-Product) there'd be no way to tell a 5Y CAD IRS apart from a Brent Crude Oil Swap from just that one field.**

Section 2's takeaway makes this exact point, and Section 3's worked examples demonstrate it concretely — three of the five trades share the identical Transaction Type value.

**3. `Interest Rate / IRSwap / FixedFloat / Swap`. Comparing to the Equity TRS's `Equity / Swap / PriceReturn / Swap`, the level that differs in a structurally interesting way is Base Product — IRSwap vs. Swap — while Transaction Type is identical ("Swap") for both, again showing Transaction Type alone can't distinguish them.**

Section 3's worked-examples block lists both paths directly, and its takeaway flags the shared "Swap" Transaction Type as the reason no single level is sufficient on its own.

**4. No — a Northern Rail Corp bond never gets an ISDA taxonomy path; it gets a CFI code instead, because it's a deliverable security, not a bilateral contract. A CDS referencing Northern Rail Corp DOES get a taxonomy path (`Credit / SingleName / Corporate / CreditDefaultSwap`), because the CDS itself is the bilateral contract being classified — the taxonomy classifies contracts, never the underlying thing a contract might reference.**

Section 4's worked comparison walks through exactly this pair — same company, two completely different classification worlds — and its nuance callout further clarifies that even a derivative ON a bond (a bond option/future) still gets ISDA-classified normally; only the underlying cash bond itself never does.

**5. ISDA's taxonomy (v1.0/v2.0) supplies the base classification vocabulary — Asset Class/Base Product/Sub-Product/Transaction Type, with no codes issued and no live system. CPMI-IOSCO's 2017 Technical Guidance adopts that vocabulary as the structural basis for a global harmonized standard. The DSB then operationalizes it into a live, queryable reference-data schema per asset class and actually issues a code (the UPI) for each unique combination of fields. Finally, EMIR/Dodd-Frank/other regimes' trade reports consume that already-issued UPI as one mandatory field, without re-deriving the taxonomy path by hand.**

Section 5's lineage diagram states each of these four layers explicitly, using the "grammar vs. dictionary built from that grammar" analogy in its takeaway.

**6. Any of: setting the block-trade notional threshold for Dodd-Frank real-time public reporting (Part 43), bucketing swaps for the CFTC's own published swaps market statistics (Part 45 recordkeeping), or scoping a SEF's MAT (Made-Available-to-Trade) determination.**

Section 6's table lists all three as uses that predate, and are independent of, the UPI's 2022 introduction — the taxonomy's original regulatory purpose, still directly load-bearing in the US regime today.

**7. No — ISDA is an industry trade association, not a regulator; it doesn't mandate that anyone use the taxonomy at all. Using it becomes mandatory only where an actual regulator (the CFTC, under Dodd-Frank) or a downstream standard (CPMI-IOSCO's UPI guidance) requires it.**

Section 7's diagram and takeaway state this directly, explicitly drawing the same infrastructure-vs-mandate distinction the UPI deep dive's Section 4 already established for the DSB, one layer further upstream.

**8. On the Product record, not the Trade record — trades select an existing product template and inherit its taxonomy path by reference; trade-specific fields (notional, counterparty, rate) never affect it. This matters operationally because a system that lets the same product shape carry different taxonomy paths on different trades has a data-integrity bug, and because new-product onboarding (creating a new taxonomy classification) is a distinct, governed event that happens once per product shape, not once per trade.**

Section 8's product-master diagram and takeaway both state this directly, tying it back to [Module 2](../1-capital-markets/module-02-hierarchy.md) §1's original point that product-defining attributes live at the Product layer of the hierarchy, not the Trade layer.

---

[← Back to Index](../../index.md)
