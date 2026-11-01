# Module 8 — Rates (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Asset Class Roadmap 5 of 6** — Equity → Fixed Income → FX → Commodities → **Rates** → Credit.
> **Work data model:** இந்த module taxonomy-ஓட **"2. Interest Rate"** node-க்கு map ஆகுது — ஐந்து products, ஒவ்வொண்ணுக்கும் தனி Trade + Valuation Avro schema.
> **மூலம்:** English Module 8-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Rates-ன்னா என்ன? — Theory & Terminology

**Definition:** Rates = cash flows ஒரு company-ஓட வருமானத்தை (Equity), ஒரு currency-ஓட மதிப்பை (FX), ஒரு physical பொருளின் விலையை (Commodities) சார்ந்திருக்காம, ஒரு **வட்டி விகிதத்தை** சார்ந்திருக்கற contracts-ஓட asset class. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8 ஏற்கனவே அடிப்படை கட்டுமானத் தொகுதியான Interest Rate Swap-ஐ அறிமுகப்படுத்திச்சு — fixed-rate cash flow stream-ஐ floating-rate ஒண்ணுக்கு மாத்திக்கற ஒரு bilateral ஒப்பந்தம், **Notional**-ஐ reference பண்ணும், அது தானே ஒருபோதும் settle ஆகாது. இந்த module முழு குடும்பத்தையும் கட்டுது: "notional ஒருபோதும் settle ஆகாது, net வித்தியாசம் மட்டுமே" ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) கிட்டத்தட்ட ஒவ்வொரு product-க்கும் விதி, விதிவிலக்கு இல்ல.

| Term | அர்த்தம் |
|---|---|
| Fixed Leg | Trade date-ல ஒத்துக்கிட்ட rate-ஐ, trade முழுக்க மாறாம கொடுக்கற swap-ஓட பக்கம் |
| Floating Leg | வெளியிடப்பட்ட reference rate-க்கு எதிரா அவ்வப்போது reset ஆகற rate-ஐ கொடுக்கற பக்கம் |
| Reference Rate | Floating leg reset ஆகற வெளியிடப்பட்ட benchmark — உதா., **CORRA** (Canada), **SOFR** (US), **€STR** (EU), **SONIA** (UK) |
| Payment Dates | Cash flows கணக்கிடப்பட்டு பரிமாறப்படற திட்டமிட்ட தேதிகள் — காலாண்டு மிகப் பொதுவான வழக்கம் |
| DV01 | "Dollar Value of 01" — rates 1 basis point (0.01%) நகர்ந்தா ஒரு Rates position-ஓட மதிப்பு எவ்வளவு மாறும்; [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1-ல இருந்த bond Duration-ஓட Rates-குறிப்பான உறவு, [Module 10](../docs/1-capital-markets/module-10-valuation.html) (Valuation)-ல முழுசா. Basis point அலகு தானே, எல்லா asset classes முழுக்க, [Module 24 — Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) |

### LIBOR-ல இருந்து RFR-க்கு மாற்றம் — ஏன் "CDOR" அல்லது "LIBOR" இல்லாம "CORRA"

**IT/reporting பார்வை:** 2020-களின் தொடக்கம் வரை, இந்த asset class-ல கிட்டத்தட்ட ஒவ்வொரு floating leg-உம் **LIBOR** (London Interbank Offered Rate) அல்லது ஒரு உள்ளூர் சமமானதை reference பண்ணும் — Canada-ல அந்த உள்ளூர் சமமானது **CDOR** (Canadian Dollar Offered Rate), banks panel தினமும் சமர்ப்பிக்கற ஒரு survey-சார்ந்த rate. LIBOR-உம் CDOR-உம் ஒரே மரணகரமான குறையை பகிர்ந்தன: அவை உண்மையில நடந்த transactions-ஐ அடிப்படையா வெச்சில்ல, ஒரு வங்கி தான் எதுக்கு கடன் வாங்க *முடியும்* என்று நம்பற interbank *மதிப்பீடுகள்*-ஐ வெச்சிருந்தன — ரெண்டுமே manipulate செய்யக்கூடியதுன்னு நிரூபிச்சது (2010-களின் தொடக்க LIBOR-rigging scandals). LIBOR-ஓட சொந்த phase-out-க்கு சில வருஷங்களுக்கு அப்புறம், CDOR **June 2024-க்கு அப்புறம் நிறுத்தப்பட்டது**.

**CORRA உண்மையில எதை அளக்குது.** CORRA (Canadian Overnight Repo Rate Average) CDOR-க்குப் பதிலா Canada-ஓட benchmark ஆச்சு. CDOR போல இல்லாம, CORRA ஒரு கருத்துக் கணிப்பு இல்ல — **Bank of Canada**, ஒவ்வொரு business day-யும், முந்தைய நாளோட உண்மையான **overnight repo transactions**-ல இருந்து ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3-ஓட repo mechanic), **Government of Canada securities** collateral-ஆ இருக்கற அவற்றில் இருந்து நேரடியா கணக்கிடுது. ஒவ்வொரு input-உம் ஒரு வங்கி சொந்த மதிப்பீடா இல்லாம, ஒரு observable rate-ல execute ஆன உண்மையான trade என்பதால, CORRA-வை manipulate பண்றது அமைப்பு ரீதியா மிகக் கடினம் — SOFR (US), €STR (EU), SONIA (UK) எல்லாம் அதே நேரத்துல தங்கள் உள்ளூர் IBOR-பாணி முன்னோடிகளை மாற்றினதுக்குப் பின்னாலுள்ள அதே காரணம். இந்த module முழுக்க CORRA-வையே பயன்படுத்துது, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ஓட அசல் worked example-உடன் ஒத்துப்போக (அது CORRA ஒரு உதாரணமா இருக்கும் பொது **Underlier** கருத்தையும் அறிமுகப்படுத்தும்) — ஆனா ~2022-க்கு முன்னாடி கட்டின எந்த reporting system-லயும் இன்னும் hardcode ஆன "LIBOR" அல்லது "CDOR" reference-rate field இருந்தா, அது இந்த மாற்றத்தின் legacy debt-ஐ சுமக்குது — தொழில்துறை இதுவரை நடத்தின மிகப்பெரிய ஒருங்கிணைந்த data-migration நிகழ்வுகளில் ஒண்ணு.

**Takeaway:** இந்த module-ல ஒவ்வொரு product-உம் ஒரே கருப்பொருளின் மாறுபாடு — fixed vs. floating, அல்லது fixed vs. floating-ல நுழையும் *option* — Notional ஒரு தூய கணக்கீட்டு input. இந்த ஒரு வாக்கியத்தை மனசுல வெச்சிருந்தா, Section 4-ல உள்ள ஒவ்வொரு worked example-ஐயும் சரியா யோசிக்க போதும்.

---

## 2. Products என்னென்ன? — Cap/Floor, IR Forward Debt, FRA, IRS & Swaption

