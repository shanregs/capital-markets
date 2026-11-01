# Module 24 — Basis Points Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Reference module**, **[Hedging Deep Dive](../docs/1-capital-markets/module-22-hedging-deep-dive.html)**, **[Interest Rate Swap Deep Dive](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.html)** குடும்பத்தை சேர்ந்தது. Basis points ஏற்கனவே [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1 (Duration), [Module 8](../docs/1-capital-markets/module-08-rates.html) §1 (DV01 முன்னோட்டம்), [Module 9](../docs/1-capital-markets/module-09-credit.html) (CDS spreads), [Module 10](../docs/1-capital-markets/module-10-valuation.html) §3–4 (DV01/Spread DV01 valuation)-ல சிதறியிருக்கு — இந்த module அவை எல்லாத்தையும் ஒரே theory-க்குள்ள இழுத்து, ஆறு-asset-class தொகுப்பை நிறைவு செய்ய Equity, FX worked examples-ஐ சேர்த்து, இந்த curriculum தொடர்ந்து திரும்பிவரும் "எவ்வளவு அடிக்கடி மாறுது, உண்மையில எதை நகர்த்துது" என்ற நடைமுறைக் கேள்விக்கு பதில் சொல்லுது.
> **மூலம்:** English Module 24-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Basis Point-ன்னா என்ன? — Definition & ஏன் Percent போதுமான துல்லியம் இல்ல

**Definition:** **basis point (bp, பன்மை bps)** = **ஒரு சதவீதத்தின் நூறில் ஒரு பங்கு** — 0.01%, அல்லது decimal வடிவத்துல 0.0001. ஒரு சதவீத நகர்வு முழு சதவீத புள்ளிகள்ல விவரிக்க ரொம்ப சிறியதா, அல்லது ரொம்ப முக்கியமானதா இருக்கும்போதெல்லாம் capital markets பயன்படுத்தும் நிலையான அலகு.

```text
THE CONVERSION EVERY OTHER SECTION IN THIS MODULE ASSUMES
========================================================================

  1 basis point (1 bp)   = 0.01%   = 0.0001
  10 basis points         = 0.10%   = 0.0010
  25 basis points         = 0.25%   = 0.0025
  100 basis points        = 1.00%   = 0.0100
  250 basis points        = 2.50%   = 0.0250

  -> "bps" is always ADDITIVE on a percentage/rate scale, never
     multiplicative -- a rate moving from 4.00% to 4.25% moved
     "up 25bps," not "up 6.25%" (which would be the PERCENTAGE
     CHANGE of 4.00 relative to itself -- a different, and much
     less useful, number in this context).
```

**ஏன் வெறுமனே "0.25%"ன்னு சொல்லக்கூடாது?** இந்த curriculum-ஓட மற்ற modules இதுவரை வெளிப்படையா சொல்லாம சார்ந்திருந்த ரெண்டு காரணங்கள்:

-   **குழப்பமில்லாத துல்லியம்.** 4.00%-ல இருந்து 4.03%-க்கு ஒரு rate நகர்வு percent-ல "0.03%" — சொல்ல அசௌகரியம், 3%-ன்னு தவறா படிக்க எளிது. "3 basis points" பார்த்தவுடனே தெளிவு, சந்தையில ஒவ்வொரு தொழில்முறை நபரும் அதை ஒரே மாதிரி படிக்கிறாங்க.
-   **வெவ்வேறு அடிப்படை எண்கள் முழுக்க ஒரு பொது அலகு.** 25bp நகர்வு — ஒரு rate அல்லது yield-க்கு 0.0025-ஐ சேர்ப்பது — தொடக்க rate 0.50% ஆனாலும் 12.00% ஆனாலும் ஒரே *பொருள்*. அடிப்படை rate தானே சிறியதா இருக்கும்போது percent-of-percent ஒப்பீடுகள் ("கால் சதவீதம்", "அரை சதவீதம்") வேகமா குழப்பமாகிடும்; bps அதை rate மாற்றத்தின் ஒரு முழுமையான, ஒப்பீட்டு இல்லாத அலகா இருப்பதன் மூலம் முழுசா தவிர்க்குது.

**Takeaway:** இந்த module-ல ஒவ்வொரு worked example-உம் — Fixed Income, Rates, Credit, Equity, FX — ஒரே கணித உண்மைக்கு குறுகுது: **bps × 0.0001 × exposure = dollar தாக்கம்**, ஒவ்வொரு தடவையும் வேறு "exposure" (DV01, Spread DV01, notional, fee base) பொருத்தப்பட்டு. Section 2 ஒவ்வொரு asset class-உம் bps-ஐ எங்க பொருத்துதுன்னு சரியா map பண்ணுது.

### கூடுதல் உதாரணம் — "25 bps" vs "25%" குழப்பம்

| கூற்று | அர்த்தம் |
|---|---|
| Rate 4.00% → 4.25% | **+25 bps** (கூட்டு) |
| Rate 4.00% → 5.00% | **+100 bps** (ஒரு முழு சதவீத புள்ளி) |
| Rate 4.00% → 4.25%, "6.25% உயர்வு" | 4.25 ÷ 4.00 − 1 = 6.25% — இது *சார்பு* மாற்றம்; சந்தை இதை பயன்படுத்தாது, bps தான் |

---

## 2. Asset Classes முழுக்க Basis Points — எங்கெல்லாம் தோன்றுது

**Definition:** bps ஒரு Fixed-Income-மட்டுமே கருத்து இல்ல — இந்த curriculum-ஓட roadmap உள்ளடக்கிய ஒவ்வொரு asset class-உம் எங்கயாவது அவற்றைப் பயன்படுத்துது, ஆனா உண்மையிலயே வேறுபட்ட நோக்கங்களுக்கு. ஒரு குறிப்பிட்ட asset class-க்கு *எந்த* பயன்பாடு நடக்குதுன்னு குழப்பிக்கறது reporting-system தவறுகளுக்கு ஒரு பொதுவான மூலம்.

| Asset class | bps எங்க தோன்றுது | ஒரு bp-க்கு என்ன நகருது | இந்த module-ஓட உதாரணம் |
|---|---|---|---|
| **Fixed Income** | Bond **yield** (YTM), விலை உணர்திறன் | DV01 வழியா விலை (Section 3) | Section 3 |
| **Rates (IRS/derivatives)** | Floating/fixed **rate** தானே, swap valuation | Swap DV01 வழியா swap MTM (Section 4) | Section 4 |
| **Credit** | CDS **spread**, benchmark-க்கு மேல bond credit spread | Spread DV01 வழியா protection மதிப்பு (Section 5) | Section 5 |
| **Equity** | Management/advisory **fees**, bid-ask **spreads**, index-நிலை நகர்வுகள் புள்ளிகள்ல (பொதுவா bps இல்ல) | Fee செலவு, execution செலவு (Section 6) | Section 6 |
| **FX** | அரிதாவே நேரடியா — FX நகர்வுகள் **pips**-ல quote பண்ணப்படுது, தொடர்புடைய ஆனா வேறுபட்ட அலகு (Section 7) | Pip அடிப்படையில exchange rate | Section 7 |
| **Commodities** | நேரடியா குறைவா; futures tick sizes பொதுவா bps இல்ல, விலை அலகுகள்ல quote பண்ணப்படுது | — | இங்க தனியா வேலை செய்யலை — tick-size வழக்கங்களுக்கு [Module 7](../docs/1-capital-markets/module-07-commodities.html) பாருங்க |

