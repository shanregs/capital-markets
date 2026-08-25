# Module 6 — FX

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Asset Class Roadmap 3 of 6** — Equity → Fixed Income → **FX** → Commodities → Rates → Credit.

---

## 1. What Is FX? — Theory &amp; Terminology

**Definition:** FX (Foreign Exchange) is the exchange of one currency for another at an agreed rate. There is no issuer, no maturity in the bond sense, no ownership claim — just two counterparties agreeing that one currency is worth a certain amount of the other, right now or on some future date. FX is the largest financial market in the world by daily turnover, and unlike Equity or Fixed Income, **every FX product is fundamentally a contract to exchange currency** — Section 3 explores exactly what that means for "what's being traded."

| Term | Meaning |
|---|---|
| Currency Pair | Two currencies quoted against each other, e.g. USD/CAD — the first is the *base* currency, the second is the *quote* currency |
| Exchange Rate | How much of the quote currency one unit of the base currency costs — e.g. USD/CAD = 1.3500 means 1 USD costs CAD 1.35 |
| Spot Rate | Today's exchange rate, for near-immediate settlement ([Module 1](./module-01-fundamentals.md) §6) |
| Forward Points | The adjustment added to (or subtracted from) the spot rate to get a forward rate, driven by the interest rate differential between the two currencies ([Module 2](./module-02-hierarchy.md) §10) |
| Notional | The currency amount referenced by the contract — in FX, this genuinely does settle for Spot/Forward (unlike an IRS's notional, [Module 1](./module-01-fundamentals.md) §8) |

**Takeaway:** FX sits in an unusual spot in the Asset Class Roadmap — it has no "security" the way Equity (a share) or Fixed Income (a bond) does. Every single FX product, from the simplest same-day trade to the most complex option, is a contractual agreement between two parties. Section 3 makes this explicit.

---

## 2. What Are The Products? — Spot, Forward, Swap &amp; Options

```text
FX
│
├── FX Spot       (near-immediate exchange, T+1/T+2 per currency pair)
├── FX Forward     (exchange on a later agreed date, rate fixed today — Module 2 §10)
├── FX Swap        (a spot leg + a forward leg, opposite directions)
└── FX Options     (the RIGHT, not obligation, to exchange at a fixed rate)
```

| Product | What it is |
|---|---|
| **FX Spot** | An agreement to exchange two currencies almost immediately — T+1 for most pairs, T+2 for a few ([Module 1](./module-01-fundamentals.md) §6) |
| **FX Forward** | An agreement to exchange two currencies on a specified future date, at a rate fixed today ([Module 2](./module-02-hierarchy.md) §10's worked example) |
| **FX Swap** | Two linked exchanges in opposite directions — a "near leg" (usually spot) and a "far leg" (a forward) — economically a short-term currency-denominated loan, not a long-dated interest rate product |
| **FX Option** | A contract giving the buyer the right, not the obligation, to exchange currency at a fixed strike rate by/at expiry, for a premium |

> **A naming trap:** "FX Swap" is **not** the same thing as a **Cross-Currency Swap** — a longer-dated product with periodic interest payments exchanged in two currencies over years, which behaves much more like the Interest Rate Swap from [Module 1](./module-01-fundamentals.md) §8 and belongs properly to [Module 8](./module-08-rates.md) (Rates). An FX Swap is short-dated (commonly overnight to one year) and has no periodic interest payments at all — just two exchanges of principal.

**Takeaway:** three of these four products (Spot, Forward, Swap) are really the same underlying mechanic — exchanging currency at an agreed rate on an agreed date — differing only in *when* that date is and how many exchanges happen. The Option is the odd one out: a contingent right, not a firm obligation, which is exactly the same buyer/seller asymmetry [Module 4](./module-04-equity.md) §4d established for equity options.

---

## 3. What Exactly Is Being Traded? — Contracts, All The Way Down

**Definition:** unlike [Module 4](./module-04-equity.md) (Equity: security vs. contract) or [Module 5](./module-05-fixed-income.md) (Fixed Income: security vs. financing contract), FX has **no security to contrast against at all**. Every product in Section 2 is a contract — Spot and Forward are the same mechanic at different points on a timeline, not a "real thing" vs. a "derivative of a real thing."

```text
FX PRODUCTS  —  ALL CONTRACTS, NO "SECURITY" EQUIVALENT
========================================================================

  FX Spot:     an agreement to exchange currencies almost immediately
               (T+1/T+2, per currency pair) -- the shortest-dated
               point on the timeline

  FX Forward:  an agreement to exchange currencies on a LATER agreed
               date, at a rate fixed TODAY -- same mechanic as Spot,
               just further out on the same timeline

  FX Swap:     TWO linked exchanges in opposite directions (near leg
               + far leg) -- effectively a short-term FX-funding
               transaction, not a new mechanic

  FX Option:   a RIGHT (not obligation) to exchange currencies at a
               fixed rate, for a premium -- the one genuinely
               different mechanic in the group

  -&gt; Unlike Equity (Module 4) or Fixed Income (Module 5), there is
     no underlying "security" any of these four reference. FX is
     contracts all the way down — even Spot is just the shortest-
     dated point on the same continuum as Forward.
```

**Takeaway:** this reframes "settlement" for FX entirely — [Module 1](./module-01-fundamentals.md) §6 already showed FX Spot settling T+1/T+2 like a security would, but it never was one. What actually happens at settlement (Section 5) is two currency payments exchanging hands, not a security moving from a seller's CSD account to a buyer's.

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

### 4a. FX Spot

```text
FX SPOT  —  USD/CAD
========================================================================

  Buyer:  Bank Of Toronto (buying USD)
  Seller: Goldman Sachs

  Trade: Bank Of Toronto buys USD 10,000,000 @ Spot Rate 1.3500
  Bank Of Toronto pays  = 10,000,000 x 1.3500 = CAD 13,500,000
  Bank Of Toronto receives = USD 10,000,000
  Settlement: T+1 (Module 1 §6 — USD/CAD is one of the pairs that
              settles T+1 rather than the more common T+2)
```

### 4b. FX Forward

Reusing [Module 2](./module-02-hierarchy.md) §10's worked example directly:

```text
FX FORWARD  —  Bank Of Toronto needs USD 10,000,000 in 3 months
========================================================================

  Today's Spot Rate (USD/CAD): 1.3500
  3-Month Forward Points:      +0.0045
  3-Month Forward Rate:        1.3545

  Bank Of Toronto agrees TODAY with Goldman Sachs:
    In 3 months, pays CAD 13,545,000, receives USD 10,000,000
    (10,000,000 x 1.3545 = 13,545,000)
  -&gt; No cash moves today. Only the RATE is locked in now.
```

### 4c. FX Swap

```text
FX SWAP  —  Bank Of Toronto needs USD today, has surplus USD in 1 month
========================================================================

  NEAR LEG (today, effectively FX Spot):
    Bank Of Toronto buys USD 10,000,000 @ Spot Rate 1.3500
    Pays CAD 13,500,000 today, receives USD 10,000,000 today

  FAR LEG (in 1 month, a Forward in the opposite direction):
    Bank Of Toronto sells USD 10,000,000 @ Forward Rate 1.3512
    (1-month forward points +0.0012, reflecting the CAD/USD
    interest rate differential over that month)
    Receives CAD 13,512,000 in 1 month, pays back USD 10,000,000

  NET EFFECT:
    Bank Of Toronto had the use of USD 10,000,000 for exactly one
    month, and the two legs together cost:
      13,512,000 - 13,500,000 = CAD 12,000
    -&gt; this CAD 12,000 is effectively the FUNDING COST of borrowing
       USD for a month via FX markets, priced entirely through the
       forward points — no separate "interest payment" line item
       the way a loan or a Cross-Currency Swap would have.
```

### 4d. FX Option

```text
FX OPTION  —  Bank Of Toronto's client hedges a future USD payable
========================================================================

  Buyer: Bank Of Toronto's client (needs to buy USD in 3 months)
  Seller (writer): Bank Of Toronto

  Trade: USD Call / CAD Put, Notional USD 5,000,000
         Strike 1.3600 CAD/USD, Expiry 3 months
         Premium: 0.0180 CAD per USD notional
  Premium Paid = 5,000,000 x 0.0180 = CAD 90,000

  AT EXPIRY -- Scenario A: Spot moves to 1.3800 (USD stronger)
    Option is in-the-money: exercising saves
      (1.3800 - 1.3600) x 5,000,000 = CAD 100,000 vs. buying at spot
    Net Benefit = 100,000 - 90,000 (premium) = CAD 10,000

  AT EXPIRY -- Scenario B: Spot moves to 1.3400 (USD weaker)
    Option expires WORTHLESS — buying USD at spot (1.3400) is
    cheaper than exercising at strike (1.3600)
    Maximum Loss = the CAD 90,000 premium, exactly as Module 4 §4d
    established for the AAPL call — never more than premium paid.
```

**Takeaway:** notice the FX Swap (4c) is really just a Spot (4a) and a Forward (4b) bolted together — nothing new mechanically, just two of the same product executed simultaneously in opposite directions. The Option (4d) is the only one of the four with the asymmetric payoff [Module 4](./module-04-equity.md) already established for equity options.

---

## 5. Trade Lifecycle — FX-Specific Walkthrough, and CLS/PvP

Applying **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** to the FX Spot trade from Section 4a.

```text
USD/CAD SPOT  —  FULL LIFECYCLE
========================================================================

  14:02:00  ORDER — Bank Of Toronto's treasury desk needs USD 10,000,000

  14:02:03  EXECUTION @ Spot Rate 1.3500 vs Goldman Sachs

  14:02:03  TRADE CAPTURE — Trade ID BOT-FX-2026-018802

  14:10:00  CONFIRMATION/MATCHING -&gt; MATCHED (bilateral, no CCP —
            Section 07)

  Settlement Date = T+1
            SETTLEMENT via CLS (Continuous Linked Settlement) —
            see below

  POSITION — Bank Of Toronto's USD cash position increases by
            USD 10,000,000; CAD cash position decreases by
            CAD 13,500,000

  VALUATION / P&amp;L — the open FX position is marked to the current
            spot rate daily until the underlying USD is used/sold:
              Trade day:        USD 10,000,000 @ 1.3500 = CAD 13,500,000
              One week later:   USD 10,000,000 @ 1.3550 = CAD 13,550,000
              Unrealized P&amp;L = 13,550,000 - 13,500,000 = +CAD 50,000

  REPORTING — FX Spot is generally OUT OF SCOPE for derivative
            transaction reporting (Section 08) — it isn't treated
            as a derivative at all under EMIR/CFTC rules
```

### CLS — FX's answer to DvP

**Definition:** [Module 1](./module-01-fundamentals.md) §7 established DvP (Delivery versus Payment) as the mechanism that ties a security's two settlement legs together so neither side is ever left exposed. FX has no security to deliver — instead, **CLS (Continuous Linked Settlement)**, owned and used by the major global banks (including, illustratively, Bank Of Toronto and Goldman Sachs), performs the FX equivalent: **PvP (Payment versus Payment)**.

```text
PvP via CLS  —  why FX settlement risk (Herstatt risk) doesn't happen
between CLS members
========================================================================

  WITHOUT PvP (the old failure mode, named after Bankhaus Herstatt's
  1974 collapse):
    Bank Of Toronto pays CAD 13,500,000 in Toronto's business hours.
    Goldman Sachs is due to pay USD 10,000,000 in New York hours,
    LATER THE SAME DAY. If Goldman Sachs fails in between, Bank Of
    Toronto has paid and received nothing back.

  WITH CLS (PvP):
    Both legs are settled simultaneously across CLS's own books,
    covering 18+ major currencies including USD and CAD. Either
    BOTH payments complete, or NEITHER does — mirroring exactly what
    DvP does for securities (Module 1 §7), just for two currency
    payments instead of a security + cash.
```

**Takeaway:** CLS is not a CCP — it doesn't novate trades or take on counterparty credit risk the way NSCC or a swap CCP does ([Module 3](./module-03-trade-lifecycle.md) §6). It's pure settlement-risk mitigation, the FX-specific answer to the exact problem DvP solves for securities.

---

## 6. Identifiers — Currency Pairs, UPI, UTI &amp; LEI

FX mostly doesn't use the identifiers Modules 4 and 5 relied on — there's no ISIN, CUSIP, SEDOL, Ticker, or FIGI for a currency pair trade, because (Section 3) there's no security to identify.

| Identifier | Applies to FX? | Example |
|---|---|---|
| ISIN / CUSIP / SEDOL / Ticker / FIGI | **No** — these identify securities; FX has none | — |
| Currency Pair (ISO 4217 codes) | Yes — the closest thing FX has to an "instrument" identifier | `USD/CAD` |
| **UPI** | Yes, for FX Forwards, Swaps, and Options reported as OTC derivatives ([Module 2](./module-02-hierarchy.md) §12) | One UPI for "USD/CAD, Forward, Deliverable" — reused across many Forward trades, same logic as [Module 2](./module-02-hierarchy.md) §12's IRS example |
| **UTI** | Yes — every individual FX Forward/Swap/Option trade gets its own | A fresh UTI per trade, same as [Module 2](./module-02-hierarchy.md) §12 |
| **LEI** | Yes — identifies Bank Of Toronto, Goldman Sachs, and any client as counterparties | Entity identifier, not instrument ([Module 4](./module-04-equity.md) §6) |

**Takeaway:** if a reporting record shows a currency pair but no ISIN/CUSIP, that's completely normal for FX — the absence of a security identifier isn't a data gap, it's the correct state for an asset class that has no security at all (Section 3).

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** the overwhelming majority of FX trades — Spot, Forward, Swap, and most Options — are **OTC and bilateral**, settled via CLS's PvP mechanism (Section 5) rather than novated to a CCP. A smaller but growing share of Forwards and NDFs clear centrally, and listed FX futures/options exist on exchange as a separate, smaller market.

| Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|
| FX Spot | OTC | Bilateral (settled via CLS's PvP, not a CCP — Section 05) |
| FX Forward | OTC | Mostly bilateral; a growing share clears via LCH ForexClear or CME (especially NDFs) |
| FX Swap | OTC | Bilateral (the norm) |
| FX Option | OTC | Mostly bilateral |
| FX Futures/Options (listed) | **Exchange-traded** (e.g. CME) | Centrally cleared — a smaller, separate market from OTC FX, not in this module's four core products but worth knowing exists |

**Takeaway:** FX is structurally closer to [Module 5](./module-05-fixed-income.md)'s Fixed Income (bilateral OTC as the default) than to [Module 4](./module-04-equity.md)'s Equity (centrally cleared as the default) — but FX adds a third mechanism, CLS's PvP, that neither of those two asset classes has: settlement-risk mitigation without a CCP's counterparty-risk mitigation. Bilateral counterparty risk ([Module 3](./module-03-trade-lifecycle.md) §6) and settlement risk (this module) are two different risks, and CLS only solves the second one.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | How it touches FX specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Covers FX Forwards, Swaps, and Options used for investment purposes; physically-settled FX Forwards used purely for commercial/payment purposes often sit outside MiFID's scope — a genuinely contested boundary in EU rulemaking |
| **EMIR** | Applies to FX **Forwards, Swaps, and Options** as OTC derivatives ([Module 2](./module-02-hierarchy.md) §07) — but **not** to FX Spot. The regulatory boundary between "Spot" and "Forward" is defined by a settlement-date threshold (roughly the standard spot cycle for that currency pair, T+1/T+2 for most, longer for some emerging-market pairs) — anything settling within that window is Spot and out of scope; anything beyond it is a Forward and in scope |
| **SFTR** | Does **not** apply to FX Swaps, despite the name similarity to "swap" in a repo/securities-lending sense ([Module 5](./module-05-fixed-income.md) §08) — SFTR is specifically for securities/commodities financing, not currency exchange. A genuinely easy mix-up worth flagging explicitly |
| **FCA** (UK) | Outsized relevance for FX specifically — London handles roughly 40%+ of global FX trading volume (BIS Triennial Survey), making the FCA one of the most consequential FX conduct regulators globally, not just a regional one |
| **CIRO** (Canada) | SRO overseeing Canadian FX dealer conduct, alongside the Bank of Canada's broader market-infrastructure oversight |
| **HKMA** (Hong Kong) | A third distinct role for HKMA across this roadmap so far: for FX specifically, the HKMA directly intervenes in the market to defend the **Hong Kong Dollar's peg to the US Dollar** under the Linked Exchange Rate System — a direct market-participant role, not just a supervisory one |
| **DTCC** | DTCC's **GTR (Global Trade Repository)** accepts FX derivative reports (Forwards, Swaps, Options) alongside other asset classes, feeding CFTC reporting requirements ([systems-landscape](./systems-landscape.md) §05) |
| **DMO** (UK Debt Management Office) | Not FX-relevant — issues Gilts ([Module 5](./module-05-fixed-income.md) §08), included here for completeness only |

**Takeaway:** HKMA has now shown up in a meaningfully different role in all three asset-class modules so far — a securities conduct footnote in Equity ([Module 4](./module-04-equity.md)), a direct bond issuer in Fixed Income ([Module 5](./module-05-fixed-income.md)), and now a direct market participant defending a currency peg in FX. Same institution, three genuinely different jobs depending on the asset class in front of you.

---

## 9. Canadian Systems Landscape — FX Front, Middle &amp; Back Office to Regulatory Reporting

**Definition:** [systems-landscape](./systems-landscape.md) §4 already named Canada's *generic* Front/Middle/Back Office architecture (Bloomberg AIM, CDSX, CDCC, Lynx). This section specializes that architecture to FX specifically — an asset class whose back office looks nothing like an equity or bond trade's, because there's no CSD leg at all (Section 3: FX has no security), only two currency payments settling via CLS's PvP mechanism (Section 5).

> **Callout — illustrative, not prescriptive.** The system names below (EBS, Refinitiv, 360T, Murex, Calypso) are representative of the real venues and vendors Canadian bank FX desks connect to — not a claim about which specific systems Bank Of Toronto itself runs internally. Treat this section the same way [systems-landscape](./systems-landscape.md) treats its own vendor lists: the *category* of system at each stage is the durable fact; the specific product name is illustrative.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | **EBS**, **Refinitiv Matching/FXall**, **360T** — the major interbank/multi-dealer FX venues; plus Bank Of Toronto's own eFX pricing/aggregation platform for client flow | Price discovery and execution — where Section 4's Spot/Forward/Swap trades actually get dealt |
| **Middle Office** | Trade capture and confirmation via SWIFT MT300-type confirmation messages; risk systems (Murex, Calypso FX module — illustrative) | Confirms both sides agree on rate/notional/value date; feeds daily FX position/P&amp;L (Section 5) |
| **Back Office / Settlement** | **CLS (Continuous Linked Settlement)** for CLS-eligible pairs (CAD is CLS-eligible, Section 5); **Lynx** (Payments Canada's large-value payment system) carries the CAD leg into CLS; non-CLS-eligible trades fall back to correspondent banking / SWIFT payment messages instead | PvP settlement (Section 5) — or, for ineligible pairs, the pre-CLS correspondent-banking mechanism CLS was built to replace |
| **Regulator / Trade Repository** | CSA **Multilateral Instrument 96-101** governs FX Forwards/Swaps/Options as OTC derivatives, reported to a CSA-designated Trade Repository (e.g. DTCC Data Repository (Canada) Ltd or ICE Trade Vault Canada — illustrative); **Bank of Canada** oversees CLS as a systemically important FMI; **CIRO** covers dealer conduct | FX Spot is **out of scope** of MI 96-101 derivative reporting, the same Spot/Forward boundary Section 8's EMIR row already draws |

```text
CANADA  —  FX FORWARD, FRONT OFFICE TO REGULATORY REPORTING
========================================================================
  (Section 4b's trade: Bank Of Toronto needs USD 10,000,000 in 3
  months, forward rate 1.3545, vs. Goldman Sachs)

  FRONT OFFICE
    Bank Of Toronto's FX desk deals the Forward via Refinitiv
    Matching/FXall (or a voice trade captured into the same system)
    -> Trade ID BOT-FX-2026-018815

  MIDDLE OFFICE
    Trade booked into Murex/Calypso's FX module for risk;
    confirmation exchanged with Goldman Sachs via a SWIFT MT300-type
    message -> Status: MATCHED (Section 05's Confirmation stage)

  BACK OFFICE / SETTLEMENT  (value date, 3 months out)
    USD/CAD is CLS-eligible (Section 05) --
      Bank Of Toronto's CAD 13,545,000 leg moves via LYNX into CLS
      Goldman Sachs' USD 10,000,000 leg moves via its own correspondent
      into CLS
      CLS settles BOTH legs simultaneously (PvP) -- neither side is
      exposed the way Herstatt risk describes (Section 05)

  REGULATORY / TRADE REPOSITORY
    This is an FX FORWARD, not Spot -&gt; in scope for CSA MI 96-101
    -&gt; reported by T+1 to a CSA-designated Trade Repository (DTCC
    Data Repository (Canada) Ltd / ICE Trade Vault Canada)
    Bank of Canada: oversees CLS itself as a systemically important
    FMI (not this individual trade)
    CIRO: oversees Bank Of Toronto's dealer conduct on the trade
```

**Takeaway:** the FX back office is structurally the odd one out across this roadmap so far — Equity and Fixed Income both route through a CSD (CDSX) for the security leg; FX has no security leg at all, so CLS's PvP mechanism *is* the entire back office, with Lynx supplying only the domestic CAD payment rail underneath it. A reporting system built assuming "settlement = a CSD movement" (true for Modules 4–5) will have no CSD event to point to for FX at all — CLS's settlement confirmation is the only settlement record that exists.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Buyer of USD spot and forward; both legs of its own FX Swap; option writer for its client |
| Goldman Sachs | Seller of USD on the spot and forward trades |
| Bank Of Toronto's client | Buyer of the USD Call option, hedging a future USD payable |
| CLS (Continuous Linked Settlement) | PvP settlement infrastructure eliminating FX settlement (Herstatt) risk between member banks |
| LCH ForexClear / CME | Venues/CCPs where a growing share of FX Forwards and NDFs centrally clear |
| Bank of Canada | Oversees CLS as a systemically important FMI from a Canadian financial-stability perspective (Section 9) |
| CIRO (Canada) | SRO overseeing Bank Of Toronto's FX dealer conduct (Section 9) |
| CSA-designated Trade Repository (e.g. DTCC Data Repository (Canada) Ltd / ICE Trade Vault Canada) | Receives the FX Forward's MI 96-101 report (Section 9) |

---

## Recap Quiz — Numbers Edition

1. On the FX Swap in Section 4c, if the 1-month forward points had instead been -0.0020 (CAD strengthening relative to USD over the month) instead of +0.0012, what would the far-leg forward rate be, and would Bank Of Toronto's funding cost be higher or lower than the CAD 12,000 in the worked example?
2. Why does FX Spot not need "the same identifier a bond or share would have" (Section 6), and is that a data-quality gap or expected behavior?
3. A trade record shows a UPI and a UTI for an FX product but no ISIN. Is that trade record more likely broken, or is that exactly what a correctly-populated FX Forward trade record should look like?
4. Explain in one sentence why CLS is described in Section 5 as "not a CCP" even though it clearly reduces risk on every trade that settles through it.
5. Bank Of Toronto's client's USD Call option (Section 4d) is struck at 1.3600 with a CAD 90,000 premium. If spot at expiry is exactly 1.3600 (unchanged from strike), what is the net outcome, and why does this differ from both Scenario A and Scenario B?
6. Using Section 8, is a plain FX Spot trade between Bank Of Toronto and Goldman Sachs reportable under EMIR? What about the FX Forward from Section 4b?
7. Why might a regulator care more about the FCA's oversight of FX specifically than it would about, say, CIRO's oversight of FX — based on the scale point made in Section 8?
8. Using Section 9, the FX Forward from Section 4b settles via CLS while a Bank Of Toronto Canadian equity trade settles via CDSX ([systems-landscape](./systems-landscape.md) §4). Why can't a reporting system built around "settlement = a CSD book-entry movement" simply be reused unchanged for the FX Forward?

Answer whenever ready — then move to **[Module 7](./module-07-commodities.md): Commodities**, the next stop on the Asset Class Roadmap.

---

## Answers — Recap Quiz, with Explanations

**1. Far-leg forward rate = 1.3500 - 0.0020 = 1.3480. Bank Of Toronto's funding cost would flip to a GAIN, not a cost.**

```text
Far leg: sell USD 10,000,000 @ 1.3480, receive CAD 13,480,000
Net effect = 13,480,000 - 13,500,000 = -CAD 20,000
```

Bank Of Toronto would receive CAD 20,000 *less* than it paid on the near leg — meaning it actually profits CAD 20,000 from holding the swap rather than paying a funding cost, because negative forward points (CAD getting *cheaper* forward, or equivalently a swap point flip) work in the opposite direction from the original +0.0012 example. The sign of the forward points, driven by the CAD/USD rate differential, directly determines whether the FX Swap is a net cost or a net benefit to whoever's borrowing the foreign currency.

**2. FX Spot has no such identifier because it has no security to identify at all (Section 3) — this is expected behavior, not a gap.**

Section 6's takeaway makes this explicit: ISIN/CUSIP/Ticker exist specifically to identify securities ([Module 2](./module-02-hierarchy.md) §2, [Module 4](./module-04-equity.md) §6, [Module 5](./module-05-fixed-income.md) §6). FX products, including Spot, aren't securities — they're contracts to exchange currency, so the absence of a security identifier is exactly correct, not a missing field.

**3. That's exactly what a correctly-populated FX Forward trade record should look like.**

Section 6's table states this directly: UPI and UTI both apply to FX Forwards, Swaps, and Options (as OTC derivatives, [Module 2](./module-02-hierarchy.md) §12), while ISIN never applies to FX at all. A record with UPI/UTI populated and ISIN blank is the healthy pattern for FX, not a red flag — this contrasts with [Module 5](./module-05-fixed-income.md) §6's warning about the *opposite* pattern (UPI populated on a bond record being suspicious).

**4. Because CLS only solves settlement-timing risk (ensuring both currency payments happen together, PvP) — it never becomes a party to the trade itself and doesn't absorb either side's counterparty credit risk the way a CCP's novation does.**

Section 5's takeaway states this precisely: CLS mirrors DvP's mechanical protection ([Module 1](./module-01-fundamentals.md) §7) but stops there — it isn't a counterparty risk mitigant like the CCP novation described in [Module 3](./module-03-trade-lifecycle.md) §6, just a settlement-risk mitigant.

**5. The option expires exactly at-the-money — technically worthless to exercise (no intrinsic value, since strike equals spot), so the buyer lets it lapse and loses the full CAD 90,000 premium, the same maximum-loss outcome as Scenario B, not a middle ground between A and B.**

At spot = strike = 1.3600, there is zero benefit to exercising (buying at 1.3600 via the option vs. buying at 1.3600 in the spot market is identical), so the CAD 90,000 premium is a pure loss — exactly Scenario B's outcome even though spot didn't fall all the way to 1.3400. This illustrates that an option's payoff has a hard breakeven at strike-plus-premium-equivalent, not a smooth scaling from Scenario B up to Scenario A.

**6. No, the FX Spot trade is not reportable under EMIR. The FX Forward from Section 4b IS reportable under EMIR.**

Section 8's table states this directly: EMIR excludes FX Spot by definition (it's outside the settlement-date threshold that defines a "derivative" for FX purposes) but explicitly covers FX Forwards, Swaps, and Options. The Section 4b Forward, settling 3 months out, is squarely inside EMIR's scope; the Section 4a Spot, settling T+1, is not.

**7. Because London (and therefore the FCA's jurisdiction) handles roughly 40%+ of ALL global FX trading volume (BIS Triennial Survey) — a scale that makes FCA oversight consequential to the entire global FX market, not just to UK-domiciled activity, whereas CIRO's remit is scoped to Canadian dealer conduct specifically.**

Section 8 flags this explicitly as "outsized relevance" — this is a genuinely different kind of importance than the conduct-regulator role CIRO or the FCA itself plays for, say, domestic equity trading ([Module 4](./module-04-equity.md) §8), where each regulator's importance is roughly proportional to its own domestic market size.

**8. Because there is no CSD leg to reuse — FX has no security (Section 3), so there is no book-entry movement for a reporting system to point to at all; CLS's PvP confirmation is the entire settlement record, not a variant of a CDSX-style event.**

Section 9's takeaway states this directly: Equity and Fixed Income both route through a CSD (CDSX) for the security leg, but FX's back office is structurally different — CLS's PvP mechanism *is* the whole back office, with Lynx only supplying the domestic CAD payment rail underneath it. A reporting system that assumes every settled trade has a corresponding CSD movement will have nothing to point to for an FX trade, and needs a genuinely separate settlement-event model for FX rather than a parameterized version of the equity/bond one.

---

[← Back to Index](../../index.md)
