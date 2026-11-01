# Hedging Deep Dive --- தமிழில் முழுமையான விளக்கம், கணக்குகளுடன்

> **யாருக்காக?** Finance / Capital Markets-ஐ புதிதாகக் கற்கும் Software
> Architect அல்லது Java Developer.\
> **முக்கிய குறிப்பு:** கீழுள்ள Bank of Toronto, Northern Rail Corp, Maple
> Leaf Pension Fund போன்ற பெயர்கள் கற்பனை உதாரணங்கள். இவை கற்றலுக்காக மட்டுமே.\
> **ஆதாரக் குறிப்பு:** இந்த ஆவணம் நீங்கள் வழங்கிய *Hedging Deep Dive* குறிப்பை
> அடிப்படையாகக் கொண்டு தமிழில் விரிவாக்கப்பட்டுள்ளது. அசல் குறிப்பில் உள்ள கருத்துகள்,
> உதாரணங்கள், எண்ணிக்கைகள் மற்றும் quiz தலைப்புகள் பாதுகாக்கப்பட்டுள்ளன. கூடுதல்
> விளக்கங்கள் கற்றலை எளிதாக்கும் நோக்கில் சேர்க்கப்பட்டுள்ளன.

------------------------------------------------------------------------

## 1. Hedging என்றால் என்ன?

**Hedging (ஹெட்ஜிங்)** என்பது ஏற்கெனவே நம்மிடம் உள்ள நிதி ஆபத்தை குறைக்க, அதற்கு
எதிர்திசையில் அல்லது அதனுடன் தொடர்புடைய இன்னொரு position-ஐ எடுப்பது. ஒரு
position-ல் நஷ்டம் ஏற்பட்டால், hedge position-ல் லாபம் கிடைத்து அந்த நஷ்டத்தை ஓரளவு
ஈடுகட்ட வேண்டும் என்பதே நோக்கம்.

Hedging-ன் நோக்கம் பொதுவாக **அதிக லாபம் சம்பாதிப்பது அல்ல; எதிர்பாராத இழப்பின்
அளவையும் முடிவுகளின் ஏற்றத்தாழ்வையும் குறைப்பது**.

### எளிய உதாரணம்

ஒரு இந்திய நிறுவனம் மூன்று மாதங்களில் USD 1,00,000 செலுத்த வேண்டும் எனக்
கொள்ளுங்கள். இப்போது USD/INR ₹83.00. டாலர் விலை ₹86 ஆக உயர்ந்தால், நிறுவனத்தின்
ரூபாய் செலவு அதிகரிக்கும்.

-   Hedge இல்லாமல்: ₹83 × 1,00,000 = ₹83,00,000.
-   பின்னர் விகிதம் ₹86 என்றால்: ₹86,00,000 செலவாகும்.
-   கூடுதல் செலவு: ₹3,00,000.

நிறுவனம் இன்று ஒரு FX Forward மூலம் எதிர்கால USD வாங்கும் விலையை நிர்ணயித்தால்,
டாலர் விலை உயர்வால் ஏற்படும் இந்த ஆபத்தை குறைக்கலாம். அதற்குப் பதிலாக, டாலர் விலை
குறைந்தால் கிடைத்திருக்கும் சலுகையையும் நிறுவனம் இழக்கலாம்.

### Hedging, Speculation, Arbitrage --- வேறுபாடு

  ------------------------------------------------------------------------
  வகை               ஏற்கெனவே offset    நோக்கம்              உதாரணம்
                    செய்ய வேண்டிய                          
                    exposure உள்ளதா?                      
  ----------------- ----------------- ------------------ -----------------
  **Hedging**       ஆம்                ஏற்கெனவே உள்ள ஆபத்தை  ஏற்கெனவே
                                      குறைத்தல்            வைத்திருக்கும்
                                                         bond-களுக்கு
                                                         எதிராக bond
                                                         futures விற்பது

  **Speculation**   பொதுவாக இல்லை      சந்தை நகர்வை கணித்து  எந்த existing
                                      புதிய ஆபத்தை        short position-உம்
                                      எடுத்துக்கொண்டு லாபம்  இல்லாமல் E-mini
                                      தேடுதல்             futures வாங்குதல்

  **Arbitrage**     கட்டாயமில்லை        தொடர்புடைய          ஒரே asset-ன்
                                      சந்தைகள்/கருவிகளில்   பொருந்தக்கூடிய
                                      விலை முரண்பாட்டைப்    விலைகளில் தற்காலிக
                                      பயன்படுத்தி மிகக்     வேறுபாட்டைப்
                                      குறைந்த ஆபத்துடன்     பயன்படுத்துதல்
                                      லாபம் பெற முயற்சித்தல் 
  ------------------------------------------------------------------------

**முக்கியமான கருத்து:** ஒரு trade-ன் instrument type மட்டும் பார்த்து அது
hedge-ஆ speculation-ஆ என்று தீர்மானிக்க முடியாது. Trader-க்கு ஏற்கெனவே என்ன
exposure இருக்கிறது என்பதைத் தெரிந்திருக்க வேண்டும்.

### ஒரே Futures, இரண்டு வேறு நோக்கங்கள்

-   **Bank of Toronto:** ஏற்கெனவே bond-களை வைத்திருக்கிறது. வட்டி விகிதம்
    உயர்ந்தால் bond விலை குறையலாம். அதனால் T-Note futures-ஐ short செய்கிறது.
    Futures-ல் கிடைக்கும் லாபம் bond-ல் ஏற்படும் இழப்பை ஈடுகட்ட வடிவமைக்கப்படுகிறது
    --- இது hedge.
-   **Maple Leaf Pension Fund:** offset செய்ய வேண்டிய existing exposure
    இல்லாமல் E-mini S&P 500 futures வாங்குகிறது. இது புதிய market exposure;
    ஆகவே speculation அல்லது deliberate exposure-taking.

### Banking software-க்கு இது ஏன் முக்கியம்?

