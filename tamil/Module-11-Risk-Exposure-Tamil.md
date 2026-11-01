# Module 11 — Risk & Exposure (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Cross-cutting module** — [Module 10](../docs/1-capital-markets/module-10-valuation.html) §1-ஓட Market Value vs Exposure வேறுபாட்டின் மேல நேரடியா கட்டப்பட்டது; Modules 6, 8, 9-ல ஏற்கனவே நிறுவப்பட்ட positions-ஐ பயன்படுத்துது. எட்டு-பாக asset-class pattern-ஐ சுத்தி அமைக்கப்படலை.
> **மூலம்:** English Module 11-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Risk & Exposure-ன்னா என்ன? — MTM ≠ Exposure

**Definition:** [Module 10](../docs/1-capital-markets/module-10-valuation.html) §1 முதல்ல Market Value-உம் Exposure-உம் ஒரே எண் இல்லைன்னு சுட்டிக்காட்டுச்சு. இந்த module அந்த வேறுபாட்டை துல்லியமாக்குது. **MTM** ஒரு position *இப்போ* எவ்வளவு மதிப்புள்ளதுன்னு சொல்லும். **Exposure** உங்க counterparty பணம் கொடுக்கத் தவறினா நீங்க எவ்வளவு *இழப்பீங்க*ன்னு சொல்லும் — இந்த ரெண்டும் ஒரே ஒரு குறிப்பிட்ட திசையில மட்டும்தான் ஒரே எண்.

| Term | அர்த்தம் |
|---|---|
| Market Value / MTM | Position-ஓட தற்போதைய மதிப்பு ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §1) — நேர்மறையாவும் இருக்கலாம், எதிர்மறையாவும் |
| Current Exposure (CE) | உங்க counterparty இப்போவே default ஆனா இன்னிக்கு நீங்க சந்திக்கும் இழப்பு — ஒருபோதும் எதிர்மறையாகாது, ஏன்னா ஒரு எதிர்மறைத் தொகையை நீங்க "இழக்க" முடியாது |
| Potential Future Exposure (PFE) | Position-ஓட மதிப்பு இன்னும் எவ்வளவு நகரலாம்ங்கறதைப் பொறுத்து, ஒரு எதிர்கால தேதிக்குள்ள Current Exposure எவ்வளவு பெருசாகலாம்ங்கற மதிப்பீடு |
| Net Exposure | ஒரே legal agreement-ஓட கீழ ஒரே counterparty-உடன் இருக்கற பல trades-ஐ netting பண்ணின பிறகு Exposure (Section 3) |
| Counterparty Risk | ஒரு trade-ஓட மறுபக்கத்துல இருக்கற தரப்பு தன் கடமைகளை நிறைவேற்றத் தவறும் பொது risk |
| Credit Exposure | பெரும்பாலும் Counterparty Risk-ஓட *dollar தொகையை* குறிக்க ஒண்ணுக்கொண்ணு மாத்தி பயன்படுத்தப்படுது — அந்தத் தவறு நடந்தா உண்மையில எவ்வளவு ஆபத்துல இருக்கு |

```text
WHY MTM AND EXPOSURE AREN'T THE SAME NUMBER
========================================================================

  Current Exposure = MAX(MTM, 0)

  5Y CAD IRS (Module 8 §4a), Bank Of Toronto pays fixed 4.00%:

  Scenario A — 6 months later, MTM = +CAD 1,230,000 (Module 10 §4a)
    -> If Goldman Sachs defaulted RIGHT NOW, Bank Of Toronto would
       lose the CAD 1,230,000 it's owed. Current Exposure = CAD 1,230,000.

  Scenario B — market instead moved the OTHER way, MTM = -CAD 1,230,000
    (Bank Of Toronto now owes Goldman Sachs, not the reverse)
    -> If Goldman Sachs defaulted RIGHT NOW, Bank Of Toronto loses
       NOTHING from this trade -- Bank Of Toronto still owes the
       money. Current Exposure = CAD 0, even though MTM is very
       much non-zero.

  -> A negative MTM is a real number on a P&L report (Module 10 §1)
     but contributes ZERO to Current Exposure. This asymmetry is
     the entire reason the two fields can never be treated as
     synonyms on a risk report.
```

**Takeaway:** Exposure அடிப்படையில ஒரு *ஒருபக்க* கருத்து — நீங்க இழக்கற scenarios-ஐ மட்டும் எண்ணுது, உங்க counterparty இழக்கற scenarios-ஐ ஒருபோதும் இல்ல. MTM, இதற்கு மாறா, சமச்சீர்: உங்களுக்கு எவ்வளவு எதிர்மறையோ, உங்க counterparty-க்கு அவ்வளவு நேர்மறை. ரெண்டையும் குழப்பிக்கறது ஒண்ணு risk-ஐ பயங்கரமா மிகைப்படுத்தும் (ஒவ்வொரு எதிர்மறை MTM-ஐயும் exposure போல நடத்துவது), இல்ல இன்னும் மோசமா குறைச்சுக் காட்டும்.

### கூடுதல் உதாரணம் — ஒரே trade, இரு திசை MTM

| நிலை | Bank Of Toronto-க்கு MTM | Current Exposure = MAX(MTM, 0) | இதன் பொருள் |
|---|---|---|---|
| Rates உயர்ந்தது (Bank Of Toronto fixed payer) | +CAD 1,230,000 | 1,230,000 | Goldman Sachs இவ்வளவு கொடுக்கணும் — default ஆனா இது இழப்பு |
| Rates குறைஞ்சது | -CAD 1,230,000 | 0 | Bank Of Toronto தான் கொடுக்கணும் — Goldman Sachs default ஆனாலும் இழப்பில்ல |
| Rates மாறலை (inception) | 0 | 0 | இன்னிக்கு exposure இல்ல — ஆனா PFE இருக்கு (Section 2) |

