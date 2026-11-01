# Module 15 — Listed Derivatives (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **ஆறு-asset-class roadmap-ஓட பகுதி இல்ல** (Equity → Fixed Income → FX → Commodities → Rates → Credit). Listed Derivatives cross-cutting: ஒரு Future அல்லது Listed Option அந்த ஆறுல எதோட underlying-ஐயும் *reference* பண்ணலாம்.
> **Work data model:** இந்த module taxonomy-ஓட **"7. Listed Derivatives"** node-க்கு map ஆகுது — asset class-ஏ இல்லாத ஒரே node; அவை எல்லாத்தையும் கடக்கற *venue/clearing வகை*.
> **மூலம்:** English Module 15-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Listed Derivative-ன்னா என்ன? — Theory & Terminology

**Definition:** Listed Derivative = ஒரு regulate பண்ணப்பட்ட **exchange**-ல trade ஆகற Future அல்லது Option; **standardized** contract terms (size, expiry தேதிகள், tick size) கொண்டது; execution-ஓட தருணத்துல இருந்தே அந்த exchange-ஓட சொந்த CCP வழியா clear ஆகுது. இந்த module roadmap முழுக்க ஏற்கனவே துண்டு துண்டா தோன்றியிருக்கு — [Module 4](../docs/1-capital-markets/module-04-equity.html) §4c-ஓட E-mini S&P 500 future, [Module 4](../docs/1-capital-markets/module-04-equity.html) §4d-ஓட AAPL listed call, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §4b-ஓட WTI crude future — ஒவ்வொண்ணும் அப்போ "உண்மையில Commodity- அல்லது Equity-குறிப்பான schema இல்ல, Listed Derivatives schema"ன்னு குறிக்கப்பட்டது. அந்த தொடர்ச்சியா வந்த cross-reference கடைசியா இங்க தன் சொந்த சிகிச்சையைப் பெறுது.

| Term | அர்த்தம் |
|---|---|
| Contract Specification | ஒரு contract-ஓட exchange வரையறுத்த, standardized terms — size, quality/grade (physical underlyings-க்கு), expiry cycle, tick size — OTC trade-ஓட சுதந்திரமா பேசப்பட்ட terms போல இல்லாம, அந்த contract-ல ஒவ்வொரு trade-க்கும் ஒரே மாதிரி |
| Tick Size / Tick Value | அனுமதிக்கப்பட்ட மிகச்சிறிய விலை அதிகரிப்பு, ஒரு tick-ஓட cash மதிப்பு — உதா., [Module 4](../docs/1-capital-markets/module-04-equity.html) §4c-ஓட E-mini S&P 500 0.25-point ticks-ல நகருது, ஒவ்வொண்ணும் USD 12.50 (0.25 x USD 50 multiplier) |
| Contract Month Code | Delivery/expiry மாதத்தை identify பண்ற ஒற்றை-எழுத்து code (F=Jan, G=Feb, H=Mar, J=Apr, K=May, M=Jun, N=Jul, Q=Aug, U=Sep, V=Oct, X=Nov, Z=Dec) — நேரடியா exchange contract code-க்குள்ள பதிக்கப்பட்டது (Section 6) |
| Open Interest | ஒரு குறிப்பிட்ட contract-க்கு தற்போது நிலுவையில உள்ள contracts-ஓட மொத்த எண்ணிக்கை — liquidity/நடவடிக்கை அளவீடு, OTC-க்கு சமமில்லாதது, ஏன்னா OTC trades இப்படி மைய கணக்கு வைக்கப்படறதில்ல |
| Novation at Execution | CCP, trade execute ஆகற **நொடியிலேயே** ஒவ்வொரு seller-க்கும் buyer-ஆ, ஒவ்வொரு buyer-க்கும் seller-ஆ ஆகுது ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §5) — வேற சில centrally-cleared products-க்கு நடக்கற மாதிரி அப்புறம் நடக்கற படி இல்ல |

**Takeaway:** ஒரு Listed Derivative-ஓட ஒவ்வொரு வரையறுக்கும் அம்சமும் — standardized terms, சுதந்திரமா quote பண்ணப்படற விலைக்குப் பதிலா tick size, contract month code, உடனடி novation — சரியா ஒரே காரணத்துக்காக இருக்கு: contract-ஐ **fungible**-ஆ ஆக்க, அப்போதான் எந்த buyer-ஓட position-உம் exchange-ஓட books-ல எந்த seller-ஓட position-க்கு எதிராகவும் net ஆக முடியும், ஒரு OTC-ல பேசப்பட்ட bespoke contract-ஆல ஒருபோதும் செய்ய முடியாதது.

---

## 2. Products என்னென்ன? — எதன் மேலயும் Futures, Listed Options

```text
LISTED DERIVATIVES
│
├── Futures          (standardized obligation to buy/sell at a future date, any underlying)
└── Listed Options    (standardized right, not obligation — on a future OR on the cash instrument)
```

| Product | அது என்ன |
|---|---|
| **Futures** | *ஏதோ ஒண்ணின்* நிலையான அளவை, நிலையான விலைல, நிலையான தேதில வாங்க/விற்க standardized, exchange-traded கடமை — அந்த "ஏதோ ஒண்ணு" ஒரு equity index ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4c), ஒரு commodity ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §4b), ஒரு bond (கீழ Section 4c), ஒரு currency pair, அல்லது ஒரு interest rate ஆக இருக்கலாம் |
| **Listed Options** | நிலையான strike-ல வாங்க (call) அல்லது விற்க (put) standardized, exchange-traded உரிமை (கடமை இல்ல) — cash instrument மேல நேரடியா ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4d-ஓட AAPL shares) அல்லது ஒரு Future மேலயே (T-Note future மேல option, Section 4d) |

```text
ONE PRODUCT PAIR, EVERY UNDERLYING
========================================================================

  Equity Index      ->  E-mini S&P 500 Future        (Module 4 §4c)
  Single Stock       ->  AAPL Listed Call              (Module 4 §4d)
  Commodity          ->  CME WTI Crude Future           (Module 7 §4b)
  Interest Rate       ->  CME 10-Year T-Note Future     (Section 4c, new)
  FX                  ->  CME EUR/USD Future             (not worked here,
                                                            but exists on the
                                                            same model)

  -> Five different asset classes, ONE Futures product mechanic and
     ONE Listed Options product mechanic, each time. This is the
     concrete meaning of "cross-cutting" from this module's masthead.
```