ஒரு trade record-ல் `instrumentType = FUTURE` என்று இருப்பதால் அதை hedge என
system தானாக வகைப்படுத்தக் கூடாது. Hedge என்பது ஒரு தனி trade-ன் பண்பு
மட்டுமல்ல; **hedge trade மற்றும் அது offset செய்யும் exposure இடையேயான உறவு**.

ஒரு risk/booking system-ல் பின்வரும் விவரங்கள் தேவைப்படலாம்:

-   Trade ID
-   Instrument / product type
-   Position direction (Long / Short)
-   Hedged item அல்லது exposure ID
-   Hedge designation மற்றும் அதன் effective date
-   Risk factor (interest rate, FX, credit, commodity, equity)
-   Hedge ratio மற்றும் effectiveness data

------------------------------------------------------------------------

## 2. Hedge எப்படிச் செயல்படுகிறது?

ஒவ்வொரு hedge-லும் பொதுவாக இரண்டு பக்கங்கள் இருக்கும்:

1.  **Underlying exposure:** நமக்கு ஏற்கெனவே உள்ள ஆபத்து.
2.  **Hedge position:** அந்த ஆபத்துக்கு எதிராக எடுக்கப்படும் புதிய position.

இரண்டின் P&L (Profit and Loss) சந்தை நகர்வுக்கு எதிர்திசையில் நகரும் வகையில்
அமைக்கப்படுகிறது.

### எளிய bond உதாரணம்

வட்டி விகிதம் உயர்ந்தால் fixed-rate bond-ன் சந்தை விலை பொதுவாகக் குறையும்.

-   Bond position: வட்டி விகிதம் உயர்ந்தால் நஷ்டம்.
-   Short bond futures: futures விலை குறைந்தால் short position-க்கு லாபம்.
-   இரண்டையும் சேர்த்தால்: bond-ன் நஷ்டத்தை futures லாபம் ஓரளவு ஈடுகட்டலாம்.

### Hedge ஆபத்தை முற்றிலும் நீக்குமா?

இல்லை. Hedge செய்தால் பொதுவாக ஆபத்து குறையும்; **ஆபத்து பூஜ்யமாகாது**.
மீதமிருக்கும் ஆபத்துகளுக்கு உதாரணங்கள்:

-   **Basis risk:** hedge instrument மற்றும் actual exposure ஒரே மாதிரி
    நகராதது.
-   **Hedge-ratio risk:** hedge அளவு அதிகமாகவோ குறைவாகவோ இருப்பது.
-   **Counterparty risk:** OTC hedge-ன் மறுபக்கம் ஒப்பந்தத்தை நிறைவேற்றத்
    தவறுவது.
-   **Wrong-way risk:** hedge counterparty நமக்கு மிகவும் தேவைப்படும்
    நேரத்திலேயே தோல்வியடையும் வாய்ப்பு அதிகரிப்பது.
-   **Liquidity risk:** தேவையான நேரத்தில் hedge-ஐ நியாயமான விலையில் மூட
    முடியாமை.
-   **Timing / maturity mismatch:** exposure மற்றும் hedge கால அளவு
    பொருந்தாமை.

எனவே, "position hedged" என்பதன் பொருள் "risk-free" அல்ல; "outcome range
குறைக்கப்பட்டுள்ளது" என்பதே சரியான விளக்கம்.

------------------------------------------------------------------------

## 3. Symmetric vs. Asymmetric Hedge

Hedge தேர்வில் மிக முக்கியமான முடிவு: **Forward / Futures / Swap போன்ற
symmetric instrument வேண்டுமா, அல்லது Option போன்ற asymmetric instrument
வேண்டுமா?**

### 3.1 Symmetric hedge

பொதுவாக Forwards, Futures, Swaps இவ்வகையில் வரும்.

-   பொதுவாக option premium போன்ற upfront premium இல்லை.
-   Futures-க்கு margin தேவைப்படலாம்; margin என்பது option premium அல்ல.
-   சந்தை நகர்வின் இரு திசைகளிலும் ஒப்பந்தத்தின் மதிப்பு மாறும்.
-   எதிர்பாராத மோசமான நகர்வை குறைக்கிறது; ஆனால் நல்ல சந்தை நகர்வின் நன்மையையும்
    விட்டுக்கொடுக்க நேரிடலாம்.
-   Hedge instrument-ல் தனியாகப் பெரிய mark-to-market loss ஏற்படவும்
    முடியும்.

**உதாரணம்:** எதிர்காலத்தில் USD வாங்க வேண்டிய நிறுவனம் FX Forward மூலம் வாங்கும்
விலையை lock செய்கிறது. USD விலை உயர்ந்தாலும் குறைந்தாலும் ஒப்பந்த விலையே
பொருந்தும்.

### 3.2 Asymmetric hedge

பொதுவாக Options இவ்வகையில் வரும்.

-   Option வாங்குபவர் upfront premium செலுத்துகிறார்.
-   சந்தை மோசமாக நகர்ந்தால் option-ஐ பயன்படுத்தலாம்.
-   சந்தை சாதகமாக நகர்ந்தால் option-ஐ பயன்படுத்தாமல் விடலாம்.
-   வாங்கிய option-ன் இழப்பு பொதுவாக செலுத்திய premium-க்கு வரையறுக்கப்படும்;
    ஆனால் premium-ஐ முழுவதும் இழக்கலாம்.
-   சந்தை சாதகமாக நகரும்போது அந்த நன்மையைப் பாதுகாக்கும் வாய்ப்பு கிடைக்கும்.

**உதாரணம்:** USD வாங்க வேண்டிய நிறுவனம் USD Call / CAD Put option
வாங்குகிறது. USD விலை உயர்ந்தால் strike price-ல் வாங்கும் உரிமையைப் பயன்படுத்தலாம்.
USD விலை குறைந்தால் சந்தையில் குறைந்த விலையில் வாங்கி option-ஐ expire ஆக
விடலாம்.

