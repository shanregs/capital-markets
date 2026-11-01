# Capital Markets — Abbreviations Glossary (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> இது module files-க்கு துணையான reference — தனி module இல்ல. ஒரு reporting field-ல தெரியாத acronym வந்தா இங்க பாருங்க. Full Form English-லயே இருக்கு (அதுதான் இந்த industry-ல பேசற மொழி); "விளக்கம்" column பேச்சுத் தமிழ்ல.
> English மூலம்: [Abbreviations Glossary](../docs/1-capital-markets/abbreviations.html)

---

## 1. Instrument & Trade Identifiers

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **ISIN** | International Securities Identification Number | ஒரு security-க்கான உலகளாவிய 12-எழுத்து identifier (2 எழுத்து country code + 9 எழுத்து body + check digit). [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2 பாருங்க. |
| **CUSIP** | Committee on Uniform Securities Identification Procedures | US/Canada securities-க்கான 9-எழுத்து identifier; North American securities-க்கு ISIN-க்குள்ளேயே பதிஞ்சிருக்கும். |
| **SEDOL** | Stock Exchange Daily Official List | UK-ல பிறந்த 7-எழுத்து identifier; சில London-listed securities-க்கு இன்னும் பயன்படுது. |
| **UPI** | Unique Product Identifier | OTC derivative *product*-க்கான (தனி trade-க்கு இல்ல) உலகளாவிய identifier — regulatory reporting-ல internal codes-ஐ மெல்ல மாத்துது. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §11. |
| **UTI** | Unique Trade Identifier | ஒரு குறிப்பிட்ட derivative *transaction*-க்கான globally unique identifier; execution-ல உருவாகும் — derivatives உலகின் "Trade ID". |
| **CFI Code** | Classification of Financial Instruments (ISO 10962) | Security master-ல instrument வகையை (debt/equity, fixed/floating, voting rights) சொல்லும் 6-எழுத்து code. Derivatives-க்கு ISDA taxonomy மாதிரி, cash instruments-க்கு CFI. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §6. |
| **LEI** | Legal Entity Identifier | ஒரு *legal entity*-க்கான (bank, fund, corporate) 20-எழுத்து global identifier — instrument-க்கு இல்ல. பெரும்பாலான regulatory trade reports-ல counterparties-ஐ அடையாளம் காட்ட கட்டாயம். [Module 13](../docs/1-capital-markets/module-13-parties-leis.html). CDS-ல LEI *Reference Entity*-யையும் அடையாளம் காட்டும் — இது counterparties-ஐ தாண்டின இரண்டாவது role ([Module 9](../docs/1-capital-markets/module-09-credit.html) §6). |
| **MIC** | Market Identifier Code (ISO 10383) | ஒரு குறிப்பிட்ட exchange / trading venue-ஐ அடையாளம் காட்டும் 4-எழுத்து code (எ.கா. NYSE-க்கு `XNYS`). |

---

## 2. Market Structure & Venues

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **OTC** | Over-the-Counter | மத்திய exchange இல்லாம, ரெண்டு தரப்பு நேரடியா பேசி முடிக்கற trade. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §5. |
| **CCP** | Central Counterparty | Trade ஆனப்புறம் ரெண்டு counterparties நடுவுல புகுந்து, ஒவ்வொரு seller-க்கும் buyer-ஆ, ஒவ்வொரு buyer-க்கும் seller-ஆ ஆகி counterparty risk-ஐ குறைக்கும் clearing house. Novation: [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6; Risk குறைப்பு: [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html). Listed derivative-ல novation execution-லயே உடனே நடக்கும் ([Module 7](../docs/1-capital-markets/module-07-commodities.html) §5, [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) §1). |
| **LCH / SwapClear** | London Clearing House / SwapClear | LCH லண்டனை தலைமையிடமா கொண்ட CCP group (LSEG-ஓட பகுதி). **SwapClear** என்பது LCH-ஓட interest rate swaps clearing service — உலகின் centrally-cleared IRS volume-ல பெரும்பகுதியை novate பண்ணுது. "LCH" = legal entity/group, "SwapClear" = அதுக்குள்ள product line. இந்த roadmap-ல concrete CCP உதாரணமா பயன்படுது. [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6, [Module 8](../docs/1-capital-markets/module-08-rates.html) §7-9, [Systems Landscape](./Systems-Landscape-Tamil.html) §6, §11. |
| **CSD** | Central Securities Depository | ஒரு market-ல securities-ஓட இறுதி சட்டப்பூர்வ உரிமையை பதிவு செய்யற ஒரே மத்திய ledger (எ.கா. Canada-ல CDS, US-ல DTC). [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7. |
| **ICSD** | International Central Securities Depository | Cross-border / international securities-ஐ settle பண்ணும் CSD (Euroclear, Clearstream). |
| **MTF** | Multilateral Trading Facility | EU-ல regulate ஆன trading venue; முறையான "exchange" இல்ல, ஆனா பல buyers/sellers-ஐ பொது rules-ல match பண்ணும். |
| **SEF** | Swap Execution Facility | சில standardized OTC swaps-ஐ trade பண்ண US-ல regulate ஆன electronic venue; 2008 crisis-க்கு பிறகு derivatives trading-ல வெளிப்படைத்தன்மை கொண்டுவர கட்டாயமாக்கப்பட்டது. |
| **SI** | Systematic Internalizer | Client orders-ஐ regulated exchange/MTF-க்கு வெளியே தன் சொந்த கணக்குல execute பண்ணும் firm (குறிப்பிட்ட volume அளவுக்கு மேல); EU/MiFID II concept. |

---

## 3. Products & Derivatives

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **IRS** | Interest Rate Swap | ரெண்டு தரப்பு ஒரு notional மேல fixed vs floating வட்டி cash flows-ஐ பரிமாறிக்கறாங்க. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §8. |
| **FRA** | Forward Rate Agreement | ஒரே ஒரு எதிர்கால காலத்துக்கான வட்டி விகிதத்தை முன்கூட்டியே பூட்டும் contract — அடிப்படையில் ஒரு-period IRS. |
| **CDS** | Credit Default Swap | ஒரு reference entity default ஆகாம பாதுகாப்பு; buyer premium கட்டுவார், credit event நடந்தா seller payout தருவார். [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §9. |
| **TRS** | Total Return Swap | ஒரு தரப்பு ஒரு asset-ஓட மொத்த return-ஐ (விலை மாற்றம் + வருமானம்) கொடுக்குது; மறுதரப்பு funding-rate payment தருது. |
| **NDF** | Non-Deliverable Forward | Currency முழுசா convertible இல்லாதப்ப, ரெண்டு currencies-ஐ physically மாத்தாம, நிகர cash வித்தியாசம் மட்டும் settle ஆகும் FX Forward. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §10. |
| **ETD** | Exchange-Traded Derivative | Regulated exchange-ல trade ஆகும் derivative (future, listed option); OTC-க்கு எதிர்ப்பதம். |
| **ETF** | Exchange-Traded Fund | Units ஒரு share மாதிரி exchange-ல trade ஆகும் fund; பொதுவா ஒரு index-ஐ பின்தொடரும். |
| **MBS** | Mortgage-Backed Security | வீட்டுக் கடன் pool-ஆல ஆதரிக்கப்படும் security; principal படிப்படியா amortize ஆகும். [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §6. |
| **ABS** | Asset-Backed Security | Mortgage அல்லாத assets (auto loans, credit card receivables) pool-ஆல ஆதரிக்கப்படும் security. |
| **CLO** | Collateralized Loan Obligation | Leveraged corporate loans pool-ஆல ஆதரிக்கப்படும் securitization. |
| **CDO** | Collateralized Debt Obligation | Debt instruments pool-ஆல ஆதரிக்கப்படும், risk/seniority படி tranche செய்யப்பட்ட பரந்த securitization structure. |
| **ADR / GDR** | American / Global Depositary Receipt | ஒரு வெளிநாட்டு company-ஓட shares-ஐ பிரதிநிதித்துவப்படுத்தும் certificate; உள்நாட்டு exchange-ல trade ஆகும் (எ.கா. ஒரு ஐரோப்பிய company-ஓட shares US-ல ADR-ஆ). |
| **CTD** | Cheapest to Deliver | Physically-settled bond future-ல, delivery basket-ல இருந்து short-க்கு source பண்ணி deliver பண்ண மிக மலிவான eligible bond; conversion factor வெச்சு short தேர்ந்தெடுக்கும். [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) §5. |

---

## 4. Rates & Benchmarks

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **CORRA** | Canadian Overnight Repo Rate Average | Canada-வின் risk-free overnight benchmark; **Bank of Canada** தினமும், Government of Canada securities-ஆல collateral ஆன உண்மையான overnight repo transactions-ல இருந்து கணக்கிடுது (CDOR/LIBOR மாதிரி bank-ஓட மதிப்பீடுகள்ல இருந்து இல்ல). CAD swaps-ல floating leg. ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8; CDOR→CORRA கதை [Module 8](../docs/1-capital-markets/module-08-rates.html) §1). |
| **CDOR** | Canadian Dollar Offered Rate | CORRA மாற்றிய பழைய, survey-அடிப்படையிலான CAD interbank benchmark — banks தாங்க கடன் வாங்க முடியும்னு நம்பறதை தாங்களே சொல்வாங்க. **June 2024-க்கு பிறகு நிறுத்தப்பட்டது.** [Module 8](../docs/1-capital-markets/module-08-rates.html) §1. |
| **SOFR** | Secured Overnight Financing Rate | US-ன் risk-free overnight benchmark; USD LIBOR-ஓட வாரிசு. |
| **SONIA** | Sterling Overnight Index Average | UK-ன் risk-free overnight benchmark; GBP LIBOR-ஓட வாரிசு. |
| **LIBOR** | London Interbank Offered Rate | பழைய interbank benchmark; 2023-க்குள் SOFR/SONIA/CORRA மாதிரி "risk-free rates"-ஆல மாற்றப்பட்டது. பழைய trade data-ல இன்னும் தெரியும். |
| **OIS** | Overnight Index Swap | Floating leg-ஆ compounded overnight rate (CORRA/SOFR மாதிரி) reference பண்ணும் swap — இப்போ standard IRS structure. |
| **bps** | Basis Points | 1 bps = 0.01% (ஒரு சதவீதத்தில் நூறில் ஒண்ணு). Spreads, rates, சின்ன விலை நகர்வுகளை துல்லியமா சொல்ல (எ.கா. CDS spread "120 bps"). முழு theory: [Module 24 — Basis Points](../docs/1-capital-markets/module-24-basis-points-deep-dive.html). |

---

## 5. Pricing & Valuation

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **YTM** | Yield to Maturity | ஒரு bond-ஐ maturity வரை வெச்சிருந்தா கிடைக்கும் மொத்த ஆண்டு return — கொடுத்த விலை, coupons, pull-to-par லாப/நஷ்டம் எல்லாம் சேர்த்து. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §1. |
| **MTM** | Mark-to-Market | ஒரு position-ஐ அதன் அசல் trade விலையில இல்லாம, நடப்பு market விலை/rates-ல மறுமதிப்பீடு செய்யறது — Middle Office தினமும் செய்யறது. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §4. |
| **Position** | (acronym இல்ல) | இதுவரை execute ஆன எல்லா trades-ல இருந்தும் கட்டப்பட்ட, ஒரு நேரத்தில் ஒரு instrument-ல நிகர holding — நீடிக்கும், தினமும் மறுமதிப்பீடு ஆகும் *நிலை*; Trade (ஒரு-முறை நிகழ்வு) இல்ல. [Module 25 — Position](../docs/1-capital-markets/module-25-position-deep-dive.html). |
| **NPV** | Net Present Value | எதிர்கால cash flows-ஐ தகுந்த rate-ல discount பண்ணி இன்றைய மதிப்பா மாத்தின தொகை. [Module 10](../docs/1-capital-markets/module-10-valuation.html); FRA upfront settlement-ல ([Module 8](../docs/1-capital-markets/module-08-rates.html) §4b) NPV-style discounting ஏற்கனவே வருது. |
| **PV01 / DV01** | Price/Dollar Value of 1 Basis Point | வட்டி 1 bps நகர்ந்தா ஒரு position-ஓட value எவ்வளவு மாறும் — முக்கிய interest-rate risk sensitivity. [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §1, [Module 8](../docs/1-capital-markets/module-08-rates.html) §1, முழுசா [Module 10](../docs/1-capital-markets/module-10-valuation.html). Credit-ல spread நகர்வுக்கு இணையானது "Spread DV01" / "CS01" ([Module 24](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §5). |
| **P&L** | Profit and Loss | ஒரு காலத்தில் ஒரு position / trade-ல வந்த லாபம் அல்லது நஷ்டம். |
| **NAV** | Net Asset Value | ஒரு fund-ஓட holdings-ஓட (assets − liabilities) ஒரு-unit மதிப்பு; fund units / ETF shares-ஐ price பண்ண. |
| **IPV** | Independent Price Verification | Front Office-ஓட சொந்த mark-ஐ எதிர்த்து, ஒரு position-ஓட valuation-ஐ சுதந்திரமா மறு-derive பண்ணும் control (பொதுவா Middle Office-ல Product Control); Fair Value Level அதிகமானா ஆய்வும் கடுமையாகும் ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §6-7). |

---

## 6. Settlement & Clearing

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **T, T+1, T+2** | Trade Date, Trade Date + n business days | Standard settlement நேர shorthand. உண்மையான தேதிகளோட உதாரணம்: [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §6. |
| **DvP** | Delivery versus Payment | Securities-உம் பணமும் ஒரே நேரத்துல நகர்வதை உறுதி செய்யும் settlement முறை — யாரும் ஒரே ஒரு leg மட்டும் வெச்சு ஆபத்துல இருக்க மாட்டாங்க. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7. |
| **SSI** | Standard Settlement Instructions | ஒரு party-க்கு securities / cash எங்க deliver பண்ணணும்னு (custodian, account number…) முன்கூட்டியே ஒப்புக்கொண்ட நிலையான instructions — reference data, trade-wise data இல்ல. |
| **FOP** | Free of Payment | பணம் இல்லாத securities transfer (DvP-க்கு எதிர்ப்பதம்) — எ.கா. collateral நகர்த்துறது, உள் transfer. |

---

## 7. Risk, Collateral & Margin

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **IM** | Initial Margin | MTM கூட ஆகாத முன்னாடியே, எதிர்கால நஷ்டங்களுக்கு ஈடா ஒரு derivative position-க்கு முன்கூட்டியே post பண்ணும் collateral. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html). |
| **VM** | Variation Margin | *நடப்பு* mark-to-market லாப/நஷ்டத்தை ஈடு செய்ய அவ்வப்போது (exchange-traded, பல cleared products-ல தினமும்) பரிமாறப்படும் collateral. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html); தினசரி உதாரணங்கள்: [Module 4](../docs/1-capital-markets/module-04-equity.html) §4c, [Module 7](../docs/1-capital-markets/module-07-commodities.html) §5, [Module 8](../docs/1-capital-markets/module-08-rates.html) §5, [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) §4c. |
| **CSA** | Credit Support Annex | ISDA Master Agreement-ஓட annex; ரெண்டு OTC derivative counterparties-ஓட collateral விதிகளை சொல்லும் சட்ட ஆவணம். [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7, [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html). |
| **VaR** | Value at Risk | கொடுக்கப்பட்ட காலம், confidence level-ல ஒரு portfolio-க்கு எதிர்பார்க்கும் அதிகபட்ச நஷ்டத்தின் புள்ளிவிவர மதிப்பீடு. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html). |
| **PFE** | Potential Future Exposure | ஒரு derivative-ஓட மீதமுள்ள வாழ்நாளில் counterparty credit exposure எவ்வளவு வளரலாம்னு மதிப்பீடு. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html). |
| **CVA** | Credit Valuation Adjustment | Contract maturity-க்கு முன்னாடி counterparty default ஆகும் risk-ஐ பிரதிபலிக்கும் derivative-ஓட value மாற்றம்; Credit-ஓட default-risk mechanics ([Module 9](../docs/1-capital-markets/module-09-credit.html)) Risk & Exposure-ல ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)) பயன்படுவது. |
| **CE** | Current Exposure | Counterparty இப்பவே default ஆனா, firm-க்கு *இன்னிக்கு* வரக்கூடிய நஷ்டம் — MAX(MTM, 0), எப்பவும் எதிர்மறை ஆகாது. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §1. |
| **SA-CCR** | Standardized Approach for Counterparty Credit Risk | Derivatives மேல counterparty credit risk-க்கு regulatory capital கணக்கிட banks பயன்படுத்தும் தற்போதைய Basel முறை; Current Exposure / PFE / netting-ஐ ஒரு regulator formula-வா மாத்துது. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §8. |
| **SIMM** | ISDA Standard Initial Margin Model | Centrally clear ஆகாத derivatives-க்கு, position-ஓட risk sensitivities-ல இருந்து நேரடியா Initial Margin கணக்கிடும் ISDA-வின் standard model. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §7, [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html). |
| **UMR** | Uncleared Margin Rules | குறிப்பிட்ட அளவுகளுக்கு மேல, centrally clear ஆகாத derivatives-க்கு Initial & Variation Margin கட்டாயமாக்கும் IOSCO/BCBS framework. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §8, [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html). |
| **CCAR** | Comprehensive Capital Analysis and Review | பெரிய banks-க்கான US Federal Reserve-ன் ஆண்டு stress-testing; VaR-style portfolio risk logic-ஐ தீவிர hypothetical macroeconomic scenarios-க்கு நீட்டுது. [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §8. |
| **Haircut** | — (acronym இல்ல; standard shorthand) | Collateral-ஆ பயன்படுத்தும்போது ஒரு security-ஓட Market Value-ல பிடிக்கப்படும் சதவீத குறைப்பு; அதை Collateral Value-வா மாத்துது. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §3. |
| **MTA** | Minimum Transfer Amount | MTM தினமும் அசையும்போது சின்ன சின்ன transfers நடக்காம இருக்க, Threshold-ஓட சேர்ந்து CSA அனுமதிக்கும் மிகச்சிறிய collateral நகர்வு. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §5. |
| **AANA** | Average Aggregate Notional Amount | UMR-ஓட ஆறு-wave rollout-ல ஒரு firm எந்த கட்டத்துக்குள் வருதுன்னு தீர்மானிக்கும் அளவீடு — AANA அதிகமான dealers முதல்ல scope-க்கு வந்தாங்க. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §8. |
| **HQLA** | High-Quality Liquid Assets | Basel LCR-ன் கீழ, 30-நாள் stress scenario-ஐ தாங்க ஒரு bank போதுமான அளவு வெச்சிருக்கணும்கிற assets pool — collateral post பண்ணுறது bank-ஓட சொந்த HQLA-வை கட்டிப்போடுது. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §8. |
| **LCR** | Liquidity Coverage Ratio | 30-நாள் liquidity stress-ஐ தாங்க போதுமான HQLA வெச்சிருக்கணும்னு கோரும் Basel III/IV ratio — collateral management-ஓட நேரடி தொடர்பு ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §8). |
| **CSDR** | Central Securities Depositories Regulation | EU CSDs வழியா settle ஆகும் securities-collateral நகர்வுகளுக்கு பொருந்தும் settlement-ஒழுக்க விதிகள் (கட்டாய buy-ins, fails-க்கு cash அபராதம்). [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §8. |

---

## 8. Global Regulatory & Industry Bodies

தனி jurisdictions-க்கு மேல / குறுக்கே இயங்கும் அமைப்புகள், standards — Section 9-ல் வர ஒவ்வொரு regional regime-உம் இறுதியில இவற்றில் ஒண்ணுக்கு திரும்பிப் போகும்.

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **ISDA** | International Swaps and Derivatives Association | OTC derivative ஆவணங்களை (Master Agreement), product classification-ஐ (ISDA Taxonomy) standardize பண்ணும் trade body. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7. இதன் **Determinations Committees** — அரசு regulator இல்ல, industry panels — ஒரு CDS Credit Event நடந்ததா என தீர்மானிக்கும்; CDS lifecycle-ல மிக முக்கிய முடிவு ([Module 9](../docs/1-capital-markets/module-09-credit.html) §5, §8). |
| **BCBS** | Basel Committee on Banking Supervision | Bank capital / liquidity விதிகளின் ("Basel" accords) உலகளாவிய standard-setter — IM/VM, capital தேவைகளை இயக்குது. |
| **IOSCO** | International Organization of Securities Commissions | பல jurisdictions-ஓட securities regulators-ஐ ஒருங்கிணைக்கும் உலகளாவிய standard-setter — பெரும்பாலான தேசிய trade-reporting regimes-ஓட கொள்கைகளுக்கு அடிப்படை. |
| **FSB** | Financial Stability Board | உலக நிதி அமைப்பு risk-ஐ கண்காணிக்கும் G20 அமைப்பு; 2008 crisis-க்கு பிறகு OTC derivative சீர்திருத்தங்களை (clearing, reporting, margining) உலகளவில் கட்டாயமாக்கியது இதுதான்; ஒவ்வொரு region-உம் அப்புறம் அதை உள்ளூரில செயல்படுத்துச்சு. |
| **CPMI** | Committee on Payments and Market Infrastructures | Payment, clearing, settlement systems-ஓட உலகளாவிய standard-setter (CCP/CSD standards-ல IOSCO-வோட இணைந்து வேலை செய்யும்). |
| **GLEIF** | Global Legal Entity Identifier Foundation | உலகளாவிய LEI அமைப்பை மேற்பார்வையிடும் அமைப்பு — ஒவ்வொரு நாட்டிலும் firms-க்கு LEI வழங்கும் Local Operating Units (LOUs)-ஐ அங்கீகரிக்கும். |
| **LOU** | Local Operating Unit | LEI-களை உண்மையில வழங்கி புதுப்பிக்க GLEIF-ஆல அங்கீகரிக்கப்பட்ட தேசிய அமைப்பு — LEI-ஓட ACTIVE/LAPSED நிலை ([Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §7) LOU மட்டத்துல பராமரிக்கப்படும். |
| **IASB** | International Accounting Standards Board | உலகளாவிய accounting standard-setter — Fair Value, அதன் Level 1/2/3 hierarchy-ஐ வரையறுக்கும் **IFRS 13**-ஐ வெளியிடுது ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §6). இந்த glossary-ல markets/derivatives regulator இல்லாத முதல் standard-setter — இது accounting அமைப்பு. |
| **FASB** | Financial Accounting Standards Board | US accounting standard-setter — IFRS 13-க்கு US GAAP இணையான **ASC 820**-ஐ வெளியிடுது; அதே Level 1/2/3 Fair Value concept. [Module 10](../docs/1-capital-markets/module-10-valuation.html) §8. |
| **FRTB** | Fundamental Review of the Trading Book | Trading-book market risk-க்கு banks regulatory capital கணக்கிடும் முறையை BCBS 2008-க்கு பிறகு மாத்தியமைத்தது — observe பண்ண கடினமான, verify பண்ண கடினமான (Level 2/3) valuations கொண்ட positions வெச்சிருக்கும் banks-க்கு நேரடியா அபராதம். [Module 10](../docs/1-capital-markets/module-10-valuation.html) §8. |

---

## 9. Regional Reporting Regimes & Regulators — US / UK / EU / Canada / Hong Kong

Bank Of Toronto book பண்ணும் ஒவ்வொரு OTC derivative trade-உம் உள்ளூர் சட்டப்படி *எங்கேயோ* report ஆகணும் — ஆனா அந்த "எங்கேயோ" என்பது trade-ஐ (அல்லது counterparty-ஐ) தொடும் jurisdiction-ஐ பொறுத்து வேற regulator, வேற rulebook, பல நேரம் வேற Trade Repository. இந்த section அந்த regulators யாருன்னு region-by-region map.

### 9a. United States

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **CFTC** | Commodity Futures Trading Commission | OTC derivatives (swaps)-க்கான US முதன்மை regulator — Dodd-Frank-ன் கீழ swap data reporting, clearing, SEF trading விதிகளை அமைக்கும். |
| **SEC** | Securities and Exchange Commission | Securities markets (equities, bonds, exchanges)-க்கும், Reg SBSR-ன் கீழ *security-based* swaps-க்கும் (single-name equity/credit swaps) US regulator. |
| **Dodd-Frank Act** | Dodd-Frank Wall Street Reform and Consumer Protection Act | 2008 நிதி நெருக்கடிக்கு பதிலா OTC derivative clearing, reporting, SEF trading-ஐ கட்டாயமாக்கிய 2010 US சட்டம் — EU-வில EMIR செய்யறதுக்கு US-ன் இணை. |
| **Reg SBSR** | Regulation Security-Based Swap Reporting | Security-based swaps-க்கு SEC-ஓட reporting rulebook (மற்ற swaps-க்கான CFTC rules-ல இருந்து வேறு) — பெரும்பாலான பிற jurisdictions-ல இல்லாத US-specific பிரிவு. |
| **SDR** | Swap Data Repository | Trade Repository-க்கு US-ல பயன்படும் சொல் — CFTC-regulated swap data report ஆக வேண்டிய இடம். |
| **FINRA** | Financial Industry Regulatory Authority | Broker-dealers-ஐ மேற்பார்வையிடும் US self-regulatory அமைப்பு. |
| **TRACE** | Trade Reporting and Compliance Engine | Corporate/agency bonds-க்கான FINRA-வின் post-trade விலை வெளிப்படைத்தன்மை system — cash-bond reporting regime, derivatives reporting-ல இருந்து தனி. |
| **CAT** | Consolidated Audit Trail | சந்தை கண்காணிப்புக்காக, எல்லா US equity, options markets-ஓட order, trade lifecycle data-வையும் capture பண்ண SEC கட்டாயமாக்கிய system. |

### 9b. United Kingdom

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **FCA** | Financial Conduct Authority | UK-ன் முதன்மை markets/conduct regulator — trade reporting, market conduct, பெரும்பாலான firms-ஓட அன்றாட compliance-ஐ மேற்பார்வையிடும். |
| **PRA** | Prudential Regulation Authority | Banks, பெரிய investment firms-ஓட prudential (capital/solvency) மேற்பார்வைக்கு பொறுப்பான Bank of England பிரிவு — UK-ன் Basel-implementer. |
| **BoE** | Bank of England | UK-ன் மத்திய வங்கி; PRA-வோட சேர்ந்து CCPs, systemic நிதி infrastructure-ஐ மேற்பார்வையிடும். |
| **UK EMIR** | UK European Market Infrastructure Regulation | Brexit-க்கு பிறகு (1 Jan 2021) UK சட்டத்துக்குள் "onshore" ஆன EU EMIR — அதே core reporting/clearing கடமைகள், இப்போ ESMA இல்லாம FCA/BoE மேற்பார்வையில். |
| **UK MiFIR** | UK Markets in Financial Instruments Regulation | EU MiFIR-ன் onshored UK பதிப்பு — Brexit-க்கு பிறகு UK transaction reporting, market transparency-ஐ நிர்வகிக்கும். |

### 9c. European Union

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **EMIR** | European Market Infrastructure Regulation | OTC derivative trades Trade Repositories-க்கு report ஆகணும், standardized products centrally clear ஆகணும்னு கோரும் EU regulation. |
| **EMIR REFIT** | EMIR Regulatory Fitness and Performance Programme | EMIR reporting fields-ஐ ISO 20022 XML message format-க்கு standardize பண்ணின 2024 சீரமைப்பு — derivatives reporting pipelines-க்கு பெரிய IT migration நிகழ்வு. [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §8. |
| **ISO 20022** | (ISO messaging standard; initialism இல்ல) | EMIR REFIT (மற்றும் பெருகிவரும் பிற regimes) reports format செய்யப்படும் structured XML financial-messaging standard; பழைய, குறைந்த structured formats-ஐ மாற்றுது. [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §8. |
| **MiFID II / MiFIR** | Markets in Financial Instruments Directive / Regulation | பெரும்பாலான financial instruments முழுக்க market structure, transparency, transaction reporting-க்கான EU-ன் core rulebook. |
| **RTS 22** | Regulatory Technical Standard 22 | Transaction Reporting field content (~65 fields)-ஐ நிர்வகிக்கும் MiFIR technical standard — EMIR-ஓட சொந்த field set-ல இருந்து வேறு; destination-ம் குறுகியது. [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §4. |
| **ESMA** | European Securities and Markets Authority | EMIR/MiFIR-ன் கீழ technical standards எழுதி, கீழே வரும் தேசிய regulators (NCAs)-ஐ ஒருங்கிணைக்கும் EU-மட்ட அமைப்பு. |
| **NCA** | National Competent Authority | ஒவ்வொரு EU member state-ஓட சொந்த regulator-க்கான பொதுச் சொல் (எ.கா. ஜெர்மனி BaFin, பிரான்ஸ் AMF) — அன்றாட மேற்பார்வை NCA மட்டத்துல; ESMA பொது rulebook-ஐ அமைக்கும். |
| **SFTR** | Securities Financing Transactions Regulation | Repo, securities lending, மற்ற securities financing transactions-ஐ report பண்ண கோரும் EU regime — EMIR-ஓட சகோதர regime, வேறு product set-ஐ கவர் பண்ணும். |
| **TR** | Trade Repository | OTC derivative trades report ஆக வேண்டிய மத்திய registry-க்கான EU/global பொதுச் சொல் ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §5) — US "SDR"-ன் EU இணை. |

### 9d. Canada

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **CSA** *(regulatory)* | Canadian Securities Administrators | Canada-வின் மாகாண/பிரதேச securities regulators (OSC, AMF, BCSC…) சேர்ந்த குடை அமைப்பு; OTC derivatives trade-reporting rules உட்பட இணக்கமான rules-ஐ சேர்ந்து எழுதும். **Credit Support Annex (Section 7) CSA-வோட ஒண்ணு இல்ல** — abbreviation மோதுது, அதனால எப்பவும் context-ல இருந்து படிங்க. |
| **OSC** | Ontario Securities Commission | Canada-வின் மிகப்பெரிய மாகாண securities regulator (Canada-ல ஒரே தேசிய SEC-equivalent இல்ல — regulation மாகாண அளவில், மேலே சொன்ன CSA வழியா ஒருங்கிணைப்பு). |
| **CIRO** | Canadian Investment Regulatory Organization | Investment dealers, trading activity-க்கான Canada-வின் self-regulatory அமைப்பு; 2023-ல IIROC, MFDA இணைந்து உருவானது. |
| **TR Rules** | Trade Reporting Rules (எ.கா. Multilateral Instrument 96-101) | CSA-ஓட இணக்கமான OTC derivatives trade-reporting rules; designated Trade Repository-க்கு report பண்ண கோரும் — EMIR/Dodd-Frank reporting-ன் Canada இணை. |
| **OSFI** | Office of the Superintendent of Financial Institutions | Banks, insurers-க்கான Canada-வின் federal prudential regulator — Bank Of Toronto போன்ற Canadian banks-க்கு Basel-implementer. |
| **BoC** | Bank of Canada | Canada-வின் மத்திய வங்கி; CORRA-வை வெளியிடும் (Section 4), systemically important clearing/settlement systems-ஐ மேற்பார்வையிடும். |

### 9e. Hong Kong

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **SFC** | Securities and Futures Commission | Hong Kong-ன் முதன்மை securities, derivatives markets regulator. |
| **HKMA** | Hong Kong Monetary Authority | Hong Kong-ன் மத்திய வங்கி நிறுவனம் — OTC derivatives reporting regime-ஐ SFC-யோட (banks-க்கு HKMA, licensed corporations-க்கு SFC) சேர்ந்து நிர்வகிக்கும்; systemic infrastructure-ஐயும் மேற்பார்வையிடும். |
| **SFO** | Securities and Futures Ordinance | OTC derivatives reporting, clearing, licensing regime நிறுவப்பட்ட முதன்மை Hong Kong சட்டம். |
| **HKTR** | Hong Kong Trade Repository | Hong Kong-ல / வழியா book ஆன OTC derivative transactions report ஆக வேண்டிய, HKMA நடத்தும் trade repository. |

**Takeaway:** acronyms வேறா இருந்தாலும் இந்த pattern எல்லா இடத்துலயும் திரும்பத் திரும்ப வரும் — ஒவ்வொரு region-லயும் (1) markets/conduct regulator, (2) prudential / central-bank-சார்ந்த supervisor, (3) OTC derivative reporting-ஐ கட்டாயமாக்கும் ஒரு குறிப்பிட்ட சட்டம்/rulebook, (4) data உண்மையில போய் சேரும் ஒரு Trade Repository. ஒரு jurisdiction-க்கு இந்த நாலையும் சொல்ல முடிஞ்சா, இன்னொண்ணுல இணையானதை கண்டுபிடிக்கறது பெரும்பாலும் சொல்லகராதி பயிற்சி மட்டும்தான் — அடிப்படை reporting கடமை ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html)) எல்லா இடத்துலயும் கட்டமைப்பில ஒண்ணுதான்.

---

## 10. Organizational Shorthand

| Abbreviation | Full Form | விளக்கம் |
|---|---|---|
| **FO / MO / BO** | Front Office / Middle Office / Back Office | ஒரு trade கடக்கும் மூன்று-நிலை உள் pipeline — execution; risk / valuation / confirmation; பின்னர் settlement / reporting. [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §4. |
| **KYC** | Know Your Customer | ஒரு counterparty-உடன் trade பண்ணும் முன் அவங்க அடையாளத்தை சரிபார்க்கும் due-diligence செயல்முறை — LEI / reference-data setup-க்கு உணவளிக்கும். |
| **AML** | Anti-Money Laundering | நிதி அமைப்பு சட்டவிரோத பணத்தை வெளுக்க பயன்படாம தடுக்கும் regulatory controls. |

---

## கூடுதல் — "ஒரு வாக்கியத்தில்" நினைவு குறிப்புகள்

எளிதா நினைவில வைக்க சில ஜோடிகள்:

| குழப்பம் | எப்படி வேறுபடுத்தறது |
|---|---|
| **UTI vs UPI vs LEI** | UTI = *எந்த transaction*; UPI = *எந்த product வகை*; LEI = *எந்த legal entity (யார்)* |
| **IM vs VM** | IM = முன்கூட்டியே பாதுகாப்பு (எதிர்கால நஷ்டம்); VM = இன்றைய MTM மாற்றத்துக்கான தினசரி ஈடு |
| **CSA (Credit Support Annex) vs CSA (Canadian Securities Administrators)** | முதலாவது collateral ஒப்பந்தம்; இரண்டாவது Canadian regulators குழு — context தான் வழி |
| **CCP vs CSD** | CCP = trade-ஓட risk-ஐ ஏற்கும் clearing house; CSD = securities உரிமையை பதிவு செய்யும் ledger |
| **CORRA vs CDOR** | CORRA = உண்மையான overnight repo transactions-ல இருந்து (தற்போதையது); CDOR = banks-ஓட survey (நிறுத்தப்பட்டது, June 2024) |
| **EMIR vs SFTR vs MiFIR** | EMIR = OTC derivatives reporting; SFTR = repo/securities lending reporting; MiFIR = transaction reporting (பெரும்பாலான instruments) |
| **SDR vs TR vs HKTR** | ஒரே concept (trade data repository) — US, EU/global, Hong Kong-ஓட பெயர்கள் |

---

**இந்த glossary-ஐ எப்படி பயன்படுத்தறது:** ஒரு reporting field-லயோ உரையாடல்லயோ தெரியாத acronym வந்தா, முதல்ல இங்க பாருங்க — பெரும்பாலான entries, கருத்து வெறுமனே வரையறுக்கப்படாம எண்களோட கற்பிக்கப்படும் module section-க்கு திரும்ப link பண்ணுது.
