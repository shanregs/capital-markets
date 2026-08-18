# Module 8 — Rates

[← Back to Index](./index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Asset Class Roadmap 5 of 6** — Equity → Fixed Income → FX → Commodities → **Rates** → Credit.
> Work data model: this module maps to the taxonomy's **"2. Interest Rate"** node — five products, each with its own Trade + Valuation Avro schema.

---

## 1. What Is Rates? — Theory &amp; Terminology

**Definition:** Rates is the asset class of contracts whose cash flows depend on an **interest rate**, not on a company's earnings (Equity), a currency's value (FX), or a physical good's price (Commodities). [Module 1](./module-01-fundamentals.md) §8 already introduced the core building block — the Interest Rate Swap — as a bilateral agreement to exchange a fixed-rate cash flow stream for a floating-rate one, referencing a **Notional** that never itself settles. This module builds out the full family: Rates is where "notional never settles, only the net difference does" ([Module 1](./module-01-fundamentals.md) §8) is the rule for almost every product, not the exception.

| Term | Meaning |
|---|---|
| Fixed Leg | The side of a swap paying a rate agreed at trade date, unchanged for the life of the trade |
| Floating Leg | The side paying a rate that resets periodically against a published reference rate |
| Reference Rate | The published benchmark a floating leg resets against — e.g. **CORRA** (Canada), **SOFR** (US), **€STR** (EU), **SONIA** (UK) |
| Payment Dates | The scheduled dates cash flows are calculated and exchanged — quarterly is the most common convention |
| DV01 | "Dollar Value of 01" — how much a Rates position's value changes for a 1 basis point (0.01%) move in rates; the Rates-specific cousin of [Module 5](./module-05-fixed-income.md) §1's bond Duration, covered fully in [Module 10](./module-10-valuation.md) (Valuation) |

### The LIBOR-to-RFR transition — why "CORRA" and not "CDOR" or "LIBOR"

**IT/reporting angle:** until the early 2020s, almost every floating leg in this asset class referenced **LIBOR** (London Interbank Offered Rate) or a local equivalent. LIBOR was discontinued (it was based on interbank *estimates*, not actual transactions, and proved manipulable) and replaced globally by **Risk-Free Rates (RFRs)** computed from real overnight transactions: SOFR in the US, €STR in the EU, SONIA in the UK, and CORRA in Canada. This module uses CORRA throughout, consistent with [Module 1](./module-01-fundamentals.md) §8's original worked example — but any reporting system built before ~2022 that still has a hardcoded "LIBOR" reference-rate field is carrying legacy debt from this exact transition, one of the largest coordinated data-migration events the industry has ever run.

**Takeaway:** every product in this module is a variation on one theme — fixed vs. floating, or the *option* to enter into fixed vs. floating — with Notional as a pure calculation input. Keeping that one sentence in mind is enough to correctly reason about every worked example in Section 4.

---

## 2. What Are The Products? — Cap/Floor, IR Forward Debt, FRA, IRS &amp; Swaption

```text
INTEREST RATE
│
├── Interest Rate Swap (IRS)   (exchange fixed vs floating cash flows, periodically, for a term)
├── FRA (Forward Rate Agreement) (a single-period IRS, cash-settled once, upfront)
├── Cap / Floor                (a strip of options on a floating rate, one per period)
├── Swaption                   (an option to ENTER an IRS at a future date)
└── IR Forward Debt            (a forward purchase/sale of a fixed income security)
```

| Product | What it is |
|---|---|
| **Interest Rate Swap (IRS)** | Two counterparties exchange a fixed-rate cash flow stream for a floating-rate one, on the same Notional, over a multi-year term — [Module 1](./module-01-fundamentals.md) §8's worked example |
| **FRA (Forward Rate Agreement)** | Economically a *single period* of an IRS, isolated: one fixed rate locked in today against one future floating rate reset, cash-settled once — but settled **upfront**, discounted, at the *start* of the reference period rather than the end (Section 4b makes this concrete) |
| **Cap / Floor** | A strip of individual options (a "Cap" is a strip of caplets, a "Floor" a strip of floorlets), each paying out only if the floating reference rate is above (Cap) or below (Floor) a strike rate at that reset — the buyer never pays more than the upfront premium, exactly the asymmetric payoff pattern from [Module 4](./module-04-equity.md) §4d, [Module 6](./module-06-fx.md) §4d, and [Module 7](./module-07-commodities.md) §4d |
| **Swaption** | An option to enter an IRS at a future date, as either the fixed-rate payer ("Payer Swaption") or receiver ("Receiver Swaption") — an option *on* a swap, not a swap itself |
| **IR Forward Debt** | A forward contract to buy or sell a fixed income security ([Module 5](./module-05-fixed-income.md)) at a future date, at a price/yield fixed today — the Rates-asset-class version of [Module 6](./module-06-fx.md) §2's FX Forward, just referencing a bond instead of a currency pair |

**Takeaway:** notice the internal structure here — IRS is the base case; FRA is one slice of it settled differently; Cap/Floor is a strip of options *on* the same floating rate an IRS references; Swaption is an option on the IRS itself; and IR Forward Debt is the odd one out, referencing a bond's price rather than a rate directly. Four of five products are built directly on top of the IRS mental model from [Module 1](./module-01-fundamentals.md) §8 — only IR Forward Debt steps outside it.

---

## 3. What Exactly Is Being Traded? — Contracts Referencing a Rate, Not a Rate Itself

**Definition:** like FX ([Module 6](./module-06-fx.md) §3), Rates has no "security" equivalent for four of its five products — an IRS, FRA, Cap/Floor, and Swaption are all pure contracts, created at execution and settled/expired without ever delivering anything except net cash. IR Forward Debt is the exception: it's a contract *today* that, if it doesn't cash-settle, results in an actual bond (a real security, [Module 5](./module-05-fixed-income.md) §3) changing hands later.

```text
FOUR PURE CONTRACTS  vs  ONE CONTRACT-ON-A-SECURITY
========================================================================

  Interest Rate Swap / FRA / Cap-Floor / Swaption (PURE CONTRACTS):
    None of these reference an existing security. An IRS's Notional
    (Module 1 §8) is a calculation input, never delivered. A Cap's
    strike is compared against a published rate, not a traded
    instrument. Nothing here has an ISIN (Section 6).

  IR Forward Debt (CONTRACT ON A SECURITY):
    Bank Of Toronto agrees TODAY to buy CAD 10,000,000 face value
    of a Government of Canada bond in 3 months, at a forward price
    fixed today. Unless it's cash-settled, in 3 months an ACTUAL
    bond (Module 5 §3's real, transferable security) moves via DvP
    -- the contract's underlying is real and has its own ISIN, even
    though the FORWARD itself is a contract, not the security.

  -&gt; This is the Rates asset class's version of Module 6 §3's
     "contracts all the way down" finding -- except one of the five
     products isn't quite all the way down; it bottoms out in an
     actual bond.
```

**Takeaway:** IR Forward Debt is the reason Section 6's identifier table needs an extra row that none of Modules 5, 6, or 7 required — a single trade record can carry a UPI/UTI for the forward contract itself *and* an ISIN for the bond it may eventually deliver, and both are correct at the same time.

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

### 4a. Interest Rate Swap

Reusing [Module 1](./module-01-fundamentals.md) §8's worked example directly:

```text
INTEREST RATE SWAP  —  5Y CAD IRS
========================================================================

  Bank Of Toronto:  pays FIXED, receives FLOATING (CORRA + spread)
  Goldman Sachs:     pays FLOATING, receives FIXED

  Notional (reference only, NEVER exchanged): CAD 100,000,000
  Term: 5 years, quarterly payments
  Fixed Rate: 4.00%

  ONE QUARTER'S SETTLEMENT — 91-day quarter, actual/365, CORRA
  averages 3.20% over the quarter + 0.05% spread = 3.25% floating

  Bank Of Toronto's FIXED leg = 100,000,000 x 4.00% x 91/365 = CAD 997,260.27
  Goldman Sachs' FLOATING leg = 100,000,000 x 3.25% x 91/365 = CAD 810,273.97

  Net cash flow: Bank Of Toronto PAYS Goldman Sachs
    997,260.27 - 810,273.97 = CAD 186,986.30
  -&gt; only this NET amount moves; the CAD 100,000,000 notional never
     settles at all, exactly Module 1 §8's original point.
```

**Schema:** `IRSwapTrade.avsc` / `IRSwapValuation.avsc`.

### 4b. FRA (Forward Rate Agreement)

```text
FRA  —  3x6 FRA (fixing in 3 months, covering a 3-month period)
========================================================================

  Bank Of Toronto: BUYS the FRA — pays FIXED (the "FRA Rate"),
                    receives FLOATING (3-month CORRA)
  Counterparty: Goldman Sachs

  Notional: CAD 20,000,000
  FRA Rate (locked today): 4.85%
  Reference period: 91 days, actual/365

  AT FIXING (3 months from trade date) — 3-month CORRA settles at 4.50%
  (rates fell since the FRA was agreed)

  Undiscounted difference = 20,000,000 x (4.85% - 4.50%) x 91/365
                           = 20,000,000 x 0.0035 x 0.249315
                           = CAD 17,452.05

  Discounted settlement (paid NOW, at the START of the reference
  period, not its end):
    = 17,452.05 / (1 + 4.50% x 91/365)
    = 17,452.05 / 1.011219
    = CAD 17,259.79

  Bank Of Toronto PAYS Goldman Sachs CAD 17,259.79 -- because Bank
  Of Toronto locked in 4.85% fixed but the market floating rate
  settled lower (4.50%), Bank Of Toronto's FRA turned out to be a
  worse deal than just floating, so it compensates the counterparty.
```

**IT/reporting angle:** this discounting step is the single field most likely to be coded wrong in an FRA valuation system by someone who's only built IRS logic before — an IRS's periodic cash flow settles at the *end* of its period; an FRA's single cash flow settles at the *start* of its period (because by the time the period would have ended, the rate is already known — there's no reason to wait), which means it must be present-valued back to that start date using the very rate that was just fixed.

