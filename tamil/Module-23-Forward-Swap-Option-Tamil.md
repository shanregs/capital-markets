# Module 23 — Forward, Swap & Option Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Reference module**, **[Hedging Deep Dive](../docs/1-capital-markets/module-22-hedging-deep-dive.html)**, **[ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html)** குடும்பத்தை சேர்ந்தது. இந்த மூணு structures-க்கான worked examples ஏற்கனவே [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9–10, [Module 6](../docs/1-capital-markets/module-06-fx.html) §4b–4d, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §4c–4d, [Module 8](../docs/1-capital-markets/module-08-rates.html) §4a–4d-ல சிதறி இருக்கு — இந்த module அவை எல்லாத்தையும் ஒரே theory-க்குள்ள இழுத்து, roadmap தனியா வேலை செய்யாத ரெண்டு இடைவெளிகளை (Equity Forward, Commodity Forward) நிரப்பி, ஒவ்வொரு asset class-ஓட Forward/Swap/Option பதிப்பையும் பக்கம்-பக்கமா வைக்குது.
> **மூலம்:** English Module 23-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Forward, Swap, Option-ன்னா என்ன? — Theory: மூன்று OTC கட்டுமானத் தொகுதிகள்

**Definition:** இந்த curriculum கட்டின கிட்டத்தட்ட ஒவ்வொரு OTC derivative-உம் — Rates, Credit, FX, Equity, Commodity முழுக்க — சரியா மூன்று அடிப்படை structures-ல ஒண்ணு. [ISDA-ஓட Product Taxonomy](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html) §2 இதை தன் **Transaction Type** level-ஆவே encode பண்ணுது: `Forward`, `Swap`, (Call/Put வழியா) `Option` — அந்த level பயன்படுத்தும் உண்மையான கட்டுப்படுத்தப்பட்ட-சொல்லகராதி மதிப்புகள், ஐந்து asset classes ஒவ்வொண்ணுலயும்.

| | **Forward** | **Swap** | **Option** |
|---|---|---|---|
| **ஒரு-வரி theory** | ஒரு நிலையான விலைல, ஒரே ஒரு எதிர்கால தேதில, ஒரே ஒரு தடவை transact பண்ணும் கடமை | ஒரு காலம் முழுக்க, ஒரு அட்டவணைப்படி, திரும்பத் திரும்ப transact பண்ணும் கடமை | ஒரு நிலையான விலைல, expiry-க்குள்ள/அன்னிக்கு, ஒரு தடவை transact பண்ணும் உரிமை (கடமை இல்ல) |
| **பரிமாற்றங்களின் எண்ணிக்கை** | ஒண்ணு | பல (periodic) | பூஜ்யம் (exercise ஆகாட்டி) அல்லது ஒண்ணு (exercise ஆனா) |
| **Inception-ல Premium** | இல்ல | இல்ல | ஆமா — முன்கூட்டியே செலுத்தணும் |
| **Payoff வடிவம்** | நேர்கோடு, சமச்சீர் — எந்தத் திசையிலும் dollar-for-dollar நகரும் | நேர்கோடு, சமச்சீர், ஒவ்வொரு period-லயும் திரும்பத் திரும்ப | சமச்சீரற்றது — buyer-க்கு வரம்புக்குள்ள downside, seller-க்கு (கொள்கையளவுல) வரம்பற்றது |

```text
THE STRUCTURAL RELATIONSHIP — HOW THE THREE FIT TOGETHER
========================================================================

  A SWAP is literally a STRIP OF FORWARDS -- one forward-style
  exchange per payment date, bundled into a single legal contract
  instead of negotiated separately. Section 3's IRS worked example
  makes this exact -- each quarterly settlement is its own tiny
  forward-rate calculation.

  An OPTION is the RIGHT to enter a Forward, not the obligation.
  Strip away the "right, not obligation" and pay no premium, and an
  Option degenerates into a Forward -- which is why a Forward is
  sometimes described as "an Option with a 100% guaranteed exercise
  decision, and therefore no premium charged for that certainty."

  Forward  ---- repeat many times ---->  Swap
     |
     +---- make it optional, charge a premium ---->  Option
```

**Takeaway:** இந்த மூன்று-வழிக் கட்டமைப்பு ஒரு curriculum எளிமைப்படுத்தல் இல்ல — அது உண்மையான regulatory வகைப்பாடு. [ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html) §2-ஓட Transaction Type level சரியா இந்த மதிப்புகளைத்தான் பயன்படுத்துது; அதனாலதான் underlying ஒரு rate, ஒரு credit spread, ஒரு currency pair, ஒரு stock, அல்லது ஒரு barrel oil எதுவா இருந்தாலும் அதே மூணு சொற்கள் trade records-ல தோன்றுது (Section 7 நேரடியா இதற்குத் திரும்புது).

### கூடுதல் உதாரணம் — தமிழ் வாழ்க்கை ஒப்புமை: ஒரு திருமண மண்டபம்

| | ஒப்புமை |
|---|---|
| **Forward** | "6 மாசம் கழிச்சு, ₹50,000-க்கு மண்டபம் எனக்கு"ன்னு இன்னிக்கே உறுதிப்படுத்தறது; முன்பணம் இல்ல, அந்த நாளில் ரெண்டு பேரும் ஒப்பந்தப்படி நடக்கணும் |
| **Swap** | ஒவ்வொரு மாசமும் ஒரே மண்டபத்தை "நிலையான வாடகை ₹10,000 ↔ சந்தை வாடகை"ன்னு மாத்திக்கறது; வித்தியாசத்தை மட்டும் செட்டில் பண்ணறது |
| **Option** | ₹2,000 கொடுத்து "6 மாசத்துக்குள்ள நான் விரும்பினா ₹50,000-க்கு மண்டபத்தை எடுக்கலாம்"ங்கற உரிமையை வாங்கறது; வேணாம்னா ₹2,000 போச்சு |

---

## 2. FORWARD — Theory & Asset Classes முழுக்க Worked Examples

