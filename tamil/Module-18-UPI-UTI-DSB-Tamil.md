# Module 18 — UPI, UTI & ANNA-DSB Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> இது **[Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.html)**, **[Module 17 — UK DMO Reporting](../docs/2-reporting/module-17-dmo-uk-reporting.html)** குடும்பத்தை சேர்ந்த **reference module**. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12 UPI, UTI-ஐ ஒரே worked example-ஓட அறிமுகப்படுத்தியது; இந்த module ஆழமா போகுது — அமைப்பு, generation விதிகள், அமைப்பை உண்மையில நடத்தும் நிறுவனம் (ANNA-DSB), எந்த regulatory regime எந்த identifier-ஐ கோருதுன்னு.
> **மூலம்:** English Module 18-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. UPI, UTI-ன்னா என்ன? — Derivatives-க்கு ஏன் புது Identifiers தேவைப்பட்டது

**Definition:** Cash securities-க்கு பல தசாப்தங்களா ஒரு ஒற்றை உலகளாவிய identifier உண்டு — **ISIN** ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2). OTC derivatives-க்கு அப்படி ஒரு சமமானது இல்லவே இல்ல, ஒரு கட்டமைப்புக் காரணத்துக்காக ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12 ஏற்கனவே நிறுவியது): ஒரு derivative-க்கு ரெண்டு parties-ஓட contract-ஐத் தவிர தனி இருப்பு இல்ல, அதனால ஒரு share அல்லது bond-க்கு இருக்கற மாதிரி ஒரு ஒற்றைக் code-ஐ பொருத்த "ஒரு instrument, பல உரிமையாளர்கள்" என்ற இயற்கையான object இல்ல. 2008 நிதி நெருக்கடி அந்த இடைவெளியை ஒரு தீவிர regulatory பிரச்சனையாக்கியது — G20-ஓட நெருக்கடிக்குப் பிந்தைய OTC derivatives சீர்திருத்தங்கள் (EMIR, Dodd-Frank-க்குப் பின்னாலுள்ள அதே Pittsburgh 2009 உறுதிமொழிகள், [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1) trade reporting-ஐ கோரியது, ஆனா வெவ்வேறு firms-ஓட reports வெவ்வேறு internal product codes-ஐ பயன்படுத்தியதால, "முழு சந்தை முழுக்க 5Y CAD interest rate swaps-ல எவ்வளவு risk இருக்கு"ன்னு ஒரு regulator-ஆல aggregate பண்ணவே முடியாத நிலை.

அந்த இடைவெளியை மூட, ரெண்டு வெவ்வேறு அடுக்குகள்ல ரெண்டு identifiers உருவாக்கப்பட்டது:

| Identifier | அடுக்கு | எதுக்கு பதில் |
|---|---|---|
| **UPI (Unique Product Identifier)** | Product | "இது *என்ன வகை* derivative?" — ஒரு product template-க்கு ஒரு code, ஆயிரக்கணக்கான சம்பந்தமில்லாத trades முழுக்க மறுபயன்பாடு |
| **UTI (Unique Transaction Identifier)** | Trade | "இது *எந்த குறிப்பிட்ட* transaction?" — ஒரு trade-க்கு ஒரு code, ஒவ்வொரு தடவையும் புதுசா generate ஆகும் |

```text
WHERE UPI AND UTI SIT — RESTATED FROM MODULE 2 §12
========================================================================

  ASSET CLASS -> PRODUCT -> [no instrument-level ID for OTC derivatives] -> TRADE
                      |                                                          |
                      v                                                          v
              UPI identifies                                        UTI identifies
              the PRODUCT TEMPLATE                                  ONE TRANSACTION
              (reused across trades)                                 (new every time)

  Both are GLOBAL standards -- not firm-specific, not jurisdiction-
  specific -- built specifically so a US regulator, an EU regulator,
  and a UK regulator looking at the SAME cross-border trade all see
  the SAME UPI and the SAME UTI on their respective reports.
```

**Takeaway:** UPI-யும் UTI-யும் ஒரே யோசனையின் ரெண்டு சுவைகள் இல்ல — அவை ரெண்டு உண்மையிலயே வேறுபட்ட aggregation பிரச்சனைகளை தீர்க்குது. UPI, ஒவ்வொரு firm-ஓட contract terms-ஐயும் படிக்காம, "இந்த PRODUCT சந்தை முழுக்க எவ்வளவு இருக்கு"ன்னு ஒரு regulator கேட்க விடுது; UTI, ஒரு regulator-ஐ (அல்லது Trade Repository-ஐ) "ரெண்டு counterparties-உம் ஒரே TRADE-ஐ நிலையாக report பண்ணாங்களா"ன்னு கேட்க விடுது — சரியா [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3 EMIR-க்கு ஏற்கனவே அறிமுகப்படுத்திய pairing/reconciliation mechanism.

---

## 2. UTI Deep Dive — அமைப்பு, Generation Waterfall & Worked Examples

**Definition:** UTI = **52 characters வரை** உள்ள ஒற்றை alphanumeric code; **20-character LEI-சார்ந்த prefix** (generate பண்ணும் entity-யை identify பண்ணும்) + generator ஒதுக்கும் **32 கூடுதல் characters வரை** unique suffix. இந்த அமைப்பு CPMI-IOSCO-ஓட 2017 "Technical Guidance: Harmonisation of the Unique Transaction Identifier"-ல இருந்து வருது — ஒவ்வொரு முக்கிய jurisdiction-ஓட UTI விதிகளும் ஒன்றிணைந்த உலகளாவிய standard.

```text
UTI ANATOMY — WORKED EXAMPLE
========================================================================

  Bank Of Toronto's LEI (illustrative, Module 4 §6): 5493001KJTIIGC8Y1R12
                                                       (exactly 20 characters)

  If Bank Of Toronto is the UTI-generating entity for a trade executed
  17-Aug-2026, it might assign the suffix "2026081700001" (its own
  internal sequencing scheme -- the generator has full discretion over
  the suffix's content, as long as it's unique):

  Full UTI:  5493001KJTIIGC8Y1R122026081700001
             \__________20 chars_______/\____14 chars____/
              Bank Of Toronto's LEI       Bank Of Toronto's own
              (identifies WHO generated    unique suffix (identifies
              this UTI)                    WHICH trade)

  -> 34 characters total here, well under the 52-character maximum --
     the suffix length is up to the generator, not fixed at 32.
```

### யார் UTI-ஐ generate பண்றாங்க — waterfall

**Definition:** ஒரு Trade Repository pair பண்ண ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3) UTI ரெண்டு counterparties-ஓட சுயாதீனமா submit பண்ணின reports-லயும் ஒரே மாதிரி இருக்கணும்கறதால, ரெண்டு பக்கத்துல யார் அதை generate பண்ணி, ஏதாவது ஒரு பக்கத்தோட reporting deadline-க்கு முன்னாடி மற்றவரோட பகிரணும்னு தீர்மானிக்க regulators-க்கு ஒரு விதி தேவைப்பட்டது. இதுதான் பொதுவா **UTI generation waterfall**-ன்னு சொல்லப்படுது — "இது பொருந்தினா, இந்த entity generate பண்ணும்; இல்லன்னா அடுத்த விதியை சோதிங்க" என்ற படிநிலை.

| முன்னுரிமை | விதி (விளக்கமா, எளிமைப்படுத்தப்பட்டது — Section 8-ஓட callout பாருங்க) | Example |
|---|---|---|
| 1 | Trade UTIs generate பண்ற ஒரு trading platform/venue-ல execute ஆனா, **venue** generate பண்ணும் | Exchange-போன்ற OTC platform execution-ல UTI-ஐ ஒதுக்குது |
| 2 | Trade (centrally) cleared ஆனா (அல்லது ஆகிட்டா), cleared leg(s)-க்கு **CCP** UTI-ஐ generate பண்ணும் | [Module 8](../docs/1-capital-markets/module-08-rates.html)-ஓட 5Y CAD IRS, LCH SwapClear-க்கு novate ஆனது — cleared ஆனப்புறம் LCH SwapClear UTI-ஐ generate பண்ணுது |
| 3 | இல்லன்னா, counterparties இடையில ஒரு படிநிலை பொருந்தும் (உதா., clearing member > swap dealer/major participant > financial counterparty > non-financial counterparty, பொருந்தும் regime-ஓட குறிப்பிட்ட விதிப்படி) | ஒரே seniority அடுக்கின் ரெண்டு dealers bilateral-ஆ trade பண்றாங்க |
| 4 | இன்னும் சமமா இருந்தா, ஒரு எளிய tie-break (உதா., எந்த counterparty-ஓட LEI அகரவரிசையில முன்னாடி வருதோ) தீர்மானிக்குது | கீழ உள்ள worked example பாருங்க |

> **Callout — இது எளிமைப்படுத்தப்பட்ட விளக்கம், உண்மையான தற்போதைய விதி text இல்ல.** EMIR, UK EMIR, Dodd-Frank/CFTC, மற்ற regimes முழுக்க சரியான waterfall சிறிய ஆனா சட்டப்படி கணிசமான வழிகள்ல வேறுபடுது, அவ்வப்போது திருத்தப்படுது. மேல உள்ள table-ஐ தர்க்கத்தின் *வடிவமா* எடுத்துக்கோங்க; ஒரு rules engine-ல encode பண்றதுக்கு முன்னாடி தற்போதைய text-ஐ Section 8-ஓட அதிகாரப்பூர்வ links-க்கு எதிரா உறுதிப்படுத்துங்க — [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §7, [Module 17 — UK DMO](../docs/2-reporting/module-17-dmo-uk-reporting.html) §2 தங்கள் சொந்த regulatory specifics-க்கு ஏற்கனவே பயன்படுத்தற அதே hedge.

```text
WORKED WATERFALL — BANK OF TORONTO'S BILATERAL CDS WITH GOLDMAN SACHS
(Module 9 §4a's Northstar Resources Inc. trade — bespoke, stays bilateral,
Module 9 §7 already established it is NOT centrally cleared)
========================================================================

  Rule 1 (venue-generated)?    NO  -- bilaterally negotiated, no platform
  Rule 2 (CCP-generated)?      NO  -- confirmed bilateral, no CCP involved
  Rule 3 (seniority hierarchy)? TIE -- both are dealers of comparable
                                       standing in this illustration
  Rule 4 (alphabetical LEI tie-break):
    Bank Of Toronto's LEI:  5493001KJTIIGC8Y1R12
    Goldman Sachs' LEI:     784F5XWPLTWKTBV3E584   (illustrative)

    "5493..." sorts BEFORE "784F..." alphanumerically
    -> BANK OF TORONTO generates the UTI, and must share it with
       Goldman Sachs before either party's own reporting deadline
       (Module 14 §3's dual-sided pairing depends on this)
```

**Takeaway:** waterfall இருக்கறது ambiguity-ஐ நீக்க, விருப்பத்துக்காக இல்ல — எந்த counterparty-உம் UTI-ஐ generate பண்ணணும்னு "விரும்பறதில்ல" (அது வெறும் நிர்வாகப் பணி), ஆனா சரியா ஒருத்தர் கட்டாயம், தீர்மானிக்கப்பட்ட முறையில, செய்யணும், அப்போதான் மற்றவர் ஊகிக்காம அதே மதிப்பை வெறுமனே பெற்று மறுபயன்படுத்தலாம். [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3-ஓட UTI-mismatch pairing-break scenario சரியா இந்த hand-off தோற்கும்போது நடக்கறது — ஒரு firm, waterfall-ஓட உண்மையான generator உருவாக்கியதை பயன்படுத்தாம, தன் சொந்த UTI-ஐ generate பண்றது.

### கூடுதல் உதாரணம் — Waterfall-ஐ ஒரு flow-chart மாதிரி

மேல உள்ள worked example-ஐ எளிய கேள்விகளா மாத்தினா:

| கேள்வி | Bank Of Toronto ↔ Goldman Sachs CDS | Bank Of Toronto ↔ LCH SwapClear IRS (cleared) |
|---|---|---|
| Venue UTI generate பண்ணுதா? | இல்ல (bilateral) | இல்ல (bilateral negotiate) |
| CCP-யில் clear ஆச்சா? | இல்ல | ஆமா → **CCP generate பண்ணும்** |
| இல்லன்னா seniority சமமா? | ஆமா (சமம்) | — |
| LEI tie-break | `5493…` < `784F…` → **Bank Of Toronto generate** | — |

---

## 3. UPI Deep Dive — அமைப்பு, Reference Data & Asset Classes முழுக்க Worked Examples

**Definition:** UPI = **ISO 4914** standard-ன் கீழ வழங்கப்படும், **12-character alphanumeric code** (ISIN போன்ற வடிவம், check digit உட்பட). ISIN போலவே, code தானே ஒரு lookup key மட்டும் — உண்மை சாராம்சம் அதுக்குப் பின்னால இருக்கற **UPI Reference Data** record-ல: DSB (Section 4) பராமரிக்கும், product-ஐ விவரிக்கும் (Asset Class, Instrument Type, Underlier, Notional Currency, அந்த instrument type-க்கே உரிய மற்ற economic terms) standardized fields தொகுப்பு.

**முக்கிய விதி, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ல இருந்து மறுபடி சொல்லப்பட்டது:** ரெண்டு trades-க்கு **ஒரே UPI** வருது, ஒவ்வொரு product-defining field-உம் பொருந்தினா, அப்போ மட்டுமே. Trade-குறிப்பான விவரங்கள் — notional தொகை, விலை/rate, counterparty, trade date — வேணும்னே UPI-ஐ வரையறுக்கறதில் இருந்து விலக்கப்பட்டிருக்கு.

> **"Underlier"-ன்னா என்ன?** கீழ உள்ள ஒவ்வொரு worked example-லயும் `Underlier` field இருக்கு — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8 இந்தப் பொது கருத்தை முழுசா வரையறுக்குது (ஒரு derivative-ஓட cash flows-ஐ இயக்கும் reference asset/rate/index/entity; Notional, அளவு மட்டும்), Interest Rate template-ஓட Underlier ஆன **CORRA** உண்மையில என்ன, எப்படி கணக்கிடப்படுதுன்னும் உட்பட. CDOR-ல இருந்து CORRA-க்கு மாறிய முழுக் கதை [Module 8](../docs/1-capital-markets/module-08-rates.html) §1-ல.

### ஒவ்வொரு asset class-க்கும் ஒரு UPI reference-data template

வெவ்வேறு asset classes "product"-ஐ வேறு வேறு fields-ஆ விவரிக்குது, ஏன்னா ரெண்டு Interest Rate Swaps-ஐ "ஒரே product"ஆ ஆக்குறது, ரெண்டு CDS-ஐ "ஒரே product"ஆ ஆக்குறதுல இருந்து வேறு. இந்த curriculum-ல வேறிடத்துல ஏற்கனவே கட்டின trades-ஐ மறுபயன்படுத்தி:

```text
FIVE ASSET CLASSES, FIVE UPI REFERENCE-DATA SHAPES
========================================================================

  INTEREST RATE  (Module 8's 5Y CAD IRS -- also Module 2 §12's example)
    Asset Class: Interest Rate | Instrument Type: Swap, Fixed-Float
    Underlier: CORRA | Notional Currency: CAD | Tenor: 5 Years
    -> UPI: QZ9F4K2XR7T1 (illustrative)

  CREDIT  (Module 9 §4a's Northstar Resources Inc. CDS)
    Asset Class: Credit | Instrument Type: Single-Name CDS
    Reference Entity: Northstar Resources Inc. | Seniority: Senior
    Unsecured | Notional Currency: USD | Tenor: 5 Years
    -> UPI: RT4M8LN2WD93 (illustrative)

  FOREIGN EXCHANGE  (Module 6's 3M USD/CAD Forward)
    Asset Class: Foreign Exchange | Instrument Type: Forward, Deliverable
    Currency Pair: USD/CAD | Settlement Type: Physical
    -> UPI: FX7K1PXQ0H26 (illustrative)

  EQUITY  (Module 2 §11's Total Return Swap on Apple Inc. shares)
    Asset Class: Equity | Instrument Type: Swap, Price Return
    Underlier: Apple Inc. common stock | Return Type: Price + Dividend
    -> UPI: EQ3T9YBV5C48 (illustrative)

  COMMODITY  (Module 7's 1Y Brent Crude Oil Swap)
    Asset Class: Commodity | Instrument Type: Swap
    Underlier: ICE Brent | Sub-Asset: Energy, Oil | Notional Currency: USD
    -> UPI: CM6X2FGL8N71 (illustrative)
```

**Takeaway:** Credit template-ல "Reference Entity" ஒரு product-defining field-ஆ இருக்கறதை கவனிங்க — அதாவது Northstar Resources Inc. மேல ஒரு CDS-உம் வேறொரு company மேல மற்றபடி ஒத்த ஒரு CDS-உம் **வேறு UPIs**-ஐ பெறுது, ரெண்டுமே "5Y Single-Name CDS" ஆனாலும். இது Interest Rate template-க்கு எதிர்: அங்க underlier ஒரு rate benchmark (CORRA), counterparty எதுவாயிருந்தாலும் அந்த வடிவத்தின் ஒவ்வொரு CAD swap-ஆலும் பகிரப்படுது — "எது ஒரே product"ங்கறதே asset-class-குறிப்பானது, DSB பராமரிக்கும் ஒவ்வொரு template-லயும் field-by-field வரையறுக்கப்பட்டதுன்னு நினைவூட்டல்.

### UPI கோருவது

ஒரு புது product வடிவத்துக்கு UPI தேவைப்படற firm, reference-data fields-ஐ DSB-க்கு (Section 4) அதன் platform அல்லது API வழியா submit பண்ணுது. அதே reference-data கலவைக்கு ஏற்கனவே UPI file-ல இருக்கான்னு DSB சரிபார்க்குது — இருந்தா, ஏற்கனவே இருக்கற code-ஐ திருப்புது (இதனாலதான் ஆயிரக்கணக்கான சம்பந்தமில்லாத trades சரியாவே ஒரு UPI-ஐ பகிர்கின்றன, Section 1); இல்லன்னா புதுசு வழங்குது. இந்த lookup-or-create நடத்தை, ஒரு உண்மையிலயே புது security-க்கு ISIN-வழங்கும் National Numbering Agency பின்பற்றும் அதே pattern, ஒரு security-ஓட issuance terms-க்குப் பதிலா derivatives-குறிப்பான reference-data schema மேல ஓடுது.

---

## 4. ANNA-DSB — இதை யார் நடத்தறாங்க, Governance & உண்மையில எப்படி வேலை செய்யுது

**Definition:** **ANNA (Association of National Numbering Agencies)** = 1990-களில் இருந்து **ISIN** வழங்கலை ஒருங்கிணைக்கும் உலகளாவிய அமைப்பு, ஒவ்வொரு நாட்டோட சொந்த National Numbering Agency வழியா வேலை செய்யுது (உதா., Canadian ISINs-ஐ வழங்கும் agency, ஜெர்மன் securities-க்கு வேறொன்னு, இப்படி). 2017-ல ANNA ஒரு அர்ப்பணிப்பு subsidiary-ஐ — **DSB (Derivatives Service Bureau Limited)** — நிறுவியது; OTC derivatives-ஓட கிட்டத்தட்ட எல்லையற்ற underliers, terms கலவை, cash securities-க்கு வேலை செய்யும் ஒரு நாடுக்கு-ஒரு-National-Numbering-Agency மாதிரிக்குப் பொருந்தாததால்.

```text
ANNA-DSB — ONE ORGANIZATION, TWO IDENTIFIER OUTPUTS
========================================================================

  ANNA (Association of National Numbering Agencies)
    -> coordinates ISIN issuance globally, via national agencies,
       for CASH securities (Module 2 §2)
       |
       v
  DSB (Derivatives Service Bureau Limited) -- ANNA's subsidiary,
  est. 2017, purpose-built for OTC DERIVATIVES:

    OUTPUT 1 -- ISIN-for-derivatives (since 2017/2018)
      Issued specifically for EU MiFID II/MiFIR pre- and post-trade
      TRANSPARENCY requirements (Section 5) -- an OTC derivative
      gets an ISIN-shaped code from the DSB, distinct from a cash
      security's ISIN, but structurally an ISIN

    OUTPUT 2 -- UPI (since 2022)
      Issued for GLOBAL TRADE REPORTING under the FSB's 2020
      mandate (implementing CPMI-IOSCO's Technical Guidance) --
      EMIR, UK EMIR, Dodd-Frank/CFTC, and other G20 jurisdictions'
      reporting regimes all consume this SAME UPI (Section 6)
```

**Governance:** **Financial Stability Board (FSB)** DSB-ஐ ஒரே உலகளாவிய UPI Service Provider-ஆ நியமித்தது, **CPMI (Committee on Payments and Market Infrastructures)**-உம் **IOSCO (International Organization of Securities Commissions)**-உம் கூட்டா உருவாக்கிய technical வழிகாட்டுதலைத் தொடர்ந்து — [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §8 ஏற்கனவே உலகளாவிய market-infrastructure standards-ஐ மேற்பார்வை பண்ணும் அவங்க பரந்த பாத்திரத்துக்கு அறிமுகப்படுத்திய அதே standard-setters. DSB தானே **லாப நோக்கமில்லாத, செலவு-மீட்பு** அடிப்படையில இயங்குது: UPI **reference data lookups இலவசமாவும் பொதுவாகவும் கிடைக்குது** (வேணும்னே செய்யப்பட்ட regulatory வடிவமைப்புத் தேர்வு, ஏன்னா முழு நோக்கமே பொது/regulatory வெளிப்படைத்தன்மை), UPI உருவாக்கப்படணும்னு *புது* product வரையறைகளை submit பண்ணும் firms DSB-ஓட செயல்பாடுகளுக்கு நிதியளிக்கும் பங்களிப்பு கட்டண அமைப்புக்கு உட்படலாம்.

**Takeaway:** DSB தானே ஒரு regulator இல்ல — அது market infrastructure, standard-setters-ஓட (FSB/CPMI-IOSCO) ஆணைக்குக் கீழ இயங்குது; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html), [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) ஏற்கனவே infrastructure providers-ஐ (CSDs, Trade Repositories) அவற்றை மேற்பார்வை பண்ணும் regulators-ல இருந்து வேறுபடுத்துவது போலவே. "DSB X-ஐ கோருது"ங்கறதை "FCA/CSA/SEC X-ஐ கோருது"ங்கறதோட குழப்பிக்கறது ஒரு பொதுவான category தவறு — DSB standard-setters, தனித்தனி jurisdictions-ஓட regulators ஆணையிட்டதை அமல்படுத்துது, தானே reporting கடமையை சுயமா நிர்ணயிக்கறதில்ல.

---

## 5. UPI vs. DSB-ஓட மற்ற Output — MiFIR-க்கான ISINs

**Definition:** DSB பத்தி இது மிகவும் குழப்பமான உண்மை, தெளிவாச் சொல்லத் தகுந்தது: **சரியா அதே reference-data record ரெண்டு வேறு identifier outputs-ஐ, ரெண்டு வேறு regimes-க்கு, ரெண்டு வேறு நோக்கங்களுக்கு சேவை செய்ய உருவாக்கலாம்** — EU MiFIR வெளிப்படைத்தன்மைக்கு ஒரு ISIN-வடிவ code, உலகளாவிய trade reporting-க்கு ஒரு UPI.

```text
ONE OTC DERIVATIVE, ONE REFERENCE-DATA RECORD, TWO REGULATORY OUTPUTS
========================================================================

  Bank Of Toronto's 5Y CAD IRS with Goldman Sachs (Module 8 §4a),
  if EITHER counterparty is EU/UK MiFID-scope:

  -> MiFIR pre/post-trade TRANSPARENCY (Module 14 §4's purpose --
     "did anyone behave abusively," restated for derivatives):
     the DSB issues an OTC-DERIVATIVE ISIN for this product, used
     on MiFIR transaction reports and public transparency data

  -> EMIR TRADE REPORTING (Module 14 §3's purpose -- "how much
     systemic derivatives risk exists"):
     the SAME trade's product is ALSO assigned a UPI, used on the
     EMIR report submitted to the Trade Repository

  -> TWO DIFFERENT DSB-ISSUED CODES, on TWO DIFFERENT REPORTS, for
     the IDENTICAL underlying product -- because MiFIR and EMIR are
     genuinely separate regimes (Module 14 §1's "one trade, five
     regimes" theme, restated at the identifier level)
```

**Takeaway:** "DSB ஒரு product-க்கு ஒரு code தருது"ன்னு கருதற reporting engineer, சட்டப்படி தேவைப்படற ரெண்டு identifiers-ல ஒண்ணை அமைதியா விட்டுடும் system-ஐ கட்டுவார் — MiFIR-scope firms-க்கு ஒரே underlying trade-ல, ஒரே மூல நிறுவனத்தில இருந்து, OTC-derivative ISIN (transparency reporting-க்கு) UPI (EMIR trade reporting-க்கு) ரெண்டும் தேவை, ஏன்னா [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1 மூல data ஒண்ணா இருக்கறதால மட்டும் regulatory நோக்கங்கள் ஒண்ணா ஒடுங்காதுன்னு ஏற்கனவே நிறுவியது.

---

## 6. UPI/UTI உண்மையில எங்க பயன்படுத்தப்படுது — Regime-by-Regime Map

**Definition:** இந்த curriculum உள்ளடக்கிய ஒவ்வொரு reporting regime-உம் UPI, UTI-ஐ ஒரே மாதிரி பயன்படுத்தறதில்ல — சில ரெண்டையும் பயன்படுத்தும், சில UTI மட்டும், சில ரெண்டுமே இல்ல. ஒரு reporting field-ஐ sanity-check பண்ண இந்த table வேகமான வழி.

| Regime | UTI பயன்படுத்துமா? | UPI பயன்படுத்துமா? | குறிப்புகள் |
|---|---|---|---|
| **EMIR / UK EMIR** ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3) | ஆமா — pairing key | ஆமா — EMIR REFIT/UPI phase-in-ல இருந்து கட்டாயம் | இந்த module-ஓட Section 2/3 worked examples சுத்தி கட்டப்பட்ட regime |
| **Dodd-Frank / CFTC** ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §5) | ஆமா | ஆமா — CFTC UPI-ஐ ஏற்றது, தன் பழைய internal product taxonomy-ஐ மாற்றி | ஒரு SDR (Swap Data Repository)-க்கு US swap data reporting |
| **SFTR / UK SFTR** ([Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §6) | **ஆமா** — அதே UTI கருத்து, அதே pairing நோக்கம் | **இல்ல** — SFTs அதுக்குப் பதிலா SFTR-ஓட சொந்த "Type of SFT" codes-ஐ (`REPO`, `SBSC`, `SLEB`, `MGLD` — [Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §2) பயன்படுத்தும், ஏன்னா SFTs UPI கட்டப்பட்ட OTC-derivative product taxonomy வழியா வகைப்படுத்தப்படறதில்ல | UTI-யும் UPI-யும் உண்மையிலயே சுயாதீனம்ங்கறதுக்கு சுத்தமான விளக்கம் — ஒரு regime மற்றது இல்லாம ஒண்ணை தேவைப்படுத்தலாம் |
| **MiFID II / MiFIR** (மேல Section 5) | பொதுவா முதன்மை key இல்ல — MiFIR transaction reports transaction reference number-ஐயும் OTC-derivative ISIN-ஐயும் key-ஆ எடுக்கும் | **DSB-ஓட மற்ற output-ஐ பயன்படுத்தும்** — ஒரு OTC-derivative ISIN, UPI இல்ல (Section 5) | UPI பயன்பாட்டோட மிக அதிகம் குழப்பிக்கொள்ளப்படும் regime, சரியா அது அதே மூல நிறுவனம் என்பதால |
| **UK DMO reporting** ([Module 17 — UK DMO Reporting](../docs/2-reporting/module-17-dmo-uk-reporting.html)) | இல்ல | இல்ல | OTC derivative regime-ஏ இல்ல — gilts ISIN-ஆல identify ஆகும் cash securities, DMO reporting derivatives trade reporting இல்ல, வணிகம்/ஒப்பந்தம் சார்ந்தது ([Module 17 — UK DMO Reporting](../docs/2-reporting/module-17-dmo-uk-reporting.html) §1) |

**Takeaway:** "இந்த trade-க்கு UPI தேவையா?"-உம் "இந்த trade-க்கு UTI தேவையா?"-உம் ரெண்டு தனி yes/no கேள்விகள், [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1-ஓட "இந்த trade reportable-ஆ?" ஐந்து regimes-க்கு எதிரா ஐந்து தனி yes/no கேள்விகளா இருந்த மாதிரியே — SFTR நிரூபிக்குது: ஒரு regime product-template aggregation (UPI) இல்லாம dual-sided pairing (UTI)-ஐ தேவைப்படுத்தலாம், ஏன்னா ஒரு SFT-ஓட "இது என்ன வகை product" கேள்விக்கு ஏற்கனவே ஒரு மிகச் சிறிய, SFTR-குறிப்பான code set பதில் சொல்லிடுது.

---

## 7. Worked Example — ஒரு Trade-ஓட முழு Identifier Stack, ஆரம்பம் முதல் முடிவு வரை

**Definition:** இந்த module-உம் [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-உம் உள்ளடக்கிய ஒவ்வொரு identifier-ஐயும் ஒரு trade record-ல இழுத்து, முழு stack துண்டு துண்டா இல்லாம, கூடியதா பார்க்கலாம்.

```text
BANK OF TORONTO'S 5Y CAD IRS WITH GOLDMAN SACHS — FULL IDENTIFIER STACK
========================================================================
(Module 8 §4a's trade, novated to LCH SwapClear per Module 8 §5)

  Trade ID         : BOT-2026-000514        (Bank Of Toronto's own
                                              internal reference only)

  UTI              : generated by LCH SwapClear once cleared (Section 2's
                     waterfall Rule 2 -- CCP-generated for cleared trades),
                     e.g. 549300LCHXXXXXXXXX2026081700099 (illustrative)

  UPI              : QZ9F4K2XR7T1 (illustrative -- Section 3's Interest
                     Rate template: 5Y CAD Fixed-Float Swap vs CORRA)

  Counterparty LEIs: Bank Of Toronto  5493001KJTIIGC8Y1R12
                     Goldman Sachs    784F5XWPLTWKTBV3E584 (illustrative)

  OTC-derivative ISIN (Section 5, ONLY if MiFIR-scope):
                     a separate DSB-issued ISIN-shaped code, distinct
                     from the UPI, used solely on MiFIR reports

  -> FIVE different identifiers on one trade, each answering a
     different question: Trade ID (internal), UTI (which specific
     transaction, for EMIR pairing), UPI (which product template,
     for EMIR aggregation), LEIs (who is trading), OTC-derivative
     ISIN (which product, for MiFIR transparency) -- restating
     Module 4 §6's "four completely different identifiers, four
     completely different questions" pattern, now with the
     derivative-specific layer fully populated
```

**Takeaway:** இந்த record-ல எந்த ஒரு identifier-உம் மற்றொண்ணோட தேவையற்றதில்ல — பல identifiers மேலோட்டமா "product-ஐ identify பண்ணுது"ன்னாலும்: UPI-யும் OTC-derivative ISIN-உம் ரெண்டும் ஒரே product வடிவத்தை விவரிக்குது, ஆனா infrastructure-ஐ பகிராத ரெண்டு வேறு regimes-க்காக இருக்கு (Section 5); UTI-யும் Trade ID-யும் ரெண்டும் ஒரே தனி transaction-ஐ identify பண்ணுது, ஆனா ரெண்டு வேறு scopes-ல (உலகளாவிய/regulatory vs. internal/firm-குறிப்பான, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ஓட அசல் வேறுபாடு).

---

## 8. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §9, [Module 17 — UK DMO Reporting](../docs/2-reporting/module-17-dmo-uk-reporting.html) §11 போல, இந்த module அவ்வப்போது திருத்தப்படும் விதிகளை மறுசொல்லாம, முதன்மை மூலங்களை சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **ANNA-DSB** | UPI reference data lookup (free), OTC-derivative ISIN issuance, UTI-related guidance, technical onboarding | [www.anna-dsb.com](https://www.anna-dsb.com) |
| **CPMI (Committee on Payments and Market Infrastructures)** | Joint technical guidance (with IOSCO) on UTI and UPI harmonization | [www.bis.org/cpmi](https://www.bis.org/cpmi) |
| **IOSCO** | Co-author of the UTI/UPI technical guidance; global securities-regulator standard-setter | [www.iosco.org](https://www.iosco.org) |
| **FSB (Financial Stability Board)** | Designated the DSB as global UPI Service Provider; oversees the broader G20 OTC derivatives data-harmonization workstream | [www.fsb.org](https://www.fsb.org) |
| **ESMA** | EU technical standards requiring UPI under EMIR REFIT, and the OTC-derivative ISIN requirement under MiFIR | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **CFTC** | US technical standards requiring UPI/UTI under Dodd-Frank swap data reporting | [www.cftc.gov](https://www.cftc.gov) |

**Takeaway:** UPI reference-data schema-ஓட சரியான தற்போதைய வடிவத்துக்கும் lookup tool-க்கும் DSB-ஓட சொந்த தளத்தை source of truth-ஆவும்; UTI/UPI கட்டமைப்பு standards-க்கு CPMI-IOSCO-ஓட வெளியிடப்பட்ட technical வழிகாட்டுதலை source of truth-ஆவும்; அந்த standard ஒரு குறிப்பிட்ட jurisdiction-ல ஒரு report-ல கட்டாய field-ஆ எப்போ, எப்படி ஆகுதுங்கறதுக்கு ஒவ்வொரு jurisdiction-ஓட சொந்த regulator-ஐயும் (ESMA, CFTC, FCA) source of truth-ஆவும் நடத்துங்க — [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §9-ஓட takeaway வேறு regime-க்கு ஏற்கனவே நிறுவிய அதே மூன்று-வழிப் பிரிவு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Section 2-ஓட bilateral CDS waterfall உதாரணத்துல UTI-generating entity; ஒவ்வொரு worked identifier stack முழுக்க counterparty |
| Goldman Sachs | ஒவ்வொரு worked example முழுக்க counterparty; Section 2-ல alphabetical LEI tie-break-ல தோற்குது |
| LCH SwapClear | Bank Of Toronto-ஓட cleared IRS-க்கு UTI-ஐ generate பண்ணும் CCP (Section 2, Section 7) |
| ANNA (Association of National Numbering Agencies) | உலகளாவிய ISIN-ஒருங்கிணைப்பு அமைப்பு; DSB-ஓட parent நிறுவனம் (Section 4) |
| DSB (Derivatives Service Bureau) | UPI-யையும் (உலகளாவிய trade reporting), OTC-derivative ISINs-ஐயும் (MiFIR transparency) வழங்குது — Sections 3–5 |
| FSB / CPMI-IOSCO | DSB இயங்கும் ஆணையைக் கொடுத்த standard-setters (Section 4) |
| ESMA / CFTC | தங்கள் சொந்த reporting regimes-ல UPI/UTI/OTC-ISIN fields-ஐ கட்டாயமாக்கும் jurisdiction-குறிப்பான regulators (Section 6) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ஒரு bond அல்லது share செய்யற மாதிரி, ஒரு derivative வெறுமனே ISIN system-ஐ ஏன் மறுபயன்படுத்த முடியாது?
2.  Section 2-ஐ பயன்படுத்தி, விளக்க UTI `5493001KJTIIGC8Y1R122026081700001`-ஐ அதன் ரெண்டு கட்டமைப்பு பகுதிகளா பிரிச்சு, ஒவ்வொண்ணும் எதை identify பண்ணுதுன்னு சொல்லுங்க.
3.  Section 2-ஓட waterfall-ஐ பயன்படுத்தி, bilateral Northstar Resources Inc. CDS உதாரணத்துல Goldman Sachs இல்லாம Bank Of Toronto UTI-ஐ generate பண்றது ஏன்? அதே trade centrally cleared-ஆ இருந்திருந்தா என்ன மாறும்?
4.  Section 3-ஐ பயன்படுத்தி, Northstar Resources Inc. மேல ஒரு CDS-உம் முற்றிலும் வேறொரு company மேல மற்றபடி ஒத்த CDS-உம் எப்போதாவது ஒரு UPI-ஐ பகிருமா? ஏன்/ஏன் இல்ல, Interest Rate template-ஓட நடத்தையோட ஒப்பிட்டு?
5.  Section 4-ஐ பயன்படுத்தி, DSB ஒரு regulator-ஆ? இல்லன்னா அது என்ன? அதன் identifiers-ஐ பயன்படுத்தணும்னு firms-ஐ உண்மையில கட்டாயப்படுத்துறது யார்?
6.  Section 5-ஐ பயன்படுத்தி, ஒரு MiFID-scope OTC interest rate swap-க்கு, trade-ஓட product-க்கு உண்மையில எத்தனை DSB-வழங்கிய identifiers தேவை? ஒவ்வொண்ணும் எதுக்கு பயன்படுத்தப்படுது?
7.  Section 6-ஐ பயன்படுத்தி, SFTR தன் reports-ல UPI-ஐ கோருதா? UTI-ஐ கோருதா? SFTR-ஓட சொந்த product-classification அணுகுமுறையை வெச்சு வித்தியாசத்தை விளக்குங்க.
8.  Section 7-ஐ பயன்படுத்தி, ரெண்டும் ஒரே underlying product-ஐ விவரிச்சாலும், OTC-derivative ISIN UPI-க்கு தேவையற்றது இல்லாதது ஏன்?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. ஏன்னா ஒரு ISIN எந்த ஒரு trade-ஐயும் சாராம இருக்கற ஒரு security-ஐ identify பண்ணுது (ஒரு share அல்லது bond இப்போ யார் வெச்சிருந்தாலும் தொடர்ந்து இருக்கும்) — ஒரு derivative-க்கு ரெண்டு parties-ஓட contract-ஐத் தவிர தனி இருப்பு இல்ல ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12), அதனால cash security-க்கு நடக்கற மாதிரி ஒரு ISIN-பாணி code பொருந்த ஒரே நிலையான "instrument" object இல்ல.**

Section 1 இதை நேரடியா சொல்லுது, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12 UPI ஏன் *product* அடுக்கில — cash instruments-க்கு ISIN உட்காரும் இடத்துக்கு ஒரு படி மேல — உட்கார வேண்டியிருக்குன்னு சொன்ன அதே கட்டமைப்புப் புள்ளிக்குத் திரும்பி.

**2. `5493001KJTIIGC8Y1R12` (முதல் 20 characters) generate பண்ற entity-யோட LEI — இந்த உதாரணத்துல Bank Of Toronto. `2026081700001` (மீதி characters) generator-ஓட சொந்த unique suffix, இந்த ஒரு குறிப்பிட்ட trade-ஐ identify பண்ணுது.**

Section 2-ஓட "UTI Anatomy" diagram இந்த சரியான பிரிவைச் சொல்லுது — LEI-சார்ந்த prefix "யார் இந்த UTI-ஐ generate பண்ணாங்க"ன்னு பதில் சொல்லுது, suffix (உள்ளடக்கம் முழுக்க generator-ஓட விருப்பம், unique ஆ இருக்கும் வரை) "எந்த trade"ன்னு பதில் சொல்லுது.

**3. ஏன்னா trade bilateral (cleared இல்ல, venue-ல execute ஆகலை), அதனால waterfall Rules 1, 2-ஐ தாண்டி Rule 3-க்கு (சமம், ஏன்னா ரெண்டும் இந்த விளக்கத்துல ஒத்த seniority-ஆ dealers) அப்புறம் Rule 4-ஓட alphabetical LEI tie-break-க்கு போகுது — Bank Of Toronto-ஓட LEI (`5493...`) Goldman Sachs-ஓடதுக்கு (`784F...`) முன்னாடி sort ஆகுது. அதே trade centrally cleared-ஆ இருந்திருந்தா, Rule 2 முதல்ல பொருந்தி, CCP தானே UTI-ஐ generate பண்ணும், சரியா Bank Of Toronto-ஓட 5Y CAD IRS LCH SwapClear-க்கு novate ஆனப்புறம் ஏற்கனவே நடக்கறது போல (Section 7).**

Section 2-ஓட worked waterfall உதாரணம் சரியா இந்த நீக்கத்தை, விதி விதியா நடத்திக் காட்டி, CCP-generation சந்தர்ப்பம் cleared IRS-க்கு ஏற்கனவே நிறுவப்பட்ட அதே mechanic-னு வெளிப்படையா குறிக்குது.

**4. இல்ல — அவை UPI-ஐ பகிராது, ஏன்னா Section 3-ஓட Credit reference-data template "Reference Entity"-ஐ product-defining fields-ல ஒண்ணா சேர்த்திருக்கு, அதனால வேறொரு company மேல CDS ஆரம்பத்துலயே "ஒவ்வொரு product-defining field-உம் பொருந்தணும்" சோதனையில தோற்குது. இது Interest Rate template-க்கு மாறுபடுது: அங்க underlier ஒரு குறிப்பிட்ட company இல்லாம, பகிரப்பட்ட rate benchmark (CORRA), அதனால அதே benchmark மேல பல சம்பந்தமில்லாத counterparties-ஓட trades ஒரு UPI-ஐ பகிருது.**

Section 3-ஓட takeaway இதை வெளிப்படையா சொல்லுது: "எது ஒரே product"ங்கறது asset-class-குறிப்பானது, ஒவ்வொரு template-லயும் field-by-field வரையறுக்கப்பட்டது — Credit-ஓட template reference entity-ஐ product அடையாளத்தின் பகுதியாவே ஆக்குது, Interest Rate-ஓட template product-ஐ எந்த ஒரு counterparty-குறிப்பான entity-யோடும் பிணைக்கறதில்ல.

**5. இல்ல — DSB market infrastructure, regulator இல்ல. இது FSB-ஓட ஆணையின் கீழ (அதை உலகளாவிய UPI Service Provider-ஆ நியமித்தது), CPMI-IOSCO-ஓட technical வழிகாட்டுதலின் கீழ இயங்குது; தனித்தனி jurisdictions-ஓட சொந்த regulators (ESMA, CFTC, FCA, முதலியன) தான் DSB-வழங்கிய identifier-ஐ தங்கள் jurisdiction-ல ஒரு report-ல கட்டாய field-ஆ ஆக்குறவங்க.**

Section 4-ஓட takeaway இதை நேரடியா சொல்லுது, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html), [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §8 CSDs, Trade Repositories-க்கு ஏற்கனவே நிறுவிய அதே infrastructure-vs-regulator வேறுபாட்டை வரைந்து — "DSB X-ஐ கோருது"ங்கறதை "ஒரு regulator X-ஐ கோருது"ங்கறதோட குழப்பிக்கறது ஒரு பொதுவான category தவறுன்னு flag பண்ணப்படுது.

**6. ரெண்டு — ஒரு UPI (EMIR trade reporting-க்கு, ரெண்டு counterparties-ல ஒண்ணு EU/UK-scope என்றா) + ஒரு தனி OTC-derivative ISIN (MiFIR pre/post-trade transparency reporting-க்கு); ரெண்டும் ஒரே அடிப்படை reference-data record-ல இருந்து DSB-ஆல வழங்கப்பட்டது, ஆனா முற்றிலும் வேறு ரெண்டு regimes-க்கு சேவை செய்யுது.**

Section 5-ஓட diagram இதை துல்லியமா சொல்லுது: "ஒரே underlying product-க்கு, ரெண்டு வெவ்வேறு reports-ல, ரெண்டு வெவ்வேறு DSB-வழங்கிய codes" — "DSB ஒரு product-க்கு ஒரு code"ன்னு கருதற reporting engineer, சட்டப்படி தேவைப்படும் ரெண்டு identifiers-ல ஒண்ணை அமைதியா under-report பண்ணுவார்.

**7. SFTR ஒரு UTI-ஐ கோருது (EMIR அதைப் பயன்படுத்தும் அதே dual-sided pairing நோக்கத்துக்கு) ஆனா UPI-ஐ கோருதில்ல — SFTs அதுக்குப் பதிலா SFTR-ஓட சொந்த "Type of SFT" codes வழியா (`REPO`, `SBSC`, `SLEB`, `MGLD`) வகைப்படுத்தப்படுது, ஏன்னா securities financing transactions UPI விவரிக்க கட்டப்பட்ட OTC-derivative product taxonomy வழியா ஓடறதில்ல.**

Section 6-ஓட table இதை நேரடியா சொல்லுது, SFTR-ஐ "UTI-யும் UPI-யும் உண்மையிலயே சுயாதீனம்ங்கறதுக்கு சுத்தமான விளக்கம் — ஒரு regime மற்றது இல்லாம ஒண்ணை தேவைப்படுத்தலாம்"ன்னு flag பண்ணி, [Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §2-ஓட சொந்த SFT-type-code table-ஐ cross-reference பண்ணி.

**8. ஏன்னா அவை reporting infrastructure-ஐ பகிராத கட்டமைப்பு ரீதியா தனி ரெண்டு regimes-க்காக இருக்கு — EMIR (ஒரு UPI-ஐ விரும்புது, ஒரு Trade Repository-க்கு submit ஆகுது), MiFIR (ஒரு OTC-derivative ISIN-ஐ விரும்புது, pre/post-trade transparency-க்கு submit ஆகுது) — அதனால ஒரு MiFID-scope firm-க்கு ஒரே trade-ல ரெண்டு codes-உம் உண்மையிலயே தேவை, ஒவ்வொரு regime-ஓட சொந்த report-க்கு ஒண்ணு, நகலா இல்லாம, ரெண்டு தனி கட்டாய fields-ஆ.**

Section 7-ஓட takeaway, Section 5-ஓட புள்ளியை முழு identifier stack-ஓட context-ல மறுபடி சொல்லுது: worked trade record-ல எந்த identifier-உம் உண்மையிலயே மற்றொண்ணோட தேவையற்றதில்ல, மேலோட்டமா "அதே product"ஐ விவரிக்கும் ரெண்டுகூட, ஏன்னா ஒவ்வொண்ணும் வேறு regime-ஓட சொந்த reporting pipeline-க்கு பதில் சொல்லுது.