```text
INTEREST RATE
│
├── Interest Rate Swap (IRS)   (exchange fixed vs floating cash flows, periodically, for a term)
├── FRA (Forward Rate Agreement) (a single-period IRS, cash-settled once, upfront)
├── Cap / Floor                (a strip of options on a floating rate, one per period)
├── Swaption                   (an option to ENTER an IRS at a future date)
└── IR Forward Debt            (a forward purchase/sale of a fixed income security)
```

| Product | அது என்ன |
|---|---|
| **Interest Rate Swap (IRS)** | ரெண்டு counterparties, ஒரே Notional மேல, பல வருஷ காலத்துக்கு, fixed-rate cash flow stream-ஐ floating-rate ஒண்ணுக்கு மாத்திக்கறாங்க — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ஓட worked example |
| **FRA (Forward Rate Agreement)** | பொருளாதார ரீதியா, IRS-ஓட *ஒரு period*-ஐ தனிமைப்படுத்தி: இன்னிக்கு lock பண்ணின ஒரு fixed rate, எதிர்கால ஒரு floating rate reset-க்கு எதிரா, ஒரு தடவை cash-settle — ஆனா period-ஓட முடிவுல இல்ல, **ஆரம்பத்துலயே**, discount பண்ணி settle ஆகும் (Section 4b இதை உறுதியா காட்டும்) |
| **Cap / Floor** | தனித்தனி options-ஓட ஒரு strip ("Cap" = caplets strip, "Floor" = floorlets strip); ஒவ்வொண்ணும் அந்த reset-ல floating reference rate strike rate-ஐ விட மேலே (Cap) அல்லது கீழே (Floor) இருந்தா மட்டும் payout — buyer ஒருபோதும் முன்கூட்டிய premium-ஐ விட அதிகம் கொடுக்கறதில்ல, [Module 4](../docs/1-capital-markets/module-04-equity.html) §4d, [Module 6](../docs/1-capital-markets/module-06-fx.html) §4d, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §4d-ல இருந்த அதே சமச்சீரற்ற payoff pattern |
| **Swaption** | எதிர்கால ஒரு தேதில ஒரு IRS-ல fixed-rate payer ("Payer Swaption") அல்லது receiver ("Receiver Swaption") ஆ நுழையும் option — ஒரு swap-ஓட *மேல்* option, swap தானே இல்ல |
| **IR Forward Debt** | எதிர்கால தேதில, இன்னிக்கே fix பண்ணின விலை/yield-ல ஒரு fixed income security-ஐ ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html)) வாங்க/விற்க forward contract — [Module 6](../docs/1-capital-markets/module-06-fx.html) §2-ஓட FX Forward-ஓட Rates-asset-class பதிப்பு, ஒரு currency pair-க்குப் பதிலா ஒரு bond-ஐ reference பண்ணுது |

