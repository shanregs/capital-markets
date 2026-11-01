# Module 19 — Interest Rate Swap (IRS) Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> இது **[SFTR Reporting Deep Dive](../docs/2-reporting/module-16-sftr-reporting.html)**, **[UK DMO Reporting Deep Dive](../docs/2-reporting/module-17-dmo-uk-reporting.html)**, **[UPI, UTI & ANNA-DSB Deep Dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)** குடும்பத்தை சேர்ந்த **reference module**. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-உம் [Module 8](../docs/1-capital-markets/module-08-rates.html) §4a/§5-உம் ஒரு ஒற்றை-quarter worked example-ஓட Interest Rate Swap-ஐ அறிமுகப்படுத்தியது; இந்த module ஆழமா போகுது — ஒரு உண்மையான trade, அதன் 5-வருஷ வாழ்க்கையின் எல்லா **20 quarterly settlements**-ஐ ஆரம்பம் முதல் முடிவு வரை, சரியான தேதிகள், சரியான cash தொகைகள், CORRA floating leg சரியா எப்படி கணக்கிடப்படுது, ஒரு Trade/Valuation record சுமக்க வேண்டிய ஒவ்வொரு field-உம்.
> **கூடுதலா:** [`examples/`](../examples/) folder-ல [Interest Rate Swap (IRS) — Complete Example](../examples/Interest_Rate_Swap_IRS_Complete_Example.html) — ஒரு சிறிய, ஒற்றை-quarter-மையம் கொண்ட துணை walkthrough, FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS வரை இணைந்த standalone examples சங்கிலியோட முதல் பாடம்.
> **மூலம்:** English Module 19-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Trade — Economic Terms

```text
5-YEAR CAD INTEREST RATE SWAP — ECONOMIC TERMS
========================================================================
  Trade ID:            BOT-IRS-2026-034410
  Trade Date:           2026-08-25  (Tuesday)
  Effective Date:       2026-08-27  (Trade Date + 2 business days — "spot start")
  Maturity Date:        2031-08-27  (Effective Date + 5 years)
  Notional:              CAD 100,000,000  (reference only — NEVER exchanged, Module 1 §8)
  Currency:              CAD

  Bank Of Toronto:       pays FIXED, receives FLOATING (CORRA + spread)
  Goldman Sachs:         pays FLOATING (CORRA + spread), receives FIXED

  Fixed Rate:            4.00% per annum
  Fixed Leg Frequency:   Quarterly
  Fixed Leg Day Count:   Actual/365 (fixed) — standard for CAD swaps

  Floating Index:        CORRA (Canadian Overnight Repo Rate Average — Module 8 §1)
  Floating Spread:       +0.05% (5 basis points), applied to Bank Of Toronto's
                          receive leg
  Floating Compounding:  Daily, compounded IN ARREARS over each quarter
  Floating Leg Frequency: Quarterly (same payment dates as the fixed leg)
  Floating Leg Day Count: Actual/365

  Business Day Convention: Modified Following
  Payment Lag:            2 business days after each Accrual End Date
                           (Section 4 explains why the floating leg needs this lag)

  Clearing:                Centrally cleared via LCH SwapClear (CCP) — mandatory
                           for a standardized CAD IRS under EMIR/Dodd-Frank
                           (Section 4 covers the novation mechanics and how it
                           changes every money flow described in this document)
```

**துல்லியம் பத்தின குறிப்பு.** கையால சரிபார்க்கக்கூடிய கணக்குக்காக, கீழ உள்ள ஒவ்வொரு quarter-உம் ஒரே மாதிரி 91-நாள் accrual period-ஆ நடத்தப்படுது ([Module 8](../docs/1-capital-markets/module-08-rates.html) §4a பயன்படுத்தும் அதே எளிமைப்படுத்தல்); காலண்டர் தேதிகள், ஒவ்வொரு தேதியையும் உண்மையான holiday calendar வழியா நடத்தாம, ஒவ்வொரு period-க்கும் 3 calendar மாதங்களை கூட்டி உருவாக்கப்படுது. Production-ல, day-count fraction-உம் (இது ஒவ்வொரு உண்மையான quarter-க்கும் 90–92 நாட்கள் வரை உண்மையில வேறுபடும்), business-day-adjusted தேதிகளும் கையால கணக்கிடப்படாம, trade system-ஓட schedule engine-ஆல உருவாக்கப்படுது — இங்க உள்ள தேதிகளையும் நாள் எண்ணிக்கைகளையும் விளக்கமா எடுத்துக்கோங்க, rate/formula mechanics-ஐ சரியானதா.

---

## 2. Fixed Leg எப்படி கணக்கிடப்படுது

Fixed leg trade-ஓட வாழ்நாள் முழுக்க மாறவே மாறாது — inputs Notional, ஒத்துக்கிட்ட Fixed Rate, ஒவ்வொரு period-ஓட day-count fraction மட்டும்:

