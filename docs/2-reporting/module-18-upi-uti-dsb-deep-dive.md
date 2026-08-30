# Module 18 — UPI, UTI &amp; ANNA-DSB Deep Dive — Derivative Identifiers, Explained

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> This is a **reference module**, in the same family as [Module 16 — SFTR](./module-16-sftr-reporting.md) and [Module 17 — UK DMO Reporting](./module-17-dmo-uk-reporting.md). [Module 2](../1-capital-markets/module-02-hierarchy.md) §12 first introduced UPI and UTI with one worked example; this module goes deep — structure, generation rules, the organization that actually runs the system (ANNA-DSB), and exactly which regulatory regimes require which identifier.

---

## 1. What Are UPI and UTI? — Why Derivatives Needed New Identifiers

**Definition:** cash securities have had a single global identifier for decades — the **ISIN** ([Module 2](../1-capital-markets/module-02-hierarchy.md) §2). OTC derivatives never had an equivalent, for a structural reason [Module 2](../1-capital-markets/module-02-hierarchy.md) §12 already established: a derivative has no existence independent of the two parties' contract, so there's no natural "one instrument, many owners" object to attach a single code to the way a share or a bond has. The 2008 financial crisis made that gap a serious regulatory problem — the G20's post-crisis OTC derivatives reforms (the same Pittsburgh 2009 commitments behind EMIR and Dodd-Frank, [Module 14](./module-14-regulatory-reporting.md) §1) required trade reporting, but reports from different firms used different internal product codes, making it effectively impossible for a regulator to aggregate "how much risk exists in 5Y CAD interest rate swaps across the whole market."

Two identifiers were built to close that gap, at two different layers:

| Identifier | Layer | Answers |
|---|---|---|
| **UPI (Unique Product Identifier)** | Product | "What KIND of derivative is this?" — one code per product template, reused across thousands of unrelated trades |
| **UTI (Unique Transaction Identifier)** | Trade | "WHICH specific transaction is this?" — one code per trade, generated fresh every time |

```text
WHERE UPI AND UTI SIT — RESTATED FROM MODULE 2 §12
========================================================================

  ASSET CLASS -&gt; PRODUCT -&gt; [no instrument-level ID for OTC derivatives] -&gt; TRADE
                      |                                                          |
                      v                                                          v
              UPI identifies                                        UTI identifies
              the PRODUCT TEMPLATE                                  ONE TRANSACTION
              (reused across trades)                                 (new every time)

  Both are GLOBAL standards -- not firm-specific, not jurisdiction-
  specific -- built specifically so a US regulator, an EU regulator,
  and a UK regulator looking at the SAME cross-border trade all see
  the SAME UPI and the SAME UTI on their respective reports.
```

**Takeaway:** UPI and UTI aren't two flavors of the same idea — they solve two genuinely different aggregation problems. UPI lets a regulator ask "how much of THIS PRODUCT exists market-wide" without reading every firm's contract terms; UTI lets a regulator (or a Trade Repository) ask "did both counterparties report the SAME TRADE consistently" — exactly the pairing/reconciliation mechanism [Module 14](./module-14-regulatory-reporting.md) §3 already introduced for EMIR.

---

## 2. UTI Deep Dive — Structure, Generation Waterfall &amp; Worked Examples

**Definition:** a UTI is a single alphanumeric code, **up to 52 characters**, structured as a **20-character LEI-based prefix** (identifying the generating entity) followed by **up to 32 additional characters** the generator assigns as a unique suffix. This structure comes from CPMI-IOSCO's 2017 "Technical Guidance: Harmonisation of the Unique Transaction Identifier" — the global standard every major jurisdiction's UTI rules converged on.

