# Module 5 — Fixed Income (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Asset Class Roadmap 2 of 6** — Equity → **Fixed Income** → FX → Commodities → Rates → Credit.
> **மூலம்:** English Module 5-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Fixed Income-ன்னா என்ன? — Theory & Terminology

**Definition:** Fixed Income instrument என்பது அடிப்படைல **ஒரு கடன்**. Buyer, issuer-க்கு (அரசு, company, municipality) பணம் கடனா கொடுக்கறார்; issuer, periodic வட்டி (coupon) கொடுக்கறதாகவும், நிர்ணயிக்கப்பட்ட எதிர்கால தேதில (Maturity) அசலை (Face Value) திருப்பறதாகவும் வாக்கு கொடுக்கறார். இது [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity-க்கு நேர் எதிர்: bond = **creditor உரிமை**, ownership உரிமை இல்ல — vote இல்ல, எதிர்கால லாபத்துல பங்கு இல்ல, ஆனா issuer தோத்தா equity-க்கு முன்னாடி பணம் வாங்கற contractual உரிமை உண்டு.

| Term | அர்த்தம் |
|---|---|
| Face Value | Maturity-ல திருப்பிக் கொடுக்கற அசல்; coupon கணக்கிடற அடிப்படையும் இதுதான் |
| Coupon | Face Value-வின் % ஆக சொல்லப்படற நிலையான periodic வட்டி ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1) |
| Maturity | Face Value திருப்பிக் கொடுக்கப்படற நிலையான தேதி — equity-க்கு இல்லாத தனிச்சிறப்பு |
| Price | Face Value 100-க்கு quote பண்றது ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1) |
| Yield | கொடுத்த விலையைக் கணக்கில் எடுத்த, buyer-ஓட உண்மையான return; coupon rate மட்டும் இல்ல ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1) |

Coupon Rate vs. Yield-ஐ [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1-ல நாலு-வருஷ worked example-ஓட முழுசா பார்த்தாச்சு — சுருக்கமா: **Coupon Rate நிலையானது, bond மேல printed; Yield derive ஆனது, Price-ஓட நகரும்.** இனி இந்த வேறுபாடு உங்களுக்கு தெரியும்னு எடுத்துக்கறோம்.

### Clean Price vs Dirty Price vs Accrued Interest — புது விஷயம்

**Definition:** Bond விலைகள் எப்பவும் **Clean Price**-ஆத்தான் *quote* பண்ணப்படும் — வட்டி சேராத, bond-ஓட மதிப்பை மட்டும் காட்டற விலை. ஆனா coupon தேதிகளுக்கு நடுவுல bond trade-ஐ settle பண்ற buyer, seller-க்கு **Accrued Interest** கொடுக்கணும் — last coupon தேதில இருந்து bond-ஐ வெச்சிருந்ததால seller "சம்பாதிச்ச" coupon பங்கு; இல்லன்னா அது முழுசா buyer-கிட்ட போய்டும். Settlement-ல உண்மையில கை மாறற cash **Dirty Price** (இதை "Full Price" அல்லது "Invoice Price"-ன்னும் சொல்வாங்க) — Clean Price + Accrued Interest.

```text
CLEAN PRICE vs DIRTY PRICE — worked example
========================================================================

  Bond: Canada 5% Bond 2035, semi-annual coupon (2.50% per period)
  Face Value: CAD 10,000,000
  Quoted (Clean) Price: 98.50
  Settlement falls 60 days after the last coupon date, in a
  182-day coupon period

  Accrued Interest = Face Value x (Coupon/2) x (Days Since Last Coupon / Days in Period)
                    = 10,000,000 x 2.50% x (60 / 182)
                    = 250,000 x 0.32967
                    = CAD 82,418  (rounded)

  Clean Price Value = 10,000,000 x 98.50 / 100 = CAD 9,850,000
  Dirty Price (actual cash paid) = 9,850,000 + 82,418 = CAD 9,932,418

  -> The seller held the bond for 60 of the 182 days in this
     coupon period, so 60/182 of the next coupon belongs to them,
     even though the BUYER will be the one who actually receives
     the full coupon payment on the next coupon date.
```

**IT/reporting பார்வை:** பெரும்பாலான systems bond-ஓட "Price" field-ஐ **Clean Price**-ஆ store பண்ணும் — அதுதான் சந்தை வழக்கம், dealers quote பண்றதும் price feed-ல நகர்றதும் அதுதான். ஆனா உண்மையில settle ஆகற cash ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7) எப்பவும் **Dirty Price**. Settlement amount `Quantity × Clean Price / 100`-க்கு சமமில்லன்னா, அது தானா bug இல்ல — Accrued Interest சேர்ந்திருக்கான்னும், trade coupon தேதிகளுக்கு நடுவுல விழுந்ததான்னும் பாருங்க.

### கூடுதல் உதாரணம் — Settlement நாள் மாற மாற Accrued Interest எப்படி நகரும்?

அதே bond (CAD 10,000,000, semi-annual coupon CAD 250,000, 182-day period):

| Last coupon-ல இருந்து நாட்கள் | Accrued Interest (CAD) | Clean 98.50-ல Dirty Price (CAD) |
|---|---|---|
| 0 (coupon தேதிக்கு அடுத்த நாளே) | 0 | 9,850,000 |
| 60 | 82,418 | 9,932,418 |
| 120 | 164,835 | 10,014,835 |
| 182 (அடுத்த coupon தேதி) | 250,000 | 10,100,000 |

Coupon தேதியன்னைக்கு buyer முழு coupon-ஐ வாங்கப்போறார்; அதுக்கு முன்னாடி வாங்கினா seller-க்கு அவர் வெச்சிருந்த நாட்களுக்கான பங்கை Accrued Interest-ஆ கொடுக்கணும். அதனாலதான் coupon தேதிக்கு உடனே அப்புறம் Clean-உம் Dirty-உம் கிட்டத்தட்ட சமமாகும்.

