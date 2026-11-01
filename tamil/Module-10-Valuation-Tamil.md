# Module 10 — Valuation (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Cross-cutting module** — ஆறு Asset Class Roadmap modules (4–9) + [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) (Listed Derivatives) எல்லாத்தையும் சார்ந்து கட்டப்பட்டது. எட்டு-பாக asset-class pattern-ஐ சுத்தி அமைக்கப்படலை, ஏன்னா Valuation ஒரு asset class இல்ல — ஒவ்வொரு asset class-ஓட Position/Valuation-P&L lifecycle stage-உம் ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html), ஒவ்வொரு asset-class module-ஓட Section 5) உண்மையில ஓடற mechanism இதுதான்.
> **மூலம்:** English Module 10-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Valuation-ன்னா என்ன? — Price, Market Value, Fair Value, MTM, PV & Exposure

**Definition:** சாதாரணப் பேச்சுல ஒண்ணுக்கொண்ணு மாத்தி பயன்படுத்தற மாதிரி தோணற ஆறு சொற்கள், ஒரு trade record-ல உண்மையிலயே ஆறு வேற வேற அர்த்தங்களை குறிக்குது; இவற்றைக் குழப்பிக்கறதுதான் ஒரு reporting system பண்ற மிகப் பொதுவான valuation தவறு.

| Term | அர்த்தம் |
|---|---|
| Price | ஒரு instrument-க்கு ஒரு unit-க்கான quote — ஒரு Coca-Cola share-க்கு CAD 68.40 ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4a), ஒரு Canada bond-க்கு face 100-க்கு 98.50 ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §4a). ஒரு புள்ளியில நிலையானது, இன்னும் quantity-ஆல பெருக்கப்படலை |
| Market Value | Price × Quantity — இன்னிக்கு விலையில ஒரு position-ஓட மொத்த மதிப்பு. Coca-Cola-க்கு: 10,000 shares × தற்போதைய விலை |
| Fair Value | Market Value-வின் accounting-standard கருத்து, ஒரு குறிப்பிட்ட அளவீட்டு hierarchy-ஓட (Section 6) முறைப்படுத்தப்பட்டது — financial statements-ல உண்மையில வந்து சேரும் எண் |
| MTM (Mark-to-Market) | ஒரு position-ஐ தற்போதைய Market/Fair Value-க்கு மறுமதிப்பீடு செய்யற *செயல்*, அதனால cost அல்லது முந்தைய valuation தேதியோட ஒப்பிட்ட Unrealized P&L ([Module 4](../docs/1-capital-markets/module-04-equity.html) §5-ஓட Coca-Cola உதாரணம், [Module 7](../docs/1-capital-markets/module-07-commodities.html) §5-ஓட தினசரி futures margin) |
| PV (Present Value) | எதிர்காலத்துல உண்மையில நடக்கப்போற ஒரு cash flow (அல்லது cash flows stream)-ஓட *இன்னிக்கான* மதிப்பு, பொருத்தமான rate-ல discount பண்ணி — ஒவ்வொரு derivative-ஓட valuation-க்கும் பின்னால இருக்கற mechanism (Section 4) |
| Exposure | விஷயங்கள் தப்பா போனா எவ்வளவு இழக்கலாம் — Market Value *இல்ல*. CAD 100,000,000 notional IRS ([Module 8](../docs/1-capital-markets/module-08-rates.html) §4a) inception-ல Market Value கிட்டத்தட்ட பூஜ்யமா இருக்கலாம், ஆனா உண்மையான, நேர்மறை Potential Future Exposure ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)) இருக்கும் |

```text
WHY THESE SIX AREN'T SYNONYMS — one trade record, six different numbers
========================================================================

  Canada 5% Bond 2035 (Module 5 §4a), one week after purchase:

  Price          : 98.65                (today's per-100 quote)
  Market Value    : CAD 9,865,000        (98.65/100 x 10,000,000 face)
  Fair Value      : CAD 9,865,000        (same number, IFRS 13/ASC 820
                     framing -- Section 06 explains WHY it's the same
                     number here but often ISN'T for a derivative)
  MTM / Unrealized P&L : +CAD 15,000    (Market Value today minus
                     Market Value at purchase -- Module 5 §5's exact
                     worked example)
  PV of remaining cash flows : NOT the same as Market Value for a
                     bond quoted on a clean-price convention -- see
                     Section 03's YTM/Duration deep dive
  Exposure        : depends entirely on WHO holds the risk and
                     against WHOM -- covered fully in Module 11
```

> **Deep dive இருக்கு.** "Price × Quantity" இந்த table தெளிவா சொல்லாத ரெண்டாவது மாறியை மறைக்குது: **Quantity தான் Position** — அதே market Price ரெண்டு மடங்கு position மேல பொருந்தினா, சரியா ரெண்டு மடங்கு Market Value. Position உண்மையில என்ன, ஒவ்வொரு asset class-க்கும் எப்படி கட்டப்படுது, ஒவ்வொரு valuation எண்ணையும் dollar எண்ணா மாத்தற பெருக்கி Position size ஏன்னு முழுசா: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html).

**Takeaway:** Modules 4–9, 15 முழுக்க ஒவ்வொரு worked example-உம் இந்த ஆறு சொற்கள்ல குறைந்தது ரெண்டு மூணை, முறையா பிரிக்காமலே ஏற்கனவே பயன்படுத்திடுச்சு. இந்த module-ஓட முழு வேலை அந்தப் பிரிவை வெளிப்படையாக்குவது, அப்புறம் முந்தைய ஒவ்வொரு module-உம் "[Module 10](../docs/1-capital-markets/module-10-valuation.html)"க்கு ஒத்தி வெச்ச உண்மையான கணக்கை — Fixed Income-க்கு YTM/Duration (Section 3), derivatives-க்கு DCF/NPV, option pricing (Section 4) — காட்டுவது.

---

## 2. இரு Valuation குடும்பங்கள் — Quote-Based vs Model-Based