Goldman Sachs-ஓட பக்கத்துல இது கண்ணாடி பிம்பம்: அவங்க MTM மேல இருந்த எதிர்மறை, அவங்களுக்கு நேர்மறை exposure ஆகும் (Bank Of Toronto default ஆனா).

---

## 2. Current Exposure vs Potential Future Exposure

**Definition:** Current Exposure "இன்னிக்கு எது ஆபத்துல"ங்கறதுக்கு பதில். Potential Future Exposure (PFE) "இந்த trade maturity ஆகறதுக்குள்ள ஏதோ ஒரு கட்டத்துல எது ஆபத்துல இருக்கலாம்"ங்கறதுக்கு பதில் — நீண்ட கால derivative-க்கு PFE பெரும்பாலும் பெரிய, அதிக விளைவுள்ள எண்.

```text
PFE — WHY A ZERO-MTM TRADE STILL CARRIES REAL RISK
========================================================================

  5Y CAD IRS, Notional CAD 100,000,000, AT INCEPTION:
    MTM = 0 (Module 10 §4a — a fairly-priced swap starts at zero)
    Current Exposure = MAX(0, 0) = CAD 0

  -> A risk report showing "Current Exposure: CAD 0" on trade date
     is CORRECT, not a broken feed. But it does NOT mean this trade
     is risk-free -- over the next 5 years, rates can move a great
     deal, and Module 10 §4a already showed a 30bp move alone
     producing roughly CAD 1,230,000 of MTM swing after just 6 months.

  POTENTIAL FUTURE EXPOSURE (illustrative, using a standard
  percentile-of-simulated-outcomes approach real risk systems use):
    At a 95% confidence level, 2 years into the trade, PFE might be
    estimated at roughly CAD 4,500,000 -- the swap's notional,
    remaining term, and rate volatility all feed this number, not
    just today's zero MTM.

  -> PFE is why a bank extends a credit limit to a counterparty
     BEFORE trading with them, sized off the WORST plausible future
     exposure, not today's (currently zero) exposure.
```

**Takeaway:** Current Exposure ஒரு snapshot; PFE ஒரு முன்னோக்கிய மதிப்பீடு — trade maturity ஆகும் முன்னாடி அதன் மதிப்புக்கு இன்னும் நடக்கக்கூடிய எல்லாத்தையும் கணக்குல எடுக்கணும். அதனாலதான் Initial Margin எவ்வளவு post பண்ணப்படுதுங்கறதை ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html)) இயக்குறதும், ஒரு வங்கி ஒரு counterparty-க்கு முதல்ல எவ்வளவு பெரிய credit limit-ஐ வழங்குதுங்கறதை இயக்குறதும் Current Exposure இல்ல, PFE.

---

## 3. Netting — Gross Exposure ஏன் உண்மை Risk-ஐ மிகைப்படுத்துது