**Takeaway:** இந்த roadmap-ஓட இந்த நிலைக்குள்ள, ஐந்து வேறு underlyings ஒவ்வொண்ணும் ஒரு Future அல்லது Listed Option உதாரணத்தை ஏற்கனவே தந்திருக்கு, எல்லாமே அதே mechanics-ஐ பயன்படுத்துச்சு — standardized contract, execution-ல CCP novation, தினசரி variation margin. ஆறு modules முழுக்க அந்த திரும்பத் திரும்ப வருதல்தான், Future/Listed Option mechanic-ஐ ஆறு தடவை நகல் எடுக்காம, இந்த taxonomy Listed Derivatives-ஐ தனி node-ஆ பிரிக்க முழுக் காரணம்.

---

## 3. உண்மையில எது Trade ஆகுது? — Fungibility-க்காக Standardize பண்ணப்பட்ட, எதையும் Reference பண்ணும் Contract

**Definition:** இந்த roadmap-ல ஒவ்வொரு derivative போலவே, ஒரு Future அல்லது Listed Option ஒரு contract, security இல்ல — ஆனா ஒரு OTC contract-ஐ போல இல்லாம ([Module 6](../docs/1-capital-markets/module-06-fx.html) §3-ஓட FX, [Module 8](../docs/1-capital-markets/module-08-rates.html) §3-ஓட Rates), ஒரு Listed Derivative-ஓட terms **exchange-ஆல நிர்ணயிக்கப்படுது**, ரெண்டு counterparties இடையில பேசப்படறதில்ல. ஒரு மணி நேர இடைவெளில அதே contract-ஐ execute பண்ற ரெண்டு traders, tick வரை *ஒரே மாதிரி* terms-ஐ பெறுவாங்க.

```text
OTC CONTRACT (NEGOTIATED)  vs  LISTED CONTRACT (STANDARDIZED)
========================================================================

  OTC FRA (Module 8 §4b):
    Bank Of Toronto and Goldman Sachs negotiate a bespoke Notional
    (CAD 20,000,000), a bespoke FRA Rate (4.85%), a bespoke period
    (3x6) -- no two FRAs need look alike.

  Listed Future (this module):
    Every E-mini S&P 500 future traded on CME today has the SAME
    multiplier (USD 50/point), the SAME tick size (0.25 points),
    and expires on ONE of a small set of standardized quarterly
    dates -- the only thing that varies trade-to-trade is price
    and quantity.

  -> This is WHY a Listed Derivative can be netted, closed out, or
     rolled (Module 7 §4b) against ANY other market participant's
     opposite position in the same contract -- an OTC trade can
     only ever be unwound against the SAME original counterparty
     (or novated via a separate compression exercise), because no
     two OTC trades are guaranteed to share identical terms.
```

**Takeaway:** "ஏன் இந்த taxonomy Futures, Listed Options-ஐ underlying இயல்பா உட்காருற இடத்துல விடாம Equity/Commodities/Rates/FX-ல இருந்து தனியா பிரிக்குது?"ங்கறதுக்கான உண்மையான பதில் இதுதான் — standardization ஒரு *venue* பண்பு, *asset class* பண்பு இல்ல; அதனால "இந்த contract எப்படி கட்டமைக்கப்பட்டு clear ஆகுது"ங்கறதை சுத்தி அமைக்கப்பட்ட schema-வுக்கு, "இதன் மதிப்பு எதைச் சார்ந்தது"ங்கறதை சுத்தி அமைக்கப்பட்ட schema-வை விட உண்மையிலயே வேறு அச்சு தேவை.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked example இருக்கு.** முழு CME 10-Year T-Note Future hedge-ஐயும் AAPL listed call option-ஐயும், ரெண்டும் முழு trade lifecycle வழியா, [Listed Futures & Listed Options — Complete Example](../examples/Listed_Futures_and_Options_Complete_Example.html)-ல பாருங்க.

### 4a. Equity Index Future — recap

முழு worked example **[Module 4](../docs/1-capital-markets/module-04-equity.html) §4c**-ல: Maple Leaf Pension Fund 20 E-mini S&P 500 futures-ஐ 5,920.00-ல வாங்குது, notional USD 5,920,000, USD 50/point multiplier, CME Clearing-க்கு எதிரா தினசரி variation margin.

### 4b. Commodity Future — recap

முழு worked example **[Module 7](../docs/1-capital-markets/module-07-commodities.html) §4b**-ல: Bank Of Toronto 50 CME WTI Crude Oil futures-ஐ USD 75.50/bbl-ல வாங்கி, ஒரு மாசம் கழிச்சு position-ஐ முன்னோக்கி roll பண்ணி, contango செலவை realize பண்ணுது.

### 4c. Interest Rate Future — புதிய worked example

```text
INTEREST RATE FUTURE  —  CME 10-Year Treasury Note Future (ZN)
========================================================================

  Seller: Bank Of Toronto (hedging duration risk on its Fixed
          Income book — Module 5 §1's Duration preview, made
          concrete here)
  Buyer:  Counterparty on the CME order book (anonymous, novated
          to CME Clearing at execution — Section 07)

  Contract: USD 100,000 face value of a notional 6% coupon 10-Year
            Treasury Note (the "notional" note is a standardized
            fiction — Section 5 explains what ACTUALLY delivers)
  Price Quotation: in points and 32nds -- 112'16 means 112 + 16/32
                   = 112.500 (a bond-market convention, distinct
                   from Module 4 §4c's decimal index points)

  Trade: SELL 100 contracts @ 112'16 (112.500)
  Notional = 100 x 100,000 x 112.500/100 = USD 11,250,000

  NEXT BUSINESS DAY, price falls to 111'24 (111.750) — rates rose,
  bond prices fell (Module 1 §1's price-vs-rates mechanic):
    Variation Margin = (112.500 - 111.750)/100 x 100,000 x 100
                      = 0.0075 x 100,000 x 100
                      = USD 75,000
    -> Bank Of Toronto is SHORT, so a price FALL is a GAIN --
       credited to Bank Of Toronto's margin account, offsetting
       the loss on the actual bond portfolio the future is hedging
```