**Definition:** இந்த roadmap-ல ஒவ்வொரு instrument-உம் சரியா ரெண்டு valuation குடும்பங்கள்ல ஒண்ணுல வரும். **Quote-Based** instruments-க்கு ஒரு screen-ல நேரடியா படிக்கக்கூடிய observable சந்தை விலை உண்டு. **Model-Based** instruments-க்கு, அந்த குறிப்பிட்ட contract-க்கு ஒற்றை observable விலை இல்லாததால், inputs-ல இருந்து ஒரு மதிப்பை கணக்கிட வேண்டியிருக்கும்.

```text
QUOTE-BASED  vs  MODEL-BASED
========================================================================

  QUOTE-BASED (read the price, multiply by quantity):
    Coca-Cola stock (Module 4 §4a)     -> NYSE prints a price every trade
    Canada 5% Bond 2035 (Module 5 §4a) -> dealers quote a Clean Price
    CME WTI Future (Module 7 §4b)      -> exchange publishes a daily
                                            settlement price
    E-mini S&P 500 Future (Module 15 §4a) -> same mechanic

  MODEL-BASED (calculate a value from inputs, no single quote exists):
    5Y CAD IRS (Module 8 §4a)          -> no exchange prints "the"
                                            price of THIS specific swap;
                                            value comes from discounting
                                            its cash flows (Section 04a)
    AAPL Call Option (Module 4 §4d)    -> value comes from an option
                                            pricing model (Section 04b)
    CDS on Northstar Resources
    (Module 9 §4a)                     -> value comes from the credit
                                            spread curve (Section 04c)

  -> The dividing line isn't "exchange-traded vs OTC" (Module 6 §7's
     axis) -- Listed Options ARE exchange-traded (Module 15 §7) but
     still need a MODEL to price, because a specific strike/expiry
     combination may not trade often enough to have its own live quote.
```

**Takeaway:** ஒரு குறிப்பிட்ட instrument-ஐ சரியா value பண்றது எவ்வளவு கடினம்ங்கறதை கணிக்க இது மிகப் பயனுள்ள ஒற்றை lens — Quote-Based instruments ஒரு lookup; Model-Based instruments ஒரு கணக்கீடு, அந்த கணக்கீட்டுல ஒவ்வொரு input-உம், அதே trade-ஐ value பண்ற ரெண்டு தரப்புகளுக்கு (அல்லது ரெண்டு systems-க்கு) இடையில கருத்து வேறுபாட்டுக்கான சாத்திய மூலம்.

---

## 3. Fixed Income Deep Dive — YTM, Duration/DV01 முழுசா

[Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1 Yield to Maturity, Duration-ஐ முன்னோட்டமா அறிமுகப்படுத்தி, முழு கணக்கை இங்க தருவதா வாக்கு கொடுத்தது. இந்த section ரெண்டையும் வழங்குது, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §4a-ஓட அதே Canada 5% Bond 2035 position-ஐ பயன்படுத்தி.

### Yield to Maturity — தோராய formula

```text
YIELD TO MATURITY (approximate) — Canada 5% Bond 2035
========================================================================

  Face Value (F): CAD 10,000,000
  Clean Price Value (P): CAD 9,850,000 (Price 98.50 — Module 5 §4a)
  Annual Coupon Cash Flow (C): 10,000,000 x 5% = CAD 500,000
  Years to Maturity (n): 9 (2035 - 2026)

  Approx. YTM = [ C + (F - P)/n ] / [ (F + P)/2 ]
              = [ 500,000 + (10,000,000 - 9,850,000)/9 ] / [ (10,000,000 + 9,850,000)/2 ]
              = [ 500,000 + 16,666.67 ] / 9,925,000
              = 516,666.67 / 9,925,000
              = 5.21%

  -> YTM (5.21%) > Coupon Rate (5.00%), exactly the discount-bond
     rule Module 1 §1 established: paying below par means the buyer's
     actual return exceeds the printed coupon rate.
```

### Modified Duration & DV01

**Definition:** [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1 Modified Duration-ஐ "yield 1% மாறினா bond விலை எவ்வளவு நகரும்"ன்னு முன்னோட்டம் காட்டியது. இந்த bond-ஓட உண்மையான 9-வருஷ, 5% semi-annual cash flow schedule-ஐ 5.21% yield-ல கொண்டா, அதன் Modified Duration தோராயமா **7.2 வருஷம்** — முழு Macaulay-Duration-ல இருந்து Modified-Duration வரையிலான derivation ஒரு நிலையான fixed income கணக்கீடு, இங்க படிப்படியா மறுஉருவாக்கப்படலை; ஆனா அந்த எண்ணின் *விளைவு* தான் ஒரு IT/reporting system சரியா பெற வேண்டியது:

```text
DV01 (Dollar Value of 1 Basis Point) — Canada 5% Bond 2035
========================================================================

  Modified Duration: 7.2
  Market Value: CAD 9,850,000

  DV01 = Modified Duration x Market Value x 0.0001
       = 7.2 x 9,850,000 x 0.0001
       = CAD 7,092 per basis point

  SCENARIO — yields rise 25 basis points (0.25%):
    Approx. Price Impact = -Modified Duration x ΔYield x Market Value
                          = -7.2 x 0.0025 x 9,850,000
                          = -CAD 177,300

  -> This is the SAME DV01 concept Module 8 §1 previewed for Rates
     products, applied here to the instrument it was originally
     defined for: bond price sensitivity. A 25bp rate move costs
     Bank Of Toronto's bond position roughly CAD 177,300 -- a real,
     quantifiable number a risk report needs, not just a directional
     "rates went up, bonds went down" statement.
```

**Takeaway:** YTM "maturity வரை வெச்சிருந்தா நான் உண்மையில எவ்வளவு சம்பாதிக்கிறேன்"ங்கறதுக்கு பதில்; Duration/DV01 "yields மாறினா இந்த position-ஓட *மதிப்பு* எவ்வளவு நகரும்"ங்கறதுக்கு பதில். ரெண்டுமே [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1 ஏற்கனவே bond-ஓட core fields-ஆ நிறுவின அதே மூணு inputs-ல (Price, Coupon, Maturity) இருந்து முழுசா derive ஆகுது — புதுசா எதுவும் தேவையில்ல, அந்த fields எப்பவும் நோக்கிக் கட்டிக்கிட்டிருந்த கணக்கு மட்டும்.

### கூடுதல் உதாரணம் — DV01-ஐ பயன்படுத்தி ஒரு நாள் risk report

Bank Of Toronto-க்கிட்ட மூணு bond positions இருக்குன்னு வெச்சுக்கோங்க:

| Bond | Market Value (CAD) | Mod. Duration | DV01 (CAD/bp) = MV × Dur × 0.0001 |
|---|---|---|---|
| Canada 5% 2035 | 9,850,000 | 7.2 | 7,092 |
| Bank Of Toronto 4.5% 2030 | 12,210,000 | 3.4 | 4,151 |
| City of Toronto 3.75% 2032 | 14,587,500 | 4.9 | 7,148 |
| **மொத்தம்** | | | **18,391** |

மொத்த DV01 = CAD 18,391 per bp. Yields 25 bp உயர்ந்தா: 18,391 × 25 ≈ **CAD 459,775 இழப்பு** (தோராயமா). இதுதான் risk report-ல desk-ஓட "rate risk" எண்; Position size மாறினா DV01-உம் அதே விகிதத்துல மாறும் ([Module 25](../docs/1-capital-markets/module-25-position-deep-dive.html)).

> **Deep dive இருக்கு.** Basis point அலகு தானே — capital markets ஏன் percent-க்குப் பதிலா bps பயன்படுத்துது, அதே DV01 mechanism Rates (Swap DV01), Credit (Spread DV01)-ல எப்படி மறுபடி பொருந்துது, Equity, FX-ல bps எப்படி தோன்றுது (அல்லது தோன்றாது), ஒவ்வொரு வகை bps எண்ணும் உண்மையில எவ்வளவு அடிக்கடி மாறுது — தனி [Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) பாருங்க.

---

## 4. Derivative Valuation Deep Dive — DCF, Option Pricing & Credit Spreads

மூணு worked examples — Section 2-ல இருந்த ஒவ்வொரு Model-Based குடும்பத்துக்கும் ஒண்ணு — ஒவ்வொண்ணும் இந்த roadmap-ல முன்னாடியே அறிமுகப்படுத்தின trade-ஐ, inception-ல இல்லாம, வாழ்க்கையின் நடுவில காட்டுது.

### 4a. Interest Rate Swap — DCF/NPV Mark-to-Market

```text
5Y CAD IRS  —  Mark-to-Market, 6 Months After Inception
========================================================================

  At inception (Module 8 §4a): Bank Of Toronto pays FIXED 4.00%,
  Notional CAD 100,000,000. A fairly-priced swap has NPV = 0 at
  inception -- the fixed rate is SET so that the PV of the fixed
  leg exactly equals the PV of the expected floating leg.

  SIX MONTHS LATER: the market's quoted rate for a new 4.5-year CAD
  swap has moved from 4.00% to 4.30% -- rates rose.

  Bank Of Toronto is locked into paying 4.00% fixed, now BELOW the
  4.30% market rate for the same remaining term -- a good deal, so
  the swap now has POSITIVE value to Bank Of Toronto.

  Approx. MTM Value (using the swap's own DV01, the same concept as
  Section 03's bond DV01, just computed off the swap's cash flow
  schedule instead of a bond's):
    Swap DV01 (approx, CAD 100,000,000 notional, 4.5Y remaining):
      ≈ CAD 41,000 per basis point
    Rate moved 30 basis points (4.00% -> 4.30%)
    Approx. MTM Gain = 41,000 x 30 = CAD 1,230,000 (to Bank Of Toronto)

  -> A full DCF valuation discounts EVERY remaining fixed and
     floating cash flow off the current CORRA curve individually --
     the DV01 shortcut shown here is the same simplification real
     risk systems use for a quick sensitivity estimate between full
     revaluations, not a replacement for the full curve-based DCF.
```

### 4b. Equity Option — Intrinsic Value vs Time Value

```text
AAPL 15-JAN-2027 250 CALL  —  Six Months Before Expiry
========================================================================

  At inception (Module 4 §4d): Strike USD 250, Premium USD 18.50/share
  100 contracts x 100 shares/contract = 10,000 shares equivalent

  SIX MONTHS LATER: AAPL trades at USD 260, option premium now
  quoted at USD 22.00/share

  Intrinsic Value = max(Spot - Strike, 0) x Shares
                   = max(260 - 250, 0) x 10,000
                   = 10 x 10,000 = USD 100,000

  Market Value (current premium) = 22.00 x 10,000 = USD 220,000

  Time Value = Market Value - Intrinsic Value
             = 220,000 - 100,000 = USD 120,000

  -> Time Value exists because of everything an option pricing
     model (the industry standard is a Black-Scholes-family model)
     actually prices in beyond today's spot-vs-strike gap: time
     remaining to expiry, volatility, the risk-free rate, and
     dividends -- the five/six inputs an option pricing model needs
     that Section 02's "Quote-Based" instruments never require at all.
```

**IT/reporting பார்வை:** Intrinsic-Value/Time-Value பிரிவு இல்லாம ஒரு option-ஓட தற்போதைய Market Value-வை மட்டும் காட்டும் trade record, அந்த மதிப்பு சரியா தெரியுதான்னு சொல்லும் மிகப் பயனுள்ள ஒற்றை diagnostic-ஐ மறைக்குது — Intrinsic Value ஒரு தூய arithmetic சோதனை (spot vs strike, model தேவையில்ல); அது Market Value-க்கு கீழ ஒரு தளத்தோட ஒத்துப்போகும், இல்லன்னா பழைய/தவறான price feed-ஐ சமிக்ஞை பண்ணும்.

### 4c. Credit Default Swap — Spread-ஆல் இயக்கப்படற Mark-to-Market

```text
5Y CDS ON NORTHSTAR RESOURCES INC.  —  Bank Of Toronto (protection
buyer) vs Goldman Sachs (protection seller) — One Year After Inception
========================================================================

  At inception (Module 9 §4a): Notional USD 10,000,000, Running
  Coupon 100bps, market spread was 250bps, Bank Of Toronto (protection
  buyer) paid Goldman Sachs (protection seller) an upfront of approx.
  USD 675,000
    = (2.50% - 1.00%) x ~4.5 x 10,000,000

  ONE YEAR LATER: Northstar Resources Inc.'s credit has deteriorated
  further (still no formal Credit Event, Module 9 §4b) -- market
  spread has WIDENED to 400bps. Remaining Risky Duration ≈ 4.0
  (one year of the original 5 has passed)

  Current Approx. Value = (Market Spread - Running Coupon) x Risky
                            Duration x Notional
                          = (4.00% - 1.00%) x 4.0 x 10,000,000
                          = 3.00% x 4.0 x 10,000,000
                          = USD 1,200,000

  Unrealized Gain to Bank Of Toronto = 1,200,000 - 675,000 = USD 525,000

  -> Bank Of Toronto's PROTECTION position gains value as Northstar
     Resources Inc.'s credit WORSENS (spread widens) -- the mirror
     image of a bond holder, who LOSES value as the same company's
     credit worsens. This is exactly why CDS makes an effective
     hedge against a bond position referencing the same Reference
     Entity (Module 9 §1) -- and exactly the "wrong-way risk"
     concept flagged in Module 9's Q4 answer, from the opposite
     side of the trade.
```

> **Callout — பல snapshots-ல ஒண்ணு.** இது ஒரு ஒற்றை விளக்க valuation snapshot: Northstar Resources Inc.-ஓட spread 250bps-ல இருந்து 400bps-க்கு விரிவடையுது. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §3 இதே trade-ஐ ஒரு *வேறு* விளக்க scenario-ல மறுமதிப்பீடு செய்யுது — அதற்குப் பதிலா spread *குறுகினா*, Bank Of Toronto-ஓட lock பண்ணின protection தற்போதைய சந்தை விலையை விட குறைவான மதிப்புள்ளதா மாறும், லாபமில்ல, ஒரு unrealized இழப்பு. ரெண்டு sections-உம் ஒரே தருணத்தை விவரிக்கலை; ஒரே trade-ல பொருத்தப்பட்ட "spread இப்படி நகர்ந்தா என்ன" என்ற ரெண்டு சுயாதீன scenarios, ஒவ்வொண்ணும் அதன் சொந்த விதிமுறைகள்ல சுய-நிலைத்தன்மையோட.

**Takeaway:** மூணு derivative valuations-உம் ஒரே அடிப்படை யோசனையா குறுகுது — trade date-ல lock பண்ணினதை, அதே மீதி risk-க்கு சந்தை *இன்னிக்கு* வழங்கறதோட ஒப்பிடு, அப்புறம் அந்த இடைவெளியை ஒரு sensitivity அளவு (Swap DV01, Option Greeks, CDS Spread DV01) பயன்படுத்தி dollar மதிப்பா மாத்து — ஒவ்வொரு தடவையும் அதே கருத்து, வேற அடிப்படை risk factor-க்கு calibrate ஆனது.

---

## 5. Cross-Asset-Class Valuation Reference

எல்லா asset class-உம் Listed Derivatives-உம், பக்கம் பக்கமா — இந்த module-ஓட அசல் curriculum outline-ல இருந்த "Compare valuation across trade types" table-ஓட முழுசா வேலை செய்து காட்டிய பதிப்பு.

| Cash / Derivative | Asset Class / Product | Valuation Family (Section 02) | Core Valuation Basis | Worked Example |
|---|---|---|---|---|
| CASH | **Equity** (Common/Preferred) | Quote-Based | Price × Quantity | [Module 4](../docs/1-capital-markets/module-04-equity.md) §5 — Coca-Cola daily MTM |
| CASH | **Fixed Income** (Bonds) | Quote-Based (Clean Price) + a PV concept underneath (YTM) | Clean Price × Face Value ÷ 100, plus Accrued Interest for Dirty Price | Section 03, this module |
| CASH + DERIVATIVE | **FX Spot/Forward/Swap** | Quote-Based (Spot) / Model-Based (Forward points derived from rate differential) | Notional × Rate; Forward = Spot + Forward Points ([Module 6](../docs/1-capital-markets/module-06-fx.md) §1) | [Module 6](../docs/1-capital-markets/module-06-fx.md) §5 — daily spot MTM |
| DERIVATIVE | **FX Option** | Model-Based | Option pricing model (same family as Section 04b) | [Module 6](../docs/1-capital-markets/module-06-fx.md) §4d |
| CASH + DERIVATIVE | **Commodity Physical/Spot/Forward** | Quote-Based (Spot) / Model-Based (Forward, via storage/carry cost) | Spot × Quantity; Forward = Spot + Carry ([Module 7](../docs/1-capital-markets/module-07-commodities.md) §1) | [Module 7](../docs/1-capital-markets/module-07-commodities.md) §4a |
| DERIVATIVE | **Commodity Future** | Quote-Based | Exchange daily settlement price × contract size | [Module 7](../docs/1-capital-markets/module-07-commodities.md) §5 — daily variation margin |
| DERIVATIVE | **Commodity Swap/Option** | Model-Based | DCF (Swap) / pricing model (Option) | [Module 7](../docs/1-capital-markets/module-07-commodities.md) §4c, §4d |
| DERIVATIVE | **Interest Rate Swap / FRA / Cap-Floor / Swaption** | Model-Based | DCF/NPV off the CORRA curve; Cap/Floor and Swaption additionally need an option pricing model layered on top | Section 04a, this module |
| DERIVATIVE | **IR Forward Debt** | Model-Based (until/unless it delivers a real bond, then Quote-Based) | Forward bond price = Spot – Carry ([Module 8](../docs/1-capital-markets/module-08-rates.md) §4e) | [Module 8](../docs/1-capital-markets/module-08-rates.md) §4e |
| DERIVATIVE | **Credit Default Swap** | Model-Based | Credit spread curve × Risky Duration × Notional | Section 04c, this module |
| DERIVATIVE | **Futures (Listed Derivatives)** | Quote-Based | Exchange daily settlement price × contract size | [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.md) §4a-c |
| DERIVATIVE | **Listed Options** | Model-Based (despite being exchange-traded — Section 02's key exception) | Option pricing model, same family as Section 04b | [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.md) §4d |

**Takeaway:** Valuation Family (Quote-Based vs Model-Based) ஒரு product-ஐ சரியா value பண்றது எவ்வளவு கடினம்ங்கறதைப் பத்தி கிட்டத்தட்ட எல்லாத்தையும் கணிக்குது — ஒவ்வொரு Model-Based வரிக்கும் ஒரு *input*-ஆ ஒரு curve, ஒரு spread, அல்லது ஒரு pricing model தேவை; அதாவது ஒவ்வொரு Model-Based valuation-உம் அந்த inputs-ஓட தரத்தளவுக்குத்தான் நல்லது — இதே கவலையை முறைப்படுத்தத்தான் Section 6-ஓட Fair Value Hierarchy இருக்கு.

---

## 6. Fair Value Hierarchy — IFRS 13 / ASC 820 Levels 1, 2 & 3

**Definition:** Accounting standards (சர்வதேச அளவுல IFRS 13, US-ல ASC 820 — இந்த roadmap-ல முதல் தடவையா markets regulator-க்குப் பதிலா *accounting* standard தேவைப்பட்டிருக்கு) ஒவ்வொரு Fair Value அளவீட்டையும் மூணு Levels-ல ஒண்ணா வகைப்படுத்துது — instrument எந்த asset class-ஐச் சேர்ந்ததுங்கறதை வெச்சு இல்ல, அதன் inputs எவ்வளவு observable-ஆ இருக்குங்கறதை முழுக்க வெச்சு.

```text
LEVEL 1, 2, 3 — RANKED BY INPUT OBSERVABILITY, NOT ASSET CLASS
========================================================================

  LEVEL 1 — quoted price, identical instrument, active market:
    Coca-Cola stock (Module 4 §4a), CME WTI Future settlement price
    (Module 7 §5), E-mini S&P 500 Future (Module 15 §4a)
    -> the cleanest case: read the price, no judgment involved

  LEVEL 2 — observable inputs, but not a direct quote for THIS
  exact instrument:
    5Y CAD IRS (Module 8 §4a) -- no one quotes "Bank Of Toronto's
      exact swap," but the CORRA curve feeding its DCF is fully
      observable
    Canada 5% Bond 2035, if it trades infrequently -- valued off
      comparable, more liquid bonds' observable yields
    A liquid, standardized single-name CDS (Module 9 §2) -- market
      spread is observable even though no exchange quotes it directly

  LEVEL 3 — unobservable inputs, model assumptions, genuine
  judgment required:
    CDS on a THINLY-TRADED reference entity, where the "market
      spread" input itself has to be estimated, not observed
      (Module 9 §4a's Northstar Resources Inc. trade, if Northstar
      bonds/CDS rarely trade)
    A bespoke FX/IR Exotic (Module 6 §4c's naming-trap Cross-
      Currency Swap, or an exotic barrier option) with no comparable
      liquid market at all

  -> Notice EQUITY, COMMODITY, and RATES products all show up in
     BOTH Level 1 and Level 2 depending on liquidity, not asset
     class -- the same instrument TYPE can sit at different Levels
     for two different specific trades.
```

**Takeaway:** Fair Value Hierarchy என்பது Section 2-ஓட Quote-Based/Model-Based பிரிவை இன்னும் ஒரு படி நுணுக்கமாக்கியது — Quote-Based instruments எப்பவும் Level 1; Model-Based instruments Level 2 (observable model inputs) அல்லது Level 3 (unobservable ones)-ஆ பிரியுது; அந்த Level 2/3 பிளவுதான் ஒரு valuation-க்கு எவ்வளவு சுயாதீன ஆய்வு தேவைங்கறதை சரியா தீர்மானிக்குது (Section 7).

---

## 7. IT/Reporting பார்வை — எந்த Field எது, யார் சரிபார்ப்பது

**Definition:** ஒரு trade record-ல ஒத்ததாத் தெரியற மூணு fields மூணு வேற கேள்விகளுக்கு பதில் சொல்லுது; ஒரு Model-Based (Section 2) position-க்கு நான்காவது ஒரு முழு process தேவை: எண் சரியா இருக்கான்னு உறுதிப்படுத்த ஒரு சுயாதீன நபர் அதை மறுபடி derive பண்றது.

```text
THREE FIELDS, THREE QUESTIONS
========================================================================

  "Price" / "Rate" field   ->  What does the market say THIS
                                 instrument is worth, per unit?

  "Market Value" field     ->  What is my TOTAL position worth,
                                 right now?

  "Unrealized P&L" field   ->  How much has that total worth CHANGED
                                 since I entered (or since the last
                                 valuation date)?

  -> A reporting system that only stores "Market Value" and expects
     a downstream consumer to infer Price or P&L by comparing rows
     over time is pushing work onto every consumer that the source
     system could have done once, correctly.
```

**Independent Price Verification (IPV):** Level 2, குறிப்பா Level 3 positions-க்கு (Section 6), Front Office trader-ஓட சொந்த mark ஒருபோதும் இறுதி வார்த்தை இல்ல — ஒரு தனி செயல்பாடு (பொதுவா Middle Office-க்குள்ள Product Control, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §4) பொதுவா மாசாமாசம், தன் சொந்த sourced inputs பயன்படுத்தி valuation-ஐ சுயாதீனமா மறுபடி derive பண்ணும்; trader-ஓட mark-ல இருந்து எந்த materially வித்தியாசமும் விசாரிக்கப்பட்டு, நியாயப்படுத்தப்பட்டா, சரிசெய்யப்படும். அதனாலதான் Level 3 positions ([Module 9](../docs/1-capital-markets/module-09-credit.html)-ஓட illiquid single-name CDS, [Module 6](../docs/1-capital-markets/module-06-fx.html)-ஓட bespoke Cross-Currency Swap) ஒரு Level 1 Coca-Cola position-ஐ விட வெகு அதிக IPV ஆய்வைப் பெறுது — ஒரு Level 1 mark-ஐ ஒரு இரண்டாவது source-ஓட சரிபார்க்க சுயாதீன சந்தை quote இல்ல; ஒரு Level 3 mark-ஓட *model, assumptions*-ஐ மட்டுமே சரிபார்க்க முடியும்.

**Takeaway:** Market Value-க்கும் Exposure-க்கும் (Section 1) உள்ள வேறுபாடு இங்கேயும் மிக முக்கியம் — IPV இருக்கறது, இன்னிக்கு valuation எண் *சரியா*ன்னு உறுதி செய்ய; அடுத்த check-க்கு முன்னாடி அந்த valuation உங்களுக்கு எதிரா இன்னும் எவ்வளவு நகரலாம்ங்கற [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட கவலையிலிருந்து முற்றிலும் தனியான கேள்வி.

---

## 8. Regulatory Perspective — IFRS 13/ASC 820, Basel FRTB, EMIR/Dodd-Frank Valuation Reporting

| Regulation / Body | Valuation-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **IFRS 13** (சர்வதேசம்) | Fair Value, அதன் மூன்று-Level hierarchy (Section 06) வரையறுக்கும் உலகளாவிய accounting standard — **IASB (International Accounting Standards Board)** வெளியிடுது; இந்த roadmap-ல markets/derivatives regulator இல்லாத முதல் standard-setter, ஒரு accounting ஒண்ணு |
| **ASC 820** (US) | IFRS 13-ஓட US GAAP சமமானது, **FASB (Financial Accounting Standards Board)** வெளியிடுது — அதே மூன்று-Level கருத்து, வேற standard-setter; [Module 8](../docs/1-capital-markets/module-08-rates.html) §08-ஓட CFTC/SEC அல்லது [Module 9](../docs/1-capital-markets/module-09-credit.html) §08-ஓட பிளவு-அதிகார எல்லை pattern-ஓட accounting-உலக இணை, trading regulation இல்லாம financial reporting-க்கு |
| **BCBS FRTB** (Fundamental Review of the Trading Book) | Trading-book market risk-க்கு எதிரா வங்கிகள் regulatory capital-ஐ எப்படி கணக்கிடணும்ங்கறதை Basel Committee 2008-க்கு அப்புறம் முழுசா மறுசீரமைச்சது — இந்த module நிறுவின அதே Quote-Based/Model-Based, Level 1/2/3 வேறுபாடுகளின் மேல நேரடியா கட்டப்பட்டது: குறைவான observable, சரிபார்க்க கடினமான valuations உள்ள positions வெச்சிருக்கற வங்கிகளை FRTB வெளிப்படையா (அதிக capital charges-ஓட) தண்டிக்குது |
| **EMIR / Dodd-Frank** | ரெண்டு regimes-உம், Trade Repository-க்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) report ஆகும் OTC derivative trades-ல நிலையான trade economics-ஓட சேர்த்து, அவ்வப்போதைய **valuation/mark-to-market fields**-ஐயும் சேர்க்கணும்னு கோருது — இந்த module-ஓட Section 04 worked examples சரியா அந்த reporting fields நிரப்பப்படும் எண்கள் |
| **IOSCO / BCBS** (கூட்டா) | Non-centrally-cleared derivatives-க்கான அவற்றின் கூட்டு margin தேவைகள் ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html): Collateral & Margin-ல குறிப்பிடப்பட்டவை) Initial, Variation Margin-ஐ சரியா கணக்கிட, துல்லியமான, சுயாதீனமா சரிபார்க்கப்பட்ட valuations (Section 07-ஓட IPV)-ஐ முழுக்க சார்ந்தவை |

**Takeaway:** இந்த module-ஓட regulatory section, markets regulators-ஐ விட accounting standard-setters (IASB, FASB) ஆதிக்கம் செலுத்தற roadmap-ல முதல் ஒண்ணு — "Valuation" என்ற தலைப்பு trading (எண் ஏன் இருக்கவே வேண்டும்) நிதி reporting (எண் ஏன் balance sheet-ல தற்காக்கத்தக்கதா இருக்க வேண்டும்) ரெண்டுக்கும் சந்திப்புல உட்காருதுங்கறதுக்கு நினைவூட்டல்; இந்த roadmap இதுவரை பெரும்பாலும் தனியா வெச்சிருந்த ரெண்டு வேறு துறைகள்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Sections 3–4 முழுக்க மறுமதிப்பீடு செய்யப்பட்ட ஒவ்வொரு position-ஓட holder: bond, IRS, (மறைமுகமா, protection buyer-ஆ) CDS |
| Goldman Sachs | மறுமதிப்பீடு செய்யப்படற bond, IRS trades-ல அசல் counterparty |
| Northstar Resources Inc. | விரிவடையும் credit spread Section 4c-ஓட CDS மறுமதிப்பீட்டை இயக்கும் Reference Entity |
| Product Control / Middle Office | Level 2/3 positions மேல Independent Price Verification செய்யும் (Section 07) |
| IASB / FASB | IFRS 13 / ASC 820-ஓட Fair Value hierarchy-க்குப் பின்னாலுள்ள standard-setters (Section 08) |
| BCBS | FRTB-ஓட valuation observability-க்கான capital treatment-க்குப் பின்னாலுள்ள standard-setter (Section 08) |

---

## Recap Quiz — Numbers Edition

1.  Section 03-ஓட தோராய YTM formula-வை பயன்படுத்தி, Canada 5% Bond 2035-ஐ 98.50-க்குப் பதிலா 102.00 (premium) Clean Price-ல வாங்கியிருந்தா, தோராய YTM என்ன? அது 5.00% coupon rate-க்கு மேலயா கீழயா இருக்கும்?
2.  அதே bond-ல, Section 03-ஓட DV01 எண்ணான CAD 7,092/bp-ஐ பயன்படுத்தி, yields 40 basis points *குறைஞ்சா* தோராய விலை தாக்கம் என்ன?
3.  Section 4b-ஓட AAPL Call-ல, expiry-க்கு ஆறு மாசம் முன்னாடி AAPL USD 245-க்கு (USD 250 strike-க்கு கீழ) விழுந்திருந்தா, Intrinsic Value என்ன? option-ஓட Market Value இன்னும் நேர்மறையா இருக்க முடியுமா?
4.  Section 5-ஓட table-ஐ பயன்படுத்தி, குறிப்பிட்ட trade-ஐப் பொறுத்து Quote-Based அல்லது Model-Based குடும்பத்துல ஏதேனும் ஒண்ணுல இருக்கக்கூடிய ஒரு product-ஐ பெயரிடுங்க, ஏன்னு விளக்குங்க.
5.  மிக liquid, அடிக்கடி trade ஆகற corporate reference entity மேல ஒரு single-name CDS value பண்ணப்படுது. Section 06-ஐ பயன்படுத்தி, இது Level 2-ஆ Level 3-ஆ Fair Value அளவீடா இருக்க அதிக வாய்ப்பு, ஏன்?
6.  Level 1 position, Level 3 position-ஐ விட "வெகு குறைவான IPV ஆய்வைப் பெறுது"ன்னு Section 07 ஏன் சொல்லுது — ஒவ்வொரு position-உம் ஒரே அளவு சரிபார்ப்புக்குத் தகுதியானதில்லையா?
7.  Section 08-ஐ பயன்படுத்தி, ஒரே அடிப்படை உண்மையைப் (ஒரு instrument-ஓட valuation observability) பத்தி IFRS 13-உம் BCBS FRTB-உம் ஒவ்வொண்ணும் குறிப்பா எதைப் பத்தி கவலைப்படுதுன்னு ஒப்பிடுங்க — அவை ஒரே பிரச்சனையைத் தீர்க்குதா, ரெண்டு வேறு பிரச்சனைகளையா?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 11](../docs/1-capital-markets/module-11-risk-exposure.html): Risk & Exposure**-க்குப் போலாம்; இது Section 1-ஓட Market Value vs Exposure வேறுபாட்டின் மேல நேரடியா கட்டுது.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. தோராய YTM ≈ 4.73%, 5.00% coupon rate-க்கு *கீழ*.**

```text
C = 500,000, F = 10,000,000, P = 10,200,000 (Price 102.00), n = 9

Approx. YTM = [ 500,000 + (10,000,000 - 10,200,000)/9 ] / [ (10,000,000 + 10,200,000)/2 ]
            = [ 500,000 - 22,222.22 ] / 10,100,000
            = 477,777.78 / 10,100,000
            = 4.73%
```

Premium கொடுக்கறது (Price > Face Value) என்றா buyer-ஓட உண்மையான return printed coupon rate-க்கு கீழ விழுது — Section 03-ஓட discount-bond முடிவின் mirror image, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1 நிறுவின சரியான விதி: Price > Par இருக்கும்போதெல்லாம் Yield < Coupon Rate.

**2. தோராய விலை *லாபம்* CAD 283,680.**

```text
Price Impact = -DV01 x Basis Points Moved (with sign flipped for a FALL)
             = 7,092 x 40
             = CAD 283,680 gain
```

DV01 எந்தத் திசையிலும் ஒரு basis point-க்கான விலை தாக்கத்தை அளக்குது — Section 03-ஓட worked example yields 25bps *உயர்ந்தப்போ* இழப்பைக் காட்டுச்சு; அதே CAD 7,092/bp உணர்திறன் yields *குறையும்போது* லாபத்தைத் தருது, ஏன்னா bond விலைகளும் yields-உம் எதிர்த்திசைல நகரும் ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1-ஓட அடிப்படை price-vs-rates உறவு).