**Definition:** [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7 ISDA Master Agreement-ஐ, ரெண்டு counterparties-க்கு இடையிலான ஒவ்வொரு OTC derivative trade-யையும் ஆளும் legal contract-ஆ அறிமுகப்படுத்திச்சு. Risk நோக்கத்துக்கு அதன் மிக விளைவுள்ள அம்சம்: **close-out netting** — ஒரு counterparty default ஆனா, அந்த ஒரே Master Agreement-ஓட கீழ உள்ள ஒவ்வொரு trade-உம் தனித்தனி வெற்றியாளர்கள், தோற்றவர்கள் குவியலா இல்லாம, ஒரே ஒரு net கடமையா ஒடுங்கிடும்.

```text
GROSS EXPOSURE  vs  NET EXPOSURE — Bank Of Toronto & Goldman Sachs,
one ISDA Master Agreement, three outstanding trades
========================================================================

  Trade 1 — 5Y CAD IRS (Module 8 §4a):        MTM = +CAD 1,500,000
  Trade 2 — FX Forward (Module 6 §4b):         MTM = +CAD   900,000
  Trade 3 — CDS, Goldman Sachs as protection
            seller (Module 9 §4a):              MTM = -CAD   400,000

  GROSS POSITIVE EXPOSURE (sum only the trades where Bank Of
  Toronto is owed money -- i.e., pretend netting doesn't exist):
    1,500,000 + 900,000 = CAD 2,400,000

  NET EXPOSURE (sum ALL three MTMs together, exactly what close-out
  netting under the ISDA Master Agreement actually produces if
  Goldman Sachs defaults):
    1,500,000 + 900,000 - 400,000 = CAD 2,000,000

  -> Without netting, a risk system would overstate Bank Of
     Toronto's exposure to Goldman Sachs by CAD 400,000 -- exactly
     the amount of Trade 3, which nets AGAINST the other two rather
     than adding on top of them.
```

> **Callout — [Module 10](../docs/1-capital-markets/module-10-valuation.html) §4c-ல இருந்து வேறு scenario.** [Module 10](../docs/1-capital-markets/module-10-valuation.html) §4c இதே CDS-ஐ, Northstar Resources Inc.-ஓட spread விரியுற scenario-ல மறுமதிப்பீடு செய்யுது — Bank Of Toronto-ஓட protection position-க்கு லாபம். இங்க Trade 3-ஓட -CAD 400,000, எதிர்த்திசை விளக்க scenario-வை கருதுது — Northstar Resources Inc.-ஓட spread அதன் அசல் மட்டத்துல இருந்து *குறுகியிருக்கு*, Bank Of Toronto lock பண்ணின protection தற்போதைய சந்தை விலை தர்றதை விட குறைவான மதிப்புள்ளதா: லாபமில்ல, இழப்பு. இவை அதே trade-ஓட வேறு சந்தை நகர்வுகள்ல ரெண்டு சுயாதீன snapshots, முரண்பாடு இல்ல. தனியா, CDS-ஓட சொந்த notional USD 10,000,000 (Module 9 §4a); Trade 3-ஓட MTM இங்க CAD-ல காட்டப்பட்டிருக்கு, ஏன்னா ஒரு netting set-ஓட exposure, ஒவ்வொரு trade-ஓட சொந்த underlying currency எதுவா இருந்தாலும், Master Agreement-ஓட கீழ உள்ள எல்லா trades முழுக்க ஒரு பொது reporting currency-ல வெளிப்படுத்தப்படுது.

**IT/reporting பார்வை:** அதனாலதான் exposure, தனிப்பட்ட trade வாரியா இல்ல, **legal netting set** வாரியா (ஒரு counterparty-உடன் ஒரு ISDA Master Agreement-ஓட கீழ உள்ள எல்லா trades) கணக்கிடப்பட்டு store பண்ணப்படணும்; ஒரு வங்கி trade பண்ற ஒவ்வொரு counterparty முழுக்க கண்மூடித்தனமா கூட்டவும் கூடாது. Trade record-ல "Netting Set ID" அல்லது "Master Agreement ID" field தான், இந்த trade-க்கு எதிரா எந்த மற்ற trades-ஓட MTM-களை சட்டப்படி offset பண்ணலாம்னு risk system-க்கு சொல்லுது.

**Takeaway:** netting, ஒரு trade கூட கை மாறாம OTC derivatives சந்தையில உண்மையான counterparty risk-ஐ குறைக்கற ஒற்றை மிகப்பெரிய நெம்புகோல் — இது முழுக்க சட்ட/ஒப்பந்த mechanism, சந்தை அல்லது விலை விளைவு இல்ல; அதனாலதான் ஒரு credit risk team மிகவும் கவலைப்படற document, எந்த ஒரு தனி trade confirmation-ஓ இல்லாம, ISDA Master Agreement.

### கூடுதல் உதாரணம் — Netting இல்லாம vs இருந்தா (இரண்டாவது counterparty-யோட)

Bank Of Toronto-க்கும் Maple Leaf Fund-க்கும் ஒரு தனி ISDA Master Agreement; ரெண்டு trades:

| Trade | MTM (CAD) |
|---|---|
| IRS | +800,000 |
| FX Forward | -300,000 |

- **Netting இல்லாம:** exposure = 800,000 (எதிர்மறை trade-ஐ புறக்கணிச்சு).
- **Netting-ஓட:** exposure = MAX(800,000 − 300,000, 0) = **500,000**.
- **இரண்டு வேறு counterparties** (Goldman Sachs, Maple Leaf Fund) trades-ஐ ஒண்ணா netting பண்ண முடியாது — வெவ்வேறு legal agreements. அதனாலதான் Netting Set ID முக்கியம்.

---

## 4. Counterparty Credit Risk Deep Dive — CVA, Wrong-Way Risk

**Definition:** [Module 10](../docs/1-capital-markets/module-10-valuation.html) §8-ஓட abbreviations table ஏற்கனவே **CVA (Credit Valuation Adjustment)**-ஐ counterparty default risk-ஐ பிரதிபலிக்கும் ஒரு adjustment-ஆ வரையறுத்திருக்கு. Mechanism இதோ: CVA, Net Exposure (Section 3)-ஐயும் ஒரு counterparty-ஓட சொந்த creditworthiness-ஐயும், position-ஓட மதிப்புக்கு எதிரான ஒரே dollar charge-ஆ மாத்துது.

```text
CVA — ILLUSTRATIVE CALCULATION
========================================================================

  Net Exposure to Goldman Sachs (Section 03): CAD 2,000,000
  Goldman Sachs' estimated 1-year Probability of Default: 0.50%
  Loss Given Default (i.e., 1 - Recovery Rate, Module 9 §1's
  Recovery Rate concept applied to a COUNTERPARTY rather than a
  CDS Reference Entity): 60%

  Simplified CVA ≈ Net Exposure x Probability of Default x Loss
                    Given Default
                  = 2,000,000 x 0.50% x 60%
                  = CAD 6,000

  -> This CAD 6,000 is subtracted from the position's Fair Value
     (Module 10 §1) specifically because the counterparty MIGHT
     default -- a real accounting adjustment, not just an internal
     risk metric.
```

**Wrong-Way Risk:** counterparty-ஓட default ஆகும் வாய்ப்பு, exposure தானே பெருசாகுறதோட *தொடர்புடையதா* இருக்கற குறிப்பிட்ட, ஆபத்தான சந்தர்ப்பம் — [Module 9](../docs/1-capital-markets/module-09-credit.html)-ஓட Q4 பதில் இதே scenario-வை ஏற்கனவே முன்னோட்டம் காட்டியது: Goldman Sachs தன் *சொந்த* default risk மேல CDS protection விக்குது. Goldman Sachs தோற்க ஆரம்பிச்சா, அந்த CDS position மேல Bank Of Toronto-ஓட exposure வளருது (Section 2-ஓட PFE விளைவு, credit மோசமாகுது) — Goldman Sachs-ஓட பணம் கொடுக்கும் திறன் மறையற *அதே நொடில* — ஒரு risk model எதிர்கொள்ளக்கூடிய மிக மோசமான தொடர்பு; ஒரு உண்மையிலயே ஆபத்தான position-க்கு "counterparty யார்"-உம் "trade எதை reference பண்ணுது"-உம் ஏன் ஒருபோதும் சுயாதீன கேள்விகள் இல்லைங்கறதுக்கும் இதுதான் காரணம்.

**Takeaway:** CVA, Section 1–3-ஓட Current/Net Exposure கருத்துகளை ஒரு position-ஓட மதிப்புக்குள்ள பதிக்கப்பட்ட உண்மையான dollar செலவா மாத்துது; Wrong-Way Risk, அந்தக் கணக்கீட்டின் *probability* பாதி (Goldman Sachs-ஓட Probability of Default) எப்பவும் நிலையான, சுயாதீன எண்ணா இருக்காது — சில சமயம் அது பெருக்கப்படற exposure-உடனேயே ஒருங்கிணைந்து நகரும்ங்கற நினைவூட்டல்.

---

## 5. Cross-Asset-Class Exposure Reference — Bilateral vs CCP

**Definition:** ஒவ்வொரு asset-class module-லயும் (Modules 4–9, 15) Section 7 எந்த products bilateral, எவை centrally cleared-ன்னு ஏற்கனவே நிறுவியது. அதே பிரிவு, இந்த module கணக்கிட்டுட்டிருக்கற counterparty risk-ஐ *யார்* சுமக்கறாங்கன்னு தீர்மானிக்குது.

| Cash / Derivative | Asset Class / Product | Counterparty Risk Sits With... | Why |
|---|---|---|---|
| CASH | Cash Equity / Bonds (mostly) | CCP (NSCC, CDCC) for equities; bilateral for most bonds | [Module 4](../docs/1-capital-markets/module-04-equity.md) §7, [Module 5](../docs/1-capital-markets/module-05-fixed-income.md) §7 |
| CASH + DERIVATIVE | FX Spot/Forward/Swap | Bilateral, mitigated by CLS's PvP (settlement risk, not counterparty risk — [Module 6](../docs/1-capital-markets/module-06-fx.md) §5) | [Module 6](../docs/1-capital-markets/module-06-fx.md) §7 |
| DERIVATIVE | Standardized IRS | CCP (LCH SwapClear) — Section 3-4's netting/CVA math still applies, just with the CCP as counterparty instead of Goldman Sachs | [Module 8](../docs/1-capital-markets/module-08-rates.md) §7 |
| DERIVATIVE | FRA / Cap-Floor / Swaption / IR Forward Debt | Bilateral (mostly) | [Module 8](../docs/1-capital-markets/module-08-rates.md) §7 |
| DERIVATIVE | CDS Index / liquid Single-Name CDS | CCP (ICE Clear Credit, LCH CDSClear) | [Module 9](../docs/1-capital-markets/module-09-credit.md) §7 |
| DERIVATIVE | Bespoke Single-Name CDS | Bilateral — Section 4's CVA/wrong-way-risk math applies directly | [Module 9](../docs/1-capital-markets/module-09-credit.md) §7 |
| DERIVATIVE | Futures / Listed Options | **Always** CCP, from execution ([Module 15](../docs/1-capital-markets/module-15-listed-derivatives.md) §7) | [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.md) §7 |

```text
CCP EXPOSURE ISN'T ZERO -- IT'S REDIRECTED
========================================================================

  Bilateral trade: Bank Of Toronto's counterparty risk -> Goldman
                    Sachs directly. CVA math (Section 04) applies
                    to Goldman Sachs' own creditworthiness.

  Centrally cleared trade: Bank Of Toronto's counterparty risk ->
                    the CCP (e.g. LCH SwapClear). Novation (Module
                    3 §6) legally substitutes the CCP for Goldman
                    Sachs -- but Bank Of Toronto STILL has residual
                    exposure to the CCP itself failing, mitigated
                    by the CCP's own layered "default waterfall":
                    the defaulting member's own Initial Margin,
                    then its Default Fund contribution, then other
                    members' mutualized Default Fund contributions,
                    then the CCP's own capital.
```

**Takeaway:** Central clearing counterparty risk-ஐ மறையச் செய்யாது — ஒரே அமைப்புக்குள்ள (CCP) அதை குவிச்சு பகிர்ந்து கொள்ளுது; அந்த அமைப்பு member defaults-ஐ உறிஞ்ச குறிப்பா கட்டமைக்கப்பட்டு, முதலீடு செய்யப்பட்டு, regulate பண்ணப்பட்டிருக்கு — இது ஒவ்வொரு தனி trading counterparty-க்கும் இருக்கற bilateral exposures வலையை விட அடிப்படையில வேறு (standardized products-க்கு பொதுவா பாதுகாப்பான) risk profile, ஆனா நேரடியா "பூஜ்ய risk" இல்ல.

---

## 6. Portfolio-Level Risk — Value at Risk (VaR)

**Definition:** [Module 10](../docs/1-capital-markets/module-10-valuation.html) §8-ஓட abbreviations table VaR-ஐ, ஒரு confidence மட்டத்துல ஒரு கால வரம்புக்கு எதிர்பார்க்கப்படும் அதிகபட்ச இழப்பின் புள்ளிவிவர மதிப்பீடா ஏற்கனவே வரையறுத்தது. கணக்கீடு இதோ, [Module 10](../docs/1-capital-markets/module-10-valuation.html) §3-ஓட bond DV01-ஐ மறுபயன்படுத்தி.

```text
1-DAY 99% VaR — Canada 5% Bond 2035 Position (Module 10 §3)
========================================================================

  DV01: CAD 7,092 per basis point (Module 10 §3)
  Assumed daily yield volatility (1 standard deviation): 8 basis points
  99% confidence level ≈ 2.33 standard deviations (a standard
  statistical constant for a normal distribution's 99th percentile)

  1-Day 99% VaR = DV01 x Confidence Multiplier x Daily Volatility (bps)
                = 7,092 x 2.33 x 8
                = CAD 132,235

  -> Interpretation: on 99 out of 100 trading days, this bond
     position should NOT lose more than approximately CAD 132,235
     in a single day, based purely on interest rate moves. On the
     1-in-100 "tail" day, it could lose more -- VaR explicitly does
     NOT bound the worst case, only the 99th-percentile case.
```

**IT/reporting பார்வை:** ஒரு தனி position-ஓட VaR (மேல உள்ள bond போல) பயனுள்ள sensitivity சோதனை, ஆனா உண்மையான risk systems, ஒரு desk (அல்லது முழு வங்கியும்) வெச்சிருக்கற ஒவ்வொரு position முழுக்கவும் **portfolio-level** VaR கணக்கிடுது — வெவ்வேறு positions-ஓட மதிப்பு மாற்றங்கள் முழுமையா தொடர்புபடாததால, portfolio VaR கிட்டத்தட்ட எப்பவும் ஒவ்வொரு தனி position-ஓட VaR-களின் எளிய கூட்டுத்தொகையை விட *குறைவு*. அந்த diversification நன்மை ஒரு உண்மையான, கணக்கிடக்கூடிய எண், "diversification உதவும்"ங்கற தரமான கூற்று மட்டுமில்ல — அதனாலதான் risk systems-க்கு ஒவ்வொரு position-ஓட சொந்த volatility மட்டுமில்ல, இந்த roadmap-ல உள்ள ஒவ்வொரு asset class முழுக்க முழு correlation data-வும் தேவை.

> **Deep dive இருக்கு.** இந்த VaR கணக்கீடு முழுக்க position-ஓட சொந்த அளவை சார்ந்தது — position-ஐ இரட்டிப்பாக்கினா, DV01 இரட்டிப்பு, VaR இரட்டிப்பு. Position உண்மையில என்ன, எவ்வளவு அடிக்கடி மாறுது, அதை reconcile பண்ணி சரியா வெச்சிருக்கறது முதல்-தர risk control ஏன்னு முழுசா: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §10.

**Takeaway:** VaR, Sections 1–5-ஓட counterparty-குறிப்பான Exposure/CVA கட்டமைப்பை நிரப்புது, மாற்றாது — VaR ஒரு position-ஓட *சொந்த மதிப்பு* *சந்தைக்கு* எதிரா எவ்வளவு நகரலாம்ங்கறதை அளக்குது; Exposure, CVA ஒரு குறிப்பிட்ட *counterparty* பணம் கொடுக்கத் தவறினா எவ்வளவு இழப்பீங்கன்னு அளக்குது. ஒரு portfolio-க்கு குறைவான VaR இருந்தும் ஒரே பெயர் மேல ஆபத்தான குவிந்த counterparty exposure இருக்கலாம், அல்லது நேர்மாறா — அவை வேறு வேறு கேள்விகளுக்கு பதில் சொல்லுது, [Module 10](../docs/1-capital-markets/module-10-valuation.html) §1 பொதுவா Market Value vs Exposure-க்கு ஏற்கனவே நிறுவினது போல.

---

## 7. IT/Reporting பார்வை — Exposure Fields, SIMM, Real-Time vs End-of-Day Risk

**Definition:** இந்த module-ஓட கருத்துகள் உருவாக்கும் மூணு நடைமுறை system-design நிஜங்கள்.

**Exposure fields, Valuation fields இல்ல:** [Module 10](../docs/1-capital-markets/module-10-valuation.html) §7 ஏற்கனவே Price/Market Value/Unrealized P&L-ஐ மூணு தனி fields-ஆ பிரிச்சது. இங்க இன்னும் மூணு சேருது — Current Exposure, PFE, Net Exposure (netting set வாரியா) — இவற்றில் எதையும் ஒரு downstream consumer Market Value-ஐ மட்டும் வெச்சு ஊகிச்சு derive பண்ணக்கூடாது; Current Exposure-க்கு குறிப்பா Section 1-ஓட MAX(MTM, 0) தளம் தேவை; ஒரு எளிய "MTM-ஐ மட்டும் காட்டு" report, எதிர்மறை MTM உள்ள ஒவ்வொரு trade-லயும் இதை தவறா பெறும்.

**SIMM (ISDA Standard Initial Margin Model):** non-centrally-cleared derivatives-க்கு Initial Margin-ஐ, ஒரு எளிய notional-சதவீத மதிப்பீட்டுக்குப் பதிலா, ஒரு position-ஓட risk *sensitivities*-ல இருந்து (ஒவ்வொரு risk factor-க்கும் அதன் DV01-போன்ற Greeks) நேரடியா கணக்கிடும், ISDA (Master Agreement-ஐ உருவாக்கிய அதே அமைப்பு, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7) உருவாக்கிய தரப்படுத்தப்பட்ட, தொழில்துறை முழுமைக்குமான model — முழு mechanics [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) (Collateral & Margin)-க்கு சொந்தம், ஆனா SIMM அடிப்படையில ஒரு PFE-குடும்ப கணக்கீடு (Section 2), தூய risk-reporting நோக்கத்துக்குப் பதிலா margin-sizing நோக்கத்துக்கு மறுபயன்படுத்தப்பட்டது.

**Real-time vs end-of-day risk:** ஒரு Front Office trading desk-க்கு pre-trade risk limit சோதனைகள் milliseconds-ல தேவை (இந்த புது trade ஒரு limit-ஐ மீறாம சேர்க்க முடியுமா?) — அவசியமாக ஒரு எளிமைப்படுத்தப்பட்ட, வேகமான தோராயம். இரவு நேரத்துல, ஒரு முழு portfolio மறுமதிப்பீடு VaR, PFE, CVA-வை துல்லியமா, ஒவ்வொரு position-ஐயும் முழு correlation data-வையும் உள்ளடக்கி மறுபடி கணக்கிடும். **ஒரே portfolio-க்கு நாளின் வெவ்வேறு புள்ளிகள்ல இந்த ரெண்டு எண்களும் நியாயமா வேறுபடலாம்**; ஒரு எண் எந்த risk run-ல இருந்து (real-time தோராயம் vs. end-of-day துல்லியம்) வந்ததுன்னு தெளிவா label பண்ணாத reporting system, ரெண்டும் பொருந்தாதப்போ உண்மையான குழப்பத்தை உண்டாக்கும்.

**Takeaway:** இந்த module-ல ஒவ்வொரு கருத்தும் — Current Exposure, PFE, Net Exposure, CVA, VaR — தன் சொந்த தெளிவா label பண்ணப்பட்ட field தேவை, தன் சொந்த cadence-ல (சில real-time, சில end-of-day) கணக்கிடப்பட்டு, ஏன்னா அவற்றை ஒரு trade record-க்கு ஒரே "Risk" எண்ணா ஒடுக்கறது, இந்த module ஏழு sections கட்டின சரியான தகவலை அழிச்சுடும்.

---

## 8. Regulatory Perspective — SA-CCR, FRTB, CCAR & Uncleared Margin Rules

| Regulation / Body | Risk & Exposure-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **BCBS SA-CCR** (Standardized Approach for Counterparty Credit Risk) | Derivatives மேல counterparty credit risk-க்கு எதிரா regulatory capital-ஐ கணக்கிட வங்கிகள் பயன்படுத்தற தற்போதைய Basel முறை — Section 1–3-ஓட Current Exposure/PFE/netting கருத்துகளை நேரடியா regulator-கட்டாய formula-வா முறைப்படுத்துது; பழைய, கரடுமுரடான "Current Exposure Method"-ஐ மாத்துது |
| **BCBS FRTB** | [Module 10](../docs/1-capital-markets/module-10-valuation.html) §8-ல valuation-observability capital charge-க்காக ஏற்கனவே அறிமுகம்; தனியா, trading book-ல **market risk**-க்கும் (VaR-குடும்ப அளவுகள், Section 06) capital தேவைகளை வகுக்குது — ஒரே standard, இந்த roadmap இப்போ ரெண்டு வேற modules-ல சந்தித்த ரெண்டு வேறு risk பரிமாணங்களை உள்ளடக்குது |
| **Federal Reserve CCAR** (Comprehensive Capital Analysis and Review) | பெரிய வங்கிகளுக்கு US Federal Reserve-ஓட வருடாந்திர stress-testing regime — நிறுவனங்களை portfolio-level இழப்புகளை model பண்ணி (Section 06-ஓட VaR logic, தீவிர hypothetical scenarios வரை நீட்டி), அவற்றை சமாளிக்க போதுமான capital வெச்சிருக்கணும்னு கட்டாயப்படுத்துது; அதே பெயரின் கீழ நேரடி EU/UK சமமான எதுவும் இல்லாத US-குறிப்பான மேற்பார்வை நடவடிக்கை |
| **EMIR / Dodd-Frank** | ரெண்டு regimes-உம், Trade Repository-க்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) OTC derivative reports, valuation fields-ஓட ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §08) சேர்த்து exposure தொடர்பான fields-ஐயும் சேர்க்கணும்னு கோருது — Current Exposure, post பண்ணின collateral ஆகியவை trade-ஓட நிலையான economics மட்டுமில்லாம, கட்டாய reportable fields-ல அடிக்கடி இருக்கும் |
| **IOSCO / BCBS Uncleared Margin Rules (UMR)** | சில வரம்புகளுக்கு மேல non-centrally-cleared derivatives-க்கு Initial, Variation Margin-ஐ கோரும் கூட்டு கட்டமைப்பு — இந்த module-ஓட PFE (Section 02), SIMM (Section 07) கருத்துகளை அதன் கணக்கீட்டு inputs-ஆ நேரடியா எடுத்துக்கும்; முழு posting mechanics [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html)-ல |