**Schema:** `IRFraTrade.avsc` / `IRFraValuation.avsc`.

### 4c. Cap/Floor

```text
INTEREST RATE CAP  —  2Y Cap on 3-month CORRA
========================================================================

  Buyer: Bank Of Toronto
  Seller (writer): Goldman Sachs

  Notional: CAD 15,000,000, Strike (Cap Rate): 5.00%
  8 quarterly caplets over 2 years
  Premium: 0.85% of notional, paid upfront = CAD 127,500

  CAPLET #3 RESET — 3-month CORRA settles at 5.40% (above strike)
    Payout = 15,000,000 x (5.40% - 5.00%) x 91/365
           = 15,000,000 x 0.004 x 0.249315
           = CAD 1,495.89 -- paid to Bank Of Toronto

  CAPLET #4 RESET — 3-month CORRA settles at 4.70% (below strike)
    Payout = CAD 0 -- this caplet simply doesn't pay; Bank Of
    Toronto's downside was already capped at the premium paid,
    exactly the asymmetric-payoff pattern from every Option product
    in this roadmap so far (Module 4 §4d, Module 6 §4d, Module 7 §4d)
```

**Schema:** `CapFloorTrade.avsc` / `CapFloorValuation.avsc` — one schema for both Cap and Floor; the direction (strike vs. rate comparison) is a field on the trade, not a different product.

