# Module 3 — Trade Lifecycle (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **மூலம்:** English Module 3-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன். Diagrams-ம் numbers-ம் மூலத்தோட அப்படியே.

---

## 1. Trade Lifecycle — ஒன்பது நிலைகள், ஒரே படம்

**Definition:** Reporting system-ல நீங்க பார்க்கற ஒவ்வொரு trade-உம் asset class எதுவா இருந்தாலும், அதே ஒன்பது stages வழியா, அதே வரிசைல போகும். Asset class-க்கு (bond vs equity vs OTC derivative) ஏத்தபடி மாறுவது ஒவ்வொரு stage-உம் *எப்படி implement ஆகுது*ங்கறதுதான் — எந்த systems, எந்த messages, என்ன கூடுதல் steps (உதா., plain bond trade-க்கு "Clearing" ஒண்ணும் செய்யாது, ஆனா cleared swap-க்கு அது ரொம்ப முக்கியம்). வரிசை மட்டும் ஒருபோதும் மாறாது.

Java பார்வைல: இது ஒரு workflow / state machine. ஒவ்வொரு stage-உம் ஒரு service; ஒவ்வொரு service-உம் trade record-ஓட `status`-ஐ அடுத்த நிலைக்கு நகர்த்தும்.

```text
THE NINE-STAGE TRADE LIFECYCLE
====================================================================

  1. ORDER            "I want to buy/sell X" — intent, not yet a trade
        |
  2. EXECUTION         Order meets a counterparty at an agreed price -> a TRADE now exists
        |
  3. TRADE CAPTURE      The trade is recorded as data (Trade ID assigned)
        |
  4. CONFIRMATION       Both sides formally state what they believe was agreed
        |
  5. MATCHING           Automated comparison: do both sides' records agree exactly?
        |
  6. CLEARING           (derivatives/some cash products) CCP steps in as buyer-to-
        |                every-seller and seller-to-every-buyer; margin is posted
        |
  7. SETTLEMENT         Securities and cash actually change hands (DvP)
        |
  8. RECONCILIATION     Internal books vs custodian/CCP records are tied out
        |
  9. REGULATORY         Trade details submitted to a Trade Repository / regulator
     REPORTING
```

| Stage | எந்த Office-க்கு சொந்தம் | என்ன produce பண்ணுது |
|---|---|---|
| Order | Front Office | ஒரு instruction, இன்னும் trade இல்ல |
| Execution | Front Office | சட்டப்படி ஒப்புக்கொண்ட trade (price, quantity, counterparty, time) |
| Trade Capture | Front Office / Middle Office | Trade ID-ஓட முதல் electronic Trade record |
| Confirmation | Middle Office | Counterparty-க்கு அனுப்பற trade economics-ஓட முறையான அறிக்கை |
| Matching | Middle Office | MATCHED அல்லது BROKEN status |
| Clearing | Middle Office / CCP | CCP-ஓட இரண்டு novated trades + margin requirement (பொருந்தினா) |
| Settlement | Back Office | Securities deliver, cash payment, ledger entries |
| Reconciliation | Back Office | Tie-out ஆன position, அல்லது flag பண்ணின break |
| Regulatory Reporting | Back Office / Compliance | Trade Repository / regulator-க்கு submit பண்ண report |

இந்த மூணு offices-க்கு பின்னாடி இருக்கற systems ஒவ்வொரு trade-க்கும் ஒரே மாதிரி இல்ல — asset class-க்கு ஏத்தபடி மாறும். கீழ இருக்கற table Bank Of Toronto முழுமைக்குமான ஒரு snapshot: ஒவ்வொரு asset class-ஓட Front/Middle/Back Office systems-ஐ (அந்த asset class module-ஓட §9-ல ஏற்கனவே அறிமுகம்) ஒரே பார்வைல பக்கம் பக்கமா காட்டுது. System names proper nouns என்பதால English-லயே விட்டிருக்கேன்; நாடு வாரியான முழு படத்துக்கு [systems-landscape](../docs/1-capital-markets/systems-landscape.html) பாருங்க.

| Capital Market Entity | Front Office Systems | Middle Office Systems | Back Office / Clearing Systems |
|---|---|---|---|
| **Equity** ([Module 4](../docs/1-capital-markets/module-04-equity.md) §9) | OMS/EMS (e.g. Bloomberg AIM, Charles River IMS, or Eze OMS — illustrative), FIX-connected smart order router | Trade capture/confirmation; CIRO market-surveillance feed under UMIR; Murex/Calypso for any equity-derivative overlay | **CDS/CDSX** (CNS, DvP book-entry settlement, T+1) |
| **Fixed Income** ([Module 5](../docs/1-capital-markets/module-05-fixed-income.md) §9) | **CanDeal**; Bloomberg ALLQ/BondTrader (illustrative); the Bank of Canada's own auction system for new-issue Government of Canada bonds | Bilateral/platform trade confirmation; valuation & risk systems (Murex, Calypso, Summit — illustrative) | **CDSX** (bond DvP settlement, T+2); **CDCC** (fixed income repo clearing) |
| **FX** ([Module 6](../docs/1-capital-markets/module-06-fx.md) §9) | **EBS**, **Refinitiv Matching/FXall**, **360T**; Bank Of Toronto's own eFX pricing/aggregation platform | Trade capture and confirmation via SWIFT MT300-type messages; risk systems (Murex, Calypso FX module) | **CLS** (PvP settlement for CLS-eligible pairs); **Lynx** carries the CAD leg into CLS; correspondent banking/SWIFT payment messages for non-CLS-eligible pairs |
| **Commodities** ([Module 7](../docs/1-capital-markets/module-07-commodities.md) §9) | **NGX** (Calgary — Canadian-domiciled physical/financial AECO gas, power, crude) *or* **CME Globex / ICE** (global benchmark venues, via a Futures Commission Merchant relationship) | Commodity risk/confirmation system (Murex/Calypso-class); ICE eConfirm-style electronic confirmation | **NGX Clearing** (part of ICE) *or* **CME Clearing / ICE Clear** |
| **Rates** ([Module 8](../docs/1-capital-markets/module-08-rates.md) §9) | Bloomberg; interdealer-broker screens (illustrative: ICAP, Tradition) for OTC; **Montreal Exchange (MX)**'s "SOLA" engine for exchange-listed CGB/CORRA futures | **MarkitWire / ICE Link** (OTC confirmation/affirmation); Calypso/Murex (swap valuation, DV01/curve risk) | **LCH SwapClear** (mandatory central clearing for standardized CAD IRS); **CDCC** (clears MX-listed CAD rate futures) |
| **Credit** ([Module 9](../docs/1-capital-markets/module-09-credit.md) §9) | **MarkitWire / ICE Link**, Bloomberg | The SAME MarkitWire/ICE Link platform also handles confirmation/affirmation here — a genuine contrast with Rates; Calypso/Murex handle ongoing valuation (upfront/running-coupon calculations) | **ICE Clear Credit** (if centrally cleared) *or* bilateral settlement; **DTCC's Trade Information Warehouse (TIW)** holds the lifecycle record either way |

**Takeaway:** ஒரு trade record-ல தெரியாத field கிடைச்சு "இது எங்கிருந்து வருது?"-ன்னு கேட்டா, அதை இந்த ஒன்பது-stage timeline-ல வெச்சுப் பார்ப்பதுதான் வேகமான diagnostic — அந்த stage எந்த office produce பண்ணுச்சு, எந்த upstream system source of truth-ங்கறதை சொல்லிடும். அதன் மேல systems table-ஐ வெச்சா diagnostic இன்னும் ஒரு படி போகும்: *asset class*, அந்த field உருவான நேரத்துல அந்த office எந்த குறிப்பிட்ட platform-ஐ பார்த்துட்டிருந்ததுன்னு சொல்லிடும்.

