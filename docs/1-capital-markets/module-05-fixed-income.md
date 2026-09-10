# Module 5 — Fixed Income

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Asset Class Roadmap 2 of 6** — Equity → **Fixed Income** → FX → Commodities → Rates → Credit.

---

## 1. What Is Fixed Income? — Theory &amp; Terminology

**Definition:** A Fixed Income instrument is, at its core, **a loan**. The buyer lends money to the issuer (a government, a company, a municipality); the issuer promises to pay periodic interest (the coupon) and return the principal (Face Value) at a fixed future date (Maturity). This is the mirror image of [Module 4](./module-04-equity.md)'s Equity: a bond is a **creditor claim**, not an ownership claim — no voting rights, no share of future profits, but a contractual right to be paid that ranks ahead of equity if the issuer fails.

| Term | Meaning |
|---|---|
| Face Value | The principal amount repaid at maturity; also the base the coupon is calculated against |
| Coupon | The fixed periodic interest payment, expressed as a % of Face Value ([Module 1](./module-01-fundamentals.md) §1) |
| Maturity | The fixed date the Face Value is repaid — the defining feature equity doesn't have |
| Price | Quoted per 100 of Face Value ([Module 1](./module-01-fundamentals.md) §1) |
| Yield | The buyer's actual return given the price paid, not just the coupon rate ([Module 1](./module-01-fundamentals.md) §1) |

