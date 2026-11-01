# Systems Landscape — Front, Middle & Back Office (Canada, US, UK, EU) (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி).
> இது ஒரு **reference module** — Modules 1–3-க்கு பக்கத்துல நிக்குது. Trade lifecycle-ஐ ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html)) நிஜமா ஓட்டுற **systems, message formats, market infrastructure** பேரை நான்கு jurisdictions-ல சொல்லுது: **Canada, United States, United Kingdom, European Union**.
> English மூலம்: [Systems Landscape](../docs/1-capital-markets/systems-landscape.html)

---

## 1. இந்த Module ஏன்? — Modules 1–3-க்கு பின்னாடி இருக்கும் Systems

[Module 1](../docs/1-capital-markets/module-01-fundamentals.html) Front/Middle/Back Office-ஐ மூணு *role*-ஆ சொன்னுச்சு. [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) ஒன்பது-stage trade lifecycle-ஐ ஒரு *process*-ஆ சொன்னுச்சு. ஆனா அந்த process-ஐ உண்மையில ஓட்டுற **systems** பேரை ரெண்டும் சொல்லல; "T+2"ங்கறது ஒரே உலகளாவிய விதி இல்ல — அது ஒவ்வொரு நாட்டுலயும், ஒவ்வொரு asset class-லயும் வேற வேற market infrastructure அமல்படுத்தும் *convention்*ன்னும் சொல்லல. இந்த module அந்த இடைவெளியை நிரப்புது: Canada, US, UK, EU ஒவ்வொண்ணுக்கும், ஒரு trade உண்மையில எந்த systems வழியா போகுது, அவற்றை இணைக்கும் message formats என்ன, அந்த market இப்போ எந்த settlement cycle-ல ஓடுது.

```text
WHERE THIS MODULE SITS
========================================================================

Module 1  ->  Front/Middle/Back Office as ROLES  (who does what)
Module 3  ->  The nine-stage trade lifecycle as a PROCESS (what happens, in order)
THIS MODULE -> The actual SYSTEMS + MARKET INFRASTRUCTURE that process runs on,
               country by country, plus who regulates each one
```

**Takeaway:** [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) "Settlement"-ல *என்ன* நடக்குதுன்னு சொன்னா, இந்த module ஒவ்வொரு market-லயும் *எந்த system* அதை செய்யுதுன்னு சொல்லுது — Canada-ல CDSX, US-ல DTC, UK-ல CREST, EU-ல T2S மேல Euroclear/Clearstream — இவை ஒண்ணுக்கொண்ணு மாத்தக்கூடிய ஒரே பொருளின் வேறு பெயர்கள் இல்ல; உண்மையிலயே வேறு platforms, வேறு rulebooks, வேறு regulators, (Section 8 காட்டுறபடி) வேறு settlement cycles.

---

## 2. பொதுவான Front / Middle / Back Office Architecture

**Definition:** எந்த நாடா இருந்தாலும், ஒவ்வொரு dealing bank-உம் அதே மூன்று-அடுக்கு system architecture-ஐ ஓட்டுது. நாடு நாடா மாறுவது (Sections 4–7) — ஒவ்வொரு பெட்டில எந்த vendor/platform உட்காருது, Back Office அடுக்கு எந்த market infrastructure-ல இணையுது என்பது.

```text
GENERIC SYSTEMS ARCHITECTURE  —  same shape everywhere
========================================================================

FRONT OFFICE
  +-------------------+        +-------------------+
  |   OMS              |  FIX   |   EMS               |
  |  (Order Mgmt Sys)   |------->|  (Execution Mgmt Sys)|----> Exchange / Venue /
  |  captures the ORDER |        |  routes the EXECUTION |     Interbank market
  +-------------------+        +-------------------+
            |
            | Trade Capture (Module 3 §3)
            v
MIDDLE OFFICE
  +---------------------+      +----------------------+
  |  Confirmation/Matching|<---->|  Risk & Valuation Engine|
  |  platform (Module 3 §4-5)|  |  (Murex, Calypso, etc.)  |
  +---------------------+      +----------------------+
            |
            | Clearing instruction, if applicable (Module 3 §6)
            v
BACK OFFICE
  +-----------+     +-----------+     +------------------+
  |    CCP     |---->|    CSD     |---->|  Custodian /       |
  | (clearing) |     |(settlement)|     |  Cash correspondent|
  +-----------+     +-----------+     +------------------+
            |
            v
  +---------------------------+
  |  Regulatory Reporting Engine |----> Trade Repository / Regulator (Module 3 §9)
  +---------------------------+
```

| அடுக்கு | System வகை | வழக்கமான message formats | உருவாக்குவது |
|---|---|---|---|
| Front Office | OMS, EMS | FIX (Financial Information eXchange) | Order, பின்னர் Execution |
| Middle Office | Confirmation/Matching platform, Risk & Valuation engine | FpML (derivatives), proprietary / ISO 20022 (cash) | Confirm ஆன, match ஆன trade; MTM / risk எண்கள் |
| Back Office | CCP, CSD, Custodian systems, Regulatory reporting engine | SWIFT MT5xx / ISO 20022 MX, proprietary CSD protocols | Settle ஆன position, reconcile ஆன books, file பண்ணின regulatory report |

**Takeaway:** கீழே Sections 4–7-ல நாம பார்க்கப்போற ஒவ்வொரு system-உம் இந்த ஐந்து பெட்டிகளில ஒண்ணோட நாடு-குறிப்பிட்ட implementation. ஒரு system *எந்த பெட்டில* இருக்குன்னு தெரிஞ்சா, அதன் documentation படிக்கறதுக்கு முன்னாடியே அது கிட்டத்தட்ட என்ன செய்யும்னு ஊகிக்கலாம்.

**Java architect-க்கு ஒப்பீடு:** இது ஒரு web app-ஓட Controller → Service → Repository அடுக்கு மாதிரி — framework (Spring/Quarkus) வேறா இருந்தாலும் shape ஒண்ணுதான். இங்க OMS/EMS = controller, Murex/Calypso = service layer, CCP/CSD = persistence + ledger.

---

## 3. T, T+1, T+2 — இந்த Systems முழுக்க கடிகாரம் எங்க இருக்கு

