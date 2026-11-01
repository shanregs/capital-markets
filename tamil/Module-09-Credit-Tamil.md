# Module 9 — Credit (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Asset Class Roadmap 6 of 6** — Equity → Fixed Income → FX → Commodities → Rates → **Credit**. இத்தோட roadmap முடியுது.
> **Work data model:** இந்த module taxonomy-ஓட **"6. Credit"** node-க்கு map ஆகுது — ஒரே product, Credit Default Swap, தன் சொந்த Trade + Valuation Avro schema-வோட.
> **மூலம்:** English Module 9-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Credit-ன்னா என்ன? — Theory & Terminology

**Definition:** Credit, ஒரு தனி asset class-ஆ, **default risk-ஐயே** trade பண்றது — ஒரு குறிப்பிட்ட borrower ("Reference Entity") தான் கொடுக்க வேண்டியதை கொடுக்கத் தவறும் risk — அந்த borrower-ஓட உண்மையான கடனை வாங்கி வெச்சிருக்கறதுல இருந்து தனியா. [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) credit risk-ஐ ஒரு bond-ஓட ஒரு *அம்சமா* அறிமுகப்படுத்திச்சு (corporate bond-க்கு government bond-க்கு இல்லாத credit risk உண்டு, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §2); இந்த module அந்த risk-ஐ தனியா transfer பண்ணவே உருவாக்கப்பட்ட ஒரு product பத்தியது, underlying bond-ஐ யாரும் வாங்கவோ விற்கவோ தேவையில்லாம. முக்கிய product **Credit Default Swap (CDS)** — பொருளாதார ரீதியா insurance-க்கு மிக நெருக்கம்: ஒரு தரப்பு periodic premium கொடுக்கும், இன்னொரு தரப்பு Reference Entity-க்கு ஒரு வரையறுக்கப்பட்ட கெட்ட நிகழ்வு நடந்தா payout கொடுக்கும்.

| Term | அர்த்தம் |
|---|---|
| Reference Entity | CDS-ஓட default risk-ஐ reference பண்ற குறிப்பிட்ட company அல்லது sovereign — உதா., ஒரு corporate borrower |
| Reference Obligation | Reference Entity வெளியிட்ட ஒரு குறிப்பிட்ட bond; settlement நோக்கத்துக்கு "கடன்"னா எதுன்னு வரையறுக்க பயன்படுது |
| Credit Event | CDS payout தூண்டும் வரையறுக்கப்பட்ட trigger (default, bankruptcy, அல்லது சில contracts-ல restructuring) |
| Running Coupon | Protection buyer கொடுக்கற periodic premium — 2009-ஓட ISDA "Big Bang" Protocol-ல இருந்து, சுதந்திரமா quote பண்ணாம, நிலையான மட்டங்களுக்கு (பொதுவா 100bps அல்லது 500bps) standardize ஆனது |
| Upfront Payment | சந்தையோட உண்மையான credit spread-க்கும் standardized Running Coupon-க்கும் இடையிலான இடைவெளியை சரிக்கட்டும் ஒரு தடவை payment, எந்த திசையிலும் (Section 4) |
| Recovery Rate | Default-க்கு அப்புறம் creditors திரும்பப் பெறுவாங்கன்னு எதிர்பார்க்கப்படற face value-வின் பின்னம் — CDS எவ்வளவு payout கொடுக்கும்ங்கறதை நேரடியா தீர்மானிக்குது |

