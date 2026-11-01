# Module 6 — FX (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Asset Class Roadmap 3 of 6** — Equity → Fixed Income → **FX** → Commodities → Rates → Credit.
> **மூலம்:** English Module 6-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. FX-ன்னா என்ன? — Theory & Terminology

**Definition:** FX (Foreign Exchange) = ஒரு currency-ஐ இன்னொரு currency-க்கு ஒத்துக்கிட்ட rate-ல மாத்தறது. Issuer இல்ல, bond அர்த்தத்துல maturity இல்ல, ownership உரிமையும் இல்ல — ரெண்டு counterparties, "இப்பவே, அல்லது எதிர்கால ஒரு தேதில, ஒரு currency மற்றதோட இவ்வளவு மதிப்பு"ன்னு ஒப்புக்கறாங்க, அவ்வளவுதான். தினசரி turnover-ல FX உலகின் மிகப்பெரிய நிதிச் சந்தை; Equity, Fixed Income போல இல்லாம, **ஒவ்வொரு FX product-உம் அடிப்படைல ஒரு currency மாத்தற contract** — "என்ன trade ஆகுது"ங்கறதுக்கு அதோட அர்த்தம் Section 3-ல.

| Term | அர்த்தம் |
|---|---|
| Currency Pair | ஒண்ணோடொண்ணு quote பண்ணப்படற ரெண்டு currencies, உதா., USD/CAD — முதலாவது *base* currency, ரெண்டாவது *quote* currency |
| Exchange Rate | Base currency-ஓட ஒரு unit, quote currency-ல எவ்வளவு — உதா., USD/CAD = 1.3500 என்றா 1 USD-க்கு CAD 1.35 |
| Spot Rate | இன்னிக்கான exchange rate, கிட்டத்தட்ட உடனடி settlement-க்கு ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §6) |
| Forward Points | Forward rate பெற spot rate-உடன் கூட்டற/கழிக்கற adjustment; ரெண்டு currencies-ஓட வட்டி விகித வித்தியாசத்தால் தீர்மானிக்கப்படுது ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §10) |
| Notional | Contract reference பண்ற currency தொகை — FX-ல இது Spot/Forward-க்கு உண்மையிலயே settle ஆகும் (IRS-ஓட notional போல இல்ல, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) |
| Pip | Exchange rate-ல நிலையான மிகச்சிறிய quote நகர்வு (பெரும்பாலான pairs-க்கு 0.0001, JPY crosses-க்கு 0.01) — rate நகர்வுகளை quote பண்ண FX-ஓட சொந்த அலகு; basis point-உடன் தொடர்புடையது, ஆனா **அதே விஷயம் இல்ல** |

**Takeaway:** Asset Class Roadmap-ல FX ஒரு வித்தியாசமான இடத்துல இருக்கு — Equity (share), Fixed Income (bond) போல இதுக்கு "security" இல்ல. எளிய same-day trade-ல இருந்து மிகச்சிக்கலான option வரை ஒவ்வொரு FX product-உம் ரெண்டு தரப்புக்கு இடையிலான contractual ஒப்பந்தம். Section 3 இதை வெளிப்படையா சொல்லுது.

> **Deep dive இருக்கு.** இந்த curriculum-ல basis points-ஐ விட்டு pips-ஐ தேர்ந்தெடுக்கற ஒரே asset class FX — "20 pips" ஏன் "20 basis points" இல்லைன்னு காட்டும் USD/CAD worked example [Module 24 — Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §7-ல.

---

## 2. Products என்னென்ன? — Spot, Forward, Swap & Options

```text
FX
│
├── FX Spot       (near-immediate exchange, T+1/T+2 per currency pair)
├── FX Forward     (exchange on a later agreed date, rate fixed today — Module 2 §10)
├── FX Swap        (a spot leg + a forward leg, opposite directions)
└── FX Options     (the RIGHT, not obligation, to exchange at a fixed rate)
```

| Cash / Derivative | Product | அது என்ன |
|---|---|---|
| CASH | **FX Spot** | ரெண்டு currencies-ஐ கிட்டத்தட்ட உடனே மாத்தற ஒப்பந்தம் — பெரும்பாலான pairs-க்கு T+1, சிலதுக்கு T+2 ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §6) |
| DERIVATIVE | **FX Forward** | இன்னிக்கே fix பண்ணின rate-ல, குறிப்பிட்ட எதிர்கால தேதில ரெண்டு currencies-ஐ மாத்தற ஒப்பந்தம் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §10-ஓட worked example) |
| DERIVATIVE | **FX Swap** | எதிர்த்திசைகள்ல இணைக்கப்பட்ட ரெண்டு பரிமாற்றங்கள் — "near leg" (பொதுவா spot), "far leg" (forward) — பொருளாதார ரீதியா, நீண்ட கால interest rate product இல்ல, குறுகிய கால currency கடன் |
| DERIVATIVE | **FX Option** | Buyer-க்கு, expiry-க்குள்ள/அன்னிக்கு, fixed strike rate-ல currency மாத்த உரிமை (கடமை இல்ல) தரும் contract; premium கொடுக்கணும் |

