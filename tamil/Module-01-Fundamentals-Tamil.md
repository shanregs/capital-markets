# Module 1 — Capital Markets Fundamentals (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **மூலம்:** English Module 1-ஐ பேச்சுத் தமிழ்ல எடுத்துச் சொல்றது. Technical சொற்கள் (bond, coupon, yield...) English-லயே இருக்கும்.

---

## 1. Capital Markets-னா என்ன? — Primary vs Secondary Market

**Definition:** Capital Markets-ங்கறது, நிறுவனங்களும் அரசும் securities (bonds, stocks) வெளியிட்டு பணம் திரட்டற இடம்; அப்புறம் investors அந்த securities-ஐ தங்களுக்குள்ள வாங்கி விக்கற இடமும் இதுதான்.

### இதுல யார் யார் இருக்காங்க

| Actor | Role |
|---|---|
| **Government of Canada** | Issuer — bond-ஐ உருவாக்கி cash திரட்டறாங்க |
| **Initial Investors** (issuance-ப்போ வாங்கற banks/funds) | புது bond-ஓட முதல் owners |
| **Goldman Sachs** | அந்த bond-ஐ அப்புறம் வித்துடற investor (secondary market seller) |
| **Bank Of Toronto** | Secondary market-ல அந்த bond-ஐ வாங்கற investor/dealer |

### எண்களோட உதாரணம் — Primary Market (புது issuance)

```text
PRIMARY MARKET  —  New Issuance
================================

   Government of Canada                         Investors
   ----------------------                       (Banks / Funds)
          |                                            |
          |  issues "Canada 5% Bond 2035"              |
          |  Total Face Value: CAD 500,000,000         |
          |-------------------------------------------->
          |     (brand-new bonds, ISIN: CA1358XXXXX)    |
          |                                            |
          <--------------------------------------------|
          |        CAD 500,000,000 cash                 |
          |                                            |

RESULT:
  - Government raises CAD 500,000,000 in cash.
  - The bond "Canada 5% Bond 2035" now exists for the first time.
```

அதாவது: அரசாங்கம் CAD 500 மில்லியன் cash-ஐ வாங்கிக்குது; அதுக்குப் பதிலா முதல் தடவையா "Canada 5% Bond 2035"-ங்கற bond உருவாகுது.

### Coupon Rate vs. Yield — "5%"-னா உண்மையில் என்ன?

"Canada 5% Bond 2035"-ல இருக்கற 5%-க்கு பேரு **coupon rate**; இது APR இல்ல (APR-ங்கறது loan/credit-ல சொல்ற வார்த்தை). Coupon rate issuance-ப்போவே fix ஆகிடும், அப்புறம் மாறவே மாறாது — bond மேல ஒட்டி வெச்ச label மாதிரி. காலப்போக்குல மாறுறது **yield**: நீங்க கொடுத்த விலையைப் பொறுத்து, உண்மையில உங்களுக்குக் கிடைக்கற annualized return.

```text
COUPON RATE  vs.  YIELD  (the "true APR-like" number)
========================================================

  Coupon Rate  = fixed, printed on the bond, never changes
                 "Canada 5% Bond 2035" always pays 5% of face value/year

  Yield        = the REAL annualized return, given what you actually paid
                 - Buy at par (100.00)   -> Yield ~= Coupon Rate (5.00%)
                 - Buy below par (98.50) -> Yield  >  Coupon Rate (you paid
                                             less, still get 5% + your
                                             money back at par -> better return)
                 - Buy above par (101.80)-> Yield  <  Coupon Rate
```

Yield-தான் "APR" மாதிரி நடந்துக்கற number — விலையை எல்லாம் சேர்த்துப் பார்த்த நேர்மையான effective annual return. Coupon rate-ங்கறது product மேல இருக்கற label மாதிரி. Yield to Maturity-ஓட முழுக் கணக்கு [Module 4](../docs/1-capital-markets/module-04-equity.md)/8-ல வரும் — இப்போதைக்கு இந்த ரெண்டையும் குழப்பிக்காதீங்க, அவ்வளவுதான்.

### Worked example — Bank Of Toronto ஒவ்வொரு வருஷமும் வேற வேற 5%-coupon bond வாங்கறாங்க

"Coupon rate மாறாது, yield எப்பவும் மாறும்"-ங்கறதை தெளிவா பார்க்க, ஒரே 5% coupon ஒவ்வொரு வருஷமும் எப்படி வேற மாதிரி நடந்துக்குதுன்னு பாருங்க — காரணம் கொடுத்த விலையும் வாங்கின face value-வும் ஒவ்வொரு தடவையும் வேற.

```text
COUPON RATE vs YIELD  —  the two formulas
============================================

  Annual Coupon Cash Flow = Face Value x Coupon Rate       <- FIXED, issuer decides
  Current Yield            = Annual Coupon Cash Flow / Price Paid   <- VARIES, market decides

  Price Paid = Face Value x (Market Price / 100)
```

| Year | Face Value Bought (CAD) | Coupon Rate | Market Price (per 100) | Price Paid | Annual Coupon Cash Flow | Current Yield | Yield vs. Coupon | Bank Of Toronto's Position |
|---|---|---|---|---|---|---|---|---|
| 2026 | 10,000,000 | 5% | 98.50 (discount) | 9,850,000 | 500,000 | 500,000 / 9,850,000 = **5.08%** | Yield > Coupon | **அதிக லாபம்** — face value-வுக்குக் கீழ வாங்கினதால, fix-ஆன CAD 500,000 coupon, நாம உண்மையில போட்ட cash-க்கு பெரிய return ஆகுது. |
| 2027 | 15,000,000 | 5% | 101.25 (premium) | 15,187,500 | 750,000 | 750,000 / 15,187,500 = **4.94%** | Yield < Coupon | **குறைவான லாபம்** — face value-வுக்கு மேல கொடுத்ததால, அதே 5% coupon போட்ட cash-க்கு சின்ன return ஆகுது. Income இருக்கு, ஆனா efficiency கம்மி. |
| 2028 | 8,000,000 | 5% | 95.00 (deep discount) | 7,600,000 | 400,000 | 400,000 / 7,600,000 = **5.26%** | Yield > Coupon | **நாலு வருஷத்துலயே best** — par-க்கு எவ்வளவு கீழ வாங்கறோமோ அவ்வளவு gap, அது Bank Of Toronto-க்கு சாதகம். |
| 2029 | 20,000,000 | 5% | 103.75 (premium) | 20,750,000 | 1,000,000 | 1,000,000 / 20,750,000 = **4.82%** | Yield < Coupon | **நாலு வருஷத்துலயே worst** — அதிக premium கொடுத்ததால, உண்மையான return printed 5% coupon-ஐ விட ரொம்பக் கீழ. |

எது மாறாம இருக்கு, எது மாறுது — reporting systems-க்கு இது ஏன் முக்கியம்:

