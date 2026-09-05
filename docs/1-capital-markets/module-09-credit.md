# Module 9 — Credit

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Asset Class Roadmap 6 of 6** — Equity → Fixed Income → FX → Commodities → Rates → **Credit**. This closes the roadmap.
> Work data model: this module maps to the taxonomy's **"6. Credit"** node — one product, Credit Default Swap, with its own Trade + Valuation Avro schema.

---

## 1. What Is Credit? — Theory &amp; Terminology

**Definition:** Credit, as its own asset class, is about trading **default risk itself** — the risk that a specific borrower (a "Reference Entity") fails to pay what it owes — separately from owning that borrower's actual debt. [Module 5](./module-05-fixed-income.md) introduced credit risk as one *feature* of a bond (a corporate bond carries credit risk a government bond doesn't, [Module 5](./module-05-fixed-income.md) §2); this module is about a product built to transfer that risk on its own, without anyone buying or selling the underlying bond at all. The core product is the **Credit Default Swap (CDS)** — economically closest to insurance: one party pays a periodic premium, and the other pays out if a defined bad event happens to the Reference Entity.

| Term | Meaning |
|---|---|
| Reference Entity | The specific company or sovereign whose default risk the CDS references — e.g. a corporate borrower |
| Reference Obligation | A specific bond issued by the Reference Entity, used to define what counts as "the debt" for settlement purposes |
| Credit Event | A defined trigger (default, bankruptcy, or — in some contracts — restructuring) that causes the CDS to pay out |
| Running Coupon | The periodic premium the protection buyer pays — since 2009's ISDA "Big Bang" Protocol, standardized to fixed levels (typically 100bps or 500bps) rather than freely quoted |
| Upfront Payment | A one-time payment, in either direction, that reconciles the gap between the market's actual credit spread and the standardized Running Coupon (Section 4) |
| Recovery Rate | The fraction of face value creditors are expected to recover after a default — directly determines how much a CDS pays out |

```text
CDS AS INSURANCE — the core mental model
========================================================================

  Protection BUYER (Bank Of Toronto):
    Pays a periodic Running Coupon (like an insurance premium)
    -> in exchange for protection against the Reference Entity
       defaulting

  Protection SELLER (Goldman Sachs):
    Receives the Running Coupon
    -> pays out a large amount ONLY IF a Credit Event happens
       (like an insurer paying a claim)

  -&gt; Critically: Bank Of Toronto does NOT need to own any bond
     issued by the Reference Entity to buy this protection -- a
     "naked" CDS position is possible, unlike home insurance,
     where you generally must own the home to insure it.
```

**Takeaway:** the insurance analogy is useful but has one famous, important limit — you can buy CDS protection on a company's debt without owning any of that debt, a structural difference from real insurance that made CDS central to the 2008 financial crisis (large protection sellers, like AIG, owed far more in potential payouts than they held in reserves) and is exactly why Section 8's regulatory response was so significant.

---

## 2. What Are The Products? — Single-Name CDS and Index CDS

```text
CREDIT
│
└── Credit Default Swap (CDS)
    ├── Single-Name CDS      (references ONE Reference Entity)
    └── CDS Index            (references a BASKET of Reference Entities,
                               e.g. CDX, iTraxx — same mechanic, many names)
```

| Product | What it is |
|---|---|
| **Single-Name CDS** | Protection on one specific Reference Entity's default risk — the worked example in Section 4 |
| **CDS Index** | A standardized basket of single-name CDS (e.g. **CDX** in North America, **iTraxx** in Europe) traded as one instrument — economically a portfolio of the same mechanic, priced and cleared as a single product |

**Takeaway:** Single-Name and Index CDS share the same underlying mechanic, with "how many Reference Entities" being the real distinction rather than a different product type. This module's worked example uses Single-Name CDS throughout, since the mechanic is identical either way — just multiplied across more names for an Index.

---

