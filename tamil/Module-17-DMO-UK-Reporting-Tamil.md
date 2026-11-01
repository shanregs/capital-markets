# Module 17 — UK DMO Reporting Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்), இங்க ஒரு **GEMM** (Gilt-Edged Market Maker) ஆ செயல்படுது — UK gilt சந்தையோட primary dealer.
> இது **[Module 16 — SFTR](../docs/2-reporting/module-16-sftr-reporting.html)** குடும்பத்தை சேர்ந்த **reference module**. [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §8 முதல்ல DMO-வை UK-ஓட gilt-வெளியிடும் அமைப்பா பெயரிட்டது; இந்த module ஒரு GEMM DMO-க்கு உண்மையில என்ன report பண்ணுது, எவ்வளவு அடிக்கடி, என்ன data-வோடன்னு ஆழமா பார்க்குது.
> **மூலம்:** English Module 17-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. DMO Reporting-ன்னா என்ன? — நோக்கமும் நிறுவன அடிப்படையும்

**Definition:** **UK Debt Management Office (DMO)** = HM Treasury-ஓட ஒரு executive agency, 1998-ல நிறுவப்பட்டது, UK அரசுக் கடனை — **gilts** (UK அரசு bonds), **Treasury bills** — வெளியிட்டு நிர்வகிக்கறதுக்கும், அரசின் அன்றாட cash management-க்கும் பொறுப்பு. **"DMO reporting"** = **GEMMs (Gilt-Edged Market Makers)** என்ற நியமிக்கப்பட்ட வங்கிகளின் ஒரு சிறிய panel, தங்கள் GEMM நிலையின் நிபந்தனையா DMO-க்கு submit பண்ணணும்ற operational, வணிக reporting: அவற்றின் gilt-auction bidding நடவடிக்கை, தொடர் secondary-market gilt trading.

**முதலிலயே முக்கியமான வேறுபாடு:** [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) அல்லது EMIR ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html)) போல இல்லாம, DMO reporting **statutory regulatory reporting regime இல்ல**. Trade Repository இல்ல, EU/UK regulation எண் இல்ல, deadline தவறினா conduct regulator-ல இருந்து அபராதம் இல்ல. இது ஒவ்வொரு வங்கிக்கும் DMO-வுக்கும் இடையிலான ஒரு **வணிக ஒப்பந்தத்தின்** (GEMM agreement) கீழ கடன்பட்ட reporting — ஒரு supplier scorecard-க்கு நெருக்கம், regulatory filing-ஐ விட; தவறினா விளைவு (GEMM நிலையை இழப்பது) வணிக ரீதியா தீவிரமானதா இருந்தாலும். அதே gilt trades-க்கு தனியா பொருந்தும் *statutory* UK MiFIR reporting கடமையோட சேர்த்து, Section 9 இந்த வேறுபாட்டை முழுசா வெளிப்படுத்துது.

**ஏன் இருக்கு:** ரெண்டு நோக்கங்கள்:

1.  **நம்பகமான, நல்லா subscribe ஆகற auctions.** DMO ஒவ்வொரு வாரமும், தவறாம, பல பத்து பில்லியன் பவுண்ட் gilts-ஐ விற்க முடியும்னு தெரிஞ்சிருக்கணும். அதுக்கு ஒவ்வொரு auction-லயும் வந்து அர்த்தமுள்ள bids போடுவோம்னு *முன்கூட்டியே உறுதியளித்த* dealers panel வேணும் — அந்த உறுதி உண்மையானதுன்னு auction by auction DMO உறுதிப்படுத்தும் வழி auction reporting.
2.  **உண்மையான market-making கண்காணிப்பு.** GEMM நிலை உண்மையான சலுகைகளைக் கொண்டிருக்கு (Section 2). ஒரு GEMM உண்மையிலயே market-making பண்றதுக்கு — தொடர்ந்து quote பண்ணி, உண்மையிலயே trade பண்ணி, வெறும் பட்டத்தை வைத்திருக்காம — தொடர் ஆதாரம் DMO-க்கு வேணும். தினசரி/வாராந்திர/aggregate turnover reporting தான் அந்த ஆதாரம்.

```text
UK GOVERNMENT DEBT ISSUANCE — WHERE DMO REPORTING SITS
========================================================================

  HM Treasury sets the annual financing "Remit" (how much to raise,
  gilts vs T-bills, maturity mix)
         |
         v
  DMO executes the Remit -- gilt auctions (mostly), syndications
  (for new/large lines), T-bill tenders
         |
         v
  GEMMs are the DMO's designated primary-dealer counterparties --
  bid at every auction, make continuous secondary-market prices
         |
         v
  GEMMs report BACK to the DMO: auction bids/results, daily/weekly
  secondary turnover -- THIS MODULE
         |
         v
  DMO monitors compliance, publishes aggregate market statistics,
  and calculates each GEMM's rolling market share (Section 7)
```

**Takeaway:** DMO reporting ஒரு ரெண்டு-பக்க பேரத்தை நேர்மையா வெச்சிருக்க இருக்கு — சிறிய வங்கிகள் panel-க்கு UK அரசுக் கடன் வெளியீட்டுக்கு சலுகை பெற்ற, பிரத்தியேக அணுகலை DMO தருது; பதிலுக்கு அந்த வங்கிகள் நான்கு வெவ்வேறு கால அடுக்குகள்ல (Sections 4–7), தங்கள் பங்கை நிறைவேத்துறாங்கன்னு DMO-க்கு தொடர் ஆதாரம் தரணும்.

---

## 2. யார் Report பண்றாங்க — GEMMs & GEMM Framework

**Definition:** **GEMMs (Gilt-Edged Market Makers)** = ஒரு இருதரப்பு **GEMM agreement**-ன் கீழ gilt சந்தையில primary dealers-ஆ DMO-வால முறையா அங்கீகரிக்கப்பட்ட வங்கிகள், broker-dealers panel. Bank Of Toronto-ஓட London entity, **Bank Of Toronto UK — GEMM code BOT-GEMM-07** (illustrative) ஆ செயல்படுது, இந்த module முழுக்க அத்தகைய ஒரு firm.