### ஒப்பீடு

  -----------------------------------------------------------------------
  அம்சம்                    Symmetric: Forward /    Asymmetric: Option
                          Futures / Swap          
  ----------------------- ----------------------- -----------------------
  ஆரம்பச் செலவு             பொதுவாக premium இல்லை;   Premium செலுத்த வேண்டும்
                          margin / collateral     
                          இருக்கலாம்                

  Payoff                  இரு திசைகளிலும் ஒப்பந்த    பாதுகாப்பு தேவைப்படும்
                          மதிப்பு மாறும்            திசையில் உரிமை தரும்

  சாதகமான சந்தை நகர்வு      நன்மை பெரும்பாலும் lock    premium-ஐ கழித்தும்
                          செய்யப்பட்ட விலையால்        நன்மையைப் பெறலாம்
                          கட்டுப்படும்               

  முக்கிய trade-off        premium இல்லாமை / விலை   premium செலவு; ஆனால்
                          உறுதி; ஆனால் upside      upside-ஐப் பாதுகாக்கலாம்
                          விட்டுக்கொடுக்கப்படலாம்      

  ஒப்புமை                  Fixed-price agreement   Insurance policy
  -----------------------------------------------------------------------

**கவனம்:** "Option-க்கு loss premium-க்கு மட்டுமே" என்பது option வாங்குபவரின்
position-க்கு பொருந்தும். Option எழுதுபவர் / விற்பவர் வேறு ஆபத்துகளை ஏற்கிறார்.

------------------------------------------------------------------------

## 4. Interest Rate Hedging --- Duration மற்றும் DV01

### 4.1 அடிப்படை

Fixed-rate bond-ன் விலை மற்றும் market interest rate பொதுவாக எதிர்திசையில்
நகரும்:

-   Interest rate உயர்ந்தால் bond price குறையும்.
-   Interest rate குறைந்தால் bond price உயரும்.

Bond-ன் வட்டி விகித உணர்திறனை அளவிட **Duration** மற்றும் **DV01**
பயன்படுத்தப்படுகின்றன.

**DV01 (Dollar Value of 1 Basis Point)** என்பது yield ஒரு basis point (1
bp = 0.01 percentage point) மாறும்போது bond அல்லது portfolio மதிப்பில்
ஏற்படும் சுமார் பண மாற்றம்.

### 4.2 வழங்கப்பட்ட குறிப்பின் worked example

Bank of Toronto-வின் Canada 5% Bond 2035 position:

-   Market Value = CAD 9,850,000
-   Modified Duration = 7.2
-   DV01 = CAD 7,092 per basis point

இதன் பொருள்: வட்டி விகிதம் 1 bp உயர்ந்தால் bond position-ன் மதிப்பு சுமார் CAD
7,092 குறையும் என்று மதிப்பிடப்படுகிறது. வட்டி விகிதம் குறைந்தால் எதிர்திசையில்
சுமார் அதே அளவிலான மாற்றம் ஏற்படலாம்; இது ஒரு approximation.

Bank of Toronto, 100 CME 10-Year T-Note futures-ஐ `112'16` விலையில் short
செய்கிறது. அடுத்த business day-ல் futures price `111'24` ஆகக் குறைந்தால் short
futures position-க்கு லாபம் கிடைக்கும். அசல் குறிப்பில் variation margin gain
USD 75,000 எனக் கொடுக்கப்பட்டுள்ளது. இது underlying bond book-ல் ஏற்பட்ட இழப்பை
ஈடுகட்ட உதவும்.

**நாணயக் குறிப்பு:** Bond exposure CAD-ல் குறிப்பிடப்பட்டுள்ளது; futures gain
USD-ல் குறிப்பிடப்பட்டுள்ளது. உண்மையான portfolio-வில் இந்த இரண்டு நாணயங்களின்
தொகைகளை நேரடியாகச் சேர்க்க முடியாது. Currency conversion மற்றும் FX
exposure-ஐ கணக்கில் கொள்ள வேண்டும். குறிப்பில் கொடுக்கப்பட்ட futures P&L-ஐ இங்கு
source example ஆகவே வைத்துள்ளோம்.

### 4.3 Hedge ratio எப்படிக் கணக்கிடுவது?

Face value / notional ஒன்றாக இருப்பதை மட்டும் பார்த்து hedge அளவைத் தீர்மானிக்கக்
கூடாது. Bond மற்றும் futures-ன் விலை உணர்திறன் ஒன்றாக இருக்காது.

**கருத்தியல் சூத்திரம்:**

``` text
தேவையான Futures Contracts
≈ Bond Exposure-ன் DV01
  ÷ ஒரு Futures Contract-ன் DV01
```

உதாரணமாக, bond portfolio DV01 = CAD 7,092/bp. ஒரு futures contract-ன்
DV01-ஐ அறிந்த பிறகு, இரண்டையும் ஒரே currency மற்றும் பொருத்தமான risk
convention-ல் ஒப்பிட்டு contracts எண்ணிக்கையை மதிப்பிடலாம்.

கற்பனை கணக்கு: ஒரு futures contract-ன் DV01 CAD 70.92/bp என்று வைத்தால்,

``` text
தேவையான contracts ≈ 7,092 ÷ 70.92 = 100
```

இது விளக்கத்திற்கான கற்பனை மதிப்பு; உண்மையான CME contract DV01-ஐ சந்தைத்
தரவிலிருந்து பெற வேண்டும்.

### 4.4 ஏன் notional matching தவறு?

Bond notional CAD 10 million என்றும் futures notional-ன் முக மதிப்பு CAD 10
million என்றும் வைத்தால், இரண்டும் சமமான interest-rate risk கொண்டிருக்கும் என்று
உறுதி இல்லை. Coupon, maturity, duration, yield curve sensitivity,
contract conversion factor போன்றவை தாக்கம் செலுத்தும்.

**சுருக்கம்:** Interest-rate hedge sizing-ல் face value-ஐ விட DV01 / risk
sensitivity-ஐ match செய்வதே முக்கியம்.