### 4d. Swaption

```text
SWAPTION  —  1Y-into-5Y Payer Swaption
========================================================================

  Buyer: Bank Of Toronto (buys the RIGHT to enter a 5Y IRS, as
         fixed-rate PAYER, in 1 year)
  Seller (writer): Goldman Sachs

  Notional: CAD 50,000,000, Strike: 4.25% fixed
  Expiry: 1 year, Underlying Swap Term: 5 years from expiry
  Premium: 0.60% of notional, paid upfront = CAD 300,000

  AT EXPIRY (1 year) -- Scenario A: prevailing 5Y swap rate = 4.75%
    Exercise: Bank Of Toronto enters the 5Y IRS paying only 4.25%
    fixed, when the market rate is 4.75% -- a favourable swap it
    could not otherwise get. In-the-money by construction.

  AT EXPIRY -- Scenario B: prevailing 5Y swap rate = 3.90%
    Don't exercise: entering a FRESH swap in the market at 3.90%
    fixed beats exercising the swaption at 4.25%. Swaption expires
    WORTHLESS; Bank Of Toronto's loss is capped at the CAD 300,000
    premium -- never more, same maximum-loss pattern as every other
    option product in this roadmap.
```

**Schema:** `SwaptionTrade.avsc` / `SwaptionValuation.avsc`.

### 4e. IR Forward Debt

```text
IR FORWARD DEBT  —  Forward purchase of a Government of Canada bond
========================================================================

  Buyer: Bank Of Toronto (agrees TODAY to buy in 3 months)
  Seller: Goldman Sachs

  Underlying: Canada 5% Bond 2035 (ISIN CA1358XXXXX — Module 5 §4a)
  Face Value: CAD 10,000,000
  Forward Price (fixed today): 97.80 (reflecting today's spot price
    of 98.50 minus 3 months of carry -- coupon income the SELLER
    keeps for holding the bond those 3 months, a mechanic closer to
    Module 6 §1's forward points than to Module 7 §1's contango)

  IN 3 MONTHS, if physically settled:
    Bank Of Toronto pays 10,000,000 x 97.80 / 100 = CAD 9,780,000
    Bank Of Toronto receives CAD 10,000,000 face value of the bond
    via DvP (Module 1 §7) -- an ACTUAL security moves, unlike 4a-4d
```

