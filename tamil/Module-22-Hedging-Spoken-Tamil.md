# Module 22 — Hedging Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Reference module**, **[Interest Rate Swap Deep Dive](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.html)**, **[ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html)** குடும்பத்தை சேர்ந்தது. Hedging உதாரணங்கள் [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9–10, [Module 6](../docs/1-capital-markets/module-06-fx.html) §4d, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §4d, [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) §4c-ல சிதறியிருக்கு — இந்த module அவை எல்லாத்தையும் ஒரே theory-க்குள்ள இழுத்து, தொகுப்பை நிறைவு செய்ய ஒரு Equity worked example-ஐ சேர்த்து, இந்த curriculum தொடர்ந்து திரும்பிவரும் "இது உண்மையில எப்படி/எங்க பயன்படுத்தப்படுது" என்ற நடைமுறைக் கேள்விக்கு பதில் சொல்லுது.
> **ஏற்கனவே இருக்கும் எழுத்துத் தமிழ் பதிப்பு:** [Hedging_Deep_Dive_Tamil](./Hedging_Deep_Dive_Tamil.html) — இது அதே தலைப்பின் *பேச்சுத் தமிழ்* பதிப்பு, மூல English module-ஐ அப்படியே பின்தொடர்ந்து.
> **மூலம்:** English Module 22-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Hedging-ன்னா என்ன? — Theory & மூன்று Trading நோக்கங்கள்

**Definition:** **hedging** = ஏற்கனவே இருக்கற ஒரு risk-க்கு **எதிர்த்திசைல மதிப்பு நகரும் ஒரு இரண்டாவது position-ஐ** வேணும்னே எடுப்பது, அதனால ஒரு பக்கத்துல இழப்பு மறுபக்கத்துல லாபத்தால — பகுதியா அல்லது முழுசா — ஈடுசெய்யப்படுது. Hedge தானே பணம் சம்பாதிக்கறது நோக்கம் இல்ல; *கூட்டுப் position*-ஓட சாத்தியமான விளைவுகளின் **வரம்பை குறுக்குவதுதான்**. ஒரு முழுசா hedge பண்ணின position, கட்டமைப்பால, சந்தை மேலே போனாலும் கீழே போனாலும் கிட்டத்தட்ட ஒரே நிகர விளைவைத் தரும் — சுவாரஸ்யமில்லாததா தோணுது, சரியா அதுதான் நோக்கம்.

புதுசா வர்றவருக்கு குழப்பம்: hedging, speculation, arbitrage மூணுமே **ஒரே instruments**-ஐ — இந்த curriculum ஏற்கனவே கட்டின அதே swaps, futures, options, forwards — பயன்படுத்துது. அவற்றை வேறுபடுத்துறது trade-ஓட வடிவம் இல்ல, trader-ஓட தொடக்க position:

| நோக்கம் | Trader-க்கு ஏற்கனவே ஈடுசெய்ய வேண்டிய exposure இருக்கா? | இலக்கு | இந்த curriculum-ல ஏற்கனவே உள்ள உதாரணம் |
|---|---|---|---|
| **Hedging** | **ஆமா** — ஏற்கனவே இருக்கற position அல்லது தெரிஞ்ச எதிர்கால தேவை | கூட்டுப் position-ஓட விளைவுகளின் *வரம்பை* குறைக்கறது | [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) §4c: Bank Of Toronto, தான் **ஏற்கனவே வெச்சிருக்கற** bonds-க்கு எதிரா T-Note futures-ஐ விற்கறது |
| **Speculation** | இல்ல — புத்தம்புது திசைசார்ந்த பந்தயம், எதுவும் ஈடுசெய்யப்படலை | விலை எங்க போகுதுங்கற ஒரு *பார்வையில்* இருந்து லாபம் | [Module 4](../docs/1-capital-markets/module-04-equity.html) §4c/§4d: Maple Leaf Pension Fund E-mini futures-ஐயும் AAPL call-ஐயும் வாங்குறது, ஒண்ணை ஈடுசெய்ய இல்ல, புது சந்தை exposure-ஐ **பெற** |
| **Arbitrage** | பொருந்தாது — பொருளாதார ரீதியா சமமான ரெண்டு விஷயங்களுக்கு இடையிலான தற்காலிக விலை இடைவெளியை பயன்படுத்துது | Mispricing-ல இருந்து கிட்டத்தட்ட risk-free லாபம், வேகமா மூடப்படும் | இந்த curriculum-ல வேறெங்கும் இல்ல; வேறுபாட்டை நிறைவு செய்ய மட்டும் இங்க |

```text
SAME INSTRUMENT, OPPOSITE MOTIVATION — THE SHARPEST ILLUSTRATION
========================================================================

  Module 15 §4c:  Bank Of Toronto SELLS 100 CME 10-Year T-Note
                  futures -- because it already OWNS bonds whose
                  value falls when rates rise. The future's loss/gain
                  is designed to CANCEL OUT the bond book's gain/loss.
                  -> THIS IS A HEDGE.

  Module 4 §4c:   Maple Leaf Pension Fund BUYS 20 E-mini S&P 500
                  futures -- with no existing short equity position
                  anywhere being offset. It's taking on NEW market
                  exposure on purpose.
                  -> THIS IS SPECULATION (or more precisely, deliberate
                     exposure-taking -- see Section 10's nuance on
                     "return-seeking" vs. purely directional bets).

  -> Both are Interest-Rate-or-Equity FUTURES. Nothing about the
     CONTRACT TYPE tells you which one is the hedge -- only knowing
     what ELSE the trader holds does.
```

**Takeaway:** ஒரு reporting அல்லது risk system, instrument வகையை மட்டும் வெச்சு "இது hedge-ஆ?"ன்னு ஊகிக்க முயன்றா, ஒவ்வொரு தடவையும் தவறாகும் — hedging என்பது ஒரு தனி trade-ஓட பண்பு இல்ல, *ரெண்டு positions இடையிலான உறவு*. அந்த உறவை systems எப்படி (ஏன்) முறையா பிடிக்குதுன்னு Section 9 சொல்லுது.

### கூடுதல் உதாரணம் — தமிழ் வாழ்க்கை ஒப்புமை: மழைக்காலத்துக்கு குடை