**Takeaway:** இங்க உள்ள உள் அமைப்பைக் கவனிங்க — IRS அடிப்படை; FRA அதன் ஒரு துண்டு, வேற மாதிரி settle ஆகுது; Cap/Floor IRS reference பண்ற அதே floating rate மேல options-ஓட strip; Swaption IRS மேலேயே option; IR Forward Debt வித்தியாசமானது, rate-ஐ நேரடியா இல்லாம bond-ஓட விலையை reference பண்ணுது. ஐந்துல நாலு products [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ஓட IRS mental model-ஓட மேலயே நேரடியா கட்டப்பட்டவை — IR Forward Debt மட்டும்தான் அதுக்கு வெளியே போகுது.

---

## 3. உண்மையில எது Trade ஆகுது? — Rate-ஐ Reference பண்ற Contracts, Rate தானே இல்ல

**Definition:** FX மாதிரி ([Module 6](../docs/1-capital-markets/module-06-fx.html) §3), Rates-ல ஐந்துல நாலு products-க்கு "security" சமமானது இல்ல — IRS, FRA, Cap/Floor, Swaption எல்லாமே தூய contracts, execution-ல உருவாகி, net cash தவிர வேற எதையும் deliver பண்ணாம settle/expire ஆகுது. IR Forward Debt விதிவிலக்கு: இன்னிக்கு ஒரு contract, cash-settle ஆகலைன்னா, அப்புறம் ஒரு உண்மையான bond (ஒரு உண்மையான security, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3) கை மாறும்.

```text
FOUR PURE CONTRACTS  vs  ONE CONTRACT-ON-A-SECURITY
========================================================================

  Interest Rate Swap / FRA / Cap-Floor / Swaption (PURE CONTRACTS):
    None of these reference an existing security. An IRS's Notional
    (Module 1 §8) is a calculation input, never delivered. A Cap's
    strike is compared against a published rate, not a traded
    instrument. Nothing here has an ISIN (Section 6).

  IR Forward Debt (CONTRACT ON A SECURITY):
    Bank Of Toronto agrees TODAY to buy CAD 10,000,000 face value
    of a Government of Canada bond in 3 months, at a forward price
    fixed today. Unless it's cash-settled, in 3 months an ACTUAL
    bond (Module 5 §3's real, transferable security) moves via DvP
    -- the contract's underlying is real and has its own ISIN, even
    though the FORWARD itself is a contract, not the security.

  -> This is the Rates asset class's version of Module 6 §3's
     "contracts all the way down" finding -- except one of the five
     products isn't quite all the way down; it bottoms out in an
     actual bond.
```

**Takeaway:** IR Forward Debt தான், Modules 5, 6, 7 எதுக்கும் தேவைப்படாத கூடுதல் வரி Section 6-ஓட identifier table-க்கு தேவைப்படுறதுக்கு காரணம் — ஒரே trade record, forward contract-க்கு ஒரு UPI/UTI-யையும், அது இறுதியில deliver பண்ணக்கூடிய bond-க்கு ஒரு ISIN-ஐயும் சுமக்கலாம், ரெண்டும் ஒரே நேரத்துல சரி.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked examples இருக்கு.** இந்த module-ஓட products ஒரு இணைக்கப்பட்ட பாடச் சங்கிலியை உருவாக்குது, ஒவ்வொண்ணுக்கும் தன் சொந்த முழு standalone example: [Interest Rate Swap (IRS)](../examples/Interest_Rate_Swap_IRS_Complete_Example.html) → [FRA](../examples/FRA_Forward_Rate_Agreement_Complete_Example.html) → [Cap](../examples/Interest_Rate_Cap_Complete_Example.html) → [Floor](../examples/Interest_Rate_Floor_Complete_Example.html) → [Collar](../examples/Interest_Rate_Collar_Complete_Example.html) → [Swaption](../examples/Swaption_Complete_Example.html) → [Cross-Currency Swap](../examples/Cross_Currency_Swap_Complete_Example.html).

### 4a. Interest Rate Swap

[Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ஓட worked example-ஐ அப்படியே மறுபயன்படுத்தி:

```text
INTEREST RATE SWAP  —  5Y CAD IRS
========================================================================

  Bank Of Toronto:  pays FIXED, receives FLOATING (CORRA + spread)
  Goldman Sachs:     pays FLOATING, receives FIXED

  Notional (reference only, NEVER exchanged): CAD 100,000,000
  Term: 5 years, quarterly payments
  Fixed Rate: 4.00%

  ONE QUARTER'S SETTLEMENT — 91-day quarter, actual/365, CORRA
  averages 3.20% over the quarter + 0.05% spread = 3.25% floating

  Bank Of Toronto's FIXED leg = 100,000,000 x 4.00% x 91/365 = CAD 997,260.27
  Goldman Sachs' FLOATING leg = 100,000,000 x 3.25% x 91/365 = CAD 810,273.97

  Net cash flow: Bank Of Toronto PAYS Goldman Sachs
    997,260.27 - 810,273.97 = CAD 186,986.30
  -> only this NET amount moves; the CAD 100,000,000 notional never
     settles at all, exactly Module 1 §8's original point.
```

> **Deep dive இருக்கு:** இந்த section இந்த trade-ஓட ஒரு quarter-ஐ மட்டும் பார்க்குது. முழுப் படம் — இதே 5Y CAD IRS 20 quarterly settlements எல்லாத்தையும் கடக்கறது, floating leg-ஓட CORRA compounding எப்படி வேலை செய்யுது, trade-ஓட வாழ்நாள்ல ஒவ்வொரு payment date, maturity-ல final settlement, சம்பந்தப்பட்ட Trade/Valuation fields — தனி [Interest Rate Swap (IRS) Deep Dive](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.html) பாருங்க.

### 4b. FRA (Forward Rate Agreement)

```text
FRA  —  3x6 FRA (fixing in 3 months, covering a 3-month period)
========================================================================

  Bank Of Toronto: BUYS the FRA — pays FIXED (the "FRA Rate"),
                    receives FLOATING (3-month CORRA)
  Counterparty: Goldman Sachs

  Notional: CAD 20,000,000
  FRA Rate (locked today): 4.85%
  Reference period: 91 days, actual/365

  AT FIXING (3 months from trade date) — 3-month CORRA settles at 4.50%
  (rates fell since the FRA was agreed)

  Undiscounted difference = 20,000,000 x (4.85% - 4.50%) x 91/365
                           = 20,000,000 x 0.0035 x 0.249315
                           = CAD 17,452.05

  Discounted settlement (paid NOW, at the START of the reference
  period, not its end):
    = 17,452.05 / (1 + 4.50% x 91/365)
    = 17,452.05 / 1.011219
    = CAD 17,259.79

  Bank Of Toronto PAYS Goldman Sachs CAD 17,259.79 -- because Bank
  Of Toronto locked in 4.85% fixed but the market floating rate
  settled lower (4.50%), Bank Of Toronto's FRA turned out to be a
  worse deal than just floating, so it compensates the counterparty.
```

**IT/reporting பார்வை:** IRS logic மட்டும் கட்டிய ஒருத்தர், FRA valuation system-ல தவறா code பண்ண அதிக வாய்ப்புள்ள ஒற்றை field இந்த discounting step: IRS-ஓட periodic cash flow அதன் period-ஓட *முடிவுல* settle ஆகும்; FRA-ஓட ஒற்றை cash flow அதன் period-ஓட *ஆரம்பத்துல* settle ஆகும் (period முடியுற நேரத்துக்கு rate ஏற்கனவே தெரிஞ்சிருக்குமே, காத்திருக்க காரணமில்ல) — அதனால அந்த ஆரம்பத் தேதிக்கு, இப்போதான் fix ஆன அதே rate-ஐ வெச்சு present-value பண்ணப்படணும்.

### 4c. Cap/Floor

```text
INTEREST RATE CAP  —  2Y Cap on 3-month CORRA
========================================================================

  Buyer: Bank Of Toronto
  Seller (writer): Goldman Sachs

  Notional: CAD 15,000,000, Strike (Cap Rate): 5.00%
  8 quarterly caplets over 2 years
  Premium: 0.85% of notional, paid upfront = CAD 127,500

  CAPLET #3 RESET — 3-month CORRA settles at 5.40% (above strike)
    Payout = 15,000,000 x (5.40% - 5.00%) x 91/365
           = 15,000,000 x 0.004 x 0.249315
           = CAD 1,495.89 -- paid to Bank Of Toronto

  CAPLET #4 RESET — 3-month CORRA settles at 4.70% (below strike)
    Payout = CAD 0 -- this caplet simply doesn't pay; Bank Of
    Toronto's downside was already capped at the premium paid,
    exactly the asymmetric-payoff pattern from every Option product
    in this roadmap so far (Module 4 §4d, Module 6 §4d, Module 7 §4d)
```

### 4d. Swaption

```text
SWAPTION  —  1Y-into-5Y Payer Swaption
========================================================================

  Buyer: Bank Of Toronto (buys the RIGHT to enter a 5Y IRS, as
         fixed-rate PAYER, in 1 year)
  Seller (writer): Goldman Sachs

  Notional: CAD 50,000,000, Strike: 4.25% fixed
  Expiry: 1 year, Underlying Swap Term: 5 years from expiry
  Premium: 0.60% of notional, paid upfront = CAD 300,000

  AT EXPIRY (1 year) -- Scenario A: prevailing 5Y swap rate = 4.75%
    Exercise: Bank Of Toronto enters the 5Y IRS paying only 4.25%
    fixed, when the market rate is 4.75% -- a favourable swap it
    could not otherwise get. In-the-money by construction.

  AT EXPIRY -- Scenario B: prevailing 5Y swap rate = 3.90%
    Don't exercise: entering a FRESH swap in the market at 3.90%
    fixed beats exercising the swaption at 4.25%. Swaption expires
    WORTHLESS; Bank Of Toronto's loss is capped at the CAD 300,000
    premium -- never more, same maximum-loss pattern as every other
    option product in this roadmap.
```

### 4e. IR Forward Debt

```text
IR FORWARD DEBT  —  Forward purchase of a Government of Canada bond
========================================================================

  Buyer: Bank Of Toronto (agrees TODAY to buy in 3 months)
  Seller: Goldman Sachs

  Underlying: Canada 5% Bond 2035 (ISIN CA1358XXXXX — Module 5 §4a)
  Face Value: CAD 10,000,000
  Forward Price (fixed today): 97.80 (reflecting today's spot price
    of 98.50 minus 3 months of carry -- coupon income the SELLER
    keeps for holding the bond those 3 months, a mechanic closer to
    Module 6 §1's forward points than to Module 7 §1's contango)

  IN 3 MONTHS, if physically settled:
    Bank Of Toronto pays 10,000,000 x 97.80 / 100 = CAD 9,780,000
    Bank Of Toronto receives CAD 10,000,000 face value of the bond
    via DvP (Module 1 §7) -- an ACTUAL security moves, unlike 4a-4d
```

**Takeaway:** ஐந்து products, ஒரே தொடர் mechanic — இன்னிக்கே lock பண்ணின fixed rate (அல்லது 4e-க்கு fixed விலை), பின்னாடி reset ஆகற அல்லது observe ஆகற ஒண்ணுக்கு எதிரா, வித்தியாசம் மட்டும் (அல்லது 4e-ல physically settle ஆனா முழு principal) உண்மையில நகரும். FRA-ஓட முன்கூட்டிய discounting (4b), Forward Debt-ஓட physical bond delivery (4e) — "vanilla IRS போல settle ஆகும்" என்பதற்கு ரெண்டு உண்மையான விதிவிலக்குகள், ரெண்டுமே reporting system இயந்திரங்களை தவறா பிடிக்கற அதிக வாய்ப்புள்ள இடங்கள்.

### கூடுதல் உதாரணம் — அதே CORRA நகர்வு, ஐந்து products-ஐ எப்படி பாதிக்குது? (CORRA 4.50%-ல இருந்து 5.00%-க்கு உயர்ந்தா)

| Product | Bank Of Toronto position | CORRA உயர்ந்தா Bank Of Toronto-க்கு என்ன? |
|---|---|---|
| IRS | Fixed pay / floating receive | நன்மை — floating leg அதிகம் கிடைக்கும் |
| FRA (buyer) | Fixed pay (4.85%) / floating receive | நன்மை — settle-ல floating அதிகம் |
| Cap (buyer) | Cap வாங்கினவர் | நன்மை — caplets in-the-money ஆகலாம்; இழப்பு premium மட்டும் |
| Payer Swaption (buyer) | Fixed pay-ல நுழையும் உரிமை | நன்மை — குறைந்த strike-ல நுழைய முடியும் |
| IR Forward Debt (buyer) | Bond-ஐ forward-ல வாங்க | பாதகம் — rates உயர்ந்தா bond விலை குறையும்; ஆனா locked forward விலை அதிகமா தோணும் |

நாலு products-ல rates உயர்வு Bank Of Toronto-க்கு சாதகம், ஒண்ணுல பாதகம் — Rates desk-ஓட மொத்த DV01 இந்த எல்லாத்தையும் கூட்டிக் கழிச்சு ஒரு net எண்ணா காட்டும் ([Module 10](../docs/1-capital-markets/module-10-valuation.html), [Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html)).

---

## 5. Trade Lifecycle — Rates-க்குரிய Walkthrough

**Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting** pattern-ஐ Section 4a-ஓட 5Y CAD IRS மேல பொருத்தறோம்.

```text
5Y CAD INTEREST RATE SWAP  —  FULL LIFECYCLE
========================================================================

  11:05:00  ORDER — Bank Of Toronto's rates desk instructed to pay
            fixed on CAD 100,000,000, 5Y, vs CORRA

  11:05:12  EXECUTION @ 4.00% fixed vs Goldman Sachs, negotiated
            bilaterally (or on a SEF for a standardized swap —
            Section 07)

  11:05:12  TRADE CAPTURE — Trade ID BOT-IRS-2026-034410

  11:20:00  CONFIRMATION/MATCHING -> MATCHED (via an affirmation
            platform, e.g. MarkitWire — systems-landscape §'s
            trade-confirmation entry)

  CLEARING — for a standardized CAD IRS, novated to a CCP (LCH
            SwapClear — Section 07) shortly after execution, under
            the EMIR/Dodd-Frank mandatory clearing obligation
            (Section 08) — unlike Module 6's FX, which mostly never
            clears via a CCP at all. Novation means Bank Of Toronto
            and Goldman Sachs no longer face each other directly --
            each instead faces LCH SwapClear separately, the same
            "faces the CCP, not each other" pattern Module 4 §4c
            established for equity futures

  DAILY     VARIATION MARGIN — LCH SwapClear marks the swap's value
            daily and moves margin, the same daily-cash-flow pattern
            Module 7 §5 established for exchange-traded futures —
            a genuine convergence between "OTC-negotiated" and
            "daily-margined like an exchange product"

  QUARTERLY SETTLEMENT — every 91-day period, the net fixed-vs-
            floating difference settles (Section 4a's CAD 186,986.30
            worked example), for 5 years / 20 quarters

  POSITION — Bank Of Toronto's Rates book shows one 5Y IRS,
            CAD 100,000,000 notional, marked-to-market between
            quarterly settlements using a discounted cash flow
            model referencing the current CORRA curve (Module 10)

  REPORTING — reported to a Trade Repository (DTCC GTR / REGIS-TR,
            systems-landscape §'s SDR entries) by T+1, carrying a
            UPI (product template) and a fresh UTI (this specific
            trade) — Section 06
```

**Clearing-க்கு அப்புறம் counterparty:** Novation ஆனவுடனே, Section 4a-ஓட CAD 186,986.30 net quarterly flow Bank Of Toronto-க்கும் Goldman Sachs-க்கும் இடையில நேரடியா நகராது — Bank Of Toronto ↔ LCH SwapClear, தனி leg-ல LCH SwapClear ↔ Goldman Sachs. Section 4a-ஓட diagram தொகையையும் திசையையும் பத்தி இன்னும் பொருளாதார ரீதியா சரி; அது trade-ஓட அசல் bilateral terms-ஐ விவரிக்குது, மேலே உள்ள CLEARING stage அந்தத் தொகை உண்மையில யாருக்கு கொடுபடணும்ங்கறதை திசை திருப்பறதுக்கு முன்னாடி.

**Takeaway:** முந்தைய எல்லா modules-ல இருந்தும் lifecycle-ல இருக்கற மிகப்பெரிய வித்தியாசம் — ஒரே trade, அதன் வாழ்நாள்ல **20 தனி quarterly settlement events**-ஐ உருவாக்குது, ஒவ்வொண்ணுக்கும் அப்போதைய CORRA reset-ல இருந்து தனி net cash flow கணக்கீடு தேவை — இந்த roadmap-ல வேற எதுக்கும், ஒரே Trade Capture event-ல இருந்து தன்னோட Settlement stage-ஐ இத்தனை முறை திரும்பச் செய்யற lifecycle இல்ல.

> **Deep dive இருக்கு.** IRS position என்பது வெச்சிருக்கற quantity இல்ல, ஒரு உயிருள்ள contract-க்குள்ள reference ஆகற தூய notional — ஒரு contract-க்கு ஒரு row, அதோட சொந்த terms-ல இருந்து value பண்ணப்படுது. முழு theory (CDS-க்கும் அதே pattern) — [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §6.

---

## 6. Identifiers — UPI, UTI, LEI & IR Forward Debt விதிவிலக்கு

FX மாதிரி ([Module 6](../docs/1-capital-markets/module-06-fx.html) §6), ஐந்துல நாலு Rates products-க்கு identify பண்ண security இல்ல — ஆனா Section 3-ஓட கண்டுபிடிப்பால IR Forward Debt-க்கு உண்மையிலயே உண்டு.

| Identifier | Rates-க்கு பொருந்துமா? | Example |
|---|---|---|
| ISIN / CUSIP / Ticker / FIGI | IRS, FRA, Cap/Floor, Swaption-க்கு **இல்ல** — தூய contracts (Section 03) | — |
| ISIN | **ஆமா, ஆனா IR Forward Debt-ல underlying bond-க்கு மட்டும்** | `CA1358XXXXX` — forward தானே இல்ல, forward deliver பண்ணக்கூடிய Canada 5% Bond 2035-ஐ identify பண்ணுது |
| **UPI** | ஆமா, ஐந்து products-க்கும், OTC derivative product templates-ஆ ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) | "CORRA-வை reference பண்ற 5Y CAD Fixed-Float IRS"-க்கு ஒரு UPI ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ஓட சொந்த worked example), அந்த shape-ஐ பகிரும் ஒவ்வொரு trade-லயும் மறுபயன்பாடு |
| **UTI** | ஆமா — ஐந்து products முழுக்க ஒவ்வொரு தனி trade-க்கும் தனி UTI | முந்தைய ஒவ்வொரு module-ஓட அதே logic, ஒவ்வொரு trade-க்கும் புது UTI |
| **LEI** | ஆமா — Bank Of Toronto, Goldman Sachs, மற்ற எந்த counterparty-ஐயும் identify பண்ணும் | Entity, instrument இல்ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §6) |