> **பெயர்க் குழப்பம்:** "FX Swap" என்பது **Cross-Currency Swap் அல்ல** — அது ஆண்டுக்கணக்கில் ரெண்டு currencies-ல periodic வட்டி மாத்தற நீண்ட கால product; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ஓட Interest Rate Swap மாதிரி நடந்துக்கும், [Module 8](../docs/1-capital-markets/module-08-rates.html) (Rates)-க்கு சொந்தம். FX Swap குறுகிய கால (பொதுவா overnight-ல இருந்து ஒரு வருஷம் வரை); periodic வட்டி கிடையாது — அசல் பரிமாற்றங்கள் ரெண்டு மட்டும்.

**Takeaway:** இந்த நாலுல மூணு (Spot, Forward, Swap) உண்மையில ஒரே அடிப்படை mechanic — ஒத்துக்கிட்ட தேதில ஒத்துக்கிட்ட rate-ல currency மாத்தறது; வேறுபாடு அந்தத் தேதி *எப்போ*, எத்தனை பரிமாற்றங்கங்கறதுல மட்டும். Option தான் வித்தியாசமானது: உறுதியான கடமை இல்ல, ஒரு contingent உரிமை; [Module 4](../docs/1-capital-markets/module-04-equity.html) §4d equity options-க்கு நிறுவின அதே buyer/seller சமச்சீரற்ற தன்மை.

---

## 3. உண்மையில எது Trade ஆகுது? — அடியோடு Contracts தான்

**Definition:** [Module 4](../docs/1-capital-markets/module-04-equity.html) (Equity: security vs contract), [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) (Fixed Income: security vs financing contract) போல இல்லாம, FX-க்கு ஒப்பிட **security-ஏ இல்ல**. Section 2-ல இருக்கற ஒவ்வொரு product-உம் ஒரு contract — Spot-உம் Forward-உம் ஒரே timeline-ல வேற வேற புள்ளிகள்ல ஒரே mechanic; "உண்மையான பொருள்" vs "உண்மையான பொருளின் derivative" இல்ல.

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

  -> Unlike Equity (Module 4) or Fixed Income (Module 5), there is
     no underlying "security" any of these four reference. FX is
     contracts all the way down — even Spot is just the shortest-
     dated point on the same continuum as Forward.
```

**Takeaway:** இது FX-க்கு "settlement"-ஐ முழுசா மாத்தி வரையறுக்குது — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §6 ஏற்கனவே FX Spot-ஐ security போல T+1/T+2-ல settle ஆகறதா காட்டிச்சு, ஆனா அது security இல்லவே இல்ல. Settlement-ல (Section 5) உண்மையில நடக்கறது ரெண்டு currency payments கை மாறுறது, security seller-ஓட CSD account-ல இருந்து buyer-ஓடதுக்கு நகருவது இல்ல.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked examples இருக்கு.** [FX Spot — Complete Example](../examples/FX_Spot_Complete_Example.html), [FX Forward — Complete Example](../examples/FX_Forward_Complete_Example.html), [FX Option — Complete USD/CAD Example](../examples/FX_Option_Complete_USDCAD_Example.html) — ஒவ்வொண்ணும் இந்த section-ஓட ஒரு product-ஐ உண்மையான எண்களோட முழு trade lifecycle வழியா நடத்திக் காட்டுது.

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

[Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §10-ஓட worked example-ஐ அப்படியே மறுபயன்படுத்தி:

```text
FX FORWARD  —  Bank Of Toronto needs USD 10,000,000 in 3 months
========================================================================

  Today's Spot Rate (USD/CAD): 1.3500
  3-Month Forward Points:      +0.0045
  3-Month Forward Rate:        1.3545

  Bank Of Toronto agrees TODAY with Goldman Sachs:
    In 3 months, pays CAD 13,545,000, receives USD 10,000,000
    (10,000,000 x 1.3545 = 13,545,000)
  -> No cash moves today. Only the RATE is locked in now.
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
    -> this CAD 12,000 is effectively the FUNDING COST of borrowing
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

**Takeaway:** FX Swap (4c) உண்மையில ஒரு Spot (4a)-உம் ஒரு Forward (4b)-உம் ஒட்டி வெச்சதுதான் — இயந்திரத்தனமா புதுசு எதுவுமில்ல, எதிர்த்திசைகள்ல ஒரே நேரத்துல execute ஆன அதே product ரெண்டு. Option (4d), [Module 4](../docs/1-capital-markets/module-04-equity.html) ஏற்கனவே equity options-க்கு நிறுவின சமச்சீரற்ற payoff உள்ள நாலுல ஒரே ஒண்ணு.