-   **Coupon Rate (5%)** — எல்லா rows-லயும் ஒரே மாதிரி, ஏன்னா இது bond/security master-ல இருக்கற static field, issuance-ப்போவே fix. இதை திரும்பக் கணக்கு போடவே மாட்டாங்க.
-   **Price** — market நிர்ணயிக்கறது, தினமும் refresh ஆகும்; ஒரே 5% coupon மேல இருக்கற நாலு வேற வேற outcome-க்கும் இதுதான் ஒரே காரணம்.
-   **Yield** — எப்பவும் *derived* field: `Annual Coupon Cash Flow ÷ Price Paid`. ஒரு system Yield-ஐ static-ஆ store பண்ணா, Price மாறின அடுத்த நிமிஷமே அது தப்பாகிடும்.
-   Coupon rate-ஐ மட்டும் பார்த்து "Bank Of Toronto-க்கு நல்லதா கெட்டதா"-ன்னு சொல்றது அர்த்தமே இல்ல — 2028, 2029 ரெண்டுலயும் 5% coupon தான், ஆனா ஒண்ணு best year, இன்னொண்ணு worst year; காரணம் கொடுத்த விலை மட்டும்தான்.

புரிய வைக்கறதுக்காக இங்க எளிமையான **Current Yield** பயன்படுத்தியிருக்கோம். **Yield to Maturity (YTM)** — discount/premium maturity-ல par-க்கு வரும்போது கிடைக்கற gain/loss-ஐயும் சேர்த்துக்கறது — அதை [Module 4](../docs/1-capital-markets/module-04-equity.md)/8-ல விரிவா பார்ப்போம்.

### ஒரே Issuance, பல Investors — ஒரே bond-ஐ பல investors பங்கு போட்டு வாங்க முடியுமா?

முடியும் — இதுதான் வழக்கம், விதிவிலக்கு இல்ல. ஒரு bond issuance-ஐ ஒரே investor வாங்கறது கிட்டத்தட்ட நடக்கவே நடக்காது. மொத்த CAD 500,000,000 face value ஒரு பெரிய pool; அது **பல investors மத்தியில பிரிஞ்சு** போகும், ஒவ்வொத்தரும் தனக்கு வேண்டிய face-value slice-ஐ எடுத்துக்கறாங்க (minimum denomination, உதாரணமா CAD 1,000 அல்லது CAD 5,000 lots). எல்லா slice-க்கும் ஒரே ISIN, ஒரே coupon rate (5%), ஒரே maturity (2035) — Apple-ஓட ஒவ்வொரு share-உம் யார் கையில இருந்தாலும் ஒண்ணுதானே, அது மாதிரி இதுவும் fungible units.

```text
ONE ISSUANCE, SPLIT ACROSS MANY INVESTORS
============================================

                         Government of Canada
                    issues ISIN CA1358XXXXX
                    Total Face Value: CAD 500,000,000
                    Coupon: 5%   Maturity: 2035
                                |
        (allocated across many investors in the SAME issuance)
                                |
        -----------------------+-----------------------
        |            |               |               |
        v            v               v               v
   Bank Of      Goldman Sachs   Maple Leaf      ... hundreds more
   Toronto                      Pension Fund     investors ...
   CAD 50M      CAD 120M        CAD 30M          CAD 300M (combined)
   face value   face value      face value       face value

  50,000,000 + 120,000,000 + 30,000,000 + 300,000,000 = CAD 500,000,000
  (adds up to the full issue size — same ISIN, same 5% coupon, same 2035
   maturity for every single investor, regardless of how much they hold)
```

அதனாலதான் கீழ வர்ற secondary market உதாரணம் வேலை செய்யுது: Goldman Sachs-க்கு issuance-ப்போ CAD 120,000,000 slice கிடைச்சிருந்தா, அதுல CAD 10,000,000-ஐ மட்டும் அப்புறம் Bank Of Toronto-க்கு விக்க முடியும் — ஏன்னா அந்த ISIN-ஓட ஒவ்வொரு unit face value-உம் ஒண்ணுதான், interchangeable. யாரோட slice-உம் "வேற மாதிரி" இல்ல; வச்சிருக்கற *quantity* மட்டும்தான் வேற.

**Allocation உண்மையில எப்படி நடக்குது (சுருக்கமா):** Government bonds பொதுவா **auction** மூலமா வெளியாகும் — investors (முக்கியமா primary dealers/banks) எவ்வளவு face value வேணும், என்ன price/yield-ல வேணும்னு bid போடுவாங்க; ஏத்துக்கிட்ட bids-க்கு அரசு CAD 500,000,000-ஐப் பிரிச்சுக் கொடுக்கும். Corporate bonds-ல பொதுவா **syndication/book-building** — underwriting banks பல buy-side investors கிட்ட demand கேட்டு, வந்த orders அடிப்படைல allocate பண்ணுவாங்க. எப்படி இருந்தாலும் முடிவு ஒண்ணுதான்: ஒரே ISIN, ஒரே coupon, பல holders.

### எண்களோட உதாரணம் — Secondary Market (ஏற்கனவே இருக்கற bond-ஐ trade பண்றது)

```text
SECONDARY MARKET  —  Trading an Existing Bond
==============================================

   Goldman Sachs (Seller)                    Bank Of Toronto (Buyer)
   -----------------------                   ------------------------
          |                                            |
          |  Sells CAD 10,000,000 face value of         |
          |  "Canada 5% Bond 2035" @ Price 98.50         |
          |-------------------------------------------->|
          |        Bonds (same ISIN, existing since       |
          |         primary issuance — nothing new)        |
          |                                            |
          <--------------------------------------------|
          |   Cash = CAD 9,850,000                       |
          |   (10,000,000 x 98.50 / 100)                 |
          |                                            |

RESULT:
  - Government of Canada is NOT involved and receives no cash.
  - Only ownership changes hands. This is what ~95% of trade/
    reporting data you'll work with actually represents.
```

### ஏன் 98.50? — Bond price எப்படி வேலை செய்யுது

இங்க ரெண்டு விஷயம் பலரைக் குழப்பும்: price ஏன் "98.50"-ன்னு quote பண்ணி 100-ஆல வகுக்கறாங்க; price 100-க்குக் கீழ இருந்தா seller-க்கு நஷ்டமா? ரெண்டுமே முதல் பார்வைல தோணறது மாதிரி இல்ல.

**100-ஆல ஏன் வகுக்கறோம் — இது quoting convention, நாமா derive பண்ற கணக்கு இல்ல.**
Bond price-ஐ "face value 100-க்கு இவ்வளவு"-ன்னு quote பண்ணுவாங்க — இது ஒரு rate, dollar amount இல்ல.

```text
BOND PRICE QUOTING CONVENTION
==============================

  Quoted Price = 98.50  means:
  "For every CAD 100 of face value, this bond costs CAD 98.50 today."

To get the cash value of ANY face amount traded, scale by that same ratio:

  Market Value = Face Value Traded x (Quoted Price / 100)

Example: Face Value Traded = CAD 10,000,000
  Market Value = 10,000,000 x (98.50 / 100)
               = 10,000,000 x 0.9850
               = CAD 9,850,000
```