| | ஒப்புமை |
|---|---|
| **Hedging** | நாளைக்கு மழைவந்தா நனையக்கூடாதுன்னு (ஏற்கனவே வெளியே போக வேண்டிய தேவை இருக்கு) குடை வாங்கறது |
| **Speculation** | வெளியே போகவே தேவையில்லை; "நாளைக்கு மழை கட்டாயம் வரும்"ன்னு பந்தயம் கட்டி குடை வியாபாரத்துல பணம் போடறது |
| **Option vs Forward** | Option = குடைக்கு பணம் கொடுத்து வாங்கறது (மழை வந்தா பயன், வராட்டி பணம் போச்சு); Forward = "மழை வந்தாலும் வராட்டியும் இந்த விலைக்கே" என்ற நிலையான ஒப்பந்தம் |

---

## 2. அடிப்படை Mechanic — Risk-ஐ நீக்கறது இல்ல, P&L-ஐ ஈடுசெய்யறது

**Definition:** ஒவ்வொரு hedge-உம், அதே அடிப்படை risk factor (ஒரு rate, ஒரு விலை, ஒரு spread) நகரும்போது எதிர்த்திசைகள்ல நகரும் profit-and-loss கொண்ட ரெண்டு positions-ஐ இணைச்சு வேலை செய்யுது. கூட்டு P&L கோடு ஒவ்வொரு position-ஓட தனி P&L-ஐ விட **தட்டையானது** — அந்த தட்டையாக்கம் *தான்* hedge.

```text
THE GENERIC SHAPE OF EVERY HEDGE IN THIS MODULE
========================================================================

  UNDERLYING POSITION P&L:   loses money as risk factor moves one way,
                              gains as it moves the other way
                    +                                    -
                    |                                    |
       (e.g. bond value falls as rates rise)

  HEDGE POSITION P&L:        moves the OPPOSITE way to the underlying
                    -                                    +
                    |                                    |
       (e.g. short future GAINS as rates rise)

  COMBINED P&L:              flatter than either leg alone -- some
                              residual movement usually remains
                              (Section 7's basis risk), but the SWINGS
                              are smaller than holding the underlying
                              position unhedged
```

**Hedging ஒருபோதும் இலவசம் இல்ல.** செலவு ரெண்டு வடிவங்கள்ல ஒண்ணுல தெரியும், எந்த instruments குடும்பம் பயன்படுத்தப்படுதுங்கறதை பொறுத்து (Section 3 முழுசா விரிவாக்குது):

-   **வாய்ப்புச் செலவு (Opportunity cost)** — ஒரு symmetric hedge (forward, future, swap) ஒரு விளைவை lock பண்ணுது, அதாவது downside-ஐ நீக்கறது போலவே சரியா *upside*-ஐயும் விட்டுக்கொடுக்குது.
-   **வெளிப்படையான premium** — ஒரு asymmetric hedge (ஒரு option) முன்கூட்டியே பணம் செலவாகும், ஒரு insurance premium மாதிரி, downside-ஐ மட்டும் பாதுகாக்கறதுக்கு பதிலா upside-ஐ வெச்சிருக்க.

**Takeaway:** "நாங்க position-ஐ hedge பண்ணிட்டோம்"ன்னா "risk மறைஞ்சிடுச்சு"ன்னு அர்த்தமில்ல — சாத்தியமான விளைவுகளின் வரம்பு ஒரு செலவுக்கு (விட்டுக்கொடுத்த upside அல்லது கொடுத்த premium) குறுகிடுச்சுன்னு அர்த்தம். Hedge பண்ணின position-ஓட P&L ஒவ்வொரு தடவையும் சரியா பூஜ்யத்துக்கு தட்டையாகுதுன்னு காட்டும் reporting system கிட்டத்தட்ட கண்டிப்பா தவறு — Section 7-ஓட basis risk சாதாரண நிலை, முழுமையா தட்டையான கூட்டு P&L அரிய விதிவிலக்கு.

---

## 3. இரு Hedge Instrument குடும்பங்கள் — Symmetric vs. Asymmetric

**Definition:** இந்த curriculum கட்டின ஒவ்வொரு hedge instrument-உம் ரெண்டு குடும்பங்கள்ல ஒண்ணுல வருது; அவற்றுக்கு இடையிலான தேர்வு ஒரு hedger எடுக்கும் மிக முக்கியமான நடைமுறை முடிவு.