### Duration — ஒரு வரி முன்னோட்டம்

**Definition:** **Duration** (குறிப்பா *Modified Duration*) = yield 1% மாறினா bond விலை எவ்வளவு நகரும்ங்கறதோட தோராய அளவு. Modified Duration 8 உள்ள bond, yields 1% உயர்ந்தா விலை சுமார் 8% குறையும், yields 1% குறைஞ்சா சுமார் 8% உயரும். முழு கணக்கு (அதோட நெருங்கிய உறவான DV01-உம்) [Module 10](../docs/1-capital-markets/module-10-valuation.html) (Valuation)-க்கு சொந்தம் — இப்போதைக்கு உள்ளுணர்வு மட்டும்: **நீண்ட maturity-உம் குறைந்த coupon-உம் பொதுவா அதிக duration — அதாவது rate நகர்வுகளுக்கு அதிக விலை உணர்திறன்.** Basis point என்ற அலகே — bond சந்தை ஏன் yield/price உணர்திறனை "சதவீதத்துக்கு" இல்லாம "basis point-க்கு" அளக்குது, அதே DV01 mechanism Rates, Credit, Equity, FX முழுக்க எப்படி மறுபடி பொருந்துதுன்னு — தனி [Module 24 — Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) பாருங்க.

**Takeaway:** Fixed Income சொற்கள் எல்லாமே ஒரே நோக்கத்துக்குத்தான் — issuance-ப்போ *நிலையானதை* (Face Value, Coupon Rate, Maturity) தினமும் *market நிர்ணயிப்பதில்* இருந்து (Price, Yield, Accrued Interest, அதனால Dirty Price) பிரிக்கறது. இந்தப் பிரிவைச் சரியா வைக்கறதுதான் எந்த bond தொடர்பான reporting field-க்கும் கிடைக்கற மிகப் பயனுள்ள mental model.

---

## 2. Products என்னென்ன? — Government, Corporate, Municipal & Other Debt

```text
FIXED INCOME
│
├── Government Bonds        (sovereign issuer, highest credit quality in its own currency)
├── Corporate Bonds         (issued by companies, incl. banks like Bank Of Toronto)
├── Municipal Bonds         (issued by cities/provinces/states/municipalities)
└── Other Debt Instruments  (money market & securitized: T-Bills, CP, CDs, MBS/ABS/CLO)
```

| Product | அது என்ன | Example |
|---|---|---|
| **Government Bonds** | தேசிய அரசு வெளியிடற debt; பொதுவா அந்த currency-ல benchmark risk-free rate | Canada 5% Bond 2035 ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1) |
| **Corporate Bonds** | Operations/விரிவாக்கத்துக்கு நிதி திரட்ட ஒரு company வெளியிடற debt; அதே issuer-ஓட அரசுக் கடனுக்கு இல்லாத credit risk உண்டு | Bank Of Toronto 4.5% Bond 2030 ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §6) |
| **Municipal Bonds** | நகரம், மாகாணம், அல்லது (US-ல) state/உள்ளூர் அரசு வெளியிடற debt; பெரும்பாலும் infrastructure-க்கு | City of Toronto 3.75% Green Bond 2032 |
| **Other Debt Instruments** | Money market (குறுகிய maturity, <1 வருஷம்), securitized debt | Treasury Bills, Commercial Paper, CDs, MBS/ABS/CLO ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §6) |

Municipal Bonds-ல ஒரு US-specific நுணுக்கம்: பல **US municipal bonds-ஓட வட்டிக்கு US federal income tax விலக்கு** (பெரும்பாலும் வெளியிடும் state-ல வசிப்பவருக்கு state tax-உம் இல்ல) — Canadian municipal/provincial bonds-க்கு பரவலான ஒப்புமை இல்லாத ஒரு தனித்துவமான tax அம்சம்; ஒரே தரமுள்ள corporate bond-ஐ விட US munis கணிசமா குறைந்த yield-ல trade ஆகறதுக்கு இது பெரிய காரணம்.

**Takeaway:** இந்தப் பட்டியல்ல கீழ போகப் போக, instrument-ஓட risk profile bond-ஓட mechanics-ஐ விட *issuer யார்*ங்கறதாலதான் அதிகம் தீர்மானிக்கப்படுது — Government Bond-ஓட முக்கிய மாறி interest-rate risk ([Module 8](../docs/1-capital-markets/module-08-rates.html): Rates); Corporate Bond அதே coupon/price/yield mechanics மேல credit risk-ஐயும் ([Module 9](../docs/1-capital-markets/module-09-credit.html): Credit) சேர்க்குது.

---

## 3. உண்மையில எது Trade ஆகுது? — Security vs Contract

**Definition:** Bond தானே, share மாதிரி, ஒரு **உண்மையான security** — CSD-ல book-entry-ஆ பதிவான ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7) transfer பண்ணக்கூடிய உண்மையான உரிமை. ஆனா Fixed Income, Equity-க்கு இந்த நிலைல இல்லாத ஒரு product-ஐ அறிமுகப்படுத்துது: **repo** — bond-ஓட *மேல* கட்டப்படற, security இல்லாத **contract**.

```text
BOND (SECURITY)  vs  REPO (CONTRACT)
========================================================================

  Canada 5% Bond 2035 (SECURITY):
    A real, transferable claim on the Government of Canada. It exists
    independently of any one trade, the same way Coca-Cola stock does
    (Module 4 §3).

  Repo — "Repurchase Agreement" (CONTRACT):
    Bank Of Toronto lends EUR 50,000,000 cash overnight to Goldman
    Sachs, who posts Germany 3% Bund 2033 as COLLATERAL and agrees
    to repurchase it (i.e., return the cash + interest and get the
    Bund back) the next business day.

    Overnight interest = 50,000,000 x 3.15% x (1/365) = EUR 4,315
    (this is the exact trade from systems-landscape §12's SFTR
    worked example — a repo IS a Securities Financing Transaction)

  -> The Bund itself doesn't change hands as a SALE — legal title
     temporarily transfers as COLLATERAL, with a contractual promise
     to reverse the transfer the next day. This is economically a
     secured cash loan, not a purchase of the bond.
```