**3. Intrinsic Value = USD 0. Market Value, முழுக்க Time Value-ல இருந்து, இன்னும் நேர்மறையா இருக்கலாம்.**

```text
Intrinsic Value = max(245 - 250, 0) x 10,000 = max(-5, 0) x 10,000 = USD 0
```

ஒரு option-ஓட Intrinsic Value ஒருபோதும் எதிர்மறையாகாது — buyer out-of-the-money option-ஐ exercise பண்ணவே மாட்டார், அதனால அதன் தளம் எதிர்மறை எண் இல்ல, பூஜ்யம் ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4d-ஓட Scenario B ஏற்கனவே இதை நிறுவியது: option "பயனற்று expire ஆகுது", கொடுத்த premium-ஐ தாண்டி ஒருபோதும் இழப்பில்ல). ஆனா Section 4b-ஓட Time Value கூறு (time-to-expiry, volatility போன்றவற்றால இயக்கப்படுவது) in-the-money-ஆ இருப்பதைச் சார்ந்ததில்ல — உண்மையான volatility உள்ள ஆறு மாசம் தள்ளிய option, USD 5 out-of-the-money-ஆ இருந்தாலும், முழுக்க Time Value-ஆ, பூஜ்யம் Intrinsic Value-ஓட, நேர்மறை premium-ஐ நிச்சயமா பெற முடியும்.

