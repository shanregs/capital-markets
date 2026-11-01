# Module 21 — ISDA Product Taxonomy Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Reference module**, **[SFTR](../docs/2-reporting/module-16-sftr-reporting.html)**, **[UK DMO Reporting](../docs/2-reporting/module-17-dmo-uk-reporting.html)**, **[UPI, UTI & ANNA-DSB](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)** குடும்பத்தை சேர்ந்தது. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7–§8 ISDA Product Taxonomy-ஓட நாலு-level hierarchy-ஐ ஒரு master table-ஓட முதல்ல அறிமுகப்படுத்தியது; இந்த module ஆழமா போகுது — theory, ஒவ்வொரு asset class முழுக்க முழு worked paths, எந்த systems-உம் regimes-உம் இதை உண்மையில பயன்படுத்துது, இது UPI-க்கு எப்படி உணவளிக்குது, ஒரு trade-ல இந்த மதிப்புகள் உண்மையில எப்படி configure ஆகுது.
> **மூலம்:** English Module 21-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. ISDA Product Taxonomy-ன்னா என்ன? — தோற்றம், நோக்கம், "ISDA" அர்த்தப்படுத்தும் ரெண்டு விஷயங்கள்

**Definition:** **ISDA** (International Swaps and Derivatives Association) = OTC derivatives தொழில்துறையோட சொந்த trade body; அது தொடர்ந்து குழப்பிக்கொள்ளப்படும் ரெண்டு உண்மையிலயே தனி விஷயங்களை standardize பண்ணுது:

| ISDA எதை standardize பண்ணுது... | அது என்ன | எங்க உள்ளடக்கம் |
|---|---|---|
| **ISDA Master Agreement** | ரெண்டு counterparties ஒரே ஒரு தடவை கையெழுத்து போடும் *legal contract template*; அவங்களுக்குள்ள ஒவ்வொரு OTC trade-யையும் ஆளும் (netting, default, collateral) | [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) |
| **ISDA Product Taxonomy** | ஒரு *classification scheme* — ஒரு trade *எந்த வகை* derivative-ன்னு label பண்ண ஒரு கட்டுப்படுத்தப்பட்ட சொல்லகராதி, அதனால ஒவ்வொரு firm, SDR, regulator-உம் அதே categories-ஐ பயன்படுத்தும் | இந்த module |

Taxonomy-ஓட தோற்றம் குறிப்பிட்டது, regulatory: 2008 நெருக்கடிக்குப் பிறகு, **Dodd-Frank Act (2010)** முதல் தடவையா US-ல swap transactions-ஓட **நிகழ்நேர பொது reporting**-ஐ கோரியது — ஒவ்வொரு swap-உம் execution-க்கு சிறிது நேரத்துல பொதுவில பரப்பப்படணும் (விலை, notional-size bucket), பெரிய ("block") trades-க்கு அளவு-சார்ந்த masking-க்கு உட்பட்டு. அது ஒரு உடனடி நடைமுறைப் பிரச்சனையை உருவாக்கியது: ஒரு நியாயமான reporting தாமதத்தையும் notional-size வரம்பையும் நிர்ணயிக்க, regulators-உம் Swap Data Repositories (SDRs)-உம் swaps-ஐ ஒப்பிடக்கூடிய categories-ஆ bucket பண்ண வேண்டியிருந்தது — ஒரு 5Y USD interest rate swap-உம் ஒரு 5Y Brent crude oil swap-உம் ஒரே block-trade வரம்பை பகிரக்கூடாதுன்னு தெளிவு. CFTC-யும், ஒவ்வொரு SDR-உம், ஒவ்வொரு reporting firm-உம் தங்கள் சொந்த ஒத்துப்போகாத bucket scheme-களை உருவாக்குவதுக்குப் பதிலா, **ISDA — தொழில்துறையோட சொந்த standards body-ஆ — எல்லாரும் ஒன்றிணையக்கூடிய taxonomy-ஐ வெளியிட்டது**: இலவசமாக, proprietary இல்லாம, [isda.org](https://www.isda.org) வழியா reference ஆவணங்கள்/spreadsheets-ஆ பகிரப்படுது.

**Takeaway:** taxonomy **classify பண்ணுது**, **identify பண்ணுது இல்ல** — [UPI](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)-ஓட ஒப்பிட்டா இது மிக முக்கியமான வேறுபாடு; ரெண்டும் "product" அடுக்கில உட்காருறதால எளிதா குழப்பிக்கொள்ளப்படுது. ஒரு taxonomy path (உதா., `Interest Rate / IRSwap / FixedFloat / Swap`) அந்த வடிவத்தின் ஒவ்வொரு trade-ஆலும் பகிரப்படும் ஒரு *category label*; ஒரு UPI அந்த category-க்குள்ள குறிப்பிட்ட economic terms கலவைக்கு ஒரு *குறிப்பிட்ட வழங்கப்பட்ட code* (Section 5 ரெண்டையும் துல்லியமா இணைக்குது).

---

## 2. Taxonomy Hierarchy — நாலு Levels, ஐந்து Asset Classes

**Definition:** [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7-ஐ மறுசொல்லி விரிவாக்கி — ISDA Product Taxonomy ஒரு **நாலு-level hierarchy**, இந்த curriculum-ஓட சொந்த Asset Class → Product → Instrument → Trade model-ல ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §1) உள்ள "Product" அடுக்கை விட வேணும்னே ஒரு படி நுணுக்கமானது:

```text
ISDA PRODUCT TAXONOMY — FOUR LEVELS
========================================================================

  Asset Class  ->  Base Product  ->  Sub-Product  ->  Transaction Type
  ======================================================================

  Interest Rate      -> IRSwap     -> FixedFloat   -> Swap
  Credit              -> SingleName -> Corporate    -> CreditDefaultSwap
  Foreign Exchange    -> Forward    -> Deliverable   -> Forward
  Equity              -> Swap       -> PriceReturn   -> Swap
  Commodity           -> Energy     -> Oil           -> Swap

  Each level narrows the category further left-to-right. A trade's
  full "taxonomy path" is all four values read together -- none of
  them alone is enough to identify the product.
```

Derivatives-க்கு **அதிகாரப்பூர்வமா சரியா ஐந்து ISDA Asset Classes** இருக்கு — cash instruments-உம் சேர்ந்தப்புறம் பார்க்கற திறந்த-முனை "Asset Class" values-ல இருந்து வேறுபட்ட, ஒரு குறுகிய, மூடிய பட்டியல் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §11-ஓட பக்கம்-பக்கம் table):