**IT/reporting பார்வை:** Bank Of Toronto, ஊகிக்க இல்லாம, தான் *ஏற்கனவே வெச்சிருக்கற* position-ஐ ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html)-ல இருந்து தன்னிடம் உள்ள bonds) hedge பண்ண futures-ஐ *விக்கறது*, ஒரு வங்கிக்குள்ள Interest Rate Future-ஓட மிகப் பொதுவான நிஜ உலக பயன்பாடு — futures-ஐ திசைசார்ந்த ஊக positions-ஆ மட்டும் model பண்ற reporting system, சரியா இந்த வகை hedge overlay-ஐ தவறா வகைப்படுத்தும்.

### 4d. Listed Option — recap

முழு worked example **[Module 4](../docs/1-capital-markets/module-04-equity.html) §4d**-ல: Maple Leaf Pension Fund 100 AAPL 15-Jan-2027 250 Call contracts-ஐ வாங்குது, premium USD 185,000, OCC வழியா clear ஆகுது.

**Takeaway:** நாலு உதாரணங்கள், நாலு வேறு underlyings (ஒரு index, ஒரு commodity, ஒரு bond future, ஒரு single stock), ஆனா அத்தனையும் முழுக்க ரெண்டே contract வகைகள் (Futures, Listed Options) — Section 3-ஓட "standardization ஒரு venue பண்பு, asset-class பண்பு இல்ல" கண்டுபிடிப்புக்கு மிகக் கூர்மையான விளக்கம்.

### கூடுதல் உதாரணம் — T-Note Futures hedge: bond இழப்பும் futures லாபமும் பக்கம் பக்கமா

Bank Of Toronto-க்கிட்ட USD 11.25 மில்லியன் மதிப்புள்ள Treasury bond portfolio இருக்குன்னு வெச்சுக்கோங்க; rates உயர்ந்தா அது இழக்கும். அதனால 100 ZN futures-ஐ short பண்ணுது.

| நாள் | நிகழ்வு | Bond portfolio P&L (USD) | ZN short futures P&L (USD) | Net |
|---|---|---|---|---|
| Day 1 | Rates உயர்வு, ZN 112.500 → 111.750 | ≈ -75,000 (தோராயம்) | +75,000 | ≈ 0 |
| Day 2 | Rates குறைவு, ZN 111.750 → 112.250 | ≈ +50,000 | -50,000 | ≈ 0 |

Hedge perfect இல்ல ([Module 22](../docs/1-capital-markets/module-22-hedging-deep-dive.html) basis risk, DV01 matching பத்தி விரிவா சொல்லும்), ஆனா நிகர விளைவு பெருசா இல்ல — அதுதான் hedge-ஓட நோக்கம்.

---

## 5. Trade Lifecycle — உண்மையிலயே புதுசு: Basket வழியா Physical Delivery

[Module 4](../docs/1-capital-markets/module-04-equity.html) (§5), [Module 7](../docs/1-capital-markets/module-07-commodities.html) (§5)-ல உள்ள Sections 5 ஏற்கனவே Futures lifecycle-ஐ முழுசா நடத்திக் காட்டிடுச்சு — உடனடி CCP novation, தினசரி variation margin, expiry-அல்லது-roll. இங்க உண்மையிலயே புதுசு, குறிப்பா ஒரு bond future-க்கு **expiry**-ல என்ன நடக்குதுங்கறது; இது இந்த roadmap-ல மற்ற ஒவ்வொரு Future உதாரணத்தை விடவும் வித்தியாசமா நடந்துக்குது.

```text
CME 10-YEAR T-NOTE FUTURE  —  WHAT HAPPENS AT EXPIRY (if not rolled/closed)
========================================================================

  E-mini S&P 500 Future (Module 4 §4c) at expiry:
    CASH-SETTLED against the index level -- no security moves

  WTI Crude Future (Module 7 §4b) at expiry:
    PHYSICALLY SETTLED into ONE specific grade/location of crude
    (if not rolled -- almost never allowed to reach this point)

  10-Year T-Note Future (this section) at expiry:
    PHYSICALLY SETTLED, but into ANY ELIGIBLE BOND from a defined
    DELIVERY BASKET (a range of Treasury Notes with maturities in
    a specified window) -- the SHORT (Bank Of Toronto, Section 4c)
    CHOOSES which eligible bond to deliver, using a published
    CONVERSION FACTOR to adjust for that bond's specific coupon
    and maturity versus the contract's notional 6% coupon

  -> The bond the short chooses to deliver is called the CHEAPEST
     TO DELIVER (CTD) -- the eligible bond that's most economical
     for the short to source and deliver, a genuinely unique
     mechanic that neither an index future (no delivery at all)
     nor a commodity future (one fixed grade) has an equivalent of.
```

**Takeaway:** "physically settled"ங்கறது இந்த module-ல ஒவ்வொரு Future-க்கும் ஒரே அர்த்தமில்ல — ஒரு index future எதையும் deliver பண்றதில்ல, ஒரு commodity future ஒரு குறிப்பிட்ட பொருளை deliver பண்ணுது, ஒரு bond future ஒரு வரையறுக்கப்பட்ட menu-ல இருந்து short-ஓட *தேர்வை* deliver பண்ணுது. "physical settlement" எப்பவும் "ஒரு முன்கூட்டியே தீர்மானிக்கப்பட்ட instrument நகருது"ன்னு கருதற reporting அல்லது settlement system, ஒரு bond future-ஓட delivery notice-ஐ தவறா கையாளும்.

---

## 6. Identifiers — Exchange Contract Codes, bilateral UTI இல்ல

Listed Derivatives தான் இந்த roadmap-ல, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ல இருந்து ஒவ்வொரு OTC module-லயும் தோன்றின **UPI/UTI ஜோடி பொருந்தவே பொருந்தாத** முதல் product குடும்பம் — ஏன்னா UPI/UTI regulatory reporting-க்கு *bilateral-ஆ பேசப்பட்ட* OTC derivative trades-ஐ identify பண்ணவே இருக்கு; ஒரு Listed Derivative முதல்லயே bilateral-ஆ பேசப்படலை.