**Definition:** [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §6, T+n = Trade Date-ல இருந்து Settlement Date வரை business days எண்ணிக்கைன்னு சொன்னுச்சு. அது சொல்லாதது: **எந்த system உண்மையில அந்த கடிகாரத்தை அமல்படுத்துது**, மேலும் நாட்களின் எண்ணிக்கை ஒரு *market / product convention*, இயற்பியல் விதி இல்ல — நாடுகள், ஒரே நாட்டுக்குள்ள வேறு asset classes வேறு cycles ஓட்டுது.

```text
WHO OWNS THE CLOCK, AT EACH STAGE
========================================================================

  Trade Date (T)                                    Settlement Date (T+n)
       |                                                      |
       v                                                      v
  FRONT OFFICE          MIDDLE OFFICE           BACK OFFICE (the CSD)
  records T at             confirms/matches         is the system that
  Execution Time            same day, T                ENFORCES T+n — it
  (Module 3 §2)              (Module 3 §4-5)             will not release
                                                          securities/cash
                                                          before T+n, and
                                                          flags a FAIL
                                                          (Module 3 §13)
                                                          if T+n passes
                                                          without completion
```

| Cash / Derivative | Market | Product | Settlement cycle (as of 2026) |
|---|---|---|---|
| CASH | Canada | Equities | **T+1** (aligned with the US, since 27 May 2024) |
| CASH | Canada | Government / corporate bonds | **T+1** or **T+2**, depending on the specific bond |
| CASH | United States | Equities | **T+1** (since 28 May 2024) |
| CASH | United States | US Treasuries | **T+1** |
| CASH | United Kingdom | Gilts (government bonds) | **T+1** (gilts have long settled faster than equities) |
| CASH | United Kingdom | Equities | **T+2** (a move to T+1 is targeted for **October 2027**) |
| CASH | European Union | Equities & bonds | **T+2** (a coordinated EU/UK/Switzerland move to **T+1 is targeted for October 2027**) |
| DERIVATIVE | OTC derivatives (any market) | Interest Rate Swaps, CDS, etc. | No notional settlement at all — only margin/cash flows ([Module 1](../docs/1-capital-markets/module-01-fundamentals.md) §8, [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.md) §11) |

**Takeaway:** "T+2" என்பது அப்பப்போ விதிவிலக்குகளோட உலகளாவிய default இல்ல — 2026-ல equities-க்கு அது ஏற்கனவே *சிறுபான்மை* நிலை (Canada, US ரெண்டும் 2024-ல T+1-க்கு போயிடுச்சு), UK/EU 2027-க்கு T+1-ஐ நோக்கி நகருது. "settlement = trade date + 2"ன்னு hard-code பண்ற reporting system, உண்மையான trades-ல பெரும்பகுதிக்கு ஏற்கனவே தவறு.

**Java குறிப்பு:** `tradeDate.plusDays(2)` எழுதாதீங்க. `SettlementCalendar.settlementDate(market, assetClass, tradeDate)` மாதிரி (market, product)-ஐ key-ஆ கொண்ட lookup + holiday calendar தான் சரியான வடிவம்.

---

## 4. Canada — Systems & Data Flow

### Front & Middle Office

| Function | வழக்கமான systems |
|---|---|
| Order Management (OMS) | Bloomberg AIM, Charles River IMS, Eze OMS |
| Execution Management (EMS) | FlexTrade, Bloomberg EMSX, Charles River |
| Middle Office risk/valuation | Murex, Calypso (Adenza), GMI (SS&C) |
| Confirmation/matching (cash securities) | CDS-ன் **CDSX** trade-matching module, DTCC-ன் **CTM** |

### Back Office — Market Infrastructure

- **CDS (Canadian Depository for Securities)** **CDSX**-ஐ நடத்துது — Canada-ன் central securities depository, settlement system; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7-ல சொன்ன CSD role-ன் Canadian இணை. CDS, **TMX Group**-ஓட subsidiary.
- **CDCC (Canadian Derivatives Clearing Corporation)** — இதுவும் TMX Group subsidiary — exchange-traded derivatives-க்கும், 2012-ல இருந்து fixed income repo market-க்கும் CCP.
- Custodians (எ.கா. RBC Investor Services, CIBC Mellon) client positions-ஐ வெச்சிருந்து, clients சார்பா CDSX-ல இணையுறாங்க — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7-ல பொதுவா சொன்னது போலவே.

### Worked example — Bank Of Toronto Canadian equity வாங்குது, T+1

```text
CANADA  —  DATA FLOW FOR ONE EQUITY TRADE
========================================================================

  09:31:05  ORDER captured in Bloomberg AIM (OMS)
              Client: Maple Leaf Pension Fund
              BUY 50,000 shares, Loblaw Companies Ltd. (TSX: L)

  09:31:07  Routed via FIX 4.4 to FlexTrade (EMS)
              Order sent to the Toronto Stock Exchange (TSX)

  09:31:09  EXECUTION on TSX @ CAD 178.40
              Trade Value = 50,000 x 178.40 = CAD 8,920,000

  09:31:09  TRADE CAPTURE — Trade ID BOT-CA-2026-004471

  09:45:00  CONFIRMATION/MATCHING via CDSX matching module
              -> Status: MATCHED

  Settlement Date = T+1 = next business day
              CDSX performs DvP: shares move from Bank Of Toronto's
              CDS participant account to the client's custodian
              account; cash (CAD 8,920,000) moves simultaneously.

  Trade Repository reporting (if OTC derivative — not applicable
  here, this is an exchange-traded equity, so the TSX's own trade
  feed is the record, per Module 1 §5)
```

**Takeaway:** Canada-வும் US-உம் ஒரே T+1 equity cycle-ஐ ஓட்டுது; 2024 May-ல ஒண்ணா மாறியதே காரணம் — Canadian order flow-ல பெரும்பகுதியும் dual-listed stocks-உம் US markets-ஓட தொடர்புடையது; T+1/T+2 பொருந்தாமை ஒவ்வொரு cross-border trade-லயும் settlement உராய்வை உருவாக்கியிருக்கும்.

### கூடுதல் உதாரணம் — அதே trade, அடுத்த நாள் காலை என்ன நடக்கும்?

மேலுள்ள Loblaw trade (Trade Date Monday) Tuesday காலை எப்படி முடியுது:

| நேரம் | System | என்ன நடக்குது | எண் |
|---|---|---|---|
| Mon 09:45 | CDSX matching | Trade MATCHED | Trade Value CAD 8,920,000 |
| Mon இரவு | Back Office | Settlement instruction தயார் | 50,000 shares ↔ CAD 8,920,000 |
| Tue காலை | CDSX | DvP — shares + cash ஒரே நேரத்துல | Bank-ஓட CDS participant account → custodian account |
| Tue மதியம் | Reconciliation | Books vs CDSX vs custodian பொருந்துதா? | Break = 0 |
| Tue | Regulatory | Exchange-traded equity, அதனால TR report இல்ல (TSX feed தான் record) | — |

எதாவது ஒரு படியில fail ஆனா ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §13) CDSX அதை "FAIL"ன்னு flag பண்ணும் — அதான் "கடிகாரத்தை அமல்படுத்தும் system".

---

## 5. United States — Systems & Data Flow

### Front & Middle Office

| Function | Typical systems |
|---|---|
| Order Management (OMS) | Charles River IMS, Bloomberg AIM, Eze OMS |
| Execution Management (EMS) | Bloomberg EMSX, FlexTrade, Instinet |
| Middle Office risk/valuation | Murex, Calypso, Numerix (derivatives pricing) |

### Back Office — Market Infrastructure

