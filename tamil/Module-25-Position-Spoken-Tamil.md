# Module 25 — Position Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Reference module**, **[Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html)**, **[Hedging Deep Dive](../docs/1-capital-markets/module-22-hedging-deep-dive.html)** குடும்பத்தை சேர்ந்தது. "Position" ஏற்கனவே ஒவ்வொரு asset-class module-லயும் தனி lifecycle stage-ஆ தோன்றுது ([Module 4](../docs/1-capital-markets/module-04-equity.html) §5, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §5, [Module 6](../docs/1-capital-markets/module-06-fx.html) §5, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §5, [Module 8](../docs/1-capital-markets/module-08-rates.html) §5), [Module 10](../docs/1-capital-markets/module-10-valuation.html)-ஓட Valuation/MTM கணக்கையும் [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட VaR-ஐயும் இயக்குது — இந்த module அவை எல்லாத்தையும் ஒரே theory-க்குள்ள இழுத்து, இந்த curriculum தொடர்ந்து திரும்பிவரும் "ஒரு Position உண்மையில ஒரு regulator-ஐ எப்படி அடையுது, எவ்வளவு அடிக்கடி" பதிலையும் சேர்க்குது.
> **ஏற்கனவே இருக்கும் எழுத்துத் தமிழ் பதிப்பு:** [Position_Deep_Dive_Tamil](./Position_Deep_Dive_Tamil.html) — இது அதே தலைப்பின் *பேச்சுத் தமிழ்* பதிப்பு, மூல English module-ஐ அப்படியே பின்தொடர்ந்து.
> **மூலம்:** English Module 25-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Position-ன்னா என்ன? — Definition & ஒவ்வொரு Position-க்கும் இருக்கும் மூன்று விஷயங்கள்

**Definition:** **Position** = ஒரு firm (அல்லது அதுக்குள்ள ஒரு குறிப்பிட்ட book/desk) ஒரு புள்ளியில ஒரு குறிப்பிட்ட instrument-ல வெச்சிருக்கற **நிகர, ஒட்டுமொத்த holding**, அந்த instrument-ல இதுவரை execute ஆன ஒவ்வொரு trade-ல இருந்தும் கட்டப்பட்டது. **Trade** ஒரு *நிகழ்வு* — ஒரு தடவை, ஒரு நொடில நடக்குது, confirm ஆனப்புறம் மாற்றவே முடியாது. **Position** ஒரு *நிலை (state)* — நீடிக்குது, புது trade எதுவும் நடக்காம தினமும் மதிப்பு மாறுது, எதிர்கால trades-ஆல கூட்டவோ, குறைக்கவோ, முழுசா மூடவோ முடியும்.

```text
TRADE (an event) vs POSITION (a state) — THE DISTINCTION EVERY
OTHER SECTION IN THIS MODULE ASSUMES
========================================================================

  TRADE:     "Bank Of Toronto bought 10,000,000 face value of the
             Canada 5% Bond 2035 on 15-Jan-2026" -- happened once,
             at one price, never changes after Confirmation
             (Module 3 §4)

  POSITION:  "Bank Of Toronto HOLDS 10,000,000 face value of the
             Canada 5% Bond 2035" -- true every single day for as
             long as the bond is held, REVALUED daily even though
             no new trade occurs, and only changes when a NEW trade
             (a further purchase, a partial sale, or maturity)
             actually happens

  -> A reporting system that treats "position" as just "the most
     recent trade" will get this wrong the moment a SECOND trade in
     the same instrument occurs -- Section 2's three position facts
     are what actually accumulate across trades.
```

**ஒவ்வொரு position-உம் சுமக்கும் மூன்று உண்மைகள்.** Asset class எதுவா இருந்தாலும், ஒரு position முழுசா மூன்று விஷயங்களால விவரிக்கப்படுது:

| உண்மை | எந்தக் கேள்விக்கு பதில் | Example ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §4a) |
|---|---|---|
| **Quantity / Notional** | எவ்வளவு வெச்சிருக்கோம்? | CAD 10,000,000 face value |
| **Direction** | Long-ஆ Short-ஆ? | Long (bond வெச்சிருக்கோம்; issuer நமக்கு கடன்பட்டிருக்கு) |
| **Cost Basis** | சராசரியா எவ்வளவு கொடுத்தோம்? | Clean Price 98.50 (CAD 9,850,000) |

**Takeaway:** இந்த module-ல ஒவ்வொரு worked example-உம் — Equity, Fixed Income, FX, Rates, Credit, Commodities — சரியா இந்த மூன்று உண்மைகளால கட்டப்பட்ட ஒரு position, "Quantity"-க்கு வேறு வேறு அலகோட (shares, face value, currency தொகை, swap notional, protection notional, contract lots). Section 2 ஒவ்வொரு asset class-ஓட position உண்மை எங்க இருக்குன்னு சரியா map பண்ணுது.

---

## 2. Asset Classes முழுக்க Position — "Quantity" எங்க இருக்கு

**Definition:** இந்த curriculum-ஓட roadmap-ல ஒவ்வொரு asset class-உம் ஏற்கனவே தன் Trade Lifecycle worked example-ல தன் சொந்த POSITION stage-ஐ காட்டியிருக்கு ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §1-ஓட ஒன்பது-stage model ஒவ்வொரு asset class-க்கும் **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting**-ஐ ஓட்டுது) — இந்த section அவற்றை பக்கம்-பக்கமா சேர்க்குது.

| Asset class | "Quantity"-னா என்ன | Long vs. Short | எங்க வேலை செய்யப்பட்டது |
|---|---|---|---|
| **Equity** | வெச்சிருக்கற shares எண்ணிக்கை | Long = shares சொந்தம்; Short = கடன் வாங்கி விற்றது, திருப்பிக் கொடுக்கணும் | Section 3 |
| **Fixed Income** | வெச்சிருக்கற bonds-ஓட Face Value | Long = bond வெச்சிருக்கோம் (ஒரு creditor); Short = அரிது, ஆனா repo/borrow வழியா சாத்தியம் | Section 4 |
| **FX** | ஒரு currency BALANCE, ஒற்றை quantity இல்ல — ஜோடியின் ரெண்டு பக்கமும் | Base currency-ல Long / quote currency-ல short, அல்லது நேர்மாறு | Section 5 |
| **Rates (IRS)** | Swap reference பண்ணும் Notional (தானே ஒருபோதும் பரிமாறப்படாது) | Long/Short என்பது உண்மையில "fixed கொடு" vs "fixed பெறு" | Section 6 |
| **Credit (CDS)** | வாங்கின/விற்ற protection-ஓட Notional | Long protection (credit மோசமானா லாபம்) vs Short protection (credit மேம்பட்டா லாபம்) | Section 6 |
| **Commodities** | Futures/forward contracts (lots) எண்ணிக்கை | Long = வாங்கியது, விலை உயர்ந்தா லாபம்; Short = விற்றது, விலை விழுந்தா லாபம் | Section 7 |