| Identifier | Listed Derivatives-க்கு பொருந்துமா? | Example |
|---|---|---|
| ISIN / CUSIP / Ticker | **இல்ல** — securities இல்ல (Section 03) | — |
| **Exchange Contract Code** | ஆமா — முதன்மை identifier; product + expiry month + year-ஐ encode பண்ணுது | `ESZ26` (E-mini S&P 500, Dec 2026); `CLZ26` (WTI Crude, Dec 2026 — [Module 7](../docs/1-capital-markets/module-07-commodities.html) §6); `ZNZ26` (10Y T-Note, Dec 2026) |
| Contract Month Code | Exchange contract code-க்குள்ள பதிக்கப்பட்டது (Section 1-ஓட F/G/H/.../Z table) | மேல உள்ள மூணு codes-லயும் `Z` = December |
| **UPI / UTI** | **இல்ல** — இவை bilateral-ஆ பேசப்பட்ட OTC derivatives-ஐ identify பண்ணும் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12); ஒரு Listed Derivative-ஓட fungibility (Section 03) ஒவ்வொரு trade-க்கும் bilateral identifiers-ஐ அர்த்தமற்றதாக்குது — அந்தப் பாத்திரத்தை exchange-ஓட சொந்த trade/clearing IDs வகிக்குது | — |
| **LEI** | ஆமா — Bank Of Toronto, மற்ற எந்த counterparty-ஐயும் identify பண்ணும், முந்தைய ஒவ்வொரு module-லயும் அதே பாத்திரம் | Entity, instrument இல்ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §6) |

**Takeaway:** Exchange contract code இருந்து UPI/UTI இல்லாத Listed Derivative trade record சரியா சரியானது — [Module 6](../docs/1-capital-markets/module-06-fx.html) §6, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §6 OTC records-க்கு ஏற்கனவே நிறுவின diagnostic-ஓட தலைகீழ். இல்லன்னா வெளிப்படையா listed Futures/Options record போல இருக்கற ஒண்ணுல UPI/UTI நிரம்பியிருந்தா, அதுவே இங்க முரண்.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** இந்த roadmap-ல மற்ற ஒவ்வொரு module-உம் Section 7-ஐ ஒரு table முழுக்க பல பதில்களோட உண்மையான இரு-அச்சு கேள்வியா மாத்தியது. Listed Derivatives, அதுல உள்ள ஒவ்வொரு product-க்கும் அந்தக் கேள்விக்கு வரையறைப்படியே **ஒற்றை, நிபந்தனையற்ற பதில்** இருக்கற ஒரே node.

| Product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|
| Futures (எந்த underlying-உம்) | **எப்பவும் exchange-traded** | **எப்பவும் centrally cleared**, execution-ஓட நொடியிலேயே (Section 01) |
| Listed Options (எந்த underlying-உம்) | **எப்பவும் exchange-traded** | **எப்பவும் centrally cleared** (US equity options-க்கு OCC, futures options-க்கு CME Clearing) |

**Takeaway:** முந்தைய ஒவ்வொரு module-ஓட Section 7-உம், இந்த இரு-அச்சு grid-ல ஒரு குறிப்பிட்ட product எங்க உட்காருதுன்னு காட்ட இருந்தது, ஏன்னா பதில் product-க்கு product உண்மையிலயே வேறுபட்டது. இங்க, இரு-அச்சு grid ஒரே cell-ஆ ஒடுங்குது — இதுதான் இந்த module ஒரு தனி node-ஆ இருக்கறதுக்கான முழு காரணம்: "Listed Derivatives" என்பது அது எதை reference பண்ணுதுங்கறதால வரையறுக்கப்படலை, Section 2-ல இருந்து ஐந்து underlyings-ல எதன் மேல கட்டப்பட்டிருந்தாலும் *எப்பவும் exchange-traded, centrally-cleared cell-ல இறங்குறது*ங்கறதால வரையறுக்கப்படுது.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | Listed Derivatives-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Listed derivatives trading venues-க்கு நேரடியா pre/post-trade transparency-ஐ வகுக்குது; சில listed commodity derivatives மேல **position limits்-ஐயும்** ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §08) — future எந்த underlying-ஐ reference பண்ணினாலும் mechanism அதே என்பதால இங்க மறுபயன்படுத்தப்பட்டது |
| **EMIR** | Exchange-traded, centrally-cleared Futures/Listed Options-க்கு OTC derivatives-க்கு பொருந்தற மாதிரி ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) பொதுவா **பொருந்தாது** — reporting கடமை தொழில்நுட்ப ரீதியா இன்னும் இருக்கு, ஆனா EMIR-ஓட *கட்டாய clearing* பாதி ([Module 8](../docs/1-capital-markets/module-08-rates.html) §08, [Module 9](../docs/1-capital-markets/module-09-credit.html) §08-ல தலைப்பு உண்மை) இங்க அர்த்தமற்றது, ஏன்னா இந்த products centrally cleared தவிர வேறெதுவா இருக்கவே போகலை |
| **SFTR** | பொருந்தாது — securities financing எதுவும் சம்பந்தப்படலை |
| **FCA** (UK) | UK listed derivatives exchanges-ஐயும் அவற்றின் members-ஓட conduct-ஐயும் regulate பண்ணுது |
| **CIRO** (Canada) | Montreal Exchange (Canada-ஓட சொந்த listed derivatives exchange)-உடன் சேர்ந்து, Canadian listed derivatives dealer conduct-ஐ மேற்பார்வை பண்ற SRO |
| **HKMA / SFC** (Hong Kong) | HKEX-listed futures/options-க்கு குறிப்பா SFC ([Module 4](../docs/1-capital-markets/module-04-equity.html) §08-ல HKMA-வோட வேறுபாடு) பொருத்தமான அமைப்பு — HKMA தானே இங்க ஒரு படி தள்ளியே நிக்குது, FX ([Module 6](../docs/1-capital-markets/module-06-fx.html) §08), Fixed Income ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08)-ல அதன் நேரடிப் பாத்திரங்களை விட Equity-ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §08) அதன் பாத்திரம் போல |
| **CFTC / SEC** (US) | CFTC பெரும்பாலான listed futures-ஐ (commodity, rate, FX, பரந்த index) regulate பண்ணுது; SEC-யும் CFTC-யும் **கூட்டா** "Security Futures Products"-ஐ (ஒரு தனி stock அல்லது narrow-based index மேல futures) regulate பண்ணுது — [Module 9](../docs/1-capital-markets/module-09-credit.html) §08-ஓட Credit பிளவுக்கு பக்கத்துல ரெண்டாவது CFTC/SEC பிளவு, ஆனா வேறு காரணத்துக்காக: இங்க அது underlying ஒரு *single security*-ங்கறதைப் பத்தியது, swaps-vs-security-based-swaps வகைப்பாட்டைப் பத்தி இல்ல |
| **DTCC / OCC** | [Module 4](../docs/1-capital-markets/module-04-equity.html) §5-ல அறிமுகப்படுத்தப்பட்ட **OCC (Options Clearing Corporation)**, எல்லா US-listed equity options-க்கும் இந்த module-ஓட அர்ப்பணிப்பு CCP — சில product அமைப்புகள்ல equity options மட்டுமில்லாம, எந்த listed option-க்கும் அதன் முழு scope சேர்ந்த module-ஆ இங்க மறுபடி சொல்லத் தகுந்தது |
| **DMO** (UK Debt Management Office) | நேரடியா பொருத்தமில்ல — ஒரு UK Gilt future reference பண்ற cash Gilts-ஐ வெளியிடுது ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08), ஆனா DMO தானே listed derivative சந்தையைத் தொடறதில்ல |