- **DTCC (Depository Trust & Clearing Corporation)** — US post-trade infrastructure-ஓட குடை parent:
  - **NSCC (National Securities Clearing Corporation)** — equity, corporate bond trades-ஐ clear பண்ணி net பண்ணும் CCP.
  - **FICC (Fixed Income Clearing Corporation)** — US Treasuries, repo market-க்கான CCP (பெரும்பாலான Treasury cash, repo trades-க்கு கட்டாய central clearing 2025–2026 வழியா படிப்படியா அமலுக்கு வந்தது).
  - **DTC (Depository Trust Company)** — securities-ஐ book-entry வடிவில வெச்சிருந்து இறுதி DvP settlement செய்யும் CSD; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7-ன் CSD role-க்கு US இணை.
- **Fedwire** (Federal Reserve நடத்துவது) — பெரிய தொகை, அதே-நாள் payments-க்கான real-time gross settlement system — DTC-ஓட securities-பக்க settlement-க்கு கீழே இருக்கும் "cash rail".
- **Swap Data Repositories (SDRs)** — எ.கா. **DTCC GTR**, **ICE Trade Vault** — CFTC விதிகளின் கீழ OTC derivatives report ஆகும் US trade repositories (Trade Repository concept-ன் US அமலாக்கம்; [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §5, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12).

### Worked example — Bank Of Toronto-வின் US desk Apple shares வாங்குது, T+1

```text
UNITED STATES  —  DATA FLOW FOR ONE EQUITY TRADE
========================================================================

  10:02:00  ORDER captured in Charles River IMS (OMS)
              BUY 5,000 shares, Apple Inc. (NASDAQ: AAPL)

  10:02:03  Routed via FIX to Bloomberg EMSX (EMS) -> NASDAQ

  10:02:05  EXECUTION @ USD 227.15
              Trade Value = 5,000 x 227.15 = USD 1,135,750

  10:02:05  TRADE CAPTURE — Trade ID BOT-US-2026-009213

  10:15:00  CONFIRMATION/MATCHING via DTCC CTM -> MATCHED

  Trade submitted to NSCC for CLEARING (novation + multilateral
  netting across all of Bank Of Toronto's trades that day —
  NSCC nets thousands of trades down to one net settlement
  obligation per security, per participant, per day)

  Settlement Date = T+1
              DTC performs DvP: shares move in DTC's book-entry
              system; cash leg settles via the DTCC/Fedwire cash
              settlement process.
```

**Takeaway:** இந்த நாலு markets-ல, ஒரு நாள் முழுக்க ஒரே security-ல ஒரு firm-ஓட trades-ஐ எதுவும் settle ஆகுறதுக்கு முன்னாடி ஒரு CCP (NSCC) *net* பண்ணுவது US மட்டும்தான் — மேலே Bank Of Toronto-ஓட 5,000-share buy, அன்னிக்கு desk செய்த மற்ற Apple trades-ஓட net ஆகலாம்; அதனால உண்மையான DTC settlement instruction எந்த ஒரு execution-ஐ விடவும் வேறு, சிறிய quantity-ஆ இருக்கலாம். அதனாலதான் "ஒரு trade = ஒரு settlement instruction"ன்னு எதிர்பார்க்கும் reporting system US market-ல குறிப்பா உடையுது.

### கூடுதல் உதாரணம் — Netting எண்களில்

அதே நாள் Bank Of Toronto-ஓட US desk Apple-ல இப்படி செஞ்சதா வெச்சுக்குவோம்:

| Trade | Side | Quantity | Price (USD) |
|---|---|---|---|
| A1 | Buy | 5,000 | 227.15 |
| A2 | Sell | 2,000 | 227.50 |
| A3 | Buy | 1,000 | 227.30 |

- Gross: 6,000 வாங்கல், 2,000 விற்பனை — 3 trades, 3 settlement-ஆ இருந்திருக்கும் (netting இல்லாத market-ல).
- NSCC net: **6,000 − 2,000 = 4,000 shares நிகர வாங்கல்** → DTC-ல ஒரே settlement obligation.
- Cash net: 5,000×227.15 + 1,000×227.30 − 2,000×227.50 = 1,135,750 + 227,300 − 455,000 = **USD 908,050 நிகரமா கட்டணும்**.

Reporting system "3 trades → 3 settlements"ன்னு எதிர்பார்த்தா, இங்க 3 → 1 வருது.

---

## 6. United Kingdom — Systems & Data Flow

### Front & Middle Office

Canada, US-ஓட கிட்டத்தட்ட அதே vendor landscape (Bloomberg AIM/EMSX, Charles River, Murex, Calypso) — front/middle office tooling market உண்மையிலயே உலகளாவியது. வேறுபடுவது back office.

### Back Office — Market Infrastructure

- **CREST**, **Euroclear UK & International** நடத்துவது (Euroclear 2002-ல Bank of England-ல இருந்து CREST business-ஐ வாங்கியது), UK-ன் CSD — UK equities, gilts, money market instruments-ஐ book-entry வடிவில settle பண்ணும்.
- **LCH (London Clearing House)**, London Stock Exchange Group-ன் பகுதி, UK (மற்றும் இன்னும் பரந்த ஐரோப்பிய / உலகளாவிய) interest rate swap clearing-ல ஆதிக்கமான CCP — [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6, §11-ல illustrative CCP-ஆ பெயரிட்ட அதே "LCH".
- **CHAPS** (Clearing House Automated Payment System), Bank of England நடத்துவது — UK-ன் real-time high-value cash payment system; Fedwire-க்கு UK இணை.
- Brexit-க்கு பிறகு UK தன் சொந்த **UK EMIR**, **UK MiFIR** reporting regimes-ஐ ஓட்டுது — EU பதிப்புகளை (Section 7) நெருக்கமா பிரதிபலிச்சாலும் தனியா; **DTCC Derivatives Repository Ltd**, **UnaVista** (LSEG) போன்ற trade repositories UK market-ஐ குறிப்பா சேவை செய்யுது.

### Worked example — Bank Of Toronto-வின் London desk UK equity விற்கிறது, T+2

```text
UNITED KINGDOM  —  DATA FLOW FOR ONE EQUITY TRADE
========================================================================

  11:00:00  ORDER captured in Charles River IMS (OMS)
              SELL 200,000 shares, Tesco PLC (LSE: TSCO)

  11:00:04  Routed via FIX to the London Stock Exchange (EMS/venue)

  11:00:06  EXECUTION @ GBP 3.42
              Trade Value = 200,000 x 3.42 = GBP 684,000

  11:00:06  TRADE CAPTURE — Trade ID BOT-UK-2026-005580

  11:20:00  CONFIRMATION/MATCHING -> MATCHED

  Settlement Date = T+2  (UK equities have NOT yet moved to
              T+1 — that move is targeted for October 2027)
              CREST performs DvP: shares move in Euroclear UK &
              International's book-entry system; cash settles
              via the linked banking payment rails.
```

**Takeaway:** UK equities-க்கு இன்னும் T+2 ஓடுது, Canada/US T+1 — அதனால Bank Of Toronto-வின் ஒரு global desk-க்கு, ஒரே நாளில் அருகருகே *ரெண்டு வேறு settlement கடிகாரங்கள்* ஓடுது; trade எந்த market-ல execute ஆச்சுங்கறதை பொறுத்து மட்டும். Section 9 இதை ஒரே நாள், நாலு-market ஒப்பீட்டால உறுதிப்படுத்துது.

---

## 7. European Union — Systems & Data Flow

### Front & Middle Office

மறுபடியும் அதே உலகளாவிய vendor landscape (Bloomberg, Charles River, Murex, Calypso) — ஆனா கவனிக்க: EU என்பது பல தேசிய markets-ஐ தாண்டிய **ஒரே regulatory framework**; அதனாலதான் அதன் back-office infrastructure மற்ற மூணிலிருந்தும் வேறு (Canada/US/UK-ஓட "முழு market-க்கும் ஒரு CSD" போல இல்லாம, ஒவ்வொரு நாட்டுக்கும் ஒரு CSD).

### Back Office — Market Infrastructure

- பெரும்பாலான EU தேசிய CSDs (ஒரே "EU CSD" இல்ல — ஜெர்மனியின் Clearstream, பெல்ஜியத்தின் Euroclear Bank, பிரான்சின் Euroclear France… ஒவ்வொண்ணும் தனி legal entity) **T2S (TARGET2-Securities)** வழியா settle ஆகுது — ECB நடத்தும் pan-European settlement platform; பங்கேற்கும் எல்லா CSDs-உம் ஒரே பகிரப்பட்ட technical platform-ல, central bank money-ல securities transactions-ஐ settle பண்ண உதவுது.
- **Euroclear**, **Clearstream** (Deutsche Börse Group பகுதி) — ரெண்டு பெரிய **ICSDs (International Central Securities Depositories)**; தங்கள் உள்நாட்டு CSD role-க்கு கூடுதலா cross-border, Eurobond settlement-ஐ கையாளுது.
- **Eurex Clearing** (Deutsche Börse Group), **LCH SA** (LCH-ன் Paris-based, EU-regulated பிரிவு) — cleared derivatives, repo-க்கான ரெண்டு பெரிய EU CCPs.
- **EMIR**-ன் கீழ trade repositories: **REGIS-TR**, **DTCC Derivatives Repository**, **UnaVista**, மற்றவை — firms எந்த EMIR-authorized TR-க்கும் report பண்ணலாம்; தேசிய regulators ஒரு counterparty பயன்படுத்தின TR-ல இருந்து data இழுக்கும்.

### Worked example — Bank Of Toronto-வின் Frankfurt desk ஒரு German Bund வாங்குது, T+2

```text
EUROPEAN UNION  —  DATA FLOW FOR ONE BOND TRADE
========================================================================

  13:00:00  ORDER captured in Bloomberg AIM (OMS)
              BUY EUR 30,000,000 face value, Germany 3% Bund 2033

  13:00:05  EXECUTION @ Price 99.10 (interbank market)
              Trade Value = 30,000,000 x 99.10 / 100 = EUR 29,730,000

  13:00:05  TRADE CAPTURE — Trade ID BOT-EU-2026-007742

  13:30:00  CONFIRMATION/MATCHING -> MATCHED

  Settlement Date = T+2  (EU securities settlement is still T+2
              as of 2026; a coordinated move to T+1 alongside the
              UK is targeted for October 2027)
              Settlement instructed to Clearstream (as the German
              CSD/ICSD), which settles on the shared T2S platform
              in central bank money — the German Bundesbank's
              books ultimately reflect the cash leg.
```

**Takeaway:** T2S தேசிய CSDs-ஐ (Clearstream, Euroclear France…) மாற்றாது — அவை எல்லாம் plug-in ஆகும் பகிரப்பட்ட settlement *engine்*; அதனாலதான் ஒரு EUR bond trade-ல உண்மையான DvP mechanics கீழே pan-European T2S platform-ல ஓடினாலும் CSD-ஆ "Clearstream" பெயர் வருது. இது Canada/US/UK-ல இருந்து கட்டமைப்பில வேறு — அவை ஒவ்வொண்ணுக்கும் ஒரே, தேசிய CSD.

---

## 8. Side-by-Side — Settlement Cycles, CSDs, CCPs & Trade Repositories

| | **Canada** | **United States** | **United Kingdom** | **European Union** |
|---|---|---|---|---|
| Equity settlement cycle | T+1 | T+1 | T+2 (T+1 இலக்கு Oct 2027) | T+2 (T+1 இலக்கு Oct 2027) |
| Government bond settlement | T+1 அல்லது T+2 | T+1 | T+1 (gilts) | T+2 |
| Central Securities Depository (CSD) | CDS (CDSX) | DTC | CREST (Euroclear UK & Int'l) | தேசிய CSDs (Clearstream, Euroclear France…) T2S வழியா |
| முக்கிய CCP(s) | CDCC | NSCC (equities), FICC (Treasuries/repo) | LCH | Eurex Clearing, LCH SA |
| Cash settlement rail | Large Value Transfer System (LVTS) | Fedwire | CHAPS | TARGET2 / T2S cash leg |
| Trade Repository (OTC derivatives) | Canadian TRs (எ.கா. DTCC Data Repository (Canada)) | DTCC GTR, ICE Trade Vault | DTCC Derivatives Repository Ltd, UnaVista (UK EMIR) | REGIS-TR, DTCC Derivatives Repository, UnaVista (EU EMIR) |
| Derivatives reporting-ன் முதன்மை விதி | மாகாண (CSA / OSC) OTC derivatives rules | CFTC (Dodd-Frank) | UK EMIR | EMIR |
| Securities financing (repo / sec-lending) reporting regime | 2026-ல் SFTR-க்கு இணையான தனி regime இல்ல (Section 12) | SEC Rule 10c-1a, FINRA-வின் SLATE வழியா (securities lending மட்டும், படிப்படியா அமல்) | UK SFTR | SFTR |

**Takeaway:** எந்த cross-border reporting field-ஐயும் sanity-check பண்ண இந்த table தான் வேகமான வழி — ஒரு Canadian equity trade record-ல T+2 settlement date இருந்தாலோ, ஒரு US trade-ல "DTC" CSD-ஆவும் "CDSX" settlement system-ஆவும் இருந்தாலோ, அந்த fields-ல ஒண்ணு தவறு; 2026-ல ஒவ்வொரு market-லயும் "சரி" எப்படி இருக்கும்னு மேல உள்ள table காட்டுது.

---

## 9. Cross-Border Worked Example — ஒரே Trade Date, நாலு Markets

Bank Of Toronto நான்கு jurisdictions-லயும் desks உள்ள ஒரு global dealer. **ஒரே calendar Trade Date**-ல, திங்கள் 17-Aug-2026, நாலு desks ஒவ்வொண்ணும் ஒரு trade செய்யுது. Sections 3–7-ஓட ஒருங்கிணைந்த மிகத் தெளிவான விளக்கம் இந்த ஒரே table:

```text
BANK OF TORONTO  —  FOUR DESKS, ONE TRADE DATE, FOUR DIFFERENT CLOCKS
========================================================================

Desk        Trade                              T+n    Settlement Date
--------------------------------------------------------------------
Toronto     BUY 50,000 sh Loblaw (TSX: L)       T+1    Tue 18-Aug-2026
            @ CAD 178.40  -- CDSX / CDS

New York    BUY 5,000 sh Apple (NASDAQ: AAPL)   T+1    Tue 18-Aug-2026
            @ USD 227.15  -- NSCC/DTC

London      SELL 200,000 sh Tesco (LSE: TSCO)   T+2    Wed 19-Aug-2026
            @ GBP 3.42    -- CREST

Frankfurt   BUY EUR 30M Germany 3% Bund 2033    T+2    Wed 19-Aug-2026
            @ Price 99.10 -- Clearstream / T2S
--------------------------------------------------------------------

Same Trade Date. Two different Settlement Dates, purely because of
which market each trade executed in -- nothing about the trades
themselves (all clean, all matched same-day) caused the difference.
```

**Takeaway:** Trades-ஐ Trade Date வாரியா குழுவாக்கி, எல்லாம் ஒரே calendar நாளில settle ஆகும்னு எதிர்பார்க்கும் ஒரு global reporting system, எந்த ஒரு நாளும் Bank Of Toronto-ஓட book-ல கிட்டத்தட்ட பாதியை தவறா வகைப்படுத்தும் — settlement date ஒவ்வொரு market-க்கும் தனித்தனியா derive பண்ணணும் (Section 3-ஓட table), trade date-ல இருந்து ஒரு நிலையான offset-ஆ ஒருபோதும் assume பண்ணக்கூடாது.

---

## 10. Regulatory Bodies — நாடு வாரியா

### Canada

| Body | Role |
|---|---|
| **CSA (Canadian Securities Administrators)** | Umbrella council of the 10 provincial/territorial securities regulators; coordinates national policy (Canada has no single federal securities regulator) |
| **OSC (Ontario Securities Commission)** | The largest individual provincial regulator, given Toronto's role as Canada's primary financial centre |
| **CIRO (Canadian Investment Regulatory Organization)** | The national self-regulatory organization (SRO) for investment dealers and trading activity, formed in 2023 from the merger of IIROC and the MFDA |
| **Bank of Canada** | Central bank; oversees systemically important financial market infrastructure, including CDS/CDSX and CDCC |
| **OSFI (Office of the Superintendent of Financial Institutions)** | Prudential regulator of federally regulated banks and insurers, including capital adequacy |

### United States

| Body | Role |
|---|---|
| **SEC (Securities and Exchange Commission)** | Primary regulator of securities markets, equities, corporate bonds, and securities-based swaps |
| **CFTC (Commodity Futures Trading Commission)** | Regulator of most OTC derivatives (interest rate swaps, CDS index products, commodities) under Dodd-Frank |
| **FINRA (Financial Industry Regulatory Authority)** | The SRO overseeing broker-dealer conduct and trade reporting |
| **Federal Reserve** | Central bank; supervises bank holding companies, operates Fedwire |
| **OCC (Office of the Comptroller of the Currency)** | Prudential regulator/charterer of national banks |

### United Kingdom

| Body | Role |
|---|---|
| **FCA (Financial Conduct Authority)** | Conduct regulator for markets, firms, and consumer protection; administers UK MiFIR/UK EMIR reporting |
| **PRA (Prudential Regulation Authority)** | Part of the Bank of England; prudential regulator of banks, insurers, and major investment firms |
| **Bank of England** | Central bank; financial stability, oversight of systemically important FMIs (CREST, LCH) |

### European Union

| Body | Role |
|---|---|
| **ESMA (European Securities and Markets Authority)** | EU-level coordinator of securities market regulation; sets technical standards for MiFID II/MiFIR and EMIR reporting |
| **EBA (European Banking Authority)** | EU-level banking regulator; sets prudential standards across member states |
| **ECB (European Central Bank)** | Monetary policy for the euro area; direct prudential supervisor of "significant" banks via the Single Supervisory Mechanism (SSM); operator of T2S |
| **National Competent Authorities (NCAs)** | Each member state's own regulator implements EU rules locally — e.g. **BaFin** (Germany), **AMF** (France), **CSSF** (Luxembourg) |

**Takeaway:** நாலு jurisdictions-லயும் ஒரே மூன்று-வழி பிரிவு: ஒரு அமைப்பு **market conduct / reporting**-க்கு (OSC/CSA, SEC, FCA, ESMA+NCAs), ஒண்ணு **prudential / bank safety**-க்கு (OSFI, OCC/Fed, PRA, EBA/ECB), ஒண்ணு market infrastructure-ஐயே மேற்பார்வையிடும் **மத்திய வங்கி** (Bank of Canada, Federal Reserve, Bank of England, ECB). ஒரு regulator இந்த மூணில் எதைச் சேர்ந்ததுன்னு அடையாளம் கண்டா, அது என்ன வகை report/requirement கேக்கும்னு புரிஞ்சுக்கலாம்.

(விரிவான acronyms: [Abbreviations Glossary — Section 9](./Abbreviations-Glossary-Tamil.html))

---

## 11. OTC Derivatives & Cash Equities — System Stacks

Sections 4–7 *நாடு வாரியா* front/middle/back office systems-ஐ பெயரிட்டுச்சு. இந்த section அதை *asset class வாரியா* குறுக்கே வெட்டுது — ஏன்னா OTC derivatives, cash equities, treasury/FX-க்கான vendor landscape உண்மையிலயே உலகளாவியது, நாடு-குறிப்பிட்டது இல்ல. ஒரு trading floor-ல "Imagine", "Calypso", "WSS"ன்னு சொல்லும்போது மக்கள் குறிப்பிடுற உண்மையான platform பெயர்கள் இவை.

### OTC Derivatives stack

| Function | Systems |
|---|---|
| Front-to-back OTC derivatives platform | **Calypso** (இப்போ **Adenza**-ன் பகுதி, Nasdaq-க்கு சொந்தம்) — trade capture, valuation, collateral/margin (Uncleared Margin Rules உட்பட), regulatory-reporting connectivity எல்லாம் ஒரே platform-ல; **Murex**; **Summit** (Finastra) |
| Real-time risk & margin engine | **ION IMAGINE** — prime brokers, futures commission merchants (FCMs), clearing firms OTC + listed derivatives positions-ஐ real-time-ல cross-margin பண்ண பரவலா பயன்படுத்துது |
| Trade confirmation / eConfirmation | **MarkitWire** (S&P Global) — Markit 2007-ல வாங்கியப்புறம் **SwapsWire** ஆன platform; OTC derivative electronic confirmation-க்கு இன்னும் மிகப் பொதுவான பெயர்; [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §04-ல குறிப்பிட்ட அதே system |
| CCP clearing service (interest rate swaps) | **SwapClear** — LCH-ன் interest rate swaps-க்கான குறிப்பிட்ட clearing service; [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §06, §11-ல illustrative CCP-ஆ சொன்ன பொதுவான "LCH"-க்கு பின்னாடி இருக்கும் உறுதியான, பெயரிட்ட service |
| Portfolio compression & reconciliation | **triReduce** / **triResolve** (இப்போ **OSTTRA**-வின் பகுதி — MarkitServ, TriOptima, Reset-ல இருந்து உருவான CME Group + S&P Global joint venture) |
| Collateral management & margin messaging | **AcadiaSoft** (Uncleared Margin Rules-க்கான industry-standard margin call messaging utility), கூட Calypso/Adenza-ல உள்ள collateral modules |
| Analytics / pricing libraries | **Numerix**, **FINCAD** |

### Cash equities stack

| Function | Systems |
|---|---|
| Order/Execution Management (OMS/EMS) | Charles River IMS, Bloomberg AIM/EMSX, FlexTrade, Eze, மற்றும் **Fidessa** (இப்போ ION-ன் பகுதி) — Fidessa குறிப்பா UK, ஐரோப்பாவில sell-side equities trading-ல இன்னும் கனமா பயன்படுது |
| Post-trade equities processing | **Broadridge** (BPS/Impact), **SS&C** (GMI, Geneva), **Torstone Technology** (Inferno — multi-asset post-trade processing), **TCS BaNCS** (custodians core securities processing-க்கு பரவலா பயன்படுத்துது) |
| Trade matching / affirmation | DTCC-ன் **CTM**; DTCC-ன் **ALERT** (standing settlement instruction repository, முன்னாள் Omgeo Alert) |
| Settlement/custody record-keeping | Custodian core platforms (TCS BaNCS, Broadridge…) — Sections 4–7-படி ஒவ்வொரு நாட்டுக்கும் CDSX / DTC / CREST / T2S-ல இணையுது |

### Treasury / FX / money markets stack

| Function | Systems |
|---|---|
| Treasury, cash management & FX processing | **Wall Street Systems (WSS)** — இப்போ **ION Treasury**-ன் பகுதி — FX, money markets, intraday liquidity, cash management-க்கு பரவலா deploy ஆன platform; மேலுள்ள derivatives platforms-க்கு *பக்கத்துல* (மாற்றாம) உட்காருது |
| FX execution venues | **FXall** (LSEG), **360T** (Deutsche Börse) |
| E-trading for fixed income | **MarketAxess**, **Tradeweb** |

> **"swapone" பற்றி ஒரு குறிப்பு:** இது ஒரு standard product பெயர் இல்ல; ஆனா கிட்டத்தட்ட நிச்சயமா ரெண்டு உண்மையான systems-ல ஒண்ணு: **SwapClear** (LCH-ன் IRS clearing service, மேலே) அல்லது **SwapsWire** (முதல் electronic OTC confirmation platform, இப்போ MarkitWire). ரெண்டும் உண்மையிலயே முக்கியம் என்பதால் மேலே ரெண்டையும் சேர்த்திருக்கு — அந்த பெயரை யாரிடமிருந்து கேட்டீங்களோ அவர்களிடம் எதை சொன்னாங்கன்னு உறுதி செஞ்சுக்கோங்க.

**Takeaway:** OTC derivatives stack (Calypso/Murex + IMAGINE + MarkitWire + SwapClear + triReduce/AcadiaSoft), cash equities stack (OMS/EMS + Broadridge/TCS BaNCS + CTM/ALERT) — ரெண்டும் ஒரே bank-க்குள்ள பெரும்பாலும் தனி toolchains, ஆனா இறுதியில ரெண்டும் Section 2-ன் ஒரே ஐந்து-அடுக்கு architecture-க்குள்ள ஊட்டுது. Bank Of Toronto-வில ஒரு derivatives desk-உம் ஒரு equities desk-உம் ஒரே Middle/Back Office functions-க்கு முற்றிலும் வேறு screens-ஐ பாக்கறாங்க என்பது மிகவும் சாத்தியம்.

---

## 12. Securities Financing Transaction Reporting — SFTR & நாடு வாரியா இணைகள்

**Definition:** **SFTR (Securities Financing Transactions Regulation)** EU-ன் reporting regime — 2020 முதல் அமலில், Brexit-க்கு பிறகு UK-க்கு **UK SFTR**-ஆ நீட்டிக்கப்பட்டது — குறிப்பா **SFTs (Securities Financing Transactions)**-க்காக: repos, reverse repos, buy-sell backs, securities/commodities lending & borrowing, margin lending. இது EMIR-க்கும் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07, [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §09) MiFIR-க்கும் ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §09) பக்கத்துல மூன்றாவது reporting regime — ஆனா அந்த ரெண்டும் கவராத product family-க்கு.

```text
WHERE SFTR SITS AMONG THE REPORTING REGIMES
========================================================================

  MiFIR   ->  reports EXECUTED TRANSACTIONS (mainly cash securities,
               some derivatives) — Module 3 §09

  EMIR    ->  reports OTC DERIVATIVES specifically — Module 2 §07,
               Module 3 §09

  SFTR    ->  reports SECURITIES FINANCING TRANSACTIONS specifically —
               repo, reverse repo, buy-sell back, securities lending/
               borrowing, margin lending — THIS SECTION

  All three flow to a Trade Repository (Module 1 §05), often the SAME
  TR (REGIS-TR, DTCC Derivatives Repository, UnaVista all accept
  EMIR AND SFTR reports) — but each is a legally distinct reporting
  obligation, with its own schema and its own deadline.
```

SFTR-க்கு EMIR/MiFIR-ல இல்லாத ஒரு அம்சம் இருக்கு: **collateral reuse reporting**. Goldman Sachs ஒரு repo-ல German Bunds-ஐ collateral-ஆ பெற்று, அதே Bunds-ஐ *வேறு* ஒரு transaction-ல re-use (rehypothecate) செஞ்சா, அந்த reuse-உம் தனியா report ஆகணும் — 2008 நெருக்கடியில collateral rehypothecation சங்கிலிகள் systemic risk-ஐ பெருக்கும் காரணியா அடையாளம் காணப்பட்டதால், குறிப்பா அறிமுகப்படுத்தப்பட்ட வெளிப்படைத்தன்மை தேவை.

### Worked example — Bank Of Toronto-வின் overnight repo, SFTR-ன் கீழ report

```text
SFTR REPORTING  —  Overnight Repo, Bank Of Toronto with Goldman Sachs
========================================================================

  Trade: Bank Of Toronto lends EUR 50,000,000 cash overnight to
         Goldman Sachs, collateralized by Germany 3% Bund 2033
         (the same instrument from Section 7's worked example)
  Repo Rate: 3.15%
  Trade Date: Mon 17-Aug-2026    Maturity: Tue 18-Aug-2026 (overnight)

  Overnight interest = 50,000,000 x 3.15% x (1/365) = EUR 4,315

  SFTR Reporting Deadline: T+1 (by end of Tue 18-Aug-2026), same
  reporting-window discipline as EMIR/MiFIR (Module 3 §09) — reported
  to REGIS-TR by both counterparties (SFTR, like EMIR, is a dual-
  sided reporting obligation)

  SEPARATE REPORT — Collateral Reuse:
  If Goldman Sachs then re-uses the German Bunds it received as
  collateral in a different repo with a third counterparty before
  returning them to Bank Of Toronto, THAT reuse is its own SFTR
  report — distinct from the original repo trade report above.
```

### நாடு வாரியா இணைகள்

| Jurisdiction | Regime | Scope | Notes |
|---|---|---|---|
| European Union | **SFTR** | Repo, reverse repo, buy-sell back, securities/commodities lending, margin lending | Reports to REGIS-TR/DTCC/UnaVista under ESMA technical standards |
| United Kingdom | **UK SFTR** | Same scope as EU SFTR | Onshored post-Brexit, administered by the FCA, reported to UK-authorized TRs |
| United States | **SEC Rule 10c-1a**, via FINRA's **SLATE** (Securities Lending and Transparency Engine) | Securities lending only — narrower than SFTR, does **not** cover repo, buy-sell back, or margin lending | Adopted 2023, phasing in through 2025–2026; the closest US analog, but deliberately narrower in product scope |
| Canada | No dedicated SFTR-equivalent as of 2026 | — | Repo activity is captured indirectly through CDCC's central clearing records; existing provincial OTC derivatives rules (e.g. CSA Multilateral Instrument 96-101) don't extend to plain repo — a genuine gap relative to the EU/UK/US |

**Takeaway:** SFTR reporting, EMIR/MiFIR reporting நடக்கும் அதே lifecycle stage-லயே நடக்குது ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §09) — நிகழ்வது ஒன்பது-stage lifecycle-ன் "Regulatory Reporting" stage தான் — வேறு product family-க்கு, தன் சொந்த T+1 deadline-ஓட, plain cash bond அல்லது cleared swap-க்கு இணையே இல்லாத கூடுதல் தொடர் கடமையோட (collateral reuse reporting). repo / securities lending-க்கு reporting logic கட்டச் சொன்னா, இருக்கும் EMIR அல்லது MiFIR reporting pipeline-ஐ வெறுமனே நீட்டாதீங்க — schema, reuse-reporting தேவை, (மேலுள்ள table படி) regulator கூட வேறுபடலாம்.

---

## Recap Quiz — எண்கள் பதிப்பு

1. Bank Of Toronto-வின் Toronto desk-உம் Frankfurt desk-உம் ஒரே திங்கள்ல ஒவ்வொரு trade execute பண்ணுது. எது முதல்ல settle ஆகும், ஏன்? Section 3-ஓட table-ல இருந்து ரெண்டு settlement cycles-ஐயும் walk through பண்ணுங்க.
2. Canada, US, UK, EU ஒவ்வொண்ணுக்கும் CSD-யையும் CCP-யையும் சொல்லுங்க (EU-க்கு ஒரே CSD இல்லாததால், settlement *platform*-ஐ சொல்லுங்க).
3. Section 5-ஓட US Apple trade-ல, Bank Of Toronto desk அதே நாள் முன்னாடி 2,000 Apple shares-ஐயும் விற்றிருந்தா, central netting CCP இல்லாத market-ஓட ஒப்பிட்டா, NSCC settlement instruction-ஐ எப்படி வேறு மாதிரி செய்யும்?
4. Section 7-ல ஒரு EUR bond trade, settlement இயந்திரரீதியா T2S-ல நடந்தாலும், CSD-ஆ "Clearstream"-ஐ ஏன் இன்னும் பெயரிடுது?
5. இந்த module-ஓட நாலு jurisdictions-ல எந்த ரெண்டு இப்போ ஒரே equity settlement cycle-ஐ பகிருது, எந்த ரெண்டு October 2027-க்கு பகிரப்பட்ட cycle மாற்றத்தை இலக்கு வெச்சிருக்கு?
6. UK-ல ஒரு trade அதன் regulatory reporting deadline-ஐ மீறுது. எந்த regulator-ஓட rulebook மீறப்பட்டது — FCA-வின் கீழ UK EMIR/UK MiFIR, அல்லது ESMA-வின் கீழ EU EMIR — மேலும் பதில் counterparties யாருங்கறதை மட்டுமில்லாம trade எங்க book ஆச்சுங்கறதை ஏன் சார்ந்திருக்கு?
7. Section 2-ஓட architecture படி, இந்த systems ஒவ்வொண்ணையும் Front Office, Middle Office, அல்லது Back Office-க்கு பொருத்துங்க: Charles River IMS, Murex, DTC, CDSX matching module, Fedwire.
8. Bank Of Toronto-வின் Goldman Sachs உடனான overnight repo EUR 50,000,000-க்கு ஒரு நாளுக்கு 3.15% (actual/365) தருது. வட்டித் தொகை என்ன, SFTR-ன் கீழ ரெண்டு counterparties-உம் எப்போதுக்குள்ள report பண்ணணும்?
9. எந்த jurisdiction-ஓட securities-financing reporting regime மற்றவற்றை விட product scope-ல குறுகியது — securities lending-ஐ மட்டும் கவர் பண்ணி, repo, buy-sell back, margin lending-ஐ இல்லாம — மேலும் அந்த reports-ஐ உண்மையில எந்த system சேகரிக்குது?

நீங்க தயாரானப்போ பதில் பாருங்க — அப்புறம் முக்கிய curriculum வரிசைக்கு திரும்புங்க: **[Module 4](../docs/1-capital-markets/module-04-equity.html)**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Toronto desk-ஓட trade முதல்ல settle ஆகுது — செவ்வாய் 18-Aug-2026, T+1-ல — Frankfurt desk-ஓட trade புதன் 19-Aug-2026, T+2-ல.**

Section 3-ஓட table படி, Canadian equities T+1 (2024 May முதல் US-உடன் ஒருங்கிணைந்து), EU government bonds 2026-லயும் T+2. ரெண்டு trades-உம் ஒரே திங்கள் Trade Date-ல execute ஆச்சு; ஆனா Canada-ஓட settlement infrastructure (CDSX) EU-ஓட T2S-அடிப்படையிலான settlement-ஐ விட ஒரு business day குறைவான cycle-ஐ அமல்படுத்துது — Section 9-ல வேலை செய்த அதே சூழல்.

**2. Canada: CDS (CDSX) / CDCC. US: DTC / NSCC (equities), FICC (Treasuries/repo). UK: CREST (Euroclear UK & International) / LCH. EU: T2S (பகிரப்பட்ட settlement platform; Clearstream, Euroclear France போன்ற தேசிய CSDs அதுல plug-in ஆகும்) / Eurex Clearing, LCH SA.**

இது Section 8-ஓட ஒப்பீட்டு table-ல இருந்து நேரடி வாசிப்பு — முக்கிய நுணுக்கம்: மற்ற மூணு markets மாதிரி EU-க்கு ஒரு "CSD" இல்ல; T2S தான் platform, உண்மையான legal CSD என்பது security settle ஆகும் தேசிய ஒண்ணு (Clearstream, Euroclear France…).

**3. NSCC settlement instruction உருவாக்கும் முன் ரெண்டு trades-ஐ net பண்ணும் — Bank Of Toronto 5,000 மற்றும் 2,000 shares-ன் ரெண்டு தனி gross instructions-ஐ இல்லாம, நிகர 3,000-share வித்தியாசத்தை (5,000 வாங்கல் − 2,000 விற்பனை) மட்டும் settle பண்ணும்.**

Section 5 குறிப்பா சொல்றது — NSCC ஒரே நாளில் ஒரே security-ல ஒரு firm-ஓட trades முழுக்க multilateral netting செய்யும்; central netting CCP இல்லாத market (அல்லது bilateral-only settlement model) அதுக்கு பதிலா, இரண்டும் தனித்தனியா DvP முடிக்க வேண்டிய ரெண்டு தனி gross settlement obligations-ஐ உருவாக்கும்.

**4. ஏன்னா T2S ஒரு பகிரப்பட்ட settlement *engine்*, தன் சொந்த legal/regulatory அர்த்தத்தில CSD இல்ல — Clearstream தான் ஜெர்மன் securities-க்கு உண்மையான central securities depository (legal book-entry record-ஐயும் CSD license-ஐயும் வெச்சிருக்கும் entity); T2S வெறும், பங்கேற்கும் மற்ற எல்லா தேசிய CSDs-ஓட instructions-ஓட சேர்ந்து, Clearstream-ஓட settlement instructions execute ஆகும் பொதுவான technical platform.**

Section 7 இதை வெளிப்படையா சொல்லுது: T2S தேசிய CSDs-ஐ மாற்றாது, அவை எல்லாம் plug-in ஆகும் platform — இது Canada/US/UK-ல இருந்து கட்டமைப்பில வேறு; அங்க CSD (CDS, DTC, CREST) legal entity-யும் settlement engine-உம் ஒரே இடத்துல.

**5. Canada, US இப்போ equities-க்கு T+1-ஐ பகிருது (ரெண்டும் 2024 May-ல ஒண்ணா மாறின). UK, EU October 2027-க்கு T+1-க்கு ஒருங்கிணைந்த மாற்றத்தை இலக்கு வெச்சிருக்கிற ரெண்டு jurisdictions.**

Section 3-ஓட table-உம் Sections 6/7-ஓட takeaways-உம் இந்த இணையை சுட்டுது — Canada/US ஒண்ணா மாறியதும் (அந்த border-ஐ கடக்கும் order flow காரணமா), இப்போ UK/EU அதே காரணத்துக்காக தங்கள் சொந்த ஒருங்கிணைந்த மாற்றத்தை திட்டமிடுவதும் தற்செயல் இல்ல.

**6. FCA-வின் கீழ UK EMIR/UK MiFIR — ஏன்னா Brexit-க்கு பிறகு UK, EU-ஓட EMIR/MiFIR-ல இருந்து தனி, தன் சொந்த reporting regime-ஐ இயக்குது; ரெண்டு rulebooks-உம் நெருக்கமா பிரதிபலிச்சாலும் வேறு. கட்டுப்படுத்தும் regime, trade எங்க book / report ஆனதுங்கறதை (executing entity எந்த jurisdiction-ஓட விதிகளுக்கு உட்பட்டது) சார்ந்தது, ரெண்டு counterparties தற்செயலா எந்த நாடுகளில இருக்காங்கங்கறதை மட்டும் சார்ந்தது இல்ல.**

Section 6 இதை நேரடியா சொல்றது: Brexit முதல் UK தன் சொந்த UK EMIR, UK MiFIR-ஐ ஓட்டுது, Section 7-ஓட EU பதிப்புகளில் இருந்து தனி; ஒவ்வொண்ணுக்கும் தன் சொந்த authorized trade repositories. UK-booked entity-க்கும் EU-booked entity-க்கும் இடையிலான ஒரு trade கொள்கையளவில ரெண்டு regimes-லயும் report ஆக வேண்டியிருக்கலாம் — ஆனா *UK* deadline-ஐ மீறுவது FCA-வின் rulebook மீறல், ESMA-வின்து இல்ல.

**7. Charles River IMS = Front Office (OMS). Murex = Middle Office (risk/valuation engine). DTC = Back Office (CSD/settlement). CDSX matching module = Middle Office (confirmation/matching) — CDS தானே ஒரு Back Office/CSD organization ஆனாலும், matching function குறிப்பா lifecycle-ன் Middle Office stage-ல உட்காருது ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §5). Fedwire = Back Office (cash settlement rail).**

இது Section 2-ஓட பொதுவான architecture-ஐ பின்தொடருது: Front Office Order/Execution-ஐ உருவாக்குது (Charles River); Middle Office confirm, match, risk price பண்ணுது (Murex, மற்றும் CDSX-ஓட தனி DvP settlement role-ல இருந்து வேறுபட்ட CDSX matching function); Back Office securities (DTC), cash (Fedwire)-ஐ physically settle பண்ணுது. ஒரே *organization* (CDS) ஒன்றுக்கு மேற்பட்ட lifecycle stages-ஐ தாண்டும் systems-ஐ இயக்கலாம்னு காட்ட CDSX உதாரணம் வேண்டுமென்றே சேர்க்கப்பட்டது — matching module-உம் DvP settlement engine-உம் ஒரே corporate கூரையின் கீழ வேறு functions.

**8. வட்டி = EUR 4,315. ரெண்டு counterparties-உம் REGIS-TR (அல்லது வேறு authorized TR)-க்கு செவ்வாய் 18-Aug-2026 முடிவுக்குள் — T+1 — report பண்ணணும்.**

```text
Overnight interest = 50,000,000 x 3.15% x (1/365) = EUR 4,315
```

Section 12-ஓட worked example, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §08-ஓட swap உதாரணத்தின் அதே actual/365 day-count convention-ஐ பயன்படுத்துது. SFTR, EMIR/MiFIR-ஓட அதே T+1 reporting-window ஒழுக்கத்தை ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §09) ஓட்டுது, EMIR மாதிரியே dual-sided கடமை — Bank Of Toronto, Goldman Sachs இருவரும் ஒரே repo-ஓட தங்கள் தங்கள் பக்கத்தை தனித்தனியா report பண்ணுது.

**9. United States, SEC Rule 10c-1a வழியா — reports-ஐ FINRA-வின் SLATE (Securities Lending and Transparency Engine) சேகரிக்குது.**

Section 12-ஓட country-equivalents table இதை வெளிப்படையா சுட்டுது: SFTR (EU), UK SFTR ரெண்டும் முழு SFT product family-யையும் — repo, reverse repo, buy-sell back, securities/commodities lending, margin lending — கவர் பண்ணுது; US விதி வேண்டுமென்றே securities lending-க்கு மட்டும் வரையறுக்கப்பட்டு, repo, buy-sell back transactions-ஐ SLATE-ன் சேகரிப்புக்கு வெளியே விடுது. Canada-வுக்கு (table குறிப்பிடுவது போல) எந்த scope-லயும் தனி regime இன்னும் இல்ல.
