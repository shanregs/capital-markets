# Module 7 — Commodities

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Asset Class Roadmap 4 of 6** — Equity → Fixed Income → FX → **Commodities** → Rates → Credit.

---

## 1. What Is Commodities? — Theory &amp; Terminology

**Definition:** A Commodity is a physical, fungible good — one barrel of WTI crude is interchangeable with any other barrel of the same grade, the same way one CAD is interchangeable with any other CAD ([Module 6](./module-06-fx.md) §1), but *unlike* a share ([Module 4](./module-04-equity.md) §1) where each certificate represents a specific claim on a specific company. Commodities span **Energy** (oil, gas, power), **Metals** (gold, silver, copper — split into *Precious* and *Base*), and **Agriculture** (wheat, corn, coffee). What makes Commodities genuinely different from every asset class studied so far: the underlying thing is **physical** — it can spoil, needs to be stored, insured, and transported — and almost nobody in capital markets actually wants that physical good. They want the *price exposure*.

| Term | Meaning |
|---|---|
| Spot Price | Today's price for near-immediate physical delivery — same concept as FX Spot ([Module 6](./module-06-fx.md) §1), applied to a barrel of oil or an ounce of gold |
| Futures Curve | The set of futures prices across different delivery months for the same commodity — plotted, it shows the market's term structure |
| Contango | Futures curve slopes *upward* — later delivery months cost more than spot, typically because storage + financing costs exceed any convenience yield |
| Backwardation | Futures curve slopes *downward* — later delivery months cost less than spot, typically when the commodity is scarce right now and holders pay a premium for immediate supply |
| Convenience Yield | The non-monetary benefit of physically holding a commodity now (e.g. never running out of oil mid-production) — offsets storage cost in the contango/backwardation math |
| Roll | Closing a futures position nearing expiry and opening the same position in a later-dated contract, to avoid ever taking physical delivery |

```text
CONTANGO vs BACKWARDATION — same mental model as FX Forward Points (Module 6 §1)
========================================================================

  CONTANGO (upward-sloping curve):
    Spot: USD 75.00/bbl   1M Future: USD 75.60   3M Future: USD 76.90
    -> storage + financing cost dominates; later delivery costs more

  BACKWARDATION (downward-sloping curve):
    Spot: USD 75.00/bbl   1M Future: USD 74.30   3M Future: USD 72.80
    -> near-term scarcity dominates; later delivery costs less

  -> Exactly the FX forward-points mechanic from Module 6 §1, just
     driven by storage/convenience yield instead of an interest
     rate differential between two currencies.
```

**Takeaway:** the single hardest habit to unlearn coming from Equity or Fixed Income is treating a commodity price like a stock price. A stock's forward price is almost entirely driven by the risk-free rate ([Module 10](./module-10-valuation.md): Valuation); a commodity's forward price is driven by storage cost, financing cost, and convenience yield — three factors a share or a bond simply doesn't have.

---

## 2. What Are The Products? — Physical, Futures, Options &amp; Swaps

```text
COMMODITIES
│
├── Physical Commodities   (the actual barrel, bar, or bushel — rare in a bank's own book)
├── Commodity Futures      (exchange-traded, standardized, obligation to buy/sell at a future date)
├── Commodity Options      (the RIGHT, not obligation, on a commodity future or physical price)
└── Commodity Swaps        (periodic net cash flows referencing a commodity price index — Module 2 §7)
```

| Cash / Derivative | Product | What it is |
|---|---|---|
| CASH | **Physical Commodity** | The actual tangible good — a 100oz Gold Bar, a barrel of crude, a tonne of wheat — bought and sold with physical delivery, storage, and title transfer |
| DERIVATIVE | **Commodity Future** | A standardized, exchange-traded contract to buy/sell a set quantity of a commodity at a fixed price on a fixed future date — almost always closed out or rolled before delivery (Section 1) |
| DERIVATIVE | **Commodity Option** | A contract giving the buyer the right, not the obligation, to buy (call) or sell (put) a commodity future at a fixed strike, for a premium — same asymmetric payoff as [Module 4](./module-04-equity.md) §4d and [Module 6](./module-06-fx.md) §2 |
| DERIVATIVE | **Commodity Swap** | Two counterparties exchange cash flows over a period — one side usually pays a **fixed** price, the other pays a **floating** price referencing a published index (e.g. ICE Brent) — reusing the exact structure from [Module 2](./module-02-hierarchy.md) §7's 1Y Brent Crude Oil Swap |