| | **Symmetric hedges** | **Asymmetric hedges** |
|---|---|---|
| **Instruments** | Forwards, Futures, Swaps | Options |
| **Upfront cost** | None (or near-none — futures need margin, not premium) | A premium, paid upfront |
| **Payoff shape** | Locks in one exact outcome — dollar-for-dollar offset in *either* direction | One-sided — protects the bad direction, keeps the good direction |
| **Max loss on the hedge itself** | Unbounded in the "wrong" direction (that's the whole point — it's meant to move against the trader when the underlying gains) | Capped at the premium paid |
| **Best analogy** | A fixed-price contract — you're locked in, for better or worse | An insurance policy — you pay a premium, you're only protected against loss |
| **Curriculum examples** | [Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §10's FX Forward, [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.md) §4c's IR Future, [Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §9's CDS (protection premium is periodic, but the *notional* payout structure is fixed by the contract, not optional) | [Module 6](../docs/1-capital-markets/module-06-fx.md) §4d's FX Option, [Module 7](../docs/1-capital-markets/module-07-commodities.md) §4d's Commodity Option |

**Takeaway:** Section 6 ரெண்டு குடும்பங்களையும் *சரியா அதே அடிப்படைத் தேவைக்கு* (3 மாசத்துல USD தேவைப்படும் Bank Of Toronto-ஓட client) எதிரா பக்கம்-பக்கம் வெக்குது — இது ரெண்டு வேறு products இல்ல, இரண்டும் "hedges"ன்னு அழைக்கப்படுது என்பதைத் தாண்டி, உண்மையான trade-offs-ஓட ஒரு உண்மையான தேர்வுன்னு காட்டும் மிகத் தெளிவான விளக்கம்.

---

## 4. Worked Example — Interest Rate Hedging (Duration/DV01)

**Definition:** இந்த curriculum-ல வேறிடத்துல ஏற்கனவே நிறுவப்பட்ட ரெண்டு உண்மைகளை மறுபயன்படுத்தி — Bank Of Toronto-ஓட Canada 5% Bond 2035 position-க்கு [Module 10](../docs/1-capital-markets/module-10-valuation.html) §3-ஓட **DV01** (1 Basis Point-ஓட Dollar Value), அதே Fixed Income book-க்கு எதிரா [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) §4c-ஓட short T-Note futures hedge.

```text
INTEREST RATE HEDGE — BANK OF TORONTO'S BOND BOOK vs. T-NOTE FUTURES
========================================================================

  THE EXPOSURE (Module 10 §3):
    Canada 5% Bond 2035 position
    Modified Duration: 7.2 | Market Value: CAD 9,850,000
    DV01 = CAD 7,092 per basis point
    -> if rates RISE, this bond position LOSES roughly CAD 7,092
       for every 1bp of rate increase (Module 10 §3's -CAD 177,300
       for a 25bp move is this same DV01 scaled up)

  THE HEDGE (Module 15 §4c):
    Bank Of Toronto SELLS 100 CME 10-Year T-Note futures @ 112'16
    -> if rates RISE, bond futures prices FALL, and a SHORT position
       GAINS -- moving opposite to the bond book's loss
    Next business day, price falls to 111'24: Variation Margin
    GAIN = USD 75,000, credited to Bank Of Toronto's margin account,
    "offsetting the loss on the actual bond portfolio the future is
    hedging" (Module 15 §4c's own wording)
```

**Hedge-ஐ அளவிடறது — "hedge ratio" கேள்வி.** எத்தனை futures contracts *சரியான* எண்? ஒரு notional-matching பயிற்சி இல்ல — CAD 9,850,000 bonds-ஐ CAD 9,850,000 futures notional-உடன் பொருத்துவது ஒரு hedge இல்ல, ஒரு தற்செயல், ஏன்னா ஒரு bond-க்கும் ஒரு bond future-க்கும் rate நகர்வுக்கு ஒரே விலை உணர்திறன் இல்ல. சரியான அணுகுமுறை face தொகைகளை இல்ல, **risk sensitivities**-ஐ பொருத்துது:

```text
HEDGE RATIO — THE GENERAL FORMULA (conceptual, not literally solved
here — Module 15 §4c's 100-contract trade is illustrative, not
derived from this formula against the exact bond position above)
========================================================================

  Number of futures contracts needed  ≈  Exposure's DV01
                                          -----------------------
                                          Future's DV01 per contract

  -> the goal is for the FUTURES POSITION'S total DV01 to
     approximately match the BOND POSITION'S DV01 (CAD 7,092/bp
     here), so a given rate move produces roughly equal and
     opposite dollar moves on each side.
```

**Takeaway:** interest rate hedging என்பது **risk-sensitivity பொருத்தல்**, notional பொருத்தல் இல்ல — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ஓட "notional ஒரு reference எண், நகர்றது இல்ல"ங்கற கருத்து hedge அளவீட்டுக்கு குறிப்பா பொருத்தப்பட்டது. Hedge ratio-வை தவறா பெறுவது (DV01-க்குப் பதிலா face value பயன்படுத்தறது) ஒரு பெரிய rate நகர்வு இடைவெளியை வெளிப்படுத்தும் வரை யாருமே உணராம, ஒரு position-ஐ **under-hedged அல்லது over-hedged** ஆக்கும் பொதுவான நிஜ உலகத் தவறு.

### கூடுதல் உதாரணம் — Hedge ratio-வை எண்களோட (முற்றிலும் விளக்க எண்கள்)

Bond DV01 = CAD 7,092/bp. ஒரு futures contract-ஓட DV01 per contract = CAD 110/bp என்று (விளக்கத்துக்கு மட்டும்; உண்மையான எண்ணை exchange specs-ல பாருங்க) வெச்சுக்கோங்க.

| அணுகுமுறை | கணக்கு | Contracts |
|---|---|---|
| **DV01 matching (சரி)** | 7,092 ÷ 110 | ≈ **64** |
| **Notional matching (தவறு)** | CAD 9,850,000 ÷ CAD 100,000 face per contract | ≈ **98** |

Notional-ஆ பொருத்தினா 98 contracts — சுமார் 53% அதிகம் (over-hedged): rates நகரும்போது futures லாபம்/நஷ்டம், bond இழப்பை விட பெருசா இருக்கும், அதாவது புது (எதிர்த்திசை) risk உருவாகிடும். அதனாலதான் DV01 matching முக்கியம்.

---

## 5. Worked Example — Credit Hedging (Bond Credit Risk-க்கு எதிரா CDS)

**Definition:** [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9-ஓட Credit Default Swap-ஐ முழுசா மறுபயன்படுத்தி — இந்த curriculum-ல மிகத் தூய்மையான hedge உறவு, ஏன்னா hedge instrument (CDS) hedge பண்ணப்படும் exposure-ஓட **சரியா அதே reference entity**-ஐ reference பண்ணுது, ஒரு தோராயத்தை இல்ல.

```text
CREDIT HEDGE — CDS ON NORTHERN RAIL CORP, AGAINST A BOND POSITION
========================================================================

  THE HYPOTHETICAL EXPOSURE (Module 2 §9's own framing): Bank Of
  Toronto holds Northern Rail Corp bonds (or has lent it money
  directly) -- if Northern Rail Corp defaults, the bond loses value.

  THE HEDGE (Module 2 §9, fully worked):
    Bank Of Toronto BUYS PROTECTION from Goldman Sachs
    Notional: CAD 20,000,000 | Spread: 120bps | Maturity: 5 years

    NO DEFAULT (normal case) -- Bank Of Toronto PAYS a quarterly
    premium: CAD 59,836 (the "cost of the hedge" -- this is money
    spent whether or not the bond ever defaults, the same "premium
    even if never used" logic as an insurance policy)

    DEFAULT OCCURS -- Goldman Sachs PAYS Bank Of Toronto:
      Loss Given Default = 20,000,000 x (1 - 40% recovery)
                          = CAD 12,000,000
    -> this payout is designed to roughly OFFSET the loss Bank Of
       Toronto simultaneously takes on the defaulted bond itself
```

**Takeaway:** இந்த module-ல "சரியான" hedge-க்கு மிக நெருக்கமானது இது, ஏன்னா reference entity பொருத்தம் சரியானது — ஆனா [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9 ஏற்கனவே சிக்கலைச் சுட்டிக்காட்டியது: CDS notional (CAD 20,000,000) bond exposure-ஓட உண்மையான அளவுக்கு எதிரா அளவிடப்படணும்; மேலும் — [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9-ஓட சொந்த cross-reference சொல்ற மாதிரி — Goldman Sachs (protection *seller்) சரியா Northern Rail Corp தோற்கற அதே நேரத்துல தோற்கும் வாய்ப்பு அதிகமா இருந்தா, அது **wrong-way risk**: hedge மிக அதிகம் தேவைப்படற நேரத்துலயே மிகக் குறைவா நம்பகமா இருக்கு. Reference-entity பொருத்தம் தானா counterparty-risk இல்லாத hedge-ஐ குறிக்காது.

---

## 6. Worked Example — FX Hedging, ரெண்டு வழிகள் (Forward vs. Option)

**Definition:** அதே அடிப்படைத் தேவை — Bank Of Toronto-ஓட client 3 மாசத்துல USD வாங்கணும் — கட்டமைப்பு ரீதியா ரெண்டு வேறு வழிகள்ல hedge பண்ணப்படுது, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §10-ஓட FX Forward-ஐயும் [Module 6](../docs/1-capital-markets/module-06-fx.html) §4d-ஓட FX Option-ஐயும் மறுபயன்படுத்தி. இது Section 3-ஓட symmetric-vs-asymmetric theory-ஐ முழுசா உறுதியாக்குது.

```text
SAME NEED, TWO HEDGE CHOICES — BUY USD IN 3 MONTHS
========================================================================

  OPTION A -- FX FORWARD (Module 2 §10, symmetric hedge, no premium)
    Locks in TODAY: pay CAD 13,545,000, receive USD 10,000,000,
    in 3 months, no matter what
    -> Spot moves to 1.3700 (USD costlier): Forward SAVED CAD 155,000
    -> Spot moves to 1.3400 (USD cheaper):  Forward COST CAD 145,000
       (compared to just buying at spot on the day)
    LOCKED IN EITHER WAY -- no premium paid, but also NO chance of
    benefiting from a favorable rate move

  OPTION B -- FX OPTION (Module 6 §4d, asymmetric hedge, premium paid)
    USD Call / CAD Put, Strike 1.3600, Notional USD 5,000,000
    Premium Paid: 5,000,000 x 0.0180 = CAD 90,000 (paid regardless
    of outcome -- the cost of the hedge)
    -> Spot moves to 1.3800 (USD costlier): EXERCISE, saves CAD
       100,000 vs. spot; net benefit after premium = CAD 10,000
    -> Spot moves to 1.3400 (USD cheaper): let it EXPIRE, buy USD
       at the cheaper spot rate instead -- maximum loss is the
       CAD 90,000 premium, capped, no matter how far spot falls
```

| | Forward (symmetric) | Option (asymmetric) |
|---|---|---|
| முன்கூட்டிய செலவு | இல்ல | CAD 90,000 premium |
| Rate சாதகமா நகர்ந்தா | பலனை இழக்கும் | பலனை வெச்சிருக்கும் (premium கழிச்சு) |
| Rate பாதகமா நகர்ந்தா | முழுசா பாதுகாக்கப்பட்டது, dollar-for-dollar | முழுசா பாதுகாக்கப்பட்டது, இழப்பு வரம்பு = premium |
| மோசமான நிலை | பின்னோக்கிப் பார்த்தா, எதுவுமில்லாமல் upside-ஐ விட்டுக்கொடுத்தது | பின்னோக்கிப் பார்த்தா, எதுவுமில்லாமல் premium-ஐ இழந்தது |

**Takeaway:** எந்தத் தேர்வும் புறநிலையா "சிறந்தது" இல்ல — hedger எதிர்கால தேவையைப் பத்தி முழுசா உறுதியா இருந்து விலை உறுதி மட்டும் வேணும்னா Forward மலிவான, எளிய hedge; Option அதிகம் செலவாகும், ஆனா hedger பாதுகாப்பையும் *அதே நேரத்துல* சாதகமான நகர்வின் வாய்ப்பையும் விட்டுக்கொடுக்க விரும்பாதப்போ சரியான கருவி. இந்த சரியான trade-off — upside இல்லாத உறுதி vs. upside வெச்சிருக்கும் பாதுகாப்பு — Section 8-ஓட Equity உதாரணத்துல வேறு underlying-ஓட அதே மாதிரி திரும்பி வருது.

---

## 7. Worked Example — Commodity Hedging & Basis Risk (குறைபாடுள்ள Hedges)

**Definition:** [Module 7](../docs/1-capital-markets/module-07-commodities.html) §4d-ஓட Commodity Option-ஐ மறுபயன்படுத்தி — ஒரு hedge பயனுள்ளதாக இருக்க hedge பண்ணப்படும் பொருளையே *சரியா* reference பண்ண தேவையில்லைங்கறதுக்கு curriculum-ஓட மிகத் தெளிவான விளக்கம், ஆனா அந்த குறுக்குவழிக்கு ஒரு உண்மையான செலவு: **basis risk**.

```text
COMMODITY HEDGE WITH A MISMATCHED UNDERLYING — JET FUEL VIA WTI
========================================================================

  THE ACTUAL EXPOSURE: an airline (Bank Of Toronto's client) is
  exposed to JET FUEL prices rising -- its real operating cost.

  THE HEDGE INSTRUMENT: a WTI Crude Oil Call option (Module 7 §4d)
    Strike USD 80.00/bbl | Notional 100,000 bbl | Premium USD 3.20/bbl
    Premium Paid = USD 320,000

  WHY WTI, NOT JET FUEL? There's no sufficiently liquid, exchange-
  listed jet fuel derivative for the airline to use directly -- WTI
  crude is used as a PROXY, because jet fuel and crude oil prices are
  highly correlated (crude is a major input cost to refining jet
  fuel) but NOT identical.

  THE GAP -- BASIS RISK:
    If WTI rises 10% AND jet fuel also rises roughly 10%, the WTI
    hedge offsets the airline's higher fuel cost well.

    If WTI rises 10% but jet fuel rises only 4% (or falls, e.g. a
    refinery-capacity shock specific to jet fuel, unrelated to crude)
    -- the WTI Call still pays off as if fuel costs rose 10%, but the
    airline's ACTUAL cost only rose 4%. The hedge OVER-corrects.

    Equally, if jet fuel rises MORE than WTI (a widening "crack
    spread" -- the refining margin between crude and refined
    products), the hedge UNDER-corrects, and the airline is still
    left exposed to part of the increase.
```

**Takeaway:** இதை Section 5-ஓட CDS உதாரணத்தோட நேரடியா ஒப்பிடுங்க, அங்க hedge instrument exposure-ஓட *சரியா அதே* reference entity-ஐ reference பண்ணுது — இங்க, hedge instrument (WTI), exposure (jet fuel) **தொடர்புடைய ஆனா தனி** risk factors, அதனால hedge-க்குப் பிறகும் சில risk எப்பவும் மிஞ்சுது. Basis risk ஒரு தவறோ hedge-ஓட குறையோ இல்ல — இல்லாம இருக்கலாம் அல்லது மிகவும் அதிகம் செலவாகலாம்ங்கற bespoke ஒண்ணுக்குப் பதிலா கிடைக்கற மிக liquid instrument-ஆல hedge பண்றதோட தவிர்க்க முடியாத செலவு. [Module 7](../docs/1-capital-markets/module-07-commodities.html) §4d-ஓட சொந்த சட்டகம் ("WTI-ஐ proxy hedge-ஆ பயன்படுத்தறது") இந்த சரியான trade-off-ஐ பெயரிடுது.

---

## 8. Worked Example — Equity Hedging (Protective Put)

**Definition:** [Module 4](../docs/1-capital-markets/module-04-equity.html) §4a ஏற்கனவே நிறுவிய ஒரு position-ஐ அடிப்படையா வெச்ச ஒரு புது worked example — Maple Leaf Pension Fund-ஓட 10,000-share Coca-Cola (KO) holding, USD 68.40-ல வாங்கியது — asset-class தொகுப்பை நிறைவு செய்ய, Section 6 ஏற்கனவே FX-க்கு காட்டிய அதே asymmetric-hedge வடிவத்தோட.

```text
PROTECTIVE PUT — MAPLE LEAF PENSION FUND HEDGES ITS KO HOLDING
========================================================================
(illustrative strike/premium; the 10,000-share KO position itself is
Module 4 §4a's real worked trade)

  EXISTING POSITION: 10,000 shares KO, bought @ USD 68.40
                      (ISIN US1912161007, Module 4 §4a)

  Ahead of an uncertain earnings announcement, Maple Leaf Pension
  Fund BUYS a protective put instead of selling the shares outright
  -- it wants to keep the position (and any dividend/upside) but cap
  its downside:

  Trade: KO Put, Strike USD 65.00, Notional 10,000 shares, Expiry
         1 month
  Premium: USD 1.20/share
  Premium Paid = 10,000 x 1.20 = USD 12,000

  AT EXPIRY -- Scenario A: KO falls to USD 58.00 (bad earnings)
    Put is in-the-money: EXERCISE, sell at strike 65.00 instead of
    market 58.00 -- avoided loss = (65.00 - 58.00) x 10,000 = USD 70,000
    Net position result vs. holding unhedged: USD 70,000 avoided
    loss, minus the USD 12,000 premium already paid
    -> the STOCK can still be sold at 65.00 regardless of how far
       below that price the market has fallen -- the floor is fixed

  AT EXPIRY -- Scenario B: KO rises to USD 75.00 (good earnings)
    Put expires WORTHLESS -- selling at market (75.00) beats
    exercising at strike (65.00)
    Maple Leaf Pension Fund keeps the FULL upside on its 10,000
    shares, out only the USD 12,000 premium already paid -- exactly
    Module 6 §4d's FX Option pattern, same asymmetric shape
```

**Takeaway:** ஒரு protective put வடிவத்துல Section 6-ஓட FX Option hedge-ஓட பொருளாதார ரீதியா ஒத்தது — ஒரு premium கொடுத்து, upside-ஐ வெச்சிருந்து, downside-ஐ strike-ல வரம்புக்குள்ள வெக்கறது — ஒரு currency payable-க்குப் பதிலா ஒரு equity position-க்கு பொருத்தப்பட்டது. Section 4-ஓட futures hedge-ல இருந்து இது எப்படி வேறுபடுதுன்னும் கவனிங்க: Maple Leaf Pension Fund அதுக்குப் பதிலா தன் equity exposure-க்கு எதிரா E-mini S&P 500 futures-ஐ *விற்றிருக்கலாம்* (ஒரு symmetric hedge, premium இல்ல, ஆனா upside-ஐயும் விட்டுக்கொடுக்கும்) — symmetric-vs-asymmetric hedging-ஓட (Section 3) அதே தேர்வு equities-க்கு FX-க்கு போலவே சரியா பொருந்துது.

### கூடுதல் உதாரணம் — Protective put-ஓட payoff எண்களோட அட்டவணை

| KO expiry விலை (USD) | Put payoff (65 strike) | Shares மதிப்பு vs 68.40 cost | Put premium | நிகர (hedged) | நிகர (hedge இல்லாமல்) |
|---|---|---|---|---|---|
| 58.00 | +70,000 | -104,000 | -12,000 | -46,000 | -104,000 |
| 65.00 | 0 | -34,000 | -12,000 | -46,000 | -34,000 |
| 68.40 | 0 | 0 | -12,000 | -12,000 | 0 |
| 75.00 | 0 | +66,000 | -12,000 | +54,000 | +66,000 |

Hedge பண்ணின நிகர விளைவு 65-க்குக் கீழ எவ்வளவு விழுந்தாலும் -46,000-லயே தடுக்கப்படுது (floor); upside முழுசா (premium கழிச்சு) வெச்சிருக்கப்படுது.

---

## 9. Hedge Accounting & Regulatory பார்வை — "Hedge" ஒரு Legal/Accounting Label, வெறும் Trading Strategy இல்ல

**Definition:** இது ஒரு IT/reporting architect-க்கு மிகப் பொருத்தமான section, "இந்த trade ஒரு hedge-ஆ?"ங்கறதை P&L pattern-ஐ பார்த்தே பதில் சொல்ல முடியாததற்கு காரணமும். **Accounting standards** (சர்வதேச அளவுல IFRS 9, US-ல ASC 815), **derivatives regulation** (EMIR, Dodd-Frank) இரண்டின் கீழும், ஏற்கனவே இருக்கற risk-ஐ உண்மையிலயே குறைக்கும் ஒரு trade, சமமான ஊக trade-ஐ விட கணிசமா வேறுபட்ட சிகிச்சைக்கு தகுதி பெறலாம் — ஆனா அது inception-ல **முறையா hedge-ஆ ஆவணப்படுத்தப்பட்டிருந்தா** மட்டும், வெறுமனே மறைமுகமா புரிந்துகொள்ளப்பட்டிருந்தா இல்ல.

| Hedge நிலையின் விளைவு | என்ன மாறுது | Operational ரீதியா ஏன் முக்கியம் |
|---|---|---|
| **Hedge accounting** (IFRS 9 / ASC 815) | Hedge instrument-ஓட gain/loss, hedged item-ஓட ஈடுசெய்யும் loss/gain-ஓட *அதே accounting period*-ல அங்கீகரிக்கப்படலாம், ரெண்டும் P&L statement-ல வேறு நேரங்கள்ல தாக்காம | அது இல்லாட்டி, உண்மையிலயே risk-ஐ குறைக்கும் trade *reported* earnings-ஐ குறைவா இல்ல, அதிக ஏற்ற இறக்கமா காட்டலாம் — ஏன்னா hedge-ஓட mark-to-market நகர்வு உடனே தெரியும், hedged item-ஓட economics காலப்போக்குல விரியும் |
| **EMIR clearing exemption** ([Module 8](../docs/1-capital-markets/module-08-rates.html) §5-ஓட clearing mandate) | ஒரு உண்மையான *வணிக* risk-ஐ hedge பண்ண (நிதி-சந்தை பந்தயம் இல்ல) derivative-ஐ பயன்படுத்தும் non-financial counterparty, அந்த trade-ஐ NFC+/NFC- clearing வரம்பு சோதனைக்கு எதிரா வேறுபட்டு எண்ணலாம் | Trade கட்டாய central clearing கடமைக்கு உட்படுதா இல்லையான்னு தீர்மானிக்குது |
| **Dodd-Frank "end-user exception"** | ஒரு உண்மையான வணிக risk-ஐ hedge பண்ற US non-financial end-user, அந்த swap-க்கு Dodd-Frank-ஓட கட்டாய clearing தேவையிலிருந்து விலக்கு பெறலாம் | EMIR விலக்கோட அதே நோக்கம், வேறு jurisdiction, ஒத்த ஆவணப்படுத்தும் சுமை |

> **Callout — இது எளிமைப்படுத்தப்பட்ட விளக்கம், உண்மையான தற்போதைய விதி text இல்ல.** ஒரு trade எந்த சோதனைகளை கடக்கணும் (effectiveness testing வரம்புகள், சரியான NFC+/NFC- notional கணக்கீடு, end-user exception தகுதி அளவுகோல்கள்) என்பது technical, jurisdiction-குறிப்பானது, அவ்வப்போது திருத்தப்படுது — இந்த curriculum-ஓட மற்ற reference modules தங்கள் சொந்த regulatory specifics-க்கு ([UPI/UTI](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) §2, [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §7) ஏற்கனவே பயன்படுத்தற அதே hedge.

```text
WHY THIS MATTERS FOR A TRADE/BOOKING SYSTEM'S DATA MODEL
========================================================================

  A trade being ECONOMICALLY a hedge (its P&L happens to move
  opposite to another position) is NOT the same fact as a trade
  being FORMALLY DESIGNATED a hedge for accounting/regulatory
  purposes -- the second requires:

    -> a "Hedge Designation" flag on the trade record, set AT
       INCEPTION (not retroactively)
    -> a documented link to the SPECIFIC hedged item/exposure
       (which bond, which forecasted cash flow, which portfolio)
    -> ongoing EFFECTIVENESS TESTING data (is the hedge still
       working as designed, checked periodically)

  Without all three captured, a trade that is economically a
  perfectly good hedge (like Section 4's futures trade) still gets
  TREATED as a plain speculative position by accounting and,
  potentially, by the clearing-obligation rules -- an expensive
  gap if the documentation was simply never captured in the system.
```

**Takeaway:** "hedge" ஒரு முறைசாரா trading label மட்டுமில்ல, உண்மையான சட்ட, accounting எடையைச் சுமக்குது — நல்லா வடிவமைக்கப்பட்ட trade capture system trade-ஓட எளிய economic terms-ஓட பக்கத்துல ஒரு வெளிப்படையான hedge-designation data model-ஐ (flag, இணைக்கப்பட்ட exposure, effectiveness-test வரலாறு) தேவைப்படுத்துது; ஏன்னா ஒரே instrument ஒரு hedge ஆகவோ ஊகமாகவோ இருக்கலாம் என்பது trade record தானே ஊகிக்காம பிடிக்க வேண்டிய உண்மைகளைச் சார்ந்ததுன்னு Section 1 ஏற்கனவே நிறுவியது.

---

## 10. Hedging உண்மையில எங்க பயன்படுத்தப்படுது — Desk-by-Desk சுருக்கம்

**Definition:** இந்த module-ல ஒவ்வொரு உதாரணத்தையும் ஒரு வழக்கமான வங்கி, அதன் clients முழுக்க *யார்* எதை எந்த instruments-ஓட hedge பண்றாங்கன்னு ஒரு வரைபடமா இழுக்குது.

| Business function | Typical exposure being hedged | Typical hedge instrument(s) | This module |
|---|---|---|---|
| **Treasury / ALM** (Asset-Liability Management) | Interest rate risk on the bank's own loan/bond book | IR Swaps, IR Futures | Section 4 |
| **Corporate Treasury** (bank's client) | FX payables/receivables from international trade | FX Forwards, FX Options | Section 6 |
| **Corporate Treasury** (energy-consuming client, e.g. an airline) | Commodity input cost (fuel) | Commodity Futures/Options/Swaps | Section 7 |
| **Credit / Loan Portfolio Management** | Credit/default risk on bonds or loans held | CDS (single-name or index) | Section 5 |
| **Asset Managers / Pension Funds** | Portfolio market-value risk on an existing holding | Equity Index Futures (symmetric), Protective Puts (asymmetric) | Section 8 |
| **Dealers / Market Makers** | Risk absorbed from client option flow (a dealer who just sold a client an option now has an unwanted exposure of its own) | Delta-hedging with the underlying instrument, or offsetting options | Not separately worked here — the same offsetting logic as every other section, just run continuously as client flow arrives, rather than once per position |

**Takeaway:** ஒவ்வொரு வரியும் இந்த curriculum முற்றிலும் வேறு நோக்கங்களுக்கு (நிதியளிப்பு திரட்டுவது, சந்தைப் பார்வையை வெளிப்படுத்துவது, exposure பெறுவது) ஏற்கனவே கட்டின instruments-ஐ மறுபயன்படுத்துவதை கவனிங்க — hedging ஒரு தனி product வகை இல்ல, அதே product set-ஓட ஒரு **பயன்பாடு**; ஈடுசெய்யும் exposure இருக்கறதாலும் (Section 1), சட்டப்படி முக்கியமான இடங்கள்ல முறையான hedge ஆவணப்படுத்தலாலும் (Section 9) மட்டும் வேறுபடுத்தப்படுது.

---

## 11. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** இந்த curriculum-ஓட மற்ற reference modules போலவே, அவ்வப்போது திருத்தப்படும் technical விதிகளை மறுசொல்லாம, முதன்மை மூலங்களை சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **ISDA** | Standard derivative documentation used to execute most OTC hedges (Master Agreement, CSA) — see the [ISDA Product Taxonomy Deep Dive](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.md) | [www.isda.org](https://www.isda.org) |
| **IFRS Foundation** | IFRS 9 — international hedge accounting requirements (designation, effectiveness testing) | [www.ifrs.org](https://www.ifrs.org) |
| **FASB** | ASC 815 — US GAAP's equivalent hedge accounting standard | [www.fasb.org](https://www.fasb.org) |
| **CFTC** | Dodd-Frank's end-user exception for commercial hedgers | [www.cftc.gov](https://www.cftc.gov) |
| **ESMA** | EMIR Article 10 — the NFC+/NFC- clearing-threshold test and its hedging exemption | [www.esma.europa.eu](https://www.esma.europa.eu) |

**Takeaway:** ISDA-ஓட ஆவணங்களை ஒரு hedge உண்மையில எப்படி ஆவணப்படுத்தப்பட்டு execute ஆகுதுங்கறதுக்கு source of truth-ஆவும்; IFRS/FASB-ஐ ஒரு hedge-ஓட P&L நிதி அறிக்கைகள்ல எப்படி அங்கீகரிக்கப்படுதுங்கறதுக்கு source of truth-ஆவும்; CFTC/ESMA-ஐ ஒரு hedging நோக்கம் ஒரு trade-ஓட regulatory clearing கடமையை எப்போ மாற்றுதுங்கறதுக்கு source of truth-ஆவும் நடத்துங்க — இந்த curriculum-ஓட மற்ற reference modules தங்கள் சொந்த பகுதிகளுக்கு ஏற்கனவே பயன்படுத்தற அதே மூன்று-வழிப் பிரிவு (ஆவணப்படுத்தும் standard, accounting கட்டளை, regulatory கட்டளை).

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | தன் bond book-ஐ T-Note futures-ஆல hedge பண்ணுது (Section 4); Northern Rail Corp மேல CDS protection வாங்குது (Section 5); ஒரு client-ஓட FX payable-ஐயும் ஒரு client-ஓட jet fuel செலவையும் dealer counterparty-ஆ hedge பண்ணுது (Sections 6–7) |
| Goldman Sachs | Northern Rail Corp CDS-ல protection seller (Section 5); FX Forward-ல counterparty (Section 6) |
| Northern Rail Corp | CDS hedge-ஓட reference entity (Section 5) |
| Maple Leaf Pension Fund | E-mini S&P 500 future-ஐ ஊகமா வாங்குது (Section 1-ஓட ஒப்பீடு); தன் existing KO equity position-ஐ ஒரு protective put-ஆல hedge பண்ணுது (Section 8) |
| Bank Of Toronto's client (FX) | எதிர்கால USD payable-ஐ ரெண்டு வழிகள்ல hedge பண்ணுது — Forward, Option (Section 6) |
| Bank Of Toronto's client (airline) | WTI proxy hedge வழியா jet fuel செலவு exposure-ஐ hedge பண்ணுது, basis risk-ஐ ஏற்று (Section 7) |
| ISDA / IFRS Foundation / FASB / CFTC / ESMA | ஒரு hedge எப்படி execute ஆகுது, அங்கீகரிக்கப்படுது, (தேவைப்படும் இடத்துல) clearing-ல இருந்து விலக்கு பெறுதுங்கறதை நிர்வகிக்கும் ஆவணப்படுத்தல், accounting/regulatory standard-setters (Sections 9, 11) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ரெண்டும் அதே instrument வகையை பயன்படுத்தினாலும், ஒரு hedge-ஐ ஒரு ஊக trade-ல இருந்து வேறுபடுத்தும் ஒற்றை உண்மை என்ன?
2.  Section 2-ஐ பயன்படுத்தி, நல்லா hedge பண்ணின position-ஓட கூட்டு P&L-ஐ "risk-free"ன்னு விவரிக்கறது தவறு ஏன்?
3.  Section 3-ஐ பயன்படுத்தி, ஒரு Option-க்குப் பதிலா ஒரு symmetric hedge (Forward) தேர்ந்தெடுக்கறதால ஒரு hedger எதை விட்டுக்கொடுக்கறார்? Option-ஐ தேர்ந்தெடுக்கறதால எதை விட்டுக்கொடுக்கறார்?
4.  Section 4-ஐ பயன்படுத்தி, notional தொகைகளை பொருத்தறது (bond market value-ஐ futures notional-உடன்) ஒரு interest rate hedge-ஐ அளவிடறதுக்கு தவறான வழி ஏன்? அதுக்குப் பதிலா எதை பொருத்தணும்?
5.  Section 5-ஐ பயன்படுத்தி, CDS-on-Northern-Rail-Corp hedge ஒரு "சரியான" reference-entity பொருத்தத்துக்கு நெருக்கம்னு விவரிக்கப்படுறது ஏன்? அப்படி இருந்தும் எந்த risk இன்னும் இருக்கு?
6.  Section 7-ஐ பயன்படுத்தி, basis risk என்றா என்ன? airline-ஓட WTI-சார்ந்த jet fuel hedge அதை சுமக்குது, ஆனா Section 5-ஓட CDS hedge (பெரும்பாலும்) சுமக்கலை ஏன்?
7.  Section 8-ஐ பயன்படுத்தி, KO protective put-ஓட payoff வடிவம் Section 6-ஓட FX Option hedge-ஓட எந்த குறிப்பிட்ட வகையில ஒத்தது?
8.  Section 9-ஐ பயன்படுத்தி, ஒரு trade பொருளாதார ரீதியா நல்ல hedge-ஆ இருந்தும் hedge accounting-ஐயோ clearing விலக்கையோ பெறத் தவறுவது ஏன்?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Trade, trader ஏற்கனவே வெச்சிருக்கற exposure-ஐ ஈடுசெய்யுதான்னு. ஒரு hedge, trader ஏற்கனவே வெச்சிருக்கற position (அல்லது தெரிஞ்ச எதிர்கால தேவை) மேல விளைவுகளின் வரம்பை குறைக்க இருக்கு; speculation எதுவும் ஈடுசெய்யப்படாத புத்தம்புது திசைசார்ந்த பந்தயம். Instrument தானே (ஒரு future, ஒரு option, ஒரு swap) இது எதுன்னு ஒருபோதும் வெளிப்படுத்தாது.**

Section 1-ஓட பக்கம்-பக்கம் உதாரணம் இதை வெளிப்படையாக்குது: Bank Of Toronto-ஓட short T-Note futures (அது ஏற்கனவே வெச்சிருக்கற bonds-க்கு எதிரா), Maple Leaf Pension Fund-ஓட long E-mini futures (ஈடுசெய்யும் position இல்லாம) ஒரே contract வகை, எதிர் நோக்கங்கள்.

**2. ஏன்னா கிட்டத்தட்ட எப்பவும் சில மிச்ச நகர்வு இருக்கும் — basis risk (Section 7), அபூரண hedge-ratio அளவீடு (Section 4), அல்லது hedge மேலயே counterparty/wrong-way risk (Section 5) எல்லாம், கூட்டு position-ஓட P&L ஒவ்வொரு leg-ஐ விடவும் தட்டையானது, முழுசா தட்டையானது இல்லைன்னு அர்த்தப்படுத்தும். Hedging விளைவுகளின் வரம்பை குறுக்குது; அதை அழிக்காது.**

Section 2-ஓட takeaway இதை நேரடியா சொல்லுது — "hedging ஒருபோதும் இலவசம் இல்ல", கூட்டு P&L diagram ஒரு தட்டையான கோட்டை இல்ல, இன்னும் தட்டையான கோட்டைக் காட்டுது.

**3. Forward-ஐ தேர்ந்தெடுக்கறது சாதகமான rate/விலை நகர்வின் பலனைப் பெறும் எந்த வாய்ப்பையும் விட்டுக்கொடுக்குது (எப்படியும் lock, premium இல்ல). Option-ஐ தேர்ந்தெடுக்கறது அந்த upside-ஐ வெச்சிருக்க ஒரு முன்கூட்டிய premium-ஐ (விளைவு எதுவா இருந்தாலும் கொடுக்கப்படும்) செலவழிக்குது.**

Section 3-ஓட table-உம் Section 6-ஓட worked forward-vs-option ஒப்பீடும் இந்த trade-off-ஐ நேரடியா சொல்லுது, ரெண்டு பக்கமும் உண்மையான எண்களோட (Forward பக்கம் CAD 155,000 விட்டுக்கொடுத்த upside vs. Option பக்கம் CAD 90,000 premium).

**4. ஏன்னா ஒரு bond-க்கும் ஒரு bond future-க்கும் கொடுக்கப்பட்ட rate நகர்வுக்கு ஒரே விலை உணர்திறன் இல்ல — face values-ஐ பொருத்தறது ஒரு hedge இல்ல, ஒரு தற்செயல். சரியான அணுகுமுறை risk sensitivities-ஐ (DV01) பொருத்துது, அதனால ஒரு rate நகர்வு bond position-லயும் futures position-லயும் கிட்டத்தட்ட சமமான, எதிரான dollar நகர்வுகளை உருவாக்குது.**

Section 4-ஓட hedge-ratio formula இதை நேரடியா சொல்லுது: தேவையான contracts ≈ exposure-ஓட DV01 ÷ future-ஓட ஒரு contract-க்கு DV01, Module 1 §8-ஓட "notional ஒரு reference எண், நகர்றது இல்ல"ங்கறதை குறிப்பா hedge அளவீட்டுக்கு பொருத்தி மறுசொல்லுது.

**5. ஏன்னா CDS, hedge பண்ணப்படும் bond-ஓட சரியா அதே reference entity-ஐ (Northern Rail Corp) reference பண்ணுது, ஒரு தோராயம் அல்லது proxy இல்ல — Section 7-ஓட WTI/jet fuel பொருத்தமின்மை போல இல்ல. அப்படியும் அது wrong-way risk-ஐ சுமக்குது: protection seller (Goldman Sachs) சரியா Northern Rail Corp தோற்கற அதே நேரத்துல தோற்கும் வாய்ப்பு அதிகம்னா, hedge மிக அதிகம் தேவைப்படற நேரத்துலயே மிகக் குறைவா நம்பகமானது.**

Section 5-ஓட takeaway இதை நேரடியா சொல்லுது, Module 2 §9-ஓட சொந்த wrong-way-risk flag-ஐ cross-reference பண்ணி.

**6. Basis risk = hedge instrument-ஓட விலை இயக்கி உண்மையான exposure-ஓட விலை இயக்கிக்கு தொடர்புடையது, ஆனா ஒரே மாதிரி இல்லாததால hedge-க்குப் பிறகும் மிஞ்சும் exposure — இங்க WTI crude oil vs. jet fuel, அவை சேர்ந்து நகருது ஆனா சரியா இல்ல ("crack spread" அகலலாம், குறுகலாம்). CDS hedge பெரும்பாலும் இதைத் தவிர்க்குது, ஏன்னா அது ஒரு தொடர்புள்ள proxy-ஐ இல்ல, சரியா அதே reference entity-ஐ reference பண்ணுது.**

Section 7-ஓட worked example நல்ல-தொடர்பு, பொருந்தாத-தொடர்பு ரெண்டு scenarios-ஐயும் வெளிப்படையா நடத்திக் காட்டுது, அதன் takeaway Section 5-ஓட சரியான-பொருத்த CDS case-உடன் நேரடி வேறுபாட்டை வரையுது.

**7. ரெண்டும் market பாதகமா நகர்ந்தா ஒரு வரம்புக்குள்ள, தெரிஞ்ச அதிகபட்ச இழப்புக்கு ஈடா முன்கூட்டியே ஒரு premium-ஐ (FX Call-க்கு CAD 90,000, KO Put-க்கு USD 12,000) செலுத்துது, சந்தை சாதகமா நகர்ந்தா முழு பலனையும் வெச்சிருக்குது — option வெறுமனே பயனற்று expire ஆகுது, premium மட்டும் ஒரே செலவு.**

Section 8-ஓட takeaway இதை வெளிப்படையா சொல்லுது: FX Option hedge-க்கு "வடிவத்துல பொருளாதார ரீதியா ஒத்தது", ஒரு currency payable-க்குப் பதிலா ஒரு equity position-க்கு பொருத்தப்பட்டது.

**8. ஏன்னா hedge accounting-உம் clearing விலக்குகளும் trade inception-ல பிடிக்கப்பட்ட **முறையான ஆவணப்படுத்தலை** கோருது — ஒரு hedge designation, குறிப்பிட்ட hedged item-க்கு ஆவணப்படுத்தப்பட்ட இணைப்பு, effectiveness-testing data — trade-ஓட P&L ஏதோ ஒரு மற்ற position-க்கு எதிரா நகருதுங்கறது மட்டுமில்ல. அந்த ஆவணப்படுத்தலை ஒருபோதும் பிடிக்காத system, பொருளாதார ரீதியா சரியான hedge-ஐ accounting, regulatory நோக்கங்களுக்கு ஒரு எளிய ஊக trade-ஆ நடத்த விட்டுடும்.**

Section 9-ஓட diagram இதை நேரடியா சொல்லுது: பொருளாதார hedging-உம் முறையான hedge designation-உம் ரெண்டு வேறு உண்மைகள், ரெண்டாவது மட்டுமே accounting/regulatory சிகிச்சையை மாற்றுது.