**Schema:** `IRForwardDebtTrade.avsc` / `IRForwardDebtValuation.avsc`. As Section 3 flagged, the delivered bond itself would separately carry `BondTrade.avsc`-shaped reference data (its ISIN) once/if it settles physically — the forward contract and the underlying security are two different schema-worthy things on the same economic trade.

**Takeaway:** five products, one recurring mechanic — a fixed rate (or fixed price, for 4e) locked in today against something that resets or is observed later, with only the difference (or, for 4e, the full principal if physically settled) actually moving. The FRA's upfront discounting (4b) and the Forward Debt's physical bond delivery (4e) are the two genuine exceptions to "settles like a vanilla IRS," and both are exactly where a reporting system is most likely to get the mechanics wrong.

---

## 5. Trade Lifecycle — Rates-Specific Walkthrough

Applying **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** to the 5Y CAD IRS from Section 4a.

```text
5Y CAD INTEREST RATE SWAP  —  FULL LIFECYCLE
========================================================================

  11:05:00  ORDER — Bank Of Toronto's rates desk instructed to pay
            fixed on CAD 100,000,000, 5Y, vs CORRA

  11:05:12  EXECUTION @ 4.00% fixed vs Goldman Sachs, negotiated
            bilaterally (or on a SEF for a standardized swap —
            Section 07)

  11:05:12  TRADE CAPTURE — Trade ID BOT-IRS-2026-034410

  11:20:00  CONFIRMATION/MATCHING -&gt; MATCHED (via an affirmation
            platform, e.g. MarkitWire — systems-landscape §'s
            trade-confirmation entry)

  CLEARING — for a standardized CAD IRS, novated to a CCP (LCH
            SwapClear — Section 07) shortly after execution, under
            the EMIR/Dodd-Frank mandatory clearing obligation
            (Section 08) — unlike Module 6's FX, which mostly never
            clears via a CCP at all

  DAILY     VARIATION MARGIN — LCH SwapClear marks the swap's value
            daily and moves margin, the same daily-cash-flow pattern
            Module 7 §5 established for exchange-traded futures —
            a genuine convergence between "OTC-negotiated" and
            "daily-margined like an exchange product"

  QUARTERLY SETTLEMENT — every 91-day period, the net fixed-vs-
            floating difference settles (Section 4a's CAD 186,986.30
            worked example), for 5 years / 20 quarters

  POSITION — Bank Of Toronto's Rates book shows one 5Y IRS,
            CAD 100,000,000 notional, marked-to-market between
            quarterly settlements using a discounted cash flow
            model referencing the current CORRA curve (Module 10)

  REPORTING — reported to a Trade Repository (DTCC GTR / REGIS-TR,
            systems-landscape §'s SDR entries) by T+1, carrying a
            UPI (product template) and a fresh UTI (this specific
            trade) — Section 06
```

**Takeaway:** the biggest lifecycle difference from every prior module is that a single trade generates **20 separate quarterly settlement events** over its life, each needing its own net cash flow calculation off a then-current CORRA reset — nothing else in this roadmap has a lifecycle that repeats its own Settlement stage that many times from one Trade Capture event.

---

## 6. Identifiers — UPI, UTI, LEI &amp; the IR Forward Debt Exception

Like FX ([Module 6](./module-06-fx.md) §6), four of the five Rates products have no security to identify — but IR Forward Debt genuinely does, because of Section 3's finding.