**Takeaway:** IR Forward Debt trade record-ல UPI/UTI (forward contract-க்கு) *மற்றும்* ISIN (underlying bond-க்கு) ரெண்டுமே இருக்கறது ஆரோக்கியமான, எதிர்பார்க்கப்பட்ட pattern — plain bond record-ல UPI தெரியறது பத்தி [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6 எழுப்பிய red flag இல்ல. வித்தியாசம்: இங்க ஒரு trade-ல உண்மையிலயே ரெண்டு உண்மையான விஷயங்கள் சேர்ந்து இருக்கு — ஒரு contract-உம் (சாத்தியமா) அது deliver பண்ணும் security-உம்.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** Rates தான் — [Module 7](../docs/1-capital-markets/module-07-commodities.html) §7-ஓட Commodity Swaps-ல *வளரும், விருப்ப* போக்கா மட்டும் பார்த்த — "OTC ஆனா கட்டாயமா centrally cleared" pattern, standardized products-க்கு ஒரு வெளிப்படையான **regulatory கட்டாயம்** ஆகுற இடம். Vanilla CAD அல்லது USD IRS OTC-ல பேசப்படுது (பெரும்பாலும் SEF-ல, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html)-ஓட Swap Execution Facility), ஆனா scope-க்குள்ள வருமளவு standardized ஆனவுடனே CCP வழியா clear ஆக *கட்டாயம்*.