**Takeaway:** ஒருங்கிணைக்கும் ஒற்றை நூல் — **bps ஒரு rate, yield, spread, அல்லது fee-ஐ அளக்குது** — ஒரு raw விலையை ஒருபோதும் இல்ல. Fixed Income, Rates ரெண்டும் bps-ஐ பயன்படுத்துது, ஏன்னா அவற்றோட முக்கிய quote செய்யப்படற எண் (yield, swap rate) ஏற்கனவே ஒரு சதவீதம்; Credit பயன்படுத்துது, ஏன்னா ஒரு CDS spread தானே ஒரு rate-ஆ quote பண்ணப்படுது; Equity-ஓட பயன்பாடு குறுகியது (fees, trading costs, stock விலை தானே இல்ல); FX பெரும்பாலும் pips-க்கு ஆதரவா விலகுது (Section 7 ஏன்னு விளக்குது). ஒரு raw equity விலை நகர்வை "150bps உயர்வு"ன்னு tag பண்ற trade அல்லது reference-data system கிட்டத்தட்ட கண்டிப்பா அந்தச் சொல்லை துல்லியமா இல்ல, தளர்வா பயன்படுத்துது — Section 6 அந்தக் கோட்டை வெளிப்படையா வரையுது.

---

## 3. Worked Example — Fixed Income: DV01 & Yield நகர்விலிருந்து விலை தாக்கம்

**Definition:** [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §4a-ஓட Canada 5% Bond 2035 position-ஐயும் [Module 10](../docs/1-capital-markets/module-10-valuation.html) §3-ஓட முழு DV01 கணக்கையும் முழுசா மறுபயன்படுத்தி — curriculum-ல bps-ல இருந்து dollars-க்கு மிகத் தூய்மையான, மிக நேரடியான மொழிபெயர்ப்பு, ஏன்னா ஒரு bond-ஓட விலையும் yield-உம் ஒரு basis point-ஆ, ஒண்ணோடொண்ணு சேர்ந்து நகருது.

```text
DV01 — CANADA 5% BOND 2035  (Module 5 §4a position, Module 10 §3 math)
========================================================================

  Modified Duration: 7.2 | Market Value: CAD 9,850,000

  DV01 = Modified Duration x Market Value x 0.0001
       = 7.2 x 9,850,000 x 0.0001
       = CAD 7,092 per basis point

  SCENARIO A -- yields RISE 25 basis points (4.75% -> 5.00% YTM area):
    Approx. Price Impact = -DV01 x Basis Points Moved
                          = -7,092 x 25
                          = -CAD 177,300  (bond LOSES value)

  SCENARIO B -- yields FALL 40 basis points:
    Approx. Price Impact = +7,092 x 40 = +CAD 283,680 (bond GAINS value)

  -> Bond prices and yields move INVERSELY (Module 1 §1) -- DV01
     is symmetric in size but the SIGN flips depending on direction.
     A reporting system computing DV01 once and reusing the same
     sign for both a rate rise and a rate fall will get one of the
     two scenarios backwards.
```

**YTM தானே ஏன் basis point வரை quote பண்ணப்படுது.** ஒரு bond-ஓட Yield to Maturity ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1, [Module 10](../docs/1-capital-markets/module-10-valuation.html) §3) வழக்கமா ஒரு சதவீதமா ரெண்டு தசம இடங்கள் வரை quote பண்ணப்படுது — இந்த bond-ஓட சொந்த YTM, 5.21% ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §3), சரியா "521 basis points" என்பதை வேறுவிதமா சொன்னது. அடுத்தடுத்து வர்ற ஒவ்வொரு DV01 கணக்கீடும் அந்த YTM எண் ஒற்றை bps-ஆ நகரும்போது விலை தாக்கத்தை அளக்குது — அதனாலதான் DV01 "சதவீதத்துக்கு" இல்லாம "basis point-க்கு" வரையறுக்கப்படுது; ஒரு முழு சதவீதப் புள்ளி yield நகர்வு ஒரு பெரிய, ஒப்பீட்டளவுல அரிய நிகழ்வு; ஒற்றை-basis-point ticks தான் bond சந்தை உண்மையில trade பண்ற அன்றாட அலகு.

**Takeaway:** DV01 (Dollar Value of 01, அதாவது 0.01% = 1bp) Fixed Income asset class-ஓட முக்கிய bps-இலிருந்து-dollars மாற்றி; curriculum-ஓட மூன்று மையக் கேள்விகள்ல ரெண்டுக்கு நேரடியா பொருந்துது: **valuation** (இந்த bond இப்போ எவ்வளவு மதிப்புள்ளது, இன்னிக்கு YTM-ல இருந்து) **MTM** (நேத்துல இருந்து அந்த மதிப்பு எவ்வளவு மாறுச்சு, bps நகர்ந்தது × DV01 ஆ). Section 9 இதே சரியான mechanic — "ஒரு basis point-க்கு" ஒரு உணர்திறன் அளவு — இந்த module-ல மற்ற ஒவ்வொரு asset class முழுக்க பொதுமைப்படுத்துது.

---

## 4. Worked Example — Rates: Swap DV01 & Interest Rate Swap-ல MTM

**Definition:** [Module 8](../docs/1-capital-markets/module-08-rates.html) §4a-ஓட 5Y CAD IRS-ஐயும் (Bank Of Toronto fixed 4.00% கொடுக்குது, floating CORRA பெறுது, Notional CAD 100,000,000) [Module 10](../docs/1-capital-markets/module-10-valuation.html) §4a-ஓட MTM கணக்கையும் மறுபயன்படுத்தி — Section 3-ஓட அதே DV01 கருத்து, ஒரு bond-ஓட cash flow அட்டவணைக்குப் பதிலா ஒரு swap-ஓட அட்டவணையிலிருந்து கணக்கிடப்படுது.

```text
SWAP DV01 & MTM — 5Y CAD IRS  (Module 8 §4a trade, Module 10 §4a math)
========================================================================

  Trade ID BOT-IRS-2026-034410
  Bank Of Toronto: pays FIXED 4.00%, receives FLOATING (CORRA)
  Notional (reference only, never exchanged): CAD 100,000,000

  Swap DV01 (approx., 4.5Y remaining): ~CAD 41,000 per basis point

  RATE MOVES from 4.00% to 4.30% (a 30bp RISE in the swap curve):
    Approx. MTM Gain to Bank Of Toronto = 41,000 x 30
                                         = CAD 1,230,000

  -> Bank Of Toronto pays a FIXED rate that is now BELOW the market
     rate for an equivalent new swap -- its existing position is
     worth MORE than it was, because it locked in a now-cheap fixed
     rate. The fixed-rate PAYER gains when rates RISE; the fixed-rate
     RECEIVER (the counterparty) loses the same CAD 1,230,000.
```