**Takeaway:** இந்த module-ஓட regulatory section, market-risk regulation (FRTB, CCAR)-உம் counterparty-credit-risk regulation (SA-CCR, UMR)-உம் சந்திக்கற இடம் — உண்மையிலயே வேறுபட்ட ரெண்டு regulatory கவலைகள், ரெண்டுமே இந்த module தொடங்கின அதே அடிப்படைக் கேள்விக்குத் திரும்புது: Market Value மட்டும் ([Module 10](../docs/1-capital-markets/module-10-valuation.html)) முழுக் கதையையும் சொல்லாத ஒரு position-ல இருந்து உண்மையில எவ்வளவு, எந்த சூழ்நிலைகள்ல இழக்கலாம்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Netting set முழுக்க Goldman Sachs-க்கு Net Exposure-ஐ வெச்சிருப்பவர் (Section 03); CVA charge-ஐ சுமந்து, தன் bond position மேல VaR-ஐ கணக்கிடுபவர் |
| Goldman Sachs | CVA, Wrong-Way Risk விவாதத்தை (Section 04) இயக்கும் creditworthiness கொண்ட counterparty |
| LCH SwapClear | தன் default waterfall மூலம் standardized IRS-க்கு counterparty risk-ஐ உறிஞ்சும் CCP (Section 05) |
| ISDA | netting-ஐ சாத்தியமாக்கும் Master Agreement (Section 03), SIMM methodology (Section 07) ரெண்டின் ஆசிரியர் |
| BCBS / Federal Reserve | SA-CCR, FRTB-ஓட market-risk capital charge, CCAR stress testing-க்குப் பின்னாலுள்ள standard-setters (Section 08) |