```text
Fixed Leg Accrual = Notional x Fixed Rate x (Accrual Days / 365)

  = 100,000,000 x 4.00% x (91 / 365)
  = 100,000,000 x 0.04 x 0.249315...
  = CAD 997,260.27

-> This SAME amount recurs every one of the 20 quarters, because the
   fixed rate and notional never change. It is the one number in this
   entire worked example that doesn't need to be recalculated per period.
```

---

## 3. Floating Leg எப்படி கணக்கிடப்படுது — CORRA Compounded in Arrears + Spread

**ஏன் "reset in advance"-க்குப் பதிலா "compounded in arrears".** பழைய CDOR/LIBOR-பாணி regime-ல, ஒரு floating leg-ஓட rate ஒவ்வொரு period-ஓட *ஆரம்பத்துல ஒரு தடவை நிர்ணயிக்கப்பட்டு*, quarter முழுக்க மாறாம இருந்தது — rate முன்கூட்டியே தெரியும். CORRA (SOFR, €STR, SONIA போல — [Module 8](../docs/1-capital-markets/module-08-rates.html) §1) ஒரு **overnight** rate, அதனால quarterly floating leg அதுக்குப் பதிலா முழு period முழுக்க கவனிக்கப்பட்ட தினசரி overnight CORRA prints-ஐ நாளுக்கு நாள் **compound பண்ணுது**; period-க்கான compounded rate period *முடிந்த பிறகே* முழுசா தெரியும். LIBOR-to-RFR மாற்றத்துல IT systems உள்வாங்க வேண்டியிருந்த மிகப்பெரிய ஒற்றை இயந்திர வேறுபாடு இதுதான்.

```text
COMPOUNDED-IN-ARREARS FORMULA

  Compounded Factor = PRODUCT over every calendar day i in the period
                       of  [ 1 + r_i x (d_i / 365) ]

    where r_i  = that day's published overnight CORRA rate
          d_i  = the number of calendar days that rate applies to
                 (1 for a weekday; 3 for a Friday print, which also
                 covers Saturday and Sunday under the standard
                 "non-business day carries the prior day's rate" rule)

  Compounded Rate (annualized) = (Compounded Factor - 1) x (365 / D)

    where D = total calendar days in the period (91, in this example)
```

இது முழு quarter-க்கும் ஒரே annualized rate-ஐ உருவாக்குது — இந்த rate அளவுகள்ல பொருளாதார ரீதியா எளிய தினசரி சராசரிக்கு மிக நெருக்கம், ஆனா அதே இல்ல; அதனாலதான் இந்த ஆவணமும் [Module 8](../docs/1-capital-markets/module-08-rates.html) §4a-உம் பயன்படுத்தும் "இந்த quarter CORRA சராசரி 3.20%" என்ற சுருக்கம் உண்மையில "period-க்கு தினசரி-compound ஆன rate 3.20%-ஆ வந்தது"ன்னு அர்த்தம், ஒரு எளிய arithmetic mean இல்ல.

**ஏன் +0.05% spread இருக்கு.** ஒரு swap பொதுவா inception-ல அதன் மதிப்பு பூஜ்யமா (ஒரு "par swap") இருக்கும்படி விலையிடப்படுது — அதை உண்மையாக்கும் fixed rate அரிதாவே ஒரு சுத்தமான round எண். 3.9713% போன்ற விசித்திரமான fixed rate-ஐ quote பண்றதுக்குப் பதிலா, ஒரு desk பொதுவா சுத்தமான round Fixed Rate-ஐ (இங்க 4.00%) quote பண்ணி, மீதி அடிப்படையை floating leg-ல சேர்க்கப்பட்ட ஒரு சிறிய spread-ஆ உள்வாங்குது. Spread ஒரு தடவை, trade date-ல ஒத்துக்கப்படுது; CORRA போல இல்லாம, trade-ஓட வாழ்நாள் முழுக்க ஒருபோதும் மாறாது.

**Worked example — Period 1-ஓட floating leg:**

```text
Floating Leg Accrual = Notional x (Compounded CORRA + Spread) x (Accrual Days / 365)

  Compounded CORRA over Period 1:  3.20%
  + Spread:                        0.05%
  = Floating Rate Applied:         3.25%

  = 100,000,000 x 3.25% x (91 / 365)
  = 100,000,000 x 0.0325 x 0.249315...
  = CAD 810,273.97
```

**IT/reporting பார்வை:** compounded rate accrual period தானே முடியும் வரை முழுசா தெரியாததால, ஒரு floating-leg accrual தொகையை period-ஓட கடைசி CORRA print வெளியிடப்படுவதுக்கு முன்னாடி கணக்கிடவே முடியாது — invoice பண்ணவோ settle பண்ணவோ கூட. அதனாலதான் Section 1-ஓட terms ஒவ்வொரு Accrual End Date-க்கும் அப்புறம் **2-business-day payment lag**-ஐ கொண்டிருக்கு: ஒரு back-office system கடைசி தினசரி CORRA prints-ஐ இழுத்து, மேல உள்ள compounding formula-வை ஓட்டி, settlement தொகையை உருவாக்கி, payment instructions-ஐ release பண்ண தேவைப்படும் operational buffer — தன்னிச்சையான வழக்கம் இல்ல.