ஏன் flat dollar figure-ஆ இல்லாம "per 100"-ன்னு quote பண்றாங்க? ஏன்னா ஒரே bond பயங்கரமா வேற வேற size-ல trade ஆகும் — Goldman Sachs CAD 10,000,000 face value trade பண்ணலாம், இன்னொரு investor அதே bond-ல CAD 250,000 trade பண்ணலாம். ஒரே "per 100" rate இருந்தா trade size எவ்வளவா இருந்தாலும் எல்லாரும் அதே quoted number-ஐப் பயன்படுத்தலாம் — FX exchange rate (EUR/USD = 1.08) மாதிரி, இல்லன்னா unit price × quantity மாதிரி.

**ஏன் 100 இல்லாம 98.50?**
Issuance-ப்போ bond பொதுவா 100 (அல்லது கிட்ட) விலைல வரும் — இதுக்குப் பேரு "par". 100 கொடுத்து, maturity-ல 100 திரும்பப் பெறுவீங்க, நடுவுல coupons வேற. Issuance-க்கு அப்புறம், bond price **தற்போதைய market interest rates-க்கு எதிர்த்திசைல** நகரும்:

```text
WHY PRICE DRIFTS AWAY FROM PAR (100)
=====================================

At issuance:
  Canada 5% Bond 2035 issued at Price = 100.00 (par)
  Market interest rates at the time  ~= 5.00%
  -> A 5% coupon is exactly competitive. Fair price = 100.

Later, market interest rates rise to ~5.30%:
  -> Newly issued bonds now offer 5.30%, better than this old bond's fixed 5%.
  -> Nobody will pay 100 for a bond only paying 5% when 5.30% is available.
  -> The bond's PRICE must drop so its effective yield rises to compete.
  -> Market repricing settles around Price = 98.50.

(If rates had instead FALLEN to ~4.70%, this bond's fixed 5% would look
 attractive, and its price would rise ABOVE par, e.g. 101.80.)
```

98.50-ங்கறது ஏதோ தோணின discount இல்ல — "இன்னிக்கு interest rates இப்படி இருக்கும்போது, இந்த fixed 5% cash-flow stream எவ்வளவு விலைக்கு இருந்தா வாங்கறவருக்கு நியாயமான, போட்டி போடக்கூடிய return கிடைக்கும்?"-ங்கற கேள்விக்கு market கொடுக்கற real-time பதில். (Yield, duration பத்தின முழு mechanics [Module 4](../docs/1-capital-markets/module-04-equity.md)/8-ல. இப்போதைக்கு ஞாபகம் வெச்சுக்கோங்க: **price, interest rates-க்கு எதிர்த்திசைல நகரும்**.)

**Seller (Goldman Sachs) உண்மையிலயே நஷ்டத்துல இருக்காங்களா?**
98.50-ஐ மட்டும் வெச்சு சொல்ல முடியாது — அவங்க ஆரம்பத்துல என்ன விலைக்கு வாங்கினாங்க, நடுவுல எவ்வளவு coupons வாங்கினாங்கங்கறதைப் பொறுத்தது.

```text
IS GOLDMAN SACHS AT A LOSS?  —  depends on cost basis + coupons earned
========================================================================

Scenario: Goldman Sachs bought this bond 2 years ago AT ISSUANCE (par = 100.00)
          Face value = CAD 10,000,000
          Cost then  = CAD 10,000,000  (10,000,000 x 100.00/100)

Today, Goldman Sachs sells at 98.50:
          Proceeds now = CAD 9,850,000  (10,000,000 x 98.50/100)

Price-only comparison:
          9,850,000 - 10,000,000 = -CAD 150,000   <- looks like a loss

BUT Goldman Sachs also collected 2 years of coupons while holding it:
          Coupon = 5% x CAD 10,000,000 = CAD 500,000 / year
          2 years of coupons = CAD 1,000,000

Net result over the holding period:
          -150,000 (price)  +  1,000,000 (coupons)  =  +CAD 850,000 net gain

  -> Even though the SALE PRICE is below what they originally paid,
     Goldman Sachs still came out ahead once coupon income is included.
```

இன்னும் ரெண்டு விஷயம் ஞாபகம் வெச்சுக்கோங்க:

1.  98.50-ங்கற price, buyer-ஓட/seller-ஓட தனிப்பட்ட P&L பத்தி எதுவும் சொல்லாது — அது இன்னிக்கான நியாயமான market price, அவ்வளவுதான். Bank Of Toronto-வும் "discount வாங்குறாங்க"-ன்னு இல்ல; par-ல இருந்தப்போவ விட இப்போ குறைவா yield கொடுக்கற bond-க்கு நியாயமான விலையைக் கொடுக்கறாங்க.
2.  Seller தன் original cost-க்குக் கீழ விக்கறதுக்கு "நஷ்டம்"-ங்கறதைத் தவிர வேற காரணங்களும் இருக்கலாம் — cash தேவை (liquidity), portfolio rebalance பண்றது, இல்லன்னா rates இன்னும் உயரும்னு நினைச்சு (அப்புறம் இன்னும் கம்மி விலைக்கு விக்கறதை விட இப்போவே வித்துடலாம்). Par-க்குக் கீழ விக்கறது சாதாரணம், தப்பான முடிவு இல்ல.

> **Mental model:** Price (98.50) = இன்னிக்கு face value 100-க்கான நியாயமான மதிப்பு, interest rates தீர்மானிக்கறது. Gain/loss = இன்னிக்கு price vs. *உங்க சொந்த* cost basis, கூட வாங்கின coupons. இது ரெண்டும் முற்றிலும் வேற வேற கேள்விகள்.

**Takeaway:** Reporting system-ல "Trade" data பார்த்தா, record-ல "New Issue" / "Primary" / "Syndication"-ன்னு தெளிவா சொல்லாத வரை, அது secondary market-ன்னு எடுத்துக்கோங்க.

---

## 2. Buy-side vs Sell-side

### இதுல யார் யார்

| Actor | Side | இந்த உதாரணத்துல Role |
|---|---|---|
| **Maple Leaf Pension Fund** | Buy-side | Retiree contributions-ஐ bonds-ல முதலீடு பண்ணனும் |
| **Bank Of Toronto** | Sell-side | Price quote பண்ணி, trade-ஓட மறுபக்கத்தை எடுத்துக்கும் |
| **Interbank Market** (மற்ற dealers) | Sell-side | Bank Of Toronto-கிட்ட இல்லாத bonds-ஐ source பண்ற இடம் |

### எண்களோட உதாரணம்