**Definition:** **Forward** = ஒரு குறிப்பிட்ட எதிர்கால தேதில, நிலையான விலைல, ஏதோ ஒண்ணோட நிலையான அளவை transact பண்ணும் bilateral ஒப்பந்தம் — inception-ல premium பரிமாற்றம் இல்ல, (credit risk தவிர) அந்த ஒரே எதிர்கால தேதி வரை எந்த cash-உம் நகராது. இதன் payoff **நேர்கோடு**: P&L = (ஒத்துக்கிட்ட விலை − maturity-ல சந்தை விலை) × Notional, எந்தத் திசையிலும் dollar-for-dollar நகரும் — [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §10-ஓட FX Forward ஏற்கனவே currencies-க்கு காட்டியதுபோல.

```text
FORWARD PAYOFF — LINEAR, BOTH DIRECTIONS
========================================================================

  Buyer's P&L
      +  |                    /
         |                  /
         |                /
    -----+--------------+------------------  Market Price at maturity
         |            / Agreed
         |          /  (Forward) Price
      -  |        /

  -> unlike Section 4's Option, there is NO flat/capped region on
     either side -- the line runs straight through, in both directions
```

### FX — Module 2 §10-ஐ முழுசா மறுபயன்படுத்தி

```text
3-MONTH USD/CAD FORWARD — Bank Of Toronto needs USD 10,000,000
========================================================================
  Forward Rate locked today: 1.3545 (Spot 1.3500 + 0.0045 forward points)
  In 3 months: Bank Of Toronto pays CAD 13,545,000, receives USD 10,000,000
  -> no cash moves today; only the RATE is locked in now
```

### Rates — FRA, அதன் உறவினர் IR Forward Debt (Module 8 §4b, §4e)

```text
FRA (Forward Rate Agreement) — a "forward" written on a RATE itself
========================================================================
  3x6 FRA: Bank Of Toronto BUYS (pays fixed, receives floating)
  Notional CAD 20,000,000 | FRA Rate: 4.85% | 91-day reference period

  At fixing, 3-month CORRA settles at 4.50% (lower than locked-in 4.85%)
  Bank Of Toronto PAYS Goldman Sachs CAD 17,259.79 -- the discounted
  difference between the FRA Rate and the settled floating rate

  -> ONE isolated forward-rate exchange, settled ONCE, upfront
     (discounted) rather than at period-end -- Section 3 shows this
     is exactly what an IRS repeats every quarter, undiscounted

IR FORWARD DEBT — a "forward" written on a BOND'S PRICE instead
========================================================================
  Bank Of Toronto agrees TODAY to buy Canada 5% Bond 2035 in 3 months
  Forward Price: 97.80 (today's spot 98.50, minus 3 months of carry)
  In 3 months, if physically settled: pays CAD 9,780,000, receives
  CAD 10,000,000 face value of the bond via DvP

  -> structurally identical to the FX Forward above (fixed price,
     one future exchange), just referencing a BOND'S PRICE instead
     of a CURRENCY PAIR'S rate -- and unlike the FRA, an ACTUAL
     security can change hands if not cash-settled first
```

### Equity — புதிய worked example (மறுபயன்படுத்த curriculum trade எதுவும் இல்லை)

```text
EQUITY FORWARD — Bank Of Toronto agrees to sell KO shares forward
========================================================================
(illustrative -- fills a gap Module 4 never separately worked;
built on Module 4 §4a's real KO position: 10,000 shares @ 68.40)

  Bank Of Toronto (holder of 10,000 KO shares) agrees TODAY to sell
  all 10,000 shares to a client in 6 months, at a forward price fixed
  now instead of whatever the market happens to be then.

  Forward Price = Spot + Cost of Carry - Expected Dividends
                = 68.40 + (68.40 x 4.5% x 0.5) - (68.40 x 1.5% x 0.5)
                = 68.40 + 1.539 - 0.513 = 69.43 (illustrative)

  IN 6 MONTHS: client pays 10,000 x 69.43 = USD 694,300, receives
  10,000 KO shares -- regardless of where KO is actually trading then

  -> same cost-of-carry logic as Module 6's FX forward points
     (interest-rate differential), with an extra term for the
     dividends the SELLER forgoes by not holding the position anymore
```

### Commodity — புதிய worked example (மறுபயன்படுத்த curriculum trade எதுவும் இல்லை)

```text
COMMODITY FORWARD — OTC, bilateral, uncleared version of a Future
========================================================================
(illustrative -- contrast directly with Module 7 §4b's EXCHANGE-
TRADED WTI Future, same underlying, different venue)

  Bank Of Toronto agrees TODAY, bilaterally with an oil producer
  client, to buy 50,000 bbl WTI crude in 2 months at USD 77.00/bbl
  fixed -- negotiated directly, NOT executed on CME's order book,
  NOT novated to a CCP at execution (contrast Module 7 §4b's Future,
  which clears instantly)

  IN 2 MONTHS, if WTI spot is USD 82.00/bbl:
    Bank Of Toronto still only pays 77.00/bbl -- gain of
    (82.00 - 77.00) x 50,000 = USD 250,000 vs. buying at spot then

  -> ECONOMICALLY near-identical to Module 7 §4b's exchange Future
     -- the difference is entirely about VENUE and CLEARING (Module
     15's "standardization is a venue property, not an asset-class
     property"), not about the payoff mechanics
```

> **Callout — Credit-க்கு தூய "Forward" கட்டுமானத் தொகுதி இல்ல.** மற்ற நாலு asset classes போல இல்லாம, Credit-ஓட முக்கிய OTC product (CDS, Section 3) ஏற்கனவே ஒரு one-off நிலையான-விலை பரிமாற்றமா இல்லாம, ஒரு *contingent* payout-ஓட periodic-premium contract-ஆ விலையிடப்பட்டு கட்டமைக்கப்பட்டிருக்கு. ஒரு "forward-starting CDS" (ஒரு எதிர்கால தேதில மட்டும் தொடங்கும் protection) இருக்கு, ஆனா அது Swap-குடும்ப CDS-ஓட ஒரு மாறுபாடு, FX/Rates/Equity/Commodity ஒவ்வொண்ணுக்கும் இருக்கற மாதிரி தனி Forward product இல்ல — ஒரு உண்மையான asset-class-குறிப்பான விதிவிலக்கு, கவனக்குறைவு இல்ல.

**Takeaway:** இந்த section-ல ஒவ்வொரு Forward-உம் சரியா அதே வடிவத்தைப் பகிர்ந்துக்குது — இன்னிக்கு ஒத்துக்கிட்ட ஒரு நிலையான விலை, ஒரு எதிர்கால தேதில ஒரே பரிமாற்றத்துக்கு, premium இல்ல — அது ஒரு currency pair, ஒரு rate, ஒரு bond, ஒரு stock, அல்லது ஒரு barrel oil எதை reference பண்ணினாலும். Asset classes முழுக்க மாறுவது நிலையான விலை *எதுக்கு* பொருத்தப்படுது, உண்மையான asset *ஒருபோதும் deliver ஆகுமா* என்பதுதான் (FX, IR Forward Debt, Equity, Commodity Forwards எல்லாம் physically settle ஆகலாம்; FRA ஒருபோதும் ஆகாது, ஏன்னா ஒரு rate தானே deliver பண்ணக்கூடிய பொருள் இல்ல).

---

## 3. SWAP — Theory & Asset Classes முழுக்க Worked Examples

**Definition:** **Swap** = காலப்போக்குல ஒரு *தொடர்* cash flows-ஐ மாத்திக்கொள்ளும் ஒப்பந்தம், ஒவ்வொண்ணும் ஒரு formula-வால கணக்கிடப்பட்டது — பெரும்பாலும் ஒரு நிலையான rate/விலைக்கு எதிரா ஒரு floating (சந்தை-observe ஆன) ஒண்ணு, அதே அடிப்படை Notional மேல, ஒரு அட்டவணைப்படி திரும்பத் திரும்ப. கட்டமைப்பு ரீதியா, Section 1 ஏற்கனவே இதை ஒரு contract-க்குள்ள மூட்டை கட்டப்பட்ட Forwards-ஓட தொடர்னு நிறுவியது; கீழ உள்ள IRS worked example அதை நேரடியா காட்டுது.

```text
5Y CAD IRS — Bank Of Toronto pays fixed, receives floating (Module 8 §4a)
========================================================================
  Notional CAD 100,000,000 | Fixed Rate 4.00% | Floating: CORRA + spread
  Quarterly settlements, netted, for 5 years (20 total settlements)

  -> EACH quarterly settlement is its own isolated fixed-vs-floating
     comparison -- economically the SAME calculation as Section 2's
     FRA, just repeated 20 times inside one contract instead of
     negotiated as 20 separate one-off forwards. This is Section 1's
     "Swap = strip of Forwards" claim, made concrete: an IRS really
     is 20 FRAs, wrapped in a single legal document, undiscounted
     (Module 8 §4a/§4b's Q2 answer covers exactly why the discounting
     convention differs between the two).

  Full 20-settlement walkthrough: see the standalone
  Interest Rate Swap (IRS) Deep Dive.
```

### Credit — CDS, "Swap"ன்னு பெயரிட்டது ஆனா கட்டமைப்பு ரீதியா வேறுபட்டது

```text
5Y CDS ON NORTHERN RAIL CORP — Bank Of Toronto buys protection
(Module 2 §9)
========================================================================
  Notional CAD 20,000,000 | Spread 120bps | Quarterly premium

  Bank Of Toronto pays Goldman Sachs CAD 59,836 EVERY QUARTER
  (the periodic, swap-like leg) -- UNLESS Northern Rail Corp
  defaults, in which case Goldman Sachs pays Bank Of Toronto
  CAD 12,000,000 ONCE (a CONTINGENT leg, not a periodic one)

  -> ONE leg (the premium) behaves exactly like a swap's periodic
     exchange. The OTHER leg (the default payout) behaves like
     Section 4's OPTION -- it only pays if a specific contingency
     is triggered, and its maximum payout is fixed in advance. A
     CDS is documented AS a swap (hence the name), but economically
     sits on the boundary between Section's 3 Swap family and
     Section 4's Option family.
```

### FX — FX Swap (ஒரு குறுகிய கால விதிவிலக்கு), Cross-Currency Swap (உண்மையான Rates-வகைப்படுத்தப்பட்ட நீண்ட கால ஒண்ணு)

```text
FX SWAP vs. CROSS-CURRENCY SWAP — A NAMING TRAP (Module 6 §2)
========================================================================
  FX SWAP (Module 6 §4c): commonly overnight to 1 year, just a Spot
  and a Forward bolted together -- TWO principal exchanges, NO
  periodic interest payments at all. Really closer to Section 2's
  Forward family than a true multi-period Swap.

  CROSS-CURRENCY SWAP: longer-dated (years), WITH periodic interest
  payments exchanged in two currencies over the life of the trade --
  this is the one that genuinely behaves like Section 3's IRS, just
  with two different-currency legs instead of one fixed/one floating
  leg in the same currency. ISDA classifies it under INTEREST RATE,
  not FX (Module 6 §2's own explicit warning) -- precisely because
  its periodic-exchange mechanic is a Rates-family Swap, not an
  FX-family product, despite involving two currencies.
```

### Equity — Apple Inc. shares மேல Total Return Swap (Module 2 §8)

```text
EQUITY TRS ON APPLE INC. SHARES
========================================================================
  Periodic net cash flows tracking the share's price movement PLUS
  dividends, exchanged against a financing-rate leg (typically a
  floating short-term rate) -- same fixed/floating-style exchange
  shape as the IRS, just with "price + dividend return" standing in
  for "fixed rate" as one of the two legs being swapped.
```

### Commodity — Brent Crude Oil Swap (Module 7 §4c)

```text
1Y BRENT CRUDE OIL SWAP — Bank Of Toronto vs. Shell Trading
========================================================================
  Fixed Price: USD 78.00/bbl | Monthly notional: 8,333 bbl

  MONTH 1 -- ICE Brent monthly average settles at USD 81.20/bbl
    Floating leg owed to Bank Of Toronto = 8,333 x 81.20 = USD 676,640
    Fixed leg owed by Bank Of Toronto    = 8,333 x 78.00 = USD 649,974
    Net cash flow: Bank Of Toronto RECEIVES USD 26,666
    -> only the NET amount moves, repeating monthly for 12 months --
       Section 1's "strip of Forwards" pattern again, one Forward-
       style netted exchange per month
```

**Takeaway:** ஐந்துல நாலு asset classes-ஓட Swaps ஒரே fixed-vs-floating (அல்லது price-return-vs-financing) periodic netting mechanic-ஐ பின்பற்றுது — உண்மையான வித்தியாசமானது Credit-ஓட CDS: அதன் premium leg ஒரு எளிய Swap போல நடந்தாலும், அதன் contingent default leg Section 4-ஓட Option-குடும்ப payoff வடிவத்தை கடன் வாங்குது. அந்த இரட்டை அடையாளம் தான் CDS இந்த module-ஓட ரெண்டு sections முழுக்க ஏன் அசௌகரியமா உட்காருதுன்னு காரணம்; அது ஒரு model தவறு இல்ல — ஒரு risk அல்லது valuation system உண்மையிலயே வேறு models-ஆல ரெண்டு legs-ஐயும் விலையிட்டு கையாள வேண்டிய ஒரு உண்மையான கட்டமைப்பு உண்மை (premium leg-க்கு ஒரு discounted cash flow, protection leg-க்கு ஒரு contingent-claim/default-probability model).

---

## 4. OPTION — Theory & Asset Classes முழுக்க Worked Examples

**Definition:** **Option** = அதன் buyer-க்கு, ஒரு முன்கூட்டிய **premium**-க்கு ஈடா, expiry-க்குள்ள/அன்னிக்கு ஒரு நிலையான strike விலைல transact பண்ண **உரிமை, கடமை இல்ல** தருது. அதன் payoff **சமச்சீரற்றது**: buyer-ஓட அதிகபட்ச இழப்பு கொடுத்த premium-ல வரம்புக்குள்ள, சாத்திய லாபம் வரம்பற்றது (call-க்கு) அல்லது strike பூஜ்யத்துக்கு விழுறதால மட்டும் வரம்புக்குள்ளது (put-க்கு) — seller (writer) mirror-image, வரம்பற்ற-risk பக்கத்தை எடுத்துக்குது.

```text
OPTION PAYOFF — ASYMMETRIC, CAPPED ON ONE SIDE
========================================================================

  Buyer's P&L
      +  |                         /
         |                       /
         |                     /
    -----+-------------------+------------------  Market Price at expiry
         |. . . . . . . . . /  Strike Price
      -  |________________/
         (flat -- capped at premium paid, no matter how far the
          market moves against the position)
```

### FX — Module 6 §4d-ஐ முழுசா மறுபயன்படுத்தி

```text
FX OPTION — Bank Of Toronto's client hedges a future USD payable
========================================================================
  USD Call / CAD Put, Notional USD 5,000,000, Strike 1.3600
  Premium: 0.0180 CAD per USD notional = CAD 90,000

  Spot to 1.3800: exercise, net benefit CAD 10,000 (after premium)
  Spot to 1.3400: expires worthless, max loss = CAD 90,000 premium
```

### Commodity — Module 7 §4d-ஐ முழுசா மறுபயன்படுத்தி

```text
COMMODITY OPTION — Call on CME WTI Crude Oil Future
========================================================================
  Strike USD 80.00/bbl, Notional 100,000 bbl, Premium USD 3.20/bbl
  Premium Paid = USD 320,000

  WTI to USD 88.00/bbl: exercise, net benefit USD 480,000
  WTI to USD 74.00/bbl: expires worthless, max loss = USD 320,000 premium
```

### Rates — Swaption (Module 8 §4d)

```text
SWAPTION — 1Y-into-5Y Payer Swaption (an OPTION on a SWAP)
========================================================================
  Bank Of Toronto buys the RIGHT to enter a 5Y IRS, as fixed-rate
  PAYER, in 1 year -- notice this is literally "the option to enter
  Section 3's IRS," Section 1's Forward-vs-Option relationship
  applied one layer up, to an entire Swap instead of a single exchange

  Notional CAD 50,000,000, Strike 4.25%, Premium 0.60% = CAD 300,000

  Prevailing 5Y rate at expiry = 4.75%: EXERCISE -- enters the swap
  paying only 4.25% fixed when the market rate is 4.75%
  Prevailing 5Y rate at expiry = 3.90%: DON'T exercise -- a fresh
  market swap beats the 4.25% strike; loss capped at CAD 300,000 premium
```

### Equity — ஒரு OTC put, ஒரு listed ஒண்ணோடு நேரடியா ஒப்பிட்டு

```text
OTC EQUITY OPTION — the bespoke, dealer-written version of a listed
option, reusing the Hedging Deep Dive's protective put on KO
========================================================================
  Maple Leaf Pension Fund already holds 10,000 KO shares (Module 4
  §4a). It buys a PROTECTIVE PUT directly from Bank Of Toronto (its
  dealer) rather than through a listed options exchange:

  KO Put, Strike USD 65.00, Notional 10,000 shares, Expiry 1 month
  Premium USD 1.20/share = USD 12,000

  KO falls to 58.00: exercise, avoided loss = USD 70,000
  KO rises to 75.00: expires worthless, max loss = USD 12,000 premium

  -> the PAYOFF SHAPE is identical to Module 4 §4d's LISTED AAPL
     call -- the only real difference is VENUE: this trade is
     negotiated bilaterally and never touches the OCC (Module 15
     §7's "standardization is a venue property, not an asset-class
     property," restated one more time here for Options specifically)
```

> **Callout — Credit-ஓட option-போன்ற நடத்தை ஏற்கனவே CDS-க்குள்ளயே வாழ்கிறது.** இங்க ஒரு தனி standalone "Credit Option" product-ஐ வேலை செய்து காட்டுவதுக்குப் பதிலா, Section 3 ஏற்கனவே CDS-ஓட சொந்த default-contingent leg ஒரு Option-ஓட payoff-ஐ சரியா போல (எப்படியும் premium கொடுக்கப்படுது, notional-ஆல வரம்புக்குள்ள ஒரு contingent payout) நடந்துக்குதுன்னு காட்டியது. ஒரு உண்மையான standalone product உண்டு (CDS Index Swaptions — ஒரு CDS index position-ல நுழைய options), ஆனா இந்த curriculum அதுக்கு தனி worked example கட்டாத ஒரு niche instrument.

**Takeaway:** சமச்சீரற்ற வடிவம் — premium கொடுக்கப்படுது, downside வரம்புக்குள்ள, upside வரம்பற்றது, பயனற்று expire ஆகுறது அடிப்படை நிலை — underlying ஒரு currency pair, ஒரு commodity, ஒரு முழு interest rate swap, அல்லது ஒரு தனி stock எதுவா இருந்தாலும் சரியா அதே மாதிரி திரும்பி வருது. இந்தப் பட்டியல்ல Swaption தான் கருத்தியல் ரீதியா மிக முக்கியமான entry: அது Section 1-ஓட Forward → Swap, Option-on-Forward உறவு ஒரு நிலை மேல பொதுமைப்படுத்தப்படுதுன்னு நிரூபிக்குது — ஒரு Option ஒரு தனி பரிமாற்றம் மேல எழுதப்படுவது போலவே எளிதா, ஒரு Swap மேலயும் எழுதப்படலாம்.

---

## 5. முழு Matrix — Forward × Swap × Option, ஐந்து Asset Classes முழுக்க

> **Complete worked examples இருக்கு.** கீழ உள்ள matrix-ல ஒவ்வொரு cell-க்கும் [`examples/`](../examples/)-ல ஒரு முழு standalone walkthrough உண்டு: [FX Forward](../examples/FX_Forward_Complete_Example.html), [FX Option](../examples/FX_Option_Complete_USDCAD_Example.html), [FRA](../examples/FRA_Forward_Rate_Agreement_Complete_Example.html), [Interest Rate Swap](../examples/Interest_Rate_Swap_IRS_Complete_Example.html), [Cap](../examples/Interest_Rate_Cap_Complete_Example.html), [Floor](../examples/Interest_Rate_Floor_Complete_Example.html), [Collar](../examples/Interest_Rate_Collar_Complete_Example.html), [Swaption](../examples/Swaption_Complete_Example.html), [Cross-Currency Swap](../examples/Cross_Currency_Swap_Complete_Example.html), [Credit Default Swap](../examples/Credit_Default_Swap_CDS_Complete_Example.html), [Equity Total Return Swap](../examples/Equity_Total_Return_Swap_Complete_Example.html), [Commodity Swap](../examples/Commodity_Swap_Complete_Example.html).

**Definition:** Sections 2–4-ல உள்ள ஒவ்வொரு worked example-உம், ஒரே reference table-ல.

| Asset Class | Forward | Swap | Option |
|---|---|---|---|
| **Interest Rate** | FRA (§2); IR Forward Debt (§2) | Interest Rate Swap (§3) | Swaption (§4) |
| **Credit** | *தனி product இல்ல* (§2-ஓட callout) | Credit Default Swap (§3) | *CDS-ஓட protection leg-க்குள்ள பதிக்கப்பட்டது* (§4-ஓட callout) |
| **Foreign Exchange** | FX Forward (§2) | FX Swap / Cross-Currency Swap (§3, பின்னது Interest Rate-ன் கீழ வகைப்படுத்தப்பட்டது) | FX Option (§4) |
| **Equity** | Equity Forward (§2, புதுசு) | Equity Total Return Swap (§3) | Equity Option, OTC அல்லது listed (§4) |
| **Commodity** | Commodity Forward (§2, புதுசு) | Commodity Swap (§3) | Commodity Option (§4) |

**Takeaway:** grid கிட்டத்தட்ட முழுமை — Credit தான் Forward-உம் Option-உம் cells-க்கு தூய standalone entries இல்லாத ஒரே asset class, ஏன்னா ரெண்டும் CDS-ஓட சொந்த இரட்டை-தன்மை கட்டமைப்புக்குள்ள (Section 3, Section 4) திறம்பட உள்வாங்கப்பட்டுடுச்சு. மற்ற ஒவ்வொரு cell-உம் Section 1-ஓட theory-ஐ சரியா பின்பற்றுது — Forward/Swap/Option சட்டகம் வெறும் வசதியான கற்பிக்கும் சாதனம் இல்ல, OTC derivatives சந்தை asset class-க்கு asset class கட்டப்பட்டிருக்கும் உண்மையான வடிவம் என்பதற்கு இதுதான் வலுவான ஆதாரம்.

---

## 6. கட்டமைப்பு வேறுபாடு ஏன் முக்கியம் — Premium நேரமும் Counterparty Credit Exposure-உம்

**Definition:** payoff-வடிவ theory-க்கு அப்பால், Forward/Swap vs. Option, ஒரு trade-ஓட வாழ்நாள் முழுக்க உண்மையிலயே வேறுபட்ட **counterparty credit exposure சுயவிவரங்களை** உருவாக்குது — [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §2-ஓட Current Exposure vs. Potential Future Exposure (PFE) வேறுபாட்டுக்கு நேரடியா பொருத்தம்.

```text
EXPOSURE PROFILE OVER TIME — FORWARD/SWAP vs. OPTION
========================================================================

  FORWARD / SWAP (no premium at inception):
    Day 1 MTM = CAD 0 (fairly priced at trade date, Module 10 §4a)
    Current Exposure = CAD 0 on day 1 -- but PFE is NOT zero (Module
    11 §2's own IRS example: PFE estimated at roughly CAD 4,500,000,
    2 years into a 5Y CAD 100,000,000 IRS, at a 95% confidence level)
    -> RISK BUILDS UP GRADUALLY as rates/prices move, in BOTH
       directions -- either counterparty can end up owing the other

  OPTION (premium paid upfront):
    Day 1: BUYER has ALREADY PAID the premium (e.g. Section 4's
    CAD 90,000 FX Option premium) -- the buyer's maximum possible
    loss is fully known and fully realized in exposure terms from
    day one; it can only go DOWN from there (toward zero, at expiry)
    -> for the SELLER (writer), exposure runs the OPPOSITE way --
       having COLLECTED the premium, the seller's risk is entirely
       in the FUTURE, potentially large and one-directional (the
       option only gets exercised against the seller if it's
       in-the-money for the buyer)
```

**Takeaway:** அதனாலதான் ஒரு வங்கியோட credit-limit, Initial Margin கணக்கீடுகள் ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html)) ஒரே notional-ல இருந்தாலும் ஒரு Forward/Swap position-ஐயும் ஒரு Option position-ஐயும் வேறு மாதிரி நடத்துது — ஒரு Forward அல்லது Swap-ஓட risk சமச்சீர், பின்-சுமை கொண்டது (trade-ஓட வாழ்நாள் முழுக்க, எந்தத் திசையிலும் வளரும்); ஒரு Option-ஓட risk buyer-க்கு சமச்சீரற்றதும் முன்-சுமை கொண்டதும் (உடனே கொடுக்கப்பட்டு வரம்புக்குள்ளது), seller-க்கு பின்-சுமை கொண்டதும் ஒருதிசைசார்ந்ததும். ரெண்டு product குடும்பங்களுக்கும் ஒரே exposure-profile வடிவத்தை பொருத்தும் risk system, அது வெச்சிருக்கற ஒவ்வொரு option book-ஓட ஒரு பக்கத்தையும் தவறா விலையிடும்.