**Takeaway:** இது [Module 4](../docs/1-capital-markets/module-04-equity.html) §3-ஓட "security vs contract" வேறுபாட்டோட Fixed Income பதிப்பு — ஆனா Equity Option stock-ஓட விலையை reference பண்ற *derivative* contract; Repo, bond-ஐ collateral-ஆ பயன்படுத்தற *financing* contract. முற்றிலும் வேற வகை contract — அதனாலதான் இது EMIR-ல இல்ல, SFTR-ல (Section 8) report ஆகுது.

### கூடுதல் உதாரணம் — Repo என்றால் ஒரு "அடமானக் கடன்" மாதிரி

தமிழ்ல புரிஞ்சுக்க: நீங்க நகையை அடமானம் வெச்சு பணம் வாங்கறீங்க; வட்டியோட பணத்தைத் திருப்பினா நகை திரும்பக் கிடைக்கும். Repo-ல நகை = Bund bond, வங்கி = பணம் கொடுக்கற Bank Of Toronto, நீங்க = பணம் வாங்கற Goldman Sachs.

| நாள் | நடப்பது |
|---|---|
| Day 1 | Bank Of Toronto → Goldman Sachs: EUR 50,000,000 cash. Goldman Sachs → Bank Of Toronto: Germany 3% Bund 2033 (collateral) |
| Day 2 | Goldman Sachs → Bank Of Toronto: EUR 50,000,000 + EUR 4,315 வட்டி. Bank Of Toronto → Goldman Sachs: Bund திரும்ப |

Bank Of Toronto-க்கு Bund-ஓட price risk இல்ல (அது collateral தான், திரும்பக் கொடுக்கப்போறது); அதுக்கு கிடைக்கறது EUR 4,315 வட்டி — secured loan.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked examples இருக்கு.** கீழ உள்ள government bond trade-க்கு [Fixed Income Bond Trade — Complete Example](../examples/Fixed_Income_Bond_Complete_Example.html) பாருங்க. இந்த module-ஓட repo trade-க்கு [Repo / Securities Financing — Complete Example](../examples/Repo_Securities_Financing_Complete_Example.html) — ரெண்டுமே உண்மையான எண்களோட முழு trade lifecycle-ஐ நடத்திக் காட்டுது.

### 4a. Government Bond

```text
GOVERNMENT BOND  —  Canada 5% Bond 2035 (ISIN CA1358XXXXX)
========================================================================

  Buyer:  Bank Of Toronto
  Seller: Goldman Sachs
  (the secondary-market trade first introduced in Module 1 §1)

  Trade: CAD 10,000,000 face value @ Price 98.50
  Clean Price Value = 10,000,000 x 98.50 / 100 = CAD 9,850,000
  (see Section 1 for the Dirty Price/Accrued Interest add-on)
```

### 4b. Corporate Bond

```text
CORPORATE BOND  —  Bank Of Toronto 4.5% Bond 2030
========================================================================

  Issuer: Bank Of Toronto
  Buyer:  Maple Leaf Pension Fund

  Trade: CAD 12,000,000 face value @ Price 101.75 (premium — market
         rates have fallen below the 4.5% coupon since issuance,
         Module 1 §1's price-vs-rates mechanic)
  Trade Value = 12,000,000 x 101.75 / 100 = CAD 12,210,000
```

### 4c. Municipal Bond

```text
MUNICIPAL BOND  —  City of Toronto 3.75% Green Bond 2032
========================================================================

  Issuer: City of Toronto (proceeds earmarked for green
          infrastructure — a "use of proceeds" bond)
  Buyer:  Maple Leaf Pension Fund
  Dealer: Bank Of Toronto

  Trade: CAD 15,000,000 face value @ Price 97.25
  Trade Value = 15,000,000 x 97.25 / 100 = CAD 14,587,500

  Note: unlike a US municipal bond, this Canadian municipal bond's
  interest is fully taxable to Maple Leaf Pension Fund — the
  broad tax-exemption feature (Section 2) is specifically a US
  municipal-bond feature, not a general "municipal bond" one.
```

> **இது primary issuance, secondary-market trade இல்ல.** City of Toronto-ஓட bond இந்த trade-க்கு முன்னாடி இன்னும் இல்லவே இல்ல — Bank Of Toronto, ஏற்கனவே இருக்கற security-ஐ ரெண்டு வேற market participants-க்கு இடையில கை மாத்தற secondary-market dealer மாதிரி இல்லாம, **ஒரு புத்தம் புது bond-ஐ underwrite/place** பண்ணுது ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1-ஓட primary-market mechanics). CAD 14,587,500 ஒரே திசைல, Bank Of Toronto வழியா, end buyer-ல இருந்து issuer-க்குப் போகுது:

```text
PRIMARY ISSUANCE CHAIN — WHO OWES WHAT TO WHOM
========================================================================

  Maple Leaf Pension Fund  ---- pays CAD 14,587,500 ---->  Bank Of Toronto
  Bank Of Toronto          ---- pays (proceeds, less underwriting
                                 fee/spread) --------------> City of Toronto

  -> Bank Of Toronto is the underwriter/placement agent standing
     between a NEW issuer and the end buyer — a different role
     than Section 4a's Government Bond trade, where Bank Of
     Toronto is simply the BUYER of an already-existing security
     with no issuer in the chain at all.
```

