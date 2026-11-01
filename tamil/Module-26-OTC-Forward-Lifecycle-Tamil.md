# Module 26 — Complete OTC Derivative Lifecycle — USD/CAD FX Forward (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி).
> **Reference module — முழு end-to-end architecture walkthrough.** [Module 6](../docs/1-capital-markets/module-06-fx.html), [Module 21](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html), [Module 22](../docs/1-capital-markets/module-22-hedging-deep-dive.html), [Module 23](../docs/1-capital-markets/module-23-otc-forward-swap-option-deep-dive.html) ஒவ்வொண்ணும் FX Forward-ஓட ஒரு துண்டை மட்டும் சொன்னுச்சு. இந்த module **ஒரே ஒரு trade-ஐ** — Bank Of Toronto-ஓட USD/CAD FX Forward, ABC Canada Inc.-உடன் — அதோட **முழு operational lifecycle** வழியா, system-by-system, field-by-field கூட்டிட்டுப் போகுது: Trade Capture → Confirmation → Enrichment → Valuation → MTM → Collateral → Settlement → Regulatory Reporting; கடைசியில இதை ஒரு platform-ஆ கட்ட microservices / event-driven architecture.
> **மூலம்:** English Module 26-ஐ பேச்சுத் தமிழ்ல சொல்றது; சில கூடுதல் எளிய உதாரணங்களும் உண்டு.

---

## 1. நோக்கம் (Purpose)

இந்த document ஒரு முழு OTC derivative trade-ஐ — **USD/CAD FX Forward** — இப்படி கூட்டிட்டுப் போகுது:

**Trade Capture → Confirmation → Enrichment → Valuation → MTM → Collateral → Settlement → Regulatory Reporting**

ரெண்டு விஷயம் புரிஞ்சுக்கணும்:

1. OTC derivative-ஓட **business lifecycle**.
2. ஒவ்வொரு stage-லயும் systems உருவாக்கற / பயன்படுத்தற **data / fields**.

---

## 2. Business Scenario

### 2.1 Parties

| Role | Party |
|---|---|
| Bank | Bank Of Toronto |
| Client / Counterparty | ABC Canada Inc. |
| Trade | USD/CAD FX Forward |
| Product | FX Forward |
| நோக்கம் | எதிர்கால USD payment-ஐ hedge பண்ணுறது |

ABC Canada, ஒரு US supplier-க்கு **30-Nov-2026**-ல **USD 10,000,000** கட்டணும்.

USD/CAD உயர்ந்துட்டா, அந்த USD payment CAD-ல இன்னும் costly ஆகிடுமேன்னு ABC கவலைப்படுது.

அதனால Bank Of Toronto-வோட ஒரு OTC FX Forward பண்ணுது.

### எளிய உதாரணம் (கடைக்காரர் analogy)

உங்களுக்கு 3 மாசம் கழிச்சு ஒரு USD பொருள் வாங்கணும்னு வெச்சுக்கோங்க. இன்னிக்கே "3 மாசம் கழிச்சு நான் உங்ககிட்ட டாலரை 1.3545-க்கு வாங்குவேன்"னு ஒரு bank-கிட்ட விலையை பூட்டிடறீங்க. 3 மாசத்துல டாலர் 1.39 ஆனாலும், நீங்க 1.3545-க்கே வாங்குவீங்க; 1.30 ஆச்சுன்னா — நீங்க இழப்பீங்க (ஏன்னா மலிவா வாங்கிருக்கலாம்). Forward-ஓட தன்மையே இதுதான்: **உறுதி கிடைக்கும், ஆனா வாய்ப்பை விட்டுக்கொடுக்கணும்**.

---

## 3. Trade Economics

Trade **30-Aug-2026**-ல execute ஆச்சுன்னு வெச்சுக்குவோம்.