| Product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|
| Interest Rate Swap (standardized) | OTC-ல பேசப்படுது (பெரும்பாலும் SEF வழியா) | EMIR/Dodd-Frank-ன் கீழ **கட்டாயமா** centrally cleared (LCH SwapClear, CME) — Section 08 |
| FRA | OTC | பெரும்பாலும் bilateral; IRS-ஐ விட குறைவா standardized, clearing குறைவாகவே கட்டாயமாக்கப்பட்டது |
| Cap/Floor | OTC | Bilateral (வழக்கம்) |
| Swaption | OTC | Bilateral (வழக்கம்); சில standardized swaptions அதிகரித்து clear ஆகுது |
| IR Forward Debt | OTC | Bilateral (வழக்கம்) — IRS-ஓட clearing கட்டாயத்தை விட [Module 5](../docs/1-capital-markets/module-05-fixed-income.html)-ஓட bond-market default-க்கு நெருக்கம் |

**Takeaway:** இந்த table [Module 7](../docs/1-capital-markets/module-07-commodities.html) §7-ஓட Commodities table-ஓட அதே வடிவம்னு கவனிங்க — ஒரு product (இங்க IRS, அங்க Futures) exchange-அருகிலுள்ள, centrally-cleared default; asset class-ஓட மீதி bilateral OTC-யாவே இருக்கும். Mechanism வேறு (Commodities-ல Futures *வடிவமைப்பாலேயே exchange-traded*-ங்கறதால clear ஆகுது; Rates-ல IRS, OTC-ல பேசப்பட்ட product-க்கு regulation *கட்டாயமாக்குறதால* clear ஆகுது) ஆனா விளைவான pattern — ஒரு standardized product முழு asset class-ஓட central-clearing volume-ஐயும் சுமக்கறது — திரும்பத் திரும்ப வருது.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

| Regulation / Body | Rates-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Standardized IRS-க்கான trading-venue விதிகள் (EU-வோட SEF-சமமான, liquid swaps-க்கு on-venue execution கட்டாயம்), + transaction reporting |
| **EMIR** | இந்த module-ஓட **வரையறுக்கும் regulation** — standardized IRS-க்கான கட்டாய central clearing கடமையை (Section 07) வகுக்குது; FRA, Cap/Floor, Swaption, IR Forward Debt-ஐயும் உள்ளடக்கும் பொதுவான OTC derivative reporting கடமையோட மேல் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) |
| **SFTR** | பொருந்தாது — இந்த ஐந்து products-ல எதுவும் securities financing transaction இல்ல ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08-ஓட repo-வோடு ஒப்பிடுக) |
| **FCA** (UK) | UK-cleared IRS நடவடிக்கையை ஒழுங்குபடுத்துது; London/LCH SwapClear IRS clearing-க்கான உலகின் மிகப்பெரிய ஒற்றை venue; FCA/Bank of England-க்கு outsized பொருத்தம், [Module 6](../docs/1-capital-markets/module-06-fx.html) §08-ஓட FX-volume புள்ளி போல |
| **CIRO** (Canada) | Canadian rates derivatives dealer conduct-ஐ மேற்பார்வை பண்ற SRO |
| **HKMA** (Hong Kong) | Securities and Futures Ordinance-ன் கீழ IRS உட்பட OTC derivatives-க்கு Hong Kong-ஓட சொந்த கட்டாய clearing, reporting regime-ஐ நடத்துது — FX-ல ([Module 6](../docs/1-capital-markets/module-06-fx.html) §08) currency-peg பங்கு, Fixed Income-ல ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08) bond-issuer பங்கு-ல இருந்து வேறுபட்ட ஒரு உண்மையான regulator பாத்திரம் |
| **DTCC** | அதன் **GTR** IRS/FRA/Swaption reporting-க்கான உலகின் மிகப்பெரிய trade repositories-ல ஒண்ணு, EU/UK-ல REGIS-TR, UnaVista-வோட |
| **DMO** (UK Debt Management Office) | இங்க குறிப்பா **Gilts மேல IR Forward Debt** வழியா பொருத்தம் — DMO-வோட issuance calendar-உம் நிலுவை Gilt stock-உம்தான் ஒரு Gilt forward reference பண்ணுறது; DMO வெறுமனே underlying cash bond-க்கு மட்டுமில்ல, ஒரு *derivative*-க்கும் உண்மையிலயே பொருத்தமான முதல் module ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08) |