**ஏன் அதே DV01 கருத்து, ஆனா வேறு எண்.** Section 3-ஓட bond DV01 (CAD 7,092/bp), இந்த swap-ஓட DV01 (CAD 41,000/bp) தனித்து ஒப்பிடக்கூடியவை இல்ல — swap-ஓட Notional bond-ஓட Market Value-வை விட சுமார் 10 மடங்கு, ஆனா அதன் DV01 சுமார் 5.8 மடங்கு மட்டும், ஏன்னா ஒரு swap-ஓட DV01 Notional-ஐ மட்டுமில்ல, அதன் மீதி cash flow அட்டவணையின் வடிவத்தையும் (4.5 வருஷ quarterly resets) சார்ந்தது. பாடம்: **DV01 ஒருபோதும் Notional/Market Value-ல இருந்து கட்டைவிரல் விதியால ஊகிக்கப்படுவதில்ல** — அது எப்பவும் ஒரு முழு மறுமதிப்பீட்டின் (அல்லது அதன் ஆவணப்படுத்தப்பட்ட தோராயத்தின்) வெளியீடு, சரியா [Module 10](../docs/1-capital-markets/module-10-valuation.html) §4a-ஓட DV01 shortcut vs. ஒரு முழு curve-சார்ந்த DCF பத்தின சொந்த இறுதி எச்சரிக்கை.

**Takeaway:** ஒரு swap-ஓட MTM நடைமுறைல கிட்டத்தட்ட எப்பவும் முழு மறுமதிப்பீடுகளுக்கு இடையில "நகர்ந்த bps × Swap DV01"ஆ report பண்ணப்படுது — Section 3-ஓட bond-ஓட அதே கணித வடிவம், ஒவ்வொரு asset class-உம் அதே `bps × 0.0001 × exposure` formula-வுக்கு குறுகுதுங்கற Section 1-ஓட கூற்றை உறுதிப்படுத்துது; bond DV01-க்கு பதிலா Swap DV01 நிக்குது.

---

## 5. Worked Example — Credit: Basis Points-ல CDS Spread & Spread DV01

**Definition:** [Module 9](../docs/1-capital-markets/module-09-credit.html) §4a-b-ஓட Northern Rail Corp / Northstar Resources Inc. மேல 5Y CDS-ஐயும் (விளக்க reference entities modules இடையில சிறிது வேறுபடுது — ஒவ்வொரு module-ஓட சொந்த cast-ஐ பாருங்க) [Module 10](../docs/1-capital-markets/module-10-valuation.html) §4c-ஓட spread-இயக்கப்படற MTM கணக்கையும் மறுபயன்படுத்தி — bps ஒரு benchmark rate-ஐ இல்லாம, *risk premium தானே* quote பண்ற asset class.

```text
CDS SPREAD IN BPS — 5Y CDS, BANK OF TORONTO (PROTECTION BUYER) vs.
GOLDMAN SACHS (PROTECTION SELLER)  (Module 9 §4a-b, Module 10 §4c)
========================================================================

  AT INCEPTION:
    Notional: USD 10,000,000
    Running Coupon: 100 bps (the CONTRACTUAL premium Bank Of Toronto
                    pays quarterly, fixed for the trade's life)
    Market Spread at inception: 250 bps (what the market judged the
                    reference entity's ACTUAL credit risk to be worth)

    Because the contractual coupon (100bps) is below the market
    spread (250bps) at trade date, Bank Of Toronto pays an upfront:
      Upfront ~ (2.50% - 1.00%) x ~4.5 (Risky Duration) x 10,000,000
              = USD 675,000

  ONE YEAR LATER -- credit deteriorates, market spread WIDENS to
  400 bps. Remaining Risky Duration ~ 4.0:

    Current Approx. Value = (Market Spread - Running Coupon)
                              x Risky Duration x Notional
                           = (4.00% - 1.00%) x 4.0 x 10,000,000
                           = USD 1,200,000

    Unrealized Gain to Bank Of Toronto (protection buyer)
      = 1,200,000 - 675,000 = USD 525,000

  -> "Spread DV01" (or "CS01," Credit Spread DV01) is the Section 3/4
     DV01 concept applied to a 1bp move in the CREDIT SPREAD instead
     of a benchmark rate -- Risky Duration x Notional x 0.0001 gives
     the dollar value of a 1bp spread move, exactly parallel to bond
     Modified Duration x Market Value x 0.0001.
```

**ஏன் credit spreads bps-ல நகருது, ஆனா rates-ஐ விட இவ்வளவு வேகமா/அகலமா.** ஒரு benchmark rate (Section 4) பொதுவா திட்டமிடப்பட்ட central bank கூட்டங்கள்ல (Section 8) 25bp அதிகரிப்புகள்ல நகருது. ஒரு credit spread-க்கு அப்படி அட்டவணை இல்ல — company-குறிப்பான செய்தி (ஒரு earnings தவறு, ஒரு ratings downgrade, ஒரு covenant மீறல்) மேல சில நாட்கள்ல பத்துகள் அல்லது நூறுகள் bps அகலலாம் அல்லது குறுகலாம், ஏன்னா அது முழு சந்தைக்கும் பகிரப்பட்ட ஒரு policy rate-ஐ இல்ல, ஒரே entity-ஓட default risk-ஐ விலையிடுது. அதனாலதான் Credit desks, Rates desks benchmark-rate நகர்வுகளை கவனிப்பதை விட spread நகர்வுகளை மிகத் தொடர்ந்து கவனிக்குது — risk factor தானே இயல்பாவே அதிக இரைச்சல் கொண்டது.

**Takeaway:** ஒரு CDS-ஓட Running Coupon inception-ல நிர்ணயிக்கப்பட்டு trade-ஓட வாழ்நாள் முழுக்க மாறாது; அதன் *சந்தை மதிப்பு* முழுக்க மாறுது, ஏன்னா *சந்தை spread* — bps-ல quote பண்ணப்பட்டு, தொடர்ந்து நகர்ந்து — அந்த நிலையான coupon-ல இருந்து விலகுது. இது Section 3-உம் Section 4-உம் பயன்படுத்தற அதே "trade date-ல lock பண்ணினது vs. சந்தை இன்னிக்கு வழங்குவது" mechanic, bond/swap DV01-க்கு பதிலா Spread DV01 நிக்குது, சரியா [Module 10](../docs/1-capital-markets/module-10-valuation.html) §4c-ஓட சொந்த takeaway ஏற்கனவே பொதுமைப்படுத்தினபடி.

---

## 6. Worked Example — Equity: Fees & Trading Costs-ல Basis Points (Stock Price-ல இல்ல)