### கூடுதல் உதாரணம் — "Compounded in arrears" ஏன் எளிய சராசரியிலிருந்து சிறிது வேறுபடுது? (சிறிய எண்களோட)

3 நாள் மட்டும் எடுத்துப் பாருங்க (வெறும் புரிதலுக்கு), CORRA = 3.00%, 3.20%, 3.40%:

| முறை | கணக்கு | ஆண்டு rate |
|---|---|---|
| எளிய சராசரி | (3.00 + 3.20 + 3.40) / 3 | 3.2000% |
| Compounded (3 நாள்) | [(1 + 0.0300/365) × (1 + 0.0320/365) × (1 + 0.0340/365) − 1] × (365/3) | ≈ 3.2001% |

வித்தியாசம் மிகச் சிறியது (ஒரு basis point-ஓட நூறில் ஒரு பங்கு கூட இல்ல), ஆனா CAD 100,000,000 notional-ல 91 நாள் காலத்துல இது சில பத்துகள் முதல் சில நூறுகள் CAD வரை வித்தியாசமாகலாம் — அதனால systems சரியான compounding formula-வை பயன்படுத்தணும்.

---

## 4. Netting & Settlement Mechanics — உண்மையில என்ன நகருது, எப்போ

### Clearing — LCH SwapClear-க்கு Novation

Section 1 சொன்ன மாதிரி, இந்த trade centrally cleared — கற்பனை இல்ல, Section 7 trade record-ல சுமக்கப்படும் உண்மையான நிலை (`clearingStatus: Cleared`, `ccpId: LCH SwapClear`). Execution-க்கு சிறிது நேரத்துல, Bank Of Toronto-வும் Goldman Sachs-உம் ஒவ்வொண்ணும் trade-ஐ **LCH SwapClear**-க்கு submit பண்ணுது — interest rate swaps-க்கான LCH-ஓட அர்ப்பணிப்பு clearing service ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6 பொது novation mechanism-ஐ உள்ளடக்குது; இந்த குறிப்பிட்ட trade-க்கு பொருத்தப்பட்ட அதே mechanism இது). Novation அசல் bilateral contract-ஐ **ரத்து செய்து (extinguish)**, அதுக்குப் பதிலா பொருளாதார ரீதியா பிரதிபலிக்கும் இரண்டு புது contracts-ஐ **உருவாக்குது**:

```text
BEFORE NOVATION  —  one bilateral trade
========================================================================
  Bank Of Toronto  <====  5Y CAD IRS, Notional CAD 100,000,000  ====>  Goldman Sachs
       (pays fixed 4.00% / receives floating CORRA + 0.05%)

AFTER NOVATION  —  LCH SwapClear steps in; TWO trades now exist
========================================================================
  Bank Of Toronto      <====  IRS Leg #1, CAD 100,000,000  ====>  LCH SwapClear (CCP)
       (pays fixed 4.00% / receives floating CORRA + 0.05%)

  LCH SwapClear (CCP)  <====  IRS Leg #2, CAD 100,000,000  ====>  Goldman Sachs
       (pays fixed 4.00% / receives floating CORRA + 0.05% — mirror of Leg #1)

  -> The original Bank Of Toronto <-> Goldman Sachs contract no longer
     exists; it was legally extinguished by novation. Every Fixed/
     Floating accrual and Net Amount calculated in Sections 2, 3, and
     5 below now settles TWICE — once per leg — never once directly
     between the two banks. Bank Of Toronto's only counterparty for
     the remaining life of the trade is LCH SwapClear; if Goldman
     Sachs defaulted, LCH SwapClear absorbs the loss and Bank Of
     Toronto's own leg is unaffected.
```