| GEMM கடமை | GEMM சலுகை (பதிலுக்கு) |
|---|---|
| Gilts-ல தொடர்ச்சியான இருவழி விலை-நிர்ணயம் (trading நாள் முழுக்க bid-உம் offer-உம் quote பண்ணுதல்) | DMO-ஓட gilt auctions-ல bidding counterparty-ஆ நேரடி, பிரத்தியேக அணுகல் |
| ஒவ்வொரு gilt auction-லயும் குறைந்தபட்ச பங்கேற்பு (competitive bidding) | Auctions-ல **Non-Competitive Allocation (NCA)** அணுகல் (Section 4) |
| ஒரு rolling அடிப்படையில அளக்கப்படும் குறைந்தபட்ச தொடர் secondary-market turnover / market share (Section 7) | **Post Auction Option Facility (PAOF)** அணுகல் (Section 4) |
| Bids, allocations & turnover-ஐ DMO-க்கு வழக்கமான reporting (Sections 4–7) | DMO-ஓட secondary-market dealing வசதிகளுக்கு அணுகல் (உதா., short positions-ஐ cover பண்ண gilt-lending வசதி) |
| — | வெளியிடப்பட்ட GEMM panel-ல சேர்ப்பு, DMO-ஓட அவ்வப்போதைய market-share rankings-ல நிலை |

> **Callout — இவற்றுக்கு எதிரா கட்டறதுக்கு முன்னாடி தற்போதைய கடமைகளை உறுதிப்படுத்துங்க.** சரியான குறைந்தபட்ச bidding சதவீதங்கள், NCA/PAOF வரம்புகள், market-share வரம்புகள் DMO-ஓட சொந்த வெளியிடப்பட்ட GEMM விதிகள், operational notices-ல இருக்கு; அவை அவ்வப்போது திருத்தப்படும் — இந்த module-ல ஒவ்வொரு சதவீதத்தையும் வரம்பையும் **கற்பிக்கும் விளக்க எண்**ணா எடுத்துக்கோங்க, தற்போதைய விதியா இல்ல; ஒரு rules engine-ல encode பண்றதுக்கு முன்னாடி உயிருள்ள எண்களை Section 11-ஓட அதிகாரப்பூர்வ links-க்கு எதிரா உறுதிப்படுத்துங்க.

**Takeaway:** GEMM நிலை *தொடர் செயல்திறன் ஆதாரத்தை நிபந்தனையா கொண்ட* ஒரு சலுகை — நடத்தை எதுவா இருந்தாலும் ஒரு தடவை கொடுக்கப்பட்டு காலவரையற்று வெச்சிருக்கப்படறதில்ல. அந்த நிபந்தனைதான் நாலு தனி கால அடுக்குகள்ல நாலு தனி report வகைகள் ஏன் இருக்குன்னு காரணம் (Section 3): DMO auction மட்டம், தினசரி மட்டம், வாராந்திர மட்டம், rolled-up periodic மட்டம்ல ஆதாரம் வேணும், ஒற்றை ஆண்டு சான்றிதழ் மட்டும் இல்ல.

---

## 3. நாலு Report வகைகள் — மேலோட்டம்

**Definition:** ஒரு GEMM-ஓட DMO reporting நாலு சுயாதீன கடமைகள் இல்ல — அது **ஒரே அடிப்படை trading நடவடிக்கை, நாலு வெவ்வேறு கால அடுக்குகள்ல rolled up**; ஒவ்வொண்ணும் அதுக்கு கீழ உள்ளதிலிருந்து கட்டப்பட்டது.

| Report வகை | Frequency | எதைப் பிடிக்குது | DMO-க்கு ஏன் வேணும் |
|---|---|---|---|
| **Auction Report** | ஒவ்வொரு gilt auction-க்கும் (தோராயமா வாராந்திரம்) | Competitive bids, NCA/PAOF applications, பெற்ற allocation, கொடுத்த விலை | GEMM தன் குறைந்தபட்ச auction-bidding கடமையை நிறைவேத்தியதா உறுதிப்படுத்துது; primary-market statistics-க்கு உணவளிக்குது |
| **Daily Trade Report** | ஒவ்வொரு trading நாளும் | Secondary-market gilt வாங்குதல்/விற்றல்கள் (turnover), gilt வாரியா, விலை, counterparty வகை | DMO-ஓட தொடர் turnover கண்காணிப்புக்கு மூலப்பொருள் |
| **Weekly Trade Report** | வாராந்திரம் | வாரத்துக்கான aggregated தினசரி turnover | Trading நடவடிக்கையின் ஒரு smoothed பார்வை; rolling market-share கணக்கீட்டுக்கு உணவளிக்குது |
| **Aggregated / Periodic Report** | Rolling (உதா., பின்னோக்கிய 6-மாத window-ல கணக்கிடப்பட்டது) | ஒட்டுமொத்த auction பங்கேற்பு + secondary turnover → **market share %** | GEMM தன் நிலையை வெச்சுக்கொள்ளுதான்னு தீர்மானிக்குது — "league table" (Section 7) |

```text
FOUR REPORTS, ONE ROLL-UP CHAIN
========================================================================

  AUCTION REPORT (Wed 19-Aug-2026's gilt auction)
       |
       | contributes into
       v
  DAILY TRADE REPORT (Wed 19-Aug-2026's full day of activity,
  auction allocation PLUS ordinary secondary trading)
       |
       | contributes into
       v
  WEEKLY TRADE REPORT (Mon 17-Aug -- Fri 21-Aug-2026, five days
  of Daily Trade Reports summed)
       |
       | contributes into
       v
  AGGREGATED / PERIODIC REPORT (rolling 6-month window of Weekly
  Trade Reports, converted into a market-share percentage)
```