> **Deep dive இருக்கு.** Running Coupon-உம் market spread-உம் ரெண்டுமே basis points-ல quote பண்ணப்படுது — basis point அலகு தானே, "Spread DV01" (bps spread நகர்வை dollar மதிப்பு மாற்றமா மாத்தற credit-market உணர்திறன் அளவு, Section 4-ஓட சொந்த valuation mechanic) பத்தியும் [Module 24 — Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §5-ல.

```text
CDS AS INSURANCE — the core mental model
========================================================================

  Protection BUYER (Bank Of Toronto):
    Pays a periodic Running Coupon (like an insurance premium)
    -> in exchange for protection against the Reference Entity
       defaulting

  Protection SELLER (Goldman Sachs):
    Receives the Running Coupon
    -> pays out a large amount ONLY IF a Credit Event happens
       (like an insurer paying a claim)

  -> Critically: Bank Of Toronto does NOT need to own any bond
     issued by the Reference Entity to buy this protection -- a
     "naked" CDS position is possible, unlike home insurance,
     where you generally must own the home to insure it.
```

**Takeaway:** Insurance ஒப்புமை பயனுள்ளது, ஆனா ஒரு புகழ்பெற்ற, முக்கியமான வரம்பு உண்டு — ஒரு company-ஓட கடனை நீங்க வெச்சிருக்காமலே அதுக்கான CDS protection வாங்கலாம்; உண்மையான insurance-ல இருந்து கட்டமைப்பு ரீதியான வித்தியாசம், 2008 நிதி நெருக்கடில CDS-ஐ மையமாக்கினது (AIG போன்ற பெரிய protection sellers, தங்களிடம் இருந்த reserves-ஐ விட பல மடங்கு சாத்திய payouts-ஐ கடனா வெச்சிருந்தாங்க) — Section 8-ஓட regulatory response ஏன் அவ்வளவு முக்கியமானதுன்னு இதுதான்.

### கூடுதல் உதாரணம் — "Naked" CDS என்றால் என்ன? (எளிய தமிழ்ல)

உங்க பக்கத்து வீட்டுக்காரர் வீட்டுக்கு நீங்க fire insurance வாங்கறீங்கன்னு நினைச்சுக்கோங்க — நிஜ உலகத்துல இது சட்டப்படி அனுமதிக்கப்படாது (அவர் வீட்டுல உங்களுக்கு "insurable interest" இல்ல). ஆனா CDS-ல அது சாத்தியம்:

| நிலைமை | யாருக்கு என்ன ஆகுது |
|---|---|
| Bank Of Toronto, Northstar bonds வெச்சிருக்கு + CDS வாங்குது | Hedge — bond இழப்பை CDS payout ஈடுகட்டுது |
| Bank Of Toronto, Northstar bonds வைக்கலை + CDS வாங்குது ("naked") | Speculation — Northstar default ஆகும்னு பந்தயம்; default ஆச்சுன்னா லாபம், ஆகலைன்னா premium இழப்பு |

2008-ல இந்த naked positions + AIG போன்றவங்களோட பெரிய sold protection சேர்ந்து அமைப்பு முழுக்க பரவிய அபாயத்தை உருவாக்கிச்சு.

---

## 2. Products என்னென்ன? — Single-Name CDS, Index CDS

```text
CREDIT
│
└── Credit Default Swap (CDS)
    ├── Single-Name CDS      (references ONE Reference Entity)
    └── CDS Index            (references a BASKET of Reference Entities,
                               e.g. CDX, iTraxx — same mechanic, many names)
```

| Product | அது என்ன |
|---|---|
| **Single-Name CDS** | ஒரு குறிப்பிட்ட Reference Entity-ஓட default risk மேல protection — Section 4-ஓட worked example |
| **CDS Index** | Single-name CDS-ஓட standardized basket (உதா., North America-ல **CDX**, Europe-ல **iTraxx**), ஒரே instrument-ஆ trade ஆகுது — பொருளாதார ரீதியா அதே mechanic-ஓட portfolio, ஒரே product-ஆ price/clear பண்ணப்படுது |

**Takeaway:** Single-Name-உம் Index CDS-உம் அதே அடிப்படை mechanic-ஐ பகிர்கின்றன; "எத்தனை Reference Entities"ங்கறது தான் உண்மையான வேறுபாடு, வேற product வகை இல்ல. இந்த module-ஓட worked example முழுக்க Single-Name CDS-ஐ பயன்படுத்துது, ஏன்னா mechanic எப்படியும் ஒரே மாதிரி — Index-க்கு அதிக பெயர்கள் மேல பெருக்கப்படுது அவ்வளவுதான்.

---

## 3. உண்மையில எது Trade ஆகுது? — தூய Contract, ஒரு பரிச்சயமான விதிவிலக்கோட

**Definition:** CDS ஒரு contract, security இல்ல — trade date-ல யாரும் எதன் ownership-ஐயும் மாத்தறதில்ல; [Module 6](../docs/1-capital-markets/module-06-fx.html) §3 FX-க்கும், [Module 8](../docs/1-capital-markets/module-08-rates.html) §3 Rates-ஓட ஐந்துல நாலு products-க்கும் நிறுவின அதே "அடியோடு contracts" pattern. ஆனா [Module 8](../docs/1-capital-markets/module-08-rates.html) §3-ஓட IR Forward Debt போலவே, CDS-ஓட Reference Obligation ஒரு உண்மையான bond, உண்மையான ISIN-ஓட, contract-ஓட settlement mechanics அதை நோக்கி திரும்புது.

```text
CDS (CONTRACT)  vs  REFERENCE OBLIGATION (SECURITY IT POINTS TO)
========================================================================

  Credit Default Swap (CONTRACT):
    Bank Of Toronto and Goldman Sachs create this contract at trade
    date. It has no existence before the trade, and (barring a
    credit event) ceases to exist at its maturity date. Nothing
    about the Reference Entity's actual debt changes hands.

  Reference Obligation (SECURITY):
    A specific bond issued by the Reference Entity -- e.g. a
    Northstar Resources Inc. 6% Bond 2031 -- named in the CDS
    contract purely to define what "the debt" means for settlement
    (Section 5's credit-event auction references THIS obligation's
    market price, post-default, to determine the payout).

  -> The CDS itself never delivers this bond in modern practice
     (Section 5's cash-settlement-via-auction convention replaced
     the older physical-settlement norm after the 2009 Big Bang
     Protocol) -- but the bond's ISIN still appears on the CDS
     trade record as a reference field (Section 06).
```

**Takeaway:** இது roadmap-ல ([Module 8](../docs/1-capital-markets/module-08-rates.html)-ஓட IR Forward Debt-க்கு அடுத்து) உண்மையிலயே "contract, security இல்ல" product, ஒரு உண்மையான ISIN-ஐ தன் trade record-ல நியாயமா சுமக்கற ரெண்டாவது product குடும்பம் — ரெண்டு சந்தர்ப்பங்கள்லயும் அதே காரணம்: contract தானே trade ஆகுது என்றாலும், contract-ஓட settlement mechanics ஒரு குறிப்பிட்ட, உண்மையான security-யின் அடிப்படையில வரையறுக்கப்பட்டிருக்கு.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked example இருக்கு.** முழு Northstar Resources Inc. trade — Running Coupon, Upfront Payment, MTM, ஒரு Credit Event payout — முழு trade lifecycle வழியா பார்க்க [Credit Default Swap (CDS) — Complete Example](../examples/Credit_Default_Swap_CDS_Complete_Example.html) பாருங்க.

### 4a. Single-Name CDS — Trade Inception

```text
SINGLE-NAME CDS  —  5Y Protection on Northstar Resources Inc.
========================================================================

  Protection Buyer:  Bank Of Toronto
  Protection Seller: Goldman Sachs

  Reference Entity: Northstar Resources Inc. (fictional corporate)
  Reference Obligation: Northstar Resources Inc. 6% Bond 2031
  Notional: USD 10,000,000
  Term: 5 years
  Standardized Running Coupon: 100bps (1.00%), paid quarterly
  Market-quoted credit spread today: 250bps (2.50%)

  Because the market spread (2.50%) is well above the standardized
  coupon (1.00%), Bank Of Toronto must pay an UPFRONT amount to
  Goldman Sachs to compensate for the below-market running coupon
  it'll actually receive going forward (a full ISDA CDS Standard
  Model calculation is beyond this module's scope; the commonly
  used approximation is shown for intuition):

  Approx. Upfront = (Market Spread - Running Coupon) x Risky Duration x Notional
                   = (2.50% - 1.00%) x ~4.5 x 10,000,000
                   = 1.50% x 4.5 x 10,000,000
                   = USD 675,000 (paid by Bank Of Toronto at inception)

  Quarterly Running Coupon payment thereafter:
    10,000,000 x 1.00% x 91/360 (CDS convention is act/360, unlike
    the act/365 convention Module 8 used for CORRA) = USD 2,527.78
```

### 4b. Single-Name CDS — Credit Event, ரெண்டு வருஷம் கழிச்சு

```text
CREDIT EVENT  —  Northstar Resources Inc. defaults
========================================================================

  Trigger: Northstar Resources Inc. misses a bond coupon payment
  and files for bankruptcy protection

  ISDA Determinations Committee (Section 08) formally rules a
  "Credit Event" has occurred -- this is a genuinely different
  kind of body from any regulator in this roadmap so far: an
  INDUSTRY body, not a government regulator, that decides the
  single most consequential fact in this whole trade's lifecycle

  CREDIT EVENT AUCTION determines the Reference Obligation's
  post-default market price: settles at 35 (i.e. 35% of face value
  -- bondholders expected to recover roughly 35 cents on the dollar)

  Cash Settlement (the modern, standard mechanic post-2009 Big
  Bang Protocol -- NOT physical delivery of the defaulted bond):
    Payout = Notional x (100% - Recovery Rate)
           = 10,000,000 x (100% - 35%)
           = 10,000,000 x 65%
           = USD 6,500,000

  Goldman Sachs (protection seller) PAYS Bank Of Toronto (protection
  buyer) USD 6,500,000 -- the CDS's entire purpose realized: Bank Of
  Toronto is made economically whole for a USD 10,000,000 exposure
  to Northstar Resources Inc. it may never have even held as a bond,
  receiving 65% of face value in cash rather than a defaulted bond
  worth only 35 cents on the dollar.
```

**Takeaway:** Credit-ஓட மிகத் தனித்துவமான lifecycle event — credit-event auction mechanic: Equity, Fixed Income, FX, Commodities, Rates எதிலும் "ஒரு industry committee ஒரு வரையறுக்கப்பட்ட கெட்ட விஷயம் நடந்ததுன்னு முறையா அறிவிக்குது, அப்புறம் ஒரு auction payout விலையை நிர்ணயிக்குது" என்ற சமமான வரிசை இல்ல. Section 4b-க்கு முன்னாடி இருக்கற எல்லாமே (Sections 1–3, 4a) நிலையான contract mechanics; Credit Event தான் இந்த roadmap-ல Credit உண்மையிலயே மற்ற எல்லா asset classes-ல இருந்தும் வேறுபடற இடம்.

### கூடுதல் உதாரணம் — Recovery Rate மாறினா payout எப்படி மாறும்?

அதே USD 10,000,000 notional:

| Auction Recovery Rate | Payout = Notional × (1 − Recovery) | Bank Of Toronto-க்கு |
|---|---|---|
| 60% | 10,000,000 × 40% = USD 4,000,000 | சிறிய payout — நல்ல recovery |
| 35% (worked example) | USD 6,500,000 | நடுத்தரம் |
| 10% | USD 9,000,000 | கிட்டத்தட்ட முழு notional |
| 0% | USD 10,000,000 | முழு notional |

குறைந்த recovery = அதிக payout. இதுதான் [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9-ல நாம பார்த்த அதே logic.

---

## 5. Trade Lifecycle — Credit-க்குரிய Walkthrough

**Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting** pattern-ஐ Section 4a-ஓட CDS-க்கு பொருத்தி, Section 4b-ஓட Credit Event வரை.

```text
5Y CDS ON NORTHSTAR RESOURCES INC.  —  FULL LIFECYCLE
========================================================================

  YEAR 0
  10:40:00  ORDER — Bank Of Toronto's credit desk instructed to buy
            5Y protection, USD 10,000,000, Northstar Resources Inc.

  10:40:15  EXECUTION @ 250bps market spread vs Goldman Sachs

  10:40:15  TRADE CAPTURE — Trade ID BOT-CDS-2026-041127
            Upfront payment calculated: USD 675,000 (Section 4a)

  10:55:00  CONFIRMATION/MATCHING -> MATCHED

  CLEARING — for a standardized single-name or index CDS, novated
            to a CCP (ICE Clear Credit or LCH CDSClear) under a
            clearing mandate that parallels EMIR/Dodd-Frank's
            Rates mandate (Module 8 §07-08) but scoped specifically
            to CDS index products and the most liquid single names

  QUARTERLY RUNNING COUPON — USD 2,527.78 paid by Bank Of Toronto
            to Goldman Sachs each quarter, for as long as the trade
            survives without a Credit Event (Section 4a)

  YEAR 2
            CREDIT EVENT — Northstar Resources Inc. defaults
            (Section 4b)

            ISDA DETERMINATIONS COMMITTEE rules the event is a
            valid Credit Event under the contract's terms

            CREDIT EVENT AUCTION sets the Reference Obligation's
            settlement price at 35

  SETTLEMENT — Goldman Sachs pays Bank Of Toronto USD 6,500,000
            cash (Section 4b); the CDS contract TERMINATES --
            unlike an IRS (Module 8 §5), which runs its full term
            regardless of what happens to interest rates, a CDS
            can end early, permanently, the moment its one defined
            trigger event occurs

  REPORTING — trade reported to a Trade Repository (DTCC GTR)
            throughout its life, with a final settlement/
            termination event reported once the Credit Event
            auction concludes
```

**Takeaway:** இந்த roadmap-ல மற்ற ஒவ்வொரு product-உம் ஒண்ணு அதன் நிர்ணயிக்கப்பட்ட maturity வரை ஓடுது, இல்லன்னா தானாக முன்வந்து மூடப்படுது/roll ஆகுது ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §4b-ஓட futures roll). *இரு counterparties-ஓட கட்டுப்பாட்டுக்கும் வெளியே ஒரு வெளி நிகழ்வு* trade-ஐ முன்கூட்டியே முடிக்கக்கூடிய முதல் product CDS — Reference Entity-ஓட சொந்த solvency, சந்தை விலை நகர்வு இல்ல, அதை terminate பண்ணுது.

> **Deep dive இருக்கு.** CDS position என்பது வாங்கின/விற்ற protection-ஓட தூய notional, அதோட நிலையான Running Coupon-க்கும் தற்போதைய market spread-க்கும் இடையிலான இடைவெளியில இருந்து value பண்ணப்படுது — முழு theory-க்கு [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §6.

---

## 6. Identifiers — UPI, UTI, LEI (ரெண்டு வேற பாத்திரங்களுக்கு), Reference Obligation ISIN

CDS இப்போது பரிச்சயமான OTC derivative identifier set-ஐ மறுபயன்படுத்துது, ஆனா ஒரு உண்மையான திருப்பத்தோட: **ஒரே trade record-ல LEI கட்டமைப்பு ரீதியா ரெண்டு வேற பாத்திரங்கள்ல வருது.**

| Identifier | CDS-க்கு பொருந்துமா? | Example |
|---|---|---|
| ISIN | CDS contract-க்கே **இல்ல** — ஆனா அது சுட்டிக்காட்டும் Reference Obligation-க்கு **ஆமா** (Section 03) | CDS-க்கு `—`; Northstar Resources Inc. 6% Bond 2031-க்கு உண்மையான ISIN |
| **UPI** | ஆமா, OTC derivative product template-ஆ ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) | "5Y USD Single-Name CDS, Northstar Resources Inc."-க்கு ஒரு UPI |
| **UTI** | ஆமா — ஒவ்வொரு தனி CDS trade-க்கும் தனி UTI | ஒவ்வொரு trade-க்கும் புது UTI |
| **LEI — Counterparty பாத்திரம்** | ஆமா — Bank Of Toronto, Goldman Sachs இரு trading parties-ஐயும் identify பண்ணும் | முந்தைய ஒவ்வொரு module-லயும் LEI வகிக்கற அதே பாத்திரம் ([Module 4](../docs/1-capital-markets/module-04-equity.html) §6) |
| **LEI — Reference Entity பாத்திரம்** | ஆமா, இது புதுசு: Northstar Resources Inc. — யாரோட *default risk* trade ஆகுதோ அந்த company — தனக்கான LEI-ஐயும் சுமக்கும்; counterparty LEIs-ல இருந்து முற்றிலும் தனி field-ஆ நிரப்பப்படும் | contract *எதை* reference பண்ணுதுன்னு identify பண்ணுது, *யார்* trade பண்றாங்கன்னு இல்ல |

```text
LEI'S TWO ROLES ON ONE CDS TRADE RECORD
========================================================================

  Counterparty LEI (Buyer)  : identifies Bank Of Toronto
  Counterparty LEI (Seller) : identifies Goldman Sachs
  Reference Entity LEI      : identifies Northstar Resources Inc.

  -> every prior module's LEI only ever answered "WHO is trading
     this?" (Module 4 §6). Credit is the first product where LEI
     ALSO answers "WHAT is the contract about?" -- a role every
     other module gave to an instrument identifier (ISIN, Ticker,
     currency pair) instead.
```

**Takeaway:** மூணு LEI fields-உம் ஒரு ISIN field-உம் (Reference Obligation-க்கு) நிரம்பிய CDS trade record, அதிகமா நிரப்பப்பட்டதோ தவறோ இல்ல — ஒரு OTC derivative trade record ஒரே நேரத்துல எத்தனை தனித்தனி "யார்", "எது" கேள்விகளுக்கு பதில் சொல்லணும்ங்கறதுக்கு இந்த roadmap-ல இதுவரையிலான முழுமையான வெளிப்பாடு.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** Credit, [Module 8](../docs/1-capital-markets/module-08-rates.html) §7 Rates-க்கு நிறுவின அதே கட்டமைப்பு pattern-ஐ பின்பற்றுது — default-ஆ OTC-ல பேசப்படுது; மிகவும் standardized instruments (CDS Indices, மிக liquid single names) கட்டாய central clearing-க்குள்ள இழுக்கப்படுது; bespoke single names bilateral-ஆவே இருக்கும்.

| Product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|
| CDS Index (CDX, iTraxx) | OTC-ல பேசப்படுது | மிக liquid indices-க்கு EMIR/Dodd-Frank-பாணி clearing mandate-ன் கீழ **கட்டாயமா** centrally cleared (ICE Clear Credit, LCH CDSClear) |
| Single-Name CDS (liquid, standardized names) | OTC-ல பேசப்படுது | அதிகரித்து centrally cleared |
| Single-Name CDS (bespoke/illiquid Reference Entity) | OTC-ல பேசப்படுது | Bilateral (வழக்கம்) — Section 4-ஓட Northstar Resources Inc. trade இந்த case-ஆ எழுதப்பட்டிருக்கு |

**Takeaway:** இது தொடர்ந்து மூன்றாவது module (Rates-க்கு அடுத்து, [Module 8](../docs/1-capital-markets/module-08-rates.html) §7, Commodities-ஓட [Module 7](../docs/1-capital-markets/module-07-commodities.html) §7-ஐயும் எதிரொலிச்சு) அதே அடிப்படை pattern மீண்டும் மீண்டும் வருது — asset class-ஓட ஒரு standardized துண்டு central-clearing volume-ஐ சுமக்குது; bespoke, illiquid instruments-ஓட நீண்ட வால் bilateral OTC-யா நிக்குது. இந்த roadmap-ல இந்த நேரத்துக்குள்ள, அந்த pattern தானே எந்த ஒரு asset class-க்கும் குறிப்பான தற்செயல் இல்ல, OTC derivatives சந்தைகள் பத்தின பொது உண்மைன்னு அடையாளம் காணத் தகுந்தது.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | Credit-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Standardized CDS-க்கு trading-venue, transparency விதிகள், Rates-ஓட நடத்தைக்கு ([Module 8](../docs/1-capital-markets/module-08-rates.html) §08) வடிவத்துல ஒத்தவை |
| **EMIR** | குறிப்பா மிக liquid CDS Indices-க்கு (உதா., iTraxx Europe Main, Crossover) central clearing-ஐ கட்டாயமாக்குது — Rates-ஓட பரந்த IRS mandate-ஐ ([Module 8](../docs/1-capital-markets/module-08-rates.html) §08) விட குறுகிய clearing mandate; பெரும்பாலான single-name CDS எவ்வளவு குறைவா standardized-ஆ இருக்குன்னு காட்டுது |
| **SFTR** | பொருந்தாது — CDS ஒரு derivative, securities financing transaction இல்ல |
| **FCA** (UK) | CDS trading, clearing நடவடிக்கைக்கான UK conduct regulator |
| **CIRO** (Canada) | Canadian credit derivatives dealer conduct-ஐ மேற்பார்வை பண்ற SRO |
| **HKMA** (Hong Kong) | Rates-உடன் ([Module 8](../docs/1-capital-markets/module-08-rates.html) §08) சேர்த்து CDS-க்கு Hong Kong-ஓட OTC derivatives reporting regime-ஐ, அதே Securities and Futures Ordinance கட்டமைப்பின் கீழ நடத்துது |
| **CFTC / SEC** (US) — இந்த module-க்கு புதுசு | இந்த roadmap-ல **முதல் பிளவு அதிகார எல்லை**: Dodd-Frank-ன் கீழ, பரந்த-அடிப்படை CDS Indices "swaps"-ஆ **CFTC**-யால regulate ஆகுது (Rates, Commodities-ஐ உள்ளடக்கும் அதே regulator), ஆனா **single-name CDS-உம் narrow-based indices-உம் "security-based swaps"**, அதற்குப் பதிலா **SEC**-யால regulate ஆகுது — வேற எந்த asset class-லயும் இந்த roadmap பார்க்காத ஒரு உண்மையான US அதிகார எல்லைப் பிளவு |
| **ISDA Determinations Committees** — இந்த module-க்கு புதுசு | அரசு regulator-ஏ இல்ல; ஒரு **industry self-governance அமைப்பு** (Section 4b, Section 5), ஒரு Credit Event நடந்ததா இல்லையா என்ற அதன் தீர்ப்பு ஒரு CDS-ஓட முழு lifecycle-ல மிக விளைவுள்ள ஒற்றை முடிவு — இந்த table-ல மற்ற ஒவ்வொரு வரியும் பின்பற்றும் "acronym + jurisdiction" pattern-க்கு பொருந்தாததாலேயே இங்க சேர்க்கத் தகுந்தது |
| **DTCC** | அதன் GTR CDS reporting-க்கான ஒரு பெரிய trade repository, [Module 8](../docs/1-capital-markets/module-08-rates.html) §08-ஓட அதே infrastructure பங்கு |
| **DMO** (UK Debt Management Office) | Rates-ல இருந்த மாதிரி (Gilts மேல IR Forward Debt, [Module 8](../docs/1-capital-markets/module-08-rates.html) §08) Credit-க்கு பொருத்தமில்ல — sovereign CDS (ஒரு அரசின் சொந்த default risk மேல protection) உண்டு, ஆனா இந்த module-ஓட worked example இல்ல; முழுமைக்காக இங்க |

**Takeaway:** இந்த roadmap-ஓட இறுதி module-ல முதல் தடவையா ரெண்டு உண்மையிலயே புது regulatory கருத்துகள் வருது — ஒரே US regulatory கட்டமைப்புக்குள்ள CFTC/SEC அதிகார எல்லைப் பிளவு, முழு trade lifecycle-ஓட மிக விளைவுள்ள முடிவை எடுக்கும் ஒரு industry அமைப்பு (ISDA-ஓட Determinations Committees). ரெண்டும் Credit-ஐ முந்தைய எல்லா asset classes-ல இருந்தும் வேறுபடுத்துறதால இருக்கு: தொடர்ந்து observe பண்ணக்கூடிய சந்தை விலைக்கு எதிரா settle ஆகாம, ஒரு *தனி, சர்ச்சைக்குரிய நிகழ்வுக்கு* (உண்மையில ஒரு default நடந்ததா, யாருடைய விதிகளின் கீழ?) தூண்டப்படுவதே முழு நோக்கமாக உள்ள contract.

---

## 9. Canadian Systems Landscape — Credit Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §2 Canada-க்கான *பொதுவான* Front/Middle/Back Office architecture-ஐ வகுத்திருக்கு. இந்த section அதை Credit-க்கு மட்டும் சுருக்குது — Section 4a-ஓட Northstar Resources Inc. CDS trade capture-ல இருந்து Canadian regulatory reporting வரை, ஒரு Canadian வங்கியோட CDS desk இணையற உண்மையான systems.

> **Callout — illustrative, எந்த குறிப்பிட்ட வங்கியோட stack பத்தின கூற்று இல்ல.** கீழ உள்ள platform பெயர்கள் (MarkitWire/ICE Link, Calypso/Murex, ICE Clear Credit, DTCC-ஓட Trade Information Warehouse) Canadian dealers CDS-க்கு உண்மையில இணையற market infrastructure-ஓட பிரதிநிதிகள் — [systems-landscape](../docs/1-capital-markets/systems-landscape.html) Murex/Summit-க்கு ஏற்கனவே பயன்படுத்தற அதே "illustrative vendor family" வழக்கம். Reporting pipeline integrate ஆக வேண்டிய system-ஓட *வகை*யா எடுத்துக்கோங்க, Bank Of Toronto-ஓட சொந்த internal build பத்தின கூற்றா இல்ல.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | MarkitWire / ICE Link, Bloomberg | Trade capture and execution workflow for the CDS itself; Bloomberg for pricing/quoting the market spread (Section 4a's 250bps) |
| **Middle Office** | MarkitWire / ICE Link (again), Calypso / Murex | CDS is unusually compressed here: the SAME MarkitWire/ICE Link platform that captured the trade also handles confirmation/affirmation — a genuine contrast with [Module 8](../docs/1-capital-markets/module-08-rates.md)'s IRS, where front-office execution and middle-office confirmation more commonly sit on separate platforms. Calypso/Murex handle ongoing valuation and risk (upfront/running-coupon calculations, Section 4a) |
| **Back Office / Clearing** | ICE Clear Credit (if centrally cleared, Section 7) *or* bilateral settlement (if not) — DTCC's **Trade Information Warehouse (TIW)** either way | Section 7 already classified the bespoke Northstar Resources Inc. single-name CDS as staying **bilateral** — so ICE Clear Credit never enters this specific trade's path. Regardless of cleared/bilateral status, DTCC's TIW holds the official post-trade lifecycle record (running coupons, the Section 4b Credit Event, final settlement) for the large majority of CDS trades globally |
| **Regulator / Trade Repository** | CSA **Multilateral Instrument 96-101**, reported to a CSA-designated Trade Repository (e.g. DTCC Data Repository (Canada) Ltd — illustrative, same naming pattern as the [SFTR](../2-reporting/module-16-sftr-reporting.md)/[DMO](../2-reporting/module-17-dmo-uk-reporting.md) modules' Canadian TR references); **CIRO** for dealer conduct; **Bank of Canada** for systemic oversight, given CDS's own role as a 2008 contagion channel (Section 1) |

```text
CANADA — CREDIT (CDS) SYSTEMS CHAIN, THE NORTHSTAR RESOURCES INC. TRADE
========================================================================

  FRONT OFFICE
    Bloomberg (price discovery, 250bps market spread)
    MarkitWire / ICE Link (trade capture -- Trade ID BOT-CDS-2026-041127)
         |
         v
  MIDDLE OFFICE
    MarkitWire / ICE Link (confirmation/affirmation -- MATCHED,
      Section 05's 10:55:00 timestamp)
    Calypso / Murex (upfront payment calc: USD 675,000; ongoing
      running-coupon and MTM valuation)
         |
         v
  BACK OFFICE
    Section 07: bespoke single-name CDS -> stays BILATERAL,
      no CCP novation (contrast: a CDX/iTraxx index trade would
      route here to ICE Clear Credit instead)
    DTCC Trade Information Warehouse (TIW) -- holds the lifecycle
      record: running coupons, the Year-2 Credit Event, the
      USD 6,500,000 cash settlement (Section 04b)
         |
         v
  REGULATORY / TRADE REPOSITORY
    CSA Multilateral Instrument 96-101 -- OTC derivatives trade
      reporting obligation
    -> reported to a CSA-designated Trade Repository (e.g. DTCC
       Data Repository (Canada) Ltd)
    CIRO -- Bank Of Toronto's dealer conduct as a CDS counterparty
    Bank of Canada -- systemic oversight (Section 01's 2008
      contagion history is exactly why this oversight exists)
```

**Takeaway:** Northstar Resources Inc. trade-ஓட சொந்த Section 7 வகைப்பாடு — bespoke, bilateral, centrally cleared இல்ல — எந்த Back Office system அதை உண்மையில தொடுதுன்னு நேரடியா தீர்மானிக்குது: DTCC-ஓட TIW எப்படியும் அதன் lifecycle-ஐ பதிவு பண்ணும், ஆனா ICE Clear Credit standardized CDS Index/liquid single-name துண்டுக்கு மட்டும்தான் படத்துல நுழையுது. ஒவ்வொரு CDS-உம் CCP வழியா ஓடும்னு கருதற reporting pipeline, இந்த module worked example-ஆ பயன்படுத்தின trade-ஐயே தவறா route பண்ணும்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Northstar Resources Inc. CDS-ல protection buyer |
| Goldman Sachs | Northstar Resources Inc. CDS-ல protection seller |
| Northstar Resources Inc. | Reference Entity (கற்பனை corporate) — trade-க்கு counterparty இல்லவே இல்ல, அதோட subject மட்டும் |
| ISDA Determinations Committee | Credit Event நடந்ததா என்று தீர்ப்பளிக்கும் industry அமைப்பு |
| ICE Clear Credit / LCH CDSClear | Standardized CDS Indices, liquid single names-க்கு central clearing செய்யும் CCPs |
| CFTC / SEC | பிளவுபட்ட US regulators — swaps vs. security-based swaps |
| DTCC Trade Information Warehouse (TIW) | Northstar Resources Inc. CDS-க்கான அதிகாரப்பூர்வ post-trade lifecycle record-ஐ வெச்சிருக்கும் (Section 9) |
| CIRO | Canadian credit derivatives சந்தையில Bank Of Toronto-ஓட dealer conduct-ஐ மேற்பார்வை பண்ற SRO (Section 9) |
| Bank of Canada | Canadian dealers-ஐ தொடும் CDS நடவடிக்கையோட systemic மேற்பார்வை (Section 9) |

---

## Recap Quiz — Numbers Edition

1.  Section 4a-ஓட CDS-ல, market-quoted spread 60bps (100bps standardized Running Coupon-க்கு கீழ) ஆக இருந்திருந்தா, Bank Of Toronto upfront தொகையை கொடுக்குமா பெறுமா, தோராயமா எவ்வளவு (அதே approximation formula, Risky Duration ~4.5 வெச்சு)?
2.  Section 4b-ஓட Credit Event payout, Bank Of Toronto முழு USD 10,000,000 notional-ஐயும் இழக்குதுன்னு வெறுமனே கருதாம, 35% Recovery Rate-ஐ பயன்படுத்தறது ஏன்?
3.  Section 3-ஐ வெச்சு, CDS trade record ஒருபோதும் ISIN-ஐ சுமக்கக்கூடாதுன்னு சொல்றது சரியா? ஏன்/ஏன் இல்ல?
4.  Section 6-ல LEI விவாதத்துல, ஒரே trade record-ல "Counterparty (Seller)" field-லயும் "Reference Entity" field-லயும் ஒரே LEI நிரம்பியிருந்தா, அது எந்த நிஜ உலக scenario-வை குறிக்கும்? அது data error-ஆ?
5.  Section 8-ல ISDA Determinations Committee, அந்த table-ல மற்ற எல்லா வரிகளோட "acronym + jurisdiction pattern"-க்கு பொருந்தாதுன்னு விவரிக்கப்படுறது ஏன்?
6.  Credit-க்கான US CFTC/SEC அதிகார எல்லைப் பிளவை (Section 8), Rates-க்கு [Module 8](../docs/1-capital-markets/module-08-rates.html) §08 விவரிச்ச single-regulator (CFTC மட்டும்) pattern-ஓட ஒப்பிடுங்க — IRS பிளவுபடாத இடத்துல CDS பிளவுபடுவதற்கு என்ன குறிப்பா காரணம்?
7.  Section 7, OTC-default-with-a-mandatorily-cleared-standardized-slice pattern-ஐ Credit-க்கு மட்டுமான தற்செயல் என்று சொல்லாம, "பொது உண்மையா அடையாளம் காணத் தகுந்தது"ன்னு ஏன் சொல்லுது?
8.  Section 9-ஐ வெச்சு, ICE Clear Credit Section 9-ஓட table-ல Back Office systems-ல ஒண்ணாக பட்டியலிடப்பட்டிருந்தாலும், Section 4-ஓட Northstar Resources Inc. CDS-ஐ ஏன் ஒருபோதும் தொடுறதில்ல?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — இத்தோட **Asset Class Roadmap** முடியுது. Modules 10-14 (Valuation, Risk & Exposure, Collateral & Margin, Parties/LEIs, Regulatory Reporting), [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) (Listed Derivatives) இதுவரை படிச்ச ஆறு asset classes மேலயும் கட்டுது.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Bank Of Toronto upfront தொகையை *பெறும்*, தோராயமா USD 180,000.**

```text
Approx. Upfront = (Market Spread - Running Coupon) x Risky Duration x Notional
                 = (0.60% - 1.00%) x 4.5 x 10,000,000
                 = -0.40% x 4.5 x 10,000,000
                 = -USD 180,000 (negative = Bank Of Toronto RECEIVES)
```

Market spread standardized Running Coupon-ஐ விட *குறைவா* இருக்கும்போது, ஒவ்வொரு quarter-உம் Northstar Resources Inc.-ஓட credit risk-ஐ சந்தை உண்மையில price பண்றதை விட Bank Of Toronto அதிகம் கொடுத்துட்டிருக்கும் — அதனால அந்த சந்தைக்கு மேல இருக்கற coupon-ல lock ஆகறதுக்கு Goldman Sachs, Bank Of Toronto-க்கு முன்கூட்டியே இழப்பீடு தருது; Section 4a-ஓட worked example-ஓட mirror image.

**2. ஏன்னா Credit Event ஒரு Credit Event Auction-ஐ தூண்டுது; அது Reference Obligation-ஓட உண்மையான default-க்கு அப்புறமான சந்தை விலையை நிர்ணயிக்குது (Section 4b-ல face value-வின் 35%) — default ஆன company-ஓட bondholders/creditors பொதுவா bankruptcy நடவடிக்கைகள் மூலம் ஏதோ ஒண்ணை இன்னும் திரும்பப் பெறுவாங்க, அதனால CDS payout, protection buyer-ஐ கருதப்பட்ட மொத்த இழப்புக்கு (100%) இல்ல, உண்மையான இழப்புக்கு (65%) ஈடுசெய்ய அளவீடு செய்யப்படுது.**

Section 4b-ஓட payout formula, `Notional x (100% - Recovery Rate)`, 0% Recovery Rate என்ற தீவிர நிலையில மட்டுமே முழு notional payout-ஆ குறுகும் — auction mechanism இருக்கறதே, மோசமான நிலையை default-ஆ கருதாம, இந்த குறிப்பிட்ட default-க்கு எந்த recovery rate பொருந்தும்னு கண்டுபிடிக்கத்தான்.

**3. இல்ல — CDS trade record ஒருபோதும் ISIN-ஐ சுமக்கக்கூடாதுன்னு சொல்றது சரியில்ல. Section 03 நிறுவுவது: CDS contract-க்கு ISIN இல்லன்னாலும், அது பெயரிடும் Reference Obligation (Reference Entity வெளியிட்ட குறிப்பிட்ட bond) ஒரு உண்மையான security, CDS trade record-ல ஒரு reference field-ஆ தன் சொந்த ISIN-ஐ நியாயமா சுமக்கும்.**

இது [Module 8](../docs/1-capital-markets/module-08-rates.html) §6 IR Forward Debt-க்கு நிறுவின அதே நுணுக்கம்: "contract-க்கே ISIN இல்ல, ஆனா contract-ஓட mechanics சுட்டிக்காட்டும் உண்மையான security-க்கு உண்டு" என்பது, உண்மையான security-ஐ deliver பண்ணாமலே அதை reference பண்ணும் contracts-க்கு ஒரு தொடர்ச்சியான, ஆரோக்கியமான pattern — "derivatives-க்கு ISIN ஒருபோதும் இருக்காது" என்ற உலகளாவிய விதி இல்ல.

**4. அது Goldman Sachs தன் *சொந்த* default risk மேல protection விற்றதைக் குறிக்கும் — Goldman Sachs ஒரே நேரத்துல protection-selling counterparty-உம் Reference Entity-உம். இது ஒரு உண்மையான (அசாதாரணமான, ஆபத்தான) scenario, தானா data error இல்ல; ஆனா வெளிப்படையான conflict-of-interest, correlation risk காரணமா (Goldman Sachs தானே default ஆனா, ஒரே நேரத்துல payout கொடுக்கவும் முடியாது, அதுவே trigger event-உம்) பொதுவா அதிக ஆய்வைத் தூண்டும்.**

Section 6-ஓட இரு-பாத்திர விளக்கம், Counterparty LEI-உம் Reference Entity LEI-உம் சுயாதீனமா நிரப்பப்படுது, கொள்கையளவுல ஒத்துப்போகவும் செய்யலாம்னு தெளிவாக்குது — systems கட்டமைப்பு ரீதியா அதை தடுக்கறதில்ல, ஒரு risk அல்லது compliance செயல்பாடு அது பிரதிநிதித்துவப்படுத்தும் குவிந்த risk-க்காக அதை flag பண்ணலாம் (இந்த குறிப்பிட்ட risk pattern சில சமயம் [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html): Risk & Exposure-ல "wrong-way risk"ன்னு அழைக்கப்படுது).

**5. ஏன்னா Section 8-ஓட table-ல மற்ற ஒவ்வொரு வரியும் ஒரு அரசு-சார்ந்த regulator-ஐ (ஒரு central bank, ஒரு securities commission, சட்ட அதிகாரமுள்ள ஒரு self-regulatory organization) ஒரு வரையறுக்கப்பட்ட அதிகார எல்லைக்குள்ள செயல்படுவதா பெயரிடுது — ISDA Determinations Committee, இதற்கு மாறா, CDS contract-ஓட சொந்த standardized terms-ஐ கூட்டா விளக்கும் industry பங்கேற்பாளர்களால் (dealers, buy-side firms) ஆன அமைப்பு; அதன் தீர்ப்புகளுக்குப் பின்னால அரசு ஆணை இல்ல, அதன் அதிகாரம் முழுக்க ISDA contract documentation-லயே தீர்மானிக்கும் அமைப்பா பெயரிடப்பட்டிருப்பதில இருந்து வருது, சட்டத்துல இருந்து இல்ல.**

Section 8-ஓட takeaway இதை இந்த module-ல உண்மையிலயே புது ரெண்டு regulatory கருத்துகள்ல ஒண்ணா வெளிப்படையா சுட்டிக்காட்டுது — அதே table-ல உண்மையான அரசு regulators-க்கு பக்கத்துல (அவற்றை மாற்றாம) இருக்கற ஒரு தனியார், contract-படி நியமிக்கப்பட்ட முடிவெடுக்கும் அமைப்பு.

**6. CFTC/SEC பிளவு இருக்கறதுக்கு காரணம், Dodd-Frank "swaps" (CFTC அதிகார எல்லை) , "security-based swaps" (SEC அதிகார எல்லை) இடையே ஒரு குறிப்பிட்ட சட்டக் கோட்டை வரைஞ்சது; single-name/narrow-based CDS குறிப்பா security-based swaps-ஆ வகைப்படுத்தப்பட்டன, ஏன்னா அவை ஒரு தனிப்பட்ட company-ஓட (அல்லது குறுகிய குழுவோட) debt securities-ஐ reference பண்ணுது — SEC ஏற்கனவே regulate பண்ற securities-law கருத்துகளோட நெருக்கமா பிணைந்தவை. Vanilla IRS, இதற்கு மாறா, ஒரு interest rate benchmark-ஐ (CORRA, SOFR) reference பண்ணுது, எந்த குறிப்பிட்ட company-ஓட securities-ஐயும் இல்ல, அதனால SEC அதிகார எல்லையை உரிமை கோர எந்த securities-law கொக்கியும் இல்லாம, CFTC-ஓட "swap" வாளியில சுத்தமா விழுது.**

Section 8-ஓட table இந்த பிளவை நேரடியா சொல்லுது, Rates-க்கு [Module 8](../docs/1-capital-markets/module-08-rates.html) §08-ஓட தெளிவான CFTC-மட்டும் படத்தோடு ஒப்பிட்டு — தீர்மானிக்கும் காரணி, underlying reference ஒரு security-சார்ந்த கருத்தா (SEC அதிகார எல்லையைத் தூண்டும்), இல்ல தூய rate/benchmark-ஆ (CFTC-யோடு மட்டும் இருக்கும்).

**7. ஏன்னா அதே சரியான வடிவம் — OTC asset class-ஓட ஒரு standardized, liquid துண்டு கட்டாய அல்லது வளரும் central clearing-க்குள்ள இழுக்கப்படுது, bespoke instruments-ஓட நீண்ட வால் bilateral-ஆ இருக்கு — இப்போ மூணு வேற வேற asset classes-ல சுயாதீனமா தோன்றியிருக்கு (Commodities-ஓட Futures, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §07; Rates-ஓட IRS, [Module 8](../docs/1-capital-markets/module-08-rates.html) §07; Credit-ஓட CDS Indices, liquid names, இந்த module-ஓட Section 07), ஒவ்வொண்ணும் வேற குறிப்பிட்ட mechanism வழியா (முறையே venue வடிவமைப்பு, regulatory mandate, ஒரு குறுகிய regulatory mandate) அடையப்பட்டது.**

Section 7-ஓட takeaway இந்த முடிவை வெளிப்படையா வரையுது: உண்மையிலயே வேறுபட்ட products-லயும் வேறுபட்ட காரண mechanisms-லயும் ஒரே கட்டமைப்பு pattern-ஓட மூணாவது சுயாதீன நிகழ்வுக்குள்ள, அது asset-class-குறிப்பான விவரமா தெரியறதை நிறுத்தி, OTC derivatives சந்தைகள் பகுதி standardization நோக்கி எப்படி பரிணமிக்குதுங்கற பொது உண்மையா தெரிய ஆரம்பிக்குது — [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) (Collateral & Margin), [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) (Regulatory Reporting)-க்கு முன்னோக்கி எடுத்துச் செல்லத் தகுந்தது; அங்க bilateral/cleared வேறுபாடு எந்த collateral, reporting கடமைகள் பொருந்தும்ங்கறதுக்கான ஒரு முக்கிய இயக்கியா மீண்டும் வெளிப்படுது.

**8. ஏன்னா Section 7 ஏற்கனவே Northstar Resources Inc. CDS-ஐ bespoke, illiquid single-name trade ஆ, BILATERAL-ஆவே இருக்கும்னு வகைப்படுத்திடுச்சு — Section 9-ஓட table-ல ICE Clear Credit-ஓட பங்கு குறிப்பா சந்தையோட standardized CDS Index / liquid single-name துண்டுக்கு எல்லை வகுக்கப்பட்டிருக்கு; இந்த குறிப்பிட்ட trade அதில சேராது.**

Section 9-ஓட takeaway இதை நேரடியா சொல்லுது: trade-ஓட சொந்த Section 7 வகைப்பாடு எந்த Back Office system அதை உண்மையில தொடுதுன்னு தீர்மானிக்குது — DTCC-ஓட TIW எப்படியும் அதன் lifecycle-ஐ பதிவு பண்ணும், ஆனா ICE Clear Credit உண்மையிலயே centrally cleared ஆன trades-க்கு மட்டும்தான் படத்துல நுழையுது; Northstar Resources Inc. trade வடிவமைப்பிலேயே அப்படியில்ல.