> **Deep dive இருக்கு.** Asset class modules (4–9) ஒவ்வொண்ணும் இந்த timeline-ஓட Reconciliation/Settlement stage-ஐ, Valuation/P&L-க்கு உணவளிக்கற தனி **Position** stage ஆ விரிவாக்குது — Position உண்மையில என்ன, Trade-ல இருந்து எப்படி வேறுபடுது, MTM, YTM, VaR, தினசரி regulatory reporting-ஐ எப்படி இயக்குதுன்னு: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html).

---

## 2. Order vs Execution — "Trade" உண்மையில எப்போ ஆரம்பிக்குது

**Definition:** **Order** = வாங்க/விற்க ஒரு instruction — இதுக்கு counterparty commitment எதுவும் இல்ல; பகுதியா நிரம்பலாம், மாற்றப்படலாம், ரத்து ஆகலாம், எந்த விளைவும் இல்லாம. **Execution** = ஒரு order, ஒத்துக்கொண்ட விலைல ஒரு பொருத்தமான counterparty-ஐ சந்திக்கற தருணம் — அந்த நொடில இருந்து, எந்த system-உம் பதிவு பண்றதுக்கு முன்னாடியே, சட்டப்படி கட்டுப்படுத்தும் trade உண்டு.

### Worked example — Maple Leaf Pension Fund, Bank Of Toronto வழியா order route பண்றாங்க

```text
ORDER  ->  EXECUTION
========================================================================

09:02:00  ORDER placed
  Maple Leaf Pension Fund instructs Bank Of Toronto:
    "BUY CAD 50,000,000 face value, Canada 5% Bond 2035,
     LIMIT price 98.60 or better"
  -> No trade exists yet. This is pure intent.

09:03:12  EXECUTION
  Bank Of Toronto's trading desk finds a seller in the interbank
  market willing to sell at 98.55 (better than the fund's 98.60 limit)
  -> A TRADE now exists, legally, at 09:03:12 — before any
     system has captured it as a data record.

  Trade economics agreed at execution:
    Instrument : Canada 5% Bond 2035 (ISIN CA1358XXXXX)
    Quantity   : CAD 50,000,000 face value
    Price      : 98.55
    Counterparty: Maple Leaf Pension Fund
    Side       : Bank Of Toronto sells / Maple Leaf Pension Fund buys
```

ஒரு order **partially fill** ஆகவும் செய்யலாம் — உதா., முதல் முயற்சில 98.60 அல்லது அதைவிட நல்ல விலைல CAD 30,000,000 face value மட்டும் desk-க்குக் கிடைச்சா, அந்த slice-ஐ உடனே execute பண்ணி, மீதி CAD 20,000,000-ஐ இன்னும் supply வரும்வரை **working order**-ஆ விட்டுடும். ஒவ்வொரு fill-உம் தனி Execution, அடுத்த கட்டத்துல தனி Trade record — ஒரு Order பல Trades-ஐ உருவாக்கலாம்.

### கூடுதல் உதாரணம் — ஒரு Order, மூணு Fills

| Fill | நேரம் | Quantity (CAD) | Price | Trade ID |
|---|---|---|---|---|
| 1 | 09:03:12 | 30,000,000 | 98.55 | BOT-2026-000901 |
| 2 | 09:41:30 | 12,000,000 | 98.58 | BOT-2026-000917 |
| 3 | 10:15:05 | 8,000,000 | 98.60 | BOT-2026-000925 |
| **மொத்தம்** | | **50,000,000** | **சராசரி ≈ 98.57** | |

Order ஒண்ணுதான் (CAD 50M), ஆனா trade records மூணு. Reporting system "order quantity = trade quantity"-ன்னு எதிர்பார்த்தா தப்பாகிடும்.

**Takeaway:** "Order"-உம் "Trade"-உம் ஒரே object இல்ல. இவை ரெண்டையும் குழப்பிக்கற reporting system, cancel/fill ஆகாத orders-க்கு phantom trades காட்டும், இல்லன்னா ஒரு order மூணு தனி trade records ஆனதை தவறவிடும். சட்டப்படி கட்டுப்படுத்தற தருணம் — Order timestamp இல்ல, Trade Capture timestamp-உம் இல்ல — **Execution timestamp** தான்.

---

## 3. Trade Capture — ஒரு Trade Record-ஓட உள்ளமைப்பு

**Definition:** **Trade Capture** = Execution ஒரு electronic record ஆகற தருணம் — Trade ID assign ஆகும், downstream-க்கு (settlement, valuation, regulatory reporting) தேவையான ஒவ்வொரு field-உம் முதல் தடவையா நிரப்பப்படும். இது பொதுவா Execution முடிஞ்சு சில milliseconds-ல தானா நடக்கும், ஆனா இது தனி stage: Execution-ல trade *சட்டப்படி* இருக்கு; Trade Capture-ல அது *data* ஆகுது.

### Worked example — மேல உள்ள trade-க்கான captured record

```text
TRADE CAPTURE  —  BOT-2026-000901
========================================================================

  Trade ID          : BOT-2026-000901
  Execution Time     : 2026-08-17 09:03:12.184 UTC
  Trade Date         : 2026-08-17  (Monday)
  Settlement Date     : 2026-08-19  (T+2 — see Module 1 §6)
  Instrument         : Canada 5% Bond 2035
  ISIN               : CA1358XXXXX
  Quantity            : CAD 50,000,000 face value
  Price              : 98.55
  Currency            : CAD
  Counterparty        : Maple Leaf Pension Fund
  Side (Bank Of Toronto): SELL
  Trader ID           : T-4471
  Desk/Book           : Government Bonds Flow Desk
  Gross Consideration : 50,000,000 x 98.55 / 100 = CAD 49,275,000
  Status              : NEW
```

இரண்டு fields-ஐ கவனமா பாருங்க — reporting systems-ஐ அடிக்கடி தடுமாற வைக்கும்:

-   **Trade Date vs Execution Time** — Trade Date ஒரு *calendar நாள்*; Execution Time ஒரு *துல்லியமான timestamp*. Best-execution analysis-க்கும், நிமிஷக் கணக்குல அளக்கப்படற regulatory reporting deadlines-க்கும் இது தேவை (Section 9).
-   **Status = NEW** — ஒவ்வொரு trade record-உம் ஒரு status-ஓட பிறக்கும்; இந்த module-ஓட மீதி (Sections 4–9) அந்த status-ஐ முன்னேத்தற பயணம். முழு state machine Section 12-ல.

**Takeaway:** Trade record-க்கும் அதோட parent Order-க்கும் quantity வேறுபட்டா, அது தானா bug இல்ல — Order பல தனி Trade Capture events-ல பகுதிபகுதியா fill ஆகியிருக்கலாம், ஒவ்வொண்ணுக்கும் தனி Trade ID.

---

## 4. Confirmation & Affirmation — எழுத்துல வாங்கிக்கறது

**Definition:** **Confirmation** = ஒரு தரப்பு, தான் ஒப்புக்கொண்டதா நினைக்கற (எல்லா economic terms-ஐயும்) அனுப்பற message. **Affirmation** = அந்த terms தன்னோட புரிதலோட பொருந்துதுன்னு counterparty முறையா ஒத்துக்கறது. ரெண்டு steps-உம் முடிஞ்சாதான் trade, கொள்கையளவுல settle ஆகத் தயாரா சட்டப்படி கருதப்படும் — "நாம ரெண்டு பேரும் கொஞ்சம் வேற மாதிரி ஒத்துக்கிட்டோம்னு நினைக்கறோம்"ங்கற classic பிரச்சனைக்கு தொழில்துறையோட பாதுகாப்பு இது.