**Takeaway:** இங்க regulatory கதை முந்தைய ஒவ்வொரு module-ஓடதுக்கும் கிட்டத்தட்ட தலைகீழ் — "இந்த regulation-ஓட mandate எந்த குறிப்பிட்ட products-க்குள்ள நீளுது"ன்னு கேட்காம, Section 8-ல இங்க பெரும்பாலான வரிகள் உண்மையில "இந்த regulation-ஓட OTC-மையம் கொண்ட இயந்திரம் சந்தையின் இந்த மூலைக்குள்ள நீள வேண்டிய அவசியம் இல்ல, ஏன்னா Section 7 ஏற்கனவே ஒரு mandate இல்லாமலே central clearing-ஐ உத்தரவாதப்படுத்துது"ன்னு சொல்லுது. CFTC/SEC Security Futures Product பிளவு ஒரே உண்மையான விதிவிலக்கு — ஒரு listed derivative-ஓட underlying ஒரு single security-ஆ இருக்கறதால regulatory கவனம் குறிப்பா அதிகரிக்கற இடம்.

---

## 9. Canadian Systems Landscape — Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §2 Canada-க்கான *பொதுவான* Front/Middle/Back Office architecture-ஐ ஏற்கனவே பெயரிட்டிருக்கு. இந்த section அதை Listed Derivatives-க்கு மட்டும் சுருக்குது — இந்த module-ஓட சொந்தக் கருப்பொருள் ஒவ்வொரு underlying-உம் (Section 2) ஒரே mechanic வழியா போகுதுங்கறதால, Canada-ஓட listed-derivatives stack வரைய அசாதாரணமா எளிது: [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §2 cash equities-க்கு பட்டியலிடும் பல போட்டி venues போல இல்லாம, Canada-ல exchange-listed derivatives-க்கு உண்மையிலயே **ஒரே ஒரு** venue-உம் **ஒரே ஒரு** CCP-யும்தான்.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | Order/Execution Management (Bloomberg EMSX, FlexTrade, or a bank's own DMA gateway — illustrative, same vendor family [systems-landscape](../docs/1-capital-markets/systems-landscape.md) §2 names) → **Montreal Exchange (MX)**, TMX Group's derivatives exchange, matched on its electronic trading engine, commonly referred to as **SOLA** | Order entry, execution, price discovery |
| **Middle Office** | Give-up/allocation processing (routes an executing broker's trade to the correct clearing member); risk/valuation engines (Murex/Calypso — illustrative) | Confirms the trade, marks intraday risk, routes it to the right clearing member |
| **Back Office / CCP** | **CDCC (Canadian Derivatives Clearing Corporation)**, also a TMX Group company | Novates every MX-listed trade the instant it executes (Section 1's Novation-at-Execution), collects Initial Margin, calculates daily Variation Margin (a SPAN-style methodology — illustrative) |
| **Cash Settlement** | **LVTS (Large Value Transfer System)**, Canada's high-value payment rail ([systems-landscape](../docs/1-capital-markets/systems-landscape.md) §8's cash-settlement-rail table — the Canadian counterpart to Fedwire/CHAPS) | Moves the actual variation-margin and settlement cash between Bank Of Toronto's settlement bank and CDCC |
| **Regulatory / Oversight** | **CIRO** (dealer conduct, trade/market surveillance); the **CSA** (recognizes MX as an exchange, CDCC as a clearing agency); the **Bank of Canada** (oversees CDCC as a systemically important Financial Market Infrastructure, alongside CDS/CDSX — [systems-landscape](../docs/1-capital-markets/systems-landscape.md) §10) | Conduct oversight and systemic-risk monitoring — **not** a Trade Repository submission (see takeaway) |

```text
CANADIAN LISTED DERIVATIVES — FRONT TO BACK, ONE TRADE
========================================================================

  FRONT OFFICE
    Bank Of Toronto's derivatives desk enters the order via an
    OMS/EMS (Bloomberg EMSX / FlexTrade / a bank DMA gateway --
    illustrative) -> routed to MONTREAL EXCHANGE (MX), matched on
    its electronic trading engine, SOLA
         |
         v
  MIDDLE OFFICE
    Give-up/allocation processing routes the executed trade to the
    correct clearing member; risk/valuation systems (Murex/Calypso
    -- illustrative) mark the position and compute intraday risk
         |
         v
  BACK OFFICE / CCP
    CDCC (Canadian Derivatives Clearing Corporation) NOVATES the
    trade the instant it executes (Section 1's Novation-at-
    Execution) -- becomes buyer to the seller and seller to the
    buyer, collects Initial Margin, and calculates daily Variation
    Margin (a SPAN-style methodology -- illustrative)
         |
         v
  CASH SETTLEMENT
    Variation margin cash moves between Bank Of Toronto's
    settlement bank and CDCC over LVTS, Canada's high-value
    payment rail (systems-landscape §8 -- the Canadian counterpart
    to Fedwire/CHAPS)
         |
         v
  REGULATORY / OVERSIGHT -- NO TRADE REPOSITORY IN THIS PATH
    CIRO oversees Bank Of Toronto's trading conduct on MX; the CSA
    recognizes MX as an exchange and CDCC as a clearing agency; the
    BANK OF CANADA oversees CDCC itself as a systemically important
    Financial Market Infrastructure (FMI), given how much of
    Canada's listed-derivatives risk concentrates through this one
    CCP
```

**Worked walkthrough — Bank Of Toronto, MX-ல S&P/TSX 60 Index Futures (SXF)-ஐ வாங்குது:**

```text
S&P/TSX 60 INDEX FUTURE (SXF)  —  MONTREAL EXCHANGE
========================================================================

  Buyer: Bank Of Toronto     Multiplier: CAD 200 / index point
  Trade: BUY 15 contracts @ 1,285.00 (index points)
  Notional = 15 x 200 x 1,285.00 = CAD 3,855,000

  Next business day, index rises to 1,291.50:
    Variation Margin = (1,291.50 - 1,285.00) x 200 x 15
                      = 6.50 x 200 x 15
                      = CAD 19,500
    -> credited to Bank Of Toronto (long position, index rose --
       the SAME mechanic as Module 4 §4c's E-mini S&P 500 example,
       just a different index, multiplier, and currency)

  SYSTEM PATH: OMS/EMS -> MX/SOLA (execution) -> clearing-member
  risk engine (middle office) -> CDCC (novation, margin) -> LVTS
  (cash) -> CIRO/CSA/Bank of Canada (oversight, no TR submission)
```

> **Callout — illustrative, பரிந்துரை இல்ல.** மேல உள்ள system/vendor பெயர்கள் (Bloomberg EMSX, FlexTrade, Murex, Calypso) Canadian வங்கி derivatives desks பொதுவா ஓட்டுறதன் பிரதிநிதிகள், Bank Of Toronto-ஓட உண்மையான internal technology stack பத்தின கூற்று இல்ல — [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §2 தன் சொந்த vendor பட்டியலை hedge பண்ற விதத்துக்கு ஒத்தது. MX, CDCC, CIRO, CSA, Bank of Canada, LVTS, இதற்கு மாறா, உண்மையான, குறிப்பிட்ட Canadian market infrastructure: MX-listed products-க்கு ஒரே ஒரு CCP, vendors தேர்வு இல்ல; மேல உள்ள front/middle-office layer போல இல்லாம.

**Takeaway:** Listed Derivatives-க்கு central clearing ஒருபோதும் விருப்பமில்லாததால EMIR-ஓட கட்டாய-clearing இயந்திரம் "அர்த்தமற்றது"ன்னு Section 8 ஏற்கனவே சுட்டிக்காட்டியது — அதே உண்மையின் Canadian-குறிப்பான விளைவை இந்த section காட்டுது: ஒவ்வொரு MX-listed trade-உம் execution-ல இருந்தே CDCC வழியா centrally cleared என்பதால, clear ஆகாத CAD IRS ([Module 8](../docs/1-capital-markets/module-08-rates.html)) அல்லது CDS ([Module 9](../docs/1-capital-markets/module-09-credit.html)) கோரும் மாதிரி CSA Multilateral Instrument 96-101 Trade Repository submission இல்ல. [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1-ஓட "இந்த trade ஒரு regulator-ஆல பார்க்கப்பட்டதா?" கேள்விக்கு Canadian சந்தையின் இந்த மூலையில பதில் சொல்லும் மேற்பார்வை layer — Trade Repository இல்ல, CIRO-ஓட சொந்த market/trade surveillance.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | 10-Year T-Note futures-ஓட seller, தன் Fixed Income book-ஓட duration-ஐ hedge பண்ணுது |
| Maple Leaf Pension Fund | E-mini S&P 500 futures ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4c), AAPL listed call ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4d)-ஓட buyer |
| CME / CME Clearing | E-mini, WTI, 10-Year T-Note futures-க்கான exchange, CCP |
| OCC (Options Clearing Corporation) | AAPL call உட்பட US-listed equity options-க்கான CCP |
| CFTC / SEC | Security Futures Products-ஓட (single stocks/narrow indices மேல futures) கூட்டு regulators |
| Montreal Exchange (MX) / CDCC | Canada-ஓட listed-derivatives exchange, அதன் அர்ப்பணிப்பு CCP — SXF trade-ஐ execute, clear பண்ணுது (Section 9) |
| CIRO / Bank of Canada | MX-ல Canadian dealer conduct-ஐ CIRO மேற்பார்வை பண்ணுது; Bank of Canada, CDCC-ஐ systemically important FMI-ஆ மேற்பார்வை பண்ணுது (Section 9) |

---

## Recap Quiz — Numbers Edition

1.  Section 4c-ஓட 10-Year T-Note future-ல, விலை அடுத்த business day-ல 113'08 (113.250)-க்கு *உயர்ந்திருந்தா*, Variation Margin தொகை என்ன? Bank Of Toronto லாபம் அடையுமா, இழக்குமா (நினைவில வெச்சுக்கோங்க: Bank Of Toronto SHORT)?
2.  ஒரு OTC FRA "அசல் அதே counterparty-க்கு எதிராக மட்டுமே unwind ஆகும்", ஆனா ஒரு Listed Future எந்த சந்தை பங்கேற்பாளருக்கு எதிராகவும் மூடப்படலாம்னு Section 3 ஏன் சொல்லுது?
3.  ஒரு bond future roll ஆகாம அல்லது மூடப்படாம expiry-ஐ அடையுது. Section 5-ஐ பயன்படுத்தி, buyer ஒரு குறிப்பிட்ட, முன்கூட்டியே தீர்மானிக்கப்பட்ட bond-ஐ பெறுமா? இல்லன்னா, எது deliver ஆகணும்னு யார் முடிவு செய்யறாங்க?
4.  Section 6-ஐ பயன்படுத்தி, ஒரு CME WTI Crude Future trade record-ல UTI field நிரம்பியிருக்கும்னு எதிர்பார்ப்பீங்களா? ஏன்/ஏன் இல்ல? அதே product-ஓட OTC இணையான Commodity Swap-ஓட ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §6) இது எப்படி மாறுபடுது?
5.  இந்த module-ஓட Exchange-vs-OTC table "ஒரே cell-ஆ ஒடுங்குது"ன்னு Section 7 ஏன் விவரிக்குது? மற்ற ஒவ்வொரு module-ஓட அதே table-ஓட பதிப்பிலிருந்து இது எப்படி வேறுபடுது?
6.  ஒரு Security Futures Product (Section 08) CFTC, SEC ரெண்டோட அதிகார எல்லைக்கும் குறிப்பா எதனால விழுது? அந்த காரணம் [Module 9](../docs/1-capital-markets/module-09-credit.html) §08 Credit-ஓட CFTC/SEC பிளவுக்கு கொடுத்த காரணத்திலிருந்து எப்படி வேறுபடுது?
7.  இந்த module-ஓட masthead, Listed Derivatives-ஐ, உதா., [Module 4](../docs/1-capital-markets/module-04-equity.html) (Equity) அல்லது [Module 7](../docs/1-capital-markets/module-07-commodities.html) (Commodities)-க்குள்ள மடிக்காம, ஆறு-asset-class roadmap-க்கு வெளியே உட்காருதுன்னு ஏன் விவரிக்குது?
8.  Section 9-ஐ பயன்படுத்தி, CAD-denominated, CDCC-cleared SXF future CSA Multilateral Instrument 96-101 Trade Repository report-ஐ உருவாக்காம, clear ஆகாத CAD IRS ([Module 8](../docs/1-capital-markets/module-08-rates.html)) உருவாக்குவது ஏன்? Trade Repository இல்லன்னா, SXF trade-ஓட regulatory மேற்பார்வையை இன்னும் எந்த அமைப்பு வழங்குது?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — இது Asset Class Roadmap (Modules 4-9), அதன் cross-cutting துணை ரெண்டையும் முடிக்குது. Modules 10-14 இது எல்லாத்தின் மேலயும் கட்டுது: Valuation, Risk & Exposure, Collateral & Margin, Parties/LEIs, Regulatory Reporting.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Variation Margin = USD 75,000, Bank Of Toronto இழக்குது (கொடுக்குது), Section 4c-ஓட worked example-ஓட mirror image.**