---

## 7. ISDA Taxonomy இணைப்பு — Transaction Type மதிப்புகளா Forward, Swap & Option

**Definition:** [ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html)-க்குத் திரும்பி வட்டத்தை முடிக்குது; அதன் §2-ஓட நாலு-level hierarchy (`Asset Class / Base Product / Sub-Product / Transaction Type`) இதே மூணு சொற்களை அதன் இறுதி level-ல கட்டுப்படுத்தப்பட்ட மதிப்புகளா பயன்படுத்துது.

```text
WHERE FORWARD / SWAP / OPTION SIT IN THE ISDA TAXONOMY
========================================================================

  Interest Rate  -> IRSwap     -> FixedFloat   -> SWAP
  Credit         -> SingleName -> Corporate    -> CREDITDEFAULTSWAP
                                                   (named "Swap," Section 3's
                                                   dual-natured exception)
  Foreign Exch.  -> Forward    -> Deliverable  -> FORWARD
  Equity         -> Swap       -> PriceReturn  -> SWAP
  Commodity      -> Energy     -> Oil          -> SWAP

  -> Notice OPTION doesn't appear as a Transaction Type value in any
     of this module's five worked ISDA-taxonomy paths above, because
     none of Sections 2-3's SWAP/FORWARD examples were Options --
     Section 4's Swaption, FX Option, Commodity Option, and Equity
     Option trades would each carry their OWN separate taxonomy
     path with Transaction Type = OPTION (or a Call/Put-specific
     variant), distinct from their underlying Forward/Swap cousins.
```