```text
CONFIRMATION & AFFIRMATION
========================================================================

  Bank Of Toronto                                Maple Leaf Pension Fund
       |                                                    |
       | 09:15  Sends CONFIRMATION                            |
       |   Trade ID BOT-2026-000901                            |
       |   Canada 5% Bond 2035, CAD 50,000,000 @ 98.55         |
       |   Settlement Date 19-Aug-2026                        |
       |------------------------------------------------------>|
       |                                                    |
       |                                    10:30  Reviews terms
       |                                    against its own order
       |                                    record -> MATCHES
       |                                                    |
       |                              10:31  Sends AFFIRMATION|
       |<------------------------------------------------------|
       |                                                    |
  Status: CONFIRMED -> AFFIRMED
```

Cash securities-க்கு (bonds, equities) இது பொதுவா **CTM** (Central Trade Manager, DTCC) அல்லது **Omgeo/Alert** போன்ற platforms வழியா நடக்கும். OTC derivatives-க்கு இதே step பொதுவா **MarkitWire** அல்லது ரெண்டு counterparties-ஓட systems-க்கு இடையிலான நேரடி **FpML** message பரிமாற்றம் வழியா நடக்கும்; [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) Section 7-ல சொன்ன ISDA Master Agreement-ஓட legal framework-க்கு இதுதான் உணவளிக்குது.

**Takeaway:** ஒரு trade "CONFIRMED" ஆகி ஆனா "AFFIRMED" ஆகாம வழக்கத்துக்கு மீறி நீண்ட நேரம் இருந்தா, ஏதோ சரியில்லைங்கறதுக்கு இது வலுவான சமிக்ஞை — ஒண்ணு counterparty ஒரு term-ஐ ஏத்துக்கலை (break, Section 5), இல்லன்னா ஒரு operational தாமதம் T+2 settlement runway-ஐ அமைதியா தின்னுட்டிருக்கு.

---

## 5. Matching — இருதரப்பு matching & trade breaks

**Definition:** **Matching** = ரெண்டு counterparties-ஓட trade records-ஐ field-by-field தானா ஒப்பிடறது. ஒவ்வொரு economic field-உம் சரியா பொருந்தினா trade **MATCHED**; நேரா settlement/clearing-க்குப் போகும். ஒரே ஒரு field வேறுபட்டாலும் அது **BREAK** — ஒரு மனிதர் அந்த வித்தியாசத்தைத் தீர்க்கற வரை trade நிறுத்தி வைக்கப்படும்.

### Worked example — சுத்தமான match vs உடைஞ்ச match

```text
MATCHING  —  side-by-side field comparison
========================================================================

FIELD                 BANK OF TORONTO SIDE       PENSION FUND SIDE
-------------------------------------------------------------------
Trade ID (internal)   BOT-2026-000901            MLPF-77213
Instrument (ISIN)     CA1358XXXXX                CA1358XXXXX          OK
Quantity               50,000,000                 50,000,000           OK
Price                  98.55                       98.55                OK
Settlement Date        2026-08-19                  2026-08-19           OK
Currency               CAD                          CAD                  OK
-------------------------------------------------------------------
RESULT: MATCHED  ->  proceeds straight to Settlement (Section 7)


A DIFFERENT TRADE, SAME DAY — a break
========================================================================
FIELD                 BANK OF TORONTO SIDE       COUNTERPARTY SIDE
-------------------------------------------------------------------
Instrument (ISIN)     CA1358XXXXX                CA1358XXXXX          OK
Quantity               10,000,000                  10,000,000          OK
Price                  98.50                        98.65               MISMATCH (15bp)
Settlement Date        2026-08-19                   2026-08-19          OK
-------------------------------------------------------------------
RESULT: BROKEN  ->  held, routed to an Ops queue for investigation

  Root cause found: the counterparty's trader fat-fingered 98.65
  instead of 98.50 when keying the trade into their own system.
  Corrected, re-submitted, re-matched -> MATCHED at 98.50, one
  business hour later than a clean trade would have taken.
```

CAD 10,000,000 face value-ல 15-basis-point price break என்பது consideration-ல `10,000,000 x 0.15 / 100 = CAD 15,000` வித்தியாசம் — தனியா பார்த்தா சின்னதா தெரியும், ஆனா தீர்க்காம விட்டா, ரெண்டு நாள் கழிச்சு failed settlement-ஆ (Section 13) அடுக்கடுக்கா போற வகை discrepancy இதுதான்.

**Takeaway:** "Matched"-உம் "Confirmed"-உம் ஒண்ணில்ல — Confirmation = ஒரு தரப்பு terms-ஐ சொல்றது; Matching = ரெண்டு தரப்போட தனித்தனியா key பண்ணின records-உம் ஒத்துப்போகுதுன்னு தானா நிரூபிக்கறது. Counterparty-ஓட சொந்த capture-ல தப்பு இருந்தா, trade Confirmed ஆனாலும் Match ஆகாம போகலாம்.

---

## 6. Clearing — CCP வழியா Novation

**Definition:** **Clearing** **CCP (Central Counterparty)** வழியா route ஆகற products-க்கு மட்டும் பொருந்தும் — 2008-க்கு அப்புறமான சீர்திருத்தங்களால் பெரும்பாலான standardized OTC derivatives-க்கு கட்டாயம், exchange-traded products-க்கு வழக்கம். CCP **novation** பண்ணுது: இது (சந்தை வழக்கம் மட்டுமில்ல, CCP-ஓட சொந்த rulebook ஆளுகிற) ஒரு குறிப்பிட்ட legal mechanism — அசல் bilateral contract-ஐ **ரத்து செஞ்சு (extinguish)**, அதுக்குப் பதிலா புது ரெண்டு contracts-ஐ **உருவாக்குது** — ஒவ்வொரு அசல் தரப்புக்கும் CCP-க்கும் இடையில ஒண்ணு. இது assignment (அசல் contract கை மாறுறது) இல்ல; novation பழைய contract-ஐ முழுசா கொன்னு, அதன் இடத்துல ரெண்டு புதுசை உருவாக்குது. Novation முடிஞ்சா, அசல் ரெண்டு counterparties-க்கும் ஒருத்தருக்கொருத்தர் நேரடி exposure எதுவும் இருக்காது — CCP-க்கு மட்டும்தான்.

> **LCH / LCH SwapClear-ன்னா என்ன?** **LCH (London Clearing House)** = London-ல தலைமையகம் கொண்ட CCP group, **London Stock Exchange Group (LSEG)**-ஓட பகுதி — உலகின் மிகப்பெரிய clearing houses-ல ஒண்ணு. **SwapClear** = interest rate swaps-க்கான LCH-ஓட குறிப்பிட்ட clearing *service*: உலகின் centrally-cleared IRS volume-ல பெரும்பகுதியை, பல currencies-ல (இந்த உதாரணம் பயன்படுத்தற CAD உட்பட) novate பண்ற அந்த CCP. "LCH" = legal entity/group; "SwapClear" = அதுக்குள்ள இருக்கற product line — "Bank Of Toronto" (வங்கி) vs. "Bank Of Toronto-ஓட mortgage desk" (அதுக்குள்ள ஒரு business line) உறவு மாதிரி. முழு market-infrastructure விவரம் [Systems Landscape](../docs/1-capital-markets/systems-landscape.html) §6, §11-ல.

### Worked example — [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ல வந்த Bank Of Toronto-ஓட 5Y CAD IRS, LCH SwapClear வழியா clear ஆகுது