**Takeaway:** "position" ஒருபோதும் ஒற்றை உலகளாவிய field வகை இல்ல — ஒரு reporting system-ஓட Position table-க்கு asset-class-அறிந்த schema தேவை, ஏன்னா Equity/Fixed Income/Commodities positions உண்மையிலயே **வெச்சிருக்கற பொருட்களின் அளவுகள்**, Rates/Credit positions **ஒரு contract-ஓட terms-க்கான notional references**, FX positions **ரெண்டு இணைக்கப்பட்ட currency balances, ஒற்றை எண் இல்ல**. இந்த வகைகளை குழப்பிக்கறது "இந்த row-க்கு Quantity-னா என்ன" bugs-க்கு ஒரு பொதுவான மூலம்.

### கூடுதல் உதாரணம் — ஒரே நாள்ல ஒரு Bank Of Toronto Position-ஐ உருவாக்கும் மூன்று Trades

KO (Coca-Cola) shares-ல Maple Leaf Pension Fund:

| Trade | நேரம் | Side | Quantity | Price (USD) | Position பின்னாடி | சராசரி cost |
|---|---|---|---|---|---|---|
| T-1 | 09:31 | Buy | 10,000 | 68.40 | 10,000 | 68.40 |
| T-2 | 11:15 | Buy | 5,000 | 69.00 | 15,000 | (10,000×68.40 + 5,000×69.00) ÷ 15,000 = **68.60** |
| T-3 | 14:00 | Sell | 4,000 | 70.00 | 11,000 | 68.60 (விற்பனை சராசரி cost-ஐ மாற்றாது); realized P&L = 4,000 × (70.00 − 68.60) = **+5,600** |

மூணு Trades — மூணு தனி நிகழ்வுகள். Position ஒண்ணு: 11,000 shares, சராசரி cost 68.60. அதனாலதான் "latest trade" = "position"ன்னு நினைக்கக்கூடாது.

---

## 3. Worked Example — Equity: ஒரு Share Position, தினமும் Mark பண்ணப்படுது

**Definition:** Maple Leaf Pension Fund-ஓட Coca-Cola (KO) trade-க்கு [Module 4](../docs/1-capital-markets/module-04-equity.html) §5-ஓட முழு lifecycle-ஐ மறுபயன்படுத்தி — சாத்தியமான மிக எளிய position, ஏன்னா ஒரு share position வெறும் ஓடும் கணக்கு.

```text
EQUITY POSITION — MAPLE LEAF PENSION FUND, KO  (Module 4 §5)
========================================================================

  TRADE: buy 10,000 shares KO @ USD 68.40 (09:31:04, Module 4 §4a)

  POSITION (after Settlement, T+1): Maple Leaf Pension Fund's
  holding of KO increases by 10,000 shares in the position-keeping
  system -- this is what Module 3 §8's reconciliation checks against
  the custodian (DTC) and CSD records

  VALUATION / P&L -- marked to the current market price EVERY DAY
  after settlement, whether or not any new trade happens:
    Day of purchase:  10,000 x 68.40 = USD 684,000 (cost basis)
    One week later:    10,000 x 71.10 = USD 711,000 (market value)
    Unrealized P&L =  711,000 - 684,000 = +USD 27,000

  -> Note the position ITSELF didn't change all week (still 10,000
     shares) -- only its VALUATION changed, because the position is
     revalued daily against a moving market price. If Maple Leaf
     Pension Fund had instead sold 4,000 shares on day 3, the
     POSITION would drop to 6,000 shares from that day forward, and
     every subsequent valuation would apply to the new, smaller
     quantity.
```

**Takeaway:** ஒரு equity position, Section 1-ஓட Trade-vs-Position வேறுபாட்டுக்கு மிகத் தூய்மையான விளக்கம் — 10,000-share Trade ஒரு தடவை நடந்தது; 10,000-share Position நீடிச்சு, ஒரு மேலதிக trade quantity-ஐ மாத்தும் வரை ஒவ்வொரு trading நாளும் மறுமதிப்பீடு ஆகுது. [Module 4](../docs/1-capital-markets/module-04-equity.html) §5-ஓட சொந்த குறிப்பு — "நடப்பு POSITION சில jurisdictions-ல அவ்வப்போதைய position reporting தேவைகளுக்கு தனியா உட்பட்டது" — சரியா Section 9-ஓட regulatory நூல், அங்க முழுசா எடுத்துக்கப்படுது.

---

## 4. Worked Example — Fixed Income: ஒரு Bond Position, Accrual-ஓட தினமும் Mark பண்ணப்படுது