| ISDA Asset Class | எளிய தமிழ்ல அர்த்தம் | Example Base Products |
|---|---|---|
| **Interest Rate** | Cash flows interest rates-ஐ பொறுத்தது | IRSwap, FRA, Swaption, CapFloor, Cross-Currency Swap |
| **Credit** | Default/credit event-ஐ பொறுத்து payout | SingleName, Index (CDX/iTraxx), TotalReturnSwap |
| **Foreign Exchange** | Currency rates-ஐ பொறுத்து payout | Forward, Swap, Option, NDF |
| **Equity** | Stock/index விலையை பொறுத்து payout | Swap, Option, VarianceSwap, Future |
| **Commodity** | Physical commodity விலையை பொறுத்து payout | Swap, Forward, Option (Energy, Metals, Agriculture sub-classes) |

> **Callout — v1.0 vs v2.0.** ISDA ஒரு ஆரம்ப taxonomy-ஐ (**v1.0**, ~2012) வெளியிட்டது, குறிப்பா Dodd-Frank-ஓட நிகழ்நேர reporting categories-ஐ திருப்திப்படுத்த — கரடுமுரடான, CFTC block-size விதிகளுக்குத் தேவைப்பட்டதைச் சுற்றி. ஒரு இன்னும் நுணுக்கமான **v2.0** (~2016) தொடர்ந்தது, **FpML**-உடன் (derivatives தொழில்துறையோட சொந்த XML messaging standard) field-by-field ஒத்துப்போக மறுகட்டமைக்கப்பட்டது; சில இடங்கள்ல இன்னும் நுணுக்கமான உப-categories சேர்த்தது (asset class-ஐ பொறுத்து, Transaction Type-க்கு அப்பால் ஒரு கூடுதல் qualifier — உதா., ஒரு settlement-type அல்லது option-style flag). சரியான category பெயர்களையும், ஒரு குறிப்பிட்ட system எந்தப் பதிப்பை அமல்படுத்துதுன்னும் ஒவ்வொரு system-க்கும் உறுதிப்படுத்தப்பட வேண்டியதா நடத்துங்க — இந்த curriculum ஏற்கனவே [UTI generation waterfall](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) §2-க்கும் [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §7-ஓட specifics-க்கும் பயன்படுத்தற அதே hedge — இவை திருத்தப்படுது, ஒரு reporting record-ஓட taxonomy field ஒரு குறிப்பிட்ட பதிப்போடு சேர்ந்து மட்டுமே அர்த்தமுள்ளது.

**Takeaway:** taxonomy ஒரு **classification tree, ஒரு தட்டையான code பட்டியல் இல்ல** — அதனாலதான் ஒண்ணுக்குப் பதிலா நாலு levels தேவை: "Swap" மட்டும் (Transaction Type) ஐந்து asset classes முழுக்க தெளிவில்லாதது, ஆனா `Interest Rate / IRSwap / FixedFloat / Swap` அப்படியில்ல. [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §2-ஓட சிறிய, மூடிய `Type of SFT` code set ஒரு levels-ல பயன்படுத்தும் அதே கொள்கை — ISDA taxonomy, "இது எந்த வகை product" என்ற அதே பிரச்சனையை OTC derivatives-ஓட மிகப் பெரிய, மிக வேறுபட்ட பிரபஞ்சத்துக்கு தீர்க்குது, அதனால தெளிவாக இருக்க இன்னும் அதிக levels தேவை.

---

## 3. Worked Examples — Asset Class வாரியா முழு Taxonomy Path

**Definition:** இந்த curriculum-ல வேறிடத்துல ஏற்கனவே கட்டப்பட்ட ஐந்து trades-ஐ முழு நாலு-level path வழியா இழுத்து, hierarchy-ஐ அருவமா இல்லாம, உண்மையான (விளக்க) trades-க்கு ஒதுக்கப்பட்டதா பார்க்கலாம்.

```text
FIVE TRADES, FIVE FULL TAXONOMY PATHS
========================================================================

  INTEREST RATE  (Module 8's 5Y CAD IRS, Bank Of Toronto pays fixed)
    Asset Class: Interest Rate | Base Product: IRSwap
    Sub-Product: FixedFloat    | Transaction Type: Swap
    -> "Interest Rate / IRSwap / FixedFloat / Swap"

  CREDIT  (Module 2 §9's 5Y CDS on Northern Rail Corp, Bank Of Toronto
           buys protection from Goldman Sachs)
    Asset Class: Credit        | Base Product: SingleName
    Sub-Product: Corporate     | Transaction Type: CreditDefaultSwap
    -> "Credit / SingleName / Corporate / CreditDefaultSwap"
    (Module 9 §4a's Northstar Resources Inc. CDS, used throughout the
     UPI/UTI deep dive, carries this identical taxonomy path -- same
     product shape, different reference entity and counterparty)

  FOREIGN EXCHANGE  (Module 2 §10's 3M USD/CAD Forward, Bank Of
                     Toronto buys USD from Goldman Sachs)
    Asset Class: Foreign Exchange | Base Product: Forward
    Sub-Product: Deliverable      | Transaction Type: Forward
    -> "Foreign Exchange / Forward / Deliverable / Forward"

  EQUITY  (Module 2 §8's Total Return Swap on Apple Inc. shares)
    Asset Class: Equity        | Base Product: Swap
    Sub-Product: PriceReturn   | Transaction Type: Swap
    -> "Equity / Swap / PriceReturn / Swap"

  COMMODITY  (Module 7's 1Y Brent Crude Oil Swap, notional
             100,000 barrels)
    Asset Class: Commodity     | Base Product: Energy
    Sub-Product: Oil           | Transaction Type: Swap
    -> "Commodity / Energy / Oil / Swap"
```

**Takeaway:** இந்த ஐந்து trades-ல **மூணுக்கு (Interest Rate, Equity, Commodity) Transaction Type "Swap"** என்பதை கவனிங்க — இந்த hierarchy-ஓட எந்த ஒரு level-உம் தனியா போதாது, "இது எந்த வகை product"ங்கறதுக்கு உண்மையிலயே நாலு levels-உம் சேர்ந்து படிக்கணும்ங்கறதுக்கு நிரூபணம். மேலும், Credit path-ஓட Sub-Product ("Corporate") *reference entity-யின் வகையை* (ஒரு corporate borrower, vs. உதா., ஒரு sovereign) விவரிக்குது, மற்ற ஒவ்வொரு asset class-ஓட Sub-Product-உம் *contract-ஓட ஒரு பொருளாதார அம்சத்தை* (Fixed vs. Floating, Deliverable vs. Non-Deliverable, Price vs. Total Return) விவரிக்குது — taxonomy-ஓட categories எதை வேறுபடுத்த தேர்ந்தெடுக்குதுங்கறதுல உண்மையிலயே asset-class-குறிப்பானவை, [UPI deep dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) §3 UPI reference-data templates பத்தி சொல்லும் அதே புள்ளியை எதிரொலிச்சு.

---

## 4. Bonds, Cash Securities எப்போதாவது ISDA Taxonomy-ஆல் வகைப்படுத்தப்படுமா?

**Definition:** இல்ல — [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §6-ஓட புள்ளியை நேரடியா மறுசொல்லி விரிவாக்கி, ஏன்னா IT/data பின்னணியில இருந்து வர்ற யாருக்கும் "taxonomy" ஒரு security master-ல இருக்கற எல்லாத்தையும் உள்ளடக்கணும்னு தோணுறதால இது ஒரு பொதுவான category தவறு. ISDA Product Taxonomy **bilateral contracts**-ஐ — ரெண்டு counterparties ஒத்துக்கிட்டதால மட்டுமே இருக்கற, அந்த ஒப்பந்தத்தை சாராத தனி இருப்பு இல்லாத instruments-ஐ — classify பண்ணுது. ஒரு bond, ஒரு share, commercial paper-ஓட ஒரு unit — இவை எல்லாம் வெளியிடப்பட்ட நிமிஷமே, இப்போ யார் வெச்சிருந்தாலும் தனியா இருந்து, வெச்சிருக்கப்படக்கூடிய விஷயங்கள். அந்த கட்டமைப்பு வேறுபாடுதான் cash instruments ஏன் முற்றிலும் வேறு classification scheme-ஐ பயன்படுத்துதுங்கறதுக்கு காரணம்:

| | Cash Instruments (bonds, equities, money market) | OTC Derivatives |
|---|---|---|
| **எது classify ஆகுது** | Deliver பண்ணக்கூடிய *பொருள்* | ஒரு bilateral *contract* |
| **Classification scheme** | **CFI Code** (ISO 10962 — *Classification of Financial Instruments*), பெரும்பாலான security masters-ல பதிக்கப்பட்ட 6-character code | **ISDA Product Taxonomy** (இந்த module) |
| **Instrument identifier** | ISIN / CUSIP | ISIN இல்ல — அதுக்குப் பதிலா UPI ([UPI deep dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)) |
| **நிர்வகிக்கும் அமைப்பு** | ISO, National Numbering Agencies வழியா | ISDA (தொழில்துறை trade body, Section 7) |

```text
A CDS ON NORTHERN RAIL CORP vs. A NORTHERN RAIL CORP BOND -- SAME
COMPANY, TWO COMPLETELY DIFFERENT CLASSIFICATION WORLDS
========================================================================

  Northern Rail Corp 4.5% Bond 2030 (a cash instrument):
    ISIN: (issued by a National Numbering Agency)
    CFI Code: DBFUFR (illustrative -- debt, straight bond,
              fixed rate, registered)
    -> NO ISDA taxonomy field. It's a deliverable security, full stop.

  5Y CDS on Northern Rail Corp (Module 2 §9, a derivative CONTRACT
  that merely references the same company):
    Taxonomy path: Credit / SingleName / Corporate / CreditDefaultSwap
    -> NO CFI code, NO ISIN. It's a bilateral contract, classified
       by ISDA taxonomy, identified by a UPI instead of an ISIN.
```

> **நுணுக்கம் — ஒரு bond மேல derivative இன்னும் சாதாரணமா ISDA-classified ஆகும்.** ஒரு bond option அல்லது bond future (உதா., ஒரு குறிப்பிட்ட government bond-ஐ எதிர்கால தேதில நிலையான விலைல வாங்கும் option) தானே ஒரு derivative contract, வேறு எந்த OTC derivative போலவும் தன் சொந்த ISDA taxonomy path-ஐ (பொதுவா கட்டமைப்பை பொறுத்து Interest Rate அல்லது Credit-ன் கீழ) பெறுது — ISDA classification-ஐ ஒருபோதும் சுமக்காதது *அடிப்படை cash bond தானே*, "bond-உடன் தொடர்புடைய எதுவும்" இல்ல.

**Takeaway:** "இந்த reporting record-க்கு taxonomy field தேவையா"ங்கறதுக்கான வேகமான sanity சோதனை — record-ல ISIN-உம் CFI code-உம் இருந்தா, அது ஒரு cash instrument; ISDA taxonomy fields காலியா/இல்லாம இருக்கணும்; அதுல UPI இருந்து ISIN இல்லன்னா, அது ஒரு derivative; taxonomy fields கட்டாயம். ஒரே record-ல ரெண்டையும் நிரப்பறதோ, ரெண்டையும் நிரப்பாம விடறதோ செய்யும் rules engine-ல bug — [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §11-ஓட பக்கம்-பக்கம் table ஏற்கனவே இதை இந்த முழு curriculum-லயும் மிக வேகமான ஒற்றை instrument-type பாகுபாடா நிறுவியது.

---

## 5. Taxonomy UPI-க்கு எப்படி உணவளிக்குது — DSB-ஓட Reference-Data Schema

**Definition:** Section 1 ஒரு கடுமையான கோட்டை வரைந்தாலும், ISDA Product Taxonomy-யும் [UPI](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)-உம் ஒரே விஷயம் போல உணர வைக்கற துண்டு இது — ஏன்னா **அவை உண்மையிலயே தொடர்புடையவை, ஒரே வம்சாவளியின் வேறு அடுக்குகள்ல மட்டும்**. CPMI-IOSCO தன் 2017 Technical Guidance-ஐ ஒரு உலகளாவிய UPI எதை classify பண்ணணும்னு வரையறுக்க உருவாக்கியபோது, முதல்ல இருந்து ஒரு புத்தம் புது classification சட்டகத்தை கண்டுபிடிக்கலை — ISDA-ஓட ஏற்கனவே தொழில்துறை-ஏற்ற taxonomy-ல (v2.0) இருந்து அடிப்படை classification logic-ஆ ஆரம்பிச்சு, அப்புறம் DSB-ஐ அதை ஒரு உயிருள்ள reference-data system-ஆ இயக்கவச்சது; அது உண்மையில codes-ஐ *வழங்க* முடியும்.

```text
FROM INDUSTRY TAXONOMY TO ISSUED IDENTIFIER — THE FULL LINEAGE
========================================================================

  ISDA PRODUCT TAXONOMY (this module)
    Industry-published classification scheme (v1.0 -> v2.0).
    A CONTROLLED VOCABULARY -- Asset Class / Base Product /
    Sub-Product / Transaction Type. No codes issued, no lookup
    system -- just the category definitions themselves.
         |
         v
  CPMI-IOSCO TECHNICAL GUIDANCE (2017)
    Global regulatory standard-setters adopt ISDA's taxonomy
    structure as the BASIS for a harmonized product-classification
    framework every G20 jurisdiction's regulators could converge on.
         |
         v
  DSB UPI REFERENCE-DATA SCHEMA (operated by ANNA-DSB, since 2022)
    Turns the taxonomy's categories into ACTUAL FIELDS on a live,
    queryable reference-data template per asset class (Asset Class,
    Instrument Type, Underlier, Notional Currency, Tenor, etc. --
    UPI deep dive Section 3) -- and, critically, ISSUES A CODE
    (the UPI itself) for each unique combination of those fields.
         |
         v
  EMIR / DODD-FRANK / OTHER REGIMES' TRADE REPORTS
    Consume the ISSUED UPI as a single mandatory field -- the
    reporting firm never re-derives the taxonomy path by hand; it
    looks up (or requests) the UPI that already encodes it.
```

**Takeaway:** ISDA Product Taxonomy-ஐ **இலக்கணம்**ஆவும், UPI reference-data template-ஐ **அந்த இலக்கணத்திலிருந்து கட்டிய அகராதி**ஆவும் நினைங்க — taxonomy எந்த வகை categories-ஐ வெளிப்படுத்தவே முடியும்னு வரையறுக்குது (Asset Class, Base Product, Sub-Product, Transaction Type), DSB-ஓட UPI system என்பது அந்த categories-ஓட ஒரு குறிப்பிட்ட கலவைக்கு, கூடுதல் பொருளாதார fields-ஓட (Underlier, Notional Currency, Tenor) சேர்த்து, ஒரு நிரந்தர, lookup-able code-ஐ உண்மையில ஒதுக்கும் operational அடுக்கு. ஒரு reporting engineer, ஒரே trade-ல "ISDA Taxonomy"-ஐயும் "UPI"-ஐயும் ரெண்டு சுயாதீனமா derive செய்த fields-ஆ ஒருபோதும் நிரப்பாது — UPI-ஓட reference data taxonomy path-ஐ *ஏற்கனவே encode பண்ணியிருக்கு*; UPI-ஐ query பண்றது (Section 8-ஓட practical workflow) அதை மீட்டெடுக்கும் வழி.

---

## 6. Taxonomy உண்மையில எங்க பயன்படுத்தப்படுது — UPI-க்கு அப்பால்

**Definition:** taxonomy UPI-க்கு சுமார் ஒரு தசாப்தம் முந்தியது; UPI reference data-வோடு எந்த சம்பந்தமும் இல்லாத பல பயன்பாடுகளைக் கொண்டிருக்கு — அது வெறுமனே புது identifier-க்கு ஒரு வரலாற்று படிக்கல்லா இல்ல, US swap regulation முழுக்க உண்மையிலயே அடிப்படையான plumbing.

| Use | How the taxonomy is actually consumed | Notes |
|---|---|---|
| **Dodd-Frank real-time public reporting** (CFTC Part 43) | Sets **which block-trade notional threshold applies** — the CFTC publishes separate large-notional thresholds per taxonomy category, since a "large" 5Y USD IRS and a "large" Brent crude swap are completely different sizes. A swap's taxonomy path literally determines its public-dissemination delay and masking rule. | The taxonomy's original reason for existing (Section 1) |
| **Dodd-Frank swap data recordkeeping** (CFTC Part 45) & CFTC's own published Swaps Report | SDRs (e.g. DTCC Data Repository) bucket submitted swap data by taxonomy category for the CFTC's own aggregate weekly/quarterly swaps market statistics | Same categories, different consumer (regulator's own market surveillance vs. public dissemination) |
| **SEF / MAT determinations** | A **Made-Available-to-Trade** determination is scoped in taxonomy terms — e.g. "this SEF's MAT determination covers Fixed-Float USD IRS in these tenor buckets" — so whether a given swap MUST trade on a SEF depends on matching its taxonomy path (plus tenor/currency) against a published MAT determination | Governs execution-venue obligations, not just reporting |
| **CCP clearing-mandate determinations** | Clearing mandates (which products a CCP like LCH SwapClear MUST clear, and which counterparties must clear through one) are published against taxonomy-shaped product descriptions | Complements — doesn't replace — [Module 8](../docs/1-capital-markets/module-08-rates.md) §5's clearing mechanics |
| **UPI reference data** (Section 5) | Basis for the DSB's product classification fields | The newest, most recently added use |
| **Internal bank product masters** | Every OTC derivative product template gets tagged with a taxonomy path at onboarding, so risk/valuation/reporting systems can filter and aggregate consistently | Section 8's practical IT view |

> **Callout — EMIR உண்மையில "ISDA Taxonomy"ன்னு ஒரு field-ஐ பெயரிடுறதில்ல.** மேல உள்ள US விதிகளிலிருந்து அதிகமா பொதுமைப்படுத்துவது எளிது என்பதால இதை வெளிப்படையா flag பண்றது முக்கியம்: **EMIR-ஓட** சொந்த reporting technical standards (REFIT-ல ஒத்துப்போன CPMI-IOSCO CDE fields-உம்) "ISDA Taxonomy"ன்னு நேரடியா பெயரிடப்பட்ட field-க்குப் பதிலா தங்கள் சொந்த product-classification fields-ஐ பயன்படுத்துது — வரலாற்று ரீதியா ஒரு CFI-பாணி code, சமீபத்துல UPI தானே. கீழ உள்ள *category logic* இன்னும் ISDA-ஓட வேலைக்கு (Section 5-ஓட வம்சாவளி) திரும்புது, ஆனா நீங்க ஒரு EMIR-குறிப்பான reporting schema-வை கட்டறீங்கன்னா, regulatory template-ல ஒரு raw taxonomy-path field-ஐ தேடாதீங்க; அது ஒரு EU/UK report-ஐ அடையும்போது UPI-க்குள்ள உள்வாங்கப்பட்டிருக்கும். மேல உள்ள CFTC/Dodd-Frank வரிகள் தான் taxonomy இன்னும் தன் சொந்த உரிமையில நேரடியா பெயரிடப்பட்ட, சுமை சுமக்கும் regulatory கருத்தா இருக்கற இடம்.

**Takeaway:** taxonomy-ஓட regulatory தடம் **US Dodd-Frank/CFTC regime**-ல மிகப் பரந்தும் மிக வெளிப்படையாகவும் இருக்கு — அது முதல்ல அந்த regime-க்காகவே கட்டப்பட்டது, இன்னும் அங்க public-dissemination, MAT-determination scoping-ஐ நேரடியா ஆளுது — **EU/UK EMIR உலகத்துல** அது நேரடியா மேற்கோள் காட்டப்படும் field-ஆ இல்லாம, பெரும்பாலும் UPI-க்குப் பின்னாலுள்ள வம்சாவளியா (Section 5) மட்டும் நிக்குது. நீங்க எந்த regime-க்கு கட்டறீங்கன்னு தெரிஞ்சிருக்கறது, "ISDA Taxonomy" உங்க data model-ல ஒரு first-class field-ஆ இருக்கணுமா, அல்லது ஒரு product-ஐ அதன் UPI-க்கு map பண்ற விதத்தை மட்டும் தெரிவிக்கணுமான்னு சொல்லும்.

---

## 7. Governance & Versioning

**Definition:** ISDA ஒரு **தொழில்துறை trade association**, ஒரு regulator-ஓ, [ANNA-DSB](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) §4 போன்ற market-infrastructure utility-ஓ இல்ல — அந்த curriculum DSB-க்கு ஏற்கனவே வரையற அதே வேறுபாடு இங்க இன்னும் நேரடியா பொருந்துது, ஏன்னா ISDA taxonomy-க்கு எந்த உயிருள்ள system-ஐயும் இயக்கறதில்ல; reference ஆவணங்களை வெளியிடுது.

```text
WHO ACTUALLY CHANGES THE TAXONOMY, AND HOW
========================================================================

  ISDA's own asset-class-specific working groups/committees
  (member firms -- dealers, buy-side, infrastructure providers --
  volunteering expertise, the same governance model that maintains
  the ISDA Master Agreement itself, Module 12) periodically:

    -> propose new Base Products / Sub-Products / Transaction Types
       as new derivative structures emerge in the market
    -> consult industry members on proposed changes
    -> publish a revised taxonomy version (v1.0 -> v2.0, and
       incremental updates within a major version) as spreadsheets/
       reference documents on isda.org -- free, not proprietary

  -> ISDA does NOT mandate that anyone use the taxonomy. It becomes
     mandatory only where a REGULATOR (CFTC, under Dodd-Frank) or a
     downstream STANDARD (CPMI-IOSCO's UPI guidance, Section 5)
     requires its use -- ISDA supplies the vocabulary; regulators
     and standard-setters decide when it's compulsory.
```

**Takeaway:** [UPI deep dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) §4 DSB-க்கு வரையற அதே infrastructure-vs-mandate வேறுபாடு இங்க ISDA-க்கு, ஒரு அடுக்கு மேல பொருந்துது — "ISDA இந்த trade-ஐ X-ஆ classify பண்ணுது"ங்கறது தொழில்துறை வழக்கத்தை பத்தின கூற்று, சட்டம் இல்ல; "CFTC இந்த trade-ஓட public report அதன் block வரம்பை தீர்மானிக்க taxonomy category X-ஐ பயன்படுத்தணும்னு கோருது"ங்கறது உண்மையில அதை முக்கியமாக்கும் regulatory mandate. ரெண்டையும் குழப்பிக்கறது, [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §8 பொதுவா infrastructure providers-க்கு ஏற்கனவே எச்சரிக்கும் அதே category தவறு.

---

## 8. நடைமுறைல Taxonomy Values-ஐ Configure பண்றது — Product-Master பார்வை

**Definition:** நடைமுறை IT கேள்வி — ஒரு bond-க்கோ, ஒரு swap-க்கோ, வேறெதுக்கோ ஒரு taxonomy path ஒரு உண்மையான trade-ல உண்மையில எப்படி set ஆகுது? பதில், [UPI deep dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) §3 UPI-க்கே ஏற்கனவே நிறுவிய அதே **template vs. instance** pattern-ஐ பின்பற்றுது: **taxonomy path ஒரு product TEMPLATE-ஓட பண்பு, ஒவ்வொரு தனி trade-க்கும் மறுபடி derive ஆகவோ உள்ளிடப்படவோ ஒருபோதும் படாது.**

```text
WHERE THE TAXONOMY PATH ACTUALLY LIVES — PRODUCT MASTER, NOT TRADE RECORD
========================================================================

  BANK OF TORONTO'S PRODUCT MASTER (illustrative static reference
  data table -- one row per tradable PRODUCT SHAPE, not per trade)

  Product Name              | Asset Class | Base Product | Sub-Product | Txn Type
  ---------------------------------------------------------------------------------
  CAD Fixed-Float IRS       | Interest Rate | IRSwap     | FixedFloat  | Swap
  Single-Name CDS (Corp)    | Credit        | SingleName | Corporate   | CreditDefaultSwap
  USD/CAD Deliverable Fwd   | Foreign Exch. | Forward    | Deliverable | Forward
  Equity TRS (Price Return) | Equity        | Swap       | PriceReturn | Swap
  Brent Crude Swap          | Commodity     | Energy     | Oil         | Swap

  WHEN A TRADER BOOKS A NEW TRADE:
    1. Trade capture system asks "which product template?"
    2. Trader selects "CAD Fixed-Float IRS" (an EXISTING template --
       most trades reuse one)
    3. Taxonomy path (all four fields) flows onto the trade record
       AUTOMATICALLY from the product master row -- never manually
       typed by the trader, never re-classified per trade
    4. Trade-specific fields (notional, tenor, counterparty, rate)
       get entered separately -- these do NOT affect the taxonomy
       path at all (Section 1's classify-vs-identify distinction)

  IF THE DESK WANTS TO TRADE SOMETHING GENUINELY NEW (no existing
  template fits):
    -> Product Control / New Product Committee classifies it against
       the taxonomy FIRST (Section 7's ISDA reference documents are
       exactly what they'd consult), creates a NEW product-master row,
       and only then can trades against that new template be booked
    -> This is also the trigger for requesting a new UPI reference-
       data record from the DSB (Section 5) -- the taxonomy
       classification decision and the UPI request are typically the
       same new-product-onboarding event, not two separate ones
```

ஒரு **bond**-க்கு, சமமான product-master வரிக்கு taxonomy columns எதுவும் நிரப்பப்பட்டிருக்காது — அதுக்குப் பதிலா ஒரு **CFI code**-ஐ சுமக்கும் (Section 4), ஏன்னா அது ஒரு derivative product template இல்ல, ஒரு security master entry. சரியா கட்டப்பட்ட trade capture system, "இந்த instrument type-க்கு taxonomy fields இருக்கா"ங்கறதை *asset class-ஓட* (cash vs. derivative) ஒரு பண்பா நடத்துது, ஒவ்வொரு product template-உம் சுயாதீனமா முடிவு பண்ணும் ஒண்ணா இல்ல.

**Takeaway:** ஒரு data model-ஐ வடிவமைக்கறப்போ "Taxonomy Path Trade table-ல இருக்கணுமா Product table-ல இருக்கணுமா"ன்னு யோசிச்சா, பதில் **Product** — trades அதை reference வழியா inherit பண்ணுது, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §1-ஓட Asset Class → Product → Instrument → Trade hierarchy ஏற்கனவே product-defining attributes Trade layer-ல இல்ல, Product layer-ல இருக்குன்னு நிறுவியது போலவே. ஒரே product வடிவத்தோட trades-க்கு வேறு வேறு taxonomy path-ஐ சேமிக்கும் (அனுமதிக்கும்) system-ல ஒரு data-integrity bug இருக்கு, ஒரு நெகிழ்வான அம்சம் இல்ல.

---

## 9. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** இந்த curriculum-ல மற்ற ஒவ்வொரு reference module போலவும், இது அவ்வப்போது திருத்தப்படும் விதிகளை மறுசொல்லாம, முதன்மை மூலங்களை சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **ISDA (International Swaps and Derivatives Association)** | Publishes the Product Taxonomy itself (v1.0, v2.0, and updates), the ISDA Master Agreement, and CSA documentation | [www.isda.org](https://www.isda.org) |
| **CFTC (Commodity Futures Trading Commission)** | Dodd-Frank real-time public reporting rules (Part 43), swap data recordkeeping (Part 45), block-trade thresholds by taxonomy category, MAT determinations | [www.cftc.gov](https://www.cftc.gov) |
| **CPMI (Committee on Payments and Market Infrastructures) / IOSCO** | 2017 Technical Guidance on UPI, which adopted ISDA's taxonomy as its classification basis (Section 5) | [www.bis.org/cpmi](https://www.bis.org/cpmi), [www.iosco.org](https://www.iosco.org) |
| **ANNA-DSB** | Operates the UPI reference-data system built on this taxonomy's lineage — see the [UPI, UTI & ANNA-DSB Deep Dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.md) | [www.anna-dsb.com](https://www.anna-dsb.com) |
| **ESMA** | EU/UK EMIR technical standards — where taxonomy-derived category logic reaches EU/UK reports via the UPI rather than a direct field (Section 6's callout) | [www.esma.europa.eu](https://www.esma.europa.eu) |

**Takeaway:** ISDA-ஓட சொந்த வெளியிடப்பட்ட taxonomy ஆவணங்களை உண்மையான தற்போதைய category கட்டமைப்புக்கும் பதிப்புக்கும் source of truth-ஆவும்; CFTC-ஓட rulebook-ஐ அந்த categories US public-reporting கடமைகளை சரியா எப்படி இயக்குதுங்கறதுக்கு source of truth-ஆவும்; CPMI-IOSCO-ஓட Technical Guidance-ஐ அதே categories உலகளாவிய அளவு ஏற்கப்பட்ட UPI-க்கு அடிப்படையா எப்படி ஆனதுங்கறதை விளக்கும் பாலமாவும் நடத்துங்க — இந்த curriculum-ஓட மற்ற reference modules தங்கள் சொந்த regimes-க்கு ஏற்கனவே பயன்படுத்தற அதே மூன்று-வழி "standard, US mandate, global mandate" பிரிவு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | ஒவ்வொரு worked taxonomy path முழுக்க counterparty (Section 3); விளக்க product master-ஓட உரிமையாளர் (Section 8) |
| Goldman Sachs | Credit, FX worked examples முழுக்க counterparty (Section 3) |
| Northern Rail Corp | Credit worked example-ஓட reference entity (Section 3, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9) |
| ISDA | Product Taxonomy தானே, தனியா Master Agreement-ஐயும் வெளியிட்டு நிர்வகிக்குது (Section 1, Section 7) |
| CFTC | அதன் Dodd-Frank விதிகள் taxonomy-ஐ public reporting, MAT determinations-க்கு நேரடியா சுமை சுமக்கற US regulator (Section 6) |
| CPMI-IOSCO | ISDA-ஓட taxonomy-ஐ உலகளாவிய UPI-க்கான classification அடிப்படையா ஏற்ற standard-setters (Section 5) |
| ANNA-DSB | Taxonomy-ஓட வம்சாவளியில கட்டப்பட்ட UPI reference-data system-ஐ இயக்குது (Section 5, [UPI deep dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)-ல இருந்து cross-reference) |
| ESMA | UPI வழியா மறைமுகமா taxonomy-ஓட செல்வாக்கு reports-ஐ அடையும் EU/UK regulator (Section 6) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ரெண்டும் "product" அடுக்கில உட்கார்ந்தாலும், ISDA Product Taxonomy செய்யறதுக்கும் UPI செய்யறதுக்கும் உள்ள வித்தியாசம் என்ன?
2.  Section 2-ஐ பயன்படுத்தி, ஒரு தட்டையான code-க்குப் பதிலா taxonomy-க்கு நாலு levels தேவை ஏன் — "Swap" போன்ற ஒரு Transaction Type மதிப்பு மட்டும் தனியா இருந்தா என்ன தப்பாகும்?
3.  Section 3-ஐ பயன்படுத்தி, Bank Of Toronto-ஓட 5Y CAD IRS-க்கு முழு taxonomy path என்ன? IRS-க்கும் Equity TRS உதாரணத்துக்கும் இடையில அந்த path-ல எந்த ஒரு level வேறுபடுது?
4.  Section 4-ஐ பயன்படுத்தி, ஒரு Northern Rail Corp bond எப்போதாவது ISDA taxonomy path-ஐ பெறுமா? Northern Rail Corp-ஐ reference பண்ற CDS பெறுமா? வேறுபாட்டை விளக்குங்க.
5.  Section 5-ஐ பயன்படுத்தி, ISDA Product Taxonomy-ல இருந்து ஒரு உண்மையான வழங்கப்பட்ட UPI வரையிலான வம்சாவளியை விவரிங்க — ஒவ்வொரு அடுக்கும் அதுக்கு முந்தையதில் இல்லாத எதைச் சேர்க்குது?
6.  Section 6-ஐ பயன்படுத்தி, UPI-உடன் எந்தச் சம்பந்தமும் இல்லாத, US Dodd-Frank/CFTC regulation-ல taxonomy-ஓட ஒரு பயன்பாட்டை பெயரிடுங்க.
7.  Section 7-ஐ பயன்படுத்தி, ISDA ஒரு regulator-ஆ? ஒரு குறிப்பிட்ட trade-க்கு அதன் taxonomy-ஐ பயன்படுத்தறதை உண்மையில கட்டாயமாக்குறது எது?
8.  Section 8-ஐ பயன்படுத்தி, நல்லா வடிவமைக்கப்பட்ட data model-ல taxonomy path எங்க இருக்கு — Trade record-லயா Product record-லயா — அது operational ரீதியா ஏன் முக்கியம்?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Taxonomy *classify* பண்ணுது — அது அந்த வடிவத்தின் ஒவ்வொரு trade-ஆலும் பகிரப்படும் ஒரு category label (உதா., `Interest Rate / IRSwap / FixedFloat / Swap`). UPI *identify* பண்ணுது — அது ஒரு குறிப்பிட்ட பொருளாதார terms கலவைக்கு வழங்கப்பட்ட குறிப்பிட்ட code, taxonomy-ஓட categories-ஐ அதன் அடிப்படை classification logic-ஆ பயன்படுத்தி கட்டப்பட்டது (Section 5), ஆனா taxonomy தானே பிடிக்காத Underlier, Notional Currency, Tenor போன்ற fields-ஐயும் சேர்க்க இன்னும் போகுது.**

Section 1-ஓட takeaway இதை நேரடியா பிடிச்சுக்க வேண்டிய மிக முக்கியமான வேறுபாடா சொல்லுது, சரியா ரெண்டும் "product" அடுக்கில உட்கார்ந்து குழப்பிக்கொள்ளப்படுறதாலதான்.

**2. ஏன்னா Transaction Type மட்டும் asset classes முழுக்க தெளிவில்லாதது — Section 3-ஓட Interest Rate, Equity, Commodity worked examples மூணுக்கும் Transaction Type "Swap"; மற்ற மூணு levels (Asset Class, Base Product, Sub-Product) இல்லாம, அந்த ஒரு field-ல இருந்து மட்டும் ஒரு 5Y CAD IRS-ஐயும் ஒரு Brent Crude Oil Swap-ஐயும் பிரிச்சுப் பார்க்க வழியே இருக்காது.**

Section 2-ஓட takeaway இதே புள்ளியை சொல்லுது, Section 3-ஓட worked examples உறுதியா நிரூபிக்குது — ஐந்து trades-ல மூணு ஒரே Transaction Type மதிப்பை பகிர்கின்றன.

**3. `Interest Rate / IRSwap / FixedFloat / Swap`. Equity TRS-ஓட `Equity / Swap / PriceReturn / Swap`-ஓட ஒப்பிட்டா, கட்டமைப்பு ரீதியா சுவாரஸ்யமா வேறுபடும் level Base Product — IRSwap vs. Swap — Transaction Type ரெண்டுக்கும் ஒரே மாதிரி ("Swap"), Transaction Type மட்டும் அவற்றை வேறுபடுத்த முடியாதுன்னு மறுபடி காட்டுது.**

Section 3-ஓட worked-examples block ரெண்டு paths-ஐயும் நேரடியா பட்டியலிடுது, அதன் takeaway பகிரப்பட்ட "Swap" Transaction Type-ஐ எந்த ஒரு level-உம் தனியா போதாததற்கான காரணமா flag பண்ணுது.

**4. இல்ல — ஒரு Northern Rail Corp bond ஒருபோதும் ISDA taxonomy path-ஐ பெறாது; அதுக்குப் பதிலா ஒரு CFI code-ஐ பெறும், ஏன்னா அது ஒரு deliver பண்ணக்கூடிய security, ஒரு bilateral contract இல்ல. Northern Rail Corp-ஐ reference பண்ற CDS ஒரு taxonomy path-ஐ பெறும் (`Credit / SingleName / Corporate / CreditDefaultSwap`), ஏன்னா CDS தானே classify ஆகும் bilateral contract — taxonomy contracts-ஐ classify பண்ணுது, ஒரு contract reference பண்ணக்கூடிய அடிப்படை பொருளை ஒருபோதும் இல்ல.**

Section 4-ஓட worked comparison சரியா இந்த ஜோடியை நடத்திக் காட்டுது — அதே company, ரெண்டு முற்றிலும் வேறு classification உலகங்கள்; அதன் நுணுக்க callout ஒரு bond *மேல* derivative-ம் (ஒரு bond option/future) இன்னும் சாதாரணமா ISDA-classified ஆகும்னு இன்னும் தெளிவாக்குது; அடிப்படை cash bond தானே ஒருபோதும் ஆகாது.

**5. ISDA-ஓட taxonomy (v1.0/v2.0) அடிப்படை classification சொல்லகராதியை வழங்குது — Asset Class/Base Product/Sub-Product/Transaction Type, codes வழங்கல் இல்ல, உயிருள்ள system இல்ல. CPMI-IOSCO-ஓட 2017 Technical Guidance அந்த சொல்லகராதியை ஒரு உலகளாவிய ஒத்திசைவான standard-ஓட கட்டமைப்பு அடிப்படையா ஏற்கும். DSB அப்புறம் அதை ஒரு உயிருள்ள, query செய்யக்கூடிய, asset class-க்கு reference-data schema-வா இயக்கி, fields-ஓட ஒவ்வொரு தனித்துவ கலவைக்கும் ஒரு code-ஐ (UPI) உண்மையில வழங்குது. கடைசியா, EMIR/Dodd-Frank/மற்ற regimes-ஓட trade reports அந்த ஏற்கனவே வழங்கப்பட்ட UPI-ஐ ஒரு கட்டாய field-ஆ எடுத்துக்கும், taxonomy path-ஐ கையால மறுபடி derive பண்ணாம.**

Section 5-ஓட வம்சாவளி diagram இந்த நாலு அடுக்குகள் ஒவ்வொண்ணையும் வெளிப்படையா சொல்லுது, அதன் takeaway-ல "இலக்கணம் vs. அந்த இலக்கணத்திலிருந்து கட்டிய அகராதி" ஒப்புமையை பயன்படுத்தி.

**6. இவற்றில் எதுவும்: Dodd-Frank நிகழ்நேர பொது reporting-க்கு (Part 43) block-trade notional வரம்பை நிர்ணயிக்கறது; CFTC-ஓட சொந்தமா வெளியிடப்பட்ட swaps market statistics-க்கு swaps-ஐ bucket பண்றது (Part 45 recordkeeping); அல்லது ஒரு SEF-ஓட MAT (Made-Available-to-Trade) determination-ஐ scope பண்றது.**

Section 6-ஓட table மூணையும் UPI-ஓட 2022 அறிமுகத்துக்கு முந்தைய, அதிலிருந்து சுயாதீனமான பயன்பாடுகளா பட்டியலிடுது — taxonomy-ஓட அசல் regulatory நோக்கம், இன்னிக்கும் US regime-ல நேரடியா சுமை சுமக்குது.

**7. இல்ல — ISDA ஒரு தொழில்துறை trade association, ஒரு regulator இல்ல; taxonomy-ஐ யாரும் பயன்படுத்தணும்னு அது கட்டாயப்படுத்தறதே இல்ல. அதன் பயன்பாடு ஒரு உண்மையான regulator (Dodd-Frank-ன் கீழ CFTC) அல்லது ஒரு downstream standard (CPMI-IOSCO-ஓட UPI வழிகாட்டுதல்) அதை கோரும் இடத்துல மட்டுமே கட்டாயமாகுது.**

Section 7-ஓட diagram, takeaway இதை நேரடியா சொல்லுது, UPI deep dive-ஓட Section 4 ஏற்கனவே DSB-க்கு நிறுவிய அதே infrastructure-vs-mandate வேறுபாட்டை, ஒரு அடுக்கு மேல வரைந்து.

**8. Product record-ல, Trade record-ல இல்ல — trades ஒரு ஏற்கனவே இருக்கற product template-ஐ தேர்ந்தெடுத்து அதன் taxonomy path-ஐ reference மூலம் inherit பண்ணுது; trade-குறிப்பான fields (notional, counterparty, rate) அதை ஒருபோதும் பாதிக்காது. இது operational ரீதியா முக்கியம், ஏன்னா ஒரே product வடிவத்தை வெவ்வேறு trades-ல வெவ்வேறு taxonomy paths-ஐ சுமக்க அனுமதிக்கும் system-ல data-integrity bug இருக்கு; மேலும் புது-product onboarding (ஒரு புது taxonomy classification-ஐ உருவாக்கறது) ஒவ்வொரு trade-க்கும் ஒரு தடவை இல்லாம, ஒவ்வொரு product வடிவத்துக்கும் ஒரு தடவை நடக்கும், தனியான, நிர்வகிக்கப்பட்ட நிகழ்வு.**

Section 8-ஓட product-master diagram, takeaway ரெண்டும் இதை நேரடியா சொல்லுது, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §1-ஓட அசல் புள்ளியோட இணைத்து: product-defining attributes hierarchy-ஓட Product layer-ல இருக்கு, Trade layer-ல இல்ல.