```text
BEFORE CLEARING  —  one bilateral trade
========================================================================

  Bank Of Toronto  <====  5Y CAD IRS, Notional CAD 100,000,000  ====>  Goldman Sachs
       (pays fixed 4.00% / receives floating CORRA)

AFTER NOVATION  —  LCH SwapClear steps in; TWO trades now exist
========================================================================

  Bank Of Toronto  <====  IRS #1, CAD 100,000,000  ====>   LCH SwapClear (CCP)
       (pays fixed 4.00% / receives floating)

  LCH SwapClear (CCP)  <====  IRS #2, CAD 100,000,000  ====>   Goldman Sachs
       (pays fixed 4.00% / receives floating — mirror of IRS #1)

  -> The original Bank Of Toronto <-> Goldman Sachs contract no longer
     exists; it was legally extinguished by novation. Bank Of Toronto's
     counterparty risk is now LCH SwapClear, not Goldman Sachs. If
     Goldman Sachs defaulted, LCH SwapClear absorbs the loss and Bank
     Of Toronto's trade with LCH SwapClear is unaffected.
```

Novation இலவசம் இல்ல — எதிர்கால இழப்புகளை cover பண்ண ரெண்டு தரப்பும் CCP-க்கு **Initial Margin (IM)** போடணும், trade-ஓட மதிப்பு நகரும்போது தினசரி **Variation Margin (VM)**-உம். Margin கணக்கீட்டோட முழு mechanics [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) (Collateral & Margin)-க்கு சொந்தம்; "clearing-க்கு செலவு இருக்கு"ங்கறதை உறுதியாக்க எண்களோட வடிவம் இதோ:

```text
CLEARING — ILLUSTRATIVE MARGIN ON THE CAD 100,000,000 IRS
========================================================================

  Initial Margin (IM) — illustrative rate ~2% of notional for a 5Y IRS:
    100,000,000 x 2.00% = CAD 2,000,000
    Bank Of Toronto posts CAD 2,000,000 to the CCP at trade inception.
    Goldman Sachs separately posts its own CAD 2,000,000 to the CCP.

  Variation Margin (VM) — next business day, market rates move,
  the swap's mark-to-market value shifts by CAD 180,000 in
  Bank Of Toronto's favor:
    CCP collects CAD 180,000 from Goldman Sachs' VM account
    CCP pays CAD 180,000 to Bank Of Toronto's VM account
    (VM is exchanged DAILY for the life of the trade — Module 12)
```

**Takeaway:** Cleared trade-ஓட record-ல counterparty-ஆ அசல் வங்கி இல்லாம **CCP** இருக்கறதுக்கு காரணம் novation — இது data error இல்ல, proxy field இல்ல, Clearing stage-ல சட்டப்படி நடந்தது அதுதான். Bilateral (uncleared) products — Sections 2–5-ல வந்த plain bond trade மாதிரி — இந்த stage-ஐ முழுசா தாண்டி, Matching-ல இருந்து நேரா Settlement-க்குப் போகும்.

---

## 7. Settlement — DvP mechanics, உண்மையான எண்களோட மறுபார்வை

**Definition:** **Settlement** = trade ஒரு வாக்குறுதியா இருந்து யதார்த்தமாகற இடம் — securities seller-ல இருந்து buyer-க்கு நகரும், cash buyer-ல இருந்து seller-க்கு நகரும்; ஒரு தரப்பும் ஒரு leg-ஐ மட்டும் கைல வெச்சுக்கிட்டு மறுபக்கம் இல்லாம இருக்காத மாதிரி, சிறந்த சூழல்ல ஒரே நேரத்துல **DvP (Delivery versus Payment)** வழியா. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) Sections 6, 7 T+2 timing-ஐயும் custodian/CSD சங்கிலியையும் சொன்னது; இந்த module தொடர்ந்து பின்தொடரும் உண்மையான trade-ல அந்த mechanism இதோ.

### Worked example — BOT-2026-000901 settle ஆகுது

```text
SETTLEMENT  —  Trade BOT-2026-000901, Settlement Date 2026-08-19 (T+2)
========================================================================

  Bank Of Toronto (Seller)                Maple Leaf Pension Fund (Buyer)
  Custodian: BNY Mellon                    Custodian: State Street
       |                                                    |
       | Instructs BNY Mellon to deliver                     |
       | CAD 50,000,000 face value                          |
       | Canada 5% Bond 2035 (ISIN CA1358XXXXX)               |
       |                                                    |
       |         Both custodians instruct the CSD (CDS)      |
       |         to move bond + cash SIMULTANEOUSLY (DvP)    |
       v                                                    v
  --------------------------------------------------------------------
  |                    CDS — Central Securities Depository            |
  |  09:00:14 (T+2)  Bond: CAD 50,000,000 face value moves from        |
  |                   Bank Of Toronto's account to Maple Leaf's        |
  |  09:00:14 (T+2)  Cash: CAD 49,275,000 moves from Maple Leaf's      |
  |                   cash account to Bank Of Toronto's cash account   |
  |                   (50,000,000 x 98.55 / 100)                       |
  --------------------------------------------------------------------

  RESULT: both legs move in the same instant. Neither party is ever
  exposed to "I delivered/paid and the other side didn't."
  Status: MATCHED -> SETTLED
```

**Takeaway:** நவீன சந்தைகள்ல securities-க்கான "settlement risk" (Herstatt risk-ன்னும் சொல்வாங்க) சமாளிக்கக்கூடியதா இருக்கறதுக்கு DvP-தான் முழு காரணம் — ரெண்டு legs-உம் CSD-ல இயந்திரத்தனமா கட்டிப்போடப்பட்டிருக்கறதால, இந்த rail வழியா settle ஆகற products-ல பகுதி failure (bond நகர்ந்துட்டு cash நகராம போறது) சாத்தியமே இல்ல. *முழு trade*-ஏ T+2-க்குள்ள settle ஆகலைன்னா என்ன நடக்கும்ங்கறதை Section 13 சொல்லுது.

---

## 8. Reconciliation — மூன்று-வழி Tie-Out

**Definition:** **Reconciliation** = ஒரே trade/position-ஐ பல சுயாதீன records முழுவதும் ஒப்பிடறது — firm-ஓட சொந்த internal books, custodian-ஓட statement, (பொருந்தினா) CCP-ஓட records — எல்லாம் ஒத்துப்போகுதுன்னு நிரூபிக்க. இது தொடர்ந்து நடக்கும்; ஆனா settlement-க்கு அப்புறமான end-of-day தான் மிக முக்கிய checkpoint.

### Worked example — சுத்தமான tie-out vs ஒரு break

```text
THREE-WAY RECONCILIATION  —  End of Day, 19-Aug-2026
========================================================================

                    Internal Books      Custodian (BNY Mellon)   CDS Record
Position in Canada
5% Bond 2035 (CAD)  450,000,000         450,000,000               450,000,000

RESULT: TIED OUT — all three sources agree. No action needed.


A DIFFERENT POSITION, SAME DAY — a break
========================================================================
                    Internal Books      Custodian (BNY Mellon)   CDS Record
Position in Bank Of
Toronto 4.5% Bond
2030 (CAD)          120,000,000         119,000,000               119,000,000

RESULT: BREAK — internal books show CAD 1,000,000 more than the
custodian and CDS agree on.

  Investigation finds: a CAD 1,000,000 face value SELL trade settled
  correctly at the custodian two days ago, but the internal position-
  keeping system never received the settlement confirmation message
  that would have decremented the internal position.
  -> Internal books corrected to 119,000,000. Root cause: a dropped
     message between the settlement system and the position-keeping
     system — flagged for the platform team, not a real economic loss.
```

