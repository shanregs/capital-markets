# Module 7 — Commodities (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Asset Class Roadmap 4 of 6** — Equity → Fixed Income → FX → **Commodities** → Rates → Credit.
> **மூலம்:** English Module 7-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Commodities-ன்னா என்ன? — Theory & Terminology

**Definition:** Commodity = ஒரு physical, fungible பொருள் — ஒரு barrel WTI crude, அதே grade-ஓட வேற எந்த barrel-உடனும் interchangeable; ஒரு CAD, வேற எந்த CAD-உடனும் interchangeable ([Module 6](../docs/1-capital-markets/module-06-fx.html) §1) இருக்கற மாதிரி. ஆனா share-ஓட ([Module 4](../docs/1-capital-markets/module-04-equity.html) §1) மாதிரி இல்ல; அங்க ஒவ்வொரு certificate-உம் ஒரு குறிப்பிட்ட company மேல ஒரு குறிப்பிட்ட உரிமையை குறிக்கும். Commodities மூணு வகை: **Energy** (oil, gas, power), **Metals** (gold, silver, copper — *Precious*, *Base*ன்னு பிரியும்), **Agriculture** (wheat, corn, coffee). இதுவரை படிச்ச எல்லா asset classes-ல இருந்தும் Commodities-ஐ உண்மையிலயே வேறுபடுத்துறது: அடிப்படைப் பொருள் **physical** — கெடலாம், சேமிக்கணும், insure பண்ணணும், கொண்டு போகணும் — ஆனா capital markets-ல கிட்டத்தட்ட யாருக்கும் அந்த physical பொருள் வேண்டாம். அவங்களுக்கு *விலை exposure* மட்டும் வேணும்.

| Term | அர்த்தம் |
|---|---|
| Spot Price | கிட்டத்தட்ட உடனடி physical delivery-க்கான இன்னிக்கு விலை — FX Spot ([Module 6](../docs/1-capital-markets/module-06-fx.html) §1) அதே கருத்து, ஒரு barrel oil அல்லது ஒரு ounce gold-க்கு |
| Futures Curve | ஒரே commodity-க்கு வெவ்வேறு delivery மாதங்களுக்கான futures விலைகளோட தொகுப்பு — வரைஞ்சா சந்தையோட term structure தெரியும் |
| Contango | Futures curve *மேல்நோக்கி* சரியுது — தாமதமான delivery மாதங்கள் spot-ஐ விட அதிகம், பொதுவா storage + financing செலவு convenience yield-ஐ விட அதிகம்ங்கறதால |
| Backwardation | Futures curve *கீழ்நோக்கி* சரியுது — தாமதமான delivery மாதங்கள் spot-ஐ விட குறைவு, பொதுவா commodity இப்போ பற்றாக்குறையா இருந்து, உடனடி supply-க்கு வெச்சிருக்கறவங்க premium கொடுக்கும்போது |
| Convenience Yield | இப்போ commodity-ஐ physically வெச்சிருக்கறதால கிடைக்கற பணமில்லா நன்மை (உதா., production நடுவுல oil தீர்ந்துபோகாம இருப்பது) — contango/backwardation கணக்குல storage செலவை ஈடுகட்டுது |
| Roll | Expiry நெருங்கற futures position-ஐ மூடி, பிற்கால contract-ல அதே position-ஐ திறக்கறது — physical delivery எடுக்காம இருக்க |

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

**Takeaway:** Equity அல்லது Fixed Income-ல இருந்து வரும்போது மறக்க வேண்டிய மிகக் கடினமான பழக்கம் — commodity விலையை stock விலை போல நடத்துவது. Stock-ஓட forward விலை கிட்டத்தட்ட முழுக்க risk-free rate-ஆல் ([Module 10](../docs/1-capital-markets/module-10-valuation.html): Valuation) இயக்கப்படுது; commodity-ஓட forward விலை storage செலவு, financing செலவு, convenience yield — share-க்கும் bond-க்கும் இல்லாத மூணு காரணிகளால் இயக்கப்படுது.

### கூடுதல் உதாரணம் — Contango-ல ஏன் roll செலவாகுது? (எளிய கணக்கு)

Spot USD 75.00/bbl. ஒரு வருஷம் கழிச்சு delivery-க்கு வாங்கணும்னு வெச்சுக்கோங்க. Spot-ல வாங்கி ஒரு வருஷம் சேமிச்சா:

| செலவு | USD/bbl |
|---|---|
| Spot விலை | 75.00 |
| Storage + insurance (வருடம்) | 2.50 |
| Financing (75 × 4% வட்டி) | 3.00 |
| Convenience yield (நன்மை) | -1.00 |
| **நியாயமான 1Y forward** | **79.50** |

இதுதான் contango: forward (79.50) > spot (75.00). நீங்க முன்கூட்டியே வாங்கற பதிலா futures-ல இருந்து roll பண்ணிட்டே இருந்தா, ஒவ்வொரு roll-லயும் இந்த வித்தியாசத்தை "செலவா" கொடுக்கறீங்க.

---

## 2. Products என்னென்ன? — Physical, Futures, Options & Swaps

```text
COMMODITIES
│
├── Physical Commodities   (the actual barrel, bar, or bushel — rare in a bank's own book)
├── Commodity Futures      (exchange-traded, standardized, obligation to buy/sell at a future date)
├── Commodity Options      (the RIGHT, not obligation, on a commodity future or physical price)
└── Commodity Swaps        (periodic net cash flows referencing a commodity price index — Module 2 §7)
```