| Identifier | Applies to Rates? | Example |
|---|---|---|
| ISIN / CUSIP / Ticker / FIGI | **No** for IRS, FRA, Cap/Floor, Swaption — pure contracts (Section 03) | — |
| ISIN | **Yes, but only for the underlying bond** on IR Forward Debt | `CA1358XXXXX` — identifies the Canada 5% Bond 2035 the forward may deliver, not the forward itself |
| **UPI** | Yes, for all five products, as OTC derivative product templates ([Module 2](./module-02-hierarchy.md) §12) | One UPI for "5Y CAD Fixed-Float IRS referencing CORRA" ([Module 2](./module-02-hierarchy.md) §12's own worked example), reused across every trade sharing that shape |
| **UTI** | Yes — every individual trade across all five products gets its own | A fresh UTI per trade, same logic as every prior module |
| **LEI** | Yes — identifies Bank Of Toronto, Goldman Sachs, and any other counterparty | Entity, not instrument ([Module 4](./module-04-equity.md) §6) |

**Takeaway:** an IR Forward Debt trade record carrying *both* a UPI/UTI (for the forward contract) and an ISIN (for the underlying bond) is the healthy, expected pattern — not the red flag [Module 5](./module-05-fixed-income.md) §6 raised about a UPI appearing on a plain bond record. The difference is that here, two real things genuinely coexist on one trade: a contract and (potentially) the security it delivers.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** Rates is where the "OTC but mandatorily centrally cleared" pattern — only glimpsed as a *growing, optional* trend in [Module 7](./module-07-commodities.md) §7's Commodity Swaps — becomes an outright **regulatory mandate** for standardized products. A vanilla CAD or USD IRS is negotiated OTC (often on a SEF, [Module 2](./module-02-hierarchy.md)'s Swap Execution Facility) but is *required* to clear via a CCP once it's standardized enough to be in-scope.

| Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|
| Interest Rate Swap (standardized) | OTC-negotiated (often via a SEF) | **Mandatorily** centrally cleared (LCH SwapClear, CME) under EMIR/Dodd-Frank — Section 08 |
| FRA | OTC | Mostly bilateral; less standardized than IRS, clearing less commonly mandated |
| Cap/Floor | OTC | Bilateral (the norm) |
| Swaption | OTC | Bilateral (the norm); some standardized swaptions increasingly clear |
| IR Forward Debt | OTC | Bilateral (the norm) — closer to [Module 5](./module-05-fixed-income.md)'s bond-market default than to IRS's clearing mandate |