---

## Recap Quiz — Numbers Edition

1.  Section 01-ஓட formula-வை பயன்படுத்தி, 5Y CAD IRS-ஓட MTM சரியா CAD 0 (ஒருத்தருக்கொருத்தர் கடனில்லாம) இருந்தா, Current Exposure என்ன? அது அந்த trade-க்கு risk சுத்தமா இல்லைன்னு அர்த்தமா?
2.  Section 03-ஓட மூணு-trade netting set-ல, Trade 3-ஓட CDS MTM -CAD 400,000-க்குப் பதிலா -CAD 2,600,000 (Bank Of Toronto-க்கு மிகப் பெரிய இழப்பு) ஆக இருந்திருந்தா, Net Exposure என்ன? ஒரு எதிர்மறை Net Exposure எண் உண்மையில எதைக் குறிக்குது?
3.  Section 04-ஓட CVA formula-வை பயன்படுத்தி, Goldman Sachs-ஓட மதிப்பிடப்பட்ட Probability of Default 1.00%-க்கு இரட்டிப்பானா (credit நிலைமைகள் மோசமாகி), Net Exposure-உம் Loss Given Default-உம் அப்படியே இருந்தா, புது CVA என்ன?
4.  Central clearing counterparty risk-ஐ நீக்காம "திசை திருப்புது"ன்னு Section 05 ஏன் சொல்லுது — CCP-க்கு novation ஆனப்புறமும் எந்த குறிப்பிட்ட மீதி risk இருக்கு?
5.  Section 06-ஓட VaR formula-வை பயன்படுத்தி, கருதப்படற தினசரி yield volatility 16 basis points-க்கு இரட்டிப்பானா (மிக volatile சந்தை), அதே bond position-க்கு புது 1-Day 99% VaR என்ன?
6.  ஒரு risk report ஒரு position-ஓட real-time (நாளுக்குள்ள) VaR-உம் end-of-day VaR-உம் குறிப்பிடத்தக்க அளவு பொருந்தாததைக் காட்டுது. Section 07-ஐ பயன்படுத்தி, இது கட்டாயம் system bug-ஆ?
7.  Section 08-ஐ பயன்படுத்தி, BCBS SA-CCR-உம் Federal Reserve CCAR-உம் ஒவ்வொண்ணும் குறிப்பா எதை அளக்குதுன்னு ஒப்பிடுங்க — ஒரு வங்கியின் risk பத்தி ஒரே கேள்விக்கு பதில் சொல்லுதா, வேறு கேள்விகளுக்கா?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 12](../docs/1-capital-markets/module-12-collateral-margin.html): Collateral & Margin**-க்குப் போலாம்; இது இந்த module-ஓட PFE, SIMM கருத்துகளை, எது, யாருக்கு, எப்போ post பண்ணப்படுதுங்கற உண்மையான mechanics-ஆ மாத்துது.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Current Exposure = MAX(0, 0) = CAD 0. இது trade-க்கு risk பூஜ்யம்னு அர்த்தமில்ல — இன்னிக்கு snapshot "இப்போவே default ஆனா இழப்பில்ல"ன்னு காட்டுது, ஆனா maturity-க்கு முன்னாடி rates கணிசமா நகரக்கூடிய 5-வருஷ trade-க்கு Potential Future Exposure (Section 02) இன்னும் உண்மையானது.**