------------------------------------------------------------------------

## 5. Credit Hedging --- Bond exposure-க்கு எதிராக CDS

### 5.1 CDS என்றால் என்ன?

**Credit Default Swap (CDS)** என்பது ஒரு reference entity-யின் credit
event / default ஆபத்துக்கு எதிராக protection வாங்கும் derivative ஒப்பந்தம்.
Protection buyer, premium செலுத்துவார்; ஒப்பந்தத்தின் விதிகளின்படி credit event
ஏற்பட்டால் protection seller settlement செய்ய வேண்டும்.

### 5.2 வழங்கப்பட்ட உதாரணம்

Bank of Toronto, Northern Rail Corp-ன் bonds வைத்திருக்கிறது அல்லது அந்த
நிறுவனத்துக்கு கடன் கொடுத்திருக்கிறது எனக் கொள்வோம். Northern Rail default
செய்தால் bond / loan மதிப்பு குறையலாம்.

Bank of Toronto, Goldman Sachs-இடமிருந்து CDS protection வாங்குகிறது:

-   Notional = CAD 20,000,000
-   CDS spread = 120 bps per year
-   Maturity = 5 years
-   Premium quarterly-ஆக செலுத்தப்படுகிறது
-   Source example-ல் quarterly premium = CAD 59,836

120 bps என்பது ஆண்டுக்கு 1.20% என்ற spread-ஐக் குறிக்கும். எளிய notional கணக்கில்
CAD 20,000,000 × 1.20% = CAD 240,000 வருடத்திற்கு. ஆனால் உண்மையான CDS
premium payment day-count, payment schedule, accrual, notional
convention மற்றும் பிற ஒப்பந்த விவரங்களைப் பொறுத்தது. அசல் குறிப்பின் CAD 59,836
quarterly amount-ஐ அதன் worked example எண்ணிக்கையாகவே எடுத்துக்கொள்ள வேண்டும்.

### 5.3 Default ஏற்பட்டால்

Source example-ல் recovery rate 40% எனக் கொள்ளப்படுகிறது.

``` text
Loss Given Default (LGD)
= Notional × (1 − Recovery Rate)
= CAD 20,000,000 × (1 − 0.40)
= CAD 12,000,000
```

அதாவது recovery-க்குப் பிறகு இழப்பு சுமார் CAD 12 million எனக்
கணிக்கப்படுகிறது. CDS settlement விதிகளின்படி protection seller பணம்
செலுத்தும்; இதனால் bond/loan exposure-ன் credit loss-ஐ ஓரளவு ஈடுகட்டலாம்.

### 5.4 இது ஏன் "near-perfect" hedge?

CDS reference entity Northern Rail Corp-ஐயே குறிப்பிடுகிறது; hedge-ன்
credit reference மற்றும் underlying bond issuer ஒரே entity. இது WTI
crude-ஐப் பயன்படுத்தி jet fuel விலையை hedge செய்வதை விட நேரடியான match.

ஆனால் இது முழுமையான ஆபத்தற்ற hedge அல்ல:

-   CDS notional உண்மையான exposure-க்கு சரியாக பொருந்தாமல் இருக்கலாம்.
-   Bond மற்றும் CDS-ன் maturity வேறுபடலாம்.
-   Bond recovery மற்றும் CDS settlement amount மாறுபடலாம்.
-   Protection seller Goldman Sachs தானே நிதி நெருக்கடியில் சிக்கலாம்.
-   **Wrong-way risk:** Northern Rail default ஆகும் அதே நேரத்தில்
    protection seller-ன் நம்பகத்தன்மையும் குறைந்தால் hedge பலன் கிடைக்காமல்
    போகலாம்.

**சுருக்கம்:** CDS credit risk-ஐ மாற்றுகிறது; credit risk-ஐ மாயமாக
அழிப்பதில்லை.

------------------------------------------------------------------------

## 6. FX Hedging --- Forward மற்றும் Option ஒப்பீடு

### 6.1 தேவையை வரையறுத்தல்

Bank of Toronto-வின் client மூன்று மாதங்களில் USD 10 million வாங்க வேண்டும்.
CAD-ல் செலவு செய்யும் client-க்கு USD விலை உயர்வது ஆபத்து.

Source example-ல் forward விலை:

-   3 மாதங்களில் USD 10,000,000 பெற
-   CAD 13,545,000 செலுத்த
-   இந்த விலை இன்றே lock செய்யப்படுகிறது

இதன் implied forward rate:

``` text
CAD 13,545,000 ÷ USD 10,000,000 = 1.3545 CAD per USD
```

### 6.2 Option A --- FX Forward (Symmetric)

Forward மூலம் மூன்று மாதங்களில் USD 10 million-ஐ CAD 13.545 million கொடுத்து
வாங்க ஒப்புக்கொள்கிறார்.

**Scenario 1: Spot = 1.3700 CAD/USD**

Spot சந்தையில் வாங்கினால் செலவு:

``` text
USD 10,000,000 × 1.3700 = CAD 13,700,000
```

Forward செலவு CAD 13,545,000. எனவே ஒப்பிடுகையில் சேமிப்பு:

``` text
CAD 13,700,000 − CAD 13,545,000 = CAD 155,000
```

**Scenario 2: Spot = 1.3400 CAD/USD**

Spot சந்தையில் வாங்கினால் செலவு:

``` text
USD 10,000,000 × 1.3400 = CAD 13,400,000
```

ஆனால் forward-ன் கீழ் CAD 13,545,000 செலுத்த வேண்டும். Spot-ஐ ஒப்பிடுகையில் CAD
145,000 அதிகம் செலவாகிறது.

  -----------------------------------------------------------------------
  Expiry spot         Forward செலவு      Spot-ல் வாங்கிய      ஒப்பீட்டு விளைவு
                                                 செலவு 
  -------------- ------------------ ------------------ ------------------
  1.3700             CAD 13,545,000     CAD 13,700,000 Forward-ன் நன்மை CAD
                                                                  155,000

  1.3400             CAD 13,545,000     CAD 13,400,000  Forward-ன் வாய்ப்புச்
                                                        செலவு CAD 145,000
  -----------------------------------------------------------------------