### கூடுதல் உதாரணம் — Forward points எப்படி வருது? (எளிய தமிழ்ல)

Forward rate = Spot + Forward Points. Forward points வட்டி விகித வித்தியாசத்துல இருந்து வருது. எளிய யோசனை: CAD-ல வட்டி குறைவு, USD-ல அதிகம்னு வெச்சுக்கோங்க. ஒருத்தர் இன்னிக்கு CAD-ஐ வெச்சு USD வாங்காம, 3 மாசம் கழிச்சு வாங்க ஒத்துக்கிட்டா, அந்த 3 மாசம் அவர் CAD-க்கு குறைந்த வட்டி கிடைக்கற இடத்துல பணத்தை வெச்சிருக்கார்; USD-ஐ வெச்சிருப்பவருக்கு அதிக வட்டி கிடைக்கும் — அந்த வித்தியாசம் forward rate-ல பிரதிபலிக்கும்.

| | மதிப்பு |
|---|---|
| Spot (USD/CAD) | 1.3500 |
| Forward points (3M) | +0.0045 |
| Forward rate (3M) | 1.3545 |
| Pips-ல | 45 pips (0.0045 ÷ 0.0001) |
| Basis points-ல (rate-ஓட %-ஆ) | 0.0045 ÷ 1.3500 ≈ 33 bps |

அதாவது "45 pips" ≠ "45 bps" — pips rate-ஓட நான்காம் தசம இடம்; bps rate-ஓட %-ல நூறில் ஒரு பங்கு. இதுதான் [Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §7 சொல்றது.

---

## 5. Trade Lifecycle — FX-க்குரிய Walkthrough, CLS/PvP

**Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting** pattern-ஐ Section 4a-ஓட FX Spot trade மேல பொருத்தறோம்.

```text
USD/CAD SPOT  —  FULL LIFECYCLE
========================================================================

  14:02:00  ORDER — Bank Of Toronto's treasury desk needs USD 10,000,000

  14:02:03  EXECUTION @ Spot Rate 1.3500 vs Goldman Sachs

  14:02:03  TRADE CAPTURE — Trade ID BOT-FX-2026-018802

  14:10:00  CONFIRMATION/MATCHING -> MATCHED (bilateral, no CCP —
            Section 07)

  Settlement Date = T+1
            SETTLEMENT via CLS (Continuous Linked Settlement) —
            see below

  POSITION — Bank Of Toronto's USD cash position increases by
            USD 10,000,000; CAD cash position decreases by
            CAD 13,500,000

  VALUATION / P&L — the open FX position is marked to the current
            spot rate daily until the underlying USD is used/sold:
              Trade day:        USD 10,000,000 @ 1.3500 = CAD 13,500,000
              One week later:   USD 10,000,000 @ 1.3550 = CAD 13,550,000
              Unrealized P&L = 13,550,000 - 13,500,000 = +CAD 50,000

  REPORTING — FX Spot is generally OUT OF SCOPE for derivative
            transaction reporting (Section 08) — it isn't treated
            as a derivative at all under EMIR/CFTC rules
```

> **Deep dive இருக்கு.** FX position உண்மையில ஒரு எண் இல்ல, இணைக்கப்பட்ட ரெண்டு currency balances — முழு theory-க்கு: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §5.

### CLS — DvP-க்கு FX-ஓட பதில்

**Definition:** [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7, ஒரு security-ஓட ரெண்டு settlement legs-ஐயும் இணைச்சு யாரும் ஆபத்துல இல்லாம பார்த்துக்கற mechanism-ஆ DvP (Delivery versus Payment)-ஐ நிறுவிச்சு. FX-ல deliver பண்ண security இல்ல — அதுக்குப் பதிலா, முக்கிய உலகளாவிய வங்கிகள் (illustrative-ஆ Bank Of Toronto, Goldman Sachs உட்பட) சொந்தமா வெச்சு பயன்படுத்தற **CLS (Continuous Linked Settlement)**, FX-க்கு சமமான **PvP (Payment versus Payment)**-ஐ நிறைவேத்துது.

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

**Takeaway:** CLS ஒரு CCP இல்ல — NSCC அல்லது swap CCP செய்யற மாதிரி trades-ஐ novate பண்ணாது, counterparty credit risk-ஐ ஏத்துக்காது ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6). இது சுத்தமான settlement-risk குறைப்பு — securities-க்கு DvP தீர்க்கற அதே பிரச்சனைக்கு FX-குறிப்பான பதில்.

### கூடுதல் உதாரணம் — CLS இல்லாம vs CLS-ஓட, நேரம் வாரியா

| நேரம் (Toronto) | CLS இல்லாம | CLS-ஓட |
|---|---|---|
| 08:00 | Bank Of Toronto CAD 13,500,000 அனுப்புது | CAD leg Lynx வழியா CLS-க்குள்ள போகுது |
| 09:00–12:00 | Goldman Sachs-ஓட USD leg இன்னும் வரலை — Bank Of Toronto வெளிப்படையான ஆபத்துல | USD leg CLS-க்குள்ள வருது |
| 13:00 | Goldman Sachs இன்னும் தோத்தா — Bank Of Toronto பணம் இழந்தது | CLS ரெண்டு legs-ஐயும் ஒரே நேரத்துல settle பண்ணுது; ஒண்ணு தவறினா எதுவுமே நடக்காது |

---

## 6. Identifiers — Currency Pairs, UPI, UTI & LEI

Modules 4, 5 சார்ந்திருந்த identifiers-ஐ FX பெரும்பாலும் பயன்படுத்தறதில்ல — currency pair trade-க்கு ISIN, CUSIP, SEDOL, Ticker, FIGI எதுவும் இல்ல, ஏன்னா (Section 3) identify பண்ண security-ஏ இல்ல.

| Identifier | FX-க்கு பொருந்துமா? | Example |
|---|---|---|
| ISIN / CUSIP / SEDOL / Ticker / FIGI | **இல்ல** — இவை securities-ஐ identify பண்ணும்; FX-க்கு அவை இல்ல | — |
| Currency Pair (ISO 4217 codes) | ஆமா — FX-க்கு "instrument" identifier-க்கு மிக நெருக்கமானது | `USD/CAD` |
| **UPI** | ஆமா, OTC derivatives-ஆ report ஆகற FX Forwards, Swaps, Options-க்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) | "USD/CAD, Forward, Deliverable"-க்கு ஒரு UPI — பல Forward trades-ல மறுபயன்பாடு, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ஓட IRS உதாரணத்தின் அதே logic |
| **UTI** | ஆமா — ஒவ்வொரு தனி FX Forward/Swap/Option trade-க்கும் தனி UTI | ஒவ்வொரு trade-க்கும் புது UTI, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12 மாதிரி |
| **LEI** | ஆமா — Bank Of Toronto, Goldman Sachs, எந்த client-ஐயும் counterparties-ஆ identify பண்ணும் | Entity identifier, instrument இல்ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §6) |