Section 02-ஓட takeaway இதை நேரடியா சொல்லுது: "trade date-ல 'Current Exposure: CAD 0' காட்டும் risk report சரியானது, உடைஞ்ச feed இல்ல... அது இந்த trade risk-free-னு அர்த்தமில்ல." இதே காரணத்துக்காகத்தான், ஒரு வங்கி credit limits-ஐ Current Exposure-க்கு எதிரா இல்ல, PFE-க்கு எதிரா அளவிடணும்.

**2. Net Exposure = 1,500,000 + 900,000 - 2,600,000 = -CAD 200,000. எதிர்மறை Net Exposure என்றா, close-out-ல Bank Of Toronto தான் Goldman Sachs-க்கு பணம் *கடன்பட்டிருக்கும்*, மாறா இல்ல — அதனால Bank Of Toronto-ஓட Goldman Sachs மேல Current Exposure (Section 01-ஓட MAX(MTM, 0) விதி) CAD 0, netting set-க்குள்ள தனி trades இன்னும் பெரிய நேர்மறை MTM-களைக் காட்டினாலும்.**

இது Section 01-ஓட Current Exposure logic-ஐ ஒரு தனி-trade மட்டத்துல இல்லாம, netting-set மட்டத்துல பொருத்தினது — ஒரு எதிர்மறை net எண், ஒரு எதிர்மறை தனி-trade MTM போலவே, Current Exposure-க்கு பூஜ்யம் பங்களிக்குது, ஏன்னா இந்த scenario-ல Goldman Sachs default ஆனா இழப்பவர் Bank Of Toronto இல்ல; Bank Of Toronto தான் net கடனாளி.