Forward விலை உறுதியை வழங்குகிறது. ஆனால் USD விலை குறைந்தால் அந்தச் சாதகமான
சந்தை விலையைப் பயன்படுத்த முடியாது.

### 6.3 Option B --- FX Option (Asymmetric)

Source example-ல் USD Call / CAD Put option:

-   Strike = 1.3600 CAD/USD
-   Notional = USD 5,000,000
-   Premium = 0.0180 CAD per USD
-   Premium = 5,000,000 × 0.0180 = CAD 90,000

**Scenario 1: Spot = 1.3800**

USD சந்தையில் விலை உயர்ந்துள்ளது. Option-ன் strike 1.3600-ல் USD வாங்கும்
உரிமையைப் பயன்படுத்தலாம்.

``` text
Spot-ஐ விட கிடைக்கும் மொத்த நன்மை
= (1.3800 − 1.3600) × USD 5,000,000
= CAD 100,000

Premium கழித்த நிகர நன்மை
= CAD 100,000 − CAD 90,000
= CAD 10,000
```

**Scenario 2: Spot = 1.3400**

சந்தையில் USD குறைந்த விலையில் கிடைக்கிறது. Option-ஐ பயன்படுத்தாமல் spot-ல்
வாங்கலாம். Option premium CAD 90,000 செலவாகியிருக்கும்; அது திரும்ப
கிடைக்காது.

  -----------------------------------------------------------------------
  அம்சம்                    Forward                 Option
  ----------------------- ----------------------- -----------------------
  Upfront premium         Source example-ல் இல்லை   CAD 90,000

  USD விலை உயர்ந்தால்        Lock செய்த விலை          Strike விலை பயன்படுத்தலாம்
                          பாதுகாக்கும்              

  USD விலை குறைந்தால்       குறைந்த spot விலையின்     Option expire ஆக
                          நன்மை கிடையாது           விடலாம்; spot-ல் வாங்கலாம்

  Trade-off               விலை உறுதி, upside      Premium செலவு, ஆனால்
                          வாய்ப்பு                  favourable move-ஐப்
                          விட்டுக்கொடுக்கப்படும்       பயன்படுத்தலாம்
  -----------------------------------------------------------------------

**கவனிக்க:** இந்த உதாரணத்தில் Forward notional USD 10 million; Option
notional USD 5 million. ஆகவே இரண்டின் மொத்தப் பண விளைவுகளை நேரடியாக ஒரே
அளவிலான hedge என ஒப்பிட வேண்டாம். Option premium மற்றும் payoff கணக்கு source
example-ன் USD 5 million notional-க்கு மட்டுமே.

------------------------------------------------------------------------

## 7. Commodity Hedging மற்றும் Basis Risk

### 7.1 ஏன் proxy hedge?

ஒரு airline-ன் முக்கிய operating cost-ல் jet fuel உள்ளது. Jet fuel விலை
உயர்ந்தால் airline செலவு அதிகரிக்கும். Jet fuel-ஐ நேரடியாக hedge செய்ய போதுமான
liquidity அல்லது பொருத்தமான exchange-listed derivative கிடைக்காமல் இருக்கலாம்.
அப்போது jet fuel-க்கு தொடர்புடைய WTI crude oil derivative-ஐ proxy-ஆகப்
பயன்படுத்தலாம்.

Source example:

-   Hedge instrument: WTI Crude Oil Call option
-   Strike = USD 80.00 per barrel
-   Notional = 100,000 barrels
-   Premium = USD 3.20 per barrel
-   Total premium = 100,000 × USD 3.20 = USD 320,000

WTI crude oil jet fuel உற்பத்திக்கு முக்கிய input என்பதால் விலைகள் தொடர்புடையவை.
ஆனால் WTI மற்றும் jet fuel விலை ஒரே அளவில் நகர்வது உறுதி இல்லை.

### 7.2 Basis risk என்றால் என்ன?

**Basis risk** என்பது hedge instrument-ன் விலை மாற்றமும் நாம் உண்மையில் hedge
செய்ய நினைக்கும் exposure-ன் விலை மாற்றமும் முழுமையாகப் பொருந்தாததால் மீதமிருக்கும்
ஆபத்து.

Source example-ல்:

1.  WTI +10%, jet fuel +10%: hedge செலவு உயர்வை நன்றாக offset செய்யக்கூடும்.
2.  WTI +10%, jet fuel +4%: hedge, உண்மையான fuel-cost மாற்றத்தை விட
    அதிகமாகச் செயல்படலாம்.
3.  Jet fuel, WTI-ஐ விட அதிகமாக உயர்ந்தால்: hedge போதாமல் போகலாம். இதனை crack
    spread widening-உடன் தொடர்புபடுத்தலாம்.

### 7.3 CDS மற்றும் WTI hedge-ஐ ஒப்பிடுக

-   CDS hedge: underlying bond issuer மற்றும் CDS reference entity ஒரே
    entity --- reference match வலுவானது.
-   WTI proxy hedge: underlying exposure jet fuel; hedge instrument WTI
    crude oil --- இரண்டும் தொடர்புடையவை, ஆனால் ஒன்றல்ல.

எனவே basis risk என்பது hedge தோல்வியடைந்துவிட்டது என்பதல்ல. கிடைக்கக்கூடிய
liquid instrument மூலம் hedge செய்யும்போது ஏற்றுக்கொள்ளப்படும் மீதமுள்ள ஆபத்தாக
இருக்கலாம்.

------------------------------------------------------------------------

## 8. Equity Hedging --- Protective Put

### 8.1 நிலைமை

Maple Leaf Pension Fund, KO பங்குகளை ஏற்கெனவே வைத்திருக்கிறது:

-   Shares = 10,000
-   வாங்கிய விலை = USD 68.40 per share
-   Earnings announcement-க்கு முன் விலை வீழ்ச்சி ஆபத்து உள்ளது.
-   பங்குகளை உடனே விற்றுவிடாமல் downside-ஐ வரையறுக்க விரும்புகிறது.

இதற்காக ஒரு மாத கால KO Put option வாங்குகிறது:

-   Strike = USD 65.00
-   Notional = 10,000 shares
-   Premium = USD 1.20 per share
-   Total premium = 10,000 × USD 1.20 = USD 12,000

### 8.2 Scenario A --- KO விலை USD 58 ஆகக் குறைகிறது

Put holder-க்கு USD 65 strike-ல் விற்கும் உரிமை உள்ளது. சந்தை விலை USD 58
என்றால், அந்த உரிமைக்கு மதிப்பு உண்டு.

Strike மற்றும் market price இடையிலான வேறுபாடு:

``` text
(USD 65 − USD 58) × 10,000
= USD 70,000
```

அதாவது expiry நேரத்தில் put-ன் intrinsic value USD 70,000. இதிலிருந்து USD
12,000 premium-ஐ கணக்கில் எடுத்தால், premium-ஐ விட மேலான option payoff நன்மை
USD 58,000 ஆகும்.

**முக்கிய நுணுக்கம்:** USD 70,000 என்பது USD 58-ல் சந்தையில் விற்பதுடன் ஒப்பிடும்
put-ன் gross payoff. பங்கு வாங்கிய விலை USD 68.40-ஐ அடிப்படையாகக் கொண்ட முழு
portfolio loss கணக்கில் premium-யும் சேர்க்கப்பட வேண்டும்.

Expiry-ல் பங்கின் மதிப்பு: USD 58 × 10,000 = USD 580,000. ஆரம்ப வாங்கிய செலவு:
USD 68.40 × 10,000 = USD 684,000. Hedge premium உட்பட நிகர மொத்த இழப்பு:

``` text
பங்கு இழப்பு = (68.40 − 58.00) × 10,000 = USD 104,000
Put payoff = USD 70,000
Premium = USD 12,000

Hedge-க்கு பின் நிகர இழப்பு
= 104,000 − 70,000 + 12,000
= USD 46,000
```

### 8.3 Scenario B --- KO விலை USD 75 ஆக உயர்கிறது

Put-ஐ பயன்படுத்த வேண்டிய அவசியமில்லை; சந்தையில் USD 75-க்கு விற்பது சிறந்தது. Put
worthless-ஆக expire ஆகலாம்.

-   பங்கின் விலை உயர்வால் கிடைக்கும் லாபம்: (75 − 68.40) × 10,000 = USD 66,000
-   Option premium: USD 12,000
-   Premium-க்கு பின் நிகர லாபம்: USD 54,000

Put இல்லாமல் இருந்தால் நிகர பங்கு லாபம் USD 66,000. Put வாங்கியதால் downside
பாதுகாப்பு கிடைத்தது; அதற்கான செலவு premium.

### 8.4 Protective Put-ன் payoff வடிவம்

Protective Put என்பது insurance போல:

-   பங்கின் விலை குறைந்தால் strike price கீழே downside-ஐ வரையறுக்க உதவுகிறது.
-   பங்கின் விலை உயர்ந்தால் பங்கின் upside-ஐ வைத்திருக்க முடியும்.
-   அதற்குப் பதிலாக premium செலுத்தப்படுகிறது.

இதே asymmetric payoff கருத்து FX option-லுமுள்ளது. Asset class வேறுபட்டாலும்
அடிப்படை யோசனை ஒன்றே.

மாற்றாக, pension fund equity index futures-ஐ short செய்தால் symmetric hedge
கிடைக்கலாம்; premium இல்லாதிருக்கலாம், ஆனால் market உயர்வின் நன்மையையும் futures
loss மூலம் இழக்க நேரிடும்.

------------------------------------------------------------------------

## 9. Hedge Accounting மற்றும் Regulation

ஒரு trade பொருளாதார ரீதியாக risk-reducing hedge ஆக இருப்பது வேறு;
accounting அல்லது regulation-ல் **formal hedge** என அங்கீகரிக்கப்படுவது வேறு.

Source note-ல் குறிப்பிடப்பட்ட முக்கிய கட்டமைப்புகள்:

-   IFRS 9 --- சர்வதேச financial reporting-ல் hedge accounting.
-   ASC 815 --- US GAAP derivative மற்றும் hedge accounting.
-   EMIR --- EU derivatives regulation; குறிப்பிட்ட non-financial
    counterparties-க்கான clearing threshold தொடர்பான விதிகள்.
-   Dodd-Frank --- அமெரிக்காவில் சில non-financial end-user commercial
    hedging-க்கு பொருந்தக்கூடிய clearing exception.

**எச்சரிக்கை:** இவை விதிகளின் எளிய அறிமுகம் மட்டுமே; சட்ட ஆலோசனை அல்லது தற்போதைய
விதிகளின் முழு விளக்கம் அல்ல. Eligibility, reporting, clearing thresholds
மற்றும் விதிவிலக்குகள் jurisdiction மற்றும் காலத்திற்கேற்ப மாறலாம்.
செயல்படுத்துவதற்கு முன் அதிகாரப்பூர்வ வழிகாட்டுதலையும் நிறுவனத்தின்
Compliance/Legal குழுவையும் அணுக வேண்டும்.

### 9.1 Hedge accounting ஏன் தேவை?

Hedge instrument-ன் gain/loss மற்றும் hedged item-ன் offsetting loss/gain
வெவ்வேறு reporting period-களில் பதிவு செய்யப்பட்டால், உண்மையில் risk
குறைந்திருந்தாலும் reported earnings அதிகமாக ஏற்றத்தாழ்வு காணலாம். Hedge
accounting தகுதி இருந்தால், பொருத்தமான accounting treatment மூலம் அவற்றைச்
சரியான காலத்தில் பிரதிபலிக்க உதவும்.