**Takeaway:** notice this table has the SAME shape as [Module 7](./module-07-commodities.md) §7's Commodities table — one product (IRS here, Futures there) is the exchange-adjacent, centrally-cleared default, while the rest of the asset class stays bilateral OTC. The mechanism differs (Commodities' Futures clear because they're *exchange-traded by design*; Rates' IRS clears because regulation *mandates* it for an OTC-negotiated product) but the resulting pattern — one standardized product carrying the whole asset class's central-clearing volume — repeats.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | How it touches Rates specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Trading-venue rules for standardized IRS (the EU's SEF-equivalent, mandating on-venue execution for liquid swaps), plus transaction reporting |
| **EMIR** | The **defining regulation for this module** — sets the mandatory central clearing obligation for standardized IRS (Section 07), on top of the general OTC derivative reporting obligation ([Module 2](./module-02-hierarchy.md) §07) that also covers FRA, Cap/Floor, Swaption, and IR Forward Debt |
| **SFTR** | Not applicable — none of these five products are securities financing transactions (contrast [Module 5](./module-05-fixed-income.md) §08's repo) |
| **FCA** (UK) | Regulates UK-cleared IRS activity; London/LCH SwapClear is the single largest global venue for IRS clearing, giving the FCA/Bank of England outsized relevance here, similar in spirit to [Module 6](./module-06-fx.md) §08's FX-volume point |
| **CIRO** (Canada) | SRO overseeing Canadian rates derivatives dealer conduct |
| **HKMA** (Hong Kong) | Runs Hong Kong's own mandatory clearing and reporting regime for OTC derivatives including IRS, under the Securities and Futures Ordinance — a genuine regulator role here, distinct from its currency-peg role in FX ([Module 6](./module-06-fx.md) §08) or bond-issuer role in Fixed Income ([Module 5](./module-05-fixed-income.md) §08) |
| **DTCC** | Its **GTR** is one of the largest global trade repositories for IRS/FRA/Swaption reporting, alongside REGIS-TR and UnaVista in the EU/UK |
| **DMO** (UK Debt Management Office) | Relevant here specifically via **IR Forward Debt on Gilts** — the DMO's issuance calendar and outstanding Gilt stock is exactly what a Gilt forward references, the first module where DMO is genuinely on-topic for a *derivative*, not just the underlying cash bond ([Module 5](./module-05-fixed-income.md) §08) |

**Takeaway:** EMIR's mandatory clearing obligation is this module's headline regulatory fact — it's the specific rule that turns Section 07's "OTC but centrally cleared" pattern from a market convention (as it is for Commodity Swaps, [Module 7](./module-07-commodities.md) §07) into a hard legal requirement for standardized IRS, and understanding *why* an IRS trade record shows a CCP counterparty while a Swaption record on the same desk shows a bilateral one comes directly from this section.

---

## 9. Canadian Systems Landscape — Rates Front, Middle &amp; Back Office to Regulatory Reporting

**Definition:** [systems-landscape](./systems-landscape.md) already lays out Canada's *generic* Front/Middle/Back Office architecture; this section specializes it to Rates — which real systems the 5Y CAD IRS from Section 4a actually flows through, end to end, in Canada.

> **Callout — illustrative, not prescriptive.** The vendor/platform names below are representative of real Canadian and global market infrastructure a Rates desk connects to — they are not a claim about Bank Of Toronto's actual internal system choices. Confirm any specific vendor against your own firm's real technology stack.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | Bloomberg; interdealer-broker screens (illustrative: ICAP, Tradition) | OTC execution of the CAD IRS, FRA, Cap/Floor, Swaption |
| **Front Office (exchange-listed)** | **Montreal Exchange (MX)**'s trading engine, "SOLA" | Trades exchange-listed CAD rate derivatives — Government of Canada bond futures ("CGB") and CORRA-based short-term rate futures (the successor to the old BAX/bankers'-acceptance futures, following Section 1's CDOR-to-CORRA transition) |
| **Middle Office** | MarkitWire / ICE Link (illustrative) | OTC derivative trade confirmation/affirmation — matches both counterparties' economic terms (Section 5's Confirmation stage) |
| **Middle Office** | Calypso / Murex (illustrative — same vendor family named in [systems-landscape](./systems-landscape.md)) | Swap valuation, DV01/curve risk ([Module 10](./module-10-valuation.md)) |
| **Back Office / Clearing** | **LCH SwapClear** (London) | Mandatory central clearing for the standardized CAD IRS (Section 7-8) — the dominant global CCP for centrally-cleared interest rate swaps across many currencies, CAD included |
| **Back Office / Clearing** | **CDCC** (Canadian Derivatives Clearing Corporation) | Clears MX-LISTED CAD rate futures (CGB, CORRA futures) domestically — a genuinely different CCP from the one clearing the OTC IRS itself |
| **Regulatory Reporting** | CSA **Multilateral Instrument 96-101**, reported to a CSA-designated Trade Repository (illustrative: DTCC Data Repository (Canada) Ltd, ICE Trade Vault Canada) | Canadian OTC interest rate derivative trade reporting, carrying the UPI/UTI from Section 6 |
| **Oversight** | **Bank of Canada** | Administers/oversees **CORRA** (the floating leg's reference rate, Section 1); oversees CDCC as a systemically important Financial Market Infrastructure |
| **Oversight** | **CIRO** | Dealer-conduct oversight of Bank Of Toronto's rates desk |

```text
CANADA -- 5Y CAD IRS (SECTION 4a) -- FRONT TO BACK, SYSTEM BY SYSTEM
========================================================================

FRONT OFFICE
  Bank Of Toronto's rates desk executes the 5Y CAD IRS against
  Goldman Sachs -- Bloomberg / interdealer-broker screens for the
  OTC-negotiated leg. (The IRS itself isn't MX-listed -- MX's SOLA
  engine instead trades the EXCHANGE-TRADED cousins of this swap:
  CGB Government of Canada bond futures and CORRA futures.)
       |
       v
MIDDLE OFFICE
  MarkitWire / ICE Link -- confirmation/affirmation, matching both
  counterparties' economic terms (Section 5's Confirmation stage)
  Calypso / Murex -- swap valuation, DV01/curve risk (Module 10)
       |
       v
BACK OFFICE / CLEARING
  LCH SwapClear (London) -- mandatory central clearing for this
  standardized CAD IRS under EMIR/Dodd-Frank (Sections 7-8)
  NOTE: the clearing leg for a CAD-denominated swap routes to a CCP
  physically OUTSIDE Canada -- there is no domestic CAD IRS CCP the
  way CDCC clears MX-listed CAD rate FUTURES domestically instead
       |
       v
REGULATORY REPORTING
  CSA Multilateral Instrument 96-101 -> reported to a CSA-designated
  Trade Repository (illustrative: DTCC Data Repository (Canada) Ltd
  or ICE Trade Vault Canada), carrying the UPI/UTI from Section 6
  Bank of Canada -- administers/oversees CORRA, oversees CDCC as a
  systemically important Financial Market Infrastructure
  CIRO -- dealer-conduct oversight of Bank Of Toronto's rates desk
```

**Takeaway:** the CAD IRS's own clearing leg is this section's sharpest lesson — even though the swap is CAD-denominated, negotiated by a Canadian bank, and referencing a Bank-of-Canada-administered rate (CORRA), its *clearing* still routes to LCH SwapClear in London, not to any domestic Canadian CCP. Meanwhile the exchange-listed CAD rate futures MX lists DO clear domestically, at CDCC. A reporting system that assumes "CAD-denominated therefore Canadian CCP" would get this exactly backwards for the OTC product and exactly right for the listed one — the currency of the trade and the location of its clearing are independent facts.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Fixed-rate payer on the IRS; FRA buyer; Cap buyer; Payer Swaption buyer; forward buyer of the Government of Canada bond |
| Goldman Sachs | Floating-rate payer on the IRS; FRA seller; Cap writer; Swaption writer; forward seller of the bond |
| LCH SwapClear / CME | CCPs performing mandatory central clearing for standardized IRS |
| DTCC GTR / REGIS-TR / UnaVista | Trade repositories receiving Rates OTC derivative reports |
| CDCC (Canadian Derivatives Clearing Corporation) | Clears MX-listed CAD rate futures (CGB, CORRA futures) domestically (Section 9) |
| Bank of Canada | Administers/oversees CORRA; oversees CDCC as a systemically important FMI (Section 9) |
| CIRO | SRO overseeing Canadian rates derivatives dealer conduct (Section 8, Section 9) |

---

## Recap Quiz — Numbers Edition

1. On the IRS in Section 4a, if CORRA had instead averaged 4.30% over the quarter (+0.05% spread = 4.35% floating), who pays whom, and how much net cash moves?
2. Why does the FRA in Section 4b discount its settlement amount before paying it, while the IRS in Section 4a never discounts its quarterly settlement?
3. On the Cap in Section 4c, if CORRA at Caplet #5's reset came in at exactly 5.00% (precisely at the strike), what is the payout, and why?
4. Using Section 4d's Swaption, explain in one sentence why "the prevailing swap rate is 4.75%, above the 4.25% strike" makes exercising favourable for a *Payer* Swaption specifically — would the same rate move favour exercise for a *Receiver* Swaption?
5. A trade record shows both a UPI/UTI and an ISIN populated. Using Section 6, is this more likely an IR Forward Debt trade or a plain Interest Rate Swap trade — and why doesn't the presence of an ISIN alone answer that question for you (hint: revisit [Module 5](./module-05-fixed-income.md) §6's warning)?
6. Why does Section 7 describe the Rates asset class's IRS-clears-by-mandate pattern as mechanically different from, yet structurally identical to, [Module 7](./module-07-commodities.md)'s Commodities Futures-clear-by-design pattern?
7. Which single regulation is responsible for a standard CAD IRS ending up facing LCH SwapClear as its counterparty instead of Goldman Sachs directly, and what does that regulation NOT require for a Swaption on the same desk?
8. Using Section 9, why does a CAD-denominated IRS's clearing leg route to a CCP physically located outside Canada, while an MX-listed CAD rate future clears domestically — and which Canadian institution clears the latter?