**Takeaway:** Reconciliation breaks தானா "custodian தப்பு" அல்லது "நாம trading mistake பண்ணிட்டோம்"ன்னு அர்த்தமில்ல — உதாரணம் காட்டினபடி, நடைமுறைல மிகப் பொதுவான மூல காரணம், ஒரு internal system downstream event-ஐ process பண்ணாம போனது. ஒரு IT/reporting engineer இழுக்கப்படற bug வகை இதுதான்: பணம் சரியா இருக்கு, *data* தான் ஒத்துப்போகலை.

---

## 9. Regulatory Reporting — T+1 Deadline

**Definition:** Trade execute ஆனப்புறம், பெரும்பாலான jurisdictions அதை ஒரு கடுமையான deadline-க்குள்ள regulator அல்லது Trade Repository-க்கு report பண்ணணும்னு கோருது — பொதுவா **T+1** (அடுத்த business day முடிவுக்குள்ள); சில products/venues-க்கு **அதே நாள்** அல்லது **near-real-time** கூட. இது lifecycle-ஓட கடைசி stage; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §5-உம் [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-உம் சொன்னபடி, trade exchange-traded-ஆ (exchange-ஏ பெரும்பாலும் report ஆக கருதப்படும்) இல்ல OTC-யா (firm தானா report பண்ணணும்)ங்கறதைப் பொறுத்து வித்தியாசமா பொருந்தும்.

### Worked example — BOT-2026-000901-ஐயும் Section 6-ல வந்த cleared IRS-ஐயும் report பண்றது

```text
REGULATORY REPORTING  —  T+1 deadline, two different trades
========================================================================

TRADE 1 — Bond, BOT-2026-000901
  Executed          : Mon 17-Aug-2026, 09:03:12 UTC
  Reporting Deadline : Tue 18-Aug-2026, 23:59:59 UTC   (T+1)
  Reported            : Mon 17-Aug-2026, 18:40:00 UTC   -> WELL WITHIN DEADLINE
  Report includes     : Trade ID, ISIN, Quantity, Price, Counterparty LEI,
                        Trade Date/Time, Venue (OTC)

TRADE 2 — Cleared 5Y CAD IRS (Section 6)
  Executed            : Mon 17-Aug-2026, 14:12:05 UTC
  Reporting Deadline   : Tue 18-Aug-2026, 23:59:59 UTC   (T+1)
  Reported              : Tue 18-Aug-2026, 08:15:00 UTC   -> WITHIN DEADLINE
  Report includes       : UTI (Section 12, Module 2), UPI (Module 2 §12),
                          Notional, Fixed Rate, Counterparty LEI,
                          CCP LEI (post-novation), ISDA Taxonomy fields

MISSED DEADLINE EXAMPLE
  If Trade 2 wasn't reported until Wed 19-Aug-2026, 09:00:00 UTC,
  that's a LATE report — a regulatory breach in itself, independent
  of whether the trade settled/cleared correctly. Firms track this
  as a control metric (% of trades reported within deadline), not
  just a per-trade pass/fail.
```

**Takeaway:** Regulatory reporting deadlines **Execution Time**-ல இருந்து அளக்கப்படும் (Section 2) — Trade Capture நேரம் இல்ல, Settlement Date-உம் இல்ல. ரெண்டு நாள் கழிச்சு சரியா settle ஆன trade-உம் கூட, முதல் நாளே T+1 window-ஐ report தவறவிட்டிருந்தா reporting breach ஆகலாம்.

---

## 10. End-to-End Timeline — ஒரு Bond Trade, Order-ல இருந்து Report வரை

இந்த module முழுக்க பின்தொடர்ந்த ஒரே trade `BOT-2026-000901`-க்கு, மேலுள்ள ஒவ்வொரு stage-யையும் ஒண்ணா இணைச்சா:

```text
BOT-2026-000901  —  FULL LIFECYCLE TIMELINE
========================================================================

Mon 17-Aug-2026 (T)
  09:02:00   ORDER placed by Maple Leaf Pension Fund               [Sec 2]
  09:03:12   EXECUTION — trade agreed at 98.55                     [Sec 2]
  09:03:12   TRADE CAPTURE — Trade ID BOT-2026-000901 assigned      [Sec 3]
  09:15:00   CONFIRMATION sent to Maple Leaf Pension Fund           [Sec 4]
  10:31:00   AFFIRMATION received                                  [Sec 4]
  10:32:00   MATCHING — fields agree, status MATCHED                [Sec 5]
             (no Clearing stage — plain bond trade, bilateral)      [Sec 6]
  18:40:00   Reported to Trade Repository (well within T+1)        [Sec 9]

Wed 19-Aug-2026 (T+2)
  09:00:14   SETTLEMENT — DvP at the CDS, bond + cash both move      [Sec 7]
  17:00:00   RECONCILIATION — internal books tie out to custodian    [Sec 8]
             and CDS record. Status: SETTLED -> RECONCILED.

Total elapsed time, Order to fully reconciled: ~56 hours, spanning
one weekday plus the standard T+2 settlement cycle. Every stage
above ran cleanly — no breaks, no fails.
```

**Takeaway:** ஒவ்வொரு reporting system-உம் வடிவமைக்கப்பட்டிருக்கற "happy path" இதுதான். ஒவ்வொரு stage-ஓட *unhappy path* எப்படி இருக்கும்னு Sections 5, 8, 13 காட்டுது — அவற்றைப் படிச்சப்புறம் இந்த timeline-ஐ மறுபடி படிச்சா, break அல்லது fail எங்கே கூடுதல் நாட்களை நுழைச்சிருக்கும்ங்கறது தெளிவா தெரியும்.

---

## 11. End-to-End Timeline — ஒரு Cleared Interest Rate Swap

அதே பயிற்சி, Section 6-ன் IRS-க்கு — Clearing உண்மையான கூடுதல் steps-ஐ (கூடுதல் நாட்களையும்) நுழைக்கறதை கவனிங்க; மேல உள்ள plain bond trade-க்கு அவை தேவையே படலை.

```text
5Y CAD IRS, CAD 100,000,000 NOTIONAL  —  FULL LIFECYCLE TIMELINE
========================================================================

Mon 17-Aug-2026 (T)
  14:10:00   EXECUTION — Bank Of Toronto & Goldman Sachs agree      [Sec 2]
             terms bilaterally (fixed 4.00% vs floating CORRA)
  14:12:05   TRADE CAPTURE in both firms' systems                   [Sec 3]
  14:20:00   CONFIRMATION exchanged via FpML/MarkitWire              [Sec 4]
  14:45:00   AFFIRMATION received, MATCHED                          [Sec 5]
  15:00:00   Submitted to CCP (e.g. LCH) for clearing                [Sec 6]
  16:30:00   NOVATION completes — CCP is now legal counterparty
             to both sides. Two new trades exist (IRS #1, IRS #2).
  17:00:00   Initial Margin call: CAD 2,000,000 due from each side   [Sec 6]

Tue 18-Aug-2026 (T+1)
  09:00:00   Bank Of Toronto posts CAD 2,000,000 IM to the CCP
  08:15:00   Reported to Trade Repository — UTI + UPI included       [Sec 9]
             (within T+1 deadline)
  17:00:00   First Variation Margin call: CAD 180,000 moves from
             Goldman Sachs' account to Bank Of Toronto's account
             via the CCP (Section 6)

Every business day thereafter, for 5 years:
  - Daily VM exchanged based on the swap's mark-to-market movement
  - Quarterly net cash flow paid per Module 1 §8's mechanics
    (~CAD 187,192 per quarter in that worked example)

Total elapsed time, Execution to fully cleared + margined:
~27 hours — one extra full stage (Clearing) versus the bond trade,
plus an ongoing daily margin-exchange relationship that a plain
bond trade never has.
```

**Takeaway:** Cleared derivative-க்கு "settlement" என்பது bond மாதிரி ஒரே ஒரு T+2 நிகழ்வு இல்ல — novation, ஒரு initial margin call, அப்புறம் trade-ஓட வாழ்நாள் முழுக்க CCP-யோட தினசரி/காலாண்டு cash-flow உறவு. ஒரு derivative trade ஒரே தேதில "settled"-ன்னு காட்டற எந்த report-உம் கிட்டத்தட்ட கண்டிப்பா அந்த வார்த்தையை தளர்வா பயன்படுத்துது; இன்னும் துல்லியமான status "cleared" — அதுக்கு அடில margin activity தொடர்ந்துட்டிருக்கும்.

---

## 12. Trade Status State Machine — Reference Table

Sections 1–9-ல இருக்கற ஒவ்வொரு stage-உம் ஒரு status value-ஐ விட்டுட்டுப் போகும். ஒரு trade record-ஓட `status` field முதல் பார்வைல புரியாதப்போ பார்க்க வேண்டிய reference table இது — கீழ இருக்கற diagram அதே தகவலை flow-ஆ காட்டுது, அதுக்குக் கீழ table lookup-ஆ.

```text
TRADE STATUS STATE MACHINE
========================================================================

  NEW                                                  [Trade Capture · Sec 3]
   |
   v
  CONFIRMED ------------------------+                   [Confirmation · Sec 4]
   |                                |
   v                                v
  AFFIRMED                      DISPUTED
   |                                |
   |                    (term corrected, re-sent)
   |                                |
   |                                +--> back to CONFIRMED
   v
  MATCHED <---------------------------+                      [Matching · Sec 5]
   |                                  |
   |                     (mismatch corrected)
   |                                  |
   |                               BROKEN <-- mismatch found
   |
   |-- cash product, no clearing --------------------+
   |                                                  |
   v  derivative / cleared product                    |
  PENDING CLEARING                                    |       [Clearing · Sec 6]
   |                                                   |
   v                                                   |
  CLEARED                                              |
   |                                                   |
   v                                                   v
  SETTLED <----------------------------------------------           [Settlement · Sec 7]
   |            ^     (CLEARED -> SETTLED is the rare "cleared cash"
   |            |      case; most cleared derivatives instead stay in
   |         FAILED    an ongoing CLEARED state with recurring margin
   |            |       activity, never reaching a discrete SETTLED)
   |    (re-attempted, resolved -- Sec 13)
   |            ^
   |            +-- Settlement Date passes, no completion
   v
  RECONCILED <----------------------------+               [Reconciliation · Sec 8]
   |                                      |
   |                     (investigated & resolved)
   |                                      |
   |                              BREAK (RECON) <-- books disagree
   v
  terminal (happy path)


  Off this spine, set independently once trade economics exist:
    REPORTED    filed to Trade Repository / regulator, terminal   [Reg. Reporting · Sec 9]

  From ANY status above:
    ---> CANCELLED   trade voided before Settlement is reached, terminal
    ---> AMENDED     a field is corrected; status reverts to whatever it held before
```

| Status | அர்த்தம் | எந்த stage-ல set ஆகும் | பொதுவா அடுத்த status |
|---|---|---|---|
| `NEW` | Trade capture ஆச்சு, இன்னும் எதுவும் verify ஆகலை | Trade Capture (Sec 3) | `CONFIRMED` |
| `CONFIRMED` | இந்த firm-ஓட terms counterparty-க்கு அனுப்பப்பட்டது | Confirmation (Sec 4) | `AFFIRMED` அல்லது `DISPUTED` |
| `AFFIRMED` | சொல்லப்பட்ட terms-ஐ counterparty ஒத்துக்கிச்சு | Confirmation (Sec 4) | `MATCHED` |
| `DISPUTED` | ஒரு term-க்கு counterparty எதிர்ப்பு தெரிவிச்சாங்க | Confirmation (Sec 4) | `CONFIRMED` (திருத்தத்துக்கு அப்புறம்) |
| `MATCHED` | ரெண்டு தரப்போட சுயாதீன records-உம் சரியா ஒத்துப்போகுது | Matching (Sec 5) | `CLEARED` அல்லது `SETTLED` |
| `BROKEN` | Matching-ல ஒரு field mismatch கண்டுபிடிக்கப்பட்டது | Matching (Sec 5) | `MATCHED` (திருத்தத்துக்கு அப்புறம்) |
| `PENDING CLEARING` | CCP-க்கு submit ஆச்சு, novation இன்னும் முடியலை | Clearing (Sec 6) | `CLEARED` |
| `CLEARED` | CCP trade-ஐ novate பண்ணிடுச்சு; margin கடமைகள் செயல்ல | Clearing (Sec 6) | `SETTLED` (அரிது, cleared cash) அல்லது தொடர்ந்து |
| `SETTLED` | Securities-உம் cash-உம் பரிமாறப்பட்டது (DvP முடிஞ்சது) | Settlement (Sec 7) | `RECONCILED` |
| `FAILED` | Settlement Date கடந்துபோச்சு, முடியலை | Settlement (Sec 7) | `SETTLED` (தீர்ந்தப்புறம்) — Sec 13 பாருங்க |
| `RECONCILED` | Internal books, custodian/CCP records-ஓட tie out ஆச்சு | Reconciliation (Sec 8) | terminal (happy path) |
| `BREAK (RECON)` | Internal books, ஒரு external record-ஓட ஒத்துப்போகலை | Reconciliation (Sec 8) | `RECONCILED` (விசாரணைக்கு அப்புறம்) |
| `REPORTED` | Trade Repository/regulator-க்கு submit ஆச்சு | Reg. Reporting (Sec 9) | terminal |
| `CANCELLED` | Settlement-க்கு முன்னாடியே trade ரத்து | Settlement-க்கு முந்தைய எந்த stage-உம் | terminal |
| `AMENDED` | ஆரம்ப capture-க்கு அப்புறம் ஒரு field திருத்தப்பட்டது, audit trail entry உருவாகும் | எந்த stage-உம் | திருத்தப்பட்ட status-க்கே திரும்பும் |

**Takeaway:** ஆரோக்கியமான trade இந்த statuses-ல கிட்டத்தட்ட ஏழை, எந்த detour-உம் இல்லாம வரிசையா தொடும் (Sections 10–11 timelines). இந்த table-ல மீதி ஒவ்வொரு status-உம் — `DISPUTED`, `BROKEN`, `FAILED`, `BREAK (RECON)` — happy path-ல தொடர்வதுக்கு முன்னாடி ஒரு மனிதர் தலையிட வேண்டிய trade-ஐ பிடிச்சு வெச்சுக்கறதுக்கே இருக்கு.

---

## 13. தப்பா போகும்போது — Settlement Fail, எண்களோட

**Definition:** **Settlement fail** = trade-ஓட Settlement Date வந்தும் ஒரு leg (பொதுவா securities leg) முடிக்க முடியாம போறது — பெரும்பாலும் seller-ஓட custody account-ல deliver பண்ணத் தேவையான அளவு security இல்லாததால. DvP (Section 7) இயந்திரத்தனமா *பகுதி* failure-ஐ தடுக்கறதால, முழு settlement-உம் நடக்கவே நடக்காது, தீர்வு வரும்வரை ரெண்டு legs-உம் நிலுவைல இருக்கும்.

### Worked example — Bank Of Toronto deliver பண்ண தவறுது

```text
SETTLEMENT FAIL  —  Trade BOT-2026-000955
========================================================================

  Instrument  : Bank Of Toronto 4.5% Bond 2030
  Quantity     : CAD 10,000,000 face value
  Price        : 99.20
  Counterparty : Goldman Sachs (buyer)
  Settlement Date (T+2): Wed 19-Aug-2026

  19-Aug-2026, 09:00: DvP instruction sent to the CDS.
  19-Aug-2026, 09:00: FAILS — Bank Of Toronto's custody account only
                       holds CAD 9,000,000 face value of this bond,
                       CAD 1,000,000 short (a separate internal
                       position error, exactly the flavor of bug
                       described in Section 8).

  Status: SETTLED -> FAILED

  Trade re-attempted daily; actually settles 2 business days late,
  on Fri 21-Aug-2026.
```

Settlement-discipline regimes-ல (உதா., EU-ல **CSDR**), தாமதமான settlement, fail நீடிக்கற ஒவ்வொரு business day-க்கும் தவறுற தரப்புக்கு **தினசரி cash penalty** விதிக்கும்:

```text
CASH PENALTY CALCULATION  —  illustrative daily rate 0.10 bps for a
government/corporate bond, applied per business day late
========================================================================

  Daily Penalty = Reference Price x Quantity x (Penalty Rate / 10,000)

  Reference Price   = 99.20 (per 100 face value)
  Quantity           = CAD 10,000,000 face value
  Penalty Rate        = 0.10 bps = 0.0010%

  Market Value used for the calc = 10,000,000 x 99.20 / 100
                                  = CAD 9,920,000

  Daily Penalty = 9,920,000 x 0.0010 / 100 = CAD 99.20 per day late

  2 business days late (settled 21-Aug instead of 19-Aug):
    Total penalty = 99.20 x 2 = CAD 198.40

  -> Charged to Bank Of Toronto, the failing (delivering) party.
```

இங்க cash penalty சின்னதா தெரிஞ்சாலும், fail-ஓட உண்மையான செலவு penalty இல்ல — அது **downstream risk**: Goldman Sachs அந்த bonds-ஐ தன்னோட *வேற* trade-ல deliver பண்ணத் தேவைப்பட்டா, Bank Of Toronto-ஓட fail சந்தையில வேற இடத்துல இரண்டாவது fail-ஆ அடுக்கடுக்கா பரவலாம் ("fails chain"); ஒரு குறிப்பிட்ட grace period-க்கு அப்புறம், buyer **buy-in** பண்ணலாம் — சந்தையில வேற இடத்துல இருந்து securities-ஐ வாங்கி, விலை வித்தியாசத்தையும் செலவுகளையும் அசல் seller-கிட்ட வசூலிக்கலாம்.

**Takeaway:** Settlement system-ல `FAILED` status என்பது upstream-ல இருந்த ஒரு break-ஓட நேரடி, கண்டுபிடிக்கக்கூடிய விளைவு — இங்க மாதிரி, Section 8-ஓட reconciliation break-ல சொன்ன அதே வகை internal position-keeping error-ஆ அடிக்கடி இருக்கும். "இந்த trade ஏன் fail ஆச்சு?"ன்னு துரத்தினா பெரும்பாலும் "நம்ம books ஏன் நம்மகிட்ட உண்மையில இருந்ததை விட அதிகம் காட்டுச்சு?"-ன்னு நேரா வந்து நிக்கும் — அதனாலதான் Reconciliation (Section 8), Settlement (Section 7) lifecycle-ல தூரத்துல இல்லாம பக்கத்துல இருக்கு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Maple Leaf Pension Fund | Trade BOT-2026-000901 ஆகப்போற order-ஐ வைக்கும் buy-side client |
| Bank Of Toronto | Trades-ஐ execute, capture, confirm, clear, settle பண்ற sell-side dealer |
| Goldman Sachs | Cleared IRS-லயும் failed bond trade உதாரணத்துலயும் counterparty |
| BNY Mellon / State Street | தங்கள் clients சார்பா settlement instruct பண்ற custodians |
| CDS (Canadian Depository for Securities) | Bond trade-ஓட DvP settlement உண்மையில நடக்கற CSD |
| CCP (உதா., LCH) | Cleared IRS-ஐ novate பண்ணி, தொடர் margin-ஐ நிர்வகிக்கும் Central Counterparty |
| Trade Repository | Section 9-ஓட T+1 regulatory reports போய்ச் சேரும் இடம் |

---

## Recap Quiz — Numbers Edition

1.  Maple Leaf Pension Fund 98.60 அல்லது அதைவிட நல்ல விலைல வாங்க limit order போடுது; Bank Of Toronto 98.55-ல execute பண்ணுது. Fill யாருக்குச் சாதகமா அமைஞ்சது, CAD 100 face value-க்கு எவ்வளவு?
2.  Trade BOT-2026-000901-ல, Order போட்ட நேரத்துல (09:02:00) `null`/தெரியாததா இருந்து, Execution/Trade Capture-ல (09:03:12) முழுசா நிரம்பிய நாலு fields-ஐ சொல்லுங்க.
3.  ஒரு trade ஆறு மணி நேரமா `CONFIRMED` ஆகி `AFFIRMED` ஆகலை, firm-ஓட வழக்கமான turnaround-ஐ விட ரொம்ப மேல. இது குறிப்பா என்ன நடந்ததை (அல்லது நடக்கலைன்னு) சொல்லுது? Confirmation, Matching ரெண்டுல எது முந்தைய stage?
4.  ரெண்டு firms ஒரே trade-ஐ ISIN mismatch-ஓட capture பண்ணுது (ஒரு bond-ஓட identifier மறுவெளியீடு ஆனப்புறம் ஒரு system பழைய ISIN-ஐ வெச்சிருந்தது). இந்த trade `MATCHED` status-ஐ அடையுமா? ஏன்/ஏன் இல்ல?
5.  CAD 100,000,000 cleared IRS-ல, novation-க்கு அப்புறம் உருவாகற ரெண்டு புது bilateral trades-ஐயும், Bank Of Toronto பார்வைல ஒவ்வொண்ணுலயும் counterparty யாருன்னும் சொல்லுங்க.
6.  Cleared derivative, Section 10-ல bond trade அடையற மாதிரி `SETTLED` status-ஐ ஏன் அடையறதில்ல — அதுக்குப் பதிலா எந்த status-ஐ அடையுது, அதுக்கப்புறம் என்ன தொடர்ந்து நடக்குது?
7.  ஒரு reconciliation break-ல internal books, custodian-ஐயும் CSD-யையும் (அவை ரெண்டும் ஒண்ணோடொண்ணு ஒத்துப்போகுது) விட CAD 1,000,000 அதிகமா காட்டுது. இது பெரும்பாலும் custodian தப்பா, market-side இழப்பா, இல்ல வேறெதுவா — என்ன?
8.  Section 9-ல Trade 2 Monday 14:12:05 UTC-ல execute ஆகி, Tuesday 08:15:00 UTC-ல report ஆச்சு. இது T+1 deadline-க்குள்ளயா? எந்த UTC timestamp இதை தவறவிட்ட deadline ஆக்கியிருக்கும்?
9.  Bank Of Toronto-ஓட bond trade 2-க்கு பதிலா 3 business days-ல settle ஆகுது (அதாவது 1 நாள் தாமதம்); Section 13-ல இருக்கற உதாரண CSDR-பாணி penalty-ப்படி, CAD 10,000,000 face value, reference price 99.20, rate 0.10bps/day. அந்த 1 நாளுக்கான cash penalty என்ன?
10. ஒரு சாதாரண (break இல்லாத) bilateral bond trade-க்கு இந்த ஐந்து statuses-ஐ சரியான காலவரிசைல அடுக்குங்க: `SETTLED`, `NEW`, `RECONCILED`, `MATCHED`, `AFFIRMED`.

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 4](../docs/1-capital-markets/module-04-equity.html): Fixed Income / Bonds**-க்குப் போலாம்.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Fill, Maple Leaf Pension Fund-க்குச் சாதகமா அமைஞ்சது; CAD 100 face value-க்கு 0.05 (5 basis points).**