```text
BUY-SIDE  <-->  SELL-SIDE  FLOW
================================

  Maple Leaf Pension Fund        Bank Of Toronto            Interbank Market
      (BUY-SIDE)                (SELL-SIDE / DEALER)         (Other Dealers)
        |                              |                              |
        | 1. "Buy CAD 50,000,000        |                              |
        |    Canada 5% Bond 2035"       |                              |
        |----------------------------->|                              |
        |                              |                              |
        |                              | 2. Sources bonds from         |
        |                              |    inventory / interbank      |
        |                              |----------------------------->|
        |                              |<-----------------------------|
        |                              |   Bought @ 98.40               |
        |                              |                              |
        | 3. Quotes price 98.50         |                              |
        |<-----------------------------|                              |
        |                              |                              |
        | 4. Fund accepts & pays         |                              |
        |    CAD 49,250,000              |                              |
        |    (50,000,000 x 98.50/100)   |                              |
        |----------------------------->|                              |
        |<-----------------------------|                              |
        |     Bonds, CAD 50,000,000       |                              |
        |     face value                 |                              |

Bank Of Toronto's spread (a preview of "valuation" — Module 10):
   Sold @ 98.50, bought @ 98.40  ->  0.10 x 50,000,000 / 100 = CAD 50,000 profit
```

**Takeaway:** Buy-side = வேற யாரோடவோ capital-ஐ (pension holders, fund investors) முதலீடு பண்றவங்க. Sell-side = buy-side clients எதிர்ல trade பண்றதுக்கு market/liquidity கொடுக்கறவங்க.

---

## 3. Banks, Brokers, Dealers, Funds

எந்த actor-ஐப் பத்தியும் கேக்க வேண்டிய அடிப்படைக் கேள்வி: **"இவங்க risk-ஐ தங்க சொந்த book-ல எடுத்துக்கறாங்களா, இல்ல வெறுமனே pass பண்றாங்களா?"**

### 3a. Dealer (Principal) — Bank Of Toronto bond-ஐ தன் சொந்த book-ல எடுத்துக்குது

```text
DEALER TRADE  —  Bank Of Toronto acts as PRINCIPAL
====================================================

   Client A                    Bank Of Toronto                Client B
   (Seller)                    (DEALER)                       (Buyer)
      |                              |                              |
      | Sells CAD 5,000,000 bond      |                              |
      | @ price 99.00                 |                              |
      |----------------------------->|                              |
      |<-----------------------------|                              |
      |     Cash CAD 4,950,000         |                              |
      |                              |                              |
      |                        [ Bond now sits on Bank Of Toronto's
      |                          OWN balance sheet — Bank Of Toronto
      |                          carries MARKET RISK until resold ]
      |                              |                              |
      |                              |  Later, sells to Client B      |
      |                              |  @ price 99.20                 |
      |                              |----------------------------->|
      |                              |<-----------------------------|
      |                              |     Cash CAD 4,960,000         |

  Bank Of Toronto's profit = 4,960,000 - 4,950,000 = CAD 10,000
  (Bank Of Toronto held price risk on the bond in between the two trades)
```

### 3b. Broker (Agent) — Bank Of Toronto bond-ஐ ஒருபோதும் சொந்தமா வெச்சுக்காது

```text
BROKER TRADE  —  Bank Of Toronto acts as AGENT
=================================================

   Client A                    Bank Of Toronto                Client B
   (Seller)                    (BROKER)                       (Buyer)
      |                              |                              |
      |--[order: sell CAD 5M bond]-->|                              |
      |                              |--[matches order]------------>|
      |                              |                              |
      |<================  Bond, CAD 5,000,000  =================>|
      |<================  Cash, CAD 4,950,000  ==================|
      |      (bond & cash move DIRECTLY between the two clients;    |
      |       Bank Of Toronto's books are never touched)             |
      |                              |                              |
      |------[pays commission CAD 2,000]--------------------------->|
      |                              |<-----------------------------|
      |                              |    Commission CAD 2,000        |

  Bank Of Toronto's profit = CAD 2,000 commission ONLY.
  Bank Of Toronto held ZERO market risk on the bond itself.
```

### Fund

**Fund** (உதாரணம்: Section 2-ல வந்த Maple Leaf Pension Fund) பல தனிநபர்கள் / நிறுவனங்களோட பணத்தை ஒண்ணா சேர்த்து, ஒரு mandate-படி (உதாரணம்: "investment-grade government bonds-ல மட்டும் முதலீடு") invest பண்ணும். மேல பார்த்த dealer/broker trades-ல இது கிட்டத்தட்ட எப்பவும் client பக்கம் இருக்கற **buy-side** actor.

---

## 4. Front Office / Middle Office / Back Office

### இதுல யார் / எந்த systems

| Office | Actor / System | என்ன produce பண்ணுது |
|---|---|---|
| Front Office | Trader, trading system | Raw trade (யார், என்ன, என்ன விலை, எவ்வளவு) |
| Middle Office | Risk officer, valuation engine, confirmation system | Validate ஆன trade, MTM value, signed confirmation |
| Back Office | Settlement ops, custodian, regulatory reporting engine | Settle ஆன position, ledger entry, regulatory report |

### எண்களோட உதாரணம் — ஒரு trade, ஆரம்பம் முதல் முடிவு வரை

```text
TRADE LIFECYCLE ACROSS FRONT / MIDDLE / BACK OFFICE
=====================================================

Day T  (Trade Date)
--------------------
FRONT OFFICE
  Trader at Bank Of Toronto sells CAD 10,000,000 face value of
  "Canada 5% Bond 2035" to Goldman Sachs @ price 98.50
        |
        v
MIDDLE OFFICE   (same day, T)
  - Checks risk limit: max exposure to Goldman Sachs = CAD 20,000,000
    Current exposure after this trade = CAD 9,850,000  -> OK, within limit
  - Computes trade value:
        10,000,000 x 98.50 / 100 = CAD 9,850,000
  - Sends confirmation to Goldman Sachs
  - Goldman Sachs affirms: trade details match  -> CONFIRMED
        |
        v
Day T+2  (Settlement Date — standard for bonds is "T+2")
-----------------------------------------------------------
BACK OFFICE
  - Delivers CAD 10,000,000 face value of bonds via custodian/CSD
  - Receives CAD 9,850,000 cash into Bank Of Toronto's account
  - Books the entry into the general ledger
  - Reconciles Bank Of Toronto's internal record vs custodian's statement
  - Reports the trade to the regulator (e.g., under MiFID II transaction
    reporting rules), if the product/jurisdiction requires it
```

**Takeaway:** "Reporting" table-ல நீங்க பார்க்கற ஒவ்வொரு field-உம் (trade ID, MTM value, settlement status, regulatory flags) இந்த மூணு offices-ல சரியா ஒண்ணுல, இதே வரிசைல produce ஆனது. Data issue debug பண்ணும்போது "இந்த field-ஐ எந்த office produce பண்ணுது?"-ன்னு கேளுங்க — எந்த upstream system-ஐ check பண்ணனும்னு அதுவே சொல்லிடும்.

---

## 5. Exchange-Traded vs OTC

### இதுல யார் யார்