## 3. What Exactly Is Being Traded? — A Pure Contract, With One Familiar Exception

**Definition:** a CDS is a contract, not a security — nobody transfers ownership of anything at trade date, the same "contracts all the way down" pattern [Module 6](./module-06-fx.md) §3 established for FX and [Module 8](./module-08-rates.md) §3 established for four of Rates' five products. But exactly like [Module 8](./module-08-rates.md) §3's IR Forward Debt, a CDS's Reference Obligation is a real bond, with a real ISIN, that the contract's settlement mechanics point back to.

```text
CDS (CONTRACT)  vs  REFERENCE OBLIGATION (SECURITY IT POINTS TO)
========================================================================

  Credit Default Swap (CONTRACT):
    Bank Of Toronto and Goldman Sachs create this contract at trade
    date. It has no existence before the trade, and (barring a
    credit event) ceases to exist at its maturity date. Nothing
    about the Reference Entity's actual debt changes hands.

  Reference Obligation (SECURITY):
    A specific bond issued by the Reference Entity -- e.g. a
    Northstar Resources Inc. 6% Bond 2031 -- named in the CDS
    contract purely to define what "the debt" means for settlement
    (Section 5's credit-event auction references THIS obligation's
    market price, post-default, to determine the payout).

  -&gt; The CDS itself never delivers this bond in modern practice
     (Section 5's cash-settlement-via-auction convention replaced
     the older physical-settlement norm after the 2009 Big Bang
     Protocol) -- but the bond's ISIN still appears on the CDS
     trade record as a reference field (Section 06).
```