```text
Variation Margin = (113.250 - 112.500)/100 x 100,000 x 100
                  = 0.0075 x 100,000 x 100
                  = USD 75,000
```

Bank Of Toronto SHORT (future-ஐ விற்றது) என்பதால, விலை உயர்வு position-க்கு எதிரா நகருது — Bank Of Toronto இப்போ variation margin-ல USD 75,000 கடன்பட்டிருக்கு; short லாபம் அடைஞ்ச Section 4c-ஓட விலை-வீழ்ச்சி scenario-வுக்கு எதிர்மறை. இதுதான் hedge நோக்கப்படி வேலை செய்யறது: rates உயருறதுக்குப் பதிலா குறைஞ்சிருந்தா, Bank Of Toronto-ஓட உண்மையான bond portfolio மதிப்பு *லாபம்* அடைஞ்சிருக்கும், இந்த futures இழப்பை ஈடுகட்டி — முதல்ல future-ஐ விற்றதற்கான முழுக் காரணம் அதுதான்.

**2. ஏன்னா ஒரு OTC FRA-ஓட terms (Notional, FRA Rate, period) Bank Of Toronto-க்கும் Goldman Sachs-க்கும் இடையிலான அந்த ஒரு பேச்சுவார்த்தைக்கு bespoke ([Module 8](../docs/1-capital-markets/module-08-rates.html) §4b) — வேற எந்த சந்தை பங்கேற்பாளரோட FRA-வுக்கும் ஒரே மாதிரி terms உத்தரவாதம் இல்ல, அதனால அசல் counterparty-ஐத் தவிர net பண்ண fungible "எதிர் position" இல்ல. ஒரு Listed Future-ஓட terms அந்த contract-ல ஒவ்வொரு trade-க்கும் ஒரே மாதிரி (Section 3), அதனால எந்த buyer-ஓட long position-உம் exchange-ஓட books-ல எந்த seller-ஓட short position-க்கு எதிராகவும் net ஆகலாம்.**