| Actor | Role |
|---|---|
| Investors A–G | Exchange-ல trade பண்ற, ஒண்ணுக்கொண்ணு சம்பந்தம் இல்லாத பல participants |
| Stock Exchange (உதா., NYSE) | எல்லா buyers/sellers-ஐயும் match பண்ற central, regulated venue |
| Bank Of Toronto, Goldman Sachs | ஒரு private OTC contract-ல இருக்கற அந்த ரெண்டு parties மட்டும் |
| Trade Repository | OTC derivative விவரங்களை report பண்ணியே ஆகணும்ங்கற இடம் (regulatory requirement) |

### உதாரணம் — Exchange-Traded (centralized, many-to-many)

```text
EXCHANGE-TRADED  —  centralized order book
=============================================

     Investor A   Investor B   Bank Of Toronto   Investor C
          \            |              |               /
           \           |              |              /
            v           v              v             v
        +-------------------------------------------------+
        |         STOCK EXCHANGE  (e.g., NYSE)              |
        |         Central Order Book                        |
        |         Apple Inc. shares — Price: USD 227.15      |
        +-------------------------------------------------+
            ^           ^              ^             ^
           /            |              |              \
          /             |              |               \
     Investor D    Investor E     Investor F        Investor G

  - Price is PUBLIC and visible to all participants.
  - Contract is IDENTICAL for everyone (1 share = 1 share).
  - No need for bilateral reporting — the exchange itself is the record.
```

### உதாரணம் — OTC (bilateral, private, custom contract)

```text
OTC  —  bilateral, privately negotiated
==========================================

  Bank Of Toronto   <====  Private Interest Rate Swap  ====>   Goldman Sachs

       Notional:            CAD 100,000,000
       Bank Of Toronto pays: Fixed 4.00%
       Goldman Sachs pays:   Floating (CORRA + spread)
       Maturity:             5 years

  - Terms are visible ONLY to these two parties.
  - No central exchange records this trade.
  - Both parties are legally REQUIRED to separately report the trade's
    details to a Trade Repository (e.g., under EMIR / MiFID II) so that
    regulators have visibility that would otherwise not exist.
```

**Takeaway:** இந்த exchange-vs-OTC இடைவெளிதான் "regulatory reporting" ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.md)) ஒரு தனி discipline-ஆ இருக்கறதுக்கு *மூல காரணம்* — exchange-traded products-க்கு exchange-ஏ record-ஆ இருக்கும்னு regulators நம்பறாங்க; ஆனா OTC-க்கு, firms தாமாகவே trade data-வை report பண்ணணும்.

---

## 6. Settlement Conventions — T, T+1, T+2 — உண்மையான தேதிகளோட

**Definition:** **T** ("Trade Date") = trade agree ஆன calendar நாள். **T+n** ("Settlement Date") = அதுக்கு n **business days** அப்புறம் — securities-உம் cash-உம் உண்மையில கை மாறற நாள். "Business days"-ல weekends-உம் அந்த market-ஓட public holidays-உம் சேராது; அதனாலதான் T+2-ங்கறது calendar date-ஓட 2-ஐ வெறுமனே கூட்டறது *இல்ல*.

தற்போதைய standard conventions (2026 நிலவரம்):

| Cash / Derivative | Product | Standard settlement |
|---|---|---|
| CASH | US & Canadian equities | T+1 (May 2024-ல T+2-ல இருந்து மாறிச்சு) |
| CASH | Government / corporate bonds | Market-ஐப் பொறுத்து T+1 அல்லது T+2 |
| CASH | FX spot | பெரும்பாலான pairs-க்கு T+2 (USD/CAD-க்கு T+1) |
| DERIVATIVE | OTC derivatives (உதா., Interest Rate Swaps) | Notional settle ஆகவே ஆகாது — periodic net cash flows மட்டும் (Section 8 பாருங்க) |

### Example 1 — நடுவுல weekend இல்ல

```text
Trade Date (T)  : Monday,   17 August 2026
T+1             : Tuesday,  18 August 2026
T+2             : Wednesday,19 August 2026   <- Settlement Date
```

### Example 2 — நடுவுல weekend இருக்கு

```text
Trade Date (T)  : Thursday, 20 August 2026
T+1             : Friday,   21 August 2026
                   Saturday, 22 August 2026  <- not a business day, skipped
                   Sunday,   23 August 2026  <- not a business day, skipped
T+2             : Monday,   24 August 2026   <- Settlement Date
```

### Example 3 — public holiday-உம் குறுக்க வருது

```text
Trade Date (T)  : Thursday,  3 September 2026
T+1             : Friday,    4 September 2026
                   Saturday,  5 September 2026  <- weekend, skipped
                   Sunday,    6 September 2026  <- weekend, skipped
                   Monday,    7 September 2026  <- Labour Day (market holiday), skipped
T+2             : Tuesday,   8 September 2026    <- Settlement Date

Notice: T+2 landed 5 CALENDAR days after T, not 2 — because only
business days count, and this stretch had both a weekend AND a holiday.
```

**Takeaway:** System-ல "Settlement Date", "Trade Date"-க்கு 2 calendar days-க்கு மேல தள்ளி இருந்தா, அது தானா data bug இல்ல — எதையாவது broken-ன்னு முடிவு பண்றதுக்கு முன்னாடி, நடுவுல weekend அல்லது market holiday வந்துச்சான்னு check பண்ணுங்க.

---

## 7. Custodian vs CSD — securities-ஐ உண்மையில யார் வெச்சிருக்காங்க, யார் நகர்த்தறாங்க

**Definitions:**

-   **Custodian** — client (investor அல்லது dealer) சார்பா securities-ஐ safekeeping-ல வெச்சிருக்கற bank; settlement instructions-ஐ நிறைவேத்தும், coupon/dividend payments-ஐ client சார்பா வசூலிக்கும், account statements தரும். நிஜ உலக உதாரணங்கள்: BNY Mellon, State Street, Northern Trust.
-   **CSD (Central Securities Depository)** — ஒரு market-க்கான ஒரே, central electronic ledger. Securities-ஓட *இறுதி legal ownership*-ஐ book-entry-ஆ (paper certificates இல்லாம) பதிவு பண்ணுது; settlement date-ல ownership-ஓட final transfer இங்கதான் நடக்குது. ஒவ்வொரு market-க்கும் பொதுவா ஒரு முக்கிய CSD இருக்கும்: Canada-ல **CDS** (Canadian Depository for Securities), US-ல **DTC**, international/Eurobonds-க்கு **Euroclear / Clearstream**.

Investor-க்கு CSD-ல நேரடியா account கிடையாது. Chain இன்னும் ஒரு layer ஆழம்:

```text
CUSTODY CHAIN  —  who's books actually move
================================================

  Bank Of Toronto  ---(client of)--->  Custodian (e.g. BNY Mellon)
                                              |
                                    (custodian holds an account at)
                                              v
                                    CSD (e.g. CDS / DTC)  <- master ledger
                                              ^
                                    (custodian holds an account at)
                                              |
  Goldman Sachs    ---(client of)--->  Custodian (its own, or same one)

On Settlement Date (T+2):
  1. Both custodians instruct the CSD to move the bond, on their
     clients' behalf, from Goldman Sachs' custody account to
     Bank Of Toronto's custody account.
  2. The CSD updates its central book-entry record — this is the
     LEGAL moment ownership changes.
  3. Cash moves separately via the banking payment system, ideally
     at the same time (Delivery vs Payment - DvP) so neither side
     is left holding one leg without the other.
```

**Takeaway:** "Custodian" = உங்க positions-ஐ வெச்சிருந்து, உங்க சார்பா settlement-ஐ பார்த்துக்கற உங்க சொந்த bank. "CSD" = ஒரு market-க்கு ஒரே ஒரு உண்மையான record-of-truth ledger; எல்லா custodians-உம் கடைசில இதுலதான் plug ஆகறாங்க. Positions-ஐ reconcile பண்ணும்போது (Back Office, Section 4), உங்க internal books உங்க custodian statement-ஓட tie out ஆகணும் — அந்த statement CSD-ஓட tie out ஆகணும்.

---

## 8. Notional Amount & Interest Rate Swaps-ஐ முதல் தடவையா பார்க்கலாம்

**Definition:** **Notional amount** = derivative-ல periodic interest cash flows-ஐ *கணக்கிட மட்டும்* பயன்படுத்தற reference principal; ரெண்டு parties-க்குள்ளயும் இது உண்மையில பரிமாறப்படவே படாது. Bond trade-ல இருந்து இதுதான் முக்கிய வித்தியாசம்: bond-ஓட face value உண்மையில deliver ஆகும் (Section 1); swap-ஓட notional கணக்குப் போட மட்டும் பயன்படற ஒரு number.

**இதோட நெருங்கிய, ஆனா உண்மையில வேற concept: Underlier.** **Underlier** ("Underlying"-ன்னும் சொல்வாங்க) = derivative-ஓட cash flows-ஐ *இயக்கற* உண்மையான reference asset, rate, index, அல்லது entity. Notional-ங்கறது, Underlier என்ன செய்யுதோ அதுக்கு மேல போடற *size* multiplier மட்டும். ஒவ்வொரு derivative-க்கும் சரியா ஒரு Underlier (சில சமயம் சின்ன basket) இருக்கும்; ஆனா அது *என்ன வகை*ங்கறது asset class-க்கு ஏத்தபடி முழுசா மாறும்:

| Asset Class | Underlier பொதுவா என்னவா இருக்கும் | உதாரணம் |
|---|---|---|
| Rates (இந்த section) | ஒரு reference interest rate | **CORRA** (கீழ callout பாருங்க) |
| Equity ([Module 4](../docs/1-capital-markets/module-04-equity.md)) | ஒரு stock, அல்லது stocks-ஓட basket/index | Apple Inc. common stock |
| Credit ([Module 9](../docs/1-capital-markets/module-09-credit.md)) | ஒரு company-ஓட creditworthiness ("Reference Entity") | Northern Rail Corp |
| FX ([Module 6](../docs/1-capital-markets/module-06-fx.md)) | ஒரு currency pair | USD/CAD |
| Commodity ([Module 7](../docs/1-capital-markets/module-07-commodities.md)) | வெளியிடப்பட்ட price index | ICE Brent Crude |

கீழ வேலை செய்து பார்க்கப்போற IRS-ல, **Notional (CAD 100,000,000), Underlier (CORRA) ரெண்டும் ஒரே trade-ஐ விவரிக்கற ரெண்டு தனித்தனி numbers, ரெண்டு வேற கேள்விகளுக்குப் பதில் சொல்லுது**: Notional = "இந்த trade எவ்வளவு பெருசு", Underlier = "floating leg உண்மையில எதுக்கு எதிரா reset ஆகுது". இந்த ரெண்டையும் குழப்பிக்கறது beginners பண்ற பொதுவான தப்பு — CAD 100,000,000 notional swap-உம் CAD 10,000,000 notional swap-உம் *அதே* Underlier (CORRA)-ஐ reference பண்ணலாம்; அதே மாதிரி *அதே* Notional இருக்கற ரெண்டு swaps முற்றிலும் வேற Underliers-ஐ reference பண்ணலாம்.

> **CORRA-ன்னா உண்மையில என்ன?** **CORRA (Canadian Overnight Repo Rate Average)** = Canada-ஓட risk-free reference rate — **Bank of Canada** ஒவ்வொரு business day-யும் கணக்கிட்டு publish பண்ணுது. Government of Canada securities-ஐ collateral-ஆ வெச்ச உண்மையான overnight repurchase (repo) transactions-ல இருந்து derive ஆகுது; இப்போ நிறுத்தப்பட்ட CDOR, LIBOR benchmarks மாதிரி banks-ஓட *மதிப்பீடுகள்*-ல இருந்து இல்ல. உண்மையான, observable transactions-ல இருந்து வருவதால, banks-ஓட கருத்துக் கணிப்பை நம்பியிருக்கற rate-ஐ விட இதை manipulate பண்றது ரொம்பக் கஷ்டம் — LIBOR-rigging scandals-க்கு அப்புறம் உலக regulators எல்லா முக்கிய currency-யையும் இது மாதிரி rate-க்கு மாத்தினதுக்கு இதுதான் காரணம் (US-ல SOFR, UK-ல SONIA, EU-ல €STR). CDOR-ல இருந்து CORRA-க்கு மாறின முழுக் கதையும் [Module 8](../docs/1-capital-markets/module-08-rates.md) §1-ல; இந்த module-க்கு CORRA-ங்கறது "இந்த trade-ஓட floating leg எதுக்கு எதிரா reset ஆகுதோ அந்த number" அவ்வளவுதான்.

Section 5-ல பார்த்த OTC swap-ஐ திரும்பப் பார்க்கலாம் — Bank Of Toronto, Goldman Sachs, Notional CAD 100,000,000, 5-year maturity, quarterly payments; Bank Of Toronto **fixed** rate கொடுக்கும், Goldman Sachs **floating** rate (CORRA + spread) கொடுக்கும்:

```text
INTEREST RATE SWAP  —  one quarterly payment date, worked example
======================================================================

Notional (reference only, NEVER exchanged): CAD 100,000,000
Period: ~91 days (one quarter), day count actual/365

Bank Of Toronto's FIXED leg:
  100,000,000 x 4.00% x (91 / 365) = CAD 997,260

Goldman Sachs' FLOATING leg (CORRA averaged 3.20% + 0.05% spread = 3.25%):
  100,000,000 x 3.25% x (91 / 365) = CAD 810,068

NETTING  (only the difference actually moves, one direction):
  997,260 - 810,068 = CAD 187,192

RESULT on this payment date:
  Bank Of Toronto  ---- pays CAD 187,192 net ---->  Goldman Sachs

  - The CAD 100,000,000 notional itself never moves, on this date
    or any other — it only ever exists to compute the two legs above.
  - This repeats every quarter for 5 years, with the floating leg
    reset each period to whatever CORRA + spread then is.
```