**4. Bonds (Fixed Income) — அதிகமா trade ஆகி live dealer quote இருக்கும்போது Quote-Based; illiquid-ஆ இருக்கும்போது (comparable bonds-ஓட yields வழியா) Level-2-Model-Based; Section 06 Fair Value Levels-க்கு வரையற அதே வேறுபாடு, ஒரு குறிப்பிட்ட trade எந்த Valuation Family-ல விழுதுங்கறதுக்கும் நேரடியா பொருந்துது.**

Section 5-ஓட table Fixed Income, Commodity Physical/Spot/Forward-க்கு "Quote-Based (Clean Price)" முதன்மை அடிப்படைன்னு குறிக்குது, ஆனா Section 06-ஓட Level 2 உதாரணம் வெளிப்படையா "Canada 5% Bond 2035, அடிக்கடி trade ஆகலைன்னா"-வை comparable liquid bonds-ல இருந்து value பண்ணப்படறதா பயன்படுத்துது — Valuation Family (Section 02) ஒரு product வகைக்கு நிலையானது இல்ல, *அந்த குறிப்பிட்ட instrument இப்போ எவ்வளவு liquid* என்பதற்கு நிலையானதுன்னு இது நிரூபிக்குது.

**5. பெரும்பாலும் Level 2 — மிக liquid, அடிக்கடி trade ஆகற name-க்கு market spread உண்மையிலயே observable (dealers தவறாம quote பண்றாங்க), ஒரு பங்குக்கு நடக்கிற மாதிரி, எந்த ஒரு குறிப்பிட்ட CDS contract-ஓட "the" விலையையும் ஒரு தனி exchange print பண்ணலைன்னாலும்.**