**Takeaway:** Reporting record-ல currency pair இருந்து ISIN/CUSIP இல்லன்னா, FX-க்கு அது முற்றிலும் இயல்பு — security identifier இல்லாதது data இடைவெளி இல்ல; security-ஏ இல்லாத asset class-க்கு (Section 3) அதுதான் சரியான நிலை.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** பெரும்பாலான FX trades — Spot, Forward, Swap, பெரும்பாலான Options — **OTC, bilateral**, CCP-க்கு novate ஆகாம CLS-ஓட PvP mechanism (Section 5) வழியா settle ஆகுது. Forwards, NDFs-ல சிறிய ஆனா வளரும் பங்கு centrally clear ஆகுது; listed FX futures/options தனி, சிறிய சந்தையா exchange-ல உண்டு.

| Cash / Derivative | Product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|---|
| CASH | FX Spot | OTC | Bilateral (CLS-ஓட PvP வழியா settle, CCP இல்ல — Section 05) |
| DERIVATIVE | FX Forward | OTC | பெரும்பாலும் bilateral; வளரும் பங்கு LCH ForexClear அல்லது CME வழியா clear (குறிப்பா NDFs) |
| DERIVATIVE | FX Swap | OTC | Bilateral (வழக்கம்) |
| DERIVATIVE | FX Option | OTC | பெரும்பாலும் bilateral |
| DERIVATIVE | FX Futures/Options (listed) | **Exchange-traded** (உதா., CME) | Centrally cleared — OTC FX-ல இருந்து தனியான சிறிய சந்தை, இந்த module-ஓட நாலு core products-ல இல்ல, ஆனா இருக்குன்னு தெரிஞ்சிருக்கணும் |