**3. புது CVA = 2,000,000 x 1.00% x 60% = CAD 12,000 — அசல் CAD 6,000-ஐ சரியா இரட்டிப்பு.**

Section 04-ஓட CVA formula மூணு காரணிகளின் எளிய பெருக்கல் — Net Exposure × Probability of Default × Loss Given Default — அதனால மற்ற ரெண்டையும் மாறாம வெச்சு எந்த ஒரு input-ஐயும் இரட்டிப்பாக்கினா முடிவு நேர்கோட்டுல இரட்டிப்பாகும். இந்த நேர்கோட்டுத்தன்மைதான் Wrong-Way Risk (Section 04) ஏன் இவ்வளவு ஆபத்தானதுங்கறதுக்கு காரணம்: Probability of Default உயரலாம் மட்டுமில்ல, Net Exposure-உம் உயருகிற *அதே நேரத்துல* உயரலாம், CVA தாக்கத்தை வெறுமனே இரட்டிப்பாக்காம கூட்டுநிலைப்படுத்தி.

**4. ஏன்னா Bank Of Toronto-க்கு CCP-யே தோற்கற மீதி exposure இன்னும் இருக்கு — CCP-ஓட சொந்த அடுக்கு default waterfall (default ஆன member-ஓட Initial Margin, அதன் Default Fund contribution, மற்ற members-ஓட பகிரப்பட்ட Default Fund contributions, அப்புறம் CCP-ஓட சொந்த capital) தீர்ந்தப்புறம்தான் Bank Of Toronto உண்மையில இழப்பைச் சந்திக்கும், ஆனா அந்த பாதுகாப்புச் சங்கிலி முடிவற்றதில்ல.**