**Definition:** asset-class தொகுப்பை நிறைவு செய்யும் புது worked example — Equity தான் bps Fixed Income, Rates, Credit-ல போல ஒரு raw விலைக்கு தானாக பொருந்தாதுங்கறதற்கு மிகத் தெளிவான விளக்கம். Equities-ல bps உண்மையிலயே தோன்றுவது **செலவு**: management fees, bid-ask spreads — ரெண்டும் [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட நிறுவப்பட்ட Maple Leaf Pension Fund positions-ஐ அடிப்படையா கொண்டவை.

```text
MANAGEMENT FEE IN BASIS POINTS — MAPLE LEAF PENSION FUND'S EQUITY
ALLOCATION  (illustrative, built on Module 4's own KO/AAPL positions)
========================================================================

  Maple Leaf Pension Fund's total equity allocation under management:
  CAD 500,000,000 (illustrative, aggregate across many positions
  including the 10,000-share KO holding from Module 4 §4a)

  Annual Management Fee: 45 basis points (0.45%) of assets under
  management -- the standard way an asset manager, index fund, or
  ETF quotes its fee (an MER, "Management Expense Ratio," is the
  fund-industry name for exactly this bps figure)

  Annual Fee Cost = 500,000,000 x 0.0045 = CAD 2,250,000

  -> Note this is the SAME "bps x 0.0001 x exposure" formula as every
     other section -- here "exposure" is Assets Under Management, not
     a DV01-style sensitivity, and the bps figure is a FEE RATE, not
     a market-observed yield or spread.
```

```text
BID-ASK SPREAD IN BASIS POINTS — AAPL, A LIQUID LARGE-CAP STOCK
========================================================================

  AAPL Bid: USD 259.98 | Ask: USD 260.02
  Absolute Spread = 260.02 - 259.98 = USD 0.04

  Spread in bps = (Absolute Spread / Mid Price) x 10,000
                = (0.04 / 260.00) x 10,000
                = 1.5 bps

  -> Quoting the spread in bps (not just cents) lets a trading desk
     compare execution cost ACROSS stocks with wildly different price
     levels on a common scale -- 1.5bps on AAPL at USD 260 is a
     TIGHTER (cheaper to trade) market than 1.5bps would represent on
     a USD 5 penny stock, in absolute cents, but the BPS figure itself
     is directly comparable across both.
```

**ஏன் stock விலை தானே பொதுவா bps-ல quote பண்ணப்படுறதில்ல.** AAPL USD 260.00-ல இருந்து USD 262.60-க்கு நகருவது 1.00% நகர்வு — சந்தை commentary இதை "ஒரு சதவீதம் உயர்வு"ன்னோ ("260 புள்ளிகள்" ஒரு index-க்கு) "100bps உயர்வு"ன்னு சொல்றதை விட மிக அடிக்கடி சொல்லுது, கணிதம் ஒரே மாதிரி இருந்தாலும். இது வழக்கம், கடினமான விதி இல்ல — சில equity derivatives desks (உதா., equity-linked note fees, dividend-swap spreads quote பண்ணும்போது) equity எண்களுக்கு bps பயன்படுத்துது — ஆனா ஒரு raw equity விலை நகர்வின் *default* அலகு percent அல்லது index புள்ளிகள், bps இல்ல. Section 3–5-ஓட இதை நேரடியா ஒப்பிடுங்க: ஒரு bond yield, ஒரு swap rate, ஒரு CDS spread எல்லாம் எந்த நகர்வும் நடக்கறதுக்கு முன்னாடியே **ஏற்கனவே** சதவீத-வடிவ எண்கள், அதனாலதான் bps அவற்றோட இயல்பா இணையுது; ஒரு stock விலை ஒரு dollar எண், ஒரு rate இல்ல, அதனால bps (மேல உள்ள fee, spread உதாரணங்கள்ல போல) default-ஆ இல்லாம, ஒரு வேணும்னே எடுத்த தேர்வு.

**Takeaway:** Equity தான் இந்த module-ல "bps ஒரு rate/yield/spread/fee-ஐ விவரிக்குது" (Sections 3–5, இந்த section-ஓட fee/spread உதாரணங்களும்) vs. "bps ஒரு raw விலையை தானாக விவரிக்காது" என்பதுக்கு இடையில மிகக் கூர்மையான கோட்டை வரையும் asset class — ஒரு reporting அல்லது analytics system சரியாப் பெற வேண்டிய வேறுபாடு, ஏன்னா bps-ல *எது* அளக்கப்படுதுன்னு தகுதிப்படுத்தாம ஒரு 1% equity விலை நகர்வை "100bps"ன்னு தவறா label பண்றது, இந்த module-ல மற்ற ஒவ்வொரு asset class-உம் உண்மையில bps-ஐ பயன்படுத்தும் yield-, spread-சார்ந்த உணர்திறன்களோட குழப்பத்தை அழைக்குது.

### கூடுதல் உதாரணம் — Management fee-ஐ ஐந்து வருஷத்துல compound பண்ணி பார்த்தா

CAD 500,000,000 portfolio, ஆண்டுக்கு 45 bps fee. Portfolio ஆண்டுக்கு 6% gross return தருதுன்னு (எளிமைக்கு) வெச்சுக்கோங்க:

| ஆண்டு | ஆரம்ப மதிப்பு | Gross return (6%) | Fee (0.45%) | முடிவு மதிப்பு (net) |
|---|---|---|---|---|
| 1 | 500,000,000 | +30,000,000 | -2,385,000 | 527,615,000 |
| 2 | 527,615,000 | +31,656,900 | -2,517,000 (தோராயம்) | ≈ 556,755,000 |
| 5-ஆம் ஆண்டு முடிவில் | | | 5 ஆண்டு மொத்த fees ≈ CAD 13.3 மில்லியன் | ≈ 654,200,000 |

சின்ன 45 bps ஐந்து வருஷத்துல தோராயமா CAD 13.3 மில்லியன் செலவு — அதனாலதான் fees-ஐ bps-ல துல்லியமா பார்க்கறாங்க.

---

## 7. Worked Example — FX: Basis Points vs. Pips — வேணும்னே வேறுபட்ட அலகு

**Definition:** [Module 6](../docs/1-capital-markets/module-06-fx.html) §4a-ஓட USD/CAD Spot trade-ஐ (Bank Of Toronto USD 10,000,000-ஐ 1.3500-ல வாங்குது) மறுபயன்படுத்தி, FX உண்மையில நகர்வுகளை quote பண்ணும் அலகான **pips**-ஐ அறிமுகப்படுத்துது — bps-உடன் தொடர்புடையது, ஆனா அதே விஷயம் இல்ல, ஒவ்வொரு asset class-உம் bps-ஐ ஒரே மாதிரி பயன்படுத்துதுன்னு நினைக்கற யாருக்கும் ஒரு உண்மையான குழப்ப மூலம்.

```text
PIPS vs. BASIS POINTS — USD/CAD  (Module 6 §4a spot trade)
========================================================================

  USD/CAD Spot Rate: 1.3500 (quoted to 4 decimal places)
  1 PIP = the smallest standard quoted move = 0.0001 -- i.e. the LAST
          decimal place of the quoted rate

  Rate moves from 1.3500 to 1.3520 -- a move of 0.0020
    = 20 PIPS (the FX-market-standard way to describe this move)

  IS THIS "20 BASIS POINTS"? -- Only by COINCIDENCE for this pair,
  and only approximately:
    20 pips / 1.3500 x 10,000 = 14.8 bps of the RATE ITSELF

  -> A pip is a FIXED decimal increment (0.0001 for most pairs); a
     basis point is 0.01% of whatever number it's attached to. For
     USD/CAD near 1.3500, a 1-pip move happens to be close to a
     1bp-of-rate move only because the rate itself is close to 1.00 --
     for a pair trading well above or below 1.00, the two diverge
     more visibly (a JPY pair like USD/JPY quotes to 2 decimals, so
     1 pip = 0.01, a much bigger fraction of a ~150.00 rate).
```

**ஏன் FX வெறுமனே bps-ஐ பயன்படுத்தறதில்ல.** ஒரு bond yield அல்லது swap rate போல இல்லாம, ஒரு FX spot rate தானா ஒரு சதவீதம் இல்ல — அது ரெண்டு currencies-ஓட மதிப்புகளின் ஒரு விகிதம், ஜோடிக்கு ஜோடி மாறுபடும் ஒரு தசம வழக்கத்தோட (பெரும்பாலான ஜோடிகளுக்கு 4 தசமங்கள், JPY crosses-க்கு 2). Pips FX traders-க்கு அந்த tick எந்த ஜோடிக்கும் எந்த சதவீதத்தை குறிக்குதுங்கறதைச் சாராத, **நிலையான, ஜோடிக்கு-குறிப்பான tick அளவை** quote பண்ண தருது — bps-க்கு சரியா எதிரான வடிவமைப்புத் தேர்வு, ஏன்னா அது வரையறைப்படி *எப்பவும்* சதவீத-சார்பு அலகு. FX உண்மையிலயே bps-ஐ நேரடியா பயன்படுத்தும் இடம் **FX Forward points, cross-currency basis** ([Module 6](../docs/1-capital-markets/module-06-fx.html) §4b-ஓட forward points, [Cross-Currency Swap உதாரணத்தின்](../examples/Cross_Currency_Swap_Complete_Example.html) basis spread) — ரெண்டும் spot-rate ticks இல்ல, rate-வடிவ எண்கள், சரியா Section 6 ஏற்கனவே நிறுவிய pattern: bps rates, spreads, fees-உடன் இணையுது, raw விலை/exchange-rate நிலைகளோடு இல்ல.

**Takeaway:** "pips"-ஐயும் "bps"-ஐயும் ஒண்ணுக்கொண்ணு மாத்தி பயன்படுத்தறது ஒரு பொதுவான, USD/CAD-க்கு பெரும்பாலும் தீங்கற்ற எளிமைப்படுத்தல், ஆனா மற்ற ஜோடிகளுக்கு தெளிவா உடையுது — நல்லா வடிவமைக்கப்பட்ட FX risk அல்லது reporting system ஒருபோதும் ஒண்ணை மற்றதுக்கு அமைதியா மாத்தக்கூடாது; spot/forward rate நகர்வுகளுக்கு pips-ஐ இயல்பா track பண்ணி, ஒரு "rate-ஓட bps" எண் உண்மையிலயே கேட்கப்படும்போது மட்டும் (மேல உள்ள cross-currency basis case போல) அதை வெளிப்படையா, தனியா கணக்கிடணும்.

---

## 8. Basis Points உண்மையில எவ்வளவு அடிக்கடி மாறுது? — திட்டமிடப்பட்ட Policy நகர்வுகள் vs. தொடர்ச்சியான சந்தை விலையிடல்

**Definition:** "basis points" ஒரு *அலகை* விவரிக்குது, *அதிர்வெண்ணை* இல்ல — ஆனா bps-ல அளக்கப்படும் விஷயங்கள் எது அப்படிங்கறதை பொறுத்து மிக வேறுபட்ட கடிகாரங்கள்ல மாறுது; ஒரு "rate" உண்மையில எவ்வளவு அடிக்கடி நகருதுன்னு புதுசா வர்றவருக்கு அவற்றைக் குழப்பிக்கறது ஒரு பொதுவான குழப்ப மூலம்.

| What moves | Typical frequency | Typical size per move | Example from this module |
|---|---|---|---|
| **Central bank policy rate** (the rate a benchmark like CORRA/SOFR ultimately tracks) | Scheduled meetings — roughly 8/year for the US Fed, Bank of Canada, ECB, BoE | 25bp increments are the historical norm; 50bp or 75bp moves happen in faster-moving cycles; 0bp ("hold") is common too | Section 4's swap curve moving 4.00% → 4.30% would typically reflect several such moves priced in advance, not one single meeting |
| **Bond yields / YTM** | Continuously, every trading session (often intraday, tick by tick) | Single-digit bps on a quiet day; tens of bps on a volatile one | Section 3's 25bp and 40bp scenarios |
| **Swap rates** | Continuously, tracking the market's evolving expectation of the future path of the policy rate | Similar to bond yields — continuous, small moves most days | Section 4 |
| **Credit spreads** | Continuously, but with sharp company-specific jumps on news (much noisier than rates) | Can move 100+ bps in a single day on a ratings action or earnings shock | Section 5's 250bp → 400bp move over a year |
| **Management fees** | Rarely — set contractually, revisited annually or at fund-restructuring events, not market-driven | N/A — not a market price at all | Section 6 |
| **Bid-ask spreads** | Continuously, widening/narrowing with liquidity conditions minute to minute | Can double or triple in stressed markets even with no price move at all | Section 6 |
| **Collateral / margin haircuts** ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.md)) | Reviewed periodically by CCPs/counterparties, not continuously | Adjustments often expressed and communicated in bps or percentage points | See [Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.md) for haircut fields |