இது வேணும்னே ஒரு cash flow-ஓட mechanics மட்டும், swaps-ஓட theory இல்ல — swap-ஐ price பண்றது/value பண்றது (discount curves, DV01/PV01, mark-to-market) **[Module 10](../docs/1-capital-markets/module-10-valuation.md): Valuation**-ல விரிவா. இப்போதைக்கு ரெண்டு விஷயம் ஞாபகம் வெச்சுக்கோங்க: (1) notional ஒரு calculation input, settle ஆகற cash amount இல்ல; (2) நடைமுறைல ஒவ்வொரு period-க்கும் ரெண்டு gross payments இல்ல, ஒரே ஒரு net payment தான் நகரும்.

**Takeaway:** Derivative trade record-ல பெரிய "Notional" value (உதா., CAD 100,000,000) இருந்து, cash/custody systems-ல எங்கயும் அந்தத் தொகைக்கான settlement இல்லன்னா — அது எதிர்பார்த்ததுதான்; notional settle ஆகவே கூடாது. Periodic net cash flows மட்டும்தான் settle ஆகும்.

> **Deep dive இருக்கு:** இந்த section ஒரு IRS-ஓட ஒரு quarter-ஐ மட்டும், இந்த curriculum-க்குத் தேவையான அளவுல பார்க்குது. முழுப் படம் — இதே trade அதன் 5 வருஷ வாழ்க்கைல 20 quarterly settlements எல்லாத்தையும் கடந்து போறது, floating leg-ஓட CORRA compounding எப்படி வேலை செய்யுது, ஒவ்வொரு payment date, maturity-ல final settlement, சம்பந்தப்பட்ட Trade/Valuation fields — இதுக்கு தனி [Interest Rate Swap (IRS) Deep Dive](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.md) பாருங்க.

---

## Actors — முழு Cast Reference

| Actor | Buy-side / Sell-side | இந்த module-ல பொதுவா என்ன role |
|---|---|---|
| Government of Canada | Issuer (technically ரெண்டுமில்ல) | Primary market-ல புது bonds வெளியிடும் |
| Maple Leaf Pension Fund | Buy-side | Client/member பணத்தை bonds-ல invest பண்ணும் |
| Bank Of Toronto | Sell-side | Trade-ஐப் பொறுத்து dealer (principal) அல்லது broker (agent) |
| Goldman Sachs | Sell-side (இந்த உதாரணங்கள்ல counterparty/dealer-ஆ) | Secondary market, OTC trades-ல counterparty |
| Stock Exchange (உதா., NYSE) | Market infrastructure | Exchange-traded products-க்கான central venue |
| Trade Repository | Market infrastructure | OTC trades report பண்ண வேண்டிய central registry |
| Custodian / CSD | Market infrastructure | Owners சார்பா securities-ஐ hold/settle பண்ணும் (மேல Section 7; [Module 13](../docs/1-capital-markets/module-13-parties-leis.md)-ல இன்னும் ஆழமா) |

---

## Recap Quiz — Numbers Edition

1.  Bank Of Toronto ஒரு bond-ஐ 98.40-க்கு வாங்கி, அதே bond-ஐ அப்புறம் 99.10-க்கு விக்குது. Face value CAD 20,000,000 என்றால், Bank Of Toronto-ஓட profit எவ்வளவு? அவங்க dealer-ஆ செயல்பட்டாங்களா, broker-ஆ?
2.  ஒரு pension fund, CAD 8,000,000 face value bond-ஐ Bank Of Toronto வழியா (முழுக்க broker-ஆ மட்டும்) 97.75 விலைல, CAD 1,500 flat commission-ஓட விக்குது. Pension fund-க்கு எவ்வளவு cash கிடைக்கும்? Bank Of Toronto எவ்வளவு சம்பாதிக்கும்?
3.  ஒரு trade Monday-ல (T) agree ஆகுது, bonds T+2-ல settle ஆகும். Monday trade date என்றால், settlement date எந்த calendar நாள்? அந்த நாட்கள்ல ஒவ்வொண்ணுலயும் எந்த "office" (front/middle/back) active-ஆ இருக்கும்?
4.  NASDAQ-ல நடக்கற Apple shares trade-ஐ தனியா Trade Repository-க்கு report பண்ண வேண்டியதில்ல, ஆனா Bank Of Toronto, Goldman Sachs இடையிலான CAD 100,000,000 Interest Rate Swap-ஐ ஏன் report பண்ணணும்?
5.  ஒரு trade Thursday, 3 September 2026-ல agree ஆகுது, T+2-ல settle. Monday, 7 September 2026 market holiday. உண்மையான Settlement Date எந்த calendar date? Saturday, 5 September 2026 ஏன் இல்ல?
6.  Custodian செய்யறதுக்கும் CSD செய்யறதுக்கும் என்ன வித்தியாசம்? Investor நேரடியா CSD-ல account வெச்சுக்காம, investor-ஓட custodian-க்கு ஏன் தனி account தேவை?
7.  CAD 100,000,000 notional Interest Rate Swap-ல, Bank Of Toronto fixed 4.00% கொடுக்குது, Goldman Sachs floating 3.10% (spread ஏற்கனவே சேர்த்து) கொடுக்குது, 91-day quarter (actual/365). யார் யாருக்கு கொடுக்கணும்? net-ஆ எவ்வளவு cash நகரும்? CAD 100,000,000 notional-ல ஏதாவது பகுதி settle ஆகுமா?
8.  ரெண்டு Interest Rate Swaps-க்கும் Underlier CORRA — ஒண்ணுக்கு Notional CAD 50,000,000, இன்னொண்ணுக்கு CAD 500,000,000. ஒரே Underlier இருக்கறதால இந்த ரெண்டு trades-உம் பொருளாதார ரீதியா ஒரே size-ன்னு சொல்லலாமா? ஏன்/ஏன் இல்ல — Notional எந்தக் கேள்விக்குப் பதில், Underlier எந்தக் கேள்விக்குப் பதில்ங்கறதை ஒரே வாக்கியத்துல சொல்லுங்க.

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 2](../docs/1-capital-markets/module-02-hierarchy.md): Asset Class → Product → Instrument → Trade**-க்குப் போலாம்.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Profit = CAD 140,000; Bank Of Toronto dealer-ஆ செயல்பட்டது.**

```text
Profit = (99.10 - 98.40) / 100 x 20,000,000
       = 0.70 / 100 x 20,000,000
       = CAD 140,000
```