**Takeaway:** EMIR-ஓட கட்டாய clearing கடமை இந்த module-ஓட தலைப்பு regulatory உண்மை — Section 07-ஓட "OTC ஆனா centrally cleared" pattern-ஐ ஒரு சந்தை வழக்கத்துல இருந்து (Commodity Swaps போல, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §07) standardized IRS-க்கு கடுமையான சட்டத் தேவையா மாத்தற குறிப்பிட்ட விதி இது; ஒரு IRS trade record CCP counterparty-ஐ காட்டி, அதே desk-ஓட Swaption record bilateral ஒண்ணை காட்டறது ஏன்னு புரிஞ்சுக்கறது நேரடியா இந்த section-ல இருந்து வருது.

---

## 9. Canadian Systems Landscape — Rates Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) Canada-ஓட *பொதுவான* Front/Middle/Back Office architecture-ஐ ஏற்கனவே வகுத்திருக்கு; இந்த section அதை Rates-க்கு சுருக்குது — Section 4a-ஓட 5Y CAD IRS Canada-ல ஆரம்பம் முதல் முடிவு வரை எந்த உண்மையான systems வழியா ஓடுது.

> **Callout — illustrative, பரிந்துரை இல்ல.** கீழ உள்ள vendor/platform பெயர்கள் ஒரு Rates desk இணையற உண்மையான Canadian, உலகளாவிய market infrastructure-ஓட பிரதிநிதிகள் — Bank Of Toronto-ஓட உண்மையான internal system தேர்வுகள் பத்தின கூற்று இல்ல. எந்த குறிப்பிட்ட vendor-ஐயும் உங்க சொந்த firm-ஓட உண்மையான technology stack-க்கு எதிராக உறுதிப்படுத்துங்க.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | Bloomberg; interdealer-broker screens (illustrative: ICAP, Tradition) | OTC execution of the CAD IRS, FRA, Cap/Floor, Swaption |
| **Front Office (exchange-listed)** | **Montreal Exchange (MX)**'s trading engine, "SOLA" | Trades exchange-listed CAD rate derivatives — Government of Canada bond futures ("CGB") and CORRA-based short-term rate futures (the successor to the old BAX/bankers'-acceptance futures, following Section 1's CDOR-to-CORRA transition) |
| **Middle Office** | MarkitWire / ICE Link (illustrative) | OTC derivative trade confirmation/affirmation — matches both counterparties' economic terms (Section 5's Confirmation stage) |
| **Middle Office** | Calypso / Murex (illustrative — same vendor family named in [systems-landscape](../docs/1-capital-markets/systems-landscape.md)) | Swap valuation, DV01/curve risk ([Module 10](../docs/1-capital-markets/module-10-valuation.md)) |
| **Back Office / Clearing** | **LCH SwapClear** (London) | Mandatory central clearing for the standardized CAD IRS (Section 7-8) — the dominant global CCP for centrally-cleared interest rate swaps across many currencies, CAD included |
| **Back Office / Clearing** | **CDCC** (Canadian Derivatives Clearing Corporation) | Clears MX-LISTED CAD rate futures (CGB, CORRA futures) domestically — a genuinely different CCP from the one clearing the OTC IRS itself |
| **Regulatory Reporting** | CSA **Multilateral Instrument 96-101**, reported to a CSA-designated Trade Repository (illustrative: DTCC Data Repository (Canada) Ltd, ICE Trade Vault Canada) | Canadian OTC interest rate derivative trade reporting, carrying the UPI/UTI from Section 6 |
| **Oversight** | **Bank of Canada** | Administers/oversees **CORRA** (the floating leg's reference rate, Section 1); oversees CDCC as a systemically important Financial Market Infrastructure |
| **Oversight** | **CIRO** | Dealer-conduct oversight of Bank Of Toronto's rates desk |

```text
CANADA -- 5Y CAD IRS (SECTION 4a) -- FRONT TO BACK, SYSTEM BY SYSTEM
========================================================================

FRONT OFFICE
  Bank Of Toronto's rates desk executes the 5Y CAD IRS against
  Goldman Sachs -- Bloomberg / interdealer-broker screens for the
  OTC-negotiated leg. (The IRS itself isn't MX-listed -- MX's SOLA
  engine instead trades the EXCHANGE-TRADED cousins of this swap:
  CGB Government of Canada bond futures and CORRA futures.)
       |
       v
MIDDLE OFFICE
  MarkitWire / ICE Link -- confirmation/affirmation, matching both
  counterparties' economic terms (Section 5's Confirmation stage)
  Calypso / Murex -- swap valuation, DV01/curve risk (Module 10)
       |
       v
BACK OFFICE / CLEARING
  LCH SwapClear (London) -- mandatory central clearing for this
  standardized CAD IRS under EMIR/Dodd-Frank (Sections 7-8)
  NOTE: the clearing leg for a CAD-denominated swap routes to a CCP
  physically OUTSIDE Canada -- there is no domestic CAD IRS CCP the
  way CDCC clears MX-listed CAD rate FUTURES domestically instead
       |
       v
REGULATORY REPORTING
  CSA Multilateral Instrument 96-101 -> reported to a CSA-designated
  Trade Repository (illustrative: DTCC Data Repository (Canada) Ltd
  or ICE Trade Vault Canada), carrying the UPI/UTI from Section 6
  Bank of Canada -- administers/oversees CORRA, oversees CDCC as a
  systemically important Financial Market Infrastructure
  CIRO -- dealer-conduct oversight of Bank Of Toronto's rates desk
```

**Takeaway:** CAD IRS-ஓட clearing leg இந்த section-ஓட கூர்மையான பாடம் — swap CAD-denominated, ஒரு Canadian வங்கியால பேசப்பட்டது, Bank-of-Canada-நிர்வகிக்கற rate (CORRA)-ஐ reference பண்ணுது என்றாலும், அதன் *clearing* லண்டன்ல LCH SwapClear-க்குத்தான் route ஆகுது, எந்த domestic Canadian CCP-க்கும் இல்ல. அதே சமயம் MX பட்டியலிடும் exchange-listed CAD rate futures domestic-ஆ CDCC-ல clear ஆகுது. "CAD-denominated அதனால Canadian CCP"ன்னு கருதற reporting system OTC product-க்கு சரியா தலைகீழாகவும், listed product-க்கு சரியாகவும் இருக்கும் — trade-ஓட currency-யும் அதன் clearing இருப்பிடமும் சுயாதீன உண்மைகள்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | IRS-ல fixed-rate payer; FRA buyer; Cap buyer; Payer Swaption buyer; Government of Canada bond-ஓட forward buyer |
| Goldman Sachs | IRS-ல floating-rate payer; FRA seller; Cap writer; Swaption writer; bond-ஓட forward seller |
| LCH SwapClear / CME | Standardized IRS-க்கு கட்டாய central clearing செய்யும் CCPs |
| DTCC GTR / REGIS-TR / UnaVista | Rates OTC derivative reports-ஐ பெறும் trade repositories |
| CDCC (Canadian Derivatives Clearing Corporation) | MX-listed CAD rate futures-ஐ (CGB, CORRA futures) domestic-ஆ clear பண்ணுது (Section 9) |
| Bank of Canada | CORRA-வை நிர்வகிக்கும்/மேற்பார்வை செய்யும்; CDCC-ஐ systemically important FMI-ஆ மேற்பார்வை பண்ணும் (Section 9) |
| CIRO | Canadian rates derivatives dealer conduct-ஐ மேற்பார்வை பண்ற SRO (Section 8, Section 9) |

---

## Recap Quiz — Numbers Edition

1.  Section 4a-ஓட IRS-ல, CORRA அந்த quarter-ல 4.30% சராசரியா இருந்திருந்தா (+0.05% spread = 4.35% floating), யார் யாருக்குக் கொடுப்பாங்க, net cash எவ்வளவு நகரும்?
2.  Section 4b-ஓட FRA settlement தொகையை கொடுக்கறதுக்கு முன்னாடி discount பண்றது ஏன், ஆனா Section 4a-ஓட IRS தன் quarterly settlement-ஐ ஒருபோதும் discount பண்றதில்ல ஏன்?
3.  Section 4c-ஓட Cap-ல, Caplet #5-ஓட reset-ல CORRA சரியா 5.00% (strike-லயே) வந்தா, payout என்ன, ஏன்?
4.  Section 4d-ஓட Swaption-ஐ பயன்படுத்தி, "prevailing swap rate 4.75%, 4.25% strike-ஐ விட மேலே" என்பது *Payer* Swaption-க்கு குறிப்பா exercise-ஐ சாதகமாக்குது ஏன்னு ஒரு வாக்கியத்துல விளக்குங்க — அதே rate நகர்வு *Receiver* Swaption-க்கு exercise-ஐ சாதகமாக்குமா?
5.  ஒரு trade record-ல UPI/UTI-உம் ISIN-உம் ரெண்டுமே நிரம்பியிருக்கு. Section 6-ஐ வெச்சு, இது IR Forward Debt trade ஆ, plain Interest Rate Swap trade ஆ — ISIN இருக்கறது மட்டும் அந்தக் கேள்விக்கு பதில் சொல்லாதது ஏன் (குறிப்பு: [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6-ஓட எச்சரிக்கையை மறுபடி பாருங்க)?
6.  Section 7, Rates asset class-ஓட IRS-clears-by-mandate pattern-ஐ, [Module 7](../docs/1-capital-markets/module-07-commodities.html)-ஓட Commodities Futures-clear-by-design pattern-ஓட இயந்திர ரீதியா வேறு ஆனா கட்டமைப்பு ரீதியா ஒரே மாதிரின்னு விவரிக்கறது ஏன்?
7.  ஒரு standard CAD IRS, Goldman Sachs நேரடியா இல்லாம LCH SwapClear-ஐ counterparty-ஆ எதிர்கொள்ள காரணமான ஒற்றை regulation எது? அதே desk-ஓட Swaption-க்கு அது எதை கட்டாயப்படுத்தாது?
8.  Section 9-ஐ வெச்சு, CAD-denominated IRS-ஓட clearing leg, Canada-க்கு வெளியே இருக்கற CCP-க்கு route ஆகறதும், MX-listed CAD rate future domestic-ஆ clear ஆகறதும் ஏன்? பின்னதை clear பண்ற Canadian institution எது?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் Asset Class Roadmap-ஓட அடுத்த நிறுத்தம் **[Module 9](../docs/1-capital-markets/module-09-credit.html): Credit**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Goldman Sachs, Bank Of Toronto-க்கு CAD 87,260.28 கொடுக்கும் — worked example-ல இருந்து திசை தலைகீழா மாறுது.**

```text
Bank Of Toronto's FIXED leg = 100,000,000 x 4.00% x 91/365 = CAD 997,260.27
Goldman Sachs' FLOATING leg = 100,000,000 x 4.35% x 91/365 = CAD 1,084,520.55
Net: Goldman Sachs PAYS Bank Of Toronto = 1,084,520.55 - 997,260.27 = CAD 87,260.28
```

Floating rate, fixed rate-ஐ விட மேலே settle ஆகும்போதெல்லாம், fixed-rate payer (Bank Of Toronto) net *receiver* ஆகுது — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8 நிறுவின அதே fixed-vs-floating netting mechanic, இந்த quarter-ல CORRA அதிகமா வந்ததால எதிர்த்திசைல ஓடுது.

**2. ஏன்னா FRA-ஓட ஒற்றை cash flow வேணும்னே அதன் reference period-ஓட ஆரம்பத்துல கொடுக்கப்படுது (rate ஏற்கனவே தெரிஞ்சப்புறம், period முடியற வரை காத்திருக்க காரணமில்ல), அதனால அந்த முந்தைய தேதிக்கு present-value பண்ணப்படணும் — IRS-ஓட quarterly cash flow, இதற்கு மாறா, அது உள்ளடக்கும் ஒவ்வொரு period-ஓட முடிவுல கொடுக்கப்படுது, அது ஏற்கனவே "இயல்பான" தேதி, மேலும் discounting தேவையில்ல.**

Section 4b-ஓட IT/reporting பார்வை இதை நேரடியா சொல்லுது: வித்தியாசமான settlement-timing வழக்கத்தை கணக்கில் எடுக்காம IRS valuation logic-ஐ FRAs-க்கு port பண்றவர் தவறா code பண்ண அதிக வாய்ப்புள்ள ஒற்றை field இது.

**3. Payout CAD 0 — சரியா strike-ல, caplet கொடுக்கப் போகும் நேர்மறை வித்தியாசம் இல்ல.**

`Payout = Notional x (Rate - Strike) x Days/365` — Rate = Strike என்றால் `(Rate - Strike) = 0`, அதனால notional அல்லது day count எதுவாயிருந்தாலும் payout பூஜ்யம். இது at-the-money FX option-க்கு [Module 6](../docs/1-capital-markets/module-06-fx.html) §4d-ஓட Q5 கண்டுபிடிப்பை பிரதிபலிக்குது: strike-ஐ சரியா தொடுவது payoff-ஓட எல்லை நிலையை உருவாக்குது, பகுதி நிலை இல்ல.

**4. Payer Swaption, fixed strike rate (4.25%)-ஐ கொடுத்து floating-ஐ பெறும் உரிமையை தருது — சந்தையின் prevailing fixed rate 4.75%-க்கு உயர்ந்திருந்தா, 4.25% மட்டும் (சந்தைக்குக் கீழ) கொடுப்பது சாதகம், அதனால exercise அர்த்தமுள்ளது. Receiver Swaption எதிர்த்திசை நகர்வை விரும்பும் — அது சாதகமா இருக்க prevailing rate strike-க்கு *கீழ* விழணும், அதனால அதே 4.75% முடிவு Receiver Swaption-ஐ பயனற்றதா ஆக்கும், exercise ஆகாது.**

Section 4d-ஓட Scenario A, புது சந்தை rate-ல ஒரு புது swap-ல நுழையறதை விட, பழைய, குறைந்த strike-ல underlying swap-ல நுழையறது சிறந்தது என்பதால exercise பண்ணுது — ஆனா அந்த logic, ரெண்டு rates-ல *குறைவானதை* lock பண்ண விரும்பும் பக்கத்துக்கு (fixed-rate payer) மட்டுமே பொருந்தும். Receiver Swaption வைத்திருப்பவர் receiver-ஆ *உயர்ந்த* rate-ஐ lock பண்ண விரும்புவார், அதனால strike-க்கு *கீழ* உள்ள சந்தை rate-தான் அதற்கு சாதகம்.

**5. பெரும்பாலும் IR Forward Debt trade — ஏன்னா Section 6, ISIN Rates products-ல IR Forward Debt deliver பண்ணக்கூடிய underlying bond-க்கு மட்டும் வரும்னு நிறுவுது; plain IRS ஒருபோதும் ஒண்ணையும் சுமக்காது. இது [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6-ஓட எச்சரிக்கைக்கு முரணாகாது, ஏன்னா அந்த எச்சரிக்கை குறிப்பா Fixed Income module-ஓட BOND record-ல UPI தெரிவது பத்தியது (அதாவது ஒரே schema/record-ல ISIN + UPI red flag) — இங்க ISIN-உம் UPI/UTI-உம் ஒரு trade-ல கருத்தியல் ரீதியா ரெண்டு வேற விஷயங்களை விவரிக்கின்றன (forward contract-உம் அதன் சாத்திய underlying delivery-உம்), Section 3, Section 6 ரெண்டும் இந்த குறிப்பிட்ட product-க்கு எதிர்பார்க்கப்பட்ட, ஆரோக்கியமான pattern-னு நிறுவுது.**

வேறுபடுத்தும் சோதனை சரியா Section 3-ஓட takeaway அமைக்கறது: ISIN derivative fields-ஓட அதே schema entity-ல இருக்கா (சந்தேகத்துக்குரியது, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §6-படி), அல்லது IR Forward Debt record-க்குள்ள ஒரு தனி underlying-security reference-க்கு தெளிவா எல்லை வகுக்கப்பட்டிருக்கா (எதிர்பார்க்கப்பட்டது, இந்த module-படி)? Context — record எந்த module-ஓட product family-க்கு சொந்தம் — எந்த வாசிப்பு பொருந்தும்னு தீர்மானிக்குது.

**6. இயந்திர ரீதியா வேறு, ஏன்னா Commodities Futures CCP வழியா clear ஆகுது, execution-ல இருந்தே *வடிவமைப்பிலேயே* exchange-traded என்பதால ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §05 — கட்டமைப்பு/venue உண்மை), Rates IRS CCP வழியா clear ஆகுது, EMIR/Dodd-Frank standardized OTC-ல பேசப்பட்ட swaps-க்கு அதை சட்டப்படி கோருவதால (Section 08 — regulatory உண்மை, venue உண்மை இல்ல) — ஆனா கட்டமைப்பு ரீதியா ஒரே மாதிரி, ஏன்னா ரெண்டு asset classes-லயும் சரியா ஒரு standardized product, முழு asset class-ஓட CCP-cleared volume-ஐ சுமக்குது, அதே asset class-ல மற்ற எல்லா products-உம் பெரும்பாலும் bilateral-ஆ இருக்கு.**

Section 7-ஓட takeaway இந்த ஒப்பீட்டை வெளிப்படையா வரையுது: அதே விளைவு table வடிவம் (ஒரு product centrally cleared, மீதி bilateral OTC), ரெண்டு முற்றிலும் வேற காரண வழிகள் வழியா — Commodities-ல venue வடிவமைப்பு, Rates-ல regulatory கட்டாயம்.

**7. EMIR (குறிப்பா standardized IRS-க்கான அதன் கட்டாய central clearing கடமை, Section 08) பொறுப்பு. அதே desk-ஓட Swaption-க்கு EMIR அதே கட்டாய clearing-ஐ கோருவதில்ல — Section 07-ஓட table Swaptions பெரும்பாலும் bilateral-ஆவே இருக்குன்னு காட்டுது, ஏன்னா அவை vanilla IRS-ஐ விட குறைவா standardized, பொதுவா EMIR-ஓட clearing mandate இலக்கு வைக்கும் குறிப்பிட்ட product scope-க்கு வெளியே.**

Section 8-ஓட takeaway இதை module-ஓட தலைப்பு regulatory உண்மையா சுட்டிக்காட்டுது: EMIR-ஓட mandate குறிப்பிட்ட, standardized product வகைகளுக்கு (vanilla IRS தெளிவான உதாரணம்) எல்லை வகுக்கப்பட்டது, ஒரு பொது "Rates derivatives" வகைக்கு இல்ல — அதனாலதான் ஒரே desk, ஒரே counterparty, ஒரே பரந்த asset class-ஓட ரெண்டு products Section 5-ஓட lifecycle walkthrough-ல முற்றிலும் வேறுபட்ட Clearing-stage விளைவுகளைக் கொண்டிருக்க முடியுது.

**8. ஏன்னா LCH SwapClear (London) பல currencies-ல, CAD உட்பட, centrally-cleared interest rate swaps-க்கான ஆதிக்க உலகளாவிய CCP — Canadian banks CAD IRS-ஐ அங்க clearing members-ஆ அல்லது client clearing வழியா clear பண்றாங்க, எந்த domestic Canadian CCP-லயும் இல்ல. CDCC, இதற்கு மாறா, MX-listed CAD rate futures-ஐ (CGB, CORRA futures) domestic-ஆ clear பண்ணுது, ஏன்னா அந்த products Montreal Exchange, ஒரு Canadian venue-ல trade ஆகுது.**

Section 9-ஓட takeaway இதை நேரடியா சொல்லுது: currency denomination-உம் clearing இடமும் சுயாதீன உண்மைகள் — CAD-denominated OTC swap-ஓட clearing, அந்த product family உலகளவுல பயன்படுத்தற CCP-யை (LCH SwapClear) பின்பற்றுது; exchange-listed CAD future-ஓட clearing அதன் listing venue-உடன் இணைந்த CCP-யை (MX-உடன் இணைந்த CDCC) பின்பற்றுது. "CAD-denominated அதனால Canadian CCP"ன்னு கருதற system OTC case-ஐ தலைகீழாக பெறும்.