```text
TWO GENUINELY DIFFERENT "HOW OFTEN" QUESTIONS
========================================================================

  "How often does the POLICY RATE change?"
    -> Roughly 8 scheduled opportunities a year per major central
       bank, and it can also simply NOT move at any given meeting.
       A policy rate is a DECISION, not a continuously observed price.

  "How often does a bond's YIELD / a swap's RATE / a CDS's SPREAD
  change?"
    -> Continuously, every time the market trades -- because these
       are OBSERVED MARKET PRICES reacting to everything (including,
       but not limited to, expectations about the next policy
       decision), not administrative decisions themselves.
```

**Takeaway:** ஆண்டுக்கு எட்டு தடவை 25bps மாறும் policy rate-உம், ஒரு trading session முழுக்க ஒவ்வொரு சில நொடிக்கும் ஒரு basis point-ஓட பின்னங்களால நகரும் bond-ஓட YTM-உம் **ரெண்டும்** "basis point நகர்வுகள்", ஆனா அவை முற்றிலும் வேறு கேள்விகளுக்கு பதில் சொல்லுது — ஒண்ணு திட்டமிடப்பட்ட நிர்வாக முடிவு, மற்றது முதல்தோடு தொடர்புடைய எதிர்பார்ப்புகளால *பகுதியா, ஆனா முழுசா இல்ல* இயக்கப்படும் தொடர்ச்சியான சந்தை விலை கண்டறிதல். ஒரு risk system-ஓட "இந்த rate எவ்வளவு பழசு" சோதனை, தன்கிட்ட இருக்கற bps எண் எந்த வகைன்னு தெரிஞ்சிருக்கணும்.

---

## 9. Valuation, MTM & YTM மேல Basis Points-ஓட தாக்கம் — Mechanism-ஐ ஒண்ணா இணைக்கறது

**Definition:** இந்த module தொடர்ந்து சுத்திவரும் கேள்வி — ஒரு basis-point நகர்வு உண்மையிலயே ஒரு position எவ்வளவு மதிப்புள்ளது, அதன் MTM என்ன காட்டுது, அதன் YTM என்ன படிக்குதுன்னு மாத்துதா? Sections 3–5 முழுக்க ஒருங்கிணைக்கப்பட்ட பதில்: **ஆமா, நேரடியாவும் இயந்திரத்தனமாவும், asset classes முழுக்க கட்டமைப்பு ரீதியா ஒரே மாதிரியான "ஒரு-basis-point உணர்திறன்" அளவு வழியா.**