```text
UTI ANATOMY — WORKED EXAMPLE
========================================================================

  Bank Of Toronto's LEI (illustrative, Module 4 §6): 5493001KJTIIGC8Y1R12
                                                       (exactly 20 characters)

  If Bank Of Toronto is the UTI-generating entity for a trade executed
  17-Aug-2026, it might assign the suffix "2026081700001" (its own
  internal sequencing scheme -- the generator has full discretion over
  the suffix's content, as long as it's unique):

  Full UTI:  5493001KJTIIGC8Y1R122026081700001
             \__________20 chars_______/\____14 chars____/
              Bank Of Toronto's LEI       Bank Of Toronto's own
              (identifies WHO generated    unique suffix (identifies
              this UTI)                    WHICH trade)

  -&gt; 34 characters total here, well under the 52-character maximum --
     the suffix length is up to the generator, not fixed at 32.
```

### Who generates the UTI — the waterfall

**Definition:** because a UTI must be identical on both counterparties' independently-submitted reports for a Trade Repository to pair them ([Module 14](./module-14-regulatory-reporting.md) §3), regulators needed a rule for deciding WHICH of the two parties generates it and shares it with the other before either party's reporting deadline. This is commonly called the **UTI generation waterfall** — a hierarchy of "if this applies, this entity generates; otherwise, check the next rule."