Section 3-ஓட takeaway இதை standardization இருக்கறதற்கான மையக் காரணமா சொல்லுது: நீங்க முதல்ல trade பண்ணின counterparty-ல இருந்து முற்றிலும் வேறொரு counterparty-க்கு எதிரா மூடுவது அல்லது roll பண்ணுவது ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §4b) சாத்தியமாக்குவது fungibility.

**3. இல்ல — buyer ஒரு முன்கூட்டியே தீர்மானிக்கப்பட்ட bond-ஐ பெறாது. SELLER (short) delivery basket-ல இருந்து எந்த தகுதியுள்ள bond-ஐ deliver பண்றதுன்னு தேர்ந்தெடுக்குது, அந்த bond-ஓட குறிப்பிட்ட coupon/maturity-ஐ contract-ஓட notional 6% coupon-ஓட ஒப்பிட்டு சரிசெய்ய வெளியிடப்பட்ட conversion factor-ஐ பயன்படுத்தி — "Cheapest to Deliver" (CTD) bond.**

Section 5 இதை நேரடியா சொல்லுது, E-mini future (delivery இல்ல, cash-settled), WTI future (ஒரு நிலையான grade/location) ரெண்டோடும் ஒப்பிட்டு — delivery செய்யும் தரப்புக்கு எதை deliver பண்றதுன்னு உண்மையான தேர்வு இருக்கற மூணுல ஒரே ஒண்ணு bond future.

**4. இல்ல — CME WTI Crude Future trade record-ல UTI இருக்கும்னு எதிர்பார்க்க மாட்டீங்க, ஏன்னா Section 6 UPI/UTI bilateral-ஆ பேசப்பட்ட OTC derivatives-க்கு மட்டுமேன்னு நிறுவுது; ஒரு Listed Future ஒருபோதும் bilateral-ஆ பேசப்படலை. இது OTC Commodity Swap-ஓட ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §6) நேரடியா மாறுபடுது; அது பேசப்படுறதால UPI/UTI-ஐ சுமக்குது — அதே underlying commodity (crude oil), எந்த venue/product வகை வழியா trade ஆகுதுங்கறதை மட்டும் பொறுத்து, கட்டமைப்பு ரீதியா வேறுபட்ட ரெண்டு identifier pattern-களை உருவாக்குது.**

Section 6-ஓட takeaway இதை இந்த module-ஓட diagnostic சோதனையா ஆக்குது: Listed Derivative-க்கு UPI/UTI இல்லாத exchange contract code சரி; அதற்குப் பதிலா UPI/UTI நிரம்பியிருந்தா அதுவே முரண் — [Module 6](../docs/1-capital-markets/module-06-fx.html) §6, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §6 OTC products-க்கு நிறுவின சோதனைகளின் தலைகீழ்.