Fund-ஓட limit "98.60 அல்லது அதைவிட நல்லது" (வாங்குபவருக்கு விலை குறைவா இருக்கறதுதான் நல்லது, அதனால 98.60 அல்லது குறைவு). Bank Of Toronto 98.55-ல execute பண்ணிச்சு — அது limit-ஐ விட 0.05 கீழ; அதாவது fund தன் worst-acceptable விலையை விட குறைவா கொடுத்துச்சு, வாங்குபவருக்கு சிறந்த விளைவு.

**2. Trade ID, Execution Time, Price, Status (`NEW`) — 09:02:00-ல தெரியாதவை; 09:03:12-க்குள்ள நிரம்பின.**

Order நேரத்துல (09:02:00) நோக்கம் மட்டும் இருந்தது — "98.60 அல்லது அதைவிட நல்ல விலைல CAD 50,000,000 வாங்கணும்" — உறுதியான counterparty இல்ல, ஒத்துக்கொண்ட விலை இல்ல, Trade ID-ம் இல்ல, ஏன்னா உண்மையான Execution நடந்து Trade Capture ஓடிய பின்னாடிதான் Trade ID assign ஆகும் (Section 3).

**3. பெரும்பாலும் counterparty இன்னும் review/respond பண்ணலை — operational தாமதம், அல்லது dispute queue-ல உட்கார்ந்திருக்கு. Confirmation முந்தைய stage; Matching Affirmation-க்கு அப்புறம்.**