**Takeaway:** FX, அமைப்பு ரீதியா [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity-ஐ (centrally cleared வழக்கம்) விட [Module 5](../docs/1-capital-markets/module-05-fixed-income.html)-ஓட Fixed Income-க்கு (bilateral OTC வழக்கம்) நெருக்கம் — ஆனா FX ஒரு மூன்றாவது mechanism-ஐ சேர்க்குது, அந்த ரெண்டு asset classes-க்கும் இல்லாதது: CCP-யோட counterparty-risk குறைப்பு இல்லாம settlement-risk குறைப்பு (CLS-ஓட PvP). Bilateral counterparty risk ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6)-உம் settlement risk (இந்த module)-உம் இரண்டு வேற risks; CLS ரெண்டாவதை மட்டும் தீர்க்குது.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | FX-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Investment நோக்கத்துக்கு பயன்படற FX Forwards, Swaps, Options-ஐ உள்ளடக்கும்; முழுக்க commercial/payment நோக்கத்துக்கு physically-settled FX Forwards பெரும்பாலும் MiFID எல்லைக்கு வெளியே — EU விதியாக்கத்துல நிஜமாவே சர்ச்சைக்குரிய எல்லை |
| **EMIR** | FX **Forwards, Swaps, Options**-க்கு OTC derivatives-ஆ பொருந்தும் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) — ஆனா FX Spot-க்கு **இல்ல**. "Spot"-க்கும் "Forward"-க்கும் இடையிலான regulatory எல்லை ஒரு settlement-date threshold-ஆல வரையறுக்கப்படுது (அந்த currency pair-க்கான நிலையான spot cycle — பெரும்பாலும் T+1/T+2, சில emerging-market pairs-க்கு நீளம்) — அந்த window-க்குள்ள settle ஆனா Spot, scope-க்கு வெளியே; அதுக்கு அப்பால் Forward, scope-க்குள்ள |
| **SFTR** | FX Swaps-க்கு **பொருந்தாது**, repo/securities-lending அர்த்தத்துல "swap" என்ற பெயர் ஒற்றுமை இருந்தாலும் ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08) — SFTR குறிப்பா securities/commodities financing-க்கு, currency exchange-க்கு இல்ல. தெளிவாக குறிக்க வேண்டிய எளிதான குழப்பம் |
| **FCA** (UK) | FX-க்கு குறிப்பா அதிகப் பொருத்தம் — உலகளாவிய FX trading volume-ல சுமார் 40%+ London கையாளுது (BIS Triennial Survey); FCA-வை பிராந்திய மட்டுமில்ல, உலகளவுல மிக விளைவுள்ள FX conduct regulators-ல ஒண்ணா ஆக்குது |
| **CIRO** (Canada) | Canadian FX dealer conduct-ஐ மேற்பார்வை பண்ற SRO, Bank of Canada-ஓட பரந்த market-infrastructure மேற்பார்வையோட |
| **HKMA** (Hong Kong) | இந்த roadmap-ல இதுவரை HKMA-வுக்கு மூன்றாவது தனி பங்கு: FX-க்கு, Linked Exchange Rate System-ன் கீழ **Hong Kong Dollar-ஓட US Dollar peg-ஐ** காக்க HKMA நேரடியா சந்தையில தலையிடுது — மேற்பார்வைப் பங்கு மட்டுமில்ல, நேரடி market-participant பங்கு |
| **DTCC** | DTCC-யோட **GTR (Global Trade Repository)** மற்ற asset classes-உடன் FX derivative reports-ஐ (Forwards, Swaps, Options) ஏற்கும், CFTC reporting தேவைகளுக்கு உணவளிக்கும் ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §05) |
| **DMO** (UK Debt Management Office) | FX-க்கு சம்பந்தமில்ல — Gilts வெளியிடுது ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08), முழுமைக்காக மட்டும் இங்க |

**Takeaway:** இதுவரை மூணு asset-class modules-லயும் HKMA அர்த்தமுள்ள வகையில் வேற வேற பாத்திரத்துல வந்திருக்கு — Equity-ல ([Module 4](../docs/1-capital-markets/module-04-equity.html)) securities conduct footnote; Fixed Income-ல ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html)) நேரடி bond issuer; இப்போ FX-ல currency peg-ஐ காக்கும் நேரடி market participant. ஒரே அமைப்பு, நீங்க பார்க்கற asset class-ஐப் பொறுத்து மூணு உண்மையிலயே வேற வேலைகள்.

---

## 9. Canadian Systems Landscape — FX Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §4 Canada-ஓட *பொதுவான* Front/Middle/Back Office architecture-ஐ (Bloomberg AIM, CDSX, CDCC, Lynx) ஏற்கனவே பெயரிட்டிருக்கு. இந்த section அதை FX-க்கு மட்டும் சுருக்குது — equity அல்லது bond trade-ஓட back office-ஐ விட முற்றிலும் வேறுபட்ட back office உள்ள asset class, ஏன்னா CSD leg-ஏ இல்ல (Section 3: FX-க்கு security இல்ல), CLS-ஓட PvP வழியா settle ஆகற ரெண்டு currency payments மட்டும் (Section 5).