**5. ஏன்னா இந்த module-ல ஒவ்வொரு product-உம் வரையறைப்படியே ஒரே cell-ல (exchange-traded, centrally cleared) இறங்குது — Section 7-ஓட grid-ல ஒரே ஒரு cell மட்டும் நிரம்பியிருக்கு, மற்ற ஒவ்வொரு module-ஓட அதே table-க்கும் தேவைப்பட்ட பல cells-க்குப் பதிலா, ஏன்னா "Listed Derivatives" சரியா அந்தக் கேள்விக்கு ஒரு நிலையான பதில் இருக்கற products-ஆ வரையறுக்கப்பட்டிருக்கு, Equity, Fixed Income, FX, Commodities, Rates, Credit-ல அதே கேள்வி asset class-க்குள்ள குறிப்பிட்ட product-க்கு product உண்மையிலயே வேறுபட்டது.**

முந்தைய ஒவ்வொரு module-க்கும் Section 7 தேவைப்பட்டது, அந்த asset class-ஓட பல products-ல ஒவ்வொண்ணும் 2x2 (அல்லது பெரிய) grid-ல எங்க இறங்குதுன்னு காட்ட. இங்க வேறுபடுத்த எதுவும் இல்ல — சரியா இதுதான் Section 7-ஓட takeaway "Listed Derivatives" ஏன் asset-class-சுற்றி அமைந்த கருத்துக்குப் பதிலா தன் சொந்த module-ஐ சம்பாதிக்குதுன்னு சொல்றது.

**6. ஒரு Security Futures Product CFTC, SEC ரெண்டோட அதிகார எல்லைக்கும் குறிப்பா விழுது, ஏன்னா அதன் underlying ஒரு single security (அல்லது அதுக்கு நெருக்கமான narrow-based index) — நேரடி securities-law கொக்கி; [Module 9](../docs/1-capital-markets/module-09-credit.html) §08 single-name CDS-க்கு அடையாளம் காட்டிய அதே underlying-security trigger. Credit பிளவு ([Module 9](../docs/1-capital-markets/module-09-credit.html) §08), இருந்தும், Dodd-Frank-ஓட OTC derivatives கட்டமைப்புக்குள்ள swaps-vs-security-based-swaps வகைப்பாட்டைப் பத்தியது; இந்த module-ஓட பிளவு, ஒரு exchange-traded product மேல எந்த listed-derivatives regulator (futures/options-க்கு பொதுவா CFTC, security-தொடர்புடையவற்றுக்கு குறிப்பா SEC) அதிகார எல்லை கொண்டுள்ளதுங்கறதைப் பத்தியது — ஒரே மாதிரியான விளைவு (கூட்டு CFTC/SEC மேற்பார்வை), ரெண்டு வேறு சட்டக் கட்டமைப்புகள் வழியா (OTC swap வகைப்பாடு vs. listed product வகைப்பாடு).**

Section 8 இதை வெளிப்படையா "ரெண்டாவது CFTC/SEC பிளவு... வேறு காரணத்துக்காக"ன்னு குறிக்குது — ரெண்டு பிளவுகளும் அதே மூல காரணத்துக்குத் (ஒரு single security-ஓட ஈடுபாடு SEC ஆர்வத்தை தூண்டுறது) திரும்புது, ஆனா உண்மையிலயே வேறு product வகைகளுக்கு (OTC swaps vs. listed futures/options) பொருந்துது.

**7. ஏன்னா Section 3-ஓட கண்டுபிடிப்பு: standardization/central clearing ஒரு VENUE பண்பு, ASSET CLASS பண்பு இல்ல — ஒரு Future அல்லது Listed Option ஆறு asset classes-ஓட underlyings-ல எதையும் (Section 2-ஓட ஐந்து-உதாரண table) reference பண்ணலாம், அதனால அதை ஒரே ஒரு asset class module-ல (உதா., Equity) மடிக்கறது, வேறு underlying-ல கட்டப்பட்ட (ஒரு WTI future, ஒரு T-Note future) ஒவ்வொரு Future/Listed Option-ஐயும் எப்படியோ Equity-க்கு சொந்தம்னு தவறா சித்தரிக்கும்.**

Masthead-ஓட சட்டகமும் Section 1-3-ஓட வாதங்களும் இதுல ஒன்றிணையுது: work data model taxonomy-ஓட சொந்த அமைப்பு (Listed Derivatives taxonomy node "7"-ஆ, asset-class nodes "1" முதல் "6" வரையிலிருந்து தனியா) ஒரு தன்னிச்சையான schema-வடிவமைப்பு தேர்வு இல்ல, ஒரு உண்மையான ஒழுங்கமைக்கும் கொள்கையை பிரதிபலிக்குது — mechanic (Section 1, Section 3) எந்த asset class underlying-ஐ வழங்குதுங்கறதுக்கு உண்மையிலயே செங்குத்துங்கறதால இந்த module இருக்கு.

**8. ஏன்னா SXF future execution-ஓட நொடியிலேயே CDCC வழியா centrally cleared (Section 1-ஓட Novation-at-Execution, Section 9-ல Canada-க்கு மறுபடி சொல்லப்பட்டது) — MI 96-101-ஓட Trade Repository கடமை இல்லன்னா மறைவாவே இருக்கற OTC derivative risk-க்கு regulators-க்கு பார்வை தரவே இருக்கு; ஒரு CDCC-cleared listed future அந்த அர்த்தத்துல ஒருபோதும் மறைவானது இல்ல: ஒவ்வொரு position-ஓட முழுமையான, மையப்படுத்தப்பட்ட record-ஐ CDCC தானே ஏற்கனவே வெச்சிருக்கு. Clear ஆகாத CAD IRS-க்கு அப்படி ஒரு மைய record இல்ல, MI 96-101 மூட இருக்கற இடைவெளி சரியா அதுதான். SXF trade-க்கு Trade Repository-க்குப் பதிலா, CIRO-ஓட சொந்த market/trade surveillance செயல்பாடு regulatory மேற்பார்வையை வழங்குது.**

Section 9-ஓட takeaway இதை நேரடியா சொல்லுது, EMIR-ஓட கட்டாய-clearing இயந்திரம் இங்க "அர்த்தமற்றது"ங்கற Section 8-ஓட கவனிப்புக்குத் திரும்பி — Canada-ஓட சொந்த OTC trade-reporting regime-க்கும் அதே தர்க்கம் பொருந்துது: ஒரு தேசிய CCP (CDCC) வழியா central clearing ஒவ்வொரு MX-listed product-க்கும் ஏற்கனவே மூடிவிடற அதே பார்வை இடைவெளியைத்தான் அது இலக்கு வைக்குது.