### 9.2 Booking system-ல் என்ன பதிவு செய்ய வேண்டும்?

குறைந்தபட்சமாக source note வலியுறுத்துவது:

1.  **Hedge designation:** Trade தொடங்கும்போதே hedge designation
    செய்யப்பட்டதா?
2.  **Hedged item link:** எந்த குறிப்பிட்ட exposure-ஐ இந்த trade hedge
    செய்கிறது?
3.  **Effectiveness testing:** Hedge உண்மையில் எதிர்பார்த்தபடி செயல்படுகிறதா
    என்பதற்கான தொடர்ந்து பரிசோதனைத் தரவு.

இதனுடன் நடைமுறையில் designation documentation, risk-management objective,
hedged risk, hedge ratio, measurement dates மற்றும் audit trail போன்றவை
தேவைப்படலாம். சரியான தேவைகள் accounting framework மற்றும் policy-ஐப் பொறுத்தவை.

### 9.3 Java / software architecture பார்வை

ஒரு trade table-ல் `isHedge = true` என்ற boolean மட்டும் வைத்திருப்பது
போதாமலிருக்கலாம். உதாரண data model:

``` text
Trade
-----
tradeId
productType
buySell
notional
currency
tradeDate
maturityDate
counterpartyId

HedgeDesignation
----------------
designationId
hedgeTradeId
hedgedExposureId
hedgedRiskType
designationTimestamp
accountingFramework
documentationReference
effectivenessMethod
status

HedgeEffectivenessResult
------------------------
resultId
designationId
measurementDate
hedgeLegChange
hedgedItemChange
effectivenessMetric
resultStatus
```

இது ஒரு conceptual schema மட்டுமே; production design-க்கு தனிப்பட்ட
requirements, data governance மற்றும் accounting rules தேவை.

**முக்கிய design principle:** Trade economic terms-ஐ தனியாகவும், hedge
designation மற்றும் exposure relationship-ஐ தனியாகவும் சேமிக்க வேண்டும். ஒரே
instrument ஒரு portfolio-வில் hedge ஆகவும் மற்றொன்றில் speculation ஆகவும்
இருக்கலாம்.

------------------------------------------------------------------------

## 10. வங்கியில் Hedging எங்கே பயன்படுத்தப்படுகிறது?

  -----------------------------------------------------------------------
  Business function       பொதுவான exposure        பொதுவான instrument
  ----------------------- ----------------------- -----------------------
  Treasury / ALM          Bank loan மற்றும் bond    Interest Rate Swaps,
                          book-ன் interest-rate    Interest Rate Futures
                          risk                    

  Corporate Treasury      எதிர்கால FX payable /    FX Forwards, FX Options
                          receivable              

  Energy / Airline        Fuel மற்றும் பிற          Commodity Futures,
  clients                 commodity input cost    Options, Swaps

  Credit / Loan Portfolio Bond / loan default     CDS
  Management              risk                    

  Asset Managers /        Equity portfolio        Equity Index Futures,
  Pension Funds           market-value risk       Protective Puts

  Dealers / Market Makers Client option trades    Underlying-ல் Delta
                          காரணமாக உருவாகும் delta  hedging
                          exposure                
  -----------------------------------------------------------------------

### Delta hedging --- சுருக்கமாக

Dealer ஒருவர் client-க்கு option விற்றால், underlying price மாறும்போது
option-ன் மதிப்பும் மாறும். Option-ன் delta-க்கு ஏற்ப underlying asset அல்லது
futures position எடுத்து dealer தனது net directional exposure-ஐ
குறைக்கலாம். Delta காலத்தோடு மற்றும் சந்தை நகர்வோடு மாறக்கூடும்; ஆகவே hedge-ஐ
மீண்டும் சரிசெய்ய வேண்டியிருக்கும்.

------------------------------------------------------------------------

## 11. Official references

உங்கள் source note பின்வரும் அதிகாரப்பூர்வ அமைப்புகளைச் சுட்டுகிறது. இவை பொதுவான
reference points; குறிப்பிட்ட விதியின் தற்போதைய நிலையை சரிபார்க்க அசல்
அதிகாரப்பூர்வ ஆவணங்களையே பார்க்கவும்.

-   **ISDA** --- OTC derivatives documentation மற்றும் market standards:
    https://www.isda.org/
-   **IFRS Foundation** --- IFRS 9 மற்றும் hedge accounting:
    https://www.ifrs.org/
-   **FASB** --- US GAAP / ASC standards: https://www.fasb.org/
-   **CFTC** --- US derivatives regulation: https://www.cftc.gov/
-   **ESMA** --- EU financial markets regulation / EMIR:
    https://www.esma.europa.eu/

------------------------------------------------------------------------

## 12. Quick recap --- தேர்வுக்கு நினைவில் கொள்ள வேண்டியவை

1.  **Hedge vs speculation:** ஏற்கெனவே உள்ள exposure-ஐ offset செய்கிறதா
    என்பதே முக்கியம்; instrument பெயர் மட்டும் போதாது.
2.  **Hedging ≠ risk-free:** Combined P&L ஏற்றத்தாழ்வு குறையலாம்; basis,
    counterparty மற்றும் sizing risk மீதமிருக்கும்.
3.  **Symmetric hedge:** Forward / Futures / Swap --- விலை அல்லது
    exposure-ஐ lock செய்கிறது; favourable move-ன் நன்மையை விட்டுக்கொடுக்கலாம்.
4.  **Asymmetric hedge:** Option --- premium செலுத்தி downside protection
    பெறுகிறது; favourable move-ஐ வைத்திருக்கலாம்.
5.  **Interest-rate hedge:** Notional-ஐ மட்டும் match செய்யாதீர்கள்; DV01 /
    risk sensitivity-ஐ match செய்யுங்கள்.
6.  **CDS hedge:** Same reference entity என்பதால் credit match வலுவானது;
    seller default / wrong-way risk மீதமிருக்கும்.