**Takeaway:** "Forward/Swap/Option வேறுபாடு ஒரு trade record-ல உண்மையில எங்க தோன்றுது"ங்கற கேள்விக்கு இதுதான் உறுதியான பதில் — அது வெறும் விளக்க மொழி இல்ல, ஒவ்வொரு OTC derivative-ஓட ISDA taxonomy path-லயும் ஒரு நேரடியான, regulator-எதிர்கொள்ளும் field மதிப்பு, இது ([ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html) §5-ஓட படி) trade-ஓட UPI reference data-வுக்கு நேரடியா உணவளிக்குது. Section 1-ஓட theory-ஐ சரியா பெறுவது அருவமான விஷயம் இல்ல — சரியா, தவறா நிரப்பப்பட்ட regulatory report-க்கு இடையிலான வித்தியாசம்.

---

## 8. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** இந்த curriculum-ஓட மற்ற reference modules போலவே, அவ்வப்போது திருத்தப்படும் technical வரையறைகளை மறுசொல்லாம, முதன்மை மூலங்களை சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **ISDA** | The **2006 ISDA Definitions** (and product-specific supplements) — the standard legal/economic definitions used to document Forwards, Swaps, and Options across every asset class in this module | [www.isda.org](https://www.isda.org) |
| **ISDA Product Taxonomy** | Where Forward/Swap/Option formally sit as Transaction Type values — see the [ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.md) | [www.isda.org](https://www.isda.org) |
| **FpML** | The XML messaging standard used to actually transmit Forward/Swap/Option trade economics between systems, structurally aligned with ISDA's taxonomy | [www.fpml.org](https://www.fpml.org) |

**Takeaway:** treat the 2006 ISDA Definitions (and its asset-class-specific supplements) as the source of truth for exactly how each product's cash flows and triggering events are legally defined; the ISDA Product Taxonomy as the source of truth for how those products get classified on a regulatory report; and FpML as the source of truth for how the same economic terms are actually represented in a trade message between two systems.

**Takeaway:** 2006 ISDA Definitions-ஐ (அதன் asset-class-குறிப்பான supplements-ஐயும்) ஒவ்வொரு product-ஓட cash flows-உம் triggering events-உம் சட்டப்படி எப்படி வரையறுக்கப்படுதுங்கறதுக்கு source of truth-ஆவும்; ISDA Product Taxonomy-ஐ அந்த products ஒரு regulatory report-ல எப்படி வகைப்படுத்தப்படுதுங்கறதுக்கு source of truth-ஆவும்; FpML-ஐ அதே பொருளாதார terms ரெண்டு systems இடையிலான trade message-ல உண்மையில எப்படி பிரதிநிதித்துவப்படுத்தப்படுதுங்கறதுக்கு source of truth-ஆவும் நடத்துங்க.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | மூன்று sections முழுக்க ஒவ்வொரு worked example-லயும் party — FX/Rates/Commodity Forwards, IRS/CDS/TRS/Commodity Swaps, ஒவ்வொரு Option-உம் |
| Goldman Sachs | FX Forward, FRA, CDS protection sale, Swaption-ல counterparty |
| Shell Trading | Brent Crude Oil Swap-ல counterparty (Section 3) |
| Maple Leaf Pension Fund | OTC put-ஆல hedge பண்ணப்பட்ட KO position-ஓட holder (Section 4) |
| Northern Rail Corp | CDS-ஓட reference entity (Section 3) |
| ISDA / FpML | இந்த மூன்று structures சட்டப்படி எப்படி வரையறுக்கப்படுது, தொழில்நுட்ப ரீதியா எப்படி transmit ஆகுதுங்கறதுக்கான standard-setters (Section 8) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ஒரு Swap ஏன் "Forwards-ஓட ஒரு தொடர்"னு விவரிக்கப்படுது, ஒரு Option ஏன் "ஒரு Forward-ல நுழையும் உரிமை"ன்னு விவரிக்கப்படுதுன்னு ஒரே வாக்கியத்துல விளக்குங்க.
2.  Section 2-ஐ பயன்படுத்தி, FRA தன் cash flow-ஐ அதன் reference period-ஓட *ஆரம்பத்துல* discount பண்ணி settle பண்ணுது, ஆனா IR Forward Debt உதாரணம் *முடிவுல* settle ஆகுது ஏன் — இது ரெண்டுக்கும் இடையிலான உண்மையான கட்டமைப்பு வேறுபாட்டை ஏன் பிரதிபலிக்கல?
3.  Section 2-ஓட callout-ஐ பயன்படுத்தி, FX, Rates, Equity, Commodity ஒவ்வொண்ணுக்கும் இருக்கற மாதிரி Credit-க்கு தனி "Forward" product ஏன் இல்ல?
4.  Section 3-ஐ பயன்படுத்தி, CDS-ஓட premium leg எந்த குறிப்பிட்ட வகையில ஒரு Swap போல நடந்துக்குது, அதன் default leg எந்த வகையில ஒரு Option போல நடந்துக்குது?
5.  Section 3-ஐ பயன்படுத்தி, ஒரு Cross-Currency Swap ரெண்டு currencies-ஐ நேரடியா பரிமாறினாலும், Foreign Exchange-க்குப் பதிலா Interest Rate-ன் கீழ ஏன் வகைப்படுத்தப்படுது?
6.  Section 4-ஐ பயன்படுத்தி, Section 1-ஓட Forward/Option உறவு எவ்வளவு தூரம் பொதுமைப்படுத்தப்படுதுன்னு Swaption என்ன நிரூபிக்குது?
7.  Section 6-ஐ பயன்படுத்தி, ஒரு Forward அல்லது Swap-ஓட counterparty credit exposure trade-ஓட வாழ்நாள் முழுக்க படிப்படியா வளருது, ஆனா ஒரு Option buyer-ஓட அதிகபட்ச exposure முதல் நாளிலேயே முழுசா தெரிஞ்சிருக்கு ஏன்?
8.  Section 7-ஐ பயன்படுத்தி, ஒரு trade-ஓட ISDA taxonomy path Transaction Type = OPTION-ல முடிஞ்சா, அது ஐந்து asset classes-ல எதுக்கு சொந்தம்னு சொல்லுமா? அதுக்குப் பதிலா அதைச் சொல்லுவது எது?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. ஒரு Swap, ஒரு பரிமாற்றத்தை ஒரு தடவை பேசுவதுக்குப் பதிலா, ஒரு அட்டவணைப்படி Forward-பாணி fixed-vs-floating பரிமாற்றத்தை திரும்பத் திரும்ப செய்யுது — உண்மையிலயே பல Forwards ஒரே contract-க்குள்ள மூட்டை கட்டப்பட்டவை. ஒரு Option, அதே வகை நிலையான-விலை பரிமாற்றத்துல நுழையும் உரிமை, கடமை இல்ல — optionality-ஐயும் premium-ஐயும் நீக்கினா, அது மறுபடி ஒரு எளிய Forward-ஆ சுருங்கிடும்.**

Section 1-ஓட கட்டமைப்பு-உறவு diagram ரெண்டு பாதிகளையும் நேரடியா சொல்லுது, Section 3-ஓட IRS உதாரணம் (20 quarterly settlements, ஒவ்வொண்ணும் தன் சொந்த தனிமைப்படுத்தப்பட்ட fixed-vs-floating ஒப்பீடு) "Forwards-ஓட தொடர்" கூற்றை உறுதியாக்குது.

**2. FRA தன் reference period-ஓட ஆரம்பத்துல settle ஆகுது, ஏன்னா அந்த நேரத்துக்குள்ள rate ஏற்கனவே தெரிஞ்சிடும், அதனால உடனே (present value-க்கு discount பண்ணி) கொடுக்கறது period முடியற வரை காத்திருக்கறதை விட இயல்பானது; IR Forward Debt முடிவுல settle ஆகுது (அதன் ஒரே settlement தேதி), ஏன்னா அதுக்கு முன்னாடி "ஏற்கனவே தெரிந்த" புள்ளி இல்ல — Forward-ஓட முழு நோக்கமே அந்த ஒரே எதிர்கால தேதி வரை எதுவும் தெரியாது, பரிமாறப்படாது என்பதுதான். இது Forward வகைகளுக்கு இடையிலான கட்டமைப்பு வேறுபாடு இல்ல — ஒரு *rate-ஐ reference பண்ணும்* forward (FRA) எப்படி ஆவணப்படுத்தப்பட்டிருக்குன்னதுக்கும், முதல்லயே ஒரே ஒரு settlement தேதி மட்டும் உள்ள *விலை-reference பண்ணும்* forward (IR Forward Debt)-க்கும் உள்ள நேரம் சார்ந்த வழக்கம் மட்டும்.**

Section 2-ஓட FRA, IR Forward Debt worked examples ரெண்டும் தங்கள் settlement நேரத்தை வெளிப்படையா சொல்லுது, Module 8-ஓட சொந்த Q2 பதில் (Section 3-ல cross-reference) discounting வழக்கங்கள் ஆழமான பொருளாதார வேறுபாட்டைப் பிரதிபலிக்காம வேறுபடுதுன்னு அதே புள்ளியைச் சொல்லுது.

**3. ஏன்னா Credit-ஓட முக்கிய OTC product (CDS) ஏற்கனவே ஒரு one-off நிலையான-விலை பரிமாற்றமா இல்லாம, ஒரு periodic-premium, contingent-payout contract-ஆ கட்டமைக்கப்பட்டிருக்கு — ஒரு currency pair, ஒரு rate, ஒரு bond விலை, ஒரு commodity விலை ஒவ்வொண்ணுக்கும் இருக்கற மாதிரி ஒரு இயற்கையான "Credit Forward" இல்ல. ஒரு forward-starting CDS இருக்கு, ஆனா அது Swap-குடும்ப CDS-ஓட ஒரு மாறுபாடு, தனி Forward product இல்ல.**

Section 2-ஓட callout இதை curriculum-ல ஒரு கவனக்குறைவு இல்லாம, ஒரு உண்மையான asset-class-குறிப்பான விதிவிலக்கா நேரடியா சொல்லுது.

**4. Premium leg ஒரு default நடக்குதோ இல்லையோங்கறதைச் சாராம, அவ்வப்போது (காலாண்டுக்கு, ஒவ்வொரு தடவையும் CAD 59,836) கொடுக்கப்படுது — வேறு எந்த Swap-ஓட periodic leg-ஓட அதே திரும்பத் திரும்ப, formula-இயக்கப்படற பரிமாற்ற வடிவம். Default leg ஒரே ஒரு தடவை, ஒரு குறிப்பிட்ட contingency (default) தூண்டப்பட்டா மட்டும் payout பண்ணுது, payout notional-ஆலும் recovery rate-ஆலும் வரம்புக்குள்ள — சரியா Section 4-ஓட Option payoff வடிவம் (எப்படியும் premium கொடுக்கப்படுது, ஒரு contingent, வரம்புக்குள்ள payout).**

Section 3-ஓட CDS worked example-உம் takeaway-உம் இந்த இரட்டைத்தன்மையை வெளிப்படையா சொல்லுது, CDS பெயரால ஒரு Swap-ஆ ஆவணப்படுத்தப்பட்டிருந்தாலும் அதன் ரெண்டு legs-ல ஒண்ணுல ஒரு Option போல ஏன் நடந்துக்குதுன்னு விளக்கி.

**5. ஏன்னா அதை முதல்லயே ஒரு "Swap" ஆக்குவது — ஒரு பல வருஷ காலத்துக்கு வட்டியோட periodic பரிமாற்றம் — IRS பயன்படுத்தும் அதே Interest-Rate-குடும்ப mechanic, ஒரு currency-க்குப் பதிலா இரண்டுல ஓடுது. FX-ஓட சொந்த products (Spot, Forward, குறுகிய கால FX Swap) periodic வட்டி payments-ஏ கொண்டிருக்கறதில்ல — principal பரிமாற்றங்கள் மட்டும் — அதனால Cross-Currency Swap-ஓட வரையறுக்கும் அம்சம் currencies தெளிவா சம்பந்தப்பட்டிருந்தாலும் கட்டமைப்பு ரீதியா FX-க்கு இல்ல, Rates-க்கு சொந்தம்.**

Section 3-ஓட "naming trap" callout இதை நேரடியா சொல்லுது, ரெண்டு products-ஓட வேறுபட்ட வகைப்பாடுகள் பத்தின Module 6 §2-ஓட சொந்த வெளிப்படையான எச்சரிக்கையை cross-reference பண்ணி.

**6. Forward-இலிருந்து-Option உறவு ஒரு ஒற்றை பரிமாற்றத்துக்கு மட்டும் வரம்புக்குள்ள இல்லைன்னு நிரூபிக்குது — ஒரு Option ஒரு முழு Swap மேலயும் (ஒரு பல-period contract) ஒரு Forward-பாணி பரிமாற்றம் மேல எழுதப்படுவது போலவே எளிதா எழுதப்படலாம். Swaption சரியா "Section 3-ஓட IRS-ல நுழையும் உரிமை", ஒரு ஒற்றை எதிர்கால பரிமாற்றம் மேல ஒரு plain vanilla Option-ஐ விட ஒரு அடுக்கு abstraction மேல.**

Section 4-ஓட Swaption worked example இதை வெளிப்படையா சொல்லுது, "Section 1-ஓட Forward-vs-Option உறவு, ஒரு ஒற்றை பரிமாற்றத்துக்குப் பதிலா ஒரு முழு Swap-க்கு, ஒரு அடுக்கு மேல பொருத்தப்பட்டது"ன்னு அழைச்சு.

**7. ஒரு Forward அல்லது Swap பூஜ்யம் MTM-ஓட தொடங்குது (inception-ல நியாயமா விலையிடப்பட்டது), ஆனா எந்தத் திசையிலும் அதிகபட்ச செலவை பூட்டும் premium இல்ல — trade-ஓட வாழ்நாள் முழுக்க rates/விலைகள் நகரும்போது, எந்த counterparty-ஓட exposure-உம் எந்தத் திசையிலும் வளரலாம், சரியா Potential Future Exposure மதிப்பிடுவது அதைத்தான். ஒரு Option buyer, இதற்கு மாறா, முதல் நாளே முழு premium-ஐ முன்கூட்டியே கொடுக்குது — அதுதான் buyer-ஓட அதிகபட்ச சாத்திய இழப்பு, முழுசா realize ஆகி உடனே தெரிஞ்சிருக்கு, expiry நெருங்க நெருங்க பூஜ்யத்தை நோக்கி மட்டுமே சுருங்கலாம்.**

Section 6-ஓட exposure-profile diagram இந்த ரெண்டு பாதிகளையும் நேரடியா சொல்லுது, Forward/Swap பக்கத்துக்கு Module 11 §2-ஓட சொந்த PFE உதாரணத்தை மறுபயன்படுத்தி.

**8. இல்ல — Transaction Type = OPTION மட்டும் asset class-ஐ சொல்லாது, ஏன்னா ஐந்து asset classes ஒவ்வொண்ணுக்கும் தன் சொந்த Option மாறுபாடு உண்டு (FX Option, Commodity Option, Swaption, Equity Option; Credit-ஓடது தனி standalone இல்லாம CDS-க்குள்ள பதிக்கப்பட்டது). அதைச் சொல்லுவது taxonomy path-ஓட முதல் level, Asset Class, Base Product, Sub-Product-உடன் சேர்த்து படிக்கப்படுவது — ISDA Product Taxonomy Deep Dive-ஓட Section 2 ஏற்கனவே நாலு-level hierarchy-ஓட எந்த ஒரு level-உம் தனியா போதாதுன்னு சொன்ன அதே புள்ளி.**

Section 7-ஓட diagram, takeaway இதை நேரடியா சொல்லுது, Transaction Type தனியா asset classes முழுக்க தெளிவில்லாதது என்ற ISDA Product Taxonomy Deep Dive-ஓட புள்ளியை cross-reference பண்ணி.