Coupon Rate vs. Yield was already covered in full, with a four-year worked example, in [Module 1](./module-01-fundamentals.md) §1 (and restated in the main curriculum doc's [Module 5](./module-05-fixed-income.md) section) — the short version: **Coupon Rate is fixed and printed on the bond; Yield is derived and moves with Price.** That distinction is assumed from here on.

### Clean Price vs Dirty Price vs Accrued Interest — new territory

**Definition:** Bond prices are always *quoted* as **Clean Price** — the price with no interest included, purely reflecting the bond's value. But a buyer settling a bond trade between coupon dates owes the seller **Accrued Interest** — the coupon the seller has "earned" by holding the bond since the last coupon date, which the seller would otherwise lose entirely to the buyer. The actual cash exchanged at settlement is the **Dirty Price** (also called the "Full Price" or "Invoice Price") — Clean Price plus Accrued Interest.

```text
CLEAN PRICE vs DIRTY PRICE — worked example
========================================================================

  Bond: Canada 5% Bond 2035, semi-annual coupon (2.50% per period)
  Face Value: CAD 10,000,000
  Quoted (Clean) Price: 98.50
  Settlement falls 60 days after the last coupon date, in a
  182-day coupon period

  Accrued Interest = Face Value x (Coupon/2) x (Days Since Last Coupon / Days in Period)
                    = 10,000,000 x 2.50% x (60 / 182)
                    = 250,000 x 0.32967
                    = CAD 82,418  (rounded)

  Clean Price Value = 10,000,000 x 98.50 / 100 = CAD 9,850,000
  Dirty Price (actual cash paid) = 9,850,000 + 82,418 = CAD 9,932,418

  -&gt; The seller held the bond for 60 of the 182 days in this
     coupon period, so 60/182 of the next coupon belongs to them,
     even though the BUYER will be the one who actually receives
     the full coupon payment on the next coupon date.
```

**IT/reporting angle:** most systems store a bond's "Price" field as the **Clean Price** — that's the market convention, the number dealers quote, and what moves on a price feed. But the cash that actually settles ([Module 1](./module-01-fundamentals.md) §7) is always the **Dirty Price**. If a settlement amount doesn't match `Quantity × Clean Price / 100`, that's not automatically a bug — check whether Accrued Interest was added, and whether the trade fell between coupon dates.

### Duration — a one-line preview

**Definition:** **Duration** (specifically *Modified Duration*) approximates how much a bond's price moves for a 1% change in yield. A bond with a Modified Duration of 8 will see its price fall roughly 8% if yields rise 1%, and rise roughly 8% if yields fall 1%. The full math (and its close relative, DV01) belongs to [Module 10](./module-10-valuation.md) (Valuation) — for now, just the intuition: **longer maturity and lower coupon generally mean higher duration, meaning more price sensitivity to rate moves.** For the basis point unit itself — why the bond market measures yield/price sensitivity "per basis point" rather than "per percent," and how the same DV01 mechanism reapplies across Rates, Credit, Equity and FX — see the standalone [Module 24 — Basis Points Deep Dive](./module-24-basis-points-deep-dive.md).

**Takeaway:** Fixed Income terminology all serves one goal — separating what's *fixed at issuance* (Face Value, Coupon Rate, Maturity) from what's *market-driven and changes daily* (Price, Yield, Accrued Interest, and therefore Dirty Price). Getting this split right is the single most useful mental model for any bond-related reporting field.

---

## 2. What Are The Products? — Government, Corporate, Municipal &amp; Other Debt

```text
FIXED INCOME
│
├── Government Bonds        (sovereign issuer, highest credit quality in its own currency)
├── Corporate Bonds         (issued by companies, incl. banks like Bank Of Toronto)
├── Municipal Bonds         (issued by cities/provinces/states/municipalities)
└── Other Debt Instruments  (money market &amp; securitized: T-Bills, CP, CDs, MBS/ABS/CLO)
```

| Product | What it is | Example |
|---|---|---|
| **Government Bonds** | Debt issued by a national government; typically the benchmark risk-free rate in its own currency | Canada 5% Bond 2035 ([Module 1](./module-01-fundamentals.md) §1) |
| **Corporate Bonds** | Debt issued by a company to fund operations/expansion; carries credit risk the issuer's government debt doesn't | Bank Of Toronto 4.5% Bond 2030 ([Module 2](./module-02-hierarchy.md) §6) |
| **Municipal Bonds** | Debt issued by a city, province, or (in the US) a state/local government, often to fund infrastructure | City of Toronto 3.75% Green Bond 2032 |
| **Other Debt Instruments** | Money market (short maturity, &lt;1 year) and securitized debt | Treasury Bills, Commercial Paper, CDs, MBS/ABS/CLO ([Module 2](./module-02-hierarchy.md) §6) |

A US-specific nuance worth flagging under Municipal Bonds: interest on many **US municipal bonds is exempt from US federal income tax** (and often state tax too, for a resident of the issuing state) — a genuinely unique tax feature that doesn't have a broad equivalent for Canadian municipal/provincial bonds, and is a major reason US munis trade at noticeably lower yields than a similarly-rated corporate bond.

**Takeaway:** the further down this list you go, the more the instrument's risk profile is driven by *who the issuer is* rather than by the mechanics of the bond itself — a Government Bond's main variable is interest-rate risk ([Module 8](./module-08-rates.md): Rates), while a Corporate Bond adds credit risk ([Module 9](./module-09-credit.md): Credit) on top of the same coupon/price/yield mechanics.

---

## 3. What Exactly Is Being Traded? — Security vs Contract

**Definition:** A bond itself is, like a share, an **actual security** — a real, transferable claim recorded in book-entry form at a CSD ([Module 1](./module-01-fundamentals.md) §7). But Fixed Income introduces a product that Equity doesn't have an equivalent of at this stage: **repo** — a **contract**, not a security, built *on top of* a bond.

```text
BOND (SECURITY)  vs  REPO (CONTRACT)
========================================================================

  Canada 5% Bond 2035 (SECURITY):
    A real, transferable claim on the Government of Canada. It exists
    independently of any one trade, the same way Coca-Cola stock does
    (Module 4 §3).

  Repo — "Repurchase Agreement" (CONTRACT):
    Bank Of Toronto lends EUR 50,000,000 cash overnight to Goldman
    Sachs, who posts Germany 3% Bund 2033 as COLLATERAL and agrees
    to repurchase it (i.e., return the cash + interest and get the
    Bund back) the next business day.

    Overnight interest = 50,000,000 x 3.15% x (1/365) = EUR 4,315
    (this is the exact trade from systems-landscape §12's SFTR
    worked example — a repo IS a Securities Financing Transaction)

  -&gt; The Bund itself doesn't change hands as a SALE — legal title
     temporarily transfers as COLLATERAL, with a contractual promise
     to reverse the transfer the next day. This is economically a
     secured cash loan, not a purchase of the bond.
```

**Takeaway:** this is Fixed Income's version of [Module 4](./module-04-equity.md) §3's "security vs contract" distinction — but where an Equity Option is a *derivative* contract referencing a stock's price, a Repo is a *financing* contract using a bond as collateral. Different category of contract entirely, which is exactly why it's reported under SFTR (Section 8) rather than EMIR.

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

> **Complete worked examples available.** For the government bond trade below, see [Fixed Income Bond Trade — Complete Example](../../examples/Fixed_Income_Bond_Complete_Example.md) ([HTML](../../examples/Fixed_Income_Bond_Complete_Example.html)). For this module's repo trade, see [Repo / Securities Financing — Complete Example](../../examples/Repo_Securities_Financing_Complete_Example.md) ([HTML](../../examples/Repo_Securities_Financing_Complete_Example.html)) — both walk the full trade lifecycle with real numbers.

### 4a. Government Bond

```text
GOVERNMENT BOND  —  Canada 5% Bond 2035 (ISIN CA1358XXXXX)
========================================================================

  Buyer:  Bank Of Toronto
  Seller: Goldman Sachs
  (the secondary-market trade first introduced in Module 1 §1)

  Trade: CAD 10,000,000 face value @ Price 98.50
  Clean Price Value = 10,000,000 x 98.50 / 100 = CAD 9,850,000
  (see Section 1 for the Dirty Price/Accrued Interest add-on)
```

### 4b. Corporate Bond

```text
CORPORATE BOND  —  Bank Of Toronto 4.5% Bond 2030
========================================================================

  Issuer: Bank Of Toronto
  Buyer:  Maple Leaf Pension Fund

  Trade: CAD 12,000,000 face value @ Price 101.75 (premium — market
         rates have fallen below the 4.5% coupon since issuance,
         Module 1 §1's price-vs-rates mechanic)
  Trade Value = 12,000,000 x 101.75 / 100 = CAD 12,210,000
```

### 4c. Municipal Bond

```text
MUNICIPAL BOND  —  City of Toronto 3.75% Green Bond 2032
========================================================================

  Issuer: City of Toronto (proceeds earmarked for green
          infrastructure — a "use of proceeds" bond)
  Buyer:  Maple Leaf Pension Fund
  Dealer: Bank Of Toronto

  Trade: CAD 15,000,000 face value @ Price 97.25
  Trade Value = 15,000,000 x 97.25 / 100 = CAD 14,587,500

  Note: unlike a US municipal bond, this Canadian municipal bond's
  interest is fully taxable to Maple Leaf Pension Fund — the
  broad tax-exemption feature (Section 2) is specifically a US
  municipal-bond feature, not a general "municipal bond" one.
```

> **This is a primary issuance, not a secondary-market trade.** City of Toronto's bond doesn't exist yet before this trade — Bank Of Toronto is **underwriting/placing a brand-new bond** ([Module 1](./module-01-fundamentals.md) §1's primary-market mechanics), not intermediating a security that's already outstanding between two other market participants the way a secondary-market dealer trade would. The CAD 14,587,500 flows in one direction, through Bank Of Toronto, from the end buyer back to the issuer:

```text
PRIMARY ISSUANCE CHAIN — WHO OWES WHAT TO WHOM
========================================================================

  Maple Leaf Pension Fund  ---- pays CAD 14,587,500 ---->  Bank Of Toronto
  Bank Of Toronto          ---- pays (proceeds, less underwriting
                                 fee/spread) --------------> City of Toronto

  -&gt; Bank Of Toronto is the underwriter/placement agent standing
     between a NEW issuer and the end buyer — a different role
     than Section 4a's Government Bond trade, where Bank Of
     Toronto is simply the BUYER of an already-existing security
     with no issuer in the chain at all.
```

### 4d. Other Debt Instrument — Treasury Bill

```text
TREASURY BILL  —  Government of Canada 90-Day T-Bill
========================================================================

  Buyer: Bank Of Toronto (money-market desk)

  Face Value: CAD 20,000,000, Maturity: 90 days, NO coupon
  Purchase Price: 98.75 (per 100) -- bought at a DISCOUNT to par

  Purchase Cost = 20,000,000 x 98.75 / 100 = CAD 19,750,000
  Discount Earned = 20,000,000 - 19,750,000 = CAD 250,000
                    (this IS the return -- there is no coupon)

  Discount Yield = (Discount / Purchase Cost) x (365 / Days to Maturity)
                 = (250,000 / 19,750,000) x (365 / 90)
                 = 1.2658% x 4.0556
                 = 5.13%

  -&gt; T-Bills return purely the gap between discounted purchase
     price and full Face Value at maturity -- there's no coupon
     to separate from yield the way Section 1 did for the 5% bond.
```

**Takeaway:** all four examples share the exact same core fields (Face Value, Price, Trade Value) from [Module 2](./module-02-hierarchy.md) §3 — what changes is who the issuer is, whether a coupon exists at all (the T-Bill has none), and the tax treatment. The mechanics of "how a Fixed Income trade is priced and valued" are consistent across the whole asset class; only the credit and tax profile shifts.

---

## 5. Trade Lifecycle — Fixed-Income-Specific Walkthrough

Applying **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** to the Government Bond trade from Section 4a, executed in Canada.

```text
CANADA 5% BOND 2035  —  FULL LIFECYCLE
========================================================================

  10:15:00  ORDER — Bank Of Toronto's desk instructed to buy
            CAD 10,000,000 face value, Canada 5% Bond 2035

  10:15:22  EXECUTION @ Price 98.50 (Clean) vs Goldman Sachs,
            interbank market

  10:15:22  TRADE CAPTURE — Trade ID BOT-FI-2026-013380
            Settlement falls 60 days after the last coupon date
            (Section 1) -&gt; Accrued Interest = CAD 82,418

  10:30:00  CONFIRMATION/MATCHING -&gt; MATCHED
            (no CCP for this bilateral OTC bond trade — Section 7)

  Settlement Date = T+2 (Government of Canada bonds — systems-
            landscape §04's Canada table)
            CDS/CDSX performs DvP:
              Bond: CAD 10,000,000 face value moves from Goldman
                    Sachs' to Bank Of Toronto's CDS account
              Cash: DIRTY PRICE = CAD 9,932,418 moves the other way
                    (Clean Price CAD 9,850,000 + Accrued Interest
                    CAD 82,418 — Section 1)

  POSITION — Bank Of Toronto's Fixed Income book increases by
            CAD 10,000,000 face value of this bond

  VALUATION / P&amp;L — marked daily to the current market Clean
            Price, PLUS newly-accruing interest each day:
              Settlement day:  Clean 98.50, Accrued 82,418
              One week later:  Clean 98.65, Accrued 106,164
                (7 more days of accrual: 250,000 x 67/182)
              Price P&amp;L   = (98.65 - 98.50)/100 x 10,000,000 = +15,000
              Accrual P&amp;L = 106,164 - 82,418               = +23,746
            -&gt; both components are real and get reported SEPARATELY
               in most Fixed Income P&amp;L systems (Module 10)

  REPORTING — trade reported to the relevant Trade Repository/
            regulator by T+1 (Module 3 §09)
```

**Takeaway:** the single biggest lifecycle difference from [Module 4](./module-04-equity.md)'s Equity walkthrough is that Fixed Income's Valuation/P&amp;L stage splits into **two** components every single day — price movement and interest accrual — while an equity position only has one (price movement; dividends are episodic, not continuously accruing). A bond P&amp;L report that only shows price movement is silently dropping the accrual leg.

> **Deep dive available.** For the full theory of what a Position is, and why position SIZE is the multiplier that turns a per-unit yield/price move into an actual dollar figure: [Module 25 — Position Deep Dive](./module-25-position-deep-dive.md) §4.

---

## 6. Identifiers — Applied to Bonds

Bonds reuse the same identifier set from [Module 2](./module-02-hierarchy.md) §2 and [Module 4](./module-04-equity.md) §6, with one structural difference: **bonds essentially never have a Ticker.**

| Identifier | Government Bond | Corporate Bond | Notes |
|---|---|---|---|
| ISIN | `CA1358XXXXX` (Canada 5% Bond 2035) | `CA0553XXXXX` (Bank Of Toronto 4.5% Bond 2030, illustrative) | Global, the primary bond identifier |
| CUSIP | `135087XX1` (illustrative) | `055349XX1` (illustrative) | North America only |
| Ticker | — none — | — none — | Bonds don't trade under exchange tickers ([Module 2](./module-02-hierarchy.md) §2) |
| LEI | Identifies the **Government of Canada** as issuer | Identifies **Bank Of Toronto** as issuer | Entity, not instrument ([Module 4](./module-04-equity.md) §6) |
| UPI | Not applicable | Not applicable | UPI is for OTC *derivatives* ([Module 2](./module-02-hierarchy.md) §12) — a cash bond isn't one. A bond *future* ([Module 8](./module-08-rates.md): Rates) would instead get an exchange contract code, similar to [Module 4](./module-04-equity.md)'s equity future |

**Takeaway:** if a reporting record has an ISIN/CUSIP but no Ticker, [Module 2](./module-02-hierarchy.md) §2 already flagged this as the classic signature of a bond. This module adds the reverse-direction check: if that same record also has a UPI populated, something is wrong — a cash bond should never carry one.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** unlike [Module 4](./module-04-equity.md)'s Equity, where "exchange-traded" and "centrally cleared" mostly travel together, **cash Fixed Income trades are overwhelmingly OTC and bilateral** — there is usually no CCP standing between a bond buyer and seller. Repo is the interesting exception: it can be either bilateral or centrally cleared, and which one it is has real consequences.

```text
FIXED INCOME'S VERSION OF THE TWO-AXIS MODEL
========================================================================

                    BILATERAL                  CENTRALLY CLEARED
   ------------------------------------------------------------------
   EXCHANGE-        n/a — bonds essentially      A few retail bond
   TRADED            never trade on an             platforms exist,
                      exchange                      but volume is tiny
   ------------------------------------------------------------------
   OTC               MOST Government, Corporate,   Repo, increasingly:
                      and Municipal bond trades     CDCC (Canada, since
                      (Section 4a-4c above) —        2012), FICC (US
                      dealer-to-dealer or             Treasury repo,
                      dealer-to-client, no CCP        systems-landscape §05)
```

| Cash / SFT | Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|---|
| CASH | Government Bond | OTC | Bilateral (the norm) |
| CASH | Corporate Bond | OTC | Bilateral (the norm) |
| CASH | Municipal Bond | OTC | Bilateral (the norm) |
| SFT | Repo | OTC | **Either** — bilateral by default, but increasingly routed through CDCC/FICC for the largest, most standardized repo activity |

**Takeaway:** this is the opposite default from [Module 4](./module-04-equity.md)'s Equity table, where bilateral-and-exchange-traded was "effectively never" and centrally-cleared was the norm. For Fixed Income, bilateral OTC is the norm and central clearing is the (growing) exception — which is exactly why bond market regulation (Section 8) leans so heavily on *transaction reporting* (MiFIR) rather than relying on a CCP's own records the way equity regulators partly can.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | How it touches Fixed Income specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Bond transaction reporting and pre/post-trade transparency — with different thresholds than equities (e.g. "large-in-scale" waivers that delay publication for big bond trades, reflecting how much less liquid bonds are than large-cap stocks) |
| **EMIR** | Does **not** apply to a cash bond trade — a bond isn't a derivative. It would apply to a *bond future or bond option* ([Module 8](./module-08-rates.md): Rates), the Fixed Income equivalent of [Module 4](./module-04-equity.md) §8's equity-derivatives carve-out |
| **SFTR** | Applies directly to **repo** (Section 3's worked example) — this is the module where that [systems-landscape](./systems-landscape.md) §12 example's underlying instrument, Germany 3% Bund 2033, actually comes from |
| **FCA** (UK) | Conduct regulator for UK bond trading, administers UK MiFIR bond transaction reporting ([systems-landscape](./systems-landscape.md) §10) |
| **CIRO** (Canada) | SRO overseeing Canadian bond dealer conduct ([systems-landscape](./systems-landscape.md) §10) |
| **HKMA** (Hong Kong) | Genuinely more central here than it was for Equity ([Module 4](./module-04-equity.md) §8, where SFC was the more relevant body): the HKMA directly issues **Exchange Fund Bills &amp; Notes** (Hong Kong's short/medium-term government debt) and acts much like a debt management office for the territory, in addition to its central-bank role |
| **DTCC** (US) | Its subsidiary **FICC** specifically clears US Treasuries and Treasury repo ([systems-landscape](./systems-landscape.md) §05) — the Fixed Income-specific piece of DTCC that [Module 4](./module-04-equity.md) didn't need to reach for |
| **DMO** (UK Debt Management Office) | This is its module: the DMO issues UK Gilts via auction — the UK's direct counterpart to how the Government of Canada issues bonds via auction ([Module 1](./module-01-fundamentals.md) §1) and how the US Treasury issues Treasuries (with the Federal Reserve acting as fiscal agent). See the [UK DMO Reporting Deep Dive](../2-reporting/module-17-dmo-uk-reporting.md) for exactly what a GEMM reports back to the DMO, and with what data |

**Takeaway:** two bodies flagged as tangential in [Module 4](./module-04-equity.md) (HKMA, DMO) turn out to be squarely on-topic here — a reminder that Section 8's regulatory perspective genuinely depends on the asset class in front of you, exactly as the Asset Class Roadmap's eight-part pattern intends: the same acronym can be the *main* actor in one module and a footnote in another.

---

## 9. Canadian Systems Landscape — Fixed Income Front, Middle &amp; Back Office to Regulatory Reporting

**Definition:** [systems-landscape](./systems-landscape.md) §2 already laid out the *generic* Front/Middle/Back Office architecture for Canada. This section specializes that architecture to **Fixed Income specifically** — naming the actual platforms a Canadian bond desk's trade touches on its way from execution to CIRO/Bank of Canada oversight, using Section 4a's Canada 5% Bond 2035 trade as the running example.

> **Callout — illustrative, not prescriptive.** The platform names below (CanDeal, Bloomberg, Murex/Calypso/Summit) are representative of real Canadian fixed income market infrastructure, in the same spirit as [systems-landscape](./systems-landscape.md) §11's own vendor list — they are not a claim about which specific systems Bank Of Toronto itself runs internally. The institutional infrastructure (CDS/CDSX, CDCC, CIRO, Bank of Canada) is the part of this section that's factual, not illustrative.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | **CanDeal** (Canada's dealer-to-client electronic platform for government &amp; corporate bonds); Bloomberg ALLQ/BondTrader (illustrative); the **Bank of Canada's** own auction system for new-issue Government of Canada bonds | Price discovery and execution — where Bank Of Toronto's desk actually buys/sells the bond, or bids at a GoC auction as a primary dealer |
| **Middle Office** | Bilateral/platform trade confirmation; valuation &amp; risk systems (Murex, Calypso, Summit — illustrative, the same vendor family [systems-landscape](./systems-landscape.md) §11 already names) | Confirms the trade matches both sides' understanding (Section 5's "CONFIRMATION/MATCHING" stage); computes Clean/Dirty Price and Accrued Interest (Section 1) for P&amp;L |
| **Back Office** | **CDS Clearing and Depository Services Inc.**, operating **CDSX** — book-entry DvP settlement of the bond itself; **CDCC (Canadian Derivatives Clearing Corporation)**, which has, since 2012, separately cleared the **fixed income repo market** (Section 3's repo distinction — CDCC's repo-clearing role is already established in this module's Section 8 and in [systems-landscape](./systems-landscape.md) §12) | Settles cash bond trades T+2 via CDSX; clears standardized repo activity via CDCC where applicable |
| **Regulator / Reporting** | **CIRO** (bond-dealer conduct, corporate-bond post-trade transparency); **Bank of Canada** (fiscal agent for GoC auctions; oversees CDSX and CDCC as designated systemically important FMIs); **CSA Multilateral Instrument 96-101** (OTC derivatives trade reporting only) | Conduct/market oversight, systemic-infrastructure oversight, and — where MI 96-101 actually applies — trade reporting to a CSA-recognized Trade Repository |

```text
CANADA 5% BOND 2035 — FRONT TO BACK, CANADIAN SYSTEMS VIEW
========================================================================

  FRONT OFFICE
    Bank Of Toronto's desk executes CAD 10,000,000 face value
    @ Clean Price 98.50 vs Goldman Sachs (Section 4a/5) --
    interbank OTC, no exchange (Section 7) -- via a platform such
    as CanDeal, or voice/chat confirmed into the same workflow
         |
         v
  MIDDLE OFFICE
    Trade confirmed/matched (Section 5's 10:30:00 step); Clean
    Price 98.50 + Accrued Interest CAD 82,418 = Dirty Price
    CAD 9,932,418 (Section 1) computed and checked
         |
         v
  BACK OFFICE
    CDS / CDSX -- book-entry DvP, T+2 (systems-landscape §04):
      Bond leg: CAD 10,000,000 face value, Goldman Sachs -> Bank
                Of Toronto's CDS account
      Cash leg: CAD 9,932,418, Bank Of Toronto -> Goldman Sachs
    (a REPO on this same bond, instead, would route through CDCC's
    repo-clearing service rather than a plain CDSX DvP -- Section 3)
         |
         v
  REGULATOR / OVERSIGHT
    CIRO -- dealer-conduct oversight of both counterparties
    Bank of Canada -- systemic oversight of CDSX itself as the
      designated FMI actually performing the settlement above
    CSA MI 96-101 -- NOT triggered by this trade; it is scoped to
      OTC DERIVATIVES reporting, and a cash Government of Canada
      bond purchase is neither OTC-derivative nor SFT (contrast
      Section 3's repo, which — per systems-landscape §12 — still
      has NO dedicated SFTR-equivalent reporting regime in Canada
      as of 2026, unlike the EU/UK module-16-sftr-reporting module's regime)
```

**Takeaway:** notice how much of this trade's journey never touches a CCP or a Trade Repository at all — a cash Government of Canada bond purchase settles bilaterally through CDSX and is overseen by CIRO/Bank of Canada, but triggers neither CDCC clearing (that's repo-specific) nor CSA MI 96-101 reporting (that's OTC-derivative-specific). The same "which systems does this trade actually touch" question, asked of Section 3's repo instead, produces a genuinely different answer — CDCC in the back office, and still no Canadian SFT reporting regime at the regulatory layer, a gap [systems-landscape](./systems-landscape.md) §12 already flags.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Government of Canada | Issuer of the Canada 5% Bond 2035 |
| Bank Of Toronto | Buyer of the government bond; issuer of its own 4.5% Bond 2030; dealer on the municipal bond trade |
| Goldman Sachs | Seller of the government bond; repo counterparty (borrower of cash, poster of Bund collateral) |
| Maple Leaf Pension Fund | Buyer of the corporate bond and the municipal bond |
| City of Toronto | Issuer of the Green Bond 2032 |
| CDS / CDSX | CSD settling the Canadian government bond trade ([systems-landscape](./systems-landscape.md) §04) |
| CDCC / FICC | CCPs that can centrally clear repo (Canada / US respectively) |
| CIRO | SRO overseeing Canadian bond-dealer conduct and post-trade transparency (Section 8, Section 9) |
| Bank of Canada | Fiscal agent for Government of Canada bond auctions; systemic overseer of CDSX and CDCC (Section 9) |

---

## Recap Quiz — Numbers Edition

1. On the Canada 5% Bond 2035 trade in Section 5, if settlement instead fell 120 days into the same 182-day coupon period, what would the new Accrued Interest and Dirty Price be?
2. Why is the T-Bill in Section 4d described as having no "coupon vs yield" distinction to make, unlike the Section 1 worked example?
3. In the repo from Section 3, is Goldman Sachs a buyer or a seller of the German Bund — and why is neither label really accurate for what's economically happening?
4. Using Section 6's table, what does the presence of a UPI field on an otherwise bond-like trade record most likely indicate about that trade?
5. Why does Section 7 flag repo as the exception to "Fixed Income is bilateral OTC," and which two named CCPs can centrally clear it?
6. A trade record shows EMIR reporting fields populated for what looks like a Fixed Income trade. Based on Section 8, what does that tell you about the instrument, and what's the most likely explanation?
7. Contrast the HKMA's relevance in this module vs. [Module 4](./module-04-equity.md)'s Equity module — what specific Fixed-Income-related function does HKMA perform that made it worth calling out here?
8. Using Section 9, does the Canada 5% Bond 2035 trade from Section 4a/5 ever touch CDCC or trigger CSA MI 96-101 reporting? Contrast your answer with what would happen if the SAME bond were instead used as collateral in a repo.

Answer whenever ready — then move to **[Module 6](./module-06-fx.md): FX**, the next stop on the Asset Class Roadmap.

---

## Answers — Recap Quiz, with Explanations

**1. Accrued Interest = CAD 164,835; Dirty Price = CAD 10,014,835.**

```text
Accrued Interest = 10,000,000 x 2.50% x (120 / 182) = 250,000 x 0.65934 = CAD 164,835
Dirty Price = 9,850,000 (Clean) + 164,835 = CAD 10,014,835
```

The later into the coupon period settlement falls, the more accrued interest has built up (approaching the full CAD 250,000 semi-annual coupon as days-since-last-coupon approaches 182) — exactly the linear accrual mechanic Section 1 established.

**2. Because a T-Bill pays no coupon at all — its entire return comes from the gap between the discounted purchase price and the full Face Value received at maturity, so there's no separate "coupon rate" number to compare a "yield" against.**

Section 1's coupon-vs-yield distinction only exists because a coupon bond has *two* numbers that can diverge (a fixed coupon rate and a price-dependent yield). A T-Bill has only one return mechanism — the discount — so "yield" is the only number that exists; there's nothing for it to be compared against.

**3. Goldman Sachs is neither cleanly a buyer nor a seller — economically, Goldman Sachs is a cash *borrower* posting the Bund as *collateral*, not selling it.**

Section 3 states this directly: legal title to the Bund temporarily transfers, but the whole transaction is structured as a secured loan with a contractual promise to reverse the transfer — which is precisely why repo is classified as a Securities *Financing* Transaction (SFTR, Section 8) rather than an outright purchase/sale that MiFID transaction reporting would otherwise capture.

**4. It most likely indicates the trade is actually a bond *derivative* (e.g. a bond future or bond option, covered in [Module 8](./module-08-rates.md): Rates), not a cash bond — or it's a data-quality error.**

Section 6's takeaway states this explicitly: UPI is for OTC derivatives; a cash bond should never carry one. Seeing it populated on an otherwise bond-shaped record is the same diagnostic signal [Module 2](./module-02-hierarchy.md) §2 established for Tickers on bonds, just running the check in the opposite direction.

**5. Repo is the exception because, unlike outright government/corporate/municipal bond trades (which stay bilateral OTC), it's increasingly routed through a CCP for standardized activity — CDCC in Canada (since 2012) and FICC in the US (for Treasury repo specifically).**

Section 7's table lists both by name, tying directly back to [systems-landscape](./systems-landscape.md) §04 (CDCC) and §05 (FICC) — the same two CCPs already introduced there for Canadian and US market infrastructure respectively.

**6. It most likely means the instrument isn't actually a plain cash bond — it's a bond future or bond option (an interest-rate derivative, properly covered in [Module 8](./module-08-rates.md): Rates) that only superficially resembles a Fixed Income cash trade.**

Section 8's table states EMIR does not apply to a cash bond, only to a derivative — seeing EMIR fields populated is the strongest possible signal that whatever "Fixed Income-looking" trade this is, it's actually a derivative in disguise, and belongs in the Rates asset class instead.

**7. The HKMA directly issues Exchange Fund Bills &amp; Notes — Hong Kong's own government debt instruments — acting much like a debt management office for the territory, in addition to its usual central-bank role.**

In [Module 4](./module-04-equity.md) (Equity), HKMA was flagged as a step removed from the action, with the SFC doing the real work of equity conduct regulation. Here, HKMA is *directly* the issuer-side actor for Hong Kong government debt, the same functional role the DMO plays for UK Gilts (Section 8) — a concrete illustration of Section 8's takeaway that the same regulator's relevance shifts with the asset class.

**8. No to both — the Section 4a/5 trade is a plain cash Government of Canada bond purchase, so it settles via CDSX only, touching neither CDCC (repo-specific) nor CSA MI 96-101 (OTC-derivative-specific). If the SAME bond were instead posted as collateral in a repo, the back-office path changes to CDCC's repo-clearing service (where the repo is standardized enough to route there), but CSA MI 96-101 STILL wouldn't apply — Canada has no dedicated SFT reporting regime as of 2026, unlike the EU/UK's SFTR.**

Section 9's worked diagram states this explicitly: the same underlying bond produces two different back-office paths (CDSX for an outright purchase, CDCC for repo) depending purely on which *contract type* it's wrapped in — Section 3's security-vs-contract distinction, now traced all the way through to which Canadian system actually touches the trade.

---

[← Back to Index](../../index.md)