Bank Of Toronto bond-ஐ முதல்ல வாங்கி, அப்புறம் விக்கறதுக்கு முன்னாடி வெச்சிருந்தது — இதுதான் principal risk எடுக்கறது, dealer-ஓட அடையாளம். Broker எப்பவும் position-ஐ தன் book-ல எடுக்காது; அதனால "ஒரே bond-ல குறைவா வாங்கி அதிகமா விக்கறது" dealer-க்கு மட்டும்தான் பொருந்தும்.

**2. Pension fund-க்கு CAD 7,818,500 கிடைக்கும்; Bank Of Toronto-க்கு CAD 1,500.**

```text
Market value = 8,000,000 x 97.75 / 100 = CAD 7,820,000
Pension fund receives = 7,820,000 - 1,500 (commission) = CAD 7,818,500
Bank Of Toronto earns = CAD 1,500 commission only
```

Broker/agent-ஆ இருக்கும்போது Bank Of Toronto bond-ஐ சொந்தமா வெச்சுக்கவே இல்ல; அதனால அதோட ஒரே வருமானம் flat commission — trade-ல market risk பூஜ்யம்.

**3. Settlement Date = Wednesday.**

Monday (T) → Tuesday (T+1) → Wednesday (T+2) — மூணு நாளும் weekdays என்பதால் நடுவுல weekend வரல.

-   **Front Office** T (Monday)-ல active — trade-ஐ execute பண்ணுது.
-   **Middle Office** அதே T (Monday)-ல active — risk checks, valuation, confirmation/affirmation.
-   **Back Office** T+2 (Wednesday)-ல active — bonds deliver, cash receive, ledger entries, reconcile, report.

**4. Exchange-ஏ public record; OTC-க்கு அந்த மாதிரி record இல்ல.**

Apple/NASDAQ trade-க்கு தனி Trade Repository reporting தேவையில்ல, ஏன்னா exchange ஏற்கனவே ஒரு public, regulated, central venue — exchange-ஓட சொந்த data feed மூலமா regulators ஒவ்வொரு trade-யையும் real time-ல பார்க்கறாங்க. CAD 100,000,000 IR Swap OTC: சரியா ரெண்டு parties-க்கு இடையிலான private, bilateral contract; அதைப் பதிவு பண்ற central venue எதுவும் இல்ல. ரெண்டு counterparties-உம் தனித்தனியா Trade Repository-க்கு report பண்ணணும்னு சட்டப்படி கட்டாயம் இல்லன்னா (உதா., EMIR/MiFID II) regulators-க்கு அதைப் பத்தி ஒண்ணுமே தெரியாது — அந்தத் தேவைதான் உண்மையில இருக்கு.

**5. Settlement Date = Tuesday, 8 September 2026.**

```text
T          : Thursday,  3 September 2026
T+1        : Friday,    4 September 2026
(skipped)  : Saturday,  5 Sept / Sunday, 6 Sept  - weekend
(skipped)  : Monday,    7 Sept - Labour Day, market holiday
T+2        : Tuesday,   8 September 2026
```

Saturday, 5 September ஏன் இல்லன்னா — Saturday ஒரு candidate-ஆகவே இல்ல; T+2 என்பது T-ல இருந்து business days-ஐ மட்டும் எண்ணும். இந்த இடைவெளில weekend-ஐயும் அடுத்த Monday holiday-வையும் தாண்டணும், அதனால உண்மையான T+2, trade date-ல இருந்து முழுசா அஞ்சு calendar days தள்ளி போச்சு.

**6. Custodian client-facing; CSD ஒரு market முழுமைக்கும் பொதுவான ஒரே central ledger.**

**Custodian** = investor அல்லது dealer-ஓட securities-ஐ safekeeping-ல வெச்சிருந்து, அவங்க சார்பா settlement instructions-ஐ execute பண்ணி, statements தர்ற bank — பல custodians இருக்காங்க, ஒவ்வொத்தருக்கும் அவங்கவங்க clients. **CSD** = அந்த market-ல வெளியான ஒவ்வொரு security-ஓட இறுதி legal ownership-ஐ பதிவு பண்ற ஒரே central ledger. Investor-ஓட custodian-க்கு CSD-ல தனி account ஏன் தேவைன்னா — ஒவ்வொரு தனி investor-கிட்டயும் CSD நேரடியா deal பண்ணாது; அது நிர்வகிக்கக்கூடிய எண்ணிக்கையிலான பெரிய participant accounts-ஐ (custodians, clearing members, major banks) மட்டும் வெச்சிருக்கும், ஒவ்வொண்ணும் தனக்குக் கீழ இருக்கற பல clients-ஓட positions-ஐ சேர்த்து net பண்ணும். Investor-ஓட உண்மையான legal ownership, இந்தச் சங்கிலி முழுக்க end-to-end சரியா reconcile ஆனா மட்டும்தான் நம்பகமா இருக்கும்.

**7. Bank Of Toronto, Goldman Sachs-க்கு net CAD 224,384 கொடுக்கும்; notional settle ஆகாது.**

```text
Fixed leg (Bank Of Toronto pays)  : 100,000,000 x 4.00% x (91/365) = CAD 997,260
Floating leg (Goldman Sachs pays) : 100,000,000 x 3.10% x (91/365) = CAD 772,877
Net = 997,260 - 772,877 = CAD 224,384
```

Fixed leg பெருசா இருக்கறதால, இந்த payment date-ல **Bank Of Toronto, Goldman Sachs-க்கு net CAD 224,384 கொடுக்கும்**. CAD 100,000,000 notional-ல எந்தப் பகுதியும் settle ஆகாது — ரெண்டு legs-ம் கணக்கிட மட்டும்தான் அது இருக்கு. Net ஆன cash வித்தியாசம் மட்டும்தான் உண்மையில நகரும்.

**8. இல்ல — ஒரே Underlier இருக்கறது economic size பத்தி எதுவும் சொல்லாது. Notional "இந்த trade எவ்வளவு பெருசு"-ங்கறதுக்குப் பதில் (CAD 50,000,000 vs. CAD 500,000,000 — ஒவ்வொரு period-லயும் cash flows 10 மடங்கு வித்தியாசம்); Underlier "floating leg எதுக்கு எதிரா reset ஆகுது"-ங்கறதுக்குப் பதில் (CORRA, ரெண்டு trades-லயும் ஒரே மாதிரி).**

ரெண்டு trades ஒரே Underlier-ஐ share பண்ணியும் size-ல ரொம்ப வேறுபடலாம்; அதே மாதிரி ஒரே Notional இருக்கற ரெண்டு trades முற்றிலும் வேற Underliers-ஐ reference பண்ணலாம் (உதா., ஒரு CAD 100,000,000 swap CORRA-க்கு எதிரா, இன்னொரு CAD 100,000,000 swap முற்றிலும் வேற rate-க்கு எதிரா) — இந்த ரெண்டு fields-உம் cash-flow கணக்குக்கு சுதந்திரமான inputs; ஒரே விஷயத்தை விவரிக்கற ரெண்டு வழிகள் இல்ல. Section 8-ல Underlier callout தெளிவா வரைஞ்ச வித்தியாசம் இதுதான்.