Section 06 சரியா இந்தக் கோட்டை வரையுது: "ஒரு liquid, standardized single-name CDS... market spread observable, எந்த exchange-உம் நேரடியா quote பண்ணலைன்னாலும்" என்பது வெளிப்படையா Level 2 உதாரணமா குறிப்பிடப்பட்டிருக்கு; இது, spread input தானே observe பண்ணாம மதிப்பிடப்பட வேண்டிய, அரிதாக trade ஆகற reference entity மேல CDS-ங்கற Level 3 உதாரணத்தோட நேரடியா மாறுபடுது.

**6. ஏன்னா IPV-ஓட முழு நோக்கம் model அல்லது input நிச்சயமின்மையால ஒரு valuation எண் தவறா இருக்கக்கூடிய நிகழ்வுகளைப் பிடிக்கிறது — Level 1 position-க்கு பிடிக்க அப்படி நிச்சயமின்மை இல்ல: சந்தை விலையே மதிப்பு, ஒரு இரண்டாவது சுயாதீன quote source-ஐ சரிபார்த்தாலே உறுதிப்படுத்தலாம், அது நொடிகள் எடுக்கும், முழு model மறுகட்டமைப்பு இல்ல. ஒவ்வொரு position-உம் தொழில்நுட்ப ரீதியா ஏதோ ஒரு சரிபார்ப்பை பெறும், ஆனா அந்த சரிபார்ப்பின் ஆழமும் செலவும் அசல் எண்ணுக்குள்ள எவ்வளவு judgment போச்சுங்கறதுக்கு ஏத்தபடி scale ஆகும்.**