**Takeaway:** this is the second product family in the roadmap (after [Module 8](./module-08-rates.md)'s IR Forward Debt) where a genuinely "contract, not security" product legitimately carries a real ISIN on its trade record — for the same reason in both cases: the contract's settlement mechanics are defined in terms of a specific, real security, even though the contract itself is what's actually traded.

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

> **Complete worked example available.** See [Credit Default Swap (CDS) — Complete Example](../../examples/Credit_Default_Swap_CDS_Complete_Example.md) ([HTML](../../examples/Credit_Default_Swap_CDS_Complete_Example.html)) for the full Northstar Resources Inc. trade — Running Coupon, Upfront Payment, MTM, and a Credit Event payout — through the complete trade lifecycle.

### 4a. Single-Name CDS — Trade Inception

```text
SINGLE-NAME CDS  —  5Y Protection on Northstar Resources Inc.
========================================================================

  Protection Buyer:  Bank Of Toronto
  Protection Seller: Goldman Sachs

  Reference Entity: Northstar Resources Inc. (fictional corporate)
  Reference Obligation: Northstar Resources Inc. 6% Bond 2031
  Notional: USD 10,000,000
  Term: 5 years
  Standardized Running Coupon: 100bps (1.00%), paid quarterly
  Market-quoted credit spread today: 250bps (2.50%)

  Because the market spread (2.50%) is well above the standardized
  coupon (1.00%), Bank Of Toronto must pay an UPFRONT amount to
  Goldman Sachs to compensate for the below-market running coupon
  it'll actually receive going forward (a full ISDA CDS Standard
  Model calculation is beyond this module's scope; the commonly
  used approximation is shown for intuition):

  Approx. Upfront = (Market Spread - Running Coupon) x Risky Duration x Notional
                   = (2.50% - 1.00%) x ~4.5 x 10,000,000
                   = 1.50% x 4.5 x 10,000,000
                   = USD 675,000 (paid by Bank Of Toronto at inception)

  Quarterly Running Coupon payment thereafter:
    10,000,000 x 1.00% x 91/360 (CDS convention is act/360, unlike
    the act/365 convention Module 8 used for CORRA) = USD 2,527.78
```

### 4b. Single-Name CDS — Credit Event, Two Years Later

```text
CREDIT EVENT  —  Northstar Resources Inc. defaults
========================================================================

  Trigger: Northstar Resources Inc. misses a bond coupon payment
  and files for bankruptcy protection

  ISDA Determinations Committee (Section 08) formally rules a
  "Credit Event" has occurred -- this is a genuinely different
  kind of body from any regulator in this roadmap so far: an
  INDUSTRY body, not a government regulator, that decides the
  single most consequential fact in this whole trade's lifecycle

  CREDIT EVENT AUCTION determines the Reference Obligation's
  post-default market price: settles at 35 (i.e. 35% of face value
  -- bondholders expected to recover roughly 35 cents on the dollar)

  Cash Settlement (the modern, standard mechanic post-2009 Big
  Bang Protocol -- NOT physical delivery of the defaulted bond):
    Payout = Notional x (100% - Recovery Rate)
           = 10,000,000 x (100% - 35%)
           = 10,000,000 x 65%
           = USD 6,500,000

  Goldman Sachs (protection seller) PAYS Bank Of Toronto (protection
  buyer) USD 6,500,000 -- the CDS's entire purpose realized: Bank Of
  Toronto is made economically whole for a USD 10,000,000 exposure
  to Northstar Resources Inc. it may never have even held as a bond,
  receiving 65% of face value in cash rather than a defaulted bond
  worth only 35 cents on the dollar.
```

**Takeaway:** the credit-event auction mechanic is Credit's single most distinctive lifecycle event — nothing in Equity, Fixed Income, FX, Commodities, or Rates has an equivalent "an industry committee formally declares a defined bad thing happened, then an auction sets the payout price" sequence. Everything before Section 4b (Sections 1–3, and 4a) is standard contract mechanics; a Credit Event is where Credit genuinely diverges from every other asset class in this roadmap.

---

## 5. Trade Lifecycle — Credit-Specific Walkthrough

Applying **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** to the CDS from Section 4a, through to the Credit Event in Section 4b.

```text
5Y CDS ON NORTHSTAR RESOURCES INC.  —  FULL LIFECYCLE
========================================================================

  YEAR 0
  10:40:00  ORDER — Bank Of Toronto's credit desk instructed to buy
            5Y protection, USD 10,000,000, Northstar Resources Inc.

  10:40:15  EXECUTION @ 250bps market spread vs Goldman Sachs

  10:40:15  TRADE CAPTURE — Trade ID BOT-CDS-2026-041127
            Upfront payment calculated: USD 675,000 (Section 4a)

  10:55:00  CONFIRMATION/MATCHING -&gt; MATCHED

  CLEARING — for a standardized single-name or index CDS, novated
            to a CCP (ICE Clear Credit or LCH CDSClear) under a
            clearing mandate that parallels EMIR/Dodd-Frank's
            Rates mandate (Module 8 §07-08) but scoped specifically
            to CDS index products and the most liquid single names

  QUARTERLY RUNNING COUPON — USD 2,527.78 paid by Bank Of Toronto
            to Goldman Sachs each quarter, for as long as the trade
            survives without a Credit Event (Section 4a)

  YEAR 2
            CREDIT EVENT — Northstar Resources Inc. defaults
            (Section 4b)

            ISDA DETERMINATIONS COMMITTEE rules the event is a
            valid Credit Event under the contract's terms

            CREDIT EVENT AUCTION sets the Reference Obligation's
            settlement price at 35

  SETTLEMENT — Goldman Sachs pays Bank Of Toronto USD 6,500,000
            cash (Section 4b); the CDS contract TERMINATES --
            unlike an IRS (Module 8 §5), which runs its full term
            regardless of what happens to interest rates, a CDS
            can end early, permanently, the moment its one defined
            trigger event occurs

  REPORTING — trade reported to a Trade Repository (DTCC GTR)
            throughout its life, with a final settlement/
            termination event reported once the Credit Event
            auction concludes
```

**Takeaway:** every other product in this roadmap either runs to its stated maturity or is closed out/rolled voluntarily ([Module 7](./module-07-commodities.md) §4b's futures roll). A CDS is the first product where an *external event outside either counterparty's control* can end the trade early — the Reference Entity's own solvency, not a market price move, is what terminates it.

---

## 6. Identifiers — UPI, UTI, LEI (for TWO different roles), and a Reference Obligation ISIN

CDS reuses the by-now-familiar OTC derivative identifier set, but with a genuine twist: **LEI shows up in two structurally different roles on the same trade record.**

| Identifier | Applies to CDS? | Example |
|---|---|---|
| ISIN | **No**, for the CDS contract itself — but **yes**, for the Reference Obligation it points to (Section 03) | `—` for the CDS; a real ISIN for the Northstar Resources Inc. 6% Bond 2031 |
| **UPI** | Yes, as an OTC derivative product template ([Module 2](./module-02-hierarchy.md) §12) | One UPI for "5Y USD Single-Name CDS, Northstar Resources Inc." |
| **UTI** | Yes — every individual CDS trade gets its own | A fresh UTI per trade |
| **LEI — Counterparty role** | Yes — identifies Bank Of Toronto and Goldman Sachs as the two trading parties | Same role LEI plays in every prior module ([Module 4](./module-04-equity.md) §6) |
| **LEI — Reference Entity role** | Yes, and this is new: Northstar Resources Inc. — the company whose *default risk* is being traded — also carries its own LEI, populated as a completely separate field from the counterparty LEIs | Identifies WHAT the contract references, not WHO is trading it |

```text
LEI'S TWO ROLES ON ONE CDS TRADE RECORD
========================================================================

  Counterparty LEI (Buyer)  : identifies Bank Of Toronto
  Counterparty LEI (Seller) : identifies Goldman Sachs
  Reference Entity LEI      : identifies Northstar Resources Inc.

  -&gt; every prior module's LEI only ever answered "WHO is trading
     this?" (Module 4 §6). Credit is the first product where LEI
     ALSO answers "WHAT is the contract about?" -- a role every
     other module gave to an instrument identifier (ISIN, Ticker,
     currency pair) instead.
```

**Takeaway:** a CDS trade record with three populated LEI fields and one populated ISIN field (for the Reference Obligation) isn't over-populated or wrong — it's the fullest expression yet in this roadmap of how many distinct "who" and "what" questions one OTC derivative trade record needs to answer at once.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** Credit follows the exact structural pattern [Module 8](./module-08-rates.md) §7 established for Rates — OTC-negotiated by default, with the most standardized instruments (CDS Indices, and the most liquid single names) pulled into mandatory central clearing, while bespoke single names stay bilateral.

| Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|
| CDS Index (CDX, iTraxx) | OTC-negotiated | **Mandatorily** centrally cleared (ICE Clear Credit, LCH CDSClear) for the most liquid indices, under an EMIR/Dodd-Frank-style clearing mandate |
| Single-Name CDS (liquid, standardized names) | OTC-negotiated | Increasingly centrally cleared |
| Single-Name CDS (bespoke/illiquid Reference Entity) | OTC-negotiated | Bilateral (the norm) — the Northstar Resources Inc. trade in Section 4 is written as this case |

**Takeaway:** this is the third module in a row (after Rates, [Module 8](./module-08-rates.md) §7, and echoing Commodities, [Module 7](./module-07-commodities.md) §7) where the same underlying pattern repeats — one standardized slice of the asset class carries the central-clearing volume, while a long tail of bespoke, illiquid instruments stays bilateral OTC. By this point in the roadmap, that pattern itself is worth recognizing as a general truth about OTC derivatives markets, not a coincidence specific to any one asset class.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | How it touches Credit specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Trading-venue and transparency rules for standardized CDS, similar in shape to its Rates treatment ([Module 8](./module-08-rates.md) §08) |
| **EMIR** | Mandates central clearing for the most liquid CDS Indices specifically (e.g. iTraxx Europe Main and Crossover) — a narrower clearing mandate than Rates' broader IRS mandate ([Module 8](./module-08-rates.md) §08), reflecting how much less standardized most single-name CDS remain |
| **SFTR** | Not applicable — a CDS is a derivative, not a securities financing transaction |
| **FCA** (UK) | UK conduct regulator for CDS trading and clearing activity |
| **CIRO** (Canada) | SRO overseeing Canadian credit derivatives dealer conduct |
| **HKMA** (Hong Kong) | Runs Hong Kong's OTC derivatives reporting regime for CDS alongside Rates ([Module 8](./module-08-rates.md) §08), under the same Securities and Futures Ordinance framework |
| **CFTC / SEC** (US) — new to this module | The **first split jurisdiction** in this roadmap: under Dodd-Frank, broad-based CDS Indices are regulated by the **CFTC** as "swaps" (the same regulator covering Rates and Commodities), while **single-name CDS and narrow-based indices are "security-based swaps," regulated instead by the SEC** — a genuine US jurisdictional fork this roadmap hasn't seen in any other asset class |
| **ISDA Determinations Committees** — new to this module | Not a government regulator at all, but an **industry self-governance body** (Section 4b, Section 5) whose ruling on whether a Credit Event occurred is the single most consequential decision in a CDS's entire lifecycle — worth including here precisely because it doesn't fit the "acronym + jurisdiction" pattern every other row in this table follows |
| **DTCC** | Its GTR is a major trade repository for CDS reporting, same infrastructure role as [Module 8](./module-08-rates.md) §08 |
| **DMO** (UK Debt Management Office) | Not Credit-relevant in the way it was for Rates (IR Forward Debt on Gilts, [Module 8](./module-08-rates.md) §08) — sovereign CDS exists (protection on a government's own default risk) but isn't this module's worked example; included here for completeness |

**Takeaway:** two genuinely new regulatory concepts appear for the first time in this roadmap's final module — a split CFTC/SEC jurisdiction inside a single US regulatory framework, and an industry body (ISDA's Determinations Committees) making the single most consequential call in the entire trade lifecycle. Both exist specifically because of what makes Credit different from every prior asset class: a contract whose entire purpose is to trigger on a *discrete, disputable event* (did a default actually happen, and under whose rules?) rather than settling against a continuously observable market price.

---

## 9. Canadian Systems Landscape — Credit Front, Middle &amp; Back Office to Regulatory Reporting

**Definition:** [systems-landscape](./systems-landscape.md) §2 laid out the *generic* Front/Middle/Back Office architecture for Canada. This section specializes that architecture to Credit specifically — the actual systems a Canadian bank's CDS desk connects through, from the Section 4a trade capture of the Northstar Resources Inc. CDS all the way to Canadian regulatory reporting.

> **Callout — illustrative, not a claim about any specific bank's stack.** The platform names below (MarkitWire/ICE Link, Calypso/Murex, ICE Clear Credit, DTCC's Trade Information Warehouse) are representative of the real market infrastructure Canadian dealers actually connect to for CDS — the same "illustrative vendor family" convention [systems-landscape](./systems-landscape.md) already uses for Murex/Summit. Treat them as the *category* of system a reporting pipeline needs to integrate with, not as an assertion about Bank Of Toronto's own internal build.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | MarkitWire / ICE Link, Bloomberg | Trade capture and execution workflow for the CDS itself; Bloomberg for pricing/quoting the market spread (Section 4a's 250bps) |
| **Middle Office** | MarkitWire / ICE Link (again), Calypso / Murex | CDS is unusually compressed here: the SAME MarkitWire/ICE Link platform that captured the trade also handles confirmation/affirmation — a genuine contrast with [Module 8](./module-08-rates.md)'s IRS, where front-office execution and middle-office confirmation more commonly sit on separate platforms. Calypso/Murex handle ongoing valuation and risk (upfront/running-coupon calculations, Section 4a) |
| **Back Office / Clearing** | ICE Clear Credit (if centrally cleared, Section 7) *or* bilateral settlement (if not) — DTCC's **Trade Information Warehouse (TIW)** either way | Section 7 already classified the bespoke Northstar Resources Inc. single-name CDS as staying **bilateral** — so ICE Clear Credit never enters this specific trade's path. Regardless of cleared/bilateral status, DTCC's TIW holds the official post-trade lifecycle record (running coupons, the Section 4b Credit Event, final settlement) for the large majority of CDS trades globally |
| **Regulator / Trade Repository** | CSA **Multilateral Instrument 96-101**, reported to a CSA-designated Trade Repository (e.g. DTCC Data Repository (Canada) Ltd — illustrative, same naming pattern as the [SFTR](../2-reporting/module-16-sftr-reporting.md)/[DMO](../2-reporting/module-17-dmo-uk-reporting.md) modules' Canadian TR references); **CIRO** for dealer conduct; **Bank of Canada** for systemic oversight, given CDS's own role as a 2008 contagion channel (Section 1) |

```text
CANADA — CREDIT (CDS) SYSTEMS CHAIN, THE NORTHSTAR RESOURCES INC. TRADE
========================================================================

  FRONT OFFICE
    Bloomberg (price discovery, 250bps market spread)
    MarkitWire / ICE Link (trade capture -- Trade ID BOT-CDS-2026-041127)
         |
         v
  MIDDLE OFFICE
    MarkitWire / ICE Link (confirmation/affirmation -- MATCHED,
      Section 05's 10:55:00 timestamp)
    Calypso / Murex (upfront payment calc: USD 675,000; ongoing
      running-coupon and MTM valuation)
         |
         v
  BACK OFFICE
    Section 07: bespoke single-name CDS -> stays BILATERAL,
      no CCP novation (contrast: a CDX/iTraxx index trade would
      route here to ICE Clear Credit instead)
    DTCC Trade Information Warehouse (TIW) -- holds the lifecycle
      record: running coupons, the Year-2 Credit Event, the
      USD 6,500,000 cash settlement (Section 04b)
         |
         v
  REGULATORY / TRADE REPOSITORY
    CSA Multilateral Instrument 96-101 -- OTC derivatives trade
      reporting obligation
    -> reported to a CSA-designated Trade Repository (e.g. DTCC
       Data Repository (Canada) Ltd)
    CIRO -- Bank Of Toronto's dealer conduct as a CDS counterparty
    Bank of Canada -- systemic oversight (Section 01's 2008
      contagion history is exactly why this oversight exists)
```

**Takeaway:** the Northstar Resources Inc. trade's own Section 7 classification — bespoke, bilateral, not centrally cleared — directly determines which Back Office system actually touches it: DTCC's TIW records its lifecycle regardless, but ICE Clear Credit only enters the picture for the standardized CDS Index/liquid single-name slice of the market. A reporting pipeline that assumes every CDS routes through a CCP would misroute exactly the trade this module used as its worked example.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Protection buyer on the Northstar Resources Inc. CDS |
| Goldman Sachs | Protection seller on the Northstar Resources Inc. CDS |
| Northstar Resources Inc. | The Reference Entity (fictional corporate) — not a counterparty to the trade at all, just the subject of it |
| ISDA Determinations Committee | Industry body ruling on whether a Credit Event occurred |
| ICE Clear Credit / LCH CDSClear | CCPs performing central clearing for standardized CDS Indices and liquid single names |
| CFTC / SEC | Split US regulators — swaps vs. security-based swaps |
| DTCC Trade Information Warehouse (TIW) | Holds the official post-trade lifecycle record for the Northstar Resources Inc. CDS (Section 9) |
| CIRO | SRO overseeing Bank Of Toronto's dealer conduct in the Canadian credit derivatives market (Section 9) |
| Bank of Canada | Systemic oversight of CDS activity touching Canadian dealers (Section 9) |

---

## Recap Quiz — Numbers Edition

1. On the CDS in Section 4a, if the market-quoted spread had instead been 60bps (below the 100bps standardized Running Coupon), would Bank Of Toronto pay or receive the upfront amount, and roughly how much (using the same approximation formula and Risky Duration of ~4.5)?
2. Why does Section 4b's Credit Event payout use a Recovery Rate of 35% rather than assuming Bank Of Toronto simply loses the entire USD 10,000,000 notional?
3. Using Section 3, is it correct to say a CDS trade record should NEVER carry an ISIN? Why or why not?
4. On the LEI discussion in Section 6, if a single trade record shows the SAME LEI populated in both the "Counterparty (Seller)" field and the "Reference Entity" field, what real-world scenario would that represent, and is it a data error?
5. Why is the ISDA Determinations Committee described in Section 8 as not fitting "the acronym + jurisdiction pattern" of every other row in that table?
6. Contrast the US CFTC/SEC jurisdictional split for Credit (Section 8) with the single-regulator (CFTC-only) pattern [Module 8](./module-08-rates.md) §08 described for Rates — what specifically causes CDS to split where IRS doesn't?
7. Why does Section 7 call the OTC-default-with-a-mandatorily-cleared-standardized-slice pattern "worth recognizing as a general truth" rather than a coincidence specific to Credit?
8. Using Section 9, why does ICE Clear Credit never touch the Northstar Resources Inc. CDS from Section 4, even though it's listed as one of the Back Office systems in Section 9's table?

Answer whenever ready — this closes the **Asset Class Roadmap**. Modules 10-14 (Valuation, Risk &amp; Exposure, Collateral &amp; Margin, Parties/LEIs, Regulatory Reporting) and [Module 15](./module-15-listed-derivatives.md) (Listed Derivatives) build on all six asset classes covered so far.

---

## Answers — Recap Quiz, with Explanations

**1. Bank Of Toronto would RECEIVE the upfront amount, roughly USD 180,000.**

```text
Approx. Upfront = (Market Spread - Running Coupon) x Risky Duration x Notional
                 = (0.60% - 1.00%) x 4.5 x 10,000,000
                 = -0.40% x 4.5 x 10,000,000
                 = -USD 180,000 (negative = Bank Of Toronto RECEIVES)
```

When the market spread is BELOW the standardized Running Coupon, Bank Of Toronto would be overpaying relative to the market's actual pricing of Northstar Resources Inc.'s credit risk every quarter — so Goldman Sachs compensates Bank Of Toronto upfront for locking into that above-market coupon, the mirror image of Section 4a's worked example.

**2. Because a Credit Event triggers a Credit Event Auction that determines the Reference Obligation's actual post-default market price (35% of face value in Section 4b) — bondholders/creditors of a defaulted company typically still recover SOMETHING through bankruptcy proceedings, so the CDS payout is calibrated to make the protection buyer whole for the ACTUAL loss (65%), not an assumed total loss (100%).**

Section 4b's payout formula, `Notional x (100% - Recovery Rate)`, only reduces to a full notional payout in the extreme case of a 0% Recovery Rate — the auction mechanism exists specifically to discover what recovery rate applies to this particular default, rather than assuming the worst case by default.

**3. No — it's not correct to say a CDS trade record should never carry an ISIN. Section 03 establishes that while the CDS contract itself has no ISIN, the Reference Obligation it names (a specific bond issued by the Reference Entity) is a real security and legitimately carries its own ISIN as a reference field on the CDS trade record.**

This is exactly the same nuance [Module 8](./module-08-rates.md) §6 established for IR Forward Debt: "no ISIN for the contract itself, but yes for the real security the contract's mechanics point to" is a recurring, healthy pattern for contracts that reference — without necessarily delivering — an actual security, not a universal "derivatives never have ISINs" rule.

**4. That would represent Goldman Sachs having sold protection on ITS OWN default risk — Goldman Sachs is simultaneously the protection-selling counterparty AND the Reference Entity. This is a real (if unusual and risky) scenario, not automatically a data error, though it would typically trigger enhanced scrutiny given the obvious conflict-of-interest and correlation risk (if Goldman Sachs itself defaults, it's simultaneously unable to pay out AND the very trigger event).**

Section 6's two-roles explanation makes clear that Counterparty LEI and Reference Entity LEI are populated independently and can, in principle, coincide — the systems don't structurally prevent it, even though a risk or compliance function would likely flag it given the concentrated risk it represents (this specific risk pattern is sometimes called "wrong-way risk" in [Module 11](./module-11-risk-exposure.md): Risk &amp; Exposure).

**5. Because every other row in Section 8's table names a government-affiliated regulator (a central bank, a securities commission, a self-regulatory organization with statutory authority) operating within a defined jurisdiction — the ISDA Determinations Committee is instead a body made up of industry participants (dealers and buy-side firms) who collectively interpret the CDS contract's own standardized terms, with no government mandate behind its rulings; its authority comes entirely from being named as the deciding body inside the ISDA contract documentation itself, not from statute.**

Section 8's takeaway flags this explicitly as one of two genuinely new regulatory concepts in this module — a private, contractually-designated decision-making body sitting alongside (not replacing) the actual government regulators in the same table.

**6. The CFTC/SEC split exists because Dodd-Frank drew a specific legal line between "swaps" (CFTC jurisdiction) and "security-based swaps" (SEC jurisdiction), and single-name/narrow-based CDS were specifically classified as security-based swaps because they reference an individual company's (or narrow group's) debt securities — closely tied to securities-law concepts the SEC already regulates. A vanilla IRS, by contrast, references an interest rate benchmark (CORRA, SOFR), not any specific company's securities, so it falls cleanly into the CFTC's "swap" bucket with no securities-law hook for the SEC to claim jurisdiction over.**

Section 8's table states this split directly, contrasting it with [Module 8](./module-08-rates.md) §08's clean CFTC-only picture for Rates — the deciding factor is whether the underlying reference is a security-linked concept (triggering SEC jurisdiction) or a pure rate/benchmark (staying with the CFTC alone).

**7. Because the exact same shape — one standardized, liquid slice of an OTC asset class pulled into mandatory or growing central clearing, while a long tail of bespoke instruments remains bilateral — has now appeared independently in three different asset classes (Commodities' Futures, [Module 7](./module-07-commodities.md) §07; Rates' IRS, [Module 8](./module-08-rates.md) §07; Credit's CDS Indices and liquid names, this module's Section 07), each reached via a different specific mechanism (venue design, regulatory mandate, and a narrower regulatory mandate respectively).**

Section 7's takeaway draws this conclusion explicitly: by the third independent occurrence of the same structural pattern across genuinely different products and different causal mechanisms, it stops looking like asset-class-specific detail and starts looking like a general truth about how OTC derivatives markets evolve toward partial standardization — worth carrying forward into [Module 12](./module-12-collateral-margin.md) (Collateral &amp; Margin) and [Module 14](../2-reporting/module-14-regulatory-reporting.md) (Regulatory Reporting), where the bilateral/cleared distinction resurfaces as a major driver of what collateral and reporting obligations apply.

**8. Because Section 7 already classified the Northstar Resources Inc. CDS as a bespoke, illiquid single-name trade that stays BILATERAL — ICE Clear Credit's role in Section 9's table is scoped specifically to the standardized CDS Index / liquid single-name slice of the market, which this particular trade doesn't belong to.**

Section 9's takeaway states this directly: the trade's own Section 7 classification determines which Back Office system actually touches it — DTCC's TIW records its lifecycle either way, but ICE Clear Credit only enters the picture for trades that are actually centrally cleared, which the Northstar Resources Inc. trade, by design, is not.

---

[← Back to Index](../../index.md)