> **A fifth product, briefly:** commodities also include the **Commodity Warrant** — an exchange-listed, long-dated call option issued *by a bank* (not written bilaterally the way Section 4d's option is) — economically closest to Section 2's Option, but issued and distributed more like a security. It's a niche product next to the other four and isn't covered with its own worked example here.

**Takeaway:** notice the pattern already established across three asset classes now ([Module 4](./module-04-equity.md) §2 Equity, [Module 6](./module-06-fx.md) §2 FX): one "real thing" (here, Physical) and a family of contracts referencing its price (Future, Option, Swap). What's new for Commodities is that the "real thing" itself is genuinely inconvenient to hold — which is exactly why Section 3 spends more time than any prior module explaining why almost everyone trades the contract, never the barrel.

---

## 3. What Exactly Is Being Traded? — Physical Good vs Price-Referencing Contract

**Definition:** unlike Equity ([Module 4](./module-04-equity.md): security vs. contract) or FX ([Module 6](./module-06-fx.md): contracts, no security at all), Commodities sit in a **third** pattern: the underlying (Physical) is real and tangible, like a security — but it's a *good*, not a financial claim, and almost never actually changes hands in a bank's trading book. The contracts (Future, Option, Swap) all reference the *price* of that good without requiring anyone to touch a barrel.

```text
PHYSICAL COMMODITY (TANGIBLE GOOD)  vs  DERIVATIVE (PRICE-REFERENCING CONTRACT)
========================================================================

  Physical Gold (TANGIBLE GOOD):
    A 100oz Gold Bar, LBMA "Good Delivery" standard, sitting in a
    vault. It exists independently of any trade, like Coca-Cola
    stock (Module 4 §3) -- but unlike a share, it has WEIGHT,
    PURITY, a physical LOCATION, and costs money just to store
    and insure.

  Commodity Future / Swap (CONTRACT):
    Bank Of Toronto enters a 1Y Brent Crude Oil Swap, notional
    100,000 barrels (the exact trade from Module 2 §7) -- no oil
    is bought, stored, or shipped. Only the DIFFERENCE between a
    fixed price and the floating ICE Brent index settles in cash,
    once a month, for the life of the swap.

  -&gt; Section 1's takeaway already flagged this: almost nobody in
     capital markets wants the physical commodity. A bank's own
     book is overwhelmingly Futures, Swaps, and Options; physical
     trading is a specialist activity (a metals desk, an energy
     trading arm) sitting apart from the rest of the derivatives book.
```

**Takeaway:** this is why [Module 2](./module-02-hierarchy.md) §7's ISDA classification table flagged Commodity as the asset class where "physical settlement of the underlying is the rare exception, not the norm" — a stronger statement than [Module 6](./module-06-fx.md) made about FX (where Spot *does* routinely settle physically) or [Module 5](./module-05-fixed-income.md) made about bonds (which always settle physically, just via book-entry). Commodities is the asset class where physical settlement is actively designed *around*.

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

> **Complete worked examples available.** [Physical Commodity Trade — Complete Example](../../examples/Commodity_Physical_Complete_Example.md) ([HTML](../../examples/Commodity_Physical_Complete_Example.html)) and [Commodity Swap — Complete Example](../../examples/Commodity_Swap_Complete_Example.md) ([HTML](../../examples/Commodity_Swap_Complete_Example.html)) walk the cash and derivative sides of this asset class through the full trade lifecycle with real numbers.

### 4a. Physical Commodity

```text
PHYSICAL GOLD  —  100oz Gold Bar, LBMA Good Delivery
========================================================================

  Buyer:  Bank Of Toronto (precious metals desk)
  Seller: A LBMA-accredited refiner

  Trade: 100 oz @ Spot Price USD 2,650.00/oz
  Trade Value = 100 x 2,650.00 = USD 265,000
  Settlement: physical delivery into an LBMA-approved vault +
              cash payment, T+2 (standard for the London gold
              market)

  -&gt; This IS one of the genuinely rare cases (Section 3) where a
     bank actually takes physical delivery -- precious metals
     desks are one of the few areas of a bank that routinely does.
```

### 4b. Commodity Future

```text
COMMODITY FUTURE  —  CME WTI Crude Oil Future (CL), 1,000 bbl/contract
========================================================================

  Buyer:  Bank Of Toronto (proprietary energy desk)
  Seller: Counterparty on the CME order book (anonymous, novated
          to the CCP at execution -- Section 07)

  Trade: BUY 50 contracts @ USD 75.50/bbl, front-month expiry
  Notional = 50 x 1,000 x 75.50 = USD 3,775,000

  ONE MONTH LATER, before expiry -- Bank Of Toronto ROLLS:
    SELLS 50 front-month contracts @ USD 76.10/bbl
      Realized P&amp;L = 50 x 1,000 x (76.10 - 75.50) = +USD 30,000
    BUYS 50 next-month contracts @ USD 76.90/bbl (contango,
      Section 01 -- next month costs more)

  -&gt; Bank Of Toronto never took delivery of a single barrel --
     exactly the "roll" mechanic Section 01 defined, and the
     reason futures desks watch the contango/backwardation shape
     of the curve as closely as an FX desk watches forward points.
```

### 4c. Commodity Swap

Reusing [Module 2](./module-02-hierarchy.md) §7's worked structure directly:

```text
COMMODITY SWAP  —  1Y Brent Crude Oil Swap, notional 100,000 barrels
========================================================================

  Bank Of Toronto:  pays FIXED, receives FLOATING (ICE Brent index)
  Counterparty:      Shell Trading

  Fixed Price agreed today: USD 78.00/bbl
  Monthly notional: 100,000 barrels / 12 = 8,333 barrels per month

  MONTH 1 SETTLEMENT -- ICE Brent monthly average settles at USD 81.20/bbl
    Floating leg owed to Bank Of Toronto = 8,333 x 81.20 = USD 676,640
    Fixed leg owed by Bank Of Toronto    = 8,333 x 78.00 = USD 649,974
    Net cash flow: Bank Of Toronto RECEIVES USD 26,666
    (81.20 - 78.00) x 8,333 = USD 26,666 -- only the NET amount
    moves, exactly the IRS netting mechanic from Module 1 §8

  -&gt; No oil changes hands at any point across the full 12 months --
     this is Module 2 §7's "Commodity / Energy / Oil / Swap" entry
     made concrete with real monthly cash flows.
```

### 4d. Commodity Option

```text
COMMODITY OPTION  —  Call on CME WTI Crude Oil Future
========================================================================

  Buyer:  Bank Of Toronto's client (an airline hedging jet fuel cost,
          using WTI as a proxy hedge)
  Seller (writer): Bank Of Toronto

  Trade: WTI Call, Strike USD 80.00/bbl, Notional 100,000 bbl
         (100 contracts x 1,000 bbl), Expiry 6 months
         Premium: USD 3.20/bbl
  Premium Paid = 100,000 x 3.20 = USD 320,000

  AT EXPIRY -- Scenario A: WTI Future settles at USD 88.00/bbl
    Option is in-the-money: exercising saves
      (88.00 - 80.00) x 100,000 = USD 800,000 vs. buying at market
    Net Benefit = 800,000 - 320,000 (premium) = USD 480,000

  AT EXPIRY -- Scenario B: WTI Future settles at USD 74.00/bbl
    Option expires WORTHLESS -- buying at market (74.00) is
    cheaper than exercising at strike (80.00)
    Maximum Loss = the USD 320,000 premium, exactly as Module 4
    §4d and Module 6 §4d established -- never more than premium paid.
```

**Takeaway:** four trades, four very different settlement realities — 4a is the rare case that actually moves a physical good; 4b, 4c, and 4d never do. This is the single most important operational fact a reporting system needs to encode correctly for Commodities: the product type alone determines whether "settlement" means a vault delivery or a cash netting, and getting that field wrong has real operational consequences (a vault booking that was never needed, or a physical delivery obligation nobody prepared for).

---

## 5. Trade Lifecycle — Commodity-Specific Walkthrough

Applying **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** to the Commodity Future trade from Section 4b.

```text
CME WTI CRUDE FUTURE  —  FULL LIFECYCLE
========================================================================

  09:31:00  ORDER — Bank Of Toronto's energy desk instructed to buy
            50 front-month WTI contracts

  09:31:04  EXECUTION @ USD 75.50/bbl on CME's order book

  09:31:04  TRADE CAPTURE — Trade ID BOT-CMD-2026-027715

  09:31:05  CLEARING — trade NOVATED to CME Clearing (the CCP)
            immediately at execution -- unlike Module 6's bilateral
            FX or Module 5's bilateral bonds, an exchange-traded
            future is centrally cleared from the moment it trades
            (Section 07)

  DAILY     VARIATION MARGIN — CME Clearing marks the position to
            the daily settlement price and moves cash EVERY DAY,
            not just at expiry:
              Day 1 settle USD 76.00: Bank Of Toronto receives
                50 x 1,000 x (76.00 - 75.50) = USD 25,000
              Day 2 settle USD 75.20: Bank Of Toronto pays
                50 x 1,000 x (76.00 - 75.20) = USD 40,000
            -&gt; this daily cash movement (Module 11: Risk &amp; Exposure)
               is a structural feature of exchange-traded futures
               that OTC swaps (Section 4c) don't have unless a CSA
               requires daily variation margin too (Module 12)

  POSITION — Bank Of Toronto's Commodity book shows 50 WTI
            contracts, front-month, until rolled or closed

  VALUATION / P&amp;L — mark-to-market daily against the CME
            settlement price; because variation margin already
            moved the cash, P&amp;L and cash flow are the SAME number
            every day for an exchange-traded future -- a genuine
            simplification versus Fixed Income's split between
            price P&amp;L and accrual (Module 5 §5)

  REPORTING — exchange-traded futures are reported to the CFTC
            (Section 08) via the exchange/clearing house itself,
            not via a bilateral UTI/UPI the way an OTC Commodity
            Swap (Section 4c) would be
```

**Takeaway:** the biggest lifecycle difference from every prior module is *when* clearing happens. Equity ([Module 4](./module-04-equity.md)) and Fixed Income ([Module 5](./module-05-fixed-income.md)) clear via a CCP but usually a step after execution; FX ([Module 6](./module-06-fx.md)) mostly never clears via a CCP at all. An exchange-traded Commodity Future is novated to the CCP essentially instantaneously, and that CCP relationship is what drives daily variation margin — a cash-flow pattern this module is the first to show in full.

---

## 6. Identifiers — Commodity Codes, Exchange Contracts, UPI &amp; UTI

Commodities split cleanly along the Section 3 physical/contract line: physical goods get commodity codes, not securities identifiers; exchange-traded futures get exchange contract codes; OTC swaps and options get UPI/UTI exactly like [Module 6](./module-06-fx.md)'s FX derivatives.

| Identifier | Applies to Commodities? | Example |
|---|---|---|
| ISIN / CUSIP / SEDOL / Ticker / FIGI | **No** for Physical or OTC Swaps — these identify securities, and neither is one (Section 3) | — |
| Commodity Code | Yes, for physical trades — an internal/industry classification, not a security identifier | `XAU` (gold, ISO 4217-style commodity code) |
| Exchange Contract Code | Yes, for exchange-traded Futures/Options — identifies the specific contract and delivery month | `CLZ26` (CME WTI Crude, December 2026 expiry) |
| **UPI** | Yes, for OTC Commodity Swaps and Options reported as OTC derivatives ([Module 2](./module-02-hierarchy.md) §12) | One UPI for "Brent Crude, Swap, Cash-Settled" — reused across many swap trades, same logic as [Module 6](./module-06-fx.md) §6's FX Forward UPI |
| **UTI** | Yes — every individual OTC Commodity Swap/Option trade gets its own | A fresh UTI per trade, same as [Module 6](./module-06-fx.md) §6 |
| **LEI** | Yes — identifies Bank Of Toronto, Shell Trading, and any client as counterparties | Entity, not instrument ([Module 4](./module-04-equity.md) §6) |

**Takeaway:** a Commodity trade record with an exchange contract code (like `CLZ26`) but no UPI/UTI is a Future — centrally cleared, exchange-identified, no bilateral reporting IDs needed. A record with a UPI/UTI but no exchange contract code is an OTC Swap or Option. Seeing *both* populated on the same trade, or neither populated at all, is the Commodities-specific version of the diagnostic checks [Module 5](./module-05-fixed-income.md) §6 and [Module 6](./module-06-fx.md) §6 already established for bonds and FX.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** Commodities is the first asset class in this roadmap where the exchange-traded, centrally-cleared product (Futures) is genuinely the dominant, most liquid way most participants get commodity exposure — closer to [Module 4](./module-04-equity.md)'s Equity default than to [Module 5](./module-05-fixed-income.md)'s Fixed Income or [Module 6](./module-06-fx.md)'s FX defaults. Physical trades and OTC Swaps/Options remain bilateral.

| Cash / Derivative | Product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|---|
| CASH | Physical Commodity | OTC (dealer-to-dealer, or dealer-to-refiner/producer) | Bilateral (the norm — Section 4a) |
| DERIVATIVE | Commodity Future | **Exchange-traded** (CME, ICE, LME) | Centrally cleared from execution (Section 05) |
| DERIVATIVE | Commodity Option | Mostly exchange-traded (options ON futures); some bespoke OTC | Centrally cleared if exchange-listed; bilateral if OTC (Section 4d's airline hedge is OTC-style, bespoke strike/expiry) |
| DERIVATIVE | Commodity Swap | OTC | Mostly bilateral; a growing share clears via ICE Clear or LCH for standardized benchmarks (e.g. Brent, WTI) |

**Takeaway:** unlike every other module's single dominant default, Commodities genuinely splits down the middle by product — Futures behave like [Module 4](./module-04-equity.md)'s Equity (exchange, centrally cleared, novated instantly), while Physical and most Swaps behave like [Module 5](./module-05-fixed-income.md)'s Fixed Income or [Module 6](./module-06-fx.md)'s FX (OTC, bilateral). Knowing *which* Commodity product a trade record represents is the single fastest way to predict its whole downstream lifecycle.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | How it touches Commodities specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Covers commodity derivatives directly, with **position limits** on certain commodity derivative contracts (capping how large a single participant's position can get) — a control mechanism Equity, Fixed Income, and FX don't have an equivalent of in this roadmap so far |
| **EMIR** | Applies to OTC Commodity Swaps and Options as derivatives ([Module 2](./module-02-hierarchy.md) §07) — but generally **not** to a physical forward used for genuine commercial delivery purposes (an "ancillary activity" carve-out similar in spirit to [Module 6](./module-06-fx.md) §8's FX Spot/Forward boundary, though defined differently) |
| **SFTR** | Not applicable to Commodities in the way it is to repo ([Module 5](./module-05-fixed-income.md) §08) — commodities lending exists but is a narrow, specialist niche, not a Commodities-module-defining product the way repo is for Fixed Income |
| **FCA** (UK) | Conduct regulator for UK commodity derivatives trading and MiFIR-equivalent transaction reporting; London is also home to the **LME (London Metal Exchange)**, making the FCA's oversight of metals trading specifically significant, similar in spirit to [Module 6](./module-06-fx.md) §08's FX-volume point |
| **CIRO** (Canada) | SRO overseeing Canadian commodity derivatives dealer conduct |
| **HKMA** (Hong Kong) | Genuinely more of a footnote here than in FX ([Module 6](./module-06-fx.md) §08) or Fixed Income ([Module 5](./module-05-fixed-income.md) §08) — Hong Kong's commodities market is smaller; the **SFC** and **HKEX** (which absorbed the LME in 2012) are the more relevant Hong Kong-linked bodies for this asset class |
| **CFTC** (US) | The single most consequential regulator for this specific module — the **Commodity Futures Trading Commission** is literally named for this asset class, sets swap data reporting, clearing, and SEF trading rules under Dodd-Frank ([systems-landscape](./systems-landscape.md) §'s CFTC entry), and enforces the position limits MiFID also uses in the EU |
| **DTCC** | Its **ICE Trade Vault** (named directly in [systems-landscape](./systems-landscape.md) §10 as a Swap Data Repository) accepts commodity derivative reports alongside interest rate and credit products, feeding CFTC reporting requirements |
| **DMO** (UK Debt Management Office) | Not Commodities-relevant — issues Gilts ([Module 5](./module-05-fixed-income.md) §08), included here for completeness only |

**Takeaway:** Commodities is the first module where the CFTC, not a European or UK body, is clearly the headline regulator — a direct consequence of Section 7's finding that Futures are the dominant product, and the largest commodity futures exchanges (CME, ICE) sit under CFTC jurisdiction. This mirrors exactly how [Module 6](./module-06-fx.md) §08 flagged the FCA as outsized for FX because of London's FX volume — same pattern, different regulator, different asset class.

---

## 9. Canadian Systems Landscape — Commodities Front, Middle &amp; Back Office to Regulatory Reporting

**Definition:** [systems-landscape](./systems-landscape.md) §2 already laid out the *generic* Canadian Front/Middle/Back Office architecture. This section specializes that architecture to Commodities specifically — and Commodities turns out to be the one asset class in this roadmap where genuinely **Canadian-domiciled** market infrastructure (NGX, CDCC) plays a comparatively narrow role next to the **global benchmark venues** (CME, ICE) Section 4's own worked examples already lean on. Both paths are shown below.

> **Callout — illustrative, not prescriptive.** The specific vendor/platform names below (Murex, Calypso, ICE eConfirm, etc.) are representative of real Canadian market infrastructure and commonly-used vendor categories — not a claim about which exact systems Bank Of Toronto (fictional) runs internally. Treat this section the same way [systems-landscape](./systems-landscape.md) treats its own platform lists: illustrative of the *category* of system, confirmed against a real firm's actual stack only if you're building against it.

### 9a. Two genuinely different paths, depending on the venue

| Layer | Path A — Domestic: NGX (Alberta energy) | Path B — Global benchmark: CME/ICE (Sections 4b–4c) |
|---|---|---|
| **Front Office** | NGX's own electronic trading platform (Calgary) — physical &amp; financial AECO natural gas, power, crude | CME Globex / ICE platform, accessed by Bank Of Toronto via a Futures Commission Merchant (FCM) relationship — not a Canadian venue at all |
| **Middle Office** | Commodity risk/confirmation system (Murex/Calypso-class) booking the trade; ICE eConfirm-style electronic confirmation | Same risk/confirmation system class, booking a US-cleared position instead of a Canadian one |
| **Back Office / Clearing** | **NGX Clearing** (part of ICE) clears the trade directly | **CME Clearing** (Section 5's worked example) or ICE Clear — neither is CDS nor CDCC |
| **Regulator** | **Alberta Securities Commission (ASC)** — NGX's primary securities regulator, since NGX is Calgary-based (a genuine exception to the Ontario/CSA-centric pattern other modules lean on) | **CFTC** (Section 8) for the venue itself; **CIRO** for Bank Of Toronto's own dealer conduct as a Canadian participant |
| **OTC reporting?** | Generally **no** — exchange-traded/cleared, same Future-vs-OTC boundary Section 6's takeaway already draws | **No**, for the same reason — Section 4b's WTI Future is exchange-cleared, not OTC |

**Takeaway:** notice what does and doesn't touch Canadian infrastructure at all. CME/ICE-listed Futures (Section 4b) clear entirely within the US ecosystem regardless of which country the trading bank is headquartered in — Canadian involvement there is limited to CIRO's conduct oversight of Bank Of Toronto as a dealer, not to any Canadian settlement system. Only trades actually executed on a Canadian venue (NGX) touch genuinely Canadian back-office infrastructure (NGX Clearing) and a Canadian regulator (ASC) directly.

### 9b. Worked example — NGX AECO natural gas swap, traced end to end

```text
NGX FINANCIAL SWAP — AECO 5A NATURAL GAS, DECEMBER 2026
========================================================================

  Bank Of Toronto's Calgary energy desk: pays FIXED, receives
  FLOATING (NGX's AECO Daily Index) -- same fixed/floating
  structure as Section 4c's Brent swap, just Canadian-domiciled

  Fixed price: CAD 3.10/GJ    Volume: 10,000 GJ/day x 31 days
  = 310,000 GJ notional for December

  MONTH-END SETTLEMENT -- AECO index averages CAD 3.45/GJ
    Floating leg owed to Bank Of Toronto = 310,000 x 3.45
      = CAD 1,069,500
    Fixed leg owed by Bank Of Toronto    = 310,000 x 3.10
      = CAD 961,000
    Net cash flow: Bank Of Toronto RECEIVES CAD 108,500

  FRONT OFFICE   -> executed on NGX's own electronic platform
  MIDDLE OFFICE  -> booked and risk-managed in Bank Of Toronto's
                    commodity risk system; confirmed electronically
  BACK OFFICE    -> cleared by NGX CLEARING (part of ICE) --
                    financial leg only; no physical gas moves
                    (Section 3's physical-vs-contract distinction,
                    restated for a genuinely Canadian venue)
  CASH SETTLEMENT-> CAD 108,500 moves via Lynx (Canada's large-value
                    payment system, systems-landscape §2)
  REGULATOR      -> Alberta Securities Commission (ASC), NOT the
                    Ontario-centric CSA path other Canadian examples
                    in this roadmap default to
  OTC REPORTING  -> NOT required under CSA Multilateral Instrument
                    96-101 -- this is exchange-cleared (NGX Clearing),
                    the same Future/OTC boundary Section 6 already
                    established, just for a Canadian exchange instead
                    of CME/OCC
```

### 9c. Where MI 96-101 reporting DOES apply — the Brent swap, again

Section 4c's Brent Crude Oil Swap with Shell Trading is bilateral OTC — the opposite case from 9b. If either counterparty has the requisite Canadian nexus, that swap (unlike the NGX example) **is** in scope for **CSA Multilateral Instrument 96-101** (Trade Repositories and Derivatives Data Reporting), Canada's OTC derivatives trade-reporting rule, and would be reported to a CSA-designated Trade Repository — commonly **DTCC Data Repository (Canada) Ltd** or **ICE Trade Vault Canada** (both illustrative of the category; confirm the live designated-TR list against CSA's own published rules before building against it).

```text
FRONT OFFICE   -> bilateral voice/electronic negotiation with
                  Shell Trading, no exchange involved
MIDDLE OFFICE  -> risk system booking; confirmation via an
                  OTC-confirmation platform (ICE eConfirm-class)
BACK OFFICE    -> NO CCP at all -- Section 7 already flagged most
                  Commodity Swaps as bilateral, not centrally cleared
REGULATOR/TR   -> CSA Multilateral Instrument 96-101 reporting to a
                  CSA-designated Trade Repository (DTCC Data
                  Repository (Canada) Ltd / ICE Trade Vault Canada,
                  illustrative) -- squarely the OTC-reporting path
                  Section 9a's NGX example explicitly does NOT take
```

**Takeaway:** the single fastest way to predict which Canadian systems a Commodity trade actually touches is Section 7's own Exchange-vs-OTC finding, applied one level deeper — an exchange-cleared trade (NGX or CME/ICE) never reaches a Canadian OTC Trade Repository at all, while a bilateral OTC trade (the Brent swap) never touches a clearing house, Canadian or otherwise, but does reach a Trade Repository under MI 96-101. Knowing the product type from Section 2 tells you which entire half of this section's systems chain a given trade will run through.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Bank Of Toronto | Buyer of physical gold; buyer/roller of WTI futures; fixed-rate payer on the Brent swap; option writer for its client |
| An LBMA-accredited refiner | Seller of the physical gold bar |
| CME (Chicago Mercantile Exchange) | Exchange and CCP for the WTI Crude Oil future |
| Shell Trading | Floating-rate payer counterparty on the Brent Crude Oil Swap |
| Bank Of Toronto's client (airline) | Buyer of the WTI Call option, hedging jet fuel cost exposure via a crude oil proxy |
| CFTC | Primary US regulator for commodity futures and swaps |
| LME / ICE | Key commodity exchanges (metals and energy respectively) referenced throughout |
| NGX (Natural Gas Exchange) | Calgary-based Canadian exchange for physical/financial natural gas and power; venue for Section 9b's AECO swap |
| Alberta Securities Commission (ASC) | Primary securities regulator overseeing NGX (Section 9) |
| CSA (Canadian Securities Administrators) | Sets Multilateral Instrument 96-101, the OTC derivatives trade-reporting rule applying to Section 9c's Brent swap |

---

## Recap Quiz — Numbers Edition

1. On the WTI Future roll in Section 4b, if Bank Of Toronto had instead rolled when the curve was in *backwardation* (next-month cheaper than front-month, say USD 74.80 instead of USD 76.90), would the roll itself cost Bank Of Toronto more or less than in the contango example, all else equal?
2. Using Section 5's variation margin walkthrough, if Day 3's settlement price came in at USD 75.20 again (unchanged from Day 2), what cash flow would move, and why?
3. Why does Section 3 describe physical settlement in Commodities as "actively designed around," and how does that compare to what [Module 5](./module-05-fixed-income.md) §3 said about Fixed Income settlement?
4. A trade record shows an exchange contract code (`CLZ26`-style) AND a UTI populated on the same trade. Based on Section 6, what does that combination most likely indicate?
5. On the Brent Crude Oil Swap in Section 4c, if the ICE Brent monthly average had instead settled at USD 76.50 (below the USD 78.00 fixed price), what would the Month 1 net cash flow be, and which party pays which?
6. Why is the CFTC described in Section 08 as "the single most consequential regulator for this specific module" — what's the causal link back to Section 07's findings?
7. Contrast why Commodity Futures clear "essentially instantaneously" (Section 05) with why FX trades ([Module 6](./module-06-fx.md) §05) mostly never clear via a CCP at all.
8. Using Section 9, why does Bank Of Toronto's CME WTI Crude Oil Future (Section 4b) never touch CDS (CDSX) or CDCC at all, while its NGX AECO natural gas swap (Section 9b) does touch NGX Clearing — what's the general rule this illustrates about when a Canadian bank's commodity trade actually uses Canadian market infrastructure?

Answer whenever ready — then move to **[Module 8](./module-08-rates.md): Rates**, the next stop on the Asset Class Roadmap.

---

## Answers — Recap Quiz, with Explanations

**1. The roll would cost Bank Of Toronto LESS (or even generate a credit) in backwardation, versus a real cost in contango.**

```text
Contango roll (worked example): sell front-month at 76.10, buy
next-month at 76.90 -> pay an extra 0.80/bbl to roll forward

Backwardation roll (this question): sell front-month at 76.10,
buy next-month at 74.80 -> RECEIVE an extra 1.30/bbl to roll forward
```

In contango, rolling forward always means buying the more expensive later-dated contract — a recurring cost to staying long via futures. In backwardation, rolling forward means buying a *cheaper* later-dated contract — a recurring benefit. This is exactly why "roll yield" is a real, closely watched P&amp;L driver for anyone holding futures long-term rather than taking delivery, tying directly back to Section 1's contango/backwardation definitions.

**2. No cash flow would move at all — variation margin only moves when the settlement price CHANGES from the prior day's settlement price.**

Section 5's mechanic is a day-over-day comparison: Day 2 already marked the position to USD 75.20 and settled that difference in cash. If Day 3 settles at the same USD 75.20, there is zero incremental mark-to-market movement, so zero variation margin changes hands that day — the position's cumulative value hasn't moved, even though a full trading day has passed.

**3. Because almost every Commodity product (Future, Swap, Option) is structurally built so physical delivery never has to happen — rolling futures, cash-settling swaps, and options on futures all exist specifically to give price exposure without the storage/insurance/transport burden Section 1 described; [Module 5](./module-05-fixed-income.md) §3, by contrast, described Fixed Income settlement as ALWAYS physical (just dematerialized into book-entry form at a CSD), never designed around.**

Section 3 states this directly: physical settlement is "the rare exception, not the norm" for Commodities, while [Module 5](./module-05-fixed-income.md) §3 never suggested a bond trade could avoid moving the actual (book-entry) security — a bond trade's DvP settlement ([Module 1](./module-01-fundamentals.md) §7) always transfers the real security, it's just no longer a paper certificate.

**4. That combination is a red flag, not a healthy pattern — it most likely indicates a data-quality error, since Section 6 establishes that exchange contract codes identify centrally-cleared Futures (which don't need bilateral UTI/UPI reporting) while UPI/UTI identify OTC derivatives reported bilaterally.**

Section 6's takeaway states this explicitly: a record should have *either* an exchange contract code (Future) *or* a UPI/UTI (OTC Swap/Option), not both — seeing both populated is the Commodities-specific version of the same diagnostic pattern [Module 5](./module-05-fixed-income.md) §6 flagged for bonds carrying a UPI, and [Module 6](./module-06-fx.md) §6 flagged for the reverse (UPI/UTI without ISIN being healthy, not broken).

**5. Bank Of Toronto would PAY the net amount, not receive it — a reversal from the worked example.**

```text
Floating leg owed to Bank Of Toronto = 8,333 x 76.50 = USD 637,575
Fixed leg owed by Bank Of Toronto    = 8,333 x 78.00 = USD 649,974
Net cash flow: Bank Of Toronto PAYS USD 12,399
(78.00 - 76.50) x 8,333 = USD 12,399
```

Because Bank Of Toronto pays fixed and receives floating (Section 4c), whenever the floating index settles *below* the fixed price, Bank Of Toronto is the net payer — the mirror image of the worked example, where a higher floating settlement made Bank Of Toronto the net receiver. This is the same fixed-vs-floating netting logic [Module 1](./module-01-fundamentals.md) §8 established for interest rate swaps.

**6. Because Section 07 found that exchange-traded Futures — which sit under CFTC jurisdiction — are the dominant, most liquid Commodity product, unlike every other module in this roadmap where the dominant product was OTC and bilateral (Fixed Income, FX) or where CCP oversight was more evenly split across jurisdictions (Equity).**

Section 08's takeaway draws this connection explicitly: the CFTC's outsized relevance is a direct consequence of Commodities being the first asset class where the centrally-cleared, exchange-traded product is the default rather than the exception — and the largest commodity futures exchanges (CME, ICE) are US-regulated.

**7. A Commodity Future clears instantaneously because it's exchange-traded from the moment of execution, with the exchange's own CCP standing behind every trade by design (Section 05) — an FX trade mostly never clears via a CCP because FX is overwhelmingly OTC and bilateral ([Module 6](./module-06-fx.md) §05, §07), settled instead via CLS's PvP mechanism, which mitigates settlement risk without ever novating the trade to a central counterparty.**

This is the same structural split Section 07 draws out for Commodities itself (Futures vs. Physical/Swaps) applied across asset classes: whether a trade clears centrally and how fast depends entirely on whether the *product* is exchange-traded by design, not on some asset-class-wide rule — Commodities happens to have both patterns coexisting within a single asset class, where FX ([Module 6](./module-06-fx.md)) and Fixed Income ([Module 5](./module-05-fixed-income.md)) are almost entirely one pattern each.

**8. CDS/CDCC are genuinely Canadian-domiciled infrastructure that only clear/settle products actually traded on Canadian venues — a CME-listed WTI future executes and clears entirely within the US CME/CME Clearing ecosystem regardless of which country the trading bank is headquartered in, so Bank Of Toronto's Canadian entity is just a remote participant via an FCM relationship, with Canadian involvement limited to CIRO's conduct oversight, not settlement infrastructure. The NGX swap, by contrast, trades AND clears on Canadian-domiciled infrastructure (NGX / NGX Clearing), so it's the genuinely "Canadian-system" path.**

Section 9a's table states the general rule directly: Canadian market infrastructure only activates when the **venue itself** is Canadian, not merely because the trading bank happens to be Canadian — the nationality of the counterparty and the nationality of the clearing/settlement chain are two entirely independent facts about a trade.

---

[← Back to Index](../../index.md)