Answer whenever ready — then move to **[Module 9](./module-09-credit.md): Credit**, the next stop on the Asset Class Roadmap.

---

## Answers — Recap Quiz, with Explanations

**1. Goldman Sachs pays Bank Of Toronto CAD 87,739.73 — a reversal of direction from the worked example.**

```text
Bank Of Toronto's FIXED leg = 100,000,000 x 4.00% x 91/365 = CAD 997,260.27
Goldman Sachs' FLOATING leg = 100,000,000 x 4.35% x 91/365 = CAD 1,085,000.00
Net: Goldman Sachs PAYS Bank Of Toronto = 1,085,000.00 - 997,260.27 = CAD 87,739.73
```

Whenever the floating rate settles above the fixed rate, the fixed-rate payer (Bank Of Toronto) becomes the net *receiver* — the same fixed-vs-floating netting mechanic [Module 1](./module-01-fundamentals.md) §8 established, just running in the opposite direction because CORRA came in higher this quarter.

**2. Because an FRA's single cash flow is deliberately paid at the START of its reference period (once the rate is already known, there's no reason to wait for the period to end), so it must be present-valued back to that earlier date — an IRS's quarterly cash flow, by contrast, is paid at the END of each period it covers, which is already the "natural" date, needing no further discounting.**

Section 4b's IT/reporting angle states this directly: this is the single field most likely to be coded wrong by someone porting IRS valuation logic to FRAs without accounting for the different settlement-timing convention.