| Attribute | Value |
|---|---|
| Trade Date | 30-Aug-2026 |
| Value Date / Maturity | 30-Nov-2026 |
| Product | FX Forward |
| Currency Pair | USD/CAD |
| Buy Currency | USD |
| Sell Currency | CAD |
| USD Notional | USD 10,000,000 |
| Forward Rate | CAD 1.3545 per USD |
| CAD Notional | CAD 13,545,000 |
| Client | ABC Canada Inc. |
| Bank | Bank Of Toronto |
| Settlement | Physical (Deliverable Forward) |
| Business Purpose | Hedge |
| Direction from ABC perspective | Buy USD / Sell CAD |
| Governing Agreement | ISDA Master Agreement + CSA between Bank Of Toronto and ABC Canada Inc. ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.md)) |
| ISDA Taxonomy Path | Foreign Exchange / Forward / Deliverable / Forward ([Module 21](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.md) §3's identical worked example) |
| Hedge Designation | Yes — hedges a known future USD payable ([Module 22](../docs/1-capital-markets/module-22-hedging-deep-dive.md) §9's formal hedge-designation flag, not just an economic observation) |

ABC பூட்டிவெச்சது:

```text
USD 10,000,000 × CAD 1.3545
= CAD 13,545,000
```

> **Callout — USD/CAD ஒரு G10, முழுசா convertible pair; அதனால Deliverable தான் default.** ஒவ்வொரு FX Forward-க்கும் Deliverable-vs-NDF தீர்மானம் தேவையில்ல — emerging-market currency-க்கு மட்டும்தான். CAD, மற்ற G10 currencies மாதிரியே, capital-account restrictions இல்லாதது; அதனால USD/CAD forward — இந்த trade மாதிரி — கிட்டத்தட்ட எப்பவும் physically settle ஆகும்: maturity-ல ரெண்டு currencies-உம் உண்மையில பரிமாறப்படும் (Section 19), எந்த dealer authorization-உம் residency test-உம் தேவையில்ல. இதை இந்த module முதல்ல கட்டப்பட்ட **INR** example-ஓட ஒப்பிடுங்க: INR முழுசா convertible இல்லாததால USD/INR forwards ரெண்டாப் பிரியுது — onshore **Deliverable Forward** (RBI-authorized dealer bank வழியா, இந்திய resident counterparty-உடன்) vs offshore **Non-Deliverable Forward (NDF)** — இது USD-ல ஒரு cash வித்தியாசம் மட்டும் settle பண்ணும், ஏன்னா offshore counterparty-ஆல INR-ஐ deliver பண்ணவே முடியாது. இந்த trade-ஓட data model-ல (Section 8) இருக்கும் `NDF Flag` field-ஐ எல்லா FX Forward record-லயும் வெச்சிருக்கறது மதிப்பு — G10 trade-ல கிட்டத்தட்ட எப்பவும் `N`, ஆனா ஒரு EM currency pair வந்தா அதே schema தயாரா இருக்கணும்.

---

## 4. High-Level Lifecycle

```text
                    OTC FX FORWARD
                          |
                          v
                  1. Trade Capture
                          |
                          v
                  2. Confirmation
                          |
                          v
                  3. Enrichment
                          |
                          v
                  4. Valuation
                          |
                          v
                     5. MTM
                          |
                          v
                  6. Collateral
                          |
                          v
                  7. Settlement
                          |
                          v
               8. Regulatory Reporting
```

ஒரே trade identifier, downstream systems எல்லாம் lifecycle முழுக்க அந்த trade-ஐ trace பண்ண உதவணும்.

---

## 5. Stage 1 — Trade Capture

### 5.1 என்ன நடக்குது?

Trader-உம் ABC-யும் trade-ஐ agree பண்றாங்க.

Front-office trading system economic terms-ஐ capture பண்ணுது.

ஒரு unique trade identifier உருவாக்கப்படுது.

உதாரணம்:

```text
Trade ID = BOT-FX-20260830-00012345
```

அப்புறம் trade downstream systems-க்கு அனுப்பப்படுது.

### 5.2 Trade Capture Data

| Field | Example |
|---|---|
| Trade ID | BOT-FX-20260830-00012345 |
| Product Type | FX_FORWARD |
| Trade Date | 2026-08-30 |
| Effective Date | 2026-08-30 |
| Maturity Date | 2026-11-30 |
| Currency Pair | USD/CAD |
| Buy Currency | USD |
| Sell Currency | CAD |
| Buy Amount | 10,000,000 USD |
| Sell Amount | 13,545,000 CAD |
| Forward Rate | 1.3545 |
| Client | ABC Canada Inc. |
| Counterparty ID | ABC12345 |
| Trader | TRADER001 |
| Trading Desk | FX-CANADA |
| Legal Entity | Bank Of Toronto |
| Book | FX_HEDGE_BOOK |
| Settlement Type | PHYSICAL |
| Execution Timestamp | 2026-08-30 10:32:15 ET |
| Trade Status | NEW |

> **IT பார்வை:** Trade Status = `NEW` — இது ஒரு state machine-ஓட முதல் state (NEW → CONFIRMED → ENRICHED → ... → MATURED). `Execution Timestamp` timezone-ஓட சேர்த்து store பண்ணுங்க (ET); Counterparty ID இங்க இன்னும் internal ID தான், LEI அல்ல — அது Enrichment-ல வரும்.

---

## 6. Stage 2 — Confirmation

### 6.1 என்ன நடக்குது?

Bank Of Toronto capture பண்ணின trade economics-ஐ counterparty-ஓட புரிதலோட ஒப்பிடுறாங்க.

ஒரு confirmation document / message உருவாக்கப்படுது.

ரெண்டு பேரும் confirm பண்றாங்க:

> "இதுதான் நாம செஞ்ச trade-னு நாங்க ஒத்துக்கறோம்."

Confirmation சட்டப்படி முக்கியமான economic, settlement terms-ஐ கொண்டிருக்கு.

### 6.2 Example Confirmation

```text
Confirmation Number:
CONF-FX-20260830-00012345

Trade:
USD/CAD FX Forward

Trade Date:
30-Aug-2026

Value Date:
30-Nov-2026

Party A:
Bank Of Toronto

Party B:
ABC Canada Inc.

Party A sells:
USD 10,000,000

Party A buys:
CAD 13,545,000

Forward Rate:
CAD 1.3545 per USD

Settlement:
Physical
```

> **கவனிக்க:** English மூலத்துல இந்த confirmation example-ல "Party A (Bank Of Toronto) buys USD 10,000,000" ன்னு இருக்கு. ஆனா trade-ஓட மற்ற பகுதிகள் (Section 2, 19, Actors table) எல்லாம் *ABC USD வாங்குது, Bank Of Toronto USD விற்குது*ன்னு சொல்லுது. அதனால இங்க நான் Bank-ஓட பார்வையில சரியா எழுதியிருக்கேன்: **Bank sells USD / buys CAD**. உண்மையான confirmation-ல buy/sell பக்கங்கள் தலைகீழா போனா அது மிகப்பெரிய break — Confirmation matching-ல இதுதான் முதல்ல பிடிபடும்.

### 6.3 முக்கியமான Confirmation Fields

| Category | Fields |
|---|---|
| Identification | Trade ID, Confirmation ID |
| Parties | Party A, Party B |
| Product | FX Forward |
| Dates | Trade Date, Value Date |
| Currency | Currency Pair |
| Amounts | Buy Amount, Sell Amount |
| Rate | Forward Rate |
| Settlement | Physical/Cash |
| Legal | Master Agreement |
| Documentation | Confirmation Status |
| Operations | Settlement Instructions |

---

## 7. Stage 3 — Enrichment

### 7.1 Enrichment ஏன் தேவை?

முதல்ல capture ஆன trade-ல business economics தான் இருக்கு. ஆனா downstream systems-க்கு கூடுதல், standardized தகவல் வேணும்.

Enrichment சேர்க்கக்கூடியவை:

- LEIs
- Legal entity identifiers
- UTI
- Product classification
- UPI
- Booking entity
- Jurisdiction
- Settlement instructions
- Counterparty classification
- Regulatory attributes
- Risk attributes

இது regulatory reporting-க்கு ரொம்ப முக்கியம்.

**ஒரு எளிய ஒப்பீடு:** Trade capture = ஒரு பார்சல்ல இருக்கற பொருள். Enrichment = அந்த பார்சல்ல முகவரி, பின் கோடு, barcode, customs form எல்லாம் ஒட்டறது — பொருள் மாறல, ஆனா இப்போ உலகம் முழுக்க எந்த system-ஆலயும் கையாள முடியும்.

---

## 8. Example Enriched Trade

```text
Trade ID:
BOT-FX-20260830-00012345

Product:
FX Forward

Currency Pair:
USD/CAD

Buy Currency:
USD

Sell Currency:
CAD

Buy Amount:
10,000,000

Sell Amount:
13,545,000

Forward Rate:
1.3545

Trade Date:
2026-08-30

Maturity Date:
2026-11-30

Bank LEI:
5493001KJTIIGC8Y1R12

Counterparty LEI:
549300ABCEXAMPLE45

UTI:
5493001KJTIIGC8Y1R1220260830FX00012345

UPI:
FX9M3RXQ2K18

ISDA Taxonomy Path:
Foreign Exchange / Forward / Deliverable / Forward

Underlier:
USD/CAD

NDF Flag:
N (Deliverable Forward — Section 3's callout)

Hedge Designation:
Y — hedges ABC's forecasted USD payable

Counterparty Country:
CA

Counterparty Classification:
Non-Financial Counterparty (NFC), per EMIR-style classification — see
Module 22 §9's hedging-exemption discussion for why this classification
matters for clearing obligations

Booking Entity:
Bank Of Toronto

Jurisdiction:
Canada / Other applicable regimes (e.g. if either party is US-connected)

Settlement Type:
Physical
```

> Note: மேலுள்ள LEI, UTI, UPI illustrative placeholders — [Module 18](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) (20-character LEI-prefixed UTI), [Module 21](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html) (12-character UPI) format மாதிரி. உண்மையான implementation-ல இவை authoritative sources / processes-ல இருந்து வரணும், booking system தானே assign பண்ணக்கூடாது.

---

## 9. Stage 4 — Valuation

### 9.1 Valuation ஏன் தேவை?

Trade book ஆனப்புறம், bank தெரிஞ்சுக்கணும்:

> "இந்த trade இன்னிக்கு எவ்வளவு மதிப்புள்ளது?"

Trade agree ஆன forward rate:

**1.3545 CAD/USD**

ஆனா market forward rate தினமும் மாறுது.

கொஞ்ச நாள் கழிச்சு, நடப்பு market 3-month USD/CAD forward rate இப்படி இருக்குன்னு வெச்சுக்கோங்க:

**1.3650**

1.3545-ல USD வாங்கற party-க்கு, இந்த original trade இப்போ மதிப்புள்ளதா மாறிடுச்சு. (1.3650-ல தான் market-ல வாங்கணும்; இவங்க 1.3545-லயே வாங்கறாங்க — மலிவு.)

---

## 10. FX Forward Valuation Concept

ஒரு எளிமையான economic கணக்கு:

```text
MTM ≈ Notional × (Current Forward Rate - Contract Forward Rate)
```

USD வாங்குபவருக்கு, discounting மற்றும் மற்ற market conventions-ஐ விட்டுட்டு.

வெச்சுக்குவோம்:

```text
Notional = USD 10,000,000

Contract Rate = CAD 1.3545

Current Forward Rate = CAD 1.3650
```

அப்புறம்:

```text
Difference
= 1.3650 - 1.3545
= CAD 0.0105/USD
```

தோராய மதிப்பு:

```text
USD 10,000,000 × CAD 0.0105
= CAD 105,000
```

ABC-ஓட position பொருளாதார ரீதியா சாதகமா இருக்கு.

---

## 11. இன்னும் துல்லியமான Valuation

ஒரு production valuation engine பொதுவா வெறுமனே இதை பயன்படுத்தாது:

```text
Notional × rate difference
```

அது கணக்கில எடுக்கறது:

- நடப்பு spot FX rate
- Forward FX points / forward curve
- மீதமுள்ள maturity
- Interest-rate curves
- Discount factors
- Currency
- Settlement convention
- Market data timestamp
- தேவையான இடத்தில Credit valuation adjustments
- Bank-ஓட framework பொறுத்து Funding / valuation adjustments

ஒரு எளிமையான conceptual formula:

```text
PV =
Expected future cash-flow difference
× Discount Factor
```

FX-forward pricing முறை, bank-ஓட valuation framework-ஐ பொறுத்தது.

### கூடுதல் உதாரணம் — Section 12-ஓட Present Value எப்படி வந்தது?

Section 10-ல undiscounted MTM = CAD 105,000. அதை 90 நாள் discount factor 0.985-ஆல பெருக்கணும்:

```text
PV = 105,000 × 0.985
   = CAD 103,425
```

அதாவது 90 நாள் கழிச்சு கிடைக்கப்போற CAD 105,000-ஓட *இன்றைய மதிப்பு* 103,425. (நாளைய ரூபாய் இன்றைய ரூபாயை விட குறைவு — அதான் discount.) Section 12 table-லயும், Section 25 regulatory record-லயும் வர "Valuation Amount 103,425" இந்த PV தான்; 105,000 அல்ல.

---

## 12. Valuation Data Fields

| Field | Example |
|---|---|
| Trade ID | BOT-FX-20260830-00012345 |
| Valuation Date | 2026-09-01 |
| Spot USD/CAD | 1.3490 |
| Forward USD/CAD | 1.3650 |
| Contract Rate | 1.3545 |
| USD Notional | 10,000,000 |
| Remaining Days | 90 |
| Discount Factor | 0.985 |
| Base Currency | CAD |
| Present Value | 103,425 |
| Valuation Currency | CAD |
| Valuation Timestamp | 2026-09-01 18:00:00 ET |
| Pricing Source | Market Data Service |
| Valuation Model | FX Forward PV |

---

## 13. Stage 5 — MTM

### 13.1 MTM-ன்னா என்ன?

**MTM = Mark-to-Market**

இது பதில் சொல்லுது:

> "இன்னிக்கு market விலைல இந்த trade-ஐ value பண்ணா, நாம எவ்வளவு லாபம் / நஷ்டம் அடைவோம்?"

வெச்சுக்குவோம்:

```text
Trade Rate = 1.3545
Market Forward Rate = 1.3650
Notional = USD 10M
```

எளிய MTM:

```text
(1.3650 - 1.3545) × 10M
= CAD 105,000
```

அதனால:

```text
ABC perspective: +CAD 105,000
Bank Of Toronto perspective: -CAD 105,000
```

ரெண்டு counterparties-க்கும் position எதிர்மறையா இருக்கறதால signs தலைகீழாகுது. (ஒருத்தர் லாபம் = இன்னொருத்தர் நஷ்டம் — zero-sum.)

---

## 14. காலப்போக்கில் MTM

இப்படி இருக்குன்னு வெச்சுக்குவோம்:

| Date | Market Forward Rate | USD வாங்குபவருக்கு தோராய MTM |
|---|---:|---:|
| 30-Aug | 1.3545 | 0 |
| 01-Sep | 1.3650 | +105,000 CAD |
| 30-Sep | 1.3400 | -145,000 CAD |
| 31-Oct | 1.3750 | +205,000 CAD |
| 30-Nov | 1.3900 | +355,000 CAD |

(ஒவ்வொரு வரிசையும் (market rate − 1.3545) × 10,000,000.) இதனாலதான் தினசரி valuation முக்கியம்.

ஒரு trade தன் வாழ்நாள்ல positive-ல இருந்து negative-க்கு மாற முடியும் — 30-Sep-ல ABC-க்கு −145,000 (அப்போ Bank Of Toronto-க்கு +145,000).

---

## 15. Stage 6 — Collateral

### 15.1 Collateral ஏன்?

OTC derivatives **counterparty credit risk**-ஐ உருவாக்குது.

Bank Of Toronto default ஆனா, அது ABC-க்கு பெரிய தொகை கொடுக்கணும்னு வெச்சுக்குவோம்.

இந்த risk-ஐ குறைக்க, parties ஒரு **CSA — Credit Support Annex**-ன் கீழ collateral பரிமாறலாம்.

Collateral இப்படி வரலாம்:

- Cash
- Government securities
- மற்ற eligible collateral

agreement-ஐ பொறுத்து.

---

## 16. Margin Concepts

ரெண்டு முக்கிய concepts:

### Variation Margin — VM

positions-ஓட நடப்பு மதிப்பு மாறுவதை பிரதிபலிக்கும்.

Conceptually:

```text
MTM increases
      ↓
Collateral requirement may increase
```

### Initial Margin — IM

Default ஆனப்புறம் positions-ஐ close / replace பண்ண தேவையான காலத்துல வரக்கூடிய எதிர்கால exposure-க்கு எதிரான பாதுகாப்பு.

உண்மையான பொருந்துதல் counterparties, regulatory requirements, documentation-ஐ பொறுத்தது.

---

## 17. Example Collateral Calculation

வெச்சுக்குவோம்:

```text
Portfolio MTM = +CAD 105,000 to ABC
Threshold = CAD 50,000
Minimum Transfer Amount = CAD 10,000
```

எளிமைப்படுத்தப்பட்ட collateral requirement:

```text
105,000 - 50,000
= CAD 55,000
```

CSA-வில் வரையறுத்த திசையையும் party-ஓட பார்வையையும் பொறுத்து, ABC collateral பெற / post பண்ண உரிமை பெறலாம். (இங்க ABC-க்கு MTM +105,000, அதாவது Bank Of Toronto தான் ABC-க்கு கடன்பட்டிருக்கு — அதனால பொதுவா Bank collateral *post* பண்ணும், ABC *பெறும்*.)

**MTA படி என்ன நடக்கும்?** Requirement 55,000 > MTA 10,000, அதனால call உண்மையிலயே தூண்டப்படுது. இதே requirement 8,000 மட்டும் இருந்திருந்தா (MTA-க்கு கீழ), எந்த transfer-உம் நடந்திருக்காது — சின்ன சின்ன தொகைக்கு தினமும் payment ஓடாம இருக்க தான் MTA.

Collateral processing பொதுவா portfolio / CSA அடிப்படையிலானது, ஒரு தனி trade கணக்கு அல்ல.

---

## 18. Collateral Data Fields

| Field | Example |
|---|---|
| CSA ID | CSA-BOT-ABC-001 |
| Counterparty | ABC12345 |
| Valuation Date | 2026-09-01 |
| Portfolio Value | CAD 105,000 |
| Threshold | CAD 50,000 |
| MTA | CAD 10,000 |
| Collateral Requirement | CAD 55,000 |
| Collateral Type | Cash |
| Collateral Currency | CAD |
| Margin Type | VM |
| Call Date | 2026-09-01 |
| Settlement Date | 2026-09-02 |
| Collateral Status | CALLED |
| Dispute Status | NONE |

---

## 19. Stage 7 — Settlement

Trade maturity-க்கு வருது:

**30-Nov-2026**

Contract சொல்றது:

```text
ABC buys USD 10M
ABC pays CAD 13,545,000
```

அதனால:

```text
ABC Canada Inc.                      Bank Of Toronto
       |                                   |
       |---------- CAD 13,545,000 -------->|
       |                                   |
       |<----------- USD 10M --------------|
```

FX forward physically settle ஆகுது. (CAD 13,545,000 → Bank; USD 10M → ABC. இந்த USD-ஐ ABC தன் US supplier-க்கு அனுப்பிடும்.)

---

## 20. Settlement Instructions

Settlement system-க்கு ரெண்டு currencies-க்கும் instructions வேணும்.

உதாரணம்:

### USD

```text
Currency: USD
Amount: 10,000,000
Settlement Account: USD account
SSI: ABC-USD-SSI-001
```

### CAD

```text
Currency: CAD
Amount: 13,545,000
Settlement Account: CAD account
SSI: ABC-CAD-SSI-001
```

SSI-ன்னா:

**Standard Settlement Instructions**

(அதாவது "எந்த bank account-க்கு, எந்த correspondent வழியா பணம் போகணும்"ன்னு முன்கூட்டியே ஒப்புக்கொண்ட நிலையான முகவரி.)

---

## 21. Settlement Data Fields

| Field | Example |
|---|---|
| Trade ID | BOT-FX-20260830-00012345 |
| Settlement ID | SET-FX-20261130-00012345 |
| Value Date | 2026-11-30 |
| Buy Currency | USD |
| Buy Amount | 10,000,000 |
| Sell Currency | CAD |
| Sell Amount | 13,545,000 |
| USD SSI | ABC-USD-SSI-001 |
| CAD SSI | ABC-CAD-SSI-001 |
| Settlement Status | PENDING |
| Payment Status | RELEASED |
| USD Settlement Status | SETTLED |
| CAD Settlement Status | SETTLED |
| Final Status | COMPLETE |

---

## 22. Settlement Risk

FX transactions settlement risk-ஐ கொண்டுவருது.

உதாரணமா:

```text
Bank Of Toronto sends USD
      ↓
ABC does not deliver CAD
```

இல்லன்னா:

```text
ABC sends CAD
      ↓
Bank Of Toronto does not deliver USD
```

அதனாலதான் FX settlement infrastructure, payment controls, settlement-risk management முக்கியம். (இதை "Herstatt risk"ன்னும் சொல்வாங்க — ஒரு பக்கம் பணம் போயிடுச்சு, மறுபக்கம் வரல. CLS போன்ற payment-versus-payment அமைப்புகள் இந்த risk-ஐ குறைக்கவே.)

---

## 23. Stage 8 — Regulatory Reporting

OTC derivatives பெரும்பாலும் இவற்றை பொறுத்து regulatory reporting தேவைகளுக்கு உட்படும்:

- Jurisdiction
- Counterparty type
- Product
- Trading location
- Legal entity
- Reporting regime

Capital Markets / RDP வேலைல நீங்க சந்திக்கக்கூடிய regimes:

- EMIR
- UK EMIR
- CFTC reporting
- Canadian derivatives reporting
- ASIC reporting
- MAS reporting
- HKMA reporting

சரியான fields-உம் rules-உம் jurisdiction, reporting regime-ஐ பொறுத்து மாறும்.

---

## 24. Regulatory Trade Data

ஒரு reporting record conceptually இதை கொண்டிருக்கலாம்:

```text
UTI
UPI
LEI of Reporting Counterparty
LEI of Other Counterparty
Trade ID
Product Classification
Asset Class
Notional
Notional Currency
Price
Price Currency
Trade Date
Effective Date
Maturity Date
Settlement Type
Direction
Execution Timestamp
Clearing Status
Collateralisation
Valuation Amount
Valuation Currency
Action Type
Event Type
Reporting Timestamp
```

---

## 25. Example Regulatory Record

Illustrative மட்டும்:

| Regulatory Field | Example |
|---|---|
| UTI | 5493001KJTIIGC8Y1R1220260830FX00012345 |
| UPI | FX9M3RXQ2K18 |
| ISDA Taxonomy Path | Foreign Exchange / Forward / Deliverable / Forward |
| Reporting Counterparty LEI | 5493001KJTIIGC8Y1R12 |
| Other Counterparty LEI | 549300ABCEXAMPLE45 |
| Product | FX Forward |
| Asset Class | Foreign Exchange |
| Currency Pair | USD/CAD |
| Notional | 10,000,000 |
| Notional Currency | USD |
| Price | 1.3545 |
| Price Currency | CAD |
| Trade Date | 2026-08-30 |
| Effective Date | 2026-08-30 |
| Maturity Date | 2026-11-30 |
| Settlement Type | Physical (Deliverable) |
| NDF Flag | N |
| Clearing Status | Not Cleared |
| Collateralisation | Bilateral |
| Hedging Exemption Claimed | Y — NFC hedging a commercial FX exposure |
| Valuation Amount | 103,425 |
| Valuation Currency | CAD |
| Action Type | NEW |
| Event Type | NEW_TRADE |
| Reporting Timestamp | 2026-08-30 10:35:00 ET |

மறுபடியும், இது ஒரு **conceptual example**, jurisdiction-specific reporting template அல்ல.

---

## 26. Trade Lifecycle Events

ஒரு trade-க்கு ஒரே ஒரு regulatory event மட்டும் இருக்கணும்னு இல்ல.

அதன் வாழ்நாள்ல events இவை:

```text
NEW
 |
 +---- AMENDMENT
 |
 +---- VALUATION UPDATE
 |
 +---- COLLATERAL UPDATE
 |
 +---- PARTIAL TERMINATION
 |
 +---- NOVATION
 |
 +---- EARLY TERMINATION
 |
 +---- MATURITY
 |
 +---- CANCEL / ERROR CORRECTION
```

அதனால ஒரு regulatory reporting platform இவற்றை வேறுபடுத்தணும்:

**Trade-level data** மற்றும் **event-level changes**.

**ஒரு எளிய உதாரணம்:** ABC ஒரு மாசம் கழிச்சு maturity-ஐ 30-Nov-ல இருந்து 15-Dec-க்கு மாத்தினா — அது புதுசா ஒரு trade அல்ல; அதே UTI-யோட `Action Type = MODIFY`, `Event Type = AMENDMENT` ஆக ஒரு புது report போகும். "புது trade"ன்னு தவறா அனுப்பினா, trade repository-ல duplicate ஆகிடும்.

---

## 27. End-to-End Data Flow

ஒரு typical architecture இப்படி இருக்கலாம்:

```text
             FRONT OFFICE
                  |
                  | Trade
                  v
          +---------------+
          | Trade Capture |
          +---------------+
                  |
                  v
          +---------------+
          | Confirmation  |
          +---------------+
                  |
                  v
          +---------------+
          |  Enrichment   |
          +---------------+
                  |
          +-------+-------+
          |               |
          v               v
   +-------------+   +-------------+
   | Valuation   |   | Regulatory  |
   | / Risk      |   | Enrichment  |
   +-------------+   +-------------+
          |               |
          v               v
        MTM          Regulatory
          |            Report
          v               |
      Collateral          v
          |          Trade Repository
          v
      Settlement
          |
          v
      Accounting
```

---

## 28. முக்கியமான System-of-Record Concepts

ஒரு பெரிய OTC derivatives platform-ல பொதுவா பல systems இருக்கும்.

| System | Primary Responsibility |
|---|---|
| Front Office | Execute/capture trade |
| Trade Store | Persist trade |
| Confirmation System | Confirm economics |
| Static Data | Counterparty/entity data |
| Reference Data | Product/instrument data |
| Market Data | Spot/forward curves |
| Valuation Engine | Calculate PV/MTM |
| Risk Engine | Exposure/risk |
| Collateral System | Margin/collateral |
| Settlement System | Payments |
| Regulatory Platform | Regulatory reporting |
| Trade Repository | Receive regulatory reports |
| Accounting | P&L/accounting |

---

## 29. Key Identifiers

RDP design பண்ணும்போது identifiers ரொம்ப முக்கியம்.

### Trade ID

Internal identifier:

```text
BOT-FX-20260830-00012345
```

### UTI

**Unique Transaction Identifier**

Regulatory reporting-க்கு ஒரு derivative transaction-ஐ நிலையா அடையாளம் காண.

### UPI

**Unique Product Identifier**

Derivative-ஓட வகை / product-ஐ அடையாளம் காண.

### LEI

**Legal Entity Identifier**

Legal entity-ஐ அடையாளம் காண.

ஒரு எளிமையான உறவு:

```text
LEI
 |
 +---- identifies Counterparty
 |
 +---- identifies Reporting Entity


UTI
 |
 +---- identifies Transaction


UPI
 |
 +---- identifies Product
```

---

## 30. Trade vs Product vs Counterparty

இந்த வேறுபாடு மிக முக்கியம்.

### Product

"எது trade ஆகுது?"

```text
FX Forward
```

### Counterparty

"யாரெல்லாம் involved?"

```text
Bank Of Toronto
ABC Canada Inc.
```

### Trade

"எந்த குறிப்பிட்ட transaction நடந்தது?"

```text
Trade ID:
BOT-FX-20260830-00012345
```

### Position

"Transactions-ஐ எல்லாம் கணக்கில் எடுத்தப்புறம் என்ன exposure மிச்சமிருக்கு?"

Data models design பண்ணும்போது இந்த வேறுபாடு படிப்படியா முக்கியமாகும். (Position பற்றி விரிவா: [Module 25 — Position](../docs/1-capital-markets/module-25-position-deep-dive.html).)

---

## 31. Example Canonical Trade Model

ஒரு normalized internal trade representation conceptually இப்படி இருக்கலாம்:

```json
{
  "tradeId": "BOT-FX-20260830-00012345",
  "productType": "FX_FORWARD",
  "assetClass": "FX",
  "tradeDate": "2026-08-30",
  "effectiveDate": "2026-08-30",
  "maturityDate": "2026-11-30",
  "buyCurrency": "USD",
  "buyAmount": 10000000,
  "sellCurrency": "CAD",
  "sellAmount": 13545000,
  "contractRate": 1.3545,
  "settlementType": "PHYSICAL",
  "ndfFlag": false,
  "isdaTaxonomy": {
    "assetClass": "FOREIGN_EXCHANGE",
    "baseProduct": "FORWARD",
    "subProduct": "DELIVERABLE",
    "transactionType": "FORWARD"
  },
  "hedge": {
    "isHedge": true,
    "hedgedExposure": "FORECASTED_USD_PAYABLE_2026_11_30"
  },
  "counterparty": {
    "lei": "549300ABCEXAMPLE45",
    "classification": "NFC"
  },
  "reportingEntity": {
    "lei": "5493001KJTIIGC8Y1R12"
  },
  "regulatory": {
    "uti": "5493001KJTIIGC8Y1R1220260830FX00012345",
    "upi": "FX9M3RXQ2K18"
  }
}
```

இது illustrative தான்; prescribed regulatory schema-வாக எடுக்கக்கூடாது.

> **Java architect-க்கு குறிப்பு:** இதை ஒரு `record TradeCanonical(...)` அல்லது immutable POJO-வா map பண்ணலாம்; `isdaTaxonomy`, `hedge`, `counterparty`, `regulatory` ஆகியவை nested value objects. Static contractual data-வை (Section 35) இதுல வைங்க; MTM, collateral status போன்ற dynamic data-வை தனி tables / events-ல.

---

## 32. Event-Based Model

RDP architecture-க்கு, event-oriented model பெரும்பாலும் பயனுள்ளது.

உதாரணம்:

```text
TradeCreated
     |
     v
TradeConfirmed
     |
     v
TradeEnriched
     |
     v
TradeValued
     |
     v
MTMUpdated
     |
     v
CollateralUpdated
     |
     v
SettlementInitiated
     |
     v
SettlementCompleted
     |
     v
RegulatoryReported
```

ஒவ்வொரு event-உம் இவற்றை கொண்டிருக்கலாம்:

```text
Event ID
Trade ID
UTI
Event Type
Event Timestamp
Source System
Previous State
New State
Version
Correlation ID
Payload
```

Distributed microservices architectures-ல இது குறிப்பா பயனுள்ளது.

---

## 33. Suggested Microservices

ஒரு நவீன microservices implementation-க்கு, lifecycle-ஐ இப்படி பிரிக்கலாம்:

```text
trade-service
      |
      +--> confirmation-service
      |
      +--> enrichment-service
      |
      +--> valuation-service
      |
      +--> risk-service
      |
      +--> collateral-service
      |
      +--> settlement-service
      |
      +--> regulatory-service
```

Supporting services:

```text
reference-data-service
counterparty-service
market-data-service
identifier-service
audit-service
notification-service
```

Kafka போன்ற ஒரு event bus trade lifecycle events-ஐ distribute பண்ணலாம்.

---

## 34. Example Kafka Event Flow

```text
trade.created
      |
      +----> confirmation-service
      |
      +----> enrichment-service
      |
      +----> risk-service
      |
      +----> regulatory-service
```

பின்னர்:

```text
valuation.updated
      |
      +----> risk-service
      +----> collateral-service
      +----> regulatory-service
```

அப்புறம்:

```text
settlement.completed
      |
      +----> accounting-service
      +----> regulatory-service
      +----> audit-service
```

**ஏன் இந்த fan-out நல்லது?** `valuation.updated` ஒரு தடவை publish ஆனா, risk-service, collateral-service, regulatory-service மூணும் தனித்தனியா, தங்கள் வேகத்தில் consume பண்ணும். collateral-service கொஞ்சம் மெதுவா இருந்தாலும் regulatory-service-ஐ தடுக்காது. ஒரு synchronous `updateTrade()` call-ஆ இருந்தா, ஒண்ணு fail ஆனா எல்லாமே நிக்கும்.

---

## 35. Lifecycle முழுக்க என்ன மாறுது?

Original trade-ஐ பாருங்க:

```text
USD 10M
@ CAD 1.3545
Maturity 30-Nov-2026
```

Core economics பொதுவா அப்படியே இருக்கும்.

ஆனா dynamic fields மாறும்:

```text
Market Spot
Forward Curve
PV
MTM
Exposure
Collateral
Settlement Status
Reporting Status
```

அதனால, ஒரு RDP இவற்றை வேறுபடுத்தணும்:

### Static / contractual data

```text
Trade ID
Product
Notional
Currency
Contract Rate
Trade Date
Maturity Date
Counterparties
```

இதுல இருந்து:

### Dynamic data

```text
Valuation
MTM
Market Data
Exposure
Collateral
Settlement Status
Reporting Status
```

---

## 36. முழு Example Timeline

### 30-Aug-2026

```text
10:32
Trade executed

10:33
Trade captured

10:34
Trade confirmed

10:35
Counterparty / product enrichment completed

10:36
Regulatory identifiers generated

End of day
Trade valued
MTM calculated
```

### 01-Sep-2026

```text
Market moves
      ↓
Forward curve changes
      ↓
Trade revalued
      ↓
MTM changes
      ↓
Collateral requirement recalculated
      ↓
Collateral call generated if required
```

### 30-Nov-2026

```text
Maturity
    ↓
Settlement instructions validated
    ↓
USD payment
    +
CAD payment
    ↓
Settlement completed
    ↓
Trade closed
    ↓
Final regulatory lifecycle event
```

### கூடுதல் உதாரணம் — ஒரே trade, எல்லா systems-ல ஒரே நாள் (01-Sep) பார்வை

| நேரம் | System | என்ன நடக்குது | எண் |
|---|---|---|---|
| 17:00 | Market Data Service | 3M forward 1.3650, spot 1.3490 snapshot | — |
| 18:00 | Valuation Engine | PV கணக்கு | 105,000 × 0.985 = **103,425 CAD** |
| 18:05 | Risk / MTM | Bank-க்கு நஷ்டம், ABC-க்கு லாபம் | Bank: −103,425 |
| 18:10 | Collateral System | Threshold 50,000 கழிச்சு requirement | ≈ 53,425 *(valuation 103,425 அடிப்படையில்; Section 17-ல் எளிமைக்கு 105,000 → 55,000)* |
| 18:15 | Collateral System | Call உருவாக்கம் (MTA 10,000-ஐ தாண்டியதால்) | Status = CALLED |
| 19:00 | Regulatory Platform | Valuation update report (Action = VALU) | Valuation Amount 103,425 |

---

## 37. Lifecycle Stage வாரியா மிக முக்கிய Fields

| Stage | Key Data |
|---|---|
| Trade Capture | Trade ID, product, notional, currencies, rate, dates |
| Confirmation | Confirmation ID, contractual terms, parties |
| Enrichment | LEI, UTI, UPI, classification, jurisdiction |
| Valuation | Spot, forward curve, discount factor, PV |
| MTM | Market value, valuation currency, P&L |
| Collateral | CSA, VM/IM, threshold, MTA, collateral |
| Settlement | SSI, payment amount, value date, settlement status |
| Regulatory | UTI, UPI, LEIs, action/event, valuation, reporting fields |

---

## 38. பெரிய படம் (The Big Picture)

மிக முக்கியமான mental model:

```text
                  TRADE
                    |
                    v
             What did we do?
                    |
                    v
             CONFIRMATION
                    |
                    v
             What did both
             parties agree?
                    |
                    v
              ENRICHMENT
                    |
                    v
             Who/what/how
             for downstream
             processing?
                    |
                    v
              VALUATION
                    |
                    v
             What is it
             worth today?
                    |
                    v
                 MTM
                    |
                    v
             Who is gaining
             or losing?
                    |
                    v
              COLLATERAL
                    |
                    v
             How much credit
             protection?
                    |
                    v
              SETTLEMENT
                    |
                    v
             What actually
             gets exchanged?
                    |
                    v
        REGULATORY REPORTING
                    |
                    v
             What must be
             reported to the
             regulator/repository?
```

---

## 39. நினைவில் வைக்க வேண்டிய Capital Markets Vocabulary

| Term | Meaning |
|---|---|
| OTC | Over The Counter |
| Derivative | Contract whose value derives from underlying |
| Forward | Obligation to transact in future at agreed price |
| Option | Right, not obligation |
| Call | Right to buy |
| Put | Right to sell |
| Strike | Option exercise price |
| Premium | Price paid for option |
| Spot | Current market price |
| Forward Rate | Agreed/market future FX rate |
| Notional | Reference amount used for calculation |
| Maturity | Contract end date |
| MTM | Current market value |
| PV | Present value |
| VM | Variation Margin |
| IM | Initial Margin |
| CSA | Credit Support Annex |
| SSI | Standard Settlement Instructions |
| LEI | Legal Entity Identifier |
| UTI | Unique Transaction Identifier |
| UPI | Unique Product Identifier |
| Trade Repository | Infrastructure receiving regulatory reports |

---

## 40. அடுத்த படி — பரிந்துரை

இந்த FX Forward example புரிஞ்சப்புறம், இதே lifecycle-ஐ ஒரு **Interest Rate Swap (IRS)**-க்கு படிங்க — [Module 19](../docs/1-capital-markets/module-19-interest-rate-swap-deep-dive.html), இது ஏற்கனவே ஒரு 5Y CAD IRS-ஐ 20 quarterly settlements வழியா இதே அளவு field-by-field விவரத்தோட கூட்டிட்டுப் போகுது.

IRS lifecycle, இந்த module சொன்னவற்றுக்கு மேல கூடுதல் concepts-ஐ கொண்டுவருது:

```text
IRS
 |
 +--> Fixed Rate
 |
 +--> Floating Rate
 |
 +--> Reference Rate
 |       |
 |       +--> CORRA
 |       +--> SOFR
 |       +--> SONIA
 |
 +--> Reset Dates
 |
 +--> Payment Dates
 |
 +--> Accrual Period
 |
 +--> Day Count Convention
 |
 +--> Compounding
 |
 +--> Discounting
 |
 +--> MTM
 |
 +--> Collateral
 |
 +--> Settlement
 |
 +--> Regulatory Reporting
```

IRS example **CORRA**-வோட நேரடியா இணையும், இது பொதுவா Rates-desk regulatory data platform (RDP) வேலைக்கு மையமானது.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | ABC Canada Inc.-க்கு USD விற்று CAD வாங்கும் forward; booking / reporting entity — இதன் systems வழியா module முழுக்க walkthrough |
| ABC Canada Inc. | Buy-side client — 30-Nov-2026-ல கட்டவேண்டிய உண்மையான USD 10,000,000 payable-ஐ hedge பண்ணும் ஒரு Canadian resident (Section 2) |
| ஒரு US supplier | ABC இறுதியா USD 10,000,000 கடன்பட்டிருக்கற (பெயரிடப்படாத) தரப்பு — இந்த trade-க்கு வெளியே; ABC-க்கு hedge ஏன் தேவைன்னு காட்டும் வணிக காரணம் |
| Trade Repository / Regulatory Platform | செறிவூட்டப்பட்ட, valuation செய்யப்பட்ட, event-tag செய்யப்பட்ட trade record-ஐ பெறும் downstream systems (Sections 23–28) |

---

## Recap Quiz

1. Section 3-ஓட callout-ஐ பயன்படுத்தி: USD/CAD forward ஏன் கிட்டத்தட்ட எப்பவும் NDF இல்லாம Deliverable Forward? அந்த வேறுபாட்டை trade record-ல எந்த field capture பண்ணுது?
2. Section 8-ஐ பயன்படுத்தி: இந்த module-ஓட enrichment சேர்க்கும், ஆனா raw trade-capture record-ல (Section 5.2) இல்லாத ரெண்டு fields-ஐ சொல்லி, ஒவ்வொண்ணும் எதுக்கு பதில்னு விளக்குங்க.
3. Sections 9–12-ஐ பயன்படுத்தி: execution-க்கு பிறகு contractual terms (rate, notional, dates) மாறவே மாறாதப்பவும் trade-ஓட MTM ஏன் மாறுது?
4. Section 15-ஐ பயன்படுத்தி: CSA-ன் கீழ collateral post பண்றது எந்த குறிப்பிட்ட risk-ஐ குறைக்குது? inception-ல MTM பூஜ்யமா இருக்கற trade-க்கு கூட அந்த risk ஏன் இருக்கு?
5. Section 19 vs Section 3 callout-ஐ பயன்படுத்தி: இதே trade ஒரு currency-pair NDF-ஆ book ஆகியிருந்தா, settlement எப்படி இருக்கும்?
6. Sections 27–34-ஐ பயன்படுத்தி: ஒரே monolithic "trade-ஐ update பண்ணு" service call-ஐ விட, இந்த lifecycle-க்கு event-based (Kafka-style) architecture ஏன் பொருத்தம்?
7. Section 8-ல இருக்கும் enriched trade-ஐ பயன்படுத்தி: ISDA Taxonomy Path field, UPI field மட்டும் சொல்லாத என்ன ஒரு downstream system-க்கு சொல்லுது?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. CAD, மற்ற G10 currencies மாதிரியே முழுசா convertible, capital-account restrictions இல்லை; அதனால maturity-ல ரெண்டு currencies-உம் எந்த சிறப்பு dealer authorization-உம் residency test-உம் இல்லாம physically பரிமாறப்படும். `NDF Flag` field (Section 8-ஓட enriched trade-ல `N`) இதை நேரடியா capture பண்ணுது — INR போன்ற முழுசா convertible இல்லாத currency-க்கு அதே field `Y`-ஆ மாறும்.**

Section 3-ஓட callout இதை வெளிப்படையா சொல்லுது — G10 default (CAD) vs INR example, onshore/offshore நிலை Deliverable vs NDF-ஐ தீர்மானிக்குது.

**2. இவற்றில் ஏதாவது ரெண்டு: LEIs ("ரெண்டு parties-உம் எந்த legal entities"), UTI ("regulatory pairing-க்கு இது எந்த குறிப்பிட்ட transaction"), UPI ("இது எந்த product template-ஐ சேர்ந்தது"), ISDA Taxonomy Path ("இந்த product எப்படி classify ஆகுது"), அல்லது Hedge Designation ("இந்த trade ஒரு ஈடு செய்யும் commercial exposure-ஓட முறையா இணைக்கப்பட்டதா"). இவற்றில் எதுவும் raw Section 5.2 trade-capture record-ல இல்லை — trade-ஓட அடிப்படை economics தெரிஞ்சப்புறம் downstream-ல சேர்க்கப்படுது.**

Section 7-ஓட list-உம் Section 8-ஓட enriched-trade example-உம், இந்த fields enrichment-க்கு பிறகுதான் தோன்றுது, capture-ல இல்லைன்னு காட்டுது.

**3. ஏன்னா MTM, trade-ஓட சொந்த contract rate-க்கு எதிராக இல்லாம, *இன்றைய* market forward rate-க்கு எதிரா value-வை அளக்குது — contract rate (1.3545) trade date-ல என்றென்றைக்கும் fix; ஆனா market-ஓட சொந்த 3-month forward rate தினமும் மாறுது (Section 14-ஓட table trade-ஓட வாழ்நாள்ல அது 1.3400 முதல் 1.3900 வரை போவதைக் காட்டுது). அதனால MTM ரெண்டு விஷயங்களின் function: fixed contract terms, எப்பவும் மாறும் வெளிப்புற market — ரெண்டாவது மட்டும் தான் நகருது.**

Section 10-ஓட formula (`MTM ≈ Notional × (Current Forward Rate − Contract Forward Rate)`) இதை தெளிவாக்குது — Contract Forward Rate அதுல ஒரு constant, Current Forward Rate தான் ஒரே variable.

**4. CSA-ன் கீழ collateral, counterparty credit risk-ஐ குறைக்குது — trade-ஓட MTM-ல தற்போது கடன்பட்டிருக்கற party default ஆனா பணம் கட்டாம போகும் risk. Inception-ல MTM zero-வா இருந்தாலும் இந்த risk இருக்கு, ஏன்னா trade-ஓட value அதன் 3 மாச வாழ்க்கையில (Section 14-ஓட timeline) எந்த திசைலயும் கணிசமா ஊசலாடலாம் — இன்னிக்கு பூஜ்ய மதிப்புள்ள trade maturity-க்குள் ஒரு தரப்புக்கு CAD 355,000 மதிப்பாகலாம்; collateral இன்றைய snapshot-ஐ அல்ல, இந்த எதிர்கால ஊசலாட்டத்தை பாதுகாக்குது.**

Section 15-ஓட framing-உம் Section 16-ஓட Variation Margin concept-உம் collateral-ஐ ஒரு static day-one எண்ணுடன் அல்ல, காலப்போக்கில் MTM இயக்கத்திலிருந்து பாதுகாப்போடு இணைக்குது.

**5. maturity-ல ஒரே ஒரு net USD cash payment மட்டும் நகரும் — contract rate (1.3545)-க்கும் fixing date-ல வெளியிடப்பட்ட reference fixing rate-க்கும் இடையிலான வித்தியாசத்தை USD notional-ஆல பெருக்கி — CAD எதுவும் பரிமாறப்படாது. Section 19-ஓட ரெண்டு-பக்க physical exchange (ஒரு பக்கம் CAD 13,545,000, மறுபக்கம் USD 10M) NDF structure-ல நடக்கவே நடக்காது.**

Section 3-ஓட callout-உம் Section 19-ஓட physical settlement diagram-உம் சேர்த்து படிச்சா சரியா இந்த வேறுபாட்டை சொல்லுது — நடைமுறையில CAD-ஓட full convertibility காரணமா USD/CAD trade இப்படி book ஆகவே ஆகாதுன்னாலும்.

**6. ஏன்னா வெவ்வேறு downstream consumers (risk, collateral, regulatory reporting, accounting) ஒரே underlying மாற்றத்துக்கு (எ.கா. ஒரு valuation update) ஒவ்வொண்ணும் தனித்தனியா, தங்கள் சொந்த வேகத்தில், ஒன்றுக்கொண்று காத்திருக்காம, ஒரு ஒற்றை "trade service"-உடன் இறுக்கமா இணையாம எதிர்வினையாற்றணும் — Section 34-ஓட Kafka flow, ஒரு `valuation.updated` event risk-service, collateral-service, regulatory-service ஆகியவற்றுக்கு ஒரே நேரத்துல fan-out ஆவதைக் காட்டுது; ஒரு synchronous update call இவற்றை ஒரே blocking chain-க்குள் தள்ளிடும்.**

Section 33-ஓட microservices decomposition-உம் Section 34-ஓட event-fan-out diagram-உம் இதை நேரடியா விளக்குது.

**7. UPI, இந்த trade பொருந்தும் குறிப்பிட்ட வெளியிடப்பட்ட product-reference-data record எது என்பதை மட்டும் அடையாளம் காட்டுது (DSB-ல இருந்து பெறப்படும் அல்லது கோரப்படும் 12-character code) — classification structure-ஐ தானே சொல்லாது. ISDA Taxonomy Path (`Foreign Exchange / Forward / Deliverable / Forward`) என்பது UPI-ஓட reference data கட்டப்பட்ட மனிதர் படிக்கக்கூடிய classification — ஒரு downstream system அல்லது வாசகருக்கு lookup இல்லாமலே asset class, base product, sub-product, transaction type என்னன்னு உடனே சொல்லுது.**

[Module 21](../docs/2-reporting/module-21-isda-product-taxonomy-deep-dive.html) §5 இதே வம்சாவளியை சொல்லுது — taxonomy "grammar", UPI reference data அதிலிருந்து கட்டப்பட்ட "dictionary".