> **Callout — illustrative, பரிந்துரை இல்ல.** கீழ உள்ள system பெயர்கள் (EBS, Refinitiv, 360T, Murex, Calypso) Canadian வங்கி FX desks இணையற உண்மையான venues, vendors-ஓட பிரதிநிதிகள் — Bank Of Toronto தானே உள்ளுக்குள்ள எந்த systems-ஐ ஓட்டுதுங்கறதைப் பத்தின கூற்று இல்ல. ஒவ்வொரு stage-லயும் system-ஓட *வகை* நிலையான உண்மை; குறிப்பிட்ட product பெயர் illustrative.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | **EBS**, **Refinitiv Matching/FXall**, **360T** — the major interbank/multi-dealer FX venues; plus Bank Of Toronto's own eFX pricing/aggregation platform for client flow | Price discovery and execution — where Section 4's Spot/Forward/Swap trades actually get dealt |
| **Middle Office** | Trade capture and confirmation via SWIFT MT300-type confirmation messages; risk systems (Murex, Calypso FX module — illustrative) | Confirms both sides agree on rate/notional/value date; feeds daily FX position/P&L (Section 5) |
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
    This is an FX FORWARD, not Spot -> in scope for CSA MI 96-101
    -> reported by T+1 to a CSA-designated Trade Repository (DTCC
    Data Repository (Canada) Ltd / ICE Trade Vault Canada)
    Bank of Canada: oversees CLS itself as a systemically important
    FMI (not this individual trade)
    CIRO: oversees Bank Of Toronto's dealer conduct on the trade