Section 1 வரிசையைப் பாருங்க: Confirmation → Matching; Affirmation, Confirmation பரிமாற்றத்தோட பகுதியா (Section 4) நடந்தப்புறம்தான், trade தானா Matching step-க்கு (Section 5) அனுப்ப முடியும். Confirmed-க்கும் Affirmed-க்கும் இடையிலான ஆறு மணி இடைவெளி என்பது counterparty இன்னும் முறையா ஒத்துக்கலை — தானா field-by-field ஒப்பீடு (Matching) ஓடற நிலைக்கே வரலை.

**4. இல்ல — அது match ஆகாது, break ஆகும்.**

Matching (Section 5) சரியான field-by-field ஒப்பீடு. மற்ற எல்லா fields-உம் (quantity, price, settlement date) ஒரே மாதிரி இருந்தாலும், ISIN mismatch என்பது Section 5 உதாரணத்துல price-ல காட்டின அதே வகை ஒற்றை-field முரண்பாடு. ஒரு தரப்பு பழைய/மறுவெளியீடு ஆன identifier-ஐ வெச்சிருக்குன்னு யாராவது கண்டுபிடிச்சு திருத்தற வரை trade `BROKEN` status-ல இருக்கும்.

**5. IRS #1: Bank Of Toronto vs CCP (உதா., LCH). IRS #2: CCP vs Goldman Sachs — அதுல Bank Of Toronto-க்கு நேரடி exposure இல்ல.**