7.  **FX forward vs option:** Forward விலை உறுதி; option premium-க்கு
    எதிராக நெகிழ்வுத்தன்மை.
8.  **Basis risk:** Actual exposure மற்றும் proxy hedge instrument ஒரே
    மாதிரி நகராதது.
9.  **Protective put:** Premium செலுத்தி பங்கின் downside-ஐ வரையறுக்கிறது;
    upside-ஐப் பாதுகாக்கிறது.
10. **Formal hedge status:** Accounting / regulatory treatment-க்கு
    documentation, hedged item link மற்றும் effectiveness evidence
    முக்கியம்.

------------------------------------------------------------------------

## 13. Recap Quiz --- உங்களையே சோதித்துப் பாருங்கள்

**Q1.** ஒரே Futures contract, ஒரு இடத்தில் hedge ஆகவும் மற்றொரு இடத்தில்
speculation ஆகவும் இருப்பதற்குக் காரணம் என்ன?

**Q2.** Hedge செய்த position-ன் combined P&L-ஐ ஏன் "risk-free" என்று கூற
முடியாது?

**Q3.** Forward-ஐத் தேர்ந்தெடுத்தால் என்ன நன்மை, என்ன trade-off? Option-ஐத்
தேர்ந்தெடுத்தால் என்ன நன்மை, என்ன செலவு?

**Q4.** Interest-rate hedge-ல் notional-ஐ match செய்வதை விட DV01-ஐ match
செய்வது ஏன் முக்கியம்?

**Q5.** Northern Rail bond-க்கு CDS வாங்குவது ஏன் வலுவான credit hedge?
அதிலும் என்ன risk மீதமிருக்கிறது?

**Q6.** Jet fuel-ஐ WTI crude oil மூலம் hedge செய்தால் basis risk எதனால்
ஏற்படுகிறது?

**Q7.** KO protective put மற்றும் FX option hedge-ன் payoff வடிவத்தில் உள்ள
ஒற்றுமை என்ன?

**Q8.** ஒரு trade பொருளாதார ரீதியாக hedge ஆக இருந்தாலும், அது ஏன் தானாகவே
hedge accounting அல்லது regulatory clearing exemption பெறாது?

------------------------------------------------------------------------

## 14. Quiz Answers --- விளக்கத்துடன்

### A1

Trader-க்கு ஏற்கெனவே உள்ள exposure-ஐ புதிய trade offset செய்கிறதா என்பதே
வேறுபாடு. Bank of Toronto ஏற்கெனவே வைத்திருக்கும் bond risk-க்கு எதிராக
futures short செய்கிறது; Maple Leaf Pension Fund offset செய்ய வேண்டிய
position இல்லாமல் futures வாங்கினால் அது புதிய directional exposure.

### A2

Basis risk, hedge ratio தவறு, maturity mismatch, counterparty risk மற்றும்
wrong-way risk போன்றவை இருக்கலாம். Hedge combined P&L-ஐ flatten செய்கிறது;
எல்லா market risk-யும் அழிக்காது.

### A3

Forward பொதுவாக upfront option premium இல்லாமல் விலை உறுதியைத் தருகிறது;
ஆனால் சாதகமான சந்தை நகர்வின் நன்மையையும் விட்டுக்கொடுக்கலாம். Option premium
செலவாகும்; ஆனால் மோசமான நகர்விலிருந்து பாதுகாப்புடன் சாதகமான நகர்வைப் பயன்படுத்தும்
வாய்ப்பைத் தருகிறது.

### A4

Bond மற்றும் futures ஒரே notional இருந்தாலும் அவற்றின் interest-rate
sensitivity வேறுபடலாம். DV01 ஒரு basis point rate move-க்கு ஏற்ப வரும் பண
மாற்றத்தை அளவிடுவதால், hedge sizing-க்கு இது பொருத்தமான risk measure.

### A5

CDS மற்றும் bond ஒரே reference entity --- Northern Rail Corp --- என்பதால்
credit exposure match வலுவானது. ஆனால் CDS protection seller default risk,
recovery/settlement mismatch, maturity mismatch மற்றும் wrong-way risk
மீதமிருக்கலாம்.

### A6

WTI crude மற்றும் jet fuel தொடர்புடையவை; ஆனால் ஒரே பொருள் அல்ல. Refining
margin / crack spread அல்லது jet-fuel-specific supply-demand மாற்றம்
காரணமாக அவற்றின் விலைகள் வேறுபட்டு நகரலாம். அந்த வேறுபாடுதான் basis risk.

### A7

இரண்டிலும் option premium upfront செலுத்தப்படுகிறது. சந்தை மோசமாக நகரும்போது
option பாதுகாப்பை வழங்குகிறது; சந்தை சாதகமாக நகரும்போது option-ஐ பயன்படுத்தாமல்
favourable market-ஐப் பயன்படுத்த முடியும். வாங்குபவரின் option loss
premium-க்கு வரையறுக்கப்படுகிறது.

### A8

Economic hedge என்பது P&L-கள் எதிர்திசையில் நகரும் உண்மை. Hedge accounting /
regulatory treatment-க்கு உரிய framework-ன் formal designation,
documentation, hedged item linkage மற்றும் தேவையான effectiveness evidence
தேவை. குறிப்பிட்ட விதிகள் jurisdiction-ஐப் பொறுத்தவை.

------------------------------------------------------------------------

## இறுதி நினைவூட்டல்

Hedging-ஐ புரிந்துகொள்ள இந்தக் கேள்வியை எப்போதும் கேளுங்கள்:

> **"எந்த existing risk-ஐ, எந்த instrument மூலம், எவ்வளவு அளவில் offset
> செய்கிறோம் --- அதற்குப் பதிலாக எந்த செலவு அல்லது residual risk-ஐ ஏற்கிறோம்?"**

இந்தக் கேள்விக்கு தெளிவான பதில் இருந்தால், hedge-ன் பொருளாதாரம், risk sizing,
trade capture மற்றும் reporting தேவைகளை சரியாக வடிவமைக்க முடியும்.