Section 07-ஓட takeaway இந்த விகிதாசாரத்தை வெளிப்படையாக்குது: Level 3 positions "ஒரு Level 1 mark-ஐ ஒரு இரண்டாவது source-ஓட சரிபார்க்க சுயாதீன சந்தை quote இல்லாததால; ஒரு Level 3 mark-ஓட model, assumptions-ஐ மட்டுமே சரிபார்க்க முடியும்" என்பதால அதிக ஆய்வைப் பெறுது — சரிபார்ப்பு பொறிமுறையே அடிப்படையில வேறாக இருக்கணும் (சுயாதீனமா sourced assumptions-ஓட model-ஐ மறுபடி ஓட்டுறது), அதே முறையில் வெறுமனே இன்னும் முழுமையாக இல்ல.

**7. அவை ரெண்டு *தொடர்புடைய* ஆனா உண்மையிலயே *வேறுபட்ட* பிரச்சனைகளைத் தீர்க்குது: IFRS 13, balance sheet-ல என்ன எண் தோன்றணும், financial statements-ஐ படிக்கறவர் அதை எவ்வளவு நம்பணும்ங்கறதைப் பத்தி கவலைப்படுது (வெளி financial-reporting/disclosure கவலை); BCBS FRTB, சரிபார்க்க கடினமான ஒரு valuation தவறா போகும் risk-க்கு எதிரா ஒரு வங்கி எவ்வளவு regulatory CAPITAL வெச்சிருக்கணும்ங்கறதைப் பத்தி கவலைப்படுது (உள், வங்கியின் prudential-பாதுகாப்பு கவலை).**

Section 08 இதை நேரடியா சொல்லுது: FRTB "குறைவான observable, சரிபார்க்க கடினமான valuations உள்ள positions வெச்சிருக்கற வங்கிகளை... வெளிப்படையா தண்டிக்குது" — IFRS 13 balance-sheet வெளிப்படைத்தன்மைக்கு பயன்படுத்தற அதே Level 1/2/3 observability கருத்தை, BCBS முற்றிலும் வேற நோக்கத்துக்கு (ஒரு வங்கி எவ்வளவு இழப்பை உறிஞ்சும் capital வெச்சிருக்கணும்) மறுபயன்படுத்துது, இதனாலதான் Section 08-ஓட takeaway இந்த module-ஐ ஏதோ ஒரு துறைக்கு மட்டும் சேராம trading-உம் financial-reporting-உம் சந்திக்கற இடத்துல இருக்கற முதல் ஒண்ணா குறிக்குது.