குறிப்பா Bank Of Toronto பார்வைல, novation-க்கு அப்புறம் மிச்சமிருக்கற ஒரே counterparty CCP; இந்த trade-ல அசல் Goldman Sachs உறவு முழுசா மாற்றப்பட்டுவிட்டது (Section 6). IRS #2-ல Bank Of Toronto party-யே இல்ல; அது CCP-க்கும் Goldman Sachs-க்கும் மட்டுமேயான trade.

**6. அது `CLEARED`-ஐ அடையுது, `SETTLED`-ஐ அல்ல — அதுக்கப்புறம் trade-ஓட வாழ்நாள் முழுக்க தினசரி Variation Margin பரிமாற்றமும், periodic (இந்த உதாரணத்துல காலாண்டு) net cash-flow payments-உம் தொடர்ந்து நடக்கும்.**

Bond-ல T+2-ல ஒரே DvP நிகழ்வு trade-ஐ முழுசா முடிச்சுடும் (Section 7); cleared swap-ல deliver பண்ண notional இல்ல ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) — ஒரே "settlement" நிகழ்வுன்னு சொல்லக்கூடியது எதுவும் இல்ல. அதுக்குப் பதிலா Section 11, trade CCP-யோட தொடர் உறவுக்குள்ள நகர்றதைக் காட்டுது: அடுத்த நாளே margin calls, அப்புறம் முழு 5-வருஷ tenor-க்கும் தொடர்ச்சியான cash-flow அட்டவணை.

**7. பெரும்பாலும் வேற ஏதோ — internal processing/messaging error; உண்மையான பொருளாதார இழப்பும் இல்ல, custodian தப்பும் இல்ல.**

Section 8 worked example-ஓட அதே மாதிரி: custodian-ம் CSD-யும் (ரெண்டு சுயாதீன, வெளி sources) ஒண்ணோடொண்ணு ஒத்துப்போய், internal books-ஓட மட்டும் முரண்பட்டா, firm-ஓட சொந்த internal position-keeping system ஒரு downstream event-ஐ (உதா., தவறிப்போன settlement confirmation message) process பண்ணலைங்கறதுதான் அதிக சாத்தியமான விளக்கம் — பணம் உண்மையில காணாம போகலை, custodian-ஓட சொந்த records-உம் தப்பில்ல.

**8. ஆமா, T+1 deadline-க்குள்ள. Tuesday 18-Aug-2026, 23:59:59 UTC வரை அல்லது அதுக்கு முன்னாடி எந்த UTC timestamp-உம் இன்னும் அதை சந்திக்கும்; Wednesday 19-Aug-2026, 00:00:00 UTC-ல இருந்து எதுவும் தவறவிட்ட deadline.**

Monday 14:12:05 UTC-ல execute ஆனதுன்னா, T+1 deadline *அடுத்த* business day முடிவுவரை — Tuesday 23:59:59 UTC (Section 9). Tuesday 08:15:00 UTC-ல நடந்த உண்மையான report அந்த cutoff-ஐ வசதியா முந்திடுச்சு; Wednesday-க்குள்ள நுழைஞ்சிருந்தாதான் மீறியிருக்கும்.

**9. CAD 99.20.**

```text
Market Value = 10,000,000 x 99.20 / 100 = CAD 9,920,000
Daily Penalty = 9,920,000 x 0.0010 / 100 = CAD 99.20 per day
1 day late    = CAD 99.20 x 1 = CAD 99.20
```

தினசரி penalty rate, trade எத்தனை நாள் தாமதம்ங்கறதால மாறாது — *மொத்தம்* மட்டும் தாமத நாட்களோட scale ஆகும். ஒரு நாள் தாமதம் = சரியா ஒரு நாள் penalty, CAD 99.20 — Section 13-ல ரெண்டு நாள் தாமதத்துக்குக் காட்டிய மொத்த CAD 198.40-ல சரி பாதி.

**10. `NEW` → `AFFIRMED` → `MATCHED` → `SETTLED` → `RECONCILED`.**

இது Section 1 stage வரிசையை நேரடியா பின்பற்றுது: Trade Capture `NEW` set பண்ணுது (Sec 3); counterparty ஒத்துக்கிட்டதும் Confirmation `AFFIRMED` தருது (Sec 4); அப்புறம் Matching, ரெண்டு தனித்தனியா key பண்ணின records-உம் ஒத்துப்போகுதுன்னு `MATCHED` உறுதிப்படுத்துது (Sec 5); Settlement T+2-ல அதை `SETTLED` ஆக்குது (Sec 7); end-of-day tie-out கடைசியா `RECONCILED`-ல இறக்குது (Sec 8) — Section 10-ஓட end-to-end timeline-ல நடந்தது இதேதான்.