Section 05-ஓட diagram இதை துல்லியமா சொல்லுது: novation சட்டப்படி CCP-ஐ அசல் bilateral counterparty-க்குப் பதிலா மாத்துது, ஆனா Bank Of Toronto-ஓட counterparty risk மறையாம, ஒரு புது, வித்தியாசமா கட்டமைக்கப்பட்ட counterparty-க்கு (CCP) நகருது — குவிக்கப்பட்டு, பகிரப்பட்டு, standardized products-க்கு பொதுவா மிகப் பாதுகாப்பானது, ஆனா "நேரடியா பூஜ்ய risk" இல்ல.

**5. புது 1-Day 99% VaR = 7,092 x 2.33 x 16 ≈ CAD 264,470 — அசல் CAD 132,235-ஐ இரட்டிப்பு.**

Section 06-ஓட VaR formula தினசரி volatility-ஓட நேர்கோட்டுல scale ஆகுது (DV01-உம் confidence multiplier-உம் மாறாம), Q3-ஓட CVA பதில் வேறு formula-க்கு காட்டிய அதே நேர்கோட்டு-scaling பண்பு — volatility input-ஐ இரட்டிப்பாக்கினா விளைவான VaR மதிப்பீடு நேரடியா இரட்டிப்பாகும்.

**6. இல்ல, கட்டாயம் system bug இல்ல — Section 07 வெளிப்படையா இந்த ரெண்டு எண்களும் "நியாயமா வேறுபடலாம்"னு சொல்லுது, ஏன்னா அவை வேறு cadences-ல வேறு துல்லிய மட்டங்களோட கணக்கிடப்படுது: real-time risk, pre-trade limit சோதனைகளுக்கு வேகமான, எளிமைப்படுத்தப்பட்ட தோராயங்களை பயன்படுத்துது; end-of-day risk முழு portfolio, correlation data-வோட துல்லியமா மறுகணக்கிடுது.**

Section 07-ஓட takeaway உண்மையான ஆபத்தை நேரடியா சுட்டிக்காட்டுது: ஒத்துப்போகாததே bug இல்ல; ஒரு எண் எந்த risk run-ல இருந்து (real-time தோராயம் vs. end-of-day துல்லியம்) வந்ததுன்னு தெளிவா LABEL பண்ணத் தவறும் reporting system தான், ரெண்டும் பொருந்தாதப்போ உண்மையான, தவிர்க்கக்கூடிய குழப்பத்தை உண்டாக்குது.

**7. வேறு கேள்விகள் — BCBS SA-CCR ஒரு வங்கி தன் derivatives book-ல COUNTERPARTY CREDIT RISK-க்கு எதிரா எவ்வளவு regulatory capital வெச்சிருக்கணும்ங்கறதை அளக்குது (ஒரு குறிப்பிட்ட trading partner பணம் கொடுக்கத் தவறும் risk, Sections 01-04-ஓட கவலை); Federal Reserve CCAR ஒரு வங்கி ஒரு முழு hypothetical MACROECONOMIC STRESS SCENARIO-வை தாங்க முடியுமான்னு அளக்குது (இயல்பான சந்தை நகர்வுகளின் புள்ளிவிவர percentiles-க்குப் பதிலா, Section 06-ஓட VaR logic-ஐ தீவிர, regulator-வடிவமைத்த scenarios வரை நீட்டிய, ஒரு பரந்த portfolio-உம்-balance-sheet-முழுமைக்குமான solvency கேள்வி).**

Section 08 இவற்றை நேரடியா வேறுபடுத்துது: SA-CCR இந்த module-ஓட Current Exposure/PFE/netting கருத்துகளை (Sections 01-03) ஒரு capital formula-வா முறைப்படுத்துது, CCAR Section 06-ஓட portfolio-risk logic-ஐ hypothetical தீவிர scenarios வரை நீட்டும் ஒரு supervisory stress-testing பயிற்சி — உணர்வுல தொடர்புடையவை (ரெண்டும் கடைசில வங்கி தோற்பதிலிருந்து பாதுகாக்குது) ஆனா உண்மையிலயே வேறு வேறு scopes-ல உண்மையிலயே வேறு விஷயங்களை அளக்குது.