**Takeaway:** இது [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1-ஓட "ஒரு trade, ஐந்து regimes"-ஐ விட வேறுவகை பன்மை — அங்க, அதே trade வேறு scopes உள்ள ஐந்து *சுயாதீன* regimes-க்கு எதிரா சரிபார்க்கப்பட்டது. இங்க, அதே trading நடவடிக்கை நாலு *சார்ந்த* கால அடுக்குகள் வழியா rolled up ஆகுது, ஒவ்வொண்ணும் முந்தையதின் aggregation. தினசரி எண்ணை தவறா பெற்றா, வாராந்திரமும் இறுதியில periodic market-share எண்ணும் தவறாகிடும் — EMIR/SFTR-ஓட dual-sided reporting தரும் உள்ளமைந்த சோதனை மாதிரி ஒவ்வொரு மட்டத்துலயும் சுயாதீன மறுகணக்கீடு இல்ல (Section 9 இந்த ஒப்பீட்டை இன்னும் விரிவாக்குது).

---

## 4. Auction Reporting — Worked Example

**Definition:** ஒரு UK gilt auction **single-price (uniform-price) auction**-ஆ ஓடுது: வெற்றிகரமான ஒவ்வொரு *competitive* bidder-உம், அவங்க உண்மையில bid பண்ணின விலை எதுவா இருந்தாலும், அவங்க bid அந்த clearing price-ல அல்லது அதுக்கு மேல இருந்தா, அதே **clearing price**-ஐ (ஏற்றுக்கொள்ளப்பட்ட மிகக்குறைந்த விலை) கொடுக்கறாங்க. தனியா, GEMMs-உம் (சில மற்ற தகுதியுள்ள investors-உம்) **Non-Competitive Allocation (NCA)**-க்கு apply பண்ணலாம் — விலை குறிப்பிடப்படாத, நிலையான nominal தொகை, **ஏற்கப்பட்ட சராசரி விலை**ல ஒதுக்கப்படுது. Results வெளியிடப்பட்டதும், சராசரி விலைல கூடுதல் stock-க்காக GEMMs **Post Auction Option Facility (PAOF)** வழியாவும் apply பண்ணலாம்.

```text
GILT AUCTION — 4¼% TREASURY GILT 2036, GBP 3,000,000,000 ON OFFER
========================================================================

  Auction date: Wed 19-Aug-2026     Competitive bidding closes: 10:30am
  Settlement (T+1): Thu 20-Aug-2026 (gilts settle T+1 via CREST --
                     systems-landscape §03's UK gilt settlement cycle)

  BANK OF TORONTO'S COMPETITIVE BIDS:
    Bid 1: GBP 400,000,000 @ price 98.75 (yield ~4.42%)
    Bid 2: GBP 300,000,000 @ price 98.70 (yield ~4.43%)
    Bid 3: GBP 200,000,000 @ price 98.60 (yield ~4.45%)
    Total competitive bid submitted: GBP 900,000,000

  BANK OF TORONTO'S NCA APPLICATION:
    GBP 15,000,000 (illustrative 0.5% of the GBP 3,000,000,000
    on offer -- the typical per-GEMM NCA cap)

  AUCTION-WIDE RESULT (published by the DMO same day):
    Total bids received (all GEMMs): GBP 8,100,000,000
    Bid-to-cover ratio = 8,100,000,000 / 3,000,000,000 = 2.70
    Lowest accepted (clearing) price: 98.62 (yield 4.44%)
    Average accepted price:            98.71 (yield 4.43%)
    Tail = average price - clearing price = 98.71 - 98.62 = 0.09
           (~1 basis point of yield -- a "tight" auction)

  BANK OF TORONTO'S ALLOCATION:
    Bid 1 (98.75 >= clearing 98.62): FILLED  -- 400,000,000
    Bid 2 (98.70 >= clearing 98.62): FILLED  -- 300,000,000
    Bid 3 (98.60 <  clearing 98.62): REJECTED -- 0
    Competitive allocation = 700,000,000 nominal @ 98.62
      = GBP 690,340,000 consideration

    NCA allocation = 15,000,000 nominal @ average price 98.71
      = GBP 14,806,500 consideration

  PAOF (illustrative cap: 10% of own competitive allocation):
    Entitlement = 10% x 700,000,000 = GBP 70,000,000 nominal
    Bank Of Toronto exercises PAOF in full @ average price 98.71
      = GBP 69,097,000 consideration

  TOTAL FROM THIS AUCTION: 715,000,000 + 70,000,000
    = GBP 785,000,000 nominal
    = GBP 690,340,000 + 14,806,500 + 69,097,000
    = GBP 774,243,500 total consideration
```

**இந்த ஒரு auction-க்கு பிடிக்கப்படும் Auction Report fields:** GEMM ID, auction தேதி, gilt identifier, ஒவ்வொரு competitive bid-உம் (விலை, yield, nominal), NCA application, PAOF exercise, clearing price, சராசரி விலை, ஒவ்வொரு அடுக்குலயும் இறுதி allocation/consideration — Section 8-ஓட முழு field reference பாருங்க.

**Takeaway:** ஒரே auction மூணு வேறு விலைகள்ல மூணு பொருளாதார ரீதியா தனி allocations-ஐ உருவாக்குது (competitive-க்கு clearing price, NCA, PAOF ரெண்டுக்கும் சராசரி விலை) — "GEMM gilt-ல GBP 785,000,000 வென்றது"ன்னு மட்டும் பிடிக்கும், ஒவ்வொரு துண்டும் எந்த அடுக்கிலிருந்து வந்ததுன்னு பாதுகாக்காத reporting system, GBP 774,243,500 மொத்த consideration-ஐ அதன் கூறுகளுக்குத் திரும்ப reconcile பண்ணவோ, GEMM-ஓட Bid 3 சரியா நிராகரிக்கப்பட்டதுன்னு நிரூபிக்கவோ முடியாது.

### கூடுதல் உதாரணம் — Uniform-price auction-ஐ ஒரு உள்ளூர் ஒப்புமையோடு

ஒரு கோவில் ஏலத்துல ஒரு பொருளுக்கு பத்து பேர் ஏலம் கேக்கறாங்க; "எல்லா வெற்றியாளரும் கடைசி வெற்றி ஏலத் தொகையையே கொடுப்பாங்க"ன்னு விதி இருந்தா (uniform price): நீங்க ₹100 சொன்னீங்க, கடைசி வெற்றியாளர் ₹90 சொன்னார் → நீங்க ₹90-தான் கொடுக்கறீங்க. நீங்க ₹85 சொன்னீங்கன்னா — 90-க்குக் கீழ — உங்களுக்கு கிடைக்காது (Bid 3 போல). NCA என்பது "ஏலம் எதுவும் சொல்லாம, முடிவுல வர்ற சராசரி விலையில வாங்கிக்கறேன்" என்று சொல்றது.

---

## 5. Daily Trade Reporting — Worked Example

**Definition:** ஒவ்வொரு trading நாளும், ஒரு GEMM தன் **secondary-market gilt turnover**-ஐ — ஒவ்வொரு gilt-லயும், ஒவ்வொரு வாங்குதல், விற்றலையும், counterparty எதுவா இருந்தாலும் — DMO-க்கு report பண்ணுது. இது Sections 6–7 எல்லாம் கட்டப்படற மூல நடவடிக்கை feed.

```text
DAILY TRADE REPORT — BANK OF TORONTO, THU 20-AUG-2026
========================================================================
  (the day after Section 4's auction settles)

  Client business:
    Buys from clients:  GBP 150,000,000 nominal (various gilts)
    Sells to clients:   GBP 210,000,000 nominal (various gilts)

  Interdealer business (with other GEMMs):
    Buys:  GBP  40,000,000 nominal
    Sells: GBP  60,000,000 nominal

  GROSS DAILY TURNOVER REPORTED
    = 150,000,000 + 210,000,000 + 40,000,000 + 60,000,000
    = GBP 460,000,000

  (reported as GROSS two-way turnover -- a GBP 150,000,000 buy and
  a GBP 210,000,000 sell are NOT netted into a GBP 60,000,000 net
  figure; the DMO wants to see the level of ACTIVITY, not just the
  net position change)
```

**Takeaway:** "gross, net இல்ல" என்பது இந்த report-ல மிக முக்கியமான வழக்கம் — [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) settlement-நிலை நடவடிக்கைக்கு (gross settlement vs. netted positions) flag பண்ற அதே வடிவமைப்புத் தேர்வு, இங்கே வேறு காரணத்துக்கா மறுபடி தோன்றுது: DMO ஒரு GEMM *market-making* எவ்வளவு தீவிரமா பண்றதுன்னு அளக்குது; ஒரு நாள்ல GBP 150,000,000 வாங்கி GBP 210,000,000 விற்கும் ஒரு market-maker, அவங்க book-ல *net* விளைவு ஒத்ததா தெரிஞ்சாலும், நாள் முழுக்க ஒரு நிலையான GBP 60,000,000 short position-ஐ வெறுமனே வெச்சிருந்தவரை விட கணிசமா அதிக market-making வேலையைச் செய்தார்.

---

## 6. Weekly Trade Reporting — Worked Example

**Definition:** **Weekly Trade Report** = Section 5-ஓட தினசரி எண்களை trading வாரம் முழுக்க கூட்டியது — rolling market-share கணக்கீட்டுக்கு (Section 7) data போகறதுக்கு முன்னாடி, ஒரு நாள் இரைச்சலை (ஒரு auction நாளின் உயர்ந்த volume போல) தணிக்கும் ஒரு smoothed பார்வை.

```text
WEEKLY TRADE REPORT — BANK OF TORONTO, WEEK OF MON 17-AUG TO FRI 21-AUG-2026
========================================================================
  Mon 17-Aug:                    GBP   320,000,000
  Tue 18-Aug:                    GBP   410,000,000
  Wed 19-Aug (auction day --
    Section 4's auction settlement
    flow plus ordinary secondary
    trading):                    GBP   900,000,000
  Thu 20-Aug (Section 5's figure): GBP  460,000,000
  Fri 21-Aug:                    GBP   350,000,000
  ---------------------------------------------------
  TOTAL WEEKLY TURNOVER:         GBP 2,440,000,000
```

**Takeaway:** புதன்கிழமையின் turnover (GBP 900,000,000) ஒரு சாதாரண நாளின் ரெண்டு மடங்குக்கு மேல இருக்கறதை கவனிங்க — auction நாட்கள் கட்டமைப்பு ரீதியா அதிக-volume, ஏன்னா allocation flows, PAOF exercises, auction-உடன் தொடரும் சாதாரண secondary-market நடவடிக்கை எல்லாம் ஒரே reporting நாளில விழுது. ஒரு வாராந்திர aggregate அந்த உச்சத்தை ஒரு முழுமையான படத்துக்குள் உறிஞ்சுது; தினசரி மட்டும் பார்வை புதன்கிழமையை வழக்கமானதா இல்லாம, சரியில்லாததா காட்டும்.

---

## 7. Aggregated / Periodic Reporting — Market Share & GEMM "League Table"

**Definition:** DMO ஒவ்வொரு GEMM-ஓட **market share**-ஐ ஒரு rolling அடிப்படையில (விளக்கமா, பின்னோக்கிய 6-மாத window) கணக்கிடுது, secondary-market turnover-ஐ (Section 6-ஓட வாராந்திர எண்கள், கூட்டியது) **எல்லா GEMMs-உம் சேர்ந்த மொத்த gilt சந்தை turnover**-க்கு எதிரா சேர்த்து. இதுதான் ஒரு GEMM தன் நிலையை வெச்சுக்கொள்கிறதான்னு தீர்மானிக்கும் எண்.

```text
ROLLING 6-MONTH MARKET SHARE — BANK OF TORONTO
========================================================================
  Total secondary gilt turnover, ALL GEMMs combined,
  rolling 6-month window ending 21-Aug-2026:    GBP 900,000,000,000

  Bank Of Toronto's own turnover, same window:  GBP  40,500,000,000

  Market share = 40,500,000,000 / 900,000,000,000 = 4.5%

  Illustrative minimum required share to retain GEMM status: 2.0%
  -> Bank Of Toronto is comfortably ABOVE the minimum

  Illustrative GEMM league table (by rolling market share):
    1. [Other GEMM]        7.8%
    2. [Other GEMM]        6.9%
    ...
    6. Bank Of Toronto     4.5%
    ...
   16. [Other GEMM]        1.6%  <- BELOW the 2.0% minimum -> under review
```

**குறைந்தபட்சத்துக்கு கீழ விழுந்தா விளைவுகள்:** DMO குறைவா செயல்படும் GEMM-ஐ review-ல வெக்குது; தொடர்ந்த குறைபாடு GEMM panel-ல இருந்து நீக்கத்துக்கு வழிவகுக்கலாம் — auction அணுகல், NCA/PAOF சலுகைகள் (Section 4), DMO-ஓட secondary-market dealing வசதிகளுக்கான அணுகல் (Section 2-ஓட சலுகை table) எல்லாத்தையும் ஒரே நேரத்துல இழக்கும், படிப்படியா இல்ல.

**Takeaway:** நாலு report வகைகள்ல periodic/aggregated report தான் ஒரு **ஒப்பீட்டு** எண்ணை (சந்தை மொத்தத்தில் பங்கு) உருவாக்கும் ஒரே ஒண்ணு, **முழுமையான** எண் (பவுண்ட்ஸ் turnover) இல்ல — அதாவது Bank Of Toronto-ஓட சொந்த reported எண்கள் மட்டும் அது compliant-ஆன்னு சொல்ல முடியாது; அதே window-ல மற்ற ஒவ்வொரு GEMM-உம் எவ்வளவு report பண்ணாங்கங்கறதையும் சமமா சார்ந்தது — Sections 4–6-ல எதிலும் இல்லாத உண்மையிலயே வேறுவகை சார்புநிலை.

---

## 8. என்ன Data Capture பண்ணணும் — Report வகை வாரியா Field Reference

**Definition:** கீழ உள்ள fields, ஒரு GEMM-ஓட சொந்த trade-capture, reporting systems Sections 4–7-ஓட reports-ஐ உண்மையில உருவாக்க வெச்சிருக்க வேண்டியவை; [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §6 தன் நாலு report tables-ஐ தொகுத்த அதே மாதிரி report வகை வாரியா தொகுக்கப்பட்டது.

| Report வகை | Core Fields |
|---|---|
| **Auction Report** | GEMM ID · Auction date · Gilt identifier (name/ISIN) · Bid type (Competitive / NCA / PAOF) · Bid price · Bid yield · Bid nominal amount · Clearing price · Average accepted price · Allocation nominal amount · Allocation price · Consideration amount · Settlement date (T+1) |
| **Daily Trade Report** | GEMM ID · Trade date · Gilt identifier · Gilt type (conventional / index-linked / green) · Direction (buy/sell) · Nominal amount · Price · Yield · Counterparty type (client / other GEMM / interdealer broker) · Venue (OTC bilateral / electronic platform) · Execution time · Settlement date · CREST settlement reference |
| **Weekly Trade Report** | GEMM ID · Week-ending date · Aggregated buy nominal · Aggregated sell nominal · Gross weekly turnover · Gilt type வாரியா பிரிவு (conventional vs. index-linked vs. green) |
| **Aggregated / Periodic Report** | GEMM ID · Reporting period (rolling window start/end) · Total auction bid nominal (period) · Total auction allocation nominal (period) · Total secondary turnover (period) · Market-wide total turnover (period) · Computed market share % · League-table rank · Compliance flag (above/below minimum) |

**Takeaway:** Weekly, Aggregated வரிகள்ல ஒவ்வொரு field-உம் **derived** field — Daily Trade Report-ஓட மூல data-வும் DMO தானே வெளியிடும் market-wide மொத்தமும் வெச்சு முழுசா கணக்கிடக்கூடியது — Auction, Daily வரிகள் ஒரு GEMM-ஓட systems trade நடக்கும் நேரத்துல capture பண்ணும் ஒரே உண்மையிலயே **primary** data-வை கொண்டிருக்கு. நல்லா வடிவமைக்கப்பட்ட reporting pipeline Auction, Daily data-வை source of truth-ஆ store பண்ணி, ஒன்றோடொன்று reconciliation-ல இருந்து விலகக்கூடிய நாலு சுயாதீனமா உள்ளிடப்படற datasets-ஐ வெக்காம, Weekly/Aggregated-ஐ அதன் மேல views-ஆ கணக்கிடும்.

---

## 9. DMO Reporting vs. மற்ற Regimes — அதே Trade, வேறு Destinations

**Definition:** ஒரு gilt trade வழக்கமா **ரெண்டு முற்றிலும் தனி reports**-ஐ, ரெண்டு முற்றிலும் தனி பெறுநர்களுக்கு, ரெண்டு முற்றிலும் தனி காரணங்களுக்காக தூண்டுது — [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1 OTC derivatives-க்கு நிறுவின அதே pattern, இப்போ ஒரு cash government bond-க்கு காட்டப்பட்டது.

```text
ONE GILT TRADE, TWO REPORTS, TWO DESTINATIONS
========================================================================

  Bank Of Toronto sells GBP 10,000,000 nominal of a gilt to a client
  on Thu 20-Aug-2026 (part of Section 5's GBP 210,000,000 client-sell
  total):

  -> UK MiFIR TRANSACTION REPORTING (statutory, to the FCA):
     every execution in an in-scope instrument -- gilts included --
     gets reported for MARKET ABUSE SURVEILLANCE, the same purpose
     Module 14 §4 describes for MiFID II Transaction Reporting, just
     under the UK's onshored MiFIR regime (systems-landscape §10)

  -> DMO DAILY TRADE REPORT (commercial/contractual, to the DMO):
     the SAME GBP 10,000,000 sale is one line inside that day's
     GBP 210,000,000 client-sell total (Section 5) -- reported for
     GEMM PERFORMANCE MONITORING, an entirely different purpose,
     to an entirely different recipient, under NO statute at all
```

| பரிமாணம் | UK MiFIR Transaction Reporting | DMO Reporting |
|---|---|---|
| சட்ட அடிப்படை | Statutory (onshored UK MiFIR) | வணிகம் சார்ந்த (GEMM agreement) |
| பெறுநர் | FCA (ஒரு Approved Reporting Mechanism வழியா) | DMO நேரடியா |
| நோக்கம் | சந்தை முறைகேடு கண்டறிதல் | GEMM செயல்திறன் / market-making கண்காணிப்பு |
| தவறினா விளைவு | Regulatory அமலாக்கம், அபராதங்கள் ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §8) | GEMM நிலையை இழப்பது (Section 7) — வணிக விளைவு, regulatory இல்ல |
| Scope | scope-ல உள்ள ஒவ்வொரு instrument execution-உம், எல்லா firms | Gilts மட்டும், GEMMs மட்டும் |

**Takeaway:** இவற்றை ரெண்டு சுயாதீன pipelines-ஆ நடத்துங்க; கிட்டத்தட்ட ஒத்த trade data-வை அவை பயன்படுத்தினாலும் — தன் UK MiFIR transaction reporting-ஐ முற்றிலும் சரியா வெச்சிருக்கற ஒரு firm-உம் ஒரு DMO reporting தோல்வியால தன் GEMM நிலையை முழுசா இழக்கலாம், நேர்மாறும், ஏன்னா அந்த ரெண்டு கடமைகளும் வேறு எஜமானர்களுக்கு (ஒரு statutory regulator vs. ஒரு வணிக ஒப்பந்தத்தின் counterparty) பதில் சொல்லுது, அவற்றுக்கிடையில பகிரப்பட்ட அமலாக்க mechanism இல்ல.

---

## 10. Settlement Tie-In — CREST & T+1

**Definition:** இந்த module-ல குறிப்பிட்ட ஒவ்வொரு gilt trade-உம் — auction allocations (Section 4), secondary-market trades (Sections 5–6) — **CREST** வழியா, Euroclear UK & International நடத்தும் UK-ஓட Central Securities Depository, **T+1** சுழற்சியில settle ஆகுது ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §3-ஓட settlement-cycle table: "United Kingdom | Gilts (government bonds) | T+1").

**Takeaway:** gilts ஏற்கனவே ரொம்ப காலமா T+1-ல settle ஆகுது — UK *equities்* போல இல்ல, அவை இன்னும் T+2 ஓடுது, அக்டோபர் 2027-ல மட்டுமே T+1-க்கு நகர இலக்கு ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §3, §6) — அதனால ஒரு gilt auction allocation-க்கு "Trade Date + 1"-ஐ சரியா கணக்கிடும் reporting system, அதே desk-ல அதே நாளில execute ஆகற UK equity trade-க்கு அதே "+1" offset-ஐ மறுபயன்படுத்தக் கூடாது; [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §3-ஓட takeaway ஏற்கனவே flag பண்ற ஒரே சந்தை-வாரியா, asset-class-வாரியான பொறி.

---

## 11. அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** [SFTR](../docs/2-reporting/module-16-sftr-reporting.html) §9 போல, இந்த module அவ்வப்போது திருத்தப்படும் விதிகளை மறுசொல்லாம, முதன்மை நிறுவன மூலங்களை சுட்டிக்காட்டுது. ஒரு குறிப்பிட்ட deep link நகர்ந்திருந்தா, பெயரிடப்பட்ட தளத்தையே நேரடியா தேடுங்க.

| Source | What It Covers | Link |
|---|---|---|
| **UK Debt Management Office (DMO)** | Official gilt auction calendar, results, GEMM panel list, published GEMM rules/operational notices | [www.dmo.gov.uk](https://www.dmo.gov.uk) |
| **HM Treasury** | Sets the annual financing "Remit" the DMO executes (Section 1) | [www.gov.uk/government/organisations/hm-treasury](https://www.gov.uk/government/organisations/hm-treasury) |
| **FCA** | UK MiFIR transaction reporting (Section 9), the statutory regime running alongside DMO reporting | [www.fca.org.uk](https://www.fca.org.uk) |
| **Euroclear UK & International (CREST)** | The CSD settling every gilt trade in this module (Section 10) | [www.euroclear.com](https://www.euroclear.com) |
| **Bank of England** | Oversight of CREST as a systemically important market infrastructure ([systems-landscape](../docs/1-capital-markets/systems-landscape.md) §10) | [www.bankofengland.co.uk](https://www.bankofengland.co.uk) |

**Takeaway:** ஒரு build பணிக்கு, சரியான bidding குறைந்தபட்சங்கள், NCA/PAOF வரம்புகள், market-share வரம்புகளுக்கு DMO-ஓட சொந்த வெளியிடப்பட்ட GEMM விதிகளை (Section 2-ஓட callout) source of truth-ஆவும்; உயிருள்ள auction data-வுக்கு DMO-ஓட auction calendar/results பக்கங்களை source of truth-ஆவும்; Section 9-ஓட statutory reporting கடமைக்கு FCA-ஓட UK MiFIR வழிகாட்டுதலை முற்றிலும் தனி source of truth-ஆவும் நடத்துங்க.

---

## 12. Worked Example — Bank Of Toronto-ஓட முழு வாரம், ஆரம்பம் முதல் முடிவு வரை

**Definition:** Sections 4–7-ஐ ஒரு தொடர்ச்சியான தடமா இணைச்சு, நாலு report வகைகளை நாலு தனி உதாரணங்களா இல்லாம, ஒரே இணைந்த data flow-ஆ பார்க்கலாம்.

```text
BANK OF TORONTO — ONE WEEK, FOUR REPORTS
========================================================================

  WED 19-AUG-2026 -- AUCTION REPORT (Section 4)
    Competitive: 700,000,000 filled @ 98.62 = GBP 690,340,000
    NCA:          15,000,000 filled @ 98.71 = GBP  14,806,500
    PAOF:         70,000,000 filled @ 98.71 = GBP  69,097,000
    Total: GBP 785,000,000 nominal / GBP 774,243,500 consideration

  MON 17 -- FRI 21-AUG-2026 -- DAILY TRADE REPORTS, ROLLED INTO
  THE WEEKLY TRADE REPORT (Sections 5-6)
    Mon 320,000,000 + Tue 410,000,000 + Wed 900,000,000
      + Thu 460,000,000 + Fri 350,000,000
    = GBP 2,440,000,000 weekly turnover

  ROLLING 6-MONTH WINDOW ENDING 21-AUG-2026 -- AGGREGATED /
  PERIODIC REPORT (Section 7)
    This week's GBP 2,440,000,000 is one of roughly 26 weekly
    figures summed into Bank Of Toronto's GBP 40,500,000,000
    rolling-window total -> 4.5% market share -> comfortably
    above the illustrative 2.0% minimum -> GEMM status retained

  ALONGSIDE ALL OF THE ABOVE -- Section 9's SEPARATE obligation
    Every individual client trade inside Mon-Fri's totals also
    generates its own UK MiFIR transaction report to the FCA,
    entirely independently of this DMO report trail
```

**Takeaway:** நாலு reports, ஒரு அடிப்படை வார trading, அதே trades மேல இணையா ஓடும் ஐந்தாவது *முற்றிலும் தனி* reporting கடமை (UK MiFIR) — ஒரு GEMM-ஓட reporting infrastructure, இந்த module வேலை செய்து காட்டின விளக்க வாரம் மட்டுமில்ல, ஒவ்வொரு trading வாரமும் தொடர்ந்து சரியா உருவாக்க வேண்டிய முழுப் படம் இதுதான்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto (UK, GEMM code BOT-GEMM-07) | Sections 4–7, 12-ல auction bidding, தினசரி/வாராந்திர turnover, rolling market share வேலை செய்து காட்டப்பட்ட GEMM |
| UK Debt Management Office (DMO) | HM Treasury-ஓட executive agency; gilt auctions நடத்துது, நாலு report வகைகளையும் பெறுது, market share, league table-ஐ கணக்கிடுது |
| HM Treasury | DMO செயல்படுத்தும் வருடாந்திர financing Remit-ஐ நிர்ணயிக்குது (Section 1) |
| மற்ற GEMMs (பெயரிடப்படாதவை) | Auction-ல Bank Of Toronto-வோடு சேர்ந்து bid பண்றாங்க (Section 4), விளக்க league table-ல தோன்றுறாங்க (Section 7) |
| FCA | முற்றிலும் தனி, statutory UK MiFIR transaction report-ஓட பெறுநர் (Section 9) |
| Euroclear UK & International (CREST) | இந்த module-ல ஒவ்வொரு trade-ஐயும் T+1-ல settle பண்ணுது (Section 10) |

---

## Recap Quiz

1.  Section 1-ஐ பயன்படுத்தி, ஒரு GEMM-ஓட Daily Trade Report submit பண்ணத் தவறுவது *regulatory* பிரச்சனையா இல்லாம *வணிக* பிரச்சனையா இருக்கறது ஏன் — தவறிய EMIR அல்லது SFTR report ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html), [SFTR module](../docs/2-reporting/module-16-sftr-reporting.html)) இல்லாத வகையில?
2.  Section 4-ஐ பயன்படுத்தி, Bank Of Toronto-ஓட Bid 3 (GBP 200,000,000 @ 98.60) நிராகரிக்கப்படுது, Bids 1, 2 நிரப்பப்படுது ஏன் — நிரப்பப்பட்ட bids உண்மையில எந்த விலையில settle ஆகுது?
3.  Section 4-ஐ பயன்படுத்தி, NCA, PAOF allocations competitive bidders கொடுக்கற clearing price (98.62)-க்குப் பதிலா சராசரி ஏற்கப்பட்ட விலையில (98.71) விலை நிர்ணயிக்கப்படுறது ஏன்?
4.  Section 5-ஐ பயன்படுத்தி, Bank Of Toronto-ஓட அந்த நாளின் NET position மாற்றத்துக்குப் பதிலா GROSS தினசரி turnover-ஐ (buys-உம் sells-உம் தனித்தனியா report பண்ணப்பட்டு) DMO விரும்புறது ஏன்?
5.  Section 7-ஐ பயன்படுத்தி, Bank Of Toronto தன் சொந்த reported எண்களிலிருந்து மட்டும், குறைந்தபட்ச market-share வரம்புக்கு compliant-ஆன்னு தானே தீர்மானிக்க முடியாதது ஏன்?
6.  Section 8-ஐ பயன்படுத்தி, நாலு report வகைகள்ல எந்த ரெண்டு உண்மையிலயே PRIMARY data-வை கொண்டிருக்கு, எந்த ரெண்டு அவற்றிலிருந்து முழுசா DERIVED — ஒரு reporting pipeline எப்படி கட்டப்படணும்ங்கறதுக்கு இந்த வேறுபாடு ஏன் முக்கியம்?
7.  Section 9-ஐ பயன்படுத்தி, Bank Of Toronto-க்கு ஒரு சரியான UK MiFIR transaction-reporting பதிவு இருந்தும் தன் GEMM நிலையை இழக்க முடியுமா? ரெண்டு கடமைகளின் வேறு சட்ட அடிப்படைகளோட இணைத்து விளக்குங்க.
8.  Section 10-ஐ பயன்படுத்தி, அதே desk-ல அதே நாளில execute ஆகற ஒரு UK gilt trade-க்கும் UK equity trade-க்கும் ஒரே "Trade Date + 1" settlement-offset logic-ஐ ஒரு reporting system பாதுகாப்பா மறுபயன்படுத்த முடியாதது ஏன்?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. ஏன்னா DMO reporting Bank Of Toronto-வுக்கும் DMO-வுக்கும் இடையிலான ஒரு தனியார் வணிக ஒப்பந்தத்தில் (GEMM agreement) இருந்து வருது, சட்டத்தில் இருந்து இல்ல — தவறினா விளைவு GEMM நிலையை (ஒரு வணிகச் சலுகை) இழப்பது, ஒரு conduct authority-யிடமிருந்து regulatory அபராதம் இல்ல; EMIR/SFTR-ல Trade Repository-யும் regulator-உம் ரெண்டு trading counterparties-ஓட சொந்த ஒப்பந்தத்துக்கு முற்றிலும் வெளியே இருப்பதற்கு மாறா.**

Section 1-ஓட "முதலிலயே முக்கியமான வேறுபாடு" இதை நேரடியா சொல்லுது: Trade Repository இல்ல, regulation எண் இல்ல, தாமதமான DMO report-க்கு regulator விதிக்கும் அபராதம் இல்ல — GEMM நிலையை இழப்பது தீவிர வணிக விளைவா இருந்தாலும், அது ஒரு regulatory filing-ஐ விட supplier scorecard-க்கு நெருக்கம்.

**2. Bid 3-ஓட விலை (98.60) auction-ஓட clearing விலைக்கு (98.62) கீழ, அதனால நிராகரிக்கப்படுது; Bids 1 (98.75), 2 (98.70) ரெண்டும் clearing விலைல அல்லது அதுக்கு மேல, அதனால ரெண்டும் நிரப்பப்படுது — முக்கியமா, அவை Bank Of Toronto உண்மையில bid பண்ணின விலையில இல்ல, CLEARING விலையில (98.62) settle ஆகுது.**

Section 4 இது single-price (uniform-price) auction-ன்னு சொல்லுது: "வெற்றிகரமான ஒவ்வொரு competitive bidder-உம்... அவங்க உண்மையில bid பண்ணின விலை எதுவா இருந்தாலும், அவங்க bid அந்த clearing price-ல அல்லது அதுக்கு மேல இருந்தா, அதே clearing price-ஐ கொடுக்கறாங்க." Bank Of Toronto-ஓட உயர்ந்த bid விலைகள் (98.75, 98.70) அதன் bids clear ஆகும்னு தீர்மானிக்க மட்டும்தான் முக்கியம், இறுதியில அது எவ்வளவு கொடுக்கும்ங்கறதுக்கு இல்ல.

**3. ஏன்னா NCA-வும் PAOF-உம், வடிவமைப்பிலேயே, ஒரு competitive விலையை குறிப்பிடாத பங்கேற்பாளர்களுக்கான mechanisms — competitive bidders தங்கள் bid-ல விலை risk-ஐ ஏற்கறதால சம்பாதிக்கும் ஒருவேளை சாதகமான clearing விலையை அவை பெறாம இருக்க, சராசரி ஏற்கப்பட்ட விலையில ஒதுக்கப்படுது.**

Section 4-ஓட வரையறை NCA "விலை குறிப்பிடப்படாத, நிலையான nominal தொகை, சராசரி ஏற்கப்பட்ட விலையில ஒதுக்கப்படுது"ன்னும், PAOF-உம் "சராசரி விலைல" apply பண்ணப்படுதுன்னும் சொல்லுது — ரெண்டுமே (வாங்குபவர் பார்வையில) clearing விலையை விட மோசமா விலையிடப்படுது, ஏன்னா முடிவுக்கு முன்னாடி ஒரு குறிப்பிட்ட விலைக்கு உறுதியளிக்க பங்கேற்பாளரை அவை கோரலை.

**4. ஏன்னா DMO ஒரு GEMM நிகழ்த்திய உண்மையான market-making நடவடிக்கையை அளக்குது, நாளின் முடிவு position மாற்றத்தை மட்டும் இல்ல — GBP 150,000,000 வாங்கி GBP 210,000,000 விற்ற (Section 5) ஒரு GEMM, நாள் முழுக்க நிலையான net short position-ஐ வெறுமனே வெச்சிருந்த ஒருவரை விட, net விளைவு ஒத்ததா தெரிஞ்சாலும், கணிசமா அதிக இருவழி விலை-நிர்ணய வேலையைச் செய்தது.**

Section 5-ஓட takeaway இதை நேரடியா சொல்லுது, [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html)-ஓட gross-vs-net settlement வேறுபாட்டோட இணைத்து: gross turnover உண்மையான dealing நடவடிக்கையை பிரதிபலிக்குது, net position மாற்றம் DMO சரிபார்க்க முயற்சிக்கற market-making நடத்தையையே மறைச்சிடும்.

**5. ஏன்னா market share ஒரு ஒப்பீட்டு எண் — Bank Of Toronto-ஓட turnover-ஐ ALL GEMMs-ஓட மொத்த turnover-ஆல வகுத்தது — அதனால அதன் சொந்த reported எண் மட்டும், அதே rolling window-ல மற்ற ஒவ்வொரு GEMM-உம் என்ன report பண்ணுதுன்னு தெரியாம, compliance பத்தி எதுவும் சொல்லாது.**

Section 7-ஓட takeaway இதை வெளிப்படையா சொல்லுது: periodic/aggregated report "ஒரு ஒப்பீட்டு எண்ணை... முழுமையான எண்ணுக்குப் பதிலா உருவாக்குது", அதாவது DMO aggregate-ஐ வெளியிடும் வரை Bank Of Toronto கட்டுப்படுத்தாத, பார்க்காத எண்களை சமமா சார்ந்தது அதன் compliance.

**6. Auction Report-உம் Daily Trade Report-உம் PRIMARY data-வை கொண்டிருக்கு (trade நடக்கும் இடத்துலயே நேரடியா capture ஆனது); Weekly-உம் Aggregated/Periodic-உம் முழுக்க DERIVED — Daily Trade Report data-வில் இருந்து (Aggregated report-க்கு, DMO-ஓட சொந்தமா வெளியிடப்பட்ட market-wide மொத்தத்தோட) கணக்கிடக்கூடியது. Auction/Daily data-வை ஒரே source of truth-ஆ store பண்ணி, Weekly/Aggregated-ஐ அதன் மேல views-ஆ கணக்கிடும் pipeline, ஒன்றோடொன்று reconciliation-ல இருந்து விலகக்கூடிய நாலு சுயாதீனமா உள்ளிடப்படற datasets-ன் risk-ஐ தவிர்க்குது — அதனால இது முக்கியம்.**

Section 8-ஓட takeaway இந்த வேறுபாட்டை நேரடியா சொல்லுது, சரியான வடிவமைப்பா சரியாக இந்த "source of truth பிளஸ் derived views" கட்டமைப்பை பரிந்துரைக்குது.

**7. ஆமா — ஏன்னா UK MiFIR transaction reporting-உம் DMO reporting-உம் முற்றிலும் வேறு சட்ட அடிப்படைகளை (statutory vs. வணிகம், Section 9-ஓட ஒப்பீட்டு table) கொண்டிருக்கு, அவற்றுக்கிடையில பகிரப்பட்ட அமலாக்க mechanism இல்ல. ஒரு கடமையில சரியான செயல்திறன் மற்றதைப் பத்தி எதுவும் சொல்லாது; ஒரு GEMM FCA-வை முழுசா திருப்திப்படுத்தி, DMO-ஓட தனி, ஒப்பந்த reporting அல்லது market-share தேவைகள்ல தோற்கலாம்.**

Section 9-ஓட takeaway இதை நேரடியா சொல்லுது: "தன் UK MiFIR transaction reporting-ஐ முற்றிலும் சரியா வெச்சிருக்கற ஒரு firm-உம் ஒரு DMO reporting தோல்வியால தன் GEMM நிலையை முழுசா இழக்கலாம், நேர்மாறும், ஏன்னா அந்த ரெண்டு கடமைகளும் வேறு எஜமானர்களுக்கு பதில் சொல்லுது... அவற்றுக்கிடையில பகிரப்பட்ட அமலாக்க mechanism இல்ல."

**8. ஏன்னா gilts T+1-ல settle ஆகுது, UK equities இன்னும் T+2-ல (அக்டோபர் 2027-ல மட்டுமே T+1-க்கு நகர இலக்கு) — ஒவ்வொரு UK trade-க்கும் "+1"-ஐ hard-code பண்ற system, அந்த 2027 மாற்றம் உண்மையில நடக்கும் வரை ஒவ்வொரு UK equity trade-க்கும் தவறான settlement தேதியை கணக்கிடும், சரியா [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §3 ஏற்கனவே எச்சரிக்கற சந்தை-வாரியா, asset-class-வாரியா பொறி.**

Section 10-ஓட takeaway இதை நேரடியா சொல்லுது, ஒரு உலகளாவிய settlement-offset அனுமானம் அதே சந்தைக்குள் வெவ்வேறு asset classes முழுக்க பொருத்தப்பட்ட நிமிஷமே உடையுதுன்னு [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §3-ல இருந்து அதே அடிப்படை எச்சரிக்கையை மேற்கோள் காட்டி.