```

**Takeaway:** இந்த roadmap-ல இதுவரை FX back office அமைப்பு ரீதியா வித்தியாசமானது — Equity-உம் Fixed Income-உம் security leg-க்கு CSD (CDSX) வழியா route ஆகுது; FX-க்கு security leg-ஏ இல்ல, அதனால CLS-ஓட PvP mechanism-தான் *முழு* back office, Lynx அதுக்குக் கீழ domestic CAD payment rail-ஐ மட்டும் தருது. "settlement = ஒரு CSD movement" (Modules 4–5-க்கு உண்மை)ன்னு கட்டின reporting system-க்கு FX-க்கு சுட்டிக்காட்ட CSD event இருக்கவே இருக்காது — CLS-ஓட settlement confirmation மட்டுமே இருக்கற ஒரே settlement record.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Spot, forward-ல USD buyer; தன் சொந்த FX Swap-ஓட ரெண்டு legs-உம்; தன் client-க்கு option writer |
| Goldman Sachs | Spot, forward trades-ல USD seller |
| Bank Of Toronto's client | எதிர்கால USD payable-ஐ hedge பண்ணும் USD Call option-ஓட buyer |
| CLS (Continuous Linked Settlement) | Member banks இடையே FX settlement (Herstatt) risk-ஐ நீக்கும் PvP settlement infrastructure |
| LCH ForexClear / CME | FX Forwards, NDFs-ல வளரும் பங்கு centrally clear ஆகும் venues/CCPs |
| Bank of Canada | Canadian financial-stability பார்வையில CLS-ஐ systemically important FMI-ஆ மேற்பார்வை பண்ணுது (Section 9) |
| CIRO (Canada) | Bank Of Toronto-ஓட FX dealer conduct-ஐ மேற்பார்வை பண்ற SRO (Section 9) |
| CSA-designated Trade Repository (உதா., DTCC Data Repository (Canada) Ltd / ICE Trade Vault Canada) | FX Forward-ஓட MI 96-101 report-ஐ பெறுது (Section 9) |

---

## Recap Quiz — Numbers Edition

1.  Section 4c-ஓட FX Swap-ல, 1-month forward points +0.0012-க்கு பதிலா -0.0020 (மாதத்துல CAD, USD-ஐ விட வலுவாகுது) இருந்திருந்தா, far-leg forward rate என்ன? Bank Of Toronto-ஓட funding cost worked example-ஓட CAD 12,000-ஐ விட அதிகமா குறைவா?
2.  FX Spot-க்கு "bond அல்லது share வெச்சிருக்கற அதே identifier" (Section 6) ஏன் தேவையில்ல? இது data-quality இடைவெளியா, எதிர்பார்த்த நடத்தையா?
3.  ஒரு trade record-ல FX product-க்கு UPI-உம் UTI-உம் இருக்கு, ISIN இல்ல. அந்த record உடைஞ்சதா, இல்ல சரியா நிரப்பப்பட்ட FX Forward trade record அப்படித்தான் இருக்கணுமா?
4.  CLS "CCP இல்ல"ன்னு Section 5 சொல்றது, அது தன்மூலமா settle ஆகற ஒவ்வொரு trade-லயும் risk-ஐ குறைக்கறது தெளிவா இருந்தாலும் ஏன்? ஒரு வாக்கியத்துல.
5.  Bank Of Toronto-ஓட client-ஓட USD Call option (Section 4d) 1.3600-ல strike, CAD 90,000 premium-ஓட. Expiry-ல spot சரியா 1.3600 (strike-லயே மாறாம) என்றா, net outcome என்ன? Scenario A, Scenario B ரெண்டுலயும் இருந்து இது ஏன் வேறுபடுது?
6.  Section 8-ஐ வெச்சு, Bank Of Toronto-க்கும் Goldman Sachs-க்கும் இடையிலான plain FX Spot trade EMIR-ல report பண்ணத்தக்கதா? Section 4b-ஓட FX Forward-ஆ?
7.  Section 8-ல சொன்ன scale புள்ளியை வெச்சு, உதா., CIRO-வோட FX மேற்பார்வையை விட FCA-வோட FX மேற்பார்வையை regulator ஏன் அதிகம் கவனிக்கலாம்?
8.  Section 9-ஐ வெச்சு, Section 4b-ஓட FX Forward CLS வழியா settle ஆகுது; Bank Of Toronto-ஓட Canadian equity trade CDSX வழியா ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §4). "settlement = CSD book-entry movement"-ஐ சுற்றி கட்டின reporting system-ஐ FX Forward-க்கு மாற்றம் இல்லாம மறுபயன்படுத்த முடியாதது ஏன்?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் Asset Class Roadmap-ஓட அடுத்த நிறுத்தம் **[Module 7](../docs/1-capital-markets/module-07-commodities.html): Commodities**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Far-leg forward rate = 1.3500 - 0.0020 = 1.3480. Bank Of Toronto-ஓட funding cost, செலவுக்குப் பதிலா ஒரு லாபமா மாறும்.**

```text
Far leg: sell USD 10,000,000 @ 1.3480, receive CAD 13,480,000
Net effect = 13,480,000 - 13,500,000 = -CAD 20,000
```

Bank Of Toronto near leg-ல கொடுத்ததை விட CAD 20,000 *குறைவா* பெறும் — அதாவது funding cost கட்டறதுக்குப் பதிலா swap-ஐ வெச்சிருக்கறதால CAD 20,000 லாபம் பெறுது; ஏன்னா negative forward points (CAD forward-ல *மலிவாகுது*, அல்லது swap points திரும்பறது) அசல் +0.0012 உதாரணத்துக்கு எதிர்த்திசைல வேலை செய்யுது. CAD/USD வட்டி வித்தியாசத்தால இயக்கப்படற forward points-ஓட குறி, அந்த foreign currency-ஐ கடன் வாங்கறவருக்கு FX Swap நிகரச் செலவா நிகர நன்மையான்னு நேரடியா தீர்மானிக்குது.

**2. FX Spot-க்கு அப்படி identifier இல்ல, ஏன்னா identify பண்ண security-ஏ இல்ல (Section 3) — இது எதிர்பார்த்த நடத்தை, இடைவெளி இல்ல.**

Section 6-ஓட takeaway இதை வெளிப்படையா சொல்லுது: ISIN/CUSIP/Ticker securities-ஐ identify பண்ணவே இருக்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2, [Module 4](../docs/1-capital-markets/module-04-equity.html) §6, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6). Spot உட்பட FX products securities இல்ல — அவை currency மாத்தற contracts, அதனால security identifier இல்லாதது சரியே, விடுபட்ட field இல்ல.

**3. சரியா நிரப்பப்பட்ட FX Forward trade record அப்படித்தான் இருக்கணும்.**

Section 6-ஓட table இதை நேரடியா சொல்லுது: UPI-உம் UTI-உம் FX Forwards, Swaps, Options-க்கு (OTC derivatives-ஆ, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) பொருந்தும்; ISIN FX-க்கு ஒருபோதும் பொருந்தாது. UPI/UTI நிரம்பி ISIN காலியா இருக்கற record FX-க்கு ஆரோக்கியமான pattern, red flag இல்ல — இது [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6-ஓட *எதிர்* pattern (bond record-ல UPI நிரம்பியிருப்பது சந்தேகமானது) பத்தின எச்சரிக்கைக்கு முரணா இருக்கு.

**4. ஏன்னா CLS settlement-timing risk-ஐ மட்டும் தீர்க்குது (ரெண்டு currency payments-உம் ஒண்ணா நடக்கறதை உறுதி செய்யறது, PvP) — அது trade-ல ஒரு தரப்பாகவே ஆகாது, CCP-யோட novation செய்யற மாதிரி எந்த தரப்போட counterparty credit risk-ஐயும் உள்வாங்காது.**

Section 5-ஓட takeaway இதை துல்லியமா சொல்லுது: CLS, DvP-ஓட இயந்திர பாதுகாப்பை ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7) பிரதிபலிக்குது, ஆனா அங்கேயே நிக்குது — [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6-ல விவரிச்ச CCP novation போல counterparty-risk குறைப்பான் இல்ல, settlement-risk குறைப்பான் மட்டும்.

**5. Option சரியா at-the-money-ல expire ஆகுது — exercise பண்ணுவது தொழில்நுட்ப ரீதியா பயனற்றது (strike = spot என்பதால intrinsic value இல்ல), அதனால buyer lapse ஆக விட்டு முழு CAD 90,000 premium-ஐ இழக்கிறார் — Scenario B-ஓட அதே அதிகபட்ச-இழப்பு முடிவு, A-க்கும் B-க்கும் நடுவுல ஒரு நடுநிலை இல்ல.**

Spot = strike = 1.3600-ல, exercise பண்ணுவதால எந்த நன்மையும் இல்ல (option வழியா 1.3600-ல வாங்குறதும் spot சந்தையில 1.3600-ல வாங்குறதும் ஒண்ணுதான்), அதனால CAD 90,000 premium முழு இழப்பு — spot 1.3400 வரை விழலைன்னாலும் Scenario B-ஓட அதே முடிவு. Option-ஓட payoff-க்கு strike-ப்ளஸ்-premium-சமமான இடத்துல ஒரு கடினமான breakeven உண்டு, Scenario B-ல இருந்து A வரை மென்மையா scale ஆகற payoff இல்லன்னு இது காட்டுது.

**6. இல்ல, plain FX Spot trade EMIR-ல report பண்ணத்தக்கது இல்ல. Section 4b-ஓட FX Forward EMIR-ல report பண்ணத்தக்கது.**

Section 8-ஓட table இதை நேரடியா சொல்லுது: EMIR, FX Spot-ஐ வரையறைப்படியே விலக்குது (FX-க்கு "derivative"-ஐ வரையறுக்கற settlement-date threshold-க்கு வெளியே) ஆனா FX Forwards, Swaps, Options-ஐ வெளிப்படையா உள்ளடக்குது. 3 மாசம் கழிச்சு settle ஆகற Section 4b Forward EMIR-ஓட scope-க்குள்ள முழுசா; T+1-ல settle ஆகற Section 4a Spot இல்ல.

**7. ஏன்னா London-ம் (அதனால FCA-வோட அதிகார எல்லையும்) உலகளாவிய FX trading volume-ல சுமார் 40%+ கையாளுது (BIS Triennial Survey) — இந்த அளவு FCA மேற்பார்வையை UK-domiciled நடவடிக்கைக்கு மட்டுமில்ல, உலகளாவிய FX சந்தை முழுமைக்கும் விளைவுள்ளதா ஆக்குது; CIRO-வோட எல்லை குறிப்பா Canadian dealer conduct-க்கு மட்டுமானது.**

Section 8 இதை வெளிப்படையா "outsized relevance"-ன்னு குறிக்குது — இது CIRO அல்லது FCA தானே, உதா., உள்நாட்டு equity trading-க்கு ([Module 4](../docs/1-capital-markets/module-04-equity.html) §8) வகிக்கற conduct-regulator பங்கை விட உண்மையிலயே வேற வகை முக்கியத்துவம்; அங்க ஒவ்வொரு regulator-ஓட முக்கியத்துவமும் தன் சொந்த உள்நாட்டு சந்தை அளவுக்கு கிட்டத்தட்ட விகிதாசாரம்.

**8. ஏன்னா மறுபயன்படுத்த CSD leg இல்ல — FX-க்கு security இல்ல (Section 3), அதனால reporting system சுட்டிக்காட்ட book-entry movement இல்லவே இல்ல; CLS-ஓட PvP confirmation தான் முழு settlement record, CDSX-பாணி event-ஓட ஒரு மாறுபாடு இல்ல.**

Section 9-ஓட takeaway இதை நேரடியா சொல்லுது: Equity-யும் Fixed Income-உம் security leg-க்கு CSD (CDSX) வழியா route ஆகுது, ஆனா FX-ஓட back office அமைப்பு ரீதியா வேறு — CLS-ஓட PvP mechanism *தான்* முழு back office, Lynx அதுக்கு கீழே domestic CAD payment rail-ஐ மட்டும் தருது. ஒவ்வொரு settled trade-க்கும் அதுக்குரிய CSD movement இருக்குன்னு கருதற reporting system, FX trade-க்கு சுட்டிக்காட்ட எதுவும் இல்லாம இருக்கும்; equity/bond model-ஓட parameterized பதிப்பா இல்லாம, FX-க்கு உண்மையிலயே தனி settlement-event model தேவை.