| Priority | Rule (illustrative, simplified — see Section 8's callout) | Example |
|---|---|---|
| 1 | If the trade is executed on a trading platform/venue that generates UTIs, the **venue** generates it | An exchange-like OTC platform assigns the UTI at execution |
| 2 | If the trade is (or becomes) centrally cleared, the **CCP** generates the UTI for the cleared leg(s) | [Module 8](../1-capital-markets/module-08-rates.md)'s 5Y CAD IRS, novated to LCH SwapClear — LCH SwapClear generates the UTI once cleared |
| 3 | Otherwise, a hierarchy among the counterparties applies (e.g. clearing member &gt; swap dealer/major participant &gt; financial counterparty &gt; non-financial counterparty, per the applicable regime's specific rule) | Two dealers of the same seniority tier trading bilaterally |
| 4 | If still tied, a simple tie-break (e.g. whichever counterparty's LEI sorts first alphabetically) decides | See the worked example below |

> **Callout — this is a simplified illustration, not the literal current rule text.** The exact waterfall differs in small but legally material ways across EMIR, UK EMIR, Dodd-Frank/CFTC, and other regimes, and gets revised periodically. Treat the table above as the *shape* of the logic, and confirm the current text against Section 8's official links before encoding it into a rules engine — this is the same hedge [SFTR](./module-16-sftr-reporting.md) §7 and [Module 17 — UK DMO](./module-17-dmo-uk-reporting.md) §2 already apply to their own regulatory specifics.

```text
WORKED WATERFALL — BANK OF TORONTO'S BILATERAL CDS WITH GOLDMAN SACHS
(Module 9 §4a's Northstar Resources Inc. trade — bespoke, stays bilateral,
Module 9 §7 already established it is NOT centrally cleared)
========================================================================

  Rule 1 (venue-generated)?    NO  -- bilaterally negotiated, no platform
  Rule 2 (CCP-generated)?      NO  -- confirmed bilateral, no CCP involved
  Rule 3 (seniority hierarchy)? TIE -- both are dealers of comparable
                                       standing in this illustration
  Rule 4 (alphabetical LEI tie-break):
    Bank Of Toronto's LEI:  5493001KJTIIGC8Y1R12
    Goldman Sachs' LEI:     784F5XWPLTWKTBV3E584   (illustrative)

    "5493..." sorts BEFORE "784F..." alphanumerically
    -&gt; BANK OF TORONTO generates the UTI, and must share it with
       Goldman Sachs before either party's own reporting deadline
       (Module 14 §3's dual-sided pairing depends on this)
```

**Takeaway:** the waterfall exists to remove ambiguity, not preference — neither counterparty "wants" to generate the UTI (it's just an administrative task), but exactly one of them MUST, deterministically, so the other can simply receive and reuse the same value rather than guessing. [Module 14](./module-14-regulatory-reporting.md) §3's UTI-mismatch pairing-break scenario is precisely what happens when this hand-off fails — one firm generates its own UTI instead of using the one the waterfall's actual generator produced.

---

## 3. UPI Deep Dive — Structure, Reference Data &amp; Worked Examples Across Asset Classes

**Definition:** a UPI is a **12-character alphanumeric code** (formatted similarly to an ISIN, including a check digit) issued under the **ISO 4914** standard. Like an ISIN, the code itself is just a lookup key — the substance lives in the **UPI Reference Data** record behind it: a standardized set of fields describing the product (Asset Class, Instrument Type, Underlier, Notional Currency, and other economic terms specific to that instrument type), maintained by the DSB (Section 4).

**Critical rule, restated from [Module 2](../1-capital-markets/module-02-hierarchy.md) §12:** two trades generate the SAME UPI if and only if every product-defining field matches. Trade-specific details — notional amount, price/rate, counterparty, trade date — are deliberately excluded from what defines a UPI.

> **What's an "Underlier"?** Every worked example below has an `Underlier` field — [Module 1](../1-capital-markets/module-01-fundamentals.md) §8 defines this general concept in full (it's the reference asset/rate/index/entity a derivative's cash flows are driven by, distinct from Notional, which is just the size), including what **CORRA** (the Interest Rate template's Underlier below) actually is and how it's calculated. [Module 8](../1-capital-markets/module-08-rates.md) §1 has the full CDOR-to-CORRA transition story.

### One UPI reference-data template per asset class

Different asset classes describe "the product" using different fields, because what makes two Interest Rate Swaps the "same product" is different from what makes two CDS the "same product." Reusing trades already built elsewhere in this curriculum:

```text
FIVE ASSET CLASSES, FIVE UPI REFERENCE-DATA SHAPES
========================================================================

  INTEREST RATE  (Module 8's 5Y CAD IRS -- also Module 2 §12's example)
    Asset Class: Interest Rate | Instrument Type: Swap, Fixed-Float
    Underlier: CORRA | Notional Currency: CAD | Tenor: 5 Years
    -&gt; UPI: QZ9F4K2XR7T1 (illustrative)

  CREDIT  (Module 9 §4a's Northstar Resources Inc. CDS)
    Asset Class: Credit | Instrument Type: Single-Name CDS
    Reference Entity: Northstar Resources Inc. | Seniority: Senior
    Unsecured | Notional Currency: USD | Tenor: 5 Years
    -&gt; UPI: RT4M8LN2WD93 (illustrative)

  FOREIGN EXCHANGE  (Module 6's 3M USD/CAD Forward)
    Asset Class: Foreign Exchange | Instrument Type: Forward, Deliverable
    Currency Pair: USD/CAD | Settlement Type: Physical
    -&gt; UPI: FX7K1PXQ0H26 (illustrative)

  EQUITY  (Module 2 §11's Total Return Swap on Apple Inc. shares)
    Asset Class: Equity | Instrument Type: Swap, Price Return
    Underlier: Apple Inc. common stock | Return Type: Price + Dividend
    -&gt; UPI: EQ3T9YBV5C48 (illustrative)

  COMMODITY  (Module 7's 1Y Brent Crude Oil Swap)
    Asset Class: Commodity | Instrument Type: Swap
    Underlier: ICE Brent | Sub-Asset: Energy, Oil | Notional Currency: USD
    -&gt; UPI: CM6X2FGL8N71 (illustrative)
```

**Takeaway:** notice the Credit template includes "Reference Entity" as a product-defining field — meaning a CDS on Northstar Resources Inc. and an otherwise-identical CDS on a different company get **different UPIs**, even though both are "5Y Single-Name CDS." This is the opposite of the Interest Rate template, where the underlier is a rate benchmark (CORRA) shared by every CAD swap of that shape regardless of counterparty — a reminder that "what counts as the same product" is itself asset-class-specific, defined field-by-field in each template the DSB maintains.

### Requesting a UPI

A firm needing a UPI for a new product shape submits the reference-data fields to the DSB (Section 4) via its platform or API. The DSB checks whether an identical reference-data combination already has a UPI on file — if so, it returns the existing code (this is *why* thousands of unrelated trades correctly share one UPI, Section 1); if not, it issues a new one. This lookup-or-create behavior is the same pattern an ISIN-issuing National Numbering Agency follows for a genuinely new security, just running against a derivatives-specific reference-data schema instead of a security's issuance terms.

---

## 4. ANNA-DSB — Who Runs This, Governance &amp; How It Actually Works

**Definition:** **ANNA (the Association of National Numbering Agencies)** is the global body that has coordinated **ISIN** issuance since the 1990s, working through each country's own National Numbering Agency (e.g., the agency issuing Canadian ISINs, a different one for German securities, and so on). In 2017, ANNA established a dedicated subsidiary, the **DSB (Derivatives Service Bureau Limited)**, specifically because OTC derivatives' near-infinite combination of underliers and terms didn't fit the National-Numbering-Agency-per-country model that works for cash securities.

```text
ANNA-DSB — ONE ORGANIZATION, TWO IDENTIFIER OUTPUTS
========================================================================

  ANNA (Association of National Numbering Agencies)
    -&gt; coordinates ISIN issuance globally, via national agencies,
       for CASH securities (Module 2 §2)
       |
       v
  DSB (Derivatives Service Bureau Limited) -- ANNA's subsidiary,
  est. 2017, purpose-built for OTC DERIVATIVES:

    OUTPUT 1 -- ISIN-for-derivatives (since 2017/2018)
      Issued specifically for EU MiFID II/MiFIR pre- and post-trade
      TRANSPARENCY requirements (Section 5) -- an OTC derivative
      gets an ISIN-shaped code from the DSB, distinct from a cash
      security's ISIN, but structurally an ISIN

    OUTPUT 2 -- UPI (since 2022)
      Issued for GLOBAL TRADE REPORTING under the FSB's 2020
      mandate (implementing CPMI-IOSCO's Technical Guidance) --
      EMIR, UK EMIR, Dodd-Frank/CFTC, and other G20 jurisdictions'
      reporting regimes all consume this SAME UPI (Section 6)
```

**Governance:** the **Financial Stability Board (FSB)** designated the DSB as the sole global UPI Service Provider, following technical guidance jointly developed by **CPMI (the Committee on Payments and Market Infrastructures)** and **IOSCO (the International Organization of Securities Commissions)** — the same standard-setters [Module 13](../1-capital-markets/module-13-parties-leis.md) §8 already introduced for their broader role overseeing global market-infrastructure standards. The DSB itself operates on a **not-for-profit, cost-recovery** basis: UPI **reference data lookups are free and publicly available** (a deliberate regulatory design choice, since the whole point is public/regulatory transparency), while firms submitting *new* product definitions for a UPI to be created may be subject to a contributory fee structure that funds the DSB's operations.

**Takeaway:** the DSB isn't a regulator itself — it's market infrastructure, operating under a mandate from standard-setters (FSB/CPMI-IOSCO) the same way [Module 1](../1-capital-markets/module-01-fundamentals.md) and [Module 13](../1-capital-markets/module-13-parties-leis.md) already distinguish infrastructure providers (CSDs, Trade Repositories) from the regulators that oversee them. Confusing "the DSB requires X" with "the FCA/CSA/SEC requires X" is a common category error — the DSB implements what the standard-setters and individual jurisdictions' regulators have mandated, it doesn't independently set the reporting obligation itself.

---

## 5. UPI vs. the DSB's Other Output — ISINs for MiFIR

**Definition:** this is the single most confusing fact about the DSB, and worth stating plainly: **the exact same underlying reference-data record can produce two different identifier outputs, for two different regimes, serving two different purposes** — an ISIN-shaped code for EU MiFIR transparency, and a UPI for global trade reporting.

```text
ONE OTC DERIVATIVE, ONE REFERENCE-DATA RECORD, TWO REGULATORY OUTPUTS
========================================================================

  Bank Of Toronto's 5Y CAD IRS with Goldman Sachs (Module 8 §4a),
  if EITHER counterparty is EU/UK MiFID-scope:

  -&gt; MiFIR pre/post-trade TRANSPARENCY (Module 14 §4's purpose --
     "did anyone behave abusively," restated for derivatives):
     the DSB issues an OTC-DERIVATIVE ISIN for this product, used
     on MiFIR transaction reports and public transparency data

  -&gt; EMIR TRADE REPORTING (Module 14 §3's purpose -- "how much
     systemic derivatives risk exists"):
     the SAME trade's product is ALSO assigned a UPI, used on the
     EMIR report submitted to the Trade Repository

  -&gt; TWO DIFFERENT DSB-ISSUED CODES, on TWO DIFFERENT REPORTS, for
     the IDENTICAL underlying product -- because MiFIR and EMIR are
     genuinely separate regimes (Module 14 §1's "one trade, five
     regimes" theme, restated at the identifier level)
```

**Takeaway:** a reporting engineer who assumes "the DSB gives one code per product" will build a system that silently drops one of two legitimately required identifiers — MiFIR-scope firms need BOTH the OTC-derivative ISIN (for transparency reporting) and the UPI (for EMIR trade reporting) on the same underlying trade, from the same source organization, because [Module 14](./module-14-regulatory-reporting.md) §1 already established that regulatory purposes don't collapse into one just because the source data does.

---

## 6. Where UPI/UTI Are Actually Used — Regime-by-Regime Map

**Definition:** not every reporting regime this curriculum has covered uses UPI and UTI the same way — some use both, some use only UTI, and some use neither. This table is the fastest way to sanity-check a reporting field.

| Regime | Uses UTI? | Uses UPI? | Notes |
|---|---|---|---|
| **EMIR / UK EMIR** ([Module 14](./module-14-regulatory-reporting.md) §3) | Yes — pairing key | Yes — mandatory since the EMIR REFIT/UPI phase-in | The regime this module's Section 2/3 worked examples are built around |
| **Dodd-Frank / CFTC** ([systems-landscape](../1-capital-markets/systems-landscape.md) §5) | Yes | Yes — CFTC adopted UPI, replacing its older internal product taxonomy | US swap data reporting to an SDR (Swap Data Repository) |
| **SFTR / UK SFTR** ([Module 16 — SFTR](./module-16-sftr-reporting.md) §6) | **Yes** — same UTI concept, same pairing purpose | **No** — SFTs use SFTR's own "Type of SFT" codes (`REPO`, `SBSC`, `SLEB`, `MGLD` — [Module 16 — SFTR](./module-16-sftr-reporting.md) §2) instead, since SFTs aren't classified through the OTC-derivative product taxonomy UPI was built for | The clean illustration that UTI and UPI are genuinely independent — a regime can need one without the other |
| **MiFID II / MiFIR** (Section 5 above) | Not typically the primary key — MiFIR transaction reports key off the transaction reference number and the OTC-derivative ISIN | **Uses the DSB's OTHER output** — an OTC-derivative ISIN, not a UPI (Section 5) | The regime most likely to be confused with UPI usage, precisely because it's the same source organization |
| **UK DMO reporting** ([Module 17 — UK DMO Reporting](./module-17-dmo-uk-reporting.md)) | No | No | Not an OTC derivative regime at all — gilts are cash securities identified by ISIN, and DMO reporting is commercial/contractual ([Module 17 — UK DMO Reporting](./module-17-dmo-uk-reporting.md) §1), not derivatives trade reporting |

**Takeaway:** "does this trade need a UPI?" and "does this trade need a UTI?" are two separate yes/no questions, exactly like [Module 14](./module-14-regulatory-reporting.md) §1's "is this trade reportable?" was five separate yes/no questions against five regimes — SFTR proves a regime can need dual-sided pairing (UTI) without needing product-template aggregation (UPI), because an SFT's "what kind of product is this" question is already answered by a much smaller, SFTR-specific code set.

---

## 7. Worked Example — One Trade's Full Identifier Stack, End to End

**Definition:** pulling every identifier this module and [Module 2](../1-capital-markets/module-02-hierarchy.md) §12 have covered onto one trade record, so the full stack can be seen assembled rather than piecemeal.

```text
BANK OF TORONTO'S 5Y CAD IRS WITH GOLDMAN SACHS — FULL IDENTIFIER STACK
========================================================================
(Module 8 §4a's trade, novated to LCH SwapClear per Module 8 §5)

  Trade ID         : BOT-2026-000514        (Bank Of Toronto's own
                                              internal reference only)

  UTI              : generated by LCH SwapClear once cleared (Section 2's
                     waterfall Rule 2 -- CCP-generated for cleared trades),
                     e.g. 549300LCHXXXXXXXXX2026081700099 (illustrative)

  UPI              : QZ9F4K2XR7T1 (illustrative -- Section 3's Interest
                     Rate template: 5Y CAD Fixed-Float Swap vs CORRA)

  Counterparty LEIs: Bank Of Toronto  5493001KJTIIGC8Y1R12
                     Goldman Sachs    784F5XWPLTWKTBV3E584 (illustrative)

  OTC-derivative ISIN (Section 5, ONLY if MiFIR-scope):
                     a separate DSB-issued ISIN-shaped code, distinct
                     from the UPI, used solely on MiFIR reports

  -&gt; FIVE different identifiers on one trade, each answering a
     different question: Trade ID (internal), UTI (which specific
     transaction, for EMIR pairing), UPI (which product template,
     for EMIR aggregation), LEIs (who is trading), OTC-derivative
     ISIN (which product, for MiFIR transparency) -- restating
     Module 4 §6's "four completely different identifiers, four
     completely different questions" pattern, now with the
     derivative-specific layer fully populated
```

**Takeaway:** no single identifier on this record is redundant with another, even though several of them superficially "identify the product" — UPI and the OTC-derivative ISIN both describe the SAME product shape but exist for two different regimes that don't share infrastructure (Section 5), while UTI and Trade ID both identify the SAME single transaction but at two different scopes (global/regulatory vs. internal/firm-specific, [Module 2](../1-capital-markets/module-02-hierarchy.md) §12's original distinction).

---

## 8. Official Sources &amp; Links

**Definition:** as with [SFTR](./module-16-sftr-reporting.md) §9 and [Module 17 — UK DMO Reporting](./module-17-dmo-uk-reporting.md) §11, this module points to primary sources rather than restating rules that get periodically revised.

| Source | What It Covers | Link |
|---|---|---|
| **ANNA-DSB** | UPI reference data lookup (free), OTC-derivative ISIN issuance, UTI-related guidance, technical onboarding | [www.anna-dsb.com](https://www.anna-dsb.com) |
| **CPMI (Committee on Payments and Market Infrastructures)** | Joint technical guidance (with IOSCO) on UTI and UPI harmonization | [www.bis.org/cpmi](https://www.bis.org/cpmi) |
| **IOSCO** | Co-author of the UTI/UPI technical guidance; global securities-regulator standard-setter | [www.iosco.org](https://www.iosco.org) |
| **FSB (Financial Stability Board)** | Designated the DSB as global UPI Service Provider; oversees the broader G20 OTC derivatives data-harmonization workstream | [www.fsb.org](https://www.fsb.org) |
| **ESMA** | EU technical standards requiring UPI under EMIR REFIT, and the OTC-derivative ISIN requirement under MiFIR | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **CFTC** | US technical standards requiring UPI/UTI under Dodd-Frank swap data reporting | [www.cftc.gov](https://www.cftc.gov) |

**Takeaway:** treat the DSB's own site as the source of truth for the literal current UPI reference-data schema and lookup tool; CPMI-IOSCO's published technical guidance as the source of truth for the UTI/UPI structural standards themselves; and each jurisdiction's own regulator (ESMA, CFTC, FCA) as the source of truth for exactly when and how that standard becomes a mandatory field on a report in that jurisdiction — the same three-way split [SFTR](./module-16-sftr-reporting.md) §9's takeaway already established for a different regime.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | UTI-generating entity in Section 2's bilateral CDS waterfall example; counterparty across every worked identifier stack |
| Goldman Sachs | Counterparty across every worked example; loses the alphabetical LEI tie-break in Section 2 |
| LCH SwapClear | CCP generating the UTI for Bank Of Toronto's cleared IRS (Section 2, Section 7) |
| ANNA (Association of National Numbering Agencies) | Global ISIN-coordination body; DSB's parent organization (Section 4) |
| DSB (Derivatives Service Bureau) | Issues both UPI (global trade reporting) and OTC-derivative ISINs (MiFIR transparency) — Sections 3–5 |
| FSB / CPMI-IOSCO | Standard-setters whose mandate the DSB operates under (Section 4) |
| ESMA / CFTC | Jurisdiction-specific regulators mandating UPI/UTI/OTC-ISIN fields on their own reporting regimes (Section 6) |

---

## Recap Quiz

1. Using Section 1, why can't a derivative simply reuse the ISIN system the way a bond or a share does?
2. Using Section 2, decompose the illustrative UTI `5493001KJTIIGC8Y1R122026081700001` into its two structural parts, and state what each part identifies.
3. Using Section 2's waterfall, why does Bank Of Toronto — not Goldman Sachs — generate the UTI in the bilateral Northstar Resources Inc. CDS example, and what would change if that same trade were instead centrally cleared?
4. Using Section 3, would a CDS on Northstar Resources Inc. and an otherwise-identical CDS on a completely different company ever share a UPI? Why or why not, contrasted with the Interest Rate template's behavior?
5. Using Section 4, is the DSB a regulator? If not, what is it, and who actually mandates that firms use its identifiers?
6. Using Section 5, for a MiFID-scope OTC interest rate swap, how many DSB-issued identifiers does the trade's product actually need, and what does each one get used for?
7. Using Section 6, does SFTR require a UPI on its reports? Does it require a UTI? Explain the difference using SFTR's own product-classification approach.
8. Using Section 7, why isn't the OTC-derivative ISIN redundant with the UPI, even though both describe the same underlying product?

---

## Answers — Recap Quiz, with Explanations

**1. Because an ISIN identifies a security that exists independently of any one trade (a share or bond keeps existing, held by whoever currently owns it) — a derivative has no existence independent of the two parties' contract ([Module 2](../1-capital-markets/module-02-hierarchy.md) §12), so there's no single persistent "instrument" object for an ISIN-style code to attach to the way it does for a cash security.**

Section 1 states this directly, tracing it back to the same structural point [Module 2](../1-capital-markets/module-02-hierarchy.md) §12 made about why UPI has to sit at the *product* layer instead — one rung higher than where an ISIN sits for cash instruments.

**2. `5493001KJTIIGC8Y1R12` (the first 20 characters) is the LEI of the generating entity — Bank Of Toronto in this example. `2026081700001` (the remaining characters) is the generator's own unique suffix, identifying this one specific trade.**

Section 2's "UTI Anatomy" diagram states this exact split — the LEI-based prefix answers "who generated this UTI," and the suffix (content fully at the generator's discretion, as long as it's unique) answers "which trade."

**3. Because the trade is bilateral (not cleared, not venue-executed), so the waterfall falls through Rules 1 and 2 to Rule 3 (a tie, since both are dealers of comparable seniority in this illustration) and then Rule 4's alphabetical LEI tie-break — Bank Of Toronto's LEI (`5493...`) sorts before Goldman Sachs' (`784F...`). If the same trade were instead centrally cleared, Rule 2 would apply first and the CCP itself would generate the UTI, exactly as already happens for Bank Of Toronto's 5Y CAD IRS once it's novated to LCH SwapClear (Section 7).**

Section 2's worked waterfall example walks through exactly this elimination, rule by rule, and explicitly notes the CCP-generation case is the same mechanic already established for the cleared IRS.

**4. No — they would NOT share a UPI, because Section 3's Credit reference-data template includes "Reference Entity" as one of the product-defining fields, so a CDS on a different company fails the "every product-defining field matches" test from the start. This contrasts with the Interest Rate template, where the underlier is a shared rate benchmark (CORRA) rather than a specific company, so many unrelated counterparties' trades on that same benchmark DO share one UPI.**

Section 3's takeaway states this explicitly: "what counts as the same product" is asset-class-specific, defined field-by-field in each template — Credit's template makes the reference entity part of the product identity itself, while Interest Rate's template doesn't tie the product to any one counterparty-specific entity at all.

**5. No — the DSB is market infrastructure, not a regulator. It operates under a mandate from the FSB (which designated it the global UPI Service Provider) and technical guidance from CPMI-IOSCO; individual jurisdictions' own regulators (ESMA, CFTC, the FCA, etc.) are the ones who actually make using a DSB-issued identifier a mandatory field on a report in their jurisdiction.**

Section 4's takeaway states this directly, drawing the same infrastructure-vs-regulator distinction [Module 1](../1-capital-markets/module-01-fundamentals.md) and [Module 13](../1-capital-markets/module-13-parties-leis.md) §8 already established for CSDs and Trade Repositories — confusing "the DSB requires X" with "a regulator requires X" is flagged as a common category error.

**6. Two — a UPI (for EMIR trade reporting, if either counterparty is EU/UK-scope) and a separate OTC-derivative ISIN (for MiFIR pre/post-trade transparency reporting), both issued by the DSB from the same underlying reference-data record but serving two entirely different regimes.**

Section 5's diagram states this precisely: "TWO DIFFERENT DSB-ISSUED CODES, on TWO DIFFERENT REPORTS, for the IDENTICAL underlying product" — a reporting engineer who assumes one DSB code per product will silently under-report one of the two legitimately required identifiers.

**7. SFTR requires a UTI (for the same dual-sided pairing purpose EMIR uses it for) but does NOT require a UPI — SFTs are classified instead through SFTR's own "Type of SFT" codes (`REPO`, `SBSC`, `SLEB`, `MGLD`), since securities financing transactions aren't run through the OTC-derivative product taxonomy UPI was built to describe.**

Section 6's table states this directly, flagging SFTR as "the clean illustration that UTI and UPI are genuinely independent — a regime can need one without the other," cross-referencing [Module 16 — SFTR](./module-16-sftr-reporting.md) §2's own SFT-type-code table.

**8. Because they exist for two structurally separate regimes that don't share reporting infrastructure — EMIR (which wants a UPI, submitted to a Trade Repository) and MiFIR (which wants an OTC-derivative ISIN, submitted for pre/post-trade transparency) — so a MiFID-scope firm genuinely needs BOTH codes on the SAME trade, one for each regime's own report, not as a duplicate but as two separate mandatory fields.**

Section 7's takeaway restates Section 5's point in the context of the full identifier stack: no identifier on the worked trade record is truly redundant with another, even the two that superficially describe "the same product," because each answers to a different regime's own reporting pipeline.

---

[← Back to Index](../../index.md)