### 4d. Other Debt Instrument — Treasury Bill

```text
TREASURY BILL  —  Government of Canada 90-Day T-Bill
========================================================================

  Buyer: Bank Of Toronto (money-market desk)

  Face Value: CAD 20,000,000, Maturity: 90 days, NO coupon
  Purchase Price: 98.75 (per 100) -- bought at a DISCOUNT to par

  Purchase Cost = 20,000,000 x 98.75 / 100 = CAD 19,750,000
  Discount Earned = 20,000,000 - 19,750,000 = CAD 250,000
                    (this IS the return -- there is no coupon)

  Discount Yield = (Discount / Purchase Cost) x (365 / Days to Maturity)
                 = (250,000 / 19,750,000) x (365 / 90)
                 = 1.2658% x 4.0556
                 = 5.13%

  -> T-Bills return purely the gap between discounted purchase
     price and full Face Value at maturity -- there's no coupon
     to separate from yield the way Section 1 did for the 5% bond.
```

**Takeaway:** நாலு உதாரணங்களும் [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §3-ல இருக்கற அதே core fields-ஐ (Face Value, Price, Trade Value) பகிர்கின்றன — மாறுறது issuer யார், coupon இருக்கா இல்லையா (T-Bill-க்கு இல்ல), tax treatment. "Fixed Income trade எப்படி price/value பண்ணப்படுது"ங்கற mechanics முழு asset class முழுக்க ஒரே மாதிரி; credit-உம் tax profile-உம் மட்டும் மாறுது.

---

## 5. Trade Lifecycle — Fixed Income-க்குரிய Walkthrough

**Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting** pattern-ஐ, Canada-ல execute ஆன Section 4a-ஓட Government Bond trade-மேல பொருத்தறோம்.

```text
CANADA 5% BOND 2035  —  FULL LIFECYCLE
========================================================================

  10:15:00  ORDER — Bank Of Toronto's desk instructed to buy
            CAD 10,000,000 face value, Canada 5% Bond 2035

  10:15:22  EXECUTION @ Price 98.50 (Clean) vs Goldman Sachs,
            interbank market

  10:15:22  TRADE CAPTURE — Trade ID BOT-FI-2026-013380
            Settlement falls 60 days after the last coupon date
            (Section 1) -> Accrued Interest = CAD 82,418

  10:30:00  CONFIRMATION/MATCHING -> MATCHED
            (no CCP for this bilateral OTC bond trade — Section 7)

  Settlement Date = T+2 (Government of Canada bonds — systems-
            landscape §04's Canada table)
            CDS/CDSX performs DvP:
              Bond: CAD 10,000,000 face value moves from Goldman
                    Sachs' to Bank Of Toronto's CDS account
              Cash: DIRTY PRICE = CAD 9,932,418 moves the other way
                    (Clean Price CAD 9,850,000 + Accrued Interest
                    CAD 82,418 — Section 1)

  POSITION — Bank Of Toronto's Fixed Income book increases by
            CAD 10,000,000 face value of this bond

  VALUATION / P&L — marked daily to the current market Clean
            Price, PLUS newly-accruing interest each day:
              Settlement day:  Clean 98.50, Accrued 82,418
              One week later:  Clean 98.65, Accrued 106,164
                (7 more days of accrual: 250,000 x 67/182)
              Price P&L   = (98.65 - 98.50)/100 x 10,000,000 = +15,000
              Accrual P&L = 106,164 - 82,418               = +23,746
            -> both components are real and get reported SEPARATELY
               in most Fixed Income P&L systems (Module 10)

  REPORTING — trade reported to the relevant Trade Repository/
            regulator by T+1 (Module 3 §09)
```

**Takeaway:** [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity walkthrough-ல இருந்து lifecycle-ல இருக்கற மிகப்பெரிய வித்தியாசம் — Fixed Income-ஓட Valuation/P&L stage ஒவ்வொரு நாளும் **ரெண்டு** components-ஆ பிரியுது: price நகர்வு + interest accrual; equity position-க்கு ஒண்ணே ஒண்ணு (price நகர்வு; dividends எப்போவாவது வருபவை, தொடர்ந்து accrue ஆகாது). Price நகர்வை மட்டும் காட்டற bond P&L report, accrual leg-ஐ அமைதியா தவறவிடுது.

> **Deep dive இருக்கு.** Position-ன்னா என்ன, ஒரு unit yield/price நகர்வை உண்மையான dollar எண்ணா மாத்தற பெருக்கி Position SIZE ஏன்னு முழுசா: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §4.

---

## 6. Identifiers — Bonds-க்கு பொருத்தி

Bonds, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2-ஓட, [Module 4](../docs/1-capital-markets/module-04-equity.html) §6-ஓட அதே identifier set-ஐ மறுபடி பயன்படுத்தும், ஒரு கட்டமைப்பு வேறுபாட்டோட: **bonds-க்கு கிட்டத்தட்ட Ticker இருக்கவே இருக்காது.**

| Identifier | Government Bond | Corporate Bond | குறிப்புகள் |
|---|---|---|---|
| ISIN | `CA1358XXXXX` (Canada 5% Bond 2035) | `CA0553XXXXX` (Bank Of Toronto 4.5% Bond 2030, illustrative) | Global, முதன்மை bond identifier |
| CUSIP | `135087XX1` (illustrative) | `055349XX1` (illustrative) | North America மட்டும் |
| Ticker | — இல்ல — | — இல்ல — | Bonds exchange tickers-ஓட trade ஆகாது ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2) |
| LEI | issuer-ஆன **Government of Canada**-வை identify பண்ணும் | issuer-ஆன **Bank Of Toronto**-வை identify பண்ணும் | Entity, instrument இல்ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §6) |
| UPI | பொருந்தாது | பொருந்தாது | UPI OTC *derivatives்*-க்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) — cash bond அது இல்ல. Bond *future* ([Module 8](../docs/1-capital-markets/module-08-rates.html): Rates) அதுக்குப் பதிலா, [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட equity future மாதிரி exchange contract code பெறும் |

**Takeaway:** Reporting record-ல ISIN/CUSIP இருந்து Ticker இல்லன்னா, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2 ஏற்கனவே இதை bond-ஓட classic அடையாளம்னு குறிச்சது. இந்த module எதிர்த்திசை check-ஐ சேர்க்குது: அதே record-ல UPI-உம் நிரம்பியிருந்தா, ஏதோ தப்பு — cash bond-க்கு UPI இருக்கவே கூடாது.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** "exchange-traded"-உம் "centrally cleared"-உம் பெரும்பாலும் ஒண்ணா பயணிக்கற [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity போல இல்லாம, **cash Fixed Income trades பெரும்பாலும் OTC, bilateral** — bond buyer-seller நடுவுல பொதுவா CCP இருக்காது. Repo ஒரு சுவாரஸ்யமான விதிவிலக்கு: bilateral-ஆவும் இருக்கலாம், centrally cleared-ஆவும் இருக்கலாம், எது என்பதற்கு உண்மையான விளைவுகள் உண்டு.

```text
FIXED INCOME'S VERSION OF THE TWO-AXIS MODEL
========================================================================

                    BILATERAL                  CENTRALLY CLEARED
   ------------------------------------------------------------------
   EXCHANGE-        n/a — bonds essentially      A few retail bond
   TRADED            never trade on an             platforms exist,
                      exchange                      but volume is tiny
   ------------------------------------------------------------------
   OTC               MOST Government, Corporate,   Repo, increasingly:
                      and Municipal bond trades     CDCC (Canada, since
                      (Section 4a-4c above) —        2012), FICC (US
                      dealer-to-dealer or             Treasury repo,
                      dealer-to-client, no CCP        systems-landscape §05)
```

| Cash / SFT | Product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|---|
| CASH | Government Bond | OTC | Bilateral (வழக்கம்) |
| CASH | Corporate Bond | OTC | Bilateral (வழக்கம்) |
| CASH | Municipal Bond | OTC | Bilateral (வழக்கம்) |
| SFT | Repo | OTC | **ரெண்டுல ஒண்ணு** — default-ஆ bilateral, ஆனா மிகப்பெரிய, அதிக standardized repo activity-க்கு CDCC/FICC வழியா அதிகரித்து route ஆகுது |

**Takeaway:** இது [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity table-ல இருந்த default-க்கு எதிர்மாறு — அங்க bilateral-and-exchange-traded "கிட்டத்தட்ட இல்லவே இல்ல", centrally-cleared வழக்கம். Fixed Income-ல bilateral OTC வழக்கம், central clearing (வளர்ந்து வரும்) விதிவிலக்கு — அதனாலதான் bond சந்தை regulation (Section 8), equity regulators ஓரளவு நம்பற மாதிரி CCP-யோட சொந்த records-ஐ நம்பாம, *transaction reporting*-ஐ (MiFIR) இவ்வளவு சார்ந்திருக்கு.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | Fixed Income-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Bond transaction reporting, pre/post-trade transparency — equities-ஐ விட வேறுபட்ட thresholds-ஓட (உதா., பெரிய bond trades-க்கு publication-ஐ தாமதிக்கும் "large-in-scale" waivers; bonds பெரிய large-cap stocks-ஐ விட எவ்வளவு குறைவான liquid என்பதை காட்டுது) |
| **EMIR** | Cash bond trade-க்கு **பொருந்தாது** — bond derivative இல்ல. *Bond future அல்லது bond option*-க்கு பொருந்தும் ([Module 8](../docs/1-capital-markets/module-08-rates.html): Rates); [Module 4](../docs/1-capital-markets/module-04-equity.html) §8-ஓட equity-derivatives விதிவிலக்கின் Fixed Income இணை |
| **SFTR** | **Repo**-க்கு நேரடியா பொருந்தும் (Section 3-ஓட worked example) — [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12 உதாரணத்தோட underlying instrument ஆன Germany 3% Bund 2033 உண்மையில வர்ற module இதுதான் |
| **FCA** (UK) | UK bond trading-ஓட conduct regulator, UK MiFIR bond transaction reporting-ஐ நிர்வகிக்குது |
| **CIRO** (Canada) | Canadian bond dealer conduct-ஐ மேற்பார்வை பண்ற SRO |
| **HKMA** (Hong Kong) | இங்க Equity-ஐ ([Module 4](../docs/1-capital-markets/module-04-equity.html) §8, அங்க SFC தான் அதிகம் பொருத்தமானது) விட நிஜமாவே மையமானது: HKMA நேரடியா **Exchange Fund Bills & Notes**-ஐ (Hong Kong-ஓட குறுகிய/நடுத்தர கால அரசுக் கடன்) வெளியிடுது; central-bank பங்குக்கு மேல, அந்தப் பிரதேசத்துக்கு debt management office மாதிரி செயல்படுது |
| **DTCC** (US) | அதன் subsidiary **FICC** குறிப்பா US Treasuries, Treasury repo-வை clear பண்ணுது — [Module 4](../docs/1-capital-markets/module-04-equity.html) தொட வேண்டிய அவசியப்படாத DTCC-யோட Fixed Income-குறிப்பான பகுதி |
| **DMO** (UK Debt Management Office) | இது அதன் module: DMO, UK Gilts-ஐ auction மூலம் வெளியிடுது — Government of Canada bonds-ஐ auction மூலம் ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1), US Treasury Treasuries-ஐ (Federal Reserve fiscal agent-ஆ) வெளியிடற அதே மாதிரி UK-ஓட நேரடி இணை. ஒரு GEMM DMO-க்கு என்ன report பண்ணுது, என்ன data-வோடன்னு [UK DMO Reporting Deep Dive](../docs/2-reporting/module-17-dmo-uk-reporting.html) பாருங்க |

**Takeaway:** [Module 4](../docs/1-capital-markets/module-04-equity.html)-ல ஓரமா சொல்லப்பட்ட ரெண்டு அமைப்புகள் (HKMA, DMO) இங்க முழுக்க பொருத்தமாகுது — Section 8-ஓட regulatory perspective நீங்க பார்க்கற asset class-ஐ உண்மையில சார்ந்தது; அதே acronym ஒரு module-ல *முக்கிய* actor-ஆவும் இன்னொண்ணுல footnote-ஆவும் இருக்கலாம். Asset Class Roadmap-ஓட எட்டு-பாக pattern ஏற்படுத்த நினைக்கறதும் அதுதான்.

---

## 9. Canadian Systems Landscape — Fixed Income Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §2 Canada-க்கான *பொதுவான* Front/Middle/Back Office architecture-ஐ ஏற்கனவே வகுத்திருக்கு. இந்த section அதை **Fixed Income-க்கு மட்டும்** சுருக்குது — Section 4a-ஓட Canada 5% Bond 2035 trade-ஐ running example-ஆ வெச்சு, ஒரு Canadian bond desk-ஓட trade execution-ல இருந்து CIRO/Bank of Canada மேற்பார்வை வரை தொடுற உண்மையான platforms-ஐ பெயரிட்டு.

> **Callout — illustrative, பரிந்துரை இல்ல.** கீழ இருக்கற platform பெயர்கள் (CanDeal, Bloomberg, Murex/Calypso/Summit) உண்மையான Canadian fixed income market infrastructure-ஓட பிரதிநிதிகள் — Bank Of Toronto தானே உள்ளுக்குள்ள எந்த systems-ஐ ஓட்டுதுங்கறதைப் பத்தின கூற்று இல்ல. Institutional infrastructure (CDS/CDSX, CDCC, CIRO, Bank of Canada) தான் இந்த section-ல உண்மையான பகுதி, illustrative இல்ல.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | **CanDeal** (Canada's dealer-to-client electronic platform for government & corporate bonds); Bloomberg ALLQ/BondTrader (illustrative); the **Bank of Canada's** own auction system for new-issue Government of Canada bonds | Price discovery and execution — where Bank Of Toronto's desk actually buys/sells the bond, or bids at a GoC auction as a primary dealer |
| **Middle Office** | Bilateral/platform trade confirmation; valuation & risk systems (Murex, Calypso, Summit — illustrative, the same vendor family [systems-landscape](../docs/1-capital-markets/systems-landscape.md) §11 already names) | Confirms the trade matches both sides' understanding (Section 5's "CONFIRMATION/MATCHING" stage); computes Clean/Dirty Price and Accrued Interest (Section 1) for P&L |
| **Back Office** | **CDS Clearing and Depository Services Inc.**, operating **CDSX** — book-entry DvP settlement of the bond itself; **CDCC (Canadian Derivatives Clearing Corporation)**, which has, since 2012, separately cleared the **fixed income repo market** (Section 3's repo distinction — CDCC's repo-clearing role is already established in this module's Section 8 and in [systems-landscape](../docs/1-capital-markets/systems-landscape.md) §12) | Settles cash bond trades T+2 via CDSX; clears standardized repo activity via CDCC where applicable |
| **Regulator / Reporting** | **CIRO** (bond-dealer conduct, corporate-bond post-trade transparency); **Bank of Canada** (fiscal agent for GoC auctions; oversees CDSX and CDCC as designated systemically important FMIs); **CSA Multilateral Instrument 96-101** (OTC derivatives trade reporting only) | Conduct/market oversight, systemic-infrastructure oversight, and — where MI 96-101 actually applies — trade reporting to a CSA-recognized Trade Repository |

```text
CANADA 5% BOND 2035 — FRONT TO BACK, CANADIAN SYSTEMS VIEW
========================================================================

  FRONT OFFICE
    Bank Of Toronto's desk executes CAD 10,000,000 face value
    @ Clean Price 98.50 vs Goldman Sachs (Section 4a/5) --
    interbank OTC, no exchange (Section 7) -- via a platform such
    as CanDeal, or voice/chat confirmed into the same workflow
         |
         v
  MIDDLE OFFICE
    Trade confirmed/matched (Section 5's 10:30:00 step); Clean
    Price 98.50 + Accrued Interest CAD 82,418 = Dirty Price
    CAD 9,932,418 (Section 1) computed and checked
         |
         v
  BACK OFFICE
    CDS / CDSX -- book-entry DvP, T+2 (systems-landscape §04):
      Bond leg: CAD 10,000,000 face value, Goldman Sachs -> Bank
                Of Toronto's CDS account
      Cash leg: CAD 9,932,418, Bank Of Toronto -> Goldman Sachs
    (a REPO on this same bond, instead, would route through CDCC's
    repo-clearing service rather than a plain CDSX DvP -- Section 3)
         |
         v
  REGULATOR / OVERSIGHT
    CIRO -- dealer-conduct oversight of both counterparties
    Bank of Canada -- systemic oversight of CDSX itself as the
      designated FMI actually performing the settlement above
    CSA MI 96-101 -- NOT triggered by this trade; it is scoped to
      OTC DERIVATIVES reporting, and a cash Government of Canada
      bond purchase is neither OTC-derivative nor SFT (contrast
      Section 3's repo, which — per systems-landscape §12 — still
      has NO dedicated SFTR-equivalent reporting regime in Canada
      as of 2026, unlike the EU/UK module-16-sftr-reporting module's regime)
```

**Takeaway:** இந்த trade-ஓட பயணத்துல எவ்வளவு பகுதி CCP-யையோ Trade Repository-யையோ தொடவே இல்லைன்னு கவனிங்க — cash Government of Canada bond purchase CDSX வழியா bilaterally settle ஆகி, CIRO/Bank of Canada-வால மேற்பார்வை செய்யப்படுது, ஆனா CDCC clearing-ஐயும் (அது repo-குறிப்பானது) CSA MI 96-101 reporting-ஐயும் (அது OTC-derivative-குறிப்பானது) தூண்டறதில்ல. "இந்த trade உண்மையில எந்த systems-ஐ தொடுது?" என்ற அதே கேள்வியை Section 3-ஓட repo மேல கேட்டா, உண்மையிலயே வேற பதில் — back office-ல CDCC, regulatory layer-ல இன்னும் Canadian SFT reporting regime இல்ல, [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12 ஏற்கனவே குறிச்ச இடைவெளி.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Government of Canada | Canada 5% Bond 2035-ஓட issuer |
| Bank Of Toronto | Government bond-ஓட buyer; தன் சொந்த 4.5% Bond 2030-ஓட issuer; municipal bond trade-ல dealer |
| Goldman Sachs | Government bond-ஓட seller; repo counterparty (cash கடன் வாங்கறவர், Bund collateral போடறவர்) |
| Maple Leaf Pension Fund | Corporate bond, municipal bond-ஓட buyer |
| City of Toronto | Green Bond 2032-ஓட issuer |
| CDS / CDSX | Canadian government bond trade-ஐ settle பண்ற CSD ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §04) |
| CDCC / FICC | Repo-வை centrally clear பண்ணக்கூடிய CCPs (முறையே Canada / US) |
| CIRO | Canadian bond-dealer conduct, post-trade transparency-ஐ மேற்பார்வை பண்ற SRO (Section 8, Section 9) |
| Bank of Canada | Government of Canada bond auctions-ஓட fiscal agent; CDSX, CDCC-யோட systemic overseer (Section 9) |

---

## Recap Quiz — Numbers Edition

1.  Section 5-ல Canada 5% Bond 2035 trade-ல, settlement அதே 182-நாள் coupon period-ல 120-ஆம் நாளில விழுந்திருந்தா, புது Accrued Interest-உம் Dirty Price-உம் என்ன?
2.  Section 4d-ல இருக்கற T-Bill-க்கு, Section 1-ஓட worked example மாதிரி "coupon vs yield" வேறுபாடு இல்லைன்னு ஏன் சொல்றாங்க?
3.  Section 3-ஓட repo-ல, Goldman Sachs German Bund-ஓட buyer-ஆ seller-ஆ — ரெண்டுல எந்த label-உம் பொருளாதார ரீதியா நடக்கறதுக்கு உண்மையில துல்லியமா இல்லாதது ஏன்?
4.  Section 6-ஓட table-ஐ வெச்சு, மற்றபடி bond போல இருக்கற trade record-ல UPI field இருக்கறது அந்த trade பத்தி பெரும்பாலும் என்ன சொல்லுது?
5.  "Fixed Income bilateral OTC"-க்கு Section 7 repo-வை ஏன் விதிவிலக்குன்னு குறிக்குது? அதை centrally clear பண்ணக்கூடிய இரண்டு பெயரிடப்பட்ட CCPs எவை?
6.  Fixed Income trade போல தெரியற ஒண்ணுக்கு ஒரு trade record-ல EMIR reporting fields நிரம்பியிருக்கு. Section 8-ஐ வெச்சு, அது instrument பத்தி என்ன சொல்லுது? பெரும்பாலும் என்ன விளக்கம்?
7.  இந்த module-ல HKMA-ஓட பொருத்தத்தை [Module 4](../docs/1-capital-markets/module-04-equity.html)-ஓட Equity module-ஓட ஒப்பிடுங்க — இங்க HKMA-வை குறிப்பிட வச்ச குறிப்பிட்ட Fixed-Income தொடர்பான செயல்பாடு என்ன?
8.  Section 9-ஐ வெச்சு, Section 4a/5-ஓட Canada 5% Bond 2035 trade எப்போதாவது CDCC-யை தொடுமா, இல்ல CSA MI 96-101 reporting-ஐ தூண்டுமா? அதே bond repo-வில collateral-ஆ பயன்படுத்தப்பட்டா என்ன நடக்கும்ங்கறதோட ஒப்பிடுங்க.

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் Asset Class Roadmap-ஓட அடுத்த நிறுத்தம் **[Module 6](../docs/1-capital-markets/module-06-fx.html): FX**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Accrued Interest = CAD 164,835; Dirty Price = CAD 10,014,835.**

```text
Accrued Interest = 10,000,000 x 2.50% x (120 / 182) = 250,000 x 0.65934 = CAD 164,835
Dirty Price = 9,850,000 (Clean) + 164,835 = CAD 10,014,835
```

Settlement coupon period-ல எவ்வளவு தாமதமா விழுதோ, அவ்வளவு accrued interest சேர்ந்திருக்கும் (last coupon-ல இருந்து நாட்கள் 182-ஐ நெருங்க நெருங்க முழு CAD 250,000 semi-annual coupon-ஐ நெருங்கும்) — Section 1 நிறுவின அதே linear accrual mechanic.

**2. ஏன்னா T-Bill-க்கு coupon-ஏ இல்ல — அதோட முழு return, discount-ல வாங்கின விலைக்கும் maturity-ல கிடைக்கற முழு Face Value-வுக்கும் இடையிலான இடைவெளில இருந்து வருது; அதனால "yield"-ஐ ஒப்பிட தனி "coupon rate" எண் இல்ல.**

Section 1-ஓட coupon-vs-yield வேறுபாடு இருக்கறதுக்கு காரணம், coupon bond-க்கு வேறுபடக்கூடிய *ரெண்டு* எண்கள் உண்டு (நிலையான coupon rate, விலையை சார்ந்த yield). T-Bill-க்கு ஒரே ஒரு return mechanism — discount — அதனால "yield" மட்டுமே இருக்கும் எண்; ஒப்பிட எதுவும் இல்ல.

**3. Goldman Sachs தெளிவா buyer-உம் இல்ல, seller-உம் இல்ல — பொருளாதார ரீதியா Goldman Sachs Bund-ஐ விக்கலை; அதை *collateral*-ஆ போட்டு cash *கடன் வாங்கறவர்*.**

Section 3 இதை நேரடியா சொல்லுது: Bund-ஓட legal title தற்காலிகமா மாறுது, ஆனா முழு transaction-உம் ஒரு secured loan-ஆ, மாற்றத்தை திரும்பப் பெறும் contractual வாக்குறுதியோட அமைக்கப்பட்டிருக்கு — அதனாலதான் repo outright purchase/sale-ஆ MiFID transaction reporting-ல பிடிபடாம, Securities *Financing* Transaction-ஆ (SFTR, Section 8) வகைப்படுத்தப்படுது.

**4. அது ஒரு cash bond இல்ல, bond *derivative* (உதா., bond future அல்லது bond option — [Module 8](../docs/1-capital-markets/module-08-rates.html): Rates-ல) என்பதை — அல்லது ஒரு data-quality error என்பதை — பெரும்பாலும் குறிக்குது.**

Section 6-ஓட takeaway இதை வெளிப்படையா சொல்லுது: UPI OTC derivatives-க்கு; cash bond-க்கு அது இருக்கவே கூடாது. மற்றபடி bond வடிவ record-ல அது நிரம்பியிருக்கறது, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2 bonds-ல Tickers பத்தி நிறுவிய அதே diagnostic சமிக்ஞை — எதிர்த்திசைல check-ஐ ஓட்டறது மட்டும்தான்.

**5. Repo விதிவிலக்கு, ஏன்னா bilateral OTC-ஆ இருக்கற outright government/corporate/municipal bond trades போல இல்லாம, standardized activity-க்கு CCP வழியா அதிகரித்து route ஆகுது — Canada-ல CDCC (2012 முதல்), US-ல FICC (குறிப்பா Treasury repo-க்கு).**

Section 7-ஓட table ரெண்டையும் பெயரிட்டு, முறையே Canadian, US market infrastructure-க்கான [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §04 (CDCC), §05 (FICC)-க்கு நேரடியா திரும்பிப் போகுது.

**6. அது plain cash bond இல்ல என்பதை — மேலோட்டமா Fixed Income cash trade போல தெரியற, ஆனா உண்மையில bond future அல்லது bond option (ஒரு interest-rate derivative, [Module 8](../docs/1-capital-markets/module-08-rates.html): Rates-ல முறையா) என்பதை — பெரும்பாலும் குறிக்குது.**

Section 8-ஓட table, EMIR cash bond-க்கு பொருந்தாது, derivative-க்கு மட்டும்னு சொல்லுது — EMIR fields நிரம்பியிருக்கறது, இது "Fixed Income-போல தெரியற" எந்த trade-ஆ இருந்தாலும், உண்மையில மாறுவேடத்துல ஒரு derivative-ங்கறதையும், Rates asset class-க்கு சொந்தமுங்கறதையும் காட்டும் வலுவான சமிக்ஞை.

**7. HKMA நேரடியா Exchange Fund Bills & Notes-ஐ — Hong Kong-ஓட சொந்த government debt instruments-ஐ — வெளியிடுது; வழக்கமான central-bank பங்குக்கு மேல, அந்தப் பிரதேசத்துக்கு debt management office மாதிரி செயல்படுது.**

[Module 4](../docs/1-capital-markets/module-04-equity.html)-ல (Equity), HKMA நடவடிக்கையிலிருந்து ஒரு படி தள்ளி இருக்குன்னு குறிக்கப்பட்டது; equity conduct regulation-ஓட உண்மையான வேலையை SFC செய்தது. இங்க HKMA நேரடியா Hong Kong government debt-ஓட issuer-பக்க actor — UK Gilts-க்கு DMO வகிக்கற அதே செயல்பாட்டு பங்கு (Section 8) — ஒரே regulator-ஓட பொருத்தம் asset class-ஓட மாறுதுங்கற Section 8-ஓட takeaway-க்கு ஒரு உறுதியான விளக்கம்.

**8. ரெண்டுக்கும் இல்ல — Section 4a/5 trade ஒரு plain cash Government of Canada bond purchase, அதனால CDSX வழியா மட்டும் settle ஆகுது; CDCC-யையும் (repo-குறிப்பானது) CSA MI 96-101-ஐயும் (OTC-derivative-குறிப்பானது) தொடாது. அதே bond repo-வில collateral-ஆ போடப்பட்டா, back-office பாதை CDCC-யோட repo-clearing service-க்கு மாறும் (repo அங்க route ஆகற அளவு standardized-ஆ இருந்தா), ஆனா CSA MI 96-101 *இன்னும்* பொருந்தாது — 2026-ல் Canada-ல EU/UK-ஓட SFTR போல தனி SFT reporting regime இல்ல.**

Section 9-ஓட worked diagram இதை வெளிப்படையா சொல்லுது: அதே underlying bond, அது எந்த *contract வகைல* சுற்றப்பட்டிருக்குன்னு மட்டும் பொறுத்து, ரெண்டு வேற back-office பாதைகளைத் தருது (outright purchase-க்கு CDSX, repo-க்கு CDCC) — Section 3-ஓட security-vs-contract வேறுபாடு, இப்போ எந்த Canadian system உண்மையில trade-ஐ தொடுதுங்கற வரை முழுசா தொடர்ந்து காட்டப்பட்டிருக்கு.