| கருத்து | எதுக்கு பதில் | bps mechanism | எங்க வேலை செய்யப்பட்டது |
|---|---|---|---|
| **Valuation** | இந்த position இப்போ எவ்வளவு மதிப்புள்ளது? | தற்போதைய மதிப்பு = f(தற்போதைய YTM / swap rate / spread) — இன்னிக்கு curve/spread-ல இருந்து ஒரு முழு DCF அல்லது model மறுமதிப்பீடு, basis point வரை வெளிப்படுத்தப்பட்டது | [Module 10](../docs/1-capital-markets/module-10-valuation.html) §3–4; இங்க Sections 3–5 |
| **MTM** (Mark-to-Market) | கடைசி valuation-ல இருந்து அந்த மதிப்பு எவ்வளவு மாறியது? | MTM மாற்றம் ≈ நகர்ந்த Basis Points × [DV01 / Swap DV01 / Spread DV01] — முழு மறுமதிப்பீடுகளுக்கு இடையில பயன்படுத்தப்படும் *shortcut* | இங்க Sections 3–5 |
| **YTM** (Yield to Maturity) | இந்த bond-ஐ maturity வரை வெச்சிருந்தா உண்மையில நான் எவ்வளவு சம்பாதிக்கிறேன்? | YTM தானே basis point வரை quote பண்ணப்படுது (Section 3-ஓட bond: "5.21%" = 521bps); சந்தை YTM-ஓட ஒரு *மாற்றம்* தான் DV01 அளக்கும் விலை தாக்கத்தை இயக்குது | [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1; இங்க Section 3 |

```text
THE SAME SHAPE, THREE TIMES OVER
========================================================================

  FIXED INCOME:  Price Impact  = -DV01        x Basis Points Moved
  RATES (SWAP):  MTM Change    =  Swap DV01   x Basis Points Moved
  CREDIT (CDS):  Value Change  =  Spread DV01 x Basis Points Moved

  -> Every one of these is "exposure's per-basis-point sensitivity,
     times how many basis points actually moved." Only the sign
     convention (Fixed Income price FALLS as yield RISES; a CDS
     protection buyer's value RISES as spread RISES) and which
     sensitivity measure applies differ by asset class -- the
     arithmetic shape is identical, and it's the same shape
     Section 1 introduced as this module's central formula.
```

**ஒரு முக்கிய நுணுக்கம் — YTM மாறுது என்றா coupon மாறிடுச்சுன்னு அர்த்தமில்ல.** [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1 ஏற்கனவே bond-ஓட Coupon Rate issuance-ல நிர்ணயிக்கப்பட்டதுன்னு நிறுவியது; YTM முழுக்க bond-ஓட *சந்தை Price* அந்த நிலையான coupon-ஓட ஒப்பிட நகருவதால (அல்லது அதற்கு சமமா, அந்த நிலையான coupon stream-க்கு ஒரு வாங்குபவர் கொடுக்கத் தயாரா இருக்கற விலை மாறியதால) நகருது. Section 3-ல 25bp YTM உயர்வு, *சந்தை* அதே நிலையான CAD 50/ஆண்டு coupon stream-ஐ குறைந்த தாராளமான விலையில மறுவிலையிடுவதைப் பிரதிபலிக்குது — YTM ஒரு derived, சந்தை-இயக்கப்படும் எண், Price-ல இருந்து தொடர்ந்து மறுகணக்கிடப்படுது, ஒரு Coupon Rate போல "நிர்ணயிக்கப்படும்" ஒப்பந்த term ஒருபோதும் இல்ல.

**Takeaway:** basis points valuation, MTM, YTM-க்கு இணைக்கப்பட்ட ஒரு பக்க விவரம் இல்ல — அவை இந்த மூன்று கருத்துகளும் **அளக்கப்பட்டு நகர்த்தப்படும் அலகு**, DV01-குடும்ப உணர்திறன்கள் (Sections 3–5) "சந்தை இத்தனை bps நகர்ந்தது"க்கும் "என் position-ஓட dollar மதிப்பு இத்தனை dollars மாறியது"க்கும் இடையிலான இயந்திரப் பாலம். இந்த mechanism-ஐ சரியாப் பெறுவதுதான் [Module 10](../docs/1-capital-markets/module-10-valuation.html) இந்த curriculum-ஓட மிக அதிக முன்னுரிமை module-ஆ இருக்கறதுக்கு சரியான காரணம்.

---

## 10. Regulatory, Collateral & Reporting Context-ல Basis Points

**Definition:** basis points ஒரு trading-desk வசதி மட்டுமில்ல — இந்த curriculum-ஓட reporting modules ஏற்கனவே கோரும் பல fields அடிப்படையில bps எண்கள், ஒரு reported field-ல அலகை தவறா பெறுவது ஒரு உண்மையான, திரும்பத் திரும்ப நடக்கும் operational தவறு.

| Context | Where bps appears | This curriculum's coverage |
|---|---|---|
| **CDS spread reporting** | The "Spread" field on an OTC derivative trade report (EMIR/Dodd-Frank), quoted in bps | [Module 9](../docs/1-capital-markets/module-09-credit.md), [Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §9 |
| **Interest rate benchmark resets** | CORRA/SOFR/€STR/SONIA fixings, and swap "spread over benchmark" legs, quoted in bps | [Module 8](../docs/1-capital-markets/module-08-rates.md) §1 |
| **Collateral/margin haircuts** | Haircut percentages on posted collateral, often communicated and adjusted in bps or percentage points | [Module 12](../docs/1-capital-markets/module-12-collateral-margin.md), [Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.md) |
| **Repo rates** | The repo rate itself, and the "repo spread" over a benchmark, quoted in bps | [the Repo/SFT worked example](../examples/Repo_Securities_Financing_Complete_Example.md) |
| **MiFID II / MiFIR price/yield fields** | Reported price and yield fields on Fixed Income and Rates transactions carry bps-level precision requirements | [Module 20 — MiFID Reporting](../docs/2-reporting/module-20-mifid-reporting.md) |

> **Callout — ஒரு உண்மையான operational தோல்வி முறை.** 523 basis points (5.23%) என்று பொருள்படும் "523"-ஐ வைத்திருக்கணும்ங்கற ஒரு field, தவறுதலா "5.23"-ஆ நிரப்பப்பட்டு (downstream-ல 5.23 basis points, அதாவது 0.0523%ன்னு விளங்கப்படுவது) ஒரு **இரண்டு-அளவு-வரிசை** தவறு — இந்த curriculum-ஓட reporting modules ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html), [Module 20](../docs/2-reporting/module-20-mifid-reporting.html)) ஒரு தொடர்ச்சியான நிஜ உலக data-quality பிரச்சனையா flag பண்ணும் சரியான வகை unit-of-measure bug. ஒரு reporting system-ஓட field-நிலை ஆவணப்படுத்தல், ஒரு எண் field "basis points-ல", "percent-ல", அல்லது "decimal-ல"ன்னு எப்பவும் வெளிப்படையா சொல்லணும் — மறைமுகமாக விடாதீங்க.

**Takeaway:** basis points நேரடியான reported fields-ஆ தோன்றுது, வெறும் desk சுருக்கம் மட்டுமில்ல — அதாவது Section 1 தொடங்கின unit-conversion ஒழுக்கம் (bps எப்பவும் கூட்டு, எப்பவும் decimal-க்கு ×0.0001) வெறும் மனக்கணக்குக்கு பயனுள்ளது மட்டுமில்ல, rate-, spread-, அல்லது yield-denominated fields-ஐத் தொடும் எந்த trade capture அல்லது reporting system-லயும் ஒரு உண்மையான data-quality control புள்ளி.

---

## 11. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** இந்த curriculum-ஓட மற்ற reference modules போலவே, அவ்வப்போது திருத்தப்படும் technical விதிகளை மறுசொல்லாம, முதன்மை மூலங்களை சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **Bank of Canada** | CORRA and Canadian policy rate decisions, scheduled meeting dates | [www.bankofcanada.ca](https://www.bankofcanada.ca) |
| **Federal Reserve (FOMC)** | US policy rate (Fed Funds Rate) decisions and SOFR | [www.federalreserve.gov](https://www.federalreserve.gov) |
| **European Central Bank (ECB)** | Eurozone policy rate decisions and €STR | [www.ecb.europa.eu](https://www.ecb.europa.eu) |
| **Bank of England (BoE)** | UK policy rate (Bank Rate) decisions and SONIA | [www.bankofengland.co.uk](https://www.bankofengland.co.uk) |
| **ISDA** | Standard CDS documentation and running coupon/spread conventions ([ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.md)) | [www.isda.org](https://www.isda.org) |
| **ESMA** | MiFID II/MiFIR reporting field precision requirements | [www.esma.europa.eu](https://www.esma.europa.eu) |

**Takeaway:** ஒவ்வொரு central bank-ஐயும் திட்டமிடப்பட்ட policy-rate நகர்வுகளுக்கு (Section 8-ஓட "நிர்வாக முடிவு" கடிகாரம்) source of truth-ஆவும்; ISDA-ஐ ஒரு credit spread-உம் running coupon-உம் எப்படி ஆவணப்படுத்தப்படுதுங்கறதுக்கு source of truth-ஆவும்; ESMA/MiFID-ஐ ஒரு reported price/yield/spread field சுமக்க வேண்டிய துல்லியம் சரியா என்னன்னு source of truth-ஆவும் நடத்துங்க — இந்த curriculum-ஓட மற்ற reference modules தங்கள் சொந்த பகுதிகளுக்கு ஏற்கனவே பயன்படுத்தற அதே மூன்று-வழிப் பிரிவு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Canada 5% Bond 2035-ஐ வெச்சிருக்கு (Section 3); 5Y CAD IRS-ல fixed கொடுக்குது (Section 4); reference entity மேல CDS protection வாங்குது (Section 5); USD/CAD spot trade-ஐ execute பண்ணுது (Section 7) |
| Goldman Sachs | CDS protection seller (Section 5); FX spot counterparty (Section 7) |
| Maple Leaf Pension Fund | Management fee-க்கு உட்பட்ட equity allocation-ஐயும், bid-ask spread விளக்கத்துல பயன்படுத்தப்படும் KO/AAPL positions-ஐயும் வெச்சிருக்கு (Section 6) |
| Bank of Canada / Federal Reserve / ECB / Bank of England | CORRA/SOFR/€STR/SONIA-வையும், மறைமுகமா இந்த module-ஓட Rates உதாரணங்கள் reference பண்ணும் ஒவ்வொரு benchmark rate-ஐயும் நங்கூரமிடும் திட்டமிடப்பட்ட policy rates-ஐ நிர்ணயிக்குது (Section 8) |
| ISDA / ESMA | Spread, rate, yield fields எப்படி ஆவணப்படுத்தப்பட்டு report பண்ணப்படுதுங்கறதை நிர்வகிக்கும் ஆவணப்படுத்தல், reporting-துல்லிய standard-setters (Sections 10–11) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ரெண்டும் ஒரே அர்த்தம்னாலும், ஒரு rate நகர்வை "0.25%"-ஐ விட "25 basis points" என்று விவரிப்பது ஏன் இன்னும் துல்லியமா, குறைவா குழப்பமா?
2.  Section 2-ஐ பயன்படுத்தி, basis points instrument-ஓட சொந்த விலையை பொதுவா விவரிக்காத ஒரு asset class-ஐ பெயரிடுங்க; அங்க அவை அதுக்குப் பதிலா எதை அளக்குதுன்னு விளக்குங்க.
3.  Section 3-ஐ பயன்படுத்தி, DV01 dollar எண் தானே அப்படியே இருந்தாலும், yields உயர்ந்ததா, குறைந்ததாங்கறதை பொறுத்து DV01-சார்ந்த விலை தாக்கத்தின் குறி புரளுவது ஏன்?
4.  Section 4-ஐ பயன்படுத்தி, ஒரு swap-ஓட DV01-ஐ அதன் Notional-ஓட ஒரு எளிய பின்னமாக நம்பகமா மதிப்பிட முடியாதது ஏன்?
5.  Section 5-ஐ பயன்படுத்தி, credit spreads பொதுவா benchmark interest rates-ஐ விட வேகமாவும் பெரிய அளவிலும் நகர்றது ஏன்?
6.  Section 6-ஐ பயன்படுத்தி, AAPL-ஓட bid-ask spread-க்கு bps பொருந்துவதுக்கும், AAPL-ஓட raw share விலை நகர்வுக்கு (பொருந்தினா) பொருந்துவதுக்கும் உள்ள முக்கிய வேறுபாடு என்ன?
7.  Section 7-ஐ பயன்படுத்தி, USD/CAD-ல "20 pips" USD/CAD rate-ஓட "20 basis points"-உடன் சரியா ஒரே விஷயம் இல்லாதது ஏன்?
8.  Section 8-ஐ பயன்படுத்தி, "policy rate எவ்வளவு அடிக்கடி மாறுது"க்கும் "ஒரு bond-ஓட YTM எவ்வளவு அடிக்கடி மாறுது"க்கும் என்ன வித்தியாசம்? அவற்றுக்கு இவ்வளவு வேறுபட்ட பதில்கள் ஏன்?
9.  Section 9-ஐ பயன்படுத்தி, Fixed Income, Rates, Credit முழுக்க DV01, Swap DV01, Spread DV01-ஐ இணைக்கும் ஒற்றை formula வடிவம் என்ன?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. ஏன்னா "0.25%" ஒரு சதவீதப் புள்ளியின் கால் பங்கு நகர்வை விவரிக்குது; அதை "25%"ன்னு தவறா படிக்கவோ, அசல் மதிப்பின் ஒரு சதவீத-மாற்றத்தோட குழப்பிக்கவோ எளிது; "25 basis points" ஒரு முழுமையான, கூட்டு அலகு, தொடக்க rate எதுவா இருந்தாலும் ஒவ்வொரு சந்தைத் தொழில்முறை நபரும் ஒரே மாதிரி படிக்கிறாங்க.**

Section 1-ஓட மாற்று table-உம் "ஏன் வெறுமனே 0.25%ன்னு சொல்லக்கூடாது" விவாதமும் இதை நேரடியா சொல்லுது — bps கூட்டு, பெருக்கல் இல்ல, வெவ்வேறு அடிப்படை rates முழுக்க குழப்பமில்லாதது.

**2. Equity — அங்க basis points பொதுவா management/advisory fees (assets under management-ஓட சதவீதமா) அல்லது bid-ask trading spreads-ஐ அளக்குது; stock-ஓட சொந்த விலை நிலையை இல்ல, அது வழக்கமா percent அல்லது index புள்ளிகள்ல quote பண்ணப்படுது.**

Section 6-ஓட fee, bid-ask worked examples, Sections 3–5-ஓட rate/yield/spread-சார்ந்த பயன்பாடுகளோட அதன் வெளிப்படையான வேறுபாடு.

**3. ஏன்னா bond விலைகளும் yields-உம் எதிர்த்திசைல நகருது (Module 1 §1) — DV01 ஒரு basis point-க்கான விலை தாக்கத்தின் *அளவை* அளக்குது, அது திசையைச் சாராம ஒரே மாதிரி; ஆனா அந்த தாக்கம் லாபமா இழப்பாங்கறது yields உண்மையில எந்தத் திசையில நகர்ந்ததுங்கறதைப் பொறுத்தது.**

Section 3-ஓட Scenario A (yields உயர்வு, bond CAD 177,300 இழக்குது) vs. Scenario B (yields குறைவு, bond CAD 283,680 பெறுது) — அதே CAD 7,092/bp DV01, எதிர் குறிகள்.

**4. ஏன்னா ஒரு swap-ஓட DV01 அதன் Notional-ஐ மட்டுமில்ல, அதன் முழு cash flow அட்டவணையின் வடிவம், மீதி காலத்தையும் (இந்த case-ல 4.5 வருஷ quarterly resets) சார்ந்தது — Section 4-ஓட swap bond-ஓட Market Value-வை விட சுமார் 10 மடங்கு Notional கொண்டிருக்கு, ஆனா அதன் DV01-ல சுமார் 5.8 மடங்கு மட்டும், உறவு ஒரு நிலையான விகிதம் இல்லைன்னு காட்டுது.**

Section 4-ஓட "ஏன் அதே DV01 கருத்து, வேறு எண்" விவாதம், DV01 ஒரு மறுமதிப்பீட்டின் வெளியீடு, ஒரு கட்டைவிரல் விதியால Notional-ல இருந்து ஒருபோதும் ஊகிக்கப்படுவதில்லைங்கற Module 10 §4a-ஓட சொந்த எச்சரிக்கை.

**5. ஏன்னா ஒரு credit spread ஒரே entity-ஓட default risk-ஐ விலையிடுது; அதை company-குறிப்பான செய்தி (earnings, ratings actions, covenant மீறல்கள்) நிலையான அட்டவணை இல்லாம இயக்கலாம்; ஒரு benchmark rate ஆண்டுக்கு சுமார் 8 திட்டமிடப்பட்ட central bank கூட்டங்கள்ல மட்டுமே நகரும் policy rate-ல நங்கூரமிடப்பட்டிருக்கு.**

Section 5-ஓட "ஏன் credit spreads bps-ல நகருது, ஆனா rates-ஐ விட இவ்வளவு வேகமா/அகலமா" விவாதம், Section 8-ஓட table திட்டமிடப்பட்ட policy நகர்வுகளை தொடர்ச்சியான, சில சமயம் கூர்மையான, credit-spread நகர்வுகளோட ஒப்பிடறது.

**6. Bid-ask spread (trading செலவு) ஒரு உண்மையான, வேணும்னே எடுத்த bps எண் — absolute spread-ஐ mid price-ஆல வகுத்தது — வெவ்வேறு விலை நிலைகள் கொண்ட stocks முழுக்க trading costs-ஐ ஒப்பிட பயன்படுத்தப்படுது. AAPL-ஓட raw share விலை நகர்வு (உதா., 1% உயர்வு) வழக்கமா percent அல்லது dollars-ல விவரிக்கப்படுது, bps-ல இல்ல, அதே கணிதம் தொழில்நுட்ப ரீதியா அதற்கு ஒரு bps எண்ணை உருவாக்கலாம் என்றாலும்.**

Section 6-ஓட bid-ask spread worked example, அதன் "ஏன் stock விலை தானே பொதுவா bps-ல quote பண்ணப்படுறதில்ல" விவாதம்.

**7. ஏன்னா ஒரு pip ஒரு நிலையான தசம அதிகரிப்பு (USD/CAD உட்பட பெரும்பாலான ஜோடிகளுக்கு 0.0001), ஆனா ஒரு basis point எப்பவும் அது இணைக்கப்படற எந்த எண்ணின் 0.01%. 1.3500-க்கு அருகில உள்ள USD/CAD-க்கு ரெண்டும் எண் ரீதியா நெருக்கமா இருக்கலாம், ஆனா மிக வேறுபட்ட rate நிலைகள்ல quote பண்ணப்படும் ஜோடிகளுக்கு (உதா., USD/JPY, அங்க 1 pip = 0.01, ~150.00 rate-ல இருந்து மிகச் சிறிய சதவீதம்) அல்லது வேறு தசம வழக்கங்களுக்கு விலகுது.**

Section 7-ஓட worked example ரெண்டு எண்களையும் வெளிப்படையா கணக்கிடுது (அதே USD/CAD நகர்வுக்கு 20 pips vs. rate-ஓட 14.8bps), அதன் "ஏன் FX வெறுமனே bps-ஐ பயன்படுத்தறதில்ல" விவாதம் அடிப்படைக் காரணத்தை விளக்குது.

**8. ஒரு policy rate திட்டமிடப்பட்ட central bank கூட்டங்கள்ல (முக்கிய central banks-க்கு ஆண்டுக்கு சுமார் 8) மட்டும் மாறுது; அது ஒரு நிர்வாக முடிவு, நகராமலும் இருக்கலாம். ஒரு bond-ஓட YTM தொடர்ச்சியா observe ஆகும் சந்தை விலை, bond trade ஆகற ஒவ்வொரு தடவையும் மறுகணக்கிடப்படுது, அடுத்த policy முடிவு பத்தின எதிர்பார்ப்புகள் உட்பட, ஆனா அதுக்கு மட்டுமில்ல, சந்தை விலையிட்டுக்கொண்டிருக்கற எல்லாத்துக்கும் எதிர்வினை ஆற்றி.**

Section 8-ஓட ரெண்டு-கேள்வி பிரிவும், "முடிவு" கடிகாரங்களை "தொடர்ச்சியான சந்தை விலையிடல்" கடிகாரங்களிலிருந்து வேறுபடுத்தும் அதன் table-உம்.

**9. "Exposure-ஓட ஒரு-basis-point உணர்திறன், நகர்ந்த basis points எண்ணிக்கையால பெருக்கப்பட்டது" — bond-க்கு DV01 × நகர்ந்த bps, swap-க்கு Swap DV01 × நகர்ந்த bps, CDS-க்கு Spread DV01 × நகர்ந்த bps. குறி வழக்கமும் எந்த குறிப்பிட்ட உணர்திறன் அளவு பொருந்துதுங்கறதும் மட்டும் வேறுபடுது; மூணுக்கும் கணித வடிவம் ஒரே மாதிரி.**

Section 9-ஓட "அதே வடிவம், மூணு முறை" diagram இந்த formula-வை மூன்று asset classes-க்கும் பக்கம்-பக்கமா வெளிப்படையா சொல்லுது.