| Cash / Derivative | Product | அது என்ன |
|---|---|---|
| CASH | **Physical Commodity** | உண்மையான உடல் பொருள் — 100oz Gold Bar, ஒரு barrel crude, ஒரு டன் wheat — physical delivery, storage, title transfer-ஓட வாங்கி விக்கப்படுது |
| DERIVATIVE | **Commodity Future** | ஒரு commodity-ஓட நிர்ணயிக்கப்பட்ட அளவை, நிர்ணயிக்கப்பட்ட எதிர்கால தேதில, நிலையான விலைல வாங்க/விற்க standardized, exchange-traded contract — கிட்டத்தட்ட எப்பவும் delivery-க்கு முன்னாடி மூடப்படும் அல்லது roll ஆகும் (Section 1) |
| DERIVATIVE | **Commodity Option** | ஒரு commodity future-ஐ fixed strike-ல வாங்க (call) அல்லது விற்க (put) buyer-க்கு உரிமை (கடமை இல்ல) தரும் contract, premium-க்கு — [Module 4](../docs/1-capital-markets/module-04-equity.html) §4d, [Module 6](../docs/1-capital-markets/module-06-fx.html) §2-ஓட அதே சமச்சீரற்ற payoff |
| DERIVATIVE | **Commodity Swap** | ரெண்டு counterparties ஒரு காலத்துல cash flows மாத்திக்கறாங்க — ஒரு பக்கம் பொதுவா **fixed** விலை, மறுபக்கம் வெளியிடப்பட்ட index-ஐ (உதா., ICE Brent) reference பண்ற **floating** விலை — [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7-ஓட 1Y Brent Crude Oil Swap-ஓட அதே structure |

> **ஐந்தாவது product, சுருக்கமா:** commodities-ல **Commodity Warrant**-உம் உண்டு — *ஒரு வங்கி* வெளியிடற (Section 4d-ல இருக்கற மாதிரி bilateral-ஆ எழுதப்படாத) exchange-listed, நீண்ட கால call option; பொருளாதார ரீதியா Section 2-ஓட Option-க்கு நெருக்கம், ஆனா security மாதிரி வெளியிடப்பட்டு விநியோகிக்கப்படுது. மற்ற நாலோட ஒப்பிட்டா இது ஒரு niche product, இங்க தனி worked example இல்ல.

**Takeaway:** இப்போ மூணு asset classes-ல ஏற்கனவே நிறுவப்பட்ட pattern-ஐ கவனிங்க ([Module 4](../docs/1-capital-markets/module-04-equity.html) §2 Equity, [Module 6](../docs/1-capital-markets/module-06-fx.html) §2 FX): ஒரு "உண்மையான பொருள்" (இங்க Physical), அதன் விலையை reference பண்ற contracts குடும்பம் (Future, Option, Swap). Commodities-ல புதுசு, "உண்மையான பொருளே" வெச்சிருக்க நிஜமாவே சிரமமானது — அதனாலதான் Section 3 எந்த முந்தைய module-ஐ விடவும், ஏன் கிட்டத்தட்ட எல்லாரும் contract-ஐ trade பண்றாங்க, barrel-ஐ ஒருபோதும் இல்லைன்னு விளக்க அதிக நேரம் எடுக்குது.

---

## 3. உண்மையில எது Trade ஆகுது? — Physical பொருள் vs விலையை Reference பண்ற Contract

**Definition:** Equity ([Module 4](../docs/1-capital-markets/module-04-equity.html): security vs contract), FX ([Module 6](../docs/1-capital-markets/module-06-fx.html): contracts, security-ஏ இல்ல) போல இல்லாம, Commodities **மூன்றாவது** pattern-ல இருக்கு: underlying (Physical) உண்மையானது, உடல் ரீதியானது, security போல — ஆனா அது ஒரு *பொருள்*, நிதி உரிமை இல்ல, ஒரு வங்கியோட trading book-ல கிட்டத்தட்ட ஒருபோதும் உண்மையில கை மாறாது. Contracts (Future, Option, Swap) எல்லாமே யாரும் ஒரு barrel-ஐத் தொடாமலே அந்தப் பொருளோட *விலையை* reference பண்ணுது.

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

  -> Section 1's takeaway already flagged this: almost nobody in
     capital markets wants the physical commodity. A bank's own
     book is overwhelmingly Futures, Swaps, and Options; physical
     trading is a specialist activity (a metals desk, an energy
     trading arm) sitting apart from the rest of the derivatives book.
```

**Takeaway:** அதனாலதான் [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7-ஓட ISDA classification table, Commodity-ஐ "underlying-ஓட physical settlement விதிவிலக்கு, வழக்கம் இல்ல" என்ற asset class-ஆ குறிச்சது — [Module 6](../docs/1-capital-markets/module-06-fx.html) FX பத்தி சொன்னதை விட (அங்க Spot வழக்கமா physically settle ஆகும்) அல்லது [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) bonds பத்தி சொன்னதை விட (அவை எப்பவும் physically settle ஆகும், book-entry வழியா மட்டும்) வலுவான கூற்று. Commodities தான் physical settlement-ஐ சுற்றியே வடிவமைக்கப்படற asset class.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked examples இருக்கு.** [Physical Commodity Trade — Complete Example](../examples/Commodity_Physical_Complete_Example.html), [Commodity Swap — Complete Example](../examples/Commodity_Swap_Complete_Example.html) — இந்த asset class-ஓட cash பக்கத்தையும் derivative பக்கத்தையும் உண்மையான எண்களோட முழு trade lifecycle வழியா நடத்திக் காட்டுது.

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

  -> This IS one of the genuinely rare cases (Section 3) where a
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
      Realized P&L = 50 x 1,000 x (76.10 - 75.50) = +USD 30,000
    BUYS 50 next-month contracts @ USD 76.90/bbl (contango,
      Section 01 -- next month costs more)

  -> Bank Of Toronto never took delivery of a single barrel --
     exactly the "roll" mechanic Section 01 defined, and the
     reason futures desks watch the contango/backwardation shape
     of the curve as closely as an FX desk watches forward points.
```

### 4c. Commodity Swap

[Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7-ஓட worked structure-ஐ அப்படியே மறுபயன்படுத்தி:

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

  -> No oil changes hands at any point across the full 12 months --
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

**Takeaway:** நாலு trades, நாலு மிக வேறுபட்ட settlement நிஜங்கள் — 4a ஒரு physical பொருளை உண்மையில நகர்த்தும் அரிதான சந்தர்ப்பம்; 4b, 4c, 4d ஒருபோதும் நகர்த்தாது. Commodities-க்கு reporting system சரியா encode பண்ண வேண்டிய மிக முக்கியமான operational உண்மை இது: product வகை மட்டுமே "settlement"-னா vault delivery-யா, cash netting-ஆன்னு தீர்மானிக்குது; அந்த field தப்பா இருந்தா உண்மையான operational விளைவுகள் உண்டு (தேவையில்லாத vault booking, அல்லது யாரும் தயாரிக்காத physical delivery கடமை).

---

## 5. Trade Lifecycle — Commodity-க்குரிய Walkthrough

**Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting** pattern-ஐ, Section 4b-ஓட Commodity Future trade மேல பொருத்தறோம்.

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
            -> this daily cash movement (Module 11: Risk & Exposure)
               is a structural feature of exchange-traded futures
               that OTC swaps (Section 4c) don't have unless a CSA
               requires daily variation margin too (Module 12)

  POSITION — Bank Of Toronto's Commodity book shows 50 WTI
            contracts, front-month, until rolled or closed

  VALUATION / P&L — mark-to-market daily against the CME
            settlement price; because variation margin already
            moved the cash, P&L and cash flow are the SAME number
            every day for an exchange-traded future -- a genuine
            simplification versus Fixed Income's split between
            price P&L and accrual (Module 5 §5)

  REPORTING — exchange-traded futures are reported to the CFTC
            (Section 08) via the exchange/clearing house itself,
            not via a bilateral UTI/UPI the way an OTC Commodity
            Swap (Section 4c) would be
```

**Takeaway:** முந்தைய எல்லா modules-ல இருந்தும் lifecycle-ல இருக்கற மிகப்பெரிய வித்தியாசம் clearing *எப்போ* நடக்குதுங்கறது. Equity ([Module 4](../docs/1-capital-markets/module-04-equity.html)), Fixed Income ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html)) CCP வழியா clear ஆகுது, ஆனா பொதுவா execution-க்கு ஒரு படி அப்புறம்; FX ([Module 6](../docs/1-capital-markets/module-06-fx.html)) பெரும்பாலும் CCP வழியா clear ஆகவே ஆகாது. Exchange-traded Commodity Future கிட்டத்தட்ட உடனடியா CCP-க்கு novate ஆகுது; அந்த CCP உறவுதான் தினசரி variation margin-ஐ இயக்குது — இந்த module முழுசா காட்டற முதல் cash-flow pattern.

> **Deep dive இருக்கு.** Futures position தான் இந்த curriculum-ல, valuation மாற்றம் ஒவ்வொரு நாளும் CASH-ஆ realize ஆகுற (காகிதத்துல மட்டும் track ஆகாத) position-க்கு மிகக் கூர்மையான உதாரணம் — [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §7 பாருங்க.

### கூடுதல் உதாரணம் — 5 நாள் Variation Margin கணக்கு (50 contracts)

| நாள் | Settlement விலை (USD/bbl) | முந்தைய நாளை விட மாற்றம் | Variation Margin (USD) = மாற்றம் × 1,000 × 50 | திசை |
|---|---|---|---|---|
| Trade நாள் (entry 75.50) | 76.00 | +0.50 | +25,000 | Bank Of Toronto பெறுது |
| Day 2 | 75.20 | -0.80 | -40,000 | Bank Of Toronto கொடுக்குது |
| Day 3 | 75.20 | 0.00 | 0 | எதுவும் நகராது |
| Day 4 | 75.90 | +0.70 | +35,000 | Bank Of Toronto பெறுது |
| Day 5 | 76.10 | +0.20 | +10,000 | Bank Of Toronto பெறுது |
| **மொத்தம்** | | **+0.60** | **+30,000** | = 50 × 1,000 × (76.10 - 75.50) |

ஐந்து நாள்ல ஐந்து தனி cash movements — ஆனா மொத்தம் entry-ல இருந்து இறுதி விலை வரையிலான லாபத்துக்கு (USD 30,000) சரியா சமம். அதாவது futures-ல "P&L" உம் "cash flow" உம் ஒரே எண்.

---

## 6. Identifiers — Commodity Codes, Exchange Contracts, UPI & UTI

Commodities, Section 3-ல உள்ள physical/contract கோட்டுல தெளிவா பிரியுது: physical பொருட்களுக்கு securities identifiers இல்ல, commodity codes; exchange-traded futures-க்கு exchange contract codes; OTC swaps, options-க்கு [Module 6](../docs/1-capital-markets/module-06-fx.html)-ஓட FX derivatives மாதிரியே UPI/UTI.

| Identifier | Commodities-க்கு பொருந்துமா? | Example |
|---|---|---|
| ISIN / CUSIP / SEDOL / Ticker / FIGI | Physical அல்லது OTC Swaps-க்கு **இல்ல** — இவை securities-ஐ identify பண்ணும், ரெண்டுமே அது இல்ல (Section 3) | — |
| Commodity Code | ஆமா, physical trades-க்கு — internal/தொழில்துறை classification, security identifier இல்ல | `XAU` (gold, ISO 4217-பாணி commodity code) |
| Exchange Contract Code | ஆமா, exchange-traded Futures/Options-க்கு — குறிப்பிட்ட contract-ஐயும் delivery month-ஐயும் identify பண்ணும் | `CLZ26` (CME WTI Crude, December 2026 expiry) |
| **UPI** | ஆமா, OTC derivatives-ஆ report ஆகும் OTC Commodity Swaps, Options-க்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) | "Brent Crude, Swap, Cash-Settled"-க்கு ஒரு UPI — பல swap trades-ல மறுபயன்பாடு, [Module 6](../docs/1-capital-markets/module-06-fx.html) §6-ஓட FX Forward UPI-ஓட அதே logic |
| **UTI** | ஆமா — ஒவ்வொரு தனி OTC Commodity Swap/Option trade-க்கும் தனி UTI | ஒவ்வொரு trade-க்கும் புது UTI, [Module 6](../docs/1-capital-markets/module-06-fx.html) §6 மாதிரி |
| **LEI** | ஆமா — Bank Of Toronto, Shell Trading, எந்த client-ஐயும் counterparties-ஆ identify பண்ணும் | Entity, instrument இல்ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §6) |

**Takeaway:** Exchange contract code (`CLZ26` போல) இருந்து UPI/UTI இல்லாத Commodity trade record ஒரு Future — centrally cleared, exchange-identified, bilateral reporting IDs தேவையில்ல. UPI/UTI இருந்து exchange contract code இல்லாத record OTC Swap அல்லது Option. ஒரே trade-ல *ரெண்டும்* நிரம்பியிருந்தாலோ, ரெண்டுமே இல்லாட்டியோ, அது [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6, [Module 6](../docs/1-capital-markets/module-06-fx.html) §6 bonds, FX-க்கு ஏற்கனவே நிறுவின diagnostic சோதனைகளோட Commodities-குறிப்பான பதிப்பு.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** இந்த roadmap-ல, exchange-traded, centrally-cleared product (Futures) பெரும்பாலான பங்கேற்பாளர்கள் commodity exposure பெறும் உண்மையிலேயே ஆதிக்க, அதிக liquid வழியா இருக்கற முதல் asset class Commodities — [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) Fixed Income, [Module 6](../docs/1-capital-markets/module-06-fx.html) FX defaults-ஐ விட [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity default-க்கு நெருக்கம். Physical trades, OTC Swaps/Options bilateral-ஆவே இருக்கு.

| Cash / Derivative | Product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|---|
| CASH | Physical Commodity | OTC (dealer-to-dealer, அல்லது dealer-to-refiner/producer) | Bilateral (வழக்கம் — Section 4a) |
| DERIVATIVE | Commodity Future | **Exchange-traded** (CME, ICE, LME) | Execution-ல இருந்தே centrally cleared (Section 05) |
| DERIVATIVE | Commodity Option | பெரும்பாலும் exchange-traded (futures மேல options); சில bespoke OTC | Exchange-listed என்றா centrally cleared; OTC என்றா bilateral (Section 4d-ஓட airline hedge OTC-பாணி, bespoke strike/expiry) |
| DERIVATIVE | Commodity Swap | OTC | பெரும்பாலும் bilateral; standardized benchmarks-க்கு (உதா., Brent, WTI) ICE Clear அல்லது LCH வழியா clear ஆகும் பங்கு வளருது |

**Takeaway:** மற்ற ஒவ்வொரு module-ஓட ஒற்றை ஆதிக்க default போல இல்லாம, Commodities product வாரியா நடுவுல உண்மையிலயே பிரியுது — Futures [Module 4](../docs/1-capital-markets/module-04-equity.html) Equity போல நடந்துக்கும் (exchange, centrally cleared, உடனே novate); Physical, பெரும்பாலான Swaps [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) Fixed Income அல்லது [Module 6](../docs/1-capital-markets/module-06-fx.html) FX போல (OTC, bilateral). ஒரு trade record *எந்த* Commodity product-ஐ குறிக்குதுன்னு தெரிஞ்சுக்கறது, அதன் முழு downstream lifecycle-ஐயும் முன்கூட்டியே கணிக்கற மிக வேகமான வழி.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | Commodities-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Commodity derivatives-ஐ நேரடியா உள்ளடக்கும், சில commodity derivative contracts மேல **position limits்-உம்** (ஒரு பங்கேற்பாளரின் position எவ்வளவு பெருசாகலாம்னு வரம்பு) — இந்த roadmap-ல இதுவரை Equity, Fixed Income, FX-க்கு இல்லாத கட்டுப்பாட்டு mechanism |
| **EMIR** | OTC Commodity Swaps, Options-க்கு derivatives-ஆ பொருந்தும் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) — ஆனா உண்மையான commercial delivery நோக்கத்துக்கு பயன்படும் physical forward-க்கு பொதுவா **பொருந்தாது** ("ancillary activity" விலக்கு, [Module 6](../docs/1-capital-markets/module-06-fx.html) §8-ஓட FX Spot/Forward எல்லை போல இருந்தாலும் வேற மாதிரி வரையறுக்கப்பட்டது) |
| **SFTR** | Repo-வுக்கு பொருந்தற மாதிரி ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08) Commodities-க்கு பொருந்தாது — commodities lending இருக்கு, ஆனா Fixed Income-க்கு repo போல Commodities module-ஐ வரையறுக்கும் product இல்ல, குறுகிய, specialist niche |
| **FCA** (UK) | UK commodity derivatives trading-ஓட conduct regulator, MiFIR-சமமான transaction reporting; London **LME (London Metal Exchange)**-ஓட இல்லமும் — metals trading-ஓட FCA மேற்பார்வையை குறிப்பா முக்கியமாக்குது, [Module 6](../docs/1-capital-markets/module-06-fx.html) §08-ஓட FX-volume புள்ளி போல |
| **CIRO** (Canada) | Canadian commodity derivatives dealer conduct-ஐ மேற்பார்வை பண்ற SRO |
| **HKMA** (Hong Kong) | FX ([Module 6](../docs/1-capital-markets/module-06-fx.html) §08), Fixed Income ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08)-ஐ விட இங்க உண்மையிலயே footnote — Hong Kong-ஓட commodities சந்தை சிறியது; இந்த asset class-க்கு **SFC**, **HKEX** (2012-ல LME-ஐ உள்வாங்கியது) தான் Hong Kong-சார்ந்த பொருத்தமான அமைப்புகள் |
| **CFTC** (US) | இந்த module-க்கு மிக விளைவுள்ள ஒற்றை regulator — **Commodity Futures Trading Commission** பெயரே இந்த asset class-ஐ வெச்சுத்தான்; Dodd-Frank-ன் கீழ swap data reporting, clearing, SEF trading விதிகளை வகுக்குது, MiFID-உம் EU-ல பயன்படுத்தற position limits-ஐ அமல்படுத்துது |
| **DTCC** | அதன் **ICE Trade Vault** ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §10-ல Swap Data Repository-ஆ நேரடியா பெயரிடப்பட்டது) interest rate, credit products-உடன் commodity derivative reports-ஐ ஏற்கும், CFTC reporting தேவைகளுக்கு உணவளிக்கும் |
| **DMO** (UK Debt Management Office) | Commodities-க்கு சம்பந்தமில்ல — Gilts வெளியிடுது ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08), முழுமைக்காக மட்டும் இங்க |

**Takeaway:** Commodities, CFTC — ஐரோப்பிய அல்லது UK அமைப்பு இல்ல — தெளிவா headline regulator-ஆ இருக்கற முதல் module; Section 7-ஓட findings-இன் நேரடி விளைவு: Futures தான் ஆதிக்க product, மிகப்பெரிய commodity futures exchanges (CME, ICE) CFTC அதிகார எல்லைக்குள்ள. [Module 6](../docs/1-capital-markets/module-06-fx.html) §08, London-ஓட FX volume-ஆல FCA-வை FX-க்கு outsized-ஆ குறிச்ச அதே pattern — வேற regulator, வேற asset class.

---

## 9. Canadian Systems Landscape — Commodities Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §2 *பொதுவான* Canadian Front/Middle/Back Office architecture-ஐ ஏற்கனவே வகுத்திருக்கு. இந்த section அதை Commodities-க்கு மட்டும் சுருக்குது — இந்த roadmap-ல, Canada-வில் அமைந்த (NGX, CDCC) market infrastructure, Section 4-ஓட worked examples சாயற **உலகளாவிய benchmark venues** (CME, ICE)-க்கு அடுத்து ஒப்பீட்டளவில் குறுகிய பங்கு வகிக்கற ஒரே asset class Commodities. ரெண்டு பாதைகளும் கீழே.

> **Callout — illustrative, பரிந்துரை இல்ல.** கீழ உள்ள vendor/platform பெயர்கள் (Murex, Calypso, ICE eConfirm, etc.) உண்மையான Canadian market infrastructure, பொதுவா பயன்படற vendor categories-ஓட பிரதிநிதிகள் — Bank Of Toronto (கற்பனை) உள்ளுக்குள்ள எந்த systems-ஐ ஓட்டுதுங்கறதைப் பத்தின கூற்று இல்ல. இந்தப் பகுதியை [systems-landscape](../docs/1-capital-markets/systems-landscape.html) தன்னோட platform பட்டியல்களை நடத்தற மாதிரியே நடத்துங்க: system-ஓட *வகை*யின் illustrative; ஒரு உண்மையான firm-ஓட stack-க்கு எதிராக கட்டறீங்கன்னா மட்டும் உறுதிப்படுத்துங்க.

### 9a. Venue-ஐ பொறுத்து உண்மையிலயே வேற ரெண்டு பாதைகள்

| Layer | Path A — Domestic: NGX (Alberta energy) | Path B — Global benchmark: CME/ICE (Sections 4b–4c) |
|---|---|---|
| **Front Office** | NGX's own electronic trading platform (Calgary) — physical & financial AECO natural gas, power, crude | CME Globex / ICE platform, accessed by Bank Of Toronto via a Futures Commission Merchant (FCM) relationship — not a Canadian venue at all |
| **Middle Office** | Commodity risk/confirmation system (Murex/Calypso-class) booking the trade; ICE eConfirm-style electronic confirmation | Same risk/confirmation system class, booking a US-cleared position instead of a Canadian one |
| **Back Office / Clearing** | **NGX Clearing** (part of ICE) clears the trade directly | **CME Clearing** (Section 5's worked example) or ICE Clear — neither is CDS nor CDCC |
| **Regulator** | **Alberta Securities Commission (ASC)** — NGX's primary securities regulator, since NGX is Calgary-based (a genuine exception to the Ontario/CSA-centric pattern other modules lean on) | **CFTC** (Section 8) for the venue itself; **CIRO** for Bank Of Toronto's own dealer conduct as a Canadian participant |
| **OTC reporting?** | Generally **no** — exchange-traded/cleared, same Future-vs-OTC boundary Section 6's takeaway already draws | **No**, for the same reason — Section 4b's WTI Future is exchange-cleared, not OTC |

**Takeaway:** எது Canadian infrastructure-ஐ தொடுது, எது தொடவே இல்லைன்னு கவனிங்க. CME/ICE-listed Futures (Section 4b) trading bank எந்த நாட்டுல தலைமையகம் கொண்டிருந்தாலும் முழுக்க US ecosystem-க்குள்ள clear ஆகுது — அங்க Canadian ஈடுபாடு Bank Of Toronto-ஓட dealer-ஆ CIRO conduct மேற்பார்வைக்கு மட்டும், எந்த Canadian settlement system-க்கும் இல்ல. Canadian venue-ல (NGX) உண்மையில execute ஆன trades மட்டுமே உண்மையான Canadian back-office infrastructure-ஐயும் (NGX Clearing) Canadian regulator-ஐயும் (ASC) நேரடியா தொடுது.

### 9b. Worked example — NGX AECO natural gas swap, ஆரம்பம் முதல் முடிவு வரை

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

### 9c. MI 96-101 reporting உண்மையில பொருந்தற இடம் — மறுபடி Brent swap

Section 4c-ஓட Shell Trading-உடனான Brent Crude Oil Swap bilateral OTC — 9b-க்கு எதிர் நிலை. ஏதாவது ஒரு counterparty-க்கு தேவையான Canadian nexus இருந்தா, அந்த swap (NGX உதாரணம் போல இல்லாம) **CSA Multilateral Instrument 96-101** (Trade Repositories and Derivatives Data Reporting), Canada-ஓட OTC derivatives trade-reporting விதியின் scope-க்குள்ள; CSA-designated Trade Repository-க்கு report ஆகும் — பொதுவா **DTCC Data Repository (Canada) Ltd** அல்லது **ICE Trade Vault Canada** (ரெண்டும் வகையின் illustrative; உண்மையான designated-TR பட்டியலை கட்டறதுக்கு முன்னாடி CSA-ஓட சொந்த வெளியிடப்பட்ட விதிகளுக்கு எதிராக உறுதிப்படுத்துங்க).

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

**Takeaway:** ஒரு Commodity trade எந்த Canadian systems-ஐ உண்மையில தொடுதுன்னு கணிக்கற மிக வேகமான வழி, Section 7-ஓட சொந்த Exchange-vs-OTC கண்டுபிடிப்பை ஒரு நிலை ஆழமா பொருத்தறது — exchange-cleared trade (NGX அல்லது CME/ICE) Canadian OTC Trade Repository-ஐ ஒருபோதும் அடையாது; bilateral OTC trade (Brent swap) Canadian ஆனாலும் வேற ஆனாலும் clearing house-ஐ ஒருபோதும் தொடாது, ஆனா MI 96-101-ன் கீழ Trade Repository-ஐ அடையும். Section 2-ல இருந்து product வகை தெரிஞ்சா, ஒரு குறிப்பிட்ட trade இந்த section-ஓட systems சங்கிலியின் எந்த முழு பாதிவழியா ஓடும்னு தெரிஞ்சிடும்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Physical gold buyer; WTI futures buyer/roller; Brent swap-ல fixed-rate payer; தன் client-க்கு option writer |
| An LBMA-accredited refiner | Physical gold bar-ஓட seller |
| CME (Chicago Mercantile Exchange) | WTI Crude Oil future-க்கான exchange, CCP |
| Shell Trading | Brent Crude Oil Swap-ல floating-rate payer counterparty |
| Bank Of Toronto's client (airline) | Crude oil proxy மூலம் jet fuel செலவு exposure-ஐ hedge பண்ணும் WTI Call option-ஓட buyer |
| CFTC | Commodity futures, swaps-க்கான முதன்மை US regulator |
| LME / ICE | முறையே metals, energy-க்கான முக்கிய commodity exchanges |
| NGX (Natural Gas Exchange) | Physical/financial natural gas, power-க்கான Calgary-based Canadian exchange; Section 9b-ஓட AECO swap-ஓட venue |
| Alberta Securities Commission (ASC) | NGX-ஐ மேற்பார்வை பண்ற முதன்மை securities regulator (Section 9) |
| CSA (Canadian Securities Administrators) | Section 9c-ஓட Brent swap-க்கு பொருந்தும் OTC derivatives trade-reporting விதியான Multilateral Instrument 96-101-ஐ வகுக்கும் |

---

## Recap Quiz — Numbers Edition

1.  Section 4b-ஓட WTI Future roll-ல, Bank Of Toronto *backwardation*-ல roll பண்ணியிருந்தா (next-month front-month-ஐ விட மலிவு, உதா., USD 76.90-க்குப் பதிலா USD 74.80), மற்ற எல்லாம் சமமா இருக்க, contango உதாரணத்தை விட roll-ஏ Bank Of Toronto-க்கு அதிகம் செலவாகுமா குறைவா?
2.  Section 5-ஓட variation margin walkthrough-ஐ வெச்சு, Day 3-ஓட settlement விலை மறுபடி USD 75.20 (Day 2-ல இருந்து மாறாம) ஆக வந்தா, என்ன cash flow நகரும், ஏன்?
3.  Section 3, Commodities-ல physical settlement "actively designed around"-ன்னு விவரிக்கறது ஏன்? [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3 Fixed Income settlement பத்தி சொன்னதோட இது எப்படி ஒப்பிடுது?
4.  ஒரு trade record-ல exchange contract code (`CLZ26` பாணி)-உம் அதே trade-ல UTI-உம் நிரம்பியிருக்கு. Section 6-ஐ வெச்சு, இந்தக் கலவை பெரும்பாலும் எதைக் குறிக்குது?
5.  Section 4c-ஓட Brent Crude Oil Swap-ல, ICE Brent monthly average USD 78.00 fixed விலைக்குக் கீழே USD 76.50-ல settle ஆகியிருந்தா, Month 1 net cash flow என்ன? யார் யாருக்கு கொடுக்கறாங்க?
6.  Section 08-ல CFTC "இந்த module-க்கு மிக விளைவுள்ள ஒற்றை regulator"-ன்னு விவரிக்கப்படுது ஏன் — Section 07-ஓட findings-க்கு காரண இணைப்பு என்ன?
7.  Commodity Futures "கிட்டத்தட்ட உடனடியா" clear ஆவதையும் (Section 05), FX trades ([Module 6](../docs/1-capital-markets/module-06-fx.html) §05) பெரும்பாலும் CCP வழியா clear ஆகவே ஆகாததையும் ஒப்பிடுங்க.
8.  Section 9-ஐ வெச்சு, Bank Of Toronto-ஓட CME WTI Crude Oil Future (Section 4b) CDS (CDSX)-ஐயோ CDCC-யையோ தொடவே தொடாது, ஆனா அதன் NGX AECO natural gas swap (Section 9b) NGX Clearing-ஐ தொடுது — ஒரு Canadian வங்கியோட commodity trade, Canadian market infrastructure-ஐ உண்மையில எப்போ பயன்படுத்துதுங்கறதுக்கு இது காட்டும் பொது விதி என்ன?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் Asset Class Roadmap-ஓட அடுத்த நிறுத்தம் **[Module 8](../docs/1-capital-markets/module-08-rates.html): Rates**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Backwardation-ல roll Bank Of Toronto-க்கு குறைவா செலவாகும் (அல்லது ஒரு credit-ஐயும் உருவாக்கும்), contango-ல உண்மையான செலவுக்கு எதிரா.**

```text
Contango roll (worked example): sell front-month at 76.10, buy
next-month at 76.90 -> pay an extra 0.80/bbl to roll forward

Backwardation roll (this question): sell front-month at 76.10,
buy next-month at 74.80 -> RECEIVE an extra 1.30/bbl to roll forward
```

Contango-ல முன்னோக்கி roll பண்றது எப்பவும் அதிக விலையுள்ள பிற்கால contract-ஐ வாங்குவதா இருக்கும் — futures வழியா long-ஆ இருக்கறதுக்கு தொடர் செலவு. Backwardation-ல முன்னோக்கி roll பண்றது *மலிவான* பிற்கால contract-ஐ வாங்குவதா — தொடர் நன்மை. Delivery எடுக்காம futures-ஐ நீண்ட காலம் வெச்சிருக்கற யாருக்கும் "roll yield" ஒரு உண்மையான, நெருக்கமா கவனிக்கப்படற P&L இயக்கி ஏன்னு இது தான், நேரடியா Section 1-ஓட contango/backwardation வரையறைகளுக்கு திரும்புது.

**2. எந்த cash flow-உம் நகராது — variation margin, settlement விலை முந்தைய நாள் settlement விலையில இருந்து *மாறும்போது* மட்டும்தான் நகரும்.**

Section 5-ஓட mechanic நாளுக்கு நாள் ஒப்பீடு: Day 2 ஏற்கனவே position-ஐ USD 75.20-க்கு mark பண்ணி அந்த வித்தியாசத்தை cash-ல settle பண்ணிடுச்சு. Day 3 அதே USD 75.20-ல settle ஆனா, கூடுதல் mark-to-market நகர்வு பூஜ்யம், அதனால அன்னிக்கு variation margin எதுவும் கை மாறாது — முழு trading நாள் கடந்து போனாலும், position-ஓட மொத்த மதிப்பு நகரலை.

**3. ஏன்னா கிட்டத்தட்ட ஒவ்வொரு Commodity product-உம் (Future, Swap, Option) physical delivery ஒருபோதும் நடக்க வேண்டியதில்லைன்னு கட்டமைப்பு ரீதியா உருவாக்கப்பட்டிருக்கு — rolling futures, cash-settling swaps, futures மேல options எல்லாம் Section 1 விவரிச்ச storage/insurance/transport சுமை இல்லாம விலை exposure தரவே இருக்கு; [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3, இதற்கு மாறா, Fixed Income settlement-ஐ எப்பவும் physical-ஆ (CSD-ல book-entry வடிவத்துல dematerialize ஆனதாக மட்டும்) விவரிச்சது, ஒருபோதும் அதைச் சுத்தி வடிவமைக்கப்படலை.**

Section 3 இதை நேரடியா சொல்லுது: Commodities-க்கு physical settlement "விதிவிலக்கு, வழக்கம் இல்ல"; [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3 ஒரு bond trade உண்மையான (book-entry) security-ஐ நகர்த்தாம இருக்கலாம்னு ஒருபோதும் சொல்லலை — bond trade-ஓட DvP settlement ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7) எப்பவும் உண்மையான security-ஐ transfer பண்ணும், அது இனி காகித certificate இல்லை அவ்வளவுதான்.

**4. இந்தக் கலவை ஆரோக்கியமான pattern இல்ல, red flag — பெரும்பாலும் data-quality error; ஏன்னா Section 6, exchange contract codes centrally-cleared Futures-ஐ identify பண்ணும் (bilateral UTI/UPI reporting தேவையில்ல), UPI/UTI bilaterally report ஆகும் OTC derivatives-ஐ identify பண்ணும்னு நிறுவுது.**

Section 6-ஓட takeaway இதை வெளிப்படையா சொல்லுது: ஒரு record-ல exchange contract code (Future) *அல்லது* UPI/UTI (OTC Swap/Option) இருக்கணும், ரெண்டுமல்ல — ரெண்டும் நிரம்பியிருக்கறது, bonds-ல UPI-க்காக [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6 எச்சரிச்ச, FX-ல எதிர்த்திசைக்காக (ISIN இல்லாம UPI/UTI இருப்பது ஆரோக்கியம், உடைஞ்சதில்ல) [Module 6](../docs/1-capital-markets/module-06-fx.html) §6 எச்சரிச்ச அதே diagnostic pattern-ஓட Commodities-குறிப்பான பதிப்பு.

**5. Bank Of Toronto net தொகையை *கொடுக்கும்*, பெறாது — worked example-ல இருந்து ஒரு தலைகீழ்.**

```text
Floating leg owed to Bank Of Toronto = 8,333 x 76.50 = USD 637,575
Fixed leg owed by Bank Of Toronto    = 8,333 x 78.00 = USD 649,974
Net cash flow: Bank Of Toronto PAYS USD 12,399
(78.00 - 76.50) x 8,333 = USD 12,399
```

Bank Of Toronto fixed-ஐ கொடுத்து floating-ஐ பெறுவதால (Section 4c), floating index fixed விலைக்கு *கீழே* settle ஆகும்போதெல்லாம் Bank Of Toronto net payer — அதிக floating settlement Bank Of Toronto-வை net receiver ஆக்கின worked example-ஓட mirror image. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8 interest rate swaps-க்கு நிறுவின அதே fixed-vs-floating netting logic.

**6. ஏன்னா Section 07, CFTC அதிகார எல்லையில இருக்கற exchange-traded Futures-தான் ஆதிக்க, அதிக liquid Commodity product-ன்னு கண்டுபிடிச்சது — இந்த roadmap-ல மற்ற எல்லா module-லயும் ஆதிக்க product OTC, bilateral-ஆ (Fixed Income, FX) இருந்தது, அல்லது CCP மேற்பார்வை அதிகார எல்லைகளுக்கு நடுவுல சமமா பிரிஞ்சிருந்தது (Equity).**

Section 08-ஓட takeaway இந்த இணைப்பை வெளிப்படையா வரையுது: CFTC-ஓட outsized பொருத்தம், Commodities-ல centrally-cleared, exchange-traded product விதிவிலக்கா இல்லாம வழக்கமா இருக்கற முதல் asset class என்பதன் நேரடி விளைவு — மிகப்பெரிய commodity futures exchanges (CME, ICE) US-regulated.

**7. Commodity Future உடனடியா clear ஆகுது, ஏன்னா execution-ல இருந்தே அது exchange-traded; exchange-ஓட சொந்த CCP ஒவ்வொரு trade-க்கு பின்னாலும் வடிவமைப்பிலேயே நிக்குது (Section 05) — FX trade பெரும்பாலும் CCP வழியா clear ஆகவே ஆகாது, ஏன்னா FX பெரும்பாலும் OTC, bilateral ([Module 6](../docs/1-capital-markets/module-06-fx.html) §05, §07); அது CLS-ஓட PvP mechanism வழியா settle ஆகுது, அது trade-ஐ ஒரு central counterparty-க்கு novate பண்ணாமலே settlement risk-ஐ குறைக்குது.**

இதே கட்டமைப்பு பிளவைத்தான் Section 07 Commodities-க்குள்ளேயே (Futures vs Physical/Swaps) வரையுது, asset classes முழுக்க பொருத்தப்பட்டு: ஒரு trade மைய clear ஆகுதா, எவ்வளவு வேகமா என்பது, ஏதோ asset-class-முழுமையான விதியை சார்ந்ததில்ல, *product* வடிவமைப்பிலேயே exchange-traded-ஆ என்பதை சார்ந்தது — Commodities-ல ரெண்டு pattern-உம் ஒரே asset class-க்குள்ள சேர்ந்து இருக்கு; FX ([Module 6](../docs/1-capital-markets/module-06-fx.html)), Fixed Income ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html)) கிட்டத்தட்ட முழுக்க ஒவ்வொண்ணுக்கும் ஒரு pattern.

**8. CDS/CDCC உண்மையிலயே Canada-வில் அமைந்த infrastructure; Canadian venues-ல உண்மையில trade ஆன products-ஐ மட்டும்தான் clear/settle பண்ணும் — CME-listed WTI future, trading bank எந்த நாட்டுல தலைமையகம் கொண்டிருந்தாலும், முழுக்க US CME/CME Clearing ecosystem-க்குள்ள execute ஆகி clear ஆகுது; Bank Of Toronto-ஓட Canadian entity FCM உறவு வழியா ஒரு remote participant மட்டும், Canadian ஈடுபாடு CIRO-ஓட conduct மேற்பார்வைக்கு மட்டும், settlement infrastructure இல்ல. NGX swap, இதற்கு மாறா, Canada-வில் அமைந்த infrastructure-ல (NGX / NGX Clearing) trade-உம் ஆகுது clear-உம் ஆகுது, அதனால அதுதான் உண்மையான "Canadian-system" பாதை.**

Section 9a-ஓட table பொது விதியை நேரடியா சொல்லுது: trading bank Canadian-ஆ இருக்கறதால மட்டும் இல்ல, **venue தானே** Canadian-ஆ இருக்கும்போது மட்டும்தான் Canadian market infrastructure செயல்படுது — counterparty-ஓட nationality-யும், clearing/settlement சங்கிலியின் nationality-யும் ஒரு trade பத்தின ரெண்டு முற்றிலும் சுயாதீன உண்மைகள்.