ரெண்டு legs-உம் rate, notional, தேதிகள்ல ஒன்றையொன்று சரியா பிரதிபலிக்குது, அதனால LCH SwapClear இந்த trade-ல நிகர *சந்தை* risk-ஐ ஏற்கவே ஏற்காது — ஒரு leg-ல கொடுக்கறதை சரியா மற்றதுல வசூலிக்குது. அது ஏற்பது ஒவ்வொரு வங்கியிலிருந்தும் **counterparty credit risk** — அதனாலதான் novation-ல ரெண்டு பக்கத்துல இருந்தும் Initial Margin-ஐயும், அதுக்குப் பிறகு தினசரி Variation Margin-ஐயும் கோருது (முழு mechanics [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html)-ல; novation mechanism தானே [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6-ல).

### Netting கணக்கீடு — ஒவ்வொரு Leg-க்கும் ஒரே மாதிரி

```text
NETTING, EVERY QUARTER  (same math on each of the two novated legs)
========================================================================
  Fixed Leg Accrual (fixed-rate payer owes the other side)   CAD 997,260.27
  Floating Leg Accrual (floating-rate payer owes the other)  CAD 810,273.97  (Period 1)
                                                              --------------
  Net Settlement = Fixed - Floating = 997,260.27 - 810,273.97 = CAD 186,986.30

  -> Only this ONE net amount moves, in ONE direction, per leg, on the
     Payment Date. Neither side of either leg ever transfers its own
     gross accrual — that is purely an intermediate calculation. The
     CAD 100,000,000 notional itself never moves at all, on this date
     or any other, on either leg (Module 1 §8).
```

```text
MONEY FLOW — PERIOD 1  (post-novation, via LCH SwapClear)
========================================================================
   Bank Of Toronto  --CAD 186,986.30 (net)-->  LCH SwapClear (CCP)  --CAD 186,986.30 (net)-->  Goldman Sachs
   (fixed-rate payer, Leg #1)                                       (floating-rate payer, Leg #2)

   Settlement Date: 2026-11-29  (Accrual End 2026-11-27 + 2 business days)

   -> Two separate settlement instructions fire on the same date, for
      the same amount, in the same direction. LCH SwapClear passes the
      amount straight through and is never left holding a net position
      on this trade — the same "collects on one leg what it pays on
      the other" property noted above.
```

**Section 5-ஓட schedule முழுக்க பயன்படுத்தப்படும் குறி வழக்கம்:** ஒரு **நேர்மறை** Net Amount என்பது Fixed leg பெருசுன்னு அர்த்தம், அதனால **Bank Of Toronto, LCH SwapClear-க்கு (Leg #1) கொடுக்கும், LCH SwapClear, Goldman Sachs-க்கு (Leg #2) கொடுக்கும்**. ஒரு **எதிர்மறை** Net Amount என்பது Floating leg பெருசுன்னு அர்த்தம், அதனால ரெண்டு legs-லயும் திசை புரளுது — **Goldman Sachs, LCH SwapClear-க்கு கொடுக்கும், LCH SwapClear, Bank Of Toronto-க்கு கொடுக்கும்**. Section 5-ஓட table பொருளாதார தெளிவுக்காக அசல் bilateral திசையை ("Bank Of Toronto → Goldman Sachs") பெயரிடுது; operational ரீதியா அது எப்பவும் CCP வழியா ரெண்டு legs, ஒருபோதும் ஒரு நேரடி transfer இல்ல — [Module 8](../docs/1-capital-markets/module-08-rates.html) §4a-ஓட Recap Quiz Answer 1 ஒரு ஒற்றை quarter-க்கு நடத்திக் காட்டும் அதே fixed-vs-floating திசை புரள் mechanic.

---

## 5. முழு Payment Schedule — 20 Quarters-உம், 5 வருஷம்

கீழ உள்ள ஒவ்வொரு period-உம் Section 2/3 formulas-ஐ சரியா பயன்படுத்துது — Fixed Accrual முழுக்க CAD 997,260.27-ல நிலையா; Floating Accrual ஒவ்வொரு quarter-உம் அந்த quarter-ஓட கருதப்படுற compounded CORRA rate-ஓட மாறுது (விளக்கம், ஒரு நம்பகமான கதை சொல்ல இங்க தேர்ந்தெடுக்கப்பட்டது: 2-ஆம் வருஷம் முழுக்க rates சரிந்து, அப்புறம் மேல ஏறி 5-ஆம் வருஷத்துக்குள்ள 4.00% fixed rate-ஐ தாண்டுது).

**Post-novation "Who Pays" column-ஐ படிக்கறது:** கீழ உள்ள ஒவ்வொரு Net Amount-உம் ரெண்டு பேருக்கும் இடையில நேரடியா இல்லாம, **LCH SwapClear** வழியா ரெண்டு legs-ஆ settle ஆகுதுன்னு Section 4 நிறுவியது. Table பொருளாதார தெளிவுக்காக அசல் bilateral திசையை பெயரிடுது — "Bank Of Toronto → Goldman Sachs"-ஐ முழுக்க "Bank Of Toronto → LCH SwapClear → Goldman Sachs"-க்கான சுருக்கமா படிங்க, Periods 17–20-க்கு எதிர்த்திசையையும் அதே மாதிரி.

| # | Accrual Start | Accrual End | Payment Date | CORRA (compounded) | Floating Rate (+0.05%) | Fixed Accrual (CAD) | Floating Accrual (CAD) | Net Amount (CAD) | Who Pays |
|---:|---|---|---|---:|---:|---:|---:|---:|---|
| 1 | 2026-08-27 | 2026-11-27 | 2026-11-29 | 3.20% | 3.25% | 997,260.27 | 810,273.97 | 186,986.30 | Bank Of Toronto → Goldman Sachs |
| 2 | 2026-11-27 | 2027-02-27 | 2027-03-01 | 3.10% | 3.15% | 997,260.27 | 785,342.47 | 211,917.80 | Bank Of Toronto → Goldman Sachs |
| 3 | 2027-02-27 | 2027-05-27 | 2027-05-29 | 3.05% | 3.10% | 997,260.27 | 772,876.71 | 224,383.56 | Bank Of Toronto → Goldman Sachs |
| 4 | 2027-05-27 | 2027-08-27 | 2027-08-29 | 3.00% | 3.05% | 997,260.27 | 760,410.96 | 236,849.31 | Bank Of Toronto → Goldman Sachs |
| 5 | 2027-08-27 | 2027-11-27 | 2027-11-29 | 2.95% | 3.00% | 997,260.27 | 747,945.21 | 249,315.06 | Bank Of Toronto → Goldman Sachs |
| 6 | 2027-11-27 | 2028-02-27 | 2028-02-29 | 2.90% | 2.95% | 997,260.27 | 735,479.45 | 261,780.82 | Bank Of Toronto → Goldman Sachs |
| 7 | 2028-02-27 | 2028-05-27 | 2028-05-29 | 2.90% | 2.95% | 997,260.27 | 735,479.45 | 261,780.82 | Bank Of Toronto → Goldman Sachs |
| 8 | 2028-05-27 | 2028-08-27 | 2028-08-29 | 2.95% | 3.00% | 997,260.27 | 747,945.21 | 249,315.06 | Bank Of Toronto → Goldman Sachs |
| 9 | 2028-08-27 | 2028-11-27 | 2028-11-29 | 3.05% | 3.10% | 997,260.27 | 772,876.71 | 224,383.56 | Bank Of Toronto → Goldman Sachs |
| 10 | 2028-11-27 | 2029-02-27 | 2029-03-01 | 3.20% | 3.25% | 997,260.27 | 810,273.97 | 186,986.30 | Bank Of Toronto → Goldman Sachs |
| 11 | 2029-02-27 | 2029-05-27 | 2029-05-29 | 3.40% | 3.45% | 997,260.27 | 860,136.99 | 137,123.28 | Bank Of Toronto → Goldman Sachs |
| 12 | 2029-05-27 | 2029-08-27 | 2029-08-29 | 3.55% | 3.60% | 997,260.27 | 897,534.25 | 99,726.02 | Bank Of Toronto → Goldman Sachs |
| 13 | 2029-08-27 | 2029-11-27 | 2029-11-29 | 3.70% | 3.75% | 997,260.27 | 934,931.51 | 62,328.76 | Bank Of Toronto → Goldman Sachs |
| 14 | 2029-11-27 | 2030-02-27 | 2030-03-01 | 3.80% | 3.85% | 997,260.27 | 959,863.01 | 37,397.26 | Bank Of Toronto → Goldman Sachs |
| 15 | 2030-02-27 | 2030-05-27 | 2030-05-29 | 3.90% | 3.95% | 997,260.27 | 984,794.52 | 12,465.75 | Bank Of Toronto → Goldman Sachs |
| 16 | 2030-05-27 | 2030-08-27 | 2030-08-29 | 3.95% | 4.00% | 997,260.27 | 997,260.27 | 0.00 | — (no payment; legs exactly offset) |
| 17 | 2030-08-27 | 2030-11-27 | 2030-11-29 | 4.00% | 4.05% | 997,260.27 | 1,009,726.03 | 12,465.76 | Goldman Sachs → Bank Of Toronto |
| 18 | 2030-11-27 | 2031-02-27 | 2031-03-01 | 4.05% | 4.10% | 997,260.27 | 1,022,191.78 | 24,931.51 | Goldman Sachs → Bank Of Toronto |
| 19 | 2031-02-27 | 2031-05-27 | 2031-05-29 | 4.10% | 4.15% | 997,260.27 | 1,034,657.53 | 37,397.26 | Goldman Sachs → Bank Of Toronto |
| **20 (Final)** | **2031-05-27** | **2031-08-27** | **2031-08-29** | **4.15%** | **4.20%** | **997,260.27** | **1,047,123.29** | **49,863.02** | **Goldman Sachs → Bank Of Toronto** |

```text
LIFETIME SUMMARY
========================================================================
  Total Fixed Leg paid (20 periods x CAD 997,260.27)      CAD 19,945,205.40
  Total Floating Leg paid (sum of 20 varying accruals)     CAD 17,427,123.29
                                                            ------------------
  Cumulative net cash flow, Bank Of Toronto -> Goldman Sachs  CAD 2,518,082.11

  -> Read this as "sum of 20 independent quarterly settlements," NOT as
     a single discounted valuation figure -- each period settles and
     closes out on its own Payment Date (Section 4); no period's cash
     flow carries forward into, or nets against, a later period's.
     A genuine present-value figure for the trade at any point in its
     life is covered in Module 10 (Valuation), not here.
```

**Takeaway:** யார் யாருக்கு கொடுக்கறாங்கங்கற திசை சரியா ஒரே ஒரு தடவை புரளுது — Period 16-க்கும் (floating, fixed-க்கு சரியா சமம், அதனால net தொகை பூஜ்யம், எதுவும் settle ஆகாது) Period 17-க்கும் (floating முதல் தடவை fixed-ஐ தாண்டுது) இடையில. முதல் 16 quarters, floating CORRA 4.00% fixed rate-க்கு கீழ நிக்குது, அதனால fixed-rate payer ஆன Bank Of Toronto முழுக்க net payer; கடைசி நாலு quarters-ல CORRA 4.00%-ஐ தாண்டி ஏறினதும், Bank Of Toronto net *receiver*-ஆ புரளுது. ஒரு IRS-ல ஒரு treasury desk fixed கொடுக்கறதுக்கான பொருளாதார காரணம் சரியா இதுதான்: rates குறைவா இருக்கும் வரை பணம் செலவாகும், rates lock பண்ணின fixed rate-ஐ தாண்டி உயர்ந்தப்புறம் பலன் தரும் ஒரு hedge.

### கூடுதல் உதாரணம் — Schedule-ஐ ஒரு விரைவுப் பார்வையில்

| கட்டம் | Periods | CORRA நிலை | Net payer |
|---|---|---|---|
| Rates விழுகிற கட்டம் | 1–6 | 3.20% → 2.90% | Bank Of Toronto (net தொகை 186,986 → 261,781 வரை அதிகரிக்குது) |
| தாழ்வு நிலை | 6–7 | 2.90% (குறைந்தபட்சம்) | Bank Of Toronto (மிகப்பெரிய net: 261,780.82) |
| Rates மீளும் கட்டம் | 8–15 | 2.95% → 3.90% | Bank Of Toronto (net படிப்படியா குறையுது) |
| Crossover | 16 | 3.95% + 0.05% = 4.00% | யாருமில்ல (0.00) |
| Rates fixed-ஐ தாண்டுது | 17–20 | 4.00% → 4.15% | Goldman Sachs (Bank Of Toronto net receiver) |

---

## 6. Maturity-ல இறுதி Settlement

மேல உள்ள Period 20 வரிதான் இறுதி settlement — அதைத் தாண்டி தனி "maturity payment" இல்ல:

```text
FINAL SETTLEMENT — 2031-08-29 (Period 20's Payment Date)
========================================================================
  Goldman Sachs --CAD 49,863.02--> LCH SwapClear --CAD 49,863.02--> Bank Of Toronto
  (the same two-leg routing Section 4 established, applied one last time)

  On the SAME date, the trade reaches its Maturity Date (2031-08-27,
  two business days earlier) and simply EXPIRES:

  - The CAD 100,000,000 Notional is NEVER exchanged -- not on this
    date, not on any of the prior 19 Payment Dates, on either leg. A
    single-currency IRS has nothing left to settle beyond each
    period's net cash flow (contrast a Cross-Currency Swap, which
    DOES exchange notional at both start and maturity -- out of
    scope for this module).
  - LCH SwapClear closes out BOTH legs simultaneously — there is no
    separate unwind step specific to the CCP relationship; maturity
    just means Period 20 was the last Payment Date on the schedule.
  - Bank Of Toronto's Rates book removes the position; the trade's
    UTI (Section 7) transitions from ACTIVE to MATURED in the
    position-keeping system.
  - No further regulatory reporting event is required beyond the
    routine lifecycle-event report closing the trade out at the
    Trade Repository (Module 14 §-scoped lifecycle reporting).
```

---

## 7. முக்கிய Fields & Attributes

### Trade-நிலை fields

| Field | Value in this example | Notes |
|---|---|---|
| `tradeId` | `BOT-IRS-2026-034410` | Internal trade identifier ([Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §-scoped Trade ID) |
| `uti` | Fresh UTI generated at execution | One per trade — [Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §12 |
| `upi` | Shared UPI for "5Y CAD Fixed-Float IRS referencing CORRA" | Reused across every trade sharing this product shape — [Module 8](../docs/1-capital-markets/module-08-rates.md) §6 |
| `tradeDate` | 2026-08-25 | Date the trade was executed |
| `effectiveDate` | 2026-08-27 | First Accrual Start Date ("spot start," T+2) |
| `maturityDate` | 2031-08-27 | Last Accrual End Date |
| `notionalAmount` | 100,000,000.00 | Calculation input only — never settles (Section 1) |
| `notionalCurrency` | CAD | |
| `payerParty` (fixed leg) | Bank Of Toronto (LEI) | |
| `receiverParty` (fixed leg) | Goldman Sachs (LEI) | |
| `fixedRate` | 0.0400 | Section 2 |
| `fixedLegFrequency` | Quarterly | |
| `fixedLegDayCountConvention` | ACT/365 | |
| `floatingRateIndex` | CORRA | [Module 8](../docs/1-capital-markets/module-08-rates.md) §1 |
| `floatingSpread` | 0.0005 | Section 3 |
| `floatingLegFrequency` | Quarterly | |
| `floatingLegDayCountConvention` | ACT/365 | |
| `compoundingMethod` | Compounded-in-Arrears | Section 3 |
| `businessDayConvention` | Modified Following | |
| `paymentLagBusinessDays` | 2 | Section 3's IT/reporting angle |
| `counterpartyLei` | Goldman Sachs' LEI | [Module 13](../docs/1-capital-markets/module-13-parties-leis.md) |
| `clearingStatus` | Cleared | |
| `ccpId` | LCH SwapClear | Section 4 |

### Valuation-நிலை fields

| Field | Purpose |
|---|---|
| `valuationDate` | The date this mark-to-market snapshot is taken (typically daily, between Payment Dates) |
| `discountCurve` | The CORRA OIS discount curve used to present-value remaining cash flows ([Module 10](../docs/1-capital-markets/module-10-valuation.md)) |
| `presentValue` / `npv` | Net present value of all remaining projected fixed-vs-floating cash flows, discounted back to `valuationDate` |
| `dv01` / `pv01` | Change in `presentValue` for a 1 basis point parallel shift in the CORRA curve — [Module 8](../docs/1-capital-markets/module-08-rates.md) §1 |
| `accruedFixedInterest` | Fixed leg interest accrued since the last Payment Date but not yet settled |
| `accruedFloatingInterest` | Floating leg interest accrued (compounding daily) since the last Payment Date |
| `lastSettledAmount` | The most recent Section 5 Net Amount actually paid |
| `nextPaymentDate` | The next unpaid row's Payment Date from Section 5's schedule |
| `unrealizedPnl` | Change in `presentValue` since trade inception or since the last reporting date |

---

## 8. Trade Lifecycle-ல இது எங்க பொருந்துது

இந்த module, அதே trade-க்கு (`BOT-IRS-2026-034410`) [Module 8](../docs/1-capital-markets/module-08-rates.html) §5-ஓட முழு lifecycle walkthrough-ல **Clearing**, **Settlement**, **Position** stages மேல ஒரு drill-down — Order, Execution, Trade Capture, Confirmation எல்லாம் trade inception-ல சரியா ஒரு தடவை நடக்கும்; **Clearing ஒரு தடவை, execution-க்கு சிறிது நேரத்துல நடக்கும் — Section 4 வரையற ரெண்டு பிரதிபலிக்கும் legs-க்கு LCH SwapClear trade-ஐ novate பண்ணும் தருணம் இது**; ஆனா **Settlement 20 தடவை திரும்ப நடக்கும்**, Section 5-ஓட table-ல ஒவ்வொரு வரிக்கும் ஒரு தடவை, ஒவ்வொண்ணும் அந்த quarter-ஓட சொந்த CORRA compounding-ல இருந்து சுயாதீனமா கணக்கிடப்பட்டு, LCH SwapClear வழியா ரெண்டு தடவை (ஒரு leg-க்கு ஒண்ணு) settle ஆகுது. [Module 8](../docs/1-capital-markets/module-08-rates.html) roadmap-ல வேறெதுக்கும், ஒரே Trade Capture event-ல இருந்து தன் சொந்த Settlement stage-ஐ இத்தனை முறை மறுபடி பார்க்கற lifecycle இல்ல.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Fixed-rate payer; Periods 1–15-ல net payer, Periods 17–20-ல net receiver |
| Goldman Sachs | Floating-rate payer; Periods 1–15-ல net receiver, Periods 17–20-ல net payer |
| LCH SwapClear | Execution-க்கு அப்புறம் trade novate ஆகும் CCP — Section 4-ல இருந்து ஒவ்வொரு money flow-வின் நடுவுலயும் உட்காரும், 20 settlements எல்லாத்தின் ரெண்டு legs-லயும் + Section 6-ஓட இறுதி settlement-லயும் |
| Bank of Canada | Floating leg compound பண்ணும் தினசரி CORRA prints-ஐ வெளியிடுது (Section 3; [Module 8](../docs/1-capital-markets/module-08-rates.html) §1) |

---

## Recap Quiz — Numbers Edition

1.  Period 6-ல compounded CORRA 2.90% (schedule-ல மிகக் குறைவு). Section 3-ஓட formula-வை பயன்படுத்தி, CAD 735,479.45 Floating Accrual-ஐ உறுதிப்படுத்துங்க; இதுதான் Bank Of Toronto-ல இருந்து Goldman Sachs-க்கு மிகப்பெரிய net payment உள்ள period-உம் ஏன்னு விளக்குங்க.
2.  Period 16 சரியா CAD 0.00-க்கு settle ஆகுறது ஏன்? அந்த Payment Date-ல operational ரீதியா எதுவுமே நடக்காதுன்னு அர்த்தமா?
3.  Section 5, Period 17-ல இருந்து payment திசை புரளுதுன்னு காட்டுது. Period 21-ல (கற்பனையா, trade 20-க்குப் பதிலா 21 quarters-ஆ இருந்திருந்தா) compounded CORRA 4.30%-ஆ வந்தா, யார் யாருக்கு கொடுக்கறாங்க, தோராயமா எவ்வளவு?
4.  Section 6, CAD 100,000,000 notional maturity-ல கூட ஒருபோதும் பரிமாறப்படாதுன்னு சொல்லுது. [Module 8](../docs/1-capital-markets/module-08-rates.html)-ல ஒரு principal தொகை இறுதியில உண்மையில கை மாறும் ஒரு Rates-family product-ஐ பெயரிடுங்க — அந்த product ஒரு plain IRS-ல இருந்து கட்டமைப்பு ரீதியா ஏன் வேறுபடுது?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 8](../docs/1-capital-markets/module-08-rates.html): Rates**-க்குத் திரும்பலாம், அல்லது Asset Class Roadmap-ஐ **[Module 9](../docs/1-capital-markets/module-09-credit.html): Credit**-ல தொடரலாம்.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Floating Accrual = 100,000,000 x 2.95% x (91/365) = CAD 735,479.45 — உறுதிப்படுத்தப்பட்டது. இது 4.00% fixed rate-க்கு கீழ மிக அகலமான இடைவெளி உள்ள period என்பதால் மிகப்பெரிய net payment (2.95% floating vs. 4.00% fixed = 1.05-புள்ளி spread, முழு schedule-லயும் மிக அகலமானது).**

Net Amount = Fixed Accrual − Floating Accrual, அதனால மிகப்பெரிய net payment, Fixed Rate-க்கும் (4.00%-ல நிலையா) அந்த period-ஓட Floating Rate-க்கும் இடையிலான மிகப்பெரிய இடைவெளிக்கு ஒத்தது — Period 6/7-ஓட 2.95% floating rate முழு 20-quarter schedule-லயும் மிகக் குறைவு, மிக அகலமான இடைவெளியையும் அதனால மிகப்பெரிய ஒற்றை net payment-ஐயும் (CAD 261,780.82) உருவாக்குது.

**2. Period 16 சரியா CAD 0.00-க்கு settle ஆகுது, ஏன்னா compounded CORRA (3.95%) + 0.05% spread சரியா 4.00% Fixed Rate-க்கு சமம் — ரெண்டு legs-உம் எண்ணளவுல ஒரே மாதிரி, net பண்ண மிச்சம் எதுவும் இல்ல. Operational ரீதியா, Payment Date இன்னும் நடக்குது, CAD 0.00 settlement instruction-ஐ இன்னும் உருவாக்குது — பெரும்பாலான systems தணிக்கைத் தடத்தின் முழுமைக்காக ஒரு பூஜ்ய-மதிப்பு settlement event-ஐ இன்னும் book பண்ணுது, period-ஐ அமைதியா தவிர்க்காம.**

இது [Module 8](../docs/1-capital-markets/module-08-rates.html) §4c-ஓட Recap Quiz Answer 3 at-the-money Cap caplet-க்கு நிறுவிய "rate-ஐ சரியா தொடுவது எல்லை நிலையை உருவாக்குது" pattern.

**3. Goldman Sachs, Bank Of Toronto-க்கு கொடுக்கும். Floating Rate = 4.30% + 0.05% spread = 4.35%; Floating Accrual = 100,000,000 x 4.35% x (91/365) = CAD 1,084,520.55; Net = 997,260.27 − 1,084,520.55 = −CAD 87,260.28, அதாவது Goldman Sachs, Bank Of Toronto-க்கு CAD 87,260.28 கொடுக்குது.**

இது Periods 17–20 ஏற்கனவே நிறுவற அதே போக்கை தொடருது: compounded CORRA 4.00% fixed rate-ஐ விட எவ்வளவு அதிகமா உயருதோ, Bank Of Toronto-ஓட net receipt அவ்வளவு பெருசாகுது.

**4. Cross-Currency Swap என்பது notional உண்மையிலயே பரிமாறப்படும் Rates-family product — ஆரம்பத்துலயும் maturity-லயும், ரெண்டு வேறு currencies-ல, ஏன்னா ஒரு cross-currency swap-ஓட முழு நோக்கமே ஒரு currency-ல ஒரு principal தொகையை, மற்றொண்ணுல சமமான principal-க்கு மாத்தறது; ஒவ்வொரு தரப்பும் அப்புறம் தான் இப்போ வெச்சிருக்கற currency மேல வட்டி கொடுக்குது. ஒரு plain single-currency IRS-க்கு notional-ஐ பரிமாறவே தேவையில்ல, ஏன்னா ரெண்டு legs-ஓட cash flows-உம் ஏற்கனவே ஒரே currency-ல (இங்க CAD) denominate ஆகியிருக்கு — வட்டியை net பண்ணினா போதும், principal-ஐ பரிமாற வேண்டிய currency-conversion நோக்கமே முதல்லயே இல்ல.**

Cross-Currency Swap [Module 8](../docs/1-capital-markets/module-08-rates.html)-ஓட ஐந்து-product Interest Rate taxonomy-க்கு வெளியே உட்காருது (அது பொதுவா தன் சொந்த FX/Rates-hybrid product-ஆ model பண்ணப்படுது) — இங்க குறிப்பா flag பண்ணப்படுறது, "notional ஒருபோதும் settle ஆகாது" (Section 1; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) என்பது *இந்த* module-ஓட ஐந்து products பத்தின விதி, Notional field உள்ள ஒவ்வொரு derivative பத்தின முழுமையான விதி இல்லை என்பதால.