**Definition:** [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §5-ஓட Canada 5% Bond 2035 position-ஐ மறுபயன்படுத்தி — Equity-ஐ விட ஒரு அடுக்கு சிக்கலானது, ஏன்னா ஒரு bond position, விலை நகருவதுக்கு மேல தினமும் வட்டியையும் accrue பண்ணுது.

```text
FIXED INCOME POSITION — BANK OF TORONTO, CANADA 5% BOND 2035
(Module 5 §5)
========================================================================

  TRADE: buy CAD 10,000,000 face value @ Clean Price 98.50
         (Dirty Price CAD 9,932,418, Module 1 §1's accrued interest)

  POSITION (after Settlement, T+2): Bank Of Toronto's Fixed Income
  book increases by CAD 10,000,000 face value of this bond

  VALUATION / P&L -- marked daily to the current market Clean
  Price, PLUS newly-accruing interest each day:
    Settlement day:  Clean 98.50, Accrued 82,418
    One week later:  Clean 98.65, Accrued 106,164
      Price P&L   = (98.65 - 98.50)/100 x 10,000,000 = +CAD 15,000
      Accrual P&L = 106,164 - 82,418                = +CAD 23,746

  -> The bond POSITION (CAD 10,000,000 face value) is unchanged all
     week -- exactly like Section 3's equity example -- but its
     valuation has TWO moving parts instead of one: the market's
     changing view of the Clean Price, and the mechanical daily
     accrual of Coupon Rate x days elapsed (Module 5 §1).
```

**ஏன் position SIZE தான் ஒரு basis-point நகர்வை dollar எண்ணா மாத்துது.** [Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §3-ஓட DV01 (ஒரு basis point-க்கு CAD 7,092) சரியா இந்த position-ஓட Market Value-ல (CAD 9,850,000) இருந்து நேரடியா கணக்கிடப்படுது — DV01 = Modified Duration × **Market Value** × 0.0001. Bank Of Toronto அதுக்குப் பதிலா ஒரே மாதிரியான bond-ஓட CAD 20,000,000 face value வெச்சிருந்தா, அதே 25bp yield நகர்வுக்கு DV01 சுமார் CAD 14,184/bp ஆ இரட்டிப்பாகும். **Position size தான் ஒரு-unit உணர்திறனை ஒரு உண்மையான dollar P&L ஆக்கும் பெருக்கி** — yield தானும் (bond-ஓட cash flow அட்டவணையின் பண்பான Modified Duration-உம்) எந்த ஒரு firm அதில் எவ்வளவு வெச்சிருக்குங்கறதைச் சார்ந்தது இல்ல, ஆனா dollars-ல மொத்த DV01 நிச்சயமா சார்ந்தது.

**Takeaway:** Fixed Income, Section 3-ஓட equity உதாரணம் மறைமுகமா மட்டும் குறிப்பிட்டதை வெளிப்படையாக்குது — ஒரு position-ஓட valuation, ஒரு unit சந்தை observation-ஐயும் (Clean Price, அல்லது YTM) position-ஓட சொந்த அளவையும் சார்ந்தது; அந்த ரெண்டு inputs-உம் முற்றிலும் வேறு logic-ல மாறுது: விலை/yield முழு சந்தையாலும் நிர்ணயிக்கப்படுது; அளவு Bank Of Toronto தானே trade பண்ணும்போது மட்டும் மாறுது.

---

## 5. Worked Example — FX: ஒரு Position உண்மையில இணைக்கப்பட்ட ரெண்டு Currency Balances

**Definition:** [Module 6](../docs/1-capital-markets/module-06-fx.html) §5-ஓட USD/CAD Spot trade-ஐ மறுபயன்படுத்தி — Sections 3–4-ஐ போல இல்லாம, "position" உண்மையிலயே ஒற்றை எண் இல்லாத asset class.

```text
FX POSITION — BANK OF TORONTO, USD/CAD  (Module 6 §5)
========================================================================

  TRADE: buy USD 10,000,000 @ Spot Rate 1.3500 (pay CAD 13,500,000)

  POSITION (after Settlement, T+1): Bank Of Toronto's USD cash
  position INCREASES by USD 10,000,000; its CAD cash position
  DECREASES by CAD 13,500,000 -- ONE trade, TWO position changes,
  in two different currencies

  VALUATION / P&L -- the open FX position is marked to the current
  spot rate daily, until the underlying USD is used or sold:
    Trade day:       USD 10,000,000 @ 1.3500 = CAD 13,500,000
    One week later:  USD 10,000,000 @ 1.3550 = CAD 13,550,000
    Unrealized P&L = 13,550,000 - 13,500,000 = +CAD 50,000

  -> Unlike Sections 3-4's single-instrument holding, an FX
     position is a PAIR of balances that must always be read
     together -- a "USD 10,000,000 position" only means something
     once you also know it's funded by a CAD 13,500,000 short
     position on the other side. A reporting system that shows FX
     positions per-currency, in isolation, loses the fact that
     they're economically one single trade's two halves.
```

**Takeaway:** "ஒரு position = ஒரு எண்"ங்கற அனுமானத்தை மிகக் கடினமா உடைக்கும் asset class FX — ஒவ்வொரு FX position-உம் உண்மையில ஒரு currency pair-ஓட இரண்டு ஒரே நேரத்து, எதிர்-குறி balances, ரெண்டுக்கும் தங்கள் சொந்த தினசரி மறுமதிப்பீடு தேவை (Section 9-ஓட SFTR/EMIR தினசரி-valuation கடமைகள் ஒரு ஒற்றை-instrument bond அல்லது swap position-க்கு போலவே அதே அடிப்படை logic-ஐ இங்க பொருத்துது).

---

## 6. Worked Example — Rates & Credit: வெச்சிருக்கற அளவு இல்லாத, தூய Notional ஆன Position

**Definition:** [Module 8](../docs/1-capital-markets/module-08-rates.html) §5-ஓட 5Y CAD IRS-ஐயும் [Module 9](../docs/1-capital-markets/module-09-credit.html) §5-ஓட Northstar Resources Inc. மேல CDS-ஐயும் மறுபயன்படுத்தி — "position" என்பது சொந்தமா வெச்சிருக்கற ஒரு அளவே இல்லாத, ஒரு உயிருள்ள contract-க்குள்ள வெறும் notional reference ஆன ரெண்டு asset classes.

```text
RATES POSITION — BANK OF TORONTO, 5Y CAD IRS  (Module 8 §5)
========================================================================

  TRADE: pay FIXED 4.00%, receive FLOATING (CORRA), Notional
         CAD 100,000,000, Trade ID BOT-IRS-2026-034410

  POSITION: Bank Of Toronto's Rates book shows ONE 5Y IRS, CAD
  100,000,000 notional -- there is no "quantity of swaps" the way
  there's a quantity of shares; the position IS the contract's
  remaining terms (notional, fixed rate, maturity, reset schedule)

  VALUATION / P&L -- marked-to-market between quarterly
  settlements using a discounted cash flow model referencing the
  current CORRA curve (Module 10 §4a):
    Rate moves 4.00% -> 4.30% (30bp rise)
    Approx. MTM Gain = Swap DV01 x bps moved = 41,000 x 30
                      = CAD 1,230,000 (Module 24 §4)
```

```text
CREDIT POSITION — BANK OF TORONTO, CDS ON NORTHSTAR RESOURCES INC.
(Module 9 §5)
========================================================================

  TRADE: BUY 5Y protection, USD 10,000,000 notional, Running
         Coupon 100bps, vs Goldman Sachs

  POSITION: Bank Of Toronto's Credit book shows ONE CDS, USD
  10,000,000 notional of protection BOUGHT -- again, notional
  referenced inside a live contract, not a quantity physically held

  VALUATION / P&L -- marked-to-market as the market spread moves
  away from the fixed Running Coupon (Module 24 §5):
    Market spread widens 250bps -> 400bps over one year
    Unrealized Gain = USD 525,000 to Bank Of Toronto (protection
                       buyer benefits as credit worsens)
```

**ஒரு position-keeping system-ஓட data model-க்கு இது ஏன் முக்கியம்.** ஒரு Rates அல்லது Credit "position" table, Quantity என்பது ஒரே மாதிரியான, மாற்றக்கூடிய ஒரு unit-ஓட (ஒரு share, ஒரு bond) buys மைனஸ் sells-ஓட எளிய ஓடும் கூட்டுத்தொகைங்கற Equity/Fixed Income position table-ஓட அனுமானத்தை மறுபயன்படுத்த முடியாது. ஒரு IRS அல்லது CDS position, அதற்கு பதிலா **ஒவ்வொரு உயிருள்ள contract-க்கும் ஒரு row**, அந்த contract-ஓட சொந்த notional, rate/spread terms, மீதி அட்டவணையை சுமந்து — position-ஐ மூடுவது என்றா ஒரு share position வேலை செய்ற மாதிரி "கொஞ்சம் quantity-ஐ விற்கறது" இல்ல, குறிப்பிட்ட contract-ஐ terminate அல்லது offset பண்ணுவது.

**Takeaway:** Sections 3–5 (Equity, Fixed Income, FX), இந்த section (Rates, Credit) இந்த curriculum-ஓட ஆறு asset classes உருவாக்கும் இரண்டு அடிப்படையில வேறுபட்ட position வடிவங்களை பிரதிநிதித்துவப்படுத்துது — **வெச்சிருக்கற-அளவு positions** (மாற்றக்கூடிய units சேருது) vs **contract-referencing positions** (ஒவ்வொரு உயிருள்ள derivative-க்கும் ஒரு row, அதன் சொந்த notional, terms-ல இருந்து value பண்ணப்படுது) — [Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §4–5-ஓட Swap DV01 / Spread DV01 சரியா இரண்டாவது வகையை, DV01 முதல் வகைக்கு செய்யற மாதிரியே, ஒரு dollar MTM எண்ணா மாத்தும் கருவிகள்.

---

## 7. Worked Example — Commodities: ஒவ்வொரு நாளும் Cash-Settle ஆகும் ஒரு Position

**Definition:** [Module 7](../docs/1-capital-markets/module-07-commodities.html) §5-ஓட CME WTI Crude Future-ஐ மறுபயன்படுத்தி — valuation மாற்றம் காகிதத்துல மட்டும் track ஆகாம, தினமும் cash-ல realize ஆகும் position-க்கு இந்த curriculum-ல மிகக் கூர்மையான விளக்கம்.

```text
COMMODITY FUTURES POSITION — BANK OF TORONTO, 50 WTI CONTRACTS
(Module 7 §5)
========================================================================

  TRADE: buy 50 front-month WTI contracts @ USD 75.50/bbl,
         novated to CME Clearing immediately at execution

  POSITION: Bank Of Toronto's Commodity book shows 50 WTI
  contracts, front-month, until rolled or closed

  DAILY VARIATION MARGIN -- CME Clearing marks the position to the
  daily settlement price and moves CASH every day, not just at expiry:
    Day 1 settle USD 76.00: Bank Of Toronto RECEIVES
      50 x 1,000 x (76.00 - 75.50) = USD 25,000
    Day 2 settle USD 75.20: Bank Of Toronto PAYS
      50 x 1,000 x (76.00 - 75.20) = USD 40,000

  -> Because variation margin already moved the cash, P&L and cash
     flow are the SAME number every day for an exchange-traded
     future -- unlike Section 4's bond (paper P&L, cash only moves
     at sale/maturity) or Section 6's OTC swap (paper P&L between
     quarterly settlements, unless a CSA requires daily VM too,
     Module 12).
```

**Takeaway:** exchange-traded futures, Section 1-ஓட "position-ஓட valuation மாறிச்சு" vs "cash உண்மையில நகர்ந்தது" வேறுபாட்டை ஒற்றை தினசரி நிகழ்வா ஒடுக்குது — இந்த module-ல மற்ற ஒவ்வொரு worked example-ல இருந்தும் உண்மையிலயே வேறுபட்ட operational pattern, futures desks தினசரி cash நகர்வுகளை position-ஓட காகித MTM-ஐ கவனிப்பது போலவே நெருக்கமா கவனிப்பதற்கும் இதுதான் காரணம்.

---

## 8. ஒரு Position உண்மையில எவ்வளவு அடிக்கடி மாறுது? — மூன்று வேறு கடிகாரங்கள்

**Definition:** "position" மூன்று உண்மையிலயே வேறு கடிகாரங்கள்ல மாறுது; "இந்த position-ஓட data பழசா?"ன்னு பதில் சொல்ல முயற்சிக்கும் ஒரு reporting system-க்கு அவற்றை குழப்பிக்கறது ஒரு பொதுவான குழப்ப மூலம்.

| What changes | Trigger | Frequency | Example |
|---|---|---|---|
| **Position quantity/notional** | A new trade (buy, sell, partial close, contract termination) | Only when a trade actually happens — could be seconds apart or months apart | Section 3's KO position stays 10,000 shares until Maple Leaf Pension Fund trades again |
| **Position valuation (MTM)** | The market moving — a new price, yield, rate, or spread observed | Continuously during market hours; formally snapshotted at End of Day for the official book of record | Sections 3–7's daily valuation examples |
| **Corporate/contractual events** | An event initiated by the issuer or the contract's own terms, not a trade | Irregular, event-driven — a stock split, a bond coupon payment, a swap's quarterly reset, a future's expiry/roll | [Module 4](../docs/1-capital-markets/module-04-equity.md)'s Corporate Actions; Section 6's quarterly IRS reset; Section 7's futures roll |

```text
TWO GENUINELY DIFFERENT "HOW OFTEN" QUESTIONS -- THE SAME SHAPE
AS MODULE 25 §8's BPS-FREQUENCY DISTINCTION
========================================================================

  "How often does the POSITION QUANTITY change?"
    -> Only when Bank Of Toronto (or Maple Leaf Pension Fund) itself
       trades -- an ADMINISTRATIVE/BUSINESS decision, not a
       continuously observed number.

  "How often does the POSITION'S VALUE change?"
    -> Continuously, every time the market moves -- and, separately,
       FORMALLY at End of Day, when the position-keeping system
       takes one authoritative snapshot that becomes "today's
       official position" for every downstream process (risk, P&L,
       regulatory reporting, Section 9).
```

**Intraday (நிகழ்நேர) position vs. End-of-Day (அதிகாரப்பூர்வ) position.** ஒரு trading desk-ஓட front-office system பொதுவா நாள் முழுக்க ஒவ்வொரு fill-உடனும் புதுப்பிக்கும் ஒரு **நிகழ்நேர** ஓடும் position-ஐ காட்டுது. ஆனா risk, P&L sign-off, regulatory reporting-க்கு உணவளிக்கும் position கிட்டத்தட்ட எப்பவும் **End-of-Day (EOD) snapshot** — ஒவ்வொரு business day-யும் ஒரு தடவை பிடிக்கப்படும் ஒற்றை, reconcile ஆன, அதிகாரப்பூர்வ எண் ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §8-ஓட reconciliation சரியா இந்த snapshot சரியானதுன்னு சான்றளிக்கும் control). நாள் நடுவுல ரெண்டும் நியாயமா முரண்படலாம், இரண்டும் "தவறு" இல்லாம — அவை வேறு கேள்விகளுக்கு பதில் சொல்லுது, [Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §8 திட்டமிடப்பட்ட policy rate-ஐ தொடர்ந்து observe ஆகும் சந்தை yield-ல இருந்து வேறுபடுத்தின அதே மாதிரி.

**Takeaway:** ஒரு position-ஓட *quantity* trade நிகழ்வுகள்ல மட்டும் மாறுது; அதன் *valuation* தொடர்ந்து மாறுது, ஆனா அதிகாரப்பூர்வமா நாளுக்கு ஒரு தடவை மட்டும் பிடிக்கப்படுது; *corporate/contractual நிகழ்வுகள்* trading நடவடிக்கையை முற்றிலும் சாராத தங்கள் சொந்த நிகழ்வு-குறிப்பான அட்டவணைல அதை மாத்துது. எந்த position எண்ணின் "as-of" timestamp-உம், இந்த மூன்று கடிகாரங்கள்ல எதற்கு எதிரா report பண்ணுதுன்னு சொல்லணும்.

---

## 9. Valuation, MTM & YTM மேல Position-ஓட தாக்கம்

**Definition:** Position ஒரு valuation எண்ணுக்கு பக்கத்துல உட்காரும் செயலற்ற label இல்ல — அது Valuation-ஐயும் MTM-ஐயும் *உருவாக்கும்* ரெண்டு நேரடி பெருக்கல் inputs-ல ஒண்ணு (சந்தை-observe ஆன price/yield/rate/spread-உடன் சேர்ந்து), YTM-உடன் ஒரு குறிப்பிட்ட, தலைகீழா பெற எளிதான வழியில தொடர்பு கொள்கிறது.

| கருத்து | எதுக்கு பதில் | Position எப்படி இயக்குது |
|---|---|---|
| **Valuation** | இந்த holding இப்போ எவ்வளவு மதிப்புள்ளது? | Valuation = Position Quantity/Notional × தற்போதைய சந்தை Price/Rate/Spread — Sections 3–7 எல்லாம் சரியா இதைத்தான் கணக்கிடுது |
| **MTM** | கடைசி valuation-ல இருந்து அந்த மதிப்பு எவ்வளவு மாறிச்சு? | MTM மாற்றம் = Position size × (நகர்ந்த bps × ஒரு-unit உணர்திறன்) — [Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §9-ஓட DV01/Swap DV01/Spread DV01 formulas, Section 4-ஓட position-size பெருக்கியால scale பண்ணப்பட்டது |
| **YTM** | இந்த bond-ஐ maturity வரை வெச்சிருந்தா நான் என்ன சம்பாதிக்கிறேன்? | YTM ஒரு **ஒரு-unit** எண் — position size-ஓட அது மாறவே மாறாது; ஒரு YTM நகர்வின் *மொத்த dollar* P&L தாக்கம் மட்டும் எவ்வளவு face value வெச்சிருக்கிறோம்ங்கறதோட scale ஆகுது |

```text
THE KEY NUANCE -- YTM DOESN'T CARE HOW BIG THE POSITION IS,
BUT THE DOLLAR IMPACT OF A YTM MOVE DOES
========================================================================

  Bank Of Toronto holds CAD 10,000,000 face value, YTM 5.21%
  (Module 10 §3) -- a SECOND desk holding CAD 100,000,000 face
  value of the IDENTICAL bond has the EXACT SAME YTM, 5.21%. YTM
  is a property of the bond's price/coupon/maturity, not of who
  holds how much.

  But a 25bp YTM rise costs:
    Bank Of Toronto's CAD 10,000,000 position:  -CAD 177,300
    The other desk's CAD 100,000,000 position:  -CAD 1,773,000
      (10x the position size = 10x the dollar impact, same YTM
      move, same DV01-per-unit)

  -> Confusing "the position's YTM" with "the position's dollar
     sensitivity" is a common reporting-system mix-up: YTM is a
     RATE (unaffected by size); DV01 in dollars is a POSITION-SIZED
     figure (directly proportional to size).
```

**Takeaway:** இந்த curriculum-ல ஒவ்வொரு valuation, MTM எண்ணும் "position size × ஒரு சந்தை-observe ஆன rate அல்லது விலை"க்கு குறுகுது; position size-ஐ தவறா பெறுவது (ஒரு தவறவிட்ட பழைய trade, பயன்படுத்தப்படாத ஒரு partial close) தவறின் அளவோட நேர்கோட்டுல scale ஆகும் ஒரு valuation தவறை உருவாக்குது — அதனாலதான் [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §8-ஓட reconciliation (custodian/CCP records-க்கு எதிரா position tie-out) ஒரு விருப்ப சோதனையா இல்லாம, ஒரு கடுமையான control-ஆ நடத்தப்படுது.

---

## 10. Risk Systems-ல Position — VaR, Exposure & Reconciliation Control

**Definition:** [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §6 ஏற்கனவே Value at Risk-ஐ ஒரு position-ல இருந்து நேரடியா கணக்கிடுது — இந்த section VaR, MTM போலவே, அடிப்படையில ஒரு position-அளவு கணக்கீடுன்னு வெளிப்படையாக்குது.

```text
1-DAY 99% VaR — CANADA 5% BOND 2035 POSITION  (Module 11 §6)
========================================================================

  Position: CAD 10,000,000 face value (Section 4 here)
  DV01: CAD 7,092 per basis point (position-sized, Module 24 §3)
  Assumed daily yield volatility: 8 basis points
  99% confidence multiplier: ~2.33

  1-Day 99% VaR = DV01 x Confidence Multiplier x Daily Volatility (bps)
                = 7,092 x 2.33 x 8
                = CAD 132,235

  -> Note this ENTIRE calculation is downstream of the position's
     size -- double the position, double the DV01, double the VaR,
     for the identical bond and identical market volatility
     assumption.
```

**Position reconciliation ஏன் வெறும் operations பணி இல்ல, ஒரு risk control.** இந்த VaR கணக்கீட்டுக்கு உணவளிக்கும் position தவறா இருந்தா — சொல்லுங்க, ஒரு தவறவிட்ட trade-ஆல குறைவா மதிப்பிடப்பட்டு — ஒவ்வொரு downstream எண்ணும் (VaR, MTM, regulatory reporting, Section 9) சரியா அதே விகிதத்துல தவறாகும், அமைதியா. [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §8-ஓட மூன்று-வழி reconciliation (internal books vs. custodian vs. CCP/Trade Repository records) சரியா இந்த வகை தவறு, ஒரு firm உருவாக்கும் ஒவ்வொரு position-derived எண்ணுக்குள்ளும் பரவறதுக்கு முன்னாடி பிடிக்கவே இருக்கு.

**Takeaway:** ஒரு position ஒரு database row-ல உட்கார்ந்திருக்கும் ஒரு எண் மட்டுமில்ல — இந்த curriculum-ல ஒவ்வொரு downstream valuation, MTM, VaR, regulatory கணக்கீடும் சார்ந்திருக்கும் பகிரப்பட்ட input; அதனாலதான் அதை reconcile பண்ணி சரியாவே வெச்சிருப்பது ஒரு பின்னெண்ணமா இல்லாம, முதல்-தர control ஆக நடத்தப்படுது.

---

## 11. Regulators-க்கு Position Reporting — தினசரி, அவ்வப்போது & நிகழ்நேரம்

**Definition:** [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1 ஏற்கனவே "regulatory reporting" ஒன்றல்ல ஐந்து தனிக் கடமைகள்ன்னு நிறுவியது — இந்த section குறிப்பா ஒரு **Trade**-ஐ (ஒரு முறை நிகழ்வை) இல்லாம, ஒரு **Position**-ஐ (ஒரு நிற்கும் நிலையை) report பண்ணும் அவற்றையும், அவை ஒவ்வொண்ணும் உண்மையில எவ்வளவு அடிக்கடி ஓடுதுங்கறதையும் மையப்படுத்துது.

| Regime | What gets reported about the Position | Frequency | This curriculum's coverage |
|---|---|---|---|
| **EMIR / Dodd-Frank Derivatives Reporting** | Daily valuation updates for every OTC derivative position that remains open — not just the trade at inception | **Daily**, for the life of the position | [Module 8](../docs/1-capital-markets/module-08-rates.md) §5's daily Variation Margin; [Module 10](../docs/1-capital-markets/module-10-valuation.md) §4a |
| **SFTR (Securities Financing)** | Daily valuation/collateral updates for every open repo/securities-lending position | **Daily**, for the life of the position | [Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.md) §6b's worked Day 1 → Day 2 example |
| **MiFID II Position Reporting (commodity derivatives)** | STANDING positions in position-limit-subject products, to spot concentration risk | **Periodic** (typically weekly to the trading venue, which reports on to ESMA/regulators) — a genuinely different cadence from trade-level reporting | [Module 7](../docs/1-capital-markets/module-07-commodities.md) §8's position limits; [Module 14](../docs/2-reporting/module-14-regulatory-reporting.md) §1's Position Reporting row |
| **CFTC Large Trader Reporting** (US) | Large concentrated positions in futures/options above a reporting threshold | **Daily** submission by clearing members once a trader crosses the threshold | Parallel US regime to MiFID's EU position-limit reporting |
| **Exchange-traded futures margining** (CME/ICE/NGX) | The position's daily settlement-price revaluation, driving Variation Margin cash movements | **Daily**, automatically, as part of clearing | [Module 7](../docs/1-capital-markets/module-07-commodities.md) §5 |

```text
WORKED EXAMPLE -- SFTR's DAILY VALUATION UPDATE, DAY 1 -> DAY 2
(Module 16 §6b, same repo position, Gilt collateral)
========================================================================

  Day 1: Collateral market value = GBP 20,250,000
  Overnight: Gilt price falls 0.5%
  Day 2: Collateral market value = 20,250,000 x 0.995
                                  = GBP 20,148,750  (down GBP 101,250)

  -> A NEW valuation-update report is submitted for THIS SAME
     POSITION on Day 2, even though NO NEW TRADE happened -- the
     position itself (the repo's principal, term, and rate) is
     unchanged; only its market-observed collateral value moved,
     and SFTR requires that moved value to be reported, daily, for
     as long as the position stays open (Section 8's "position
     valuation changes continuously, snapshotted daily" clock).
```

```text
TRADE-LEVEL REPORTING vs POSITION-LEVEL REPORTING -- A GENUINE
FORK IN THE ROAD (Module 14 §1)
========================================================================

  Bank Of Toronto's 5Y CAD IRS (Module 8 §5) with Goldman Sachs:

  TRADE-LEVEL (EMIR/Dodd-Frank Derivatives Reporting):
    -> ONE initial report at execution (new UTI), THEN a fresh
       valuation-update report EVERY DAY the position stays open --
       genuinely a mix of "event-driven" (the initial trade) and
       "position-driven" (every day after)

  POSITION-LEVEL (MiFID Position Reporting):
    -> NOT triggered at all for a plain IRS -- only relevant if
       Bank Of Toronto's cumulative position in a POSITION-LIMIT-
       SUBJECT product (a commodity derivative, Module 7 §8) crosses
       a regulatory threshold. A single trade's size is irrelevant;
       only the STANDING, cumulative position across many trades
       matters here.
```

**Takeaway:** "ஒரு position regulators-க்கு எவ்வளவு அடிக்கடி report பண்ணப்படுது"க்கு ஒற்றைப் பதில் இல்ல — EMIR/SFTR, ஒரு derivative அல்லது SFT position திறந்திருக்கும் வரை **தினசரி** valuation report-ஐ திறம்பட கோருது (Section 8-ஓட தொடர்ச்சியான MTM கடிகாரத்தை ஒரு முறையான தினசரி regulatory கடமையா மாத்தி), MiFID-ஓட commodity Position Reporting முற்றிலும் வேறு cadence-லயும் முற்றிலும் வேறு கேள்விக்கும் ("இன்னிக்கு இந்த position எவ்வளவு மதிப்புள்ளது?" இல்ல, "ஒரு பங்கேற்பாளர் ஒரு product-ல மிகப் பெருசா இருக்காரா?") ஒரு **அவ்வப்போதைய, concentration-மையம் கொண்ட** சோதனை. ஒரு reporting system-ஓட rules engine ரெண்டு கடிகாரங்களையும் தனித்தனியா இணைக்கணும் — ஒண்ணு மற்றதுக்கு பதிலா ஆகாது.

---

## 12. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** இந்த curriculum-ஓட மற்ற reference modules போலவே, அவ்வப்போது திருத்தப்படும் technical விதிகளை மறுசொல்லாம, முதன்மை மூலங்களை சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **ESMA** | EMIR daily valuation reporting requirements (RTS on reporting); MiFID II Article 57/58 commodity derivative position limits and position reporting | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **CFTC** | Dodd-Frank swap data reporting (including daily valuation updates); Large Trader Reporting for futures/options | [www.cftc.gov](https://www.cftc.gov) |
| **DTCC** | Trade Repository infrastructure (GTR) accepting the daily position-valuation updates this module's Section 11 describes | [www.dtcc.com](https://www.dtcc.com) |
| **ISDA** | Standard derivative documentation underlying the positions this module's Rates/Credit examples reference | [www.isda.org](https://www.isda.org) |
| **ICE / CME** | Exchange clearing-house rulebooks governing daily futures position marking and Variation Margin | [www.theice.com](https://www.theice.com), [www.cmegroup.com](https://www.cmegroup.com) |

**Takeaway:** எந்த positions report பண்ணப்படணும், எவ்வளவு அடிக்கடி, எந்த regime-ன் கீழன்னு சரியா ESMA-வையும் CFTC-யையும் source of truth-ஆவும்; ஒரு position-valuation report அல்லது Variation Margin call உடல்ரீதியா பயணிக்கும் உண்மையான infrastructure-க்கு DTCC/ICE/CME-ஐ source of truth-ஆவும் நடத்துங்க — இந்த curriculum-ஓட மற்ற reference modules தங்கள் சொந்த பகுதிகளுக்கு ஏற்கனவே பயன்படுத்தற அதே பிரிவு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | முழுக்க பயன்படுத்தப்படும் bond, IRS, CDS, WTI futures, USD/CAD FX positions-ஐ வெச்சிருக்கு (Sections 4–7, 10–11) |
| Maple Leaf Pension Fund | KO equity position-ஐ வெச்சிருக்கு (Section 3) |
| Goldman Sachs | பல worked examples முழுக்க FX/IRS/CDS counterparty (Sections 5–6) |
| Northstar Resources Inc. | CDS position-ஓட reference entity (Section 6) |
| CME Clearing | WTI futures position-ஐ தினமும் mark பண்ணி Variation Margin cash-ஐ நகர்த்துது (Section 7) |
| ESMA / CFTC | தினசரி (EMIR/SFTR/Dodd-Frank), அவ்வப்போதைய (MiFID position limits) regulatory position-reporting கடிகாரங்களை நிர்ணயிக்குது (Section 11) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ஒரு Trade-க்கும் ஒரு Position-க்கும் இடையிலான அடிப்படை வித்தியாசம் என்ன? "position"-ஐ "கடைசி trade"ன்னு மட்டும் நடத்தும் system, இரண்டாவது trade நடந்தவுடனே உடைஞ்சு போவது ஏன்?
2.  Section 2-ஐ பயன்படுத்தி, Equity அல்லது Fixed Income-க்கு வேலை செய்யற "Quantity = buys மைனஸ் sells-ஓட ஓடும் கூட்டுத்தொகை" logic-ஐ ஒரு Rates அல்லது Credit position table மறுபயன்படுத்த முடியாதது ஏன்?
3.  Section 4-ஐ பயன்படுத்தி, bond-ஓட YTM-உம் Modified Duration-உம் சரியா அப்படியே இருந்தாலும், ஒரு bond position-ஓட face value-வை இரட்டிப்பாக்கறது அதன் DV01-ஐ இரட்டிப்பாக்குவது ஏன்?
4.  Section 5-ஐ பயன்படுத்தி, ஒரு FX position ஏன் ஒற்றை எண்ணா இல்லாம "இணைக்கப்பட்ட ரெண்டு currency balances"ன்னு விவரிக்கப்படுது?
5.  Section 7-ஐ பயன்படுத்தி, ஒரு bond அல்லது OTC swap position போல இல்லாம, ஒரு exchange-traded futures position-ஓட தினசரி P&L-ஐயும் தினசரி cash flow-வையும் *ஒரே எண்ணா* ஆக்குவது எது?
6.  Section 8-ஐ பயன்படுத்தி, ஒரு position மாறும் மூன்று வேறு "கடிகாரங்கள்" என்ன? ஒரு stock split எதுக்குள்ள வருது?
7.  Section 9-ஐ பயன்படுத்தி, ஒரே bond-ஐ வெவ்வேறு அளவுகளில வெச்சிருக்கும் ரெண்டு desks, ஒரே yield நகர்வுக்கு ஒத்த YTM, ஆனா மிக வேறுபட்ட dollar P&L கொண்டிருக்க முடியுறது ஏன்?
8.  Section 11-ஐ பயன்படுத்தி, ஒரு 5Y IRS EMIR தினசரி valuation reporting-ஐ தூண்டுது, ஆனா MiFID Position Reporting-ஐ தூண்டாதது ஏன்? அதுக்குப் பதிலா MiFID Position Reporting பொருந்த என்ன உண்மையா இருக்கணும்?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Trade ஒரு-முறை நிகழ்வு, confirm ஆனப்புறம் மாறவே மாறாது; Position ஒரு instrument-ல ஒவ்வொரு trade-ல இருந்தும் ஒட்டுமொத்தமா கட்டப்பட்டு, புது trade இல்லாதப்பவும் தொடர்ந்து மறுமதிப்பீடு ஆகும் ஒரு நீடிக்கும் நிலை. "position"-ஐ "கடைசி trade"ன்னு நடத்தும் system, இரண்டாவது trade முதலாவதோட சேர்க்கப்படவோ, net ஆகவோ வேண்டிய நிமிஷமே ஒட்டுமொத்த ஓடும் கூட்டுத்தொகையை இழக்கும்.**

Section 1-ஓட Trade-vs-Position diagram இதை நேரடியா சொல்லி, சரியா இந்த தோல்வி முறையை flag பண்ணுது.

**2. ஏன்னா Equity/Fixed Income position ஒரு மாற்றக்கூடிய, ஒரே மாதிரியான unit-ஓட (ஒரு share, ஒரு bond) ஓடும் கூட்டுத்தொகையை சேர்க்குது; ஒரு Rates அல்லது Credit position ஒவ்வொரு உயிருள்ள contract-க்கும் ஒரு row, அந்த குறிப்பிட்ட contract-ஓட சொந்த notional, terms-ல இருந்து value பண்ணப்படுது — shares-ஓட quantity இருக்கற மாதிரி "swaps-ஓட quantity" கூட்டக்கூடியது இல்ல.**

Section 6-ஓட "ஒரு position-keeping system-ஓட data model-க்கு இது ஏன் முக்கியம்" விவாதம் இதை வெளிப்படையா சொல்லுது, வெச்சிருக்கற-அளவு positions-ஐ contract-referencing positions-ஓட ஒப்பிட்டு.

**3. ஏன்னா DV01 = Modified Duration × Market Value × 0.0001 — Modified Duration-உம் YTM-உம் bond-ஓட சொந்த cash flow அட்டவணை, விலையின் பண்புகள், எந்த ஒரு firm எவ்வளவு வெச்சிருக்குங்கறதாலபாதிக்கப்படாதவை; ஆனா Market Value (அதனால dollars-ல DV01) position size-ஓட நேரடியா scale ஆகுது.**

Section 4-ஓட "ஏன் position size தான் ஒரு basis-point நகர்வை dollar எண்ணா மாத்துது" விவாதம், Section 9-ஓட CAD 10,000,000 vs. CAD 100,000,000 worked ஒப்பீடு.

**4. ஏன்னா ஒரு FX trade எப்பவும் ரெண்டு currency balances-ஐ ஒரே நேரத்துல, எதிர்த்திசைகள்ல மாத்துது (USD வாங்குவது அவசியமா CAD விற்பது) — இரண்டில எதையும் தனியா படிக்கறது, அவை பொருளாதார ரீதியா ஒரு trade-ஓட இணைக்கப்பட்ட ரெண்டு பாதிகள்ங்கறதை இழக்குது.**

Section 5-ஓட worked USD/CAD உதாரணம்: USD 10,000,000 வாங்குவது அதே ஒற்றை trade-ல இருந்து USD position-ஐ அதிகரிக்கிறது, CAD position-ஐ CAD 13,500,000 குறைக்கிறது.

**5. ஏன்னா position ஒரு CCP-க்கு (CME Clearing) novate ஆகுது; அது அதை தினசரி settlement விலைக்கு mark பண்ணி ஒவ்வொரு நாளும் Variation Margin CASH-ஐ நகர்த்துது — அதனால காகித valuation மாற்றமும் உண்மையான cash flow-வும் ஒரே எண், ஒரு bond (cash sale/maturity-ல மட்டும் நகரும்) அல்லது ஒரு OTC swap (quarterly settlement-ல மட்டும், ஒரு CSA தினசரி VM கோராதவரை) போல இல்லாம.**

Section 7-ஓட worked WTI futures உதாரணம் இதை நேரடியா சொல்லுது, Sections 4, 6-உடன் ஒப்பிட்டு.

**6. Position quantity/notional (trade நடக்கும்போது மட்டும் மாறுது), position valuation/MTM (தொடர்ந்து மாறுது, End of Day-ல முறையா snapshot எடுக்கப்படுது), corporate/contractual நிகழ்வுகள் (ஒழுங்கற்றவை, நிகழ்வு-இயக்கப்பட்டவை, trade-இயக்கப்பட்டவை இல்ல). ஒரு stock split மூன்றாவது கடிகாரத்துக்குள்ள வருது — எந்த trade-உம் நடக்காமலே position-ஓட share count-ஐ மாத்துது.**

Section 8-ஓட மூன்று-கடிகார table, corporate actions-ஐ மூன்றாவது வகைக்கு வெளிப்படையான உதாரணமா கொடுத்து.

**7. ஏன்னா YTM ஒரு ஒரு-unit rate, முழுக்க bond-ஓட விலை/coupon/maturity-ஓட பண்பு — position size எதுவா இருந்தாலும் அது ஒரே மாதிரி. ஆனா YTM நகர்வின் DOLLAR தாக்கம் Position Size × ஒரு-unit DV01, ஒவ்வொரு desk எவ்வளவு face value வெச்சிருக்குங்கறதோட நேரடியா scale ஆகுது — அதே rate நகர்வு, அதே ஒரு-unit உணர்திறன், ஆனா 10 மடங்கு பெரிய position 10 மடங்கு பெரிய dollar P&L-ஐ தருது.**

Section 9-ஓட worked CAD 10,000,000 vs. CAD 100,000,000 ஒப்பீடு இதை "முக்கிய நுணுக்கம்"ன்னு வெளிப்படையா சொல்லுது.

**8. EMIR-ஓட Derivatives Reporting கடமை, ஒரு plain IRS உட்பட, கிட்டத்தட்ட ஒவ்வொரு OTC derivative position-க்கும் பொருந்தும், அது திறந்திருக்கும் வரை தினசரி valuation updates-ஐ கோரும் — அதனால IRS எப்பவும் அங்க scope-ல. MiFID Position Reporting, இதற்கு மாறா, position-limit-subject products-க்கு (Module 7 §8-ஓட commodity derivatives) மட்டும், ஒரு firm-ஓட அந்த குறிப்பிட்ட product-ல ஒட்டுமொத்த position ஒரு regulatory வரம்பைக் கடக்கும்போது மட்டும் பொருந்தும் — ஒரு plain CAD IRS ஒருபோதும் position-limit-subject product இல்ல, அதனால Bank Of Toronto அதுக்குப் பதிலா ஒரு scope-ல உள்ள commodity derivative-ல ஒரு பெரிய, வரம்பைக் கடக்கும் position-ஐ வெச்சிருந்தா மட்டுமே MiFID Position Reporting பொருந்தும்.**

Section 11-ஓட "trade-level reporting vs. position-level reporting" diagram, Module 14 §1-ஓட சொந்த IRS உதாரணத்தை நாலு regimes-க்கும் எதிரா மறுபயன்படுத்தி.