**3. The payout is CAD 0 — at exactly the strike, there's no positive difference for the caplet to pay on.**

`Payout = Notional x (Rate - Strike) x Days/365` — when Rate equals Strike, `(Rate - Strike) = 0`, so the payout is zero regardless of notional or day count. This mirrors [Module 6](./module-06-fx.md) §4d's Q5 finding for the at-the-money FX option: hitting the strike exactly produces the boundary case of the payoff, not a partial one.

**4. A Payer Swaption gives the right to PAY the fixed strike rate (4.25%) and RECEIVE floating — if the market's prevailing fixed rate has risen to 4.75%, paying only 4.25% (below market) is favourable, so exercise makes sense. A Receiver Swaption would want the OPPOSITE move — it needs the prevailing rate to fall BELOW the strike to be favourable, so the same 4.75% outcome would make a Receiver Swaption worthless, not exercised.**

Section 4d's Scenario A specifically exercises because entering the underlying swap at the old, lower strike beats entering a fresh swap at the new, higher market rate — but that logic only holds for the side that wants to lock in the LOWER of the two rates (the fixed-rate payer). A Receiver Swaption holder wants to lock in the HIGHER rate as a receiver, so a market rate *below* strike would be what favours it instead.

**5. More likely an IR Forward Debt trade — because Section 6 established that ISIN only ever appears on Rates products for the underlying bond an IR Forward Debt may deliver; a plain IRS never carries one at all. This does NOT contradict [Module 5](./module-05-fixed-income.md) §6's warning, because that warning was specifically about a UPI appearing on a Fixed Income module's BOND record (i.e., ISIN + UPI on the same schema/record as a red flag) — here, the ISIN and UPI/UTI are describing two conceptually different things on one trade (the forward contract and its potential underlying delivery), which Section 3 and Section 6 both establish as the expected, healthy pattern for this specific product.**

The distinguishing test is exactly the one Section 3's takeaway sets up: is the ISIN on the SAME schema entity as the derivative fields (suspicious, per [Module 5](./module-05-fixed-income.md) §6), or is it clearly scoped to a distinct underlying-security reference within an IR Forward Debt record (expected, per this module)? Context — which module's product family the record belongs to — decides which reading applies.

**6. Mechanically different because Commodities Futures clear via a CCP purely because they're exchange-traded BY DESIGN from the moment of execution ([Module 7](./module-07-commodities.md) §05 — a structural/venue fact), while Rates IRS clear via a CCP because EMIR/Dodd-Frank LEGALLY REQUIRE it for standardized OTC-negotiated swaps (Section 08 — a regulatory fact, not a venue fact) — but structurally identical because in both asset classes, exactly one standardized product ends up carrying the CCP-cleared volume for the whole asset class while every other product in the same asset class stays predominantly bilateral.**

Section 7's takeaway draws this comparison explicitly: same resulting table shape (one product centrally cleared, the rest bilateral OTC), reached by two completely different causal routes — venue design in Commodities, regulatory mandate in Rates.

**7. EMIR (specifically its mandatory central clearing obligation for standardized IRS, Section 08) is responsible. EMIR does NOT require the same mandatory clearing for a Swaption on the same desk — Section 07's table shows Swaptions remain predominantly bilateral, since they're less standardized than a vanilla IRS and generally sit outside the specific product scope EMIR's clearing mandate targets.**

Section 8's takeaway calls this out as the module's headline regulatory fact: EMIR's mandate is scoped to specific, standardized product types (vanilla IRS being the clearest example), not to "Rates derivatives" as a blanket category — which is exactly why two products from the same desk, the same counterparty, and the same broad asset class can have completely different Clearing-stage outcomes in Section 5's lifecycle walkthrough.

**8. Because LCH SwapClear (London) is the dominant global CCP for centrally-cleared interest rate swaps across many currencies, CAD included — Canadian banks clear a CAD IRS there as clearing members or via client clearing, rather than at any domestic Canadian CCP. CDCC, by contrast, clears MX-listed CAD rate futures (CGB, CORRA futures) domestically, because those products trade on Montreal Exchange, a Canadian venue.**

Section 9's takeaway states this directly: currency denomination and clearing location are independent facts — a CAD-denominated OTC swap's clearing follows the CCP the product family uses globally (LCH SwapClear), while an exchange-listed CAD future's clearing follows the CCP attached to its listing venue (CDCC, attached to MX). A system that assumes "CAD-denominated therefore Canadian CCP" gets the OTC case backwards.

---

[← Back to Index](./index.md)
