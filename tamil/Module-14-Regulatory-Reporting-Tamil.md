# Module 14 — Regulatory Reporting + IT View (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Cross-cutting module — roadmap-ஓட இறுதி module.** Modules 1–13, 15-ல உள்ள ஒவ்வொரு நூலையும் ஒரே annotate செய்த trade record-ஆ முடிக்குது; இதைத் தொடரும் curriculum-ஓட "Final End-to-End Case Study"-க்கு களம் அமைக்குது.
> **மூலம்:** English Module 14-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Regulatory Reporting-ன்னா என்ன? — ஒண்ணு இல்ல, ஐந்து கடமைகள்

**Definition:** "regulatory reporting" ஒரே எல்லாம்-உள்ளடக்கும் சொற்றொடரா பயன்படுத்தப்படுது, ஆனா இந்த roadmap உண்மையில **ஐந்து தனி reporting கடமைகளைத்** தொட்டிருக்கு; ஒவ்வொண்ணும் வேறு regulatory கேள்விக்கு பதில் சொல்லுது, வேறு இடங்களுக்குப் போகுது, வேறு நிகழ்வுகளால தூண்டப்படுது.

| கடமை | என்ன Report பண்ணப்படுது | எந்தக் கேள்விக்கு பதில் | முதல்ல பார்த்த இடம் |
|---|---|---|---|
| **Trade Reporting** (பொதுச் சொல்) | கீழே உள்ள எதையும் தளர்வா குறிக்க — தானே துல்லியமான regulatory வகை இல்ல | — | எல்லா இடத்திலும் |
| **Transaction Reporting** (MiFIR) | scope-ல உள்ள ஒவ்வொரு execution-உம் — equities, bonds, listed derivatives, OTC derivatives எல்லாம் | "சந்தை முறைகேடு நடந்ததா?" — கண்காணிப்பு-மையம் | [Module 4](../docs/1-capital-markets/module-04-equity.html) §8 |
| **Position Reporting** | நிற்கும் positions, அவ்வப்போது — தனி trades இல்ல | "ஒரு பங்கேற்பாளர் ஒரே product-ல அதிக risk-ஐ குவிக்கிறாரா?" | [Module 7](../docs/1-capital-markets/module-07-commodities.html) §8-ஓட position limits |
| **Derivatives Reporting** (EMIR/Dodd-Frank) | ஒரு Trade Repository-க்கு OTC derivative trade economics | "முழு சந்தை முழுக்க systemic OTC derivatives exposure என்ன?" | [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7 |
| **Securities Financing Reporting** (SFTR) | Repo, securities lending, மற்ற financing transactions | "அமைப்புல எவ்வளவு collateral re-use, financing leverage இருக்கு?" | [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §8 |

```text
WHY ONE TRADE CAN TRIGGER MULTIPLE, GENUINELY DIFFERENT REPORTS
========================================================================

  Bank Of Toronto's 5Y CAD IRS (Module 8 §4a) with Goldman Sachs:

  -> DERIVATIVES REPORTING (EMIR/Dodd-Frank): YES, reportable --
     it's an OTC derivative (Module 2 §07)

  -> TRANSACTION REPORTING (MiFIR): potentially YES if either
     party is EU/UK-regulated and the swap is deemed
     MiFID-in-scope (Module 6 §08's boundary discussion, applied
     to Rates instead of FX)

  -> POSITION REPORTING: only if Bank Of Toronto's cumulative
     position in a POSITION-LIMIT-SUBJECT product (Module 7 §08's
     commodity derivative limits) crosses a threshold -- NOT
     applicable to a plain CAD IRS

  -> SECURITIES FINANCING REPORTING (SFTR): NO -- an IRS is a
     derivative, not a financing transaction (Module 5 §08's
     distinction, restated)

  -> Same trade, evaluated against FOUR different reporting
     regimes, with genuinely different YES/NO/MAYBE answers for
     each -- exactly the kind of decision matrix a reporting
     system's rules engine has to encode correctly.
```

**Takeaway:** "இந்த trade reportable-ஆ?" ஒருபோதும் ஒற்றை yes/no கேள்வி இல்ல — அது ஐந்து தனி regimes-க்கு எதிரான ஐந்து தனி yes/no கேள்விகள்; ஒரு trade சட்டப்படி சிலவற்றுக்கு reportable-ஆவும் வேறு சிலவற்றுக்கு விலக்கு பெற்றதாவும் ஒரே நேரத்துல இருக்கலாம். [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §6 ஏற்கனவே *யார்* report பண்றாங்கங்கறதுக்கு இதே பன்மையைக் காட்டிச்சு; இந்த section *என்ன* report பண்ணப்படுதுங்கறதுக்கே காட்டுது.

> **Deep dive இருக்கு.** Position Reporting-உம் Derivatives/SFT Reporting-உம் உண்மையிலயே வேறு கடிகாரங்கள்ல ஓடுது — ஒண்ணு அவ்வப்போது, concentration-மையம்; மற்றவை ஒரு position திறந்திருக்கும் வரை **தினசரி** valuation-update கடமை. ஒவ்வொரு regime-உம் எவ்வளவு அடிக்கடி report பண்ணுதுங்கற முழு விவரம், EMIR, SFTR தினசரி-valuation worked examples-ஓட: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §11.

---

## 2. என்ன Data, ஏன் — Curriculum-ஓட சொந்தக் கேள்வியை முடிக்கிறது

**Definition:** curriculum-படி இந்த module-ஓட நோக்கம் **"trade-ல என்ன நடந்தது → எந்த data அதை விவரிக்குது → regulator-க்கு அந்த data ஏன் வேணும்?"** என்பதற்கு பதில் சொல்றது — Section 1 முதல் பாதிக்கு பதில் சொல்லிச்சு; இந்த section ரெண்டாவது பாதிக்கு.

| Data வகை | Example Fields | Regulator-க்கு ஏன் வேணும் |
|---|---|---|
| Economic Terms | Notional, Fixed Rate, Maturity ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) | அடிப்படை "இந்த trade என்ன" — இது இல்லாம வேற எதுவும் புரியாது |
| Product Classification | UPI, ISDA Taxonomy ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7, §12) | ஒவ்வொரு contract-ஐயும் படிக்காம, மில்லியன் கணக்கான trades-ஐ ஒப்பிடக்கூடிய categories-ஆ regulator பிரிக்க உதவுது |
| Counterparty Identity | LEI, [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §4-ஓட எல்லா பாத்திரங்கள்ல | யார் யாருக்கு exposed — systemic concentration பகுப்பாய்வுக்கான மூலப்பொருள் |
| Valuation | MTM, Fair Value Level ([Module 10](../docs/1-capital-markets/module-10-valuation.html)) | Report பண்ணப்பட்ட exposure உண்மையானதா, தற்போதையதா, இல்ல பழசா? |
| Exposure & Collateral | Current Exposure, Collateral Held ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-12) | Risk உண்மையில நிர்வகிக்கப்படுதா, இல்ல ஒரு firm under-collateralized-ஆ, ஒரு default தள்ளி இருக்கா? |
| Venue & Clearing | Exchange vs OTC, CCP vs bilateral (ஒவ்வொரு asset-class module-ஓட §7) | Risk மறைவான bilateral உறவுகள்ல குவிஞ்சிருக்கா, இல்ல regulate பண்ணப்பட்ட CCP-ஆல உறிஞ்சப்படுதா? |

**Takeaway:** இந்த table-ல ஒவ்வொரு field வகையும் இந்த roadmap ஏற்கனவே விரிவா கட்டின ஒரு section-க்கு map ஆகுது — regulatory reporting கடைசியில ஒட்டப்படும் புது கருத்துகளின் தொகுப்பு இல்ல; Modules 1–13, 15-ல உள்ள எல்லாத்தையும், ஐந்து வெவ்வேறு regulators ஒவ்வொருத்தரும் சுயாதீனமா தங்களுக்குத் தேவைன்னு முடிவு பண்ணின குறிப்பிட்ட வடிவத்துல மறுபொதி செய்தது.

---

## 3. EMIR Deep Dive — ஒரு OTC Derivative Report உண்மையில என்ன கொண்டிருக்கு

**Definition:** [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7 EMIR-ஓட Trade Repository கடமையை அறிமுகப்படுத்தியது; [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §6 dual-sided reporting-ஐ அறிமுகப்படுத்தியது. உண்மையில submit பண்ணப்படுறது என்ன, இன்னும் ஒரு உண்மையிலயே புது கருத்து: Trade Repository-லயே **pairing and reconciliation**.

```text
EMIR REPORT — FIELD GROUPS, USING BANK OF TORONTO'S 5Y CAD IRS
========================================================================

  Counterparty data: Bank Of Toronto's LEI, Goldman Sachs' LEI
                      (Module 4 §6, Module 13 §4)
  Common data:        UPI (product template), UTI (this specific
                      trade), Notional, Fixed Rate, Maturity
                      (Module 2 §12)
  Valuation data:      MTM, valuation timestamp, Fair Value Level
                      (Module 10 §6)
  Collateral data:     Collateral posted, CSA reference (Module 12)

  PAIRING & RECONCILIATION (new): because EMIR is dual-sided
  (Module 13 §6), BOTH Bank Of Toronto's and Goldman Sachs'
  independently-submitted reports for this SAME trade land at the
  Trade Repository. The Trade Repository automatically attempts to
  PAIR them (matching on UTI) and RECONCILE the economic fields --
  if Bank Of Toronto reports Notional CAD 100,000,000 and Goldman
  Sachs reports CAD 100,500,000 for the same UTI, that's a
  reconciliation BREAK, flagged back to both firms for correction.
```

**Takeaway:** EMIR-ஓட dual-sided வடிவமைப்பு வெறும் redundancy-க்காக இல்ல — Trade Repository-ஓட pairing/reconciliation process தானே ஒரு data-quality control; [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §7 பாணியிலான dispute-ஐ (ஒரே trade-க்கு ரெண்டு firms வேறு எண்களை கணக்கிடறது) bilateral collateral-call மட்டத்துல மட்டுமில்ல, regulatory மட்டத்துலயும் பிடிக்குது.

### கூடுதல் உதாரணம் — Pairing எப்படி வேலை செய்யுது? (எளிய தமிழ்ல)

ரெண்டு பேரும் ஒரே திருமண அழைப்பிதழ்ல ஒரே தேதியை எழுதியிருக்காங்களான்னு யாரோ சரிபார்க்கிற மாதிரி: UTI = அழைப்பிதழ் எண்; அந்த எண் ஒண்ணாத்தான் இருக்கணும், அப்புறம் உள்ள விவரங்கள் (தேதி, இடம்) ஒத்துப்போகணும்.

| Bank Of Toronto report | Goldman Sachs report | Trade Repository முடிவு |
|---|---|---|
| UTI ABC123, Notional 100,000,000 | UTI ABC123, Notional 100,000,000 | ✅ Paired & reconciled |
| UTI ABC123, Notional 100,000,000 | UTI ABC123, Notional 100,500,000 | ⚠️ Paired, ஆனா Notional field-ல reconciliation break |
| UTI ABC123, Notional 100,000,000 | UTI ABC124, Notional 100,000,000 | ❌ Pair ஆகவே இல்ல (unpaired) — UTI தவறு, இன்னும் கடுமையான பிரச்சனை |

---

## 4. MiFID II Transaction Reporting Deep Dive — வேறு Regime, வேறு நோக்கம்

**Definition:** MiFID II Transaction Reporting (அதன் technical standard RTS 22-ன் கீழ) அடிக்கடி EMIR-ஓட குழப்பிக்கொள்ளப்படுது, ஆனா ரெண்டு regimes-உம் முக்கியமான கிட்டத்தட்ட எல்லா பரிமாணங்கள்லயும் வேறுபடுது.

| பரிமாணம் | EMIR | MiFID II Transaction Reporting |
|---|---|---|
| Scope | OTC derivatives மட்டும் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) | scope-ல உள்ள எல்லா financial instruments — equities, bonds, listed மற்றும் OTC derivatives |
| சேரும் இடம் | Trade Repository ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07) | National Competent Authority நேரடியா (Trade Repository இடைநிலை இல்ல) |
| நோக்கம் | Systemic risk கண்காணிப்பு (Section 01) | சந்தை முறைகேடு கண்டறிதல் (insider dealing, market manipulation) |
| Report பண்ணும் பக்கங்கள் | Dual-sided ([Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §6) | பொதுவா single-sided, investment-firm-client சார்பா report பண்ணும் மாதிரி |
| Field எண்ணிக்கை (விளக்கம்) | EMIR REFIT-ன் கீழ ~85 fields | RTS 22-ன் கீழ ~65 fields |
| Deadline | T+1 | T+1 |

```text
THE SAME EQUITY TRADE, TWO DIFFERENT REGIMES, ZERO OVERLAP IN PURPOSE
========================================================================

  Maple Leaf Pension Fund buys Coca-Cola stock via Bank Of Toronto
  (Module 4 §4a):

  EMIR: NOT APPLICABLE -- Coca-Cola stock is a cash security, not
        an OTC derivative (Module 4 §08's exact scoping rule)

  MiFID II Transaction Reporting: APPLICABLE -- Bank Of Toronto
        reports the execution to its National Competent Authority,
        specifically so regulators can screen for suspicious
        trading patterns (e.g., trading ahead of undisclosed news)

  -> A cash equity trade that NEVER touches EMIR still generates
     a full MiFID II transaction report -- proof that "regulatory
     reporting" genuinely means different things depending on
     which regime is asking.
```

**Takeaway:** EMIR "சந்தையில எவ்வளவு systemic derivatives risk இருக்கு"ன்னு கேட்குது; MiFID II Transaction Reporting "இந்த குறிப்பிட்ட execution-ல யாராவது முறைகேடா நடந்தாங்களா"ன்னு கேட்குது — சில infrastructure கருத்துகளை (T+1 deadlines, LEIs) பகிர்ந்தாலும் முற்றிலும் வேறு regulatory நோக்கங்களுக்கு சேவை செய்யற ரெண்டு regimes; அதனாலதான் ஒரு cash equity trade-க்கு ஒண்ணு தேவை, இன்னொண்ணு தேவையில்ல.

---

## 5. SFTR Deep Dive — Repo Reporting படத்தை முடிக்கிறது

**Definition:** [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §8 முதல்ல SFTR-ஐ repo-வை உள்ளடக்குதுன்னு அறிமுகப்படுத்தியது; [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12 Bank Of Toronto-ஓட overnight repo worked example-ஐ நடத்திக் காட்டியது. அது இந்த module-ஓட பரந்த reporting கட்டமைப்போட எப்படி இணையுதுன்னு இதோ.

```text
SFTR REPORT — Bank Of Toronto's overnight repo (systems-landscape §12)
========================================================================

  Loan data:      EUR 50,000,000 cash lent, overnight, 3.15% rate
  Collateral data: Germany 3% Bund 2033, Market Value, Haircut
                   (Module 12 §3's exact haircut mechanic, applied
                   to THIS specific collateral)
  Counterparty:    Bank Of Toronto's LEI, Goldman Sachs' LEI

  -> Notice SFTR's report shape genuinely mirrors EMIR's (Section
     03) -- counterparty data, economic terms, collateral data --
     but answers a DIFFERENT systemic question (Section 01's
     "how much collateral re-use and financing leverage exists,"
     not "how much derivatives risk exists").
```

**Takeaway:** SFTR தான், reporting-field PATTERN (யார், என்ன, valuation, collateral) எந்த ஒரு தனி regime-ஐ விடவும் உலகளாவியதுங்கறதுக்கு இந்த roadmap-ஓட நிரூபணம் — அதே வடிவ data EMIR-ஓட derivatives-risk கேள்விக்கும் SFTR-ஓட financing-leverage கேள்விக்கும் சமமா நன்றா சேவை செய்யுது, ஏன்னா ரெண்டுமே அடிப்படையில "யார் யாருக்கு என்ன கடன்பட்டிருக்காங்க, எதன் ஆதரவோட"ன்னு கேட்குது.

---

## 6. Regime-க்கு Regime ஒப்பீடு — முழுப் படம்

| Regime | Covers | Destination | Core Question |
|---|---|---|---|
| **EMIR** (EU/UK) | OTC derivatives | Trade Repository | Systemic derivatives risk |
| **Dodd-Frank** (US) | OTC derivatives (swaps + security-based swaps, [Module 9](../docs/1-capital-markets/module-09-credit.md) §08's split) | Swap Data Repository | Systemic derivatives risk (US equivalent of EMIR) |
| **MiFID II / MiFIR** (EU/UK) | All in-scope instruments | National Competent Authority | Market abuse detection |
| **SFTR** (EU/UK) | Repo, securities lending | Trade Repository | Financing leverage / collateral re-use |
| **Position Reporting** (various) | Commodity derivatives, some others | Exchange / regulator | Concentration risk in one product |

**[Systems-landscape](../docs/1-capital-markets/systems-landscape.html) cross-reference:** ஒரு குறிப்பிட்ட trade எந்த regulator-க்கும் எந்த உள்ளூர் Trade Repository/NCA-க்கும் report பண்ணுதுங்கறது jurisdiction-ஐ பொறுத்தது — [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §09-10 ஏற்கனவே இதை நாடு நாடா (Canada, US, UK, EU, Hong Kong) முழுசா map பண்ணியிருக்கு, குறிப்பிட்ட உள்ளூர் regulator, repository பெயர்களோட; இந்த module அந்த mapping-ஐ வேணும்னே திரும்ப சொல்லல, ஏன்னா அது ஒரு கருத்து இல்ல, ஒரு lookup table.

**Takeaway:** ஐந்து regimes, ஐந்து இடங்கள், ஐந்து core கேள்விகள் — ஆனா இந்த table-ல ஒவ்வொரு regime-உம் கடைசில [Module 6](../docs/1-capital-markets/module-06-fx.html) §8, [Module 8](../docs/1-capital-markets/module-08-rates.html) §8, [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §01 எல்லாம் சுயாதீனமா சுட்டிக்காட்டின அதே 2008-க்கு அப்புறமான மூலக் காரணத்துக்குத் திரும்புது: நெருக்கடிக்கு முன்னாடி OTC, financing சந்தைகள்ல என்ன நடந்ததுங்கறதை regulators-ஆல போதுமான அளவு பார்க்க முடியலை; இங்க ஒவ்வொரு regime-உம் அதே பார்வை இடைவெளியின் ஏதோ ஒரு பதிப்பை மூடவே இருக்கு.

---

## 7. IT/Reporting பார்வை — Curriculum-ஓட சொந்த Capstone Record-ஐ Annotate பண்றது

**இந்த முழு roadmap-உம் நோக்கி கட்டிக்கிட்டிருந்த தருணம் இது.** Curriculum-ஓட சொந்த "Final End-to-End Case Study" (இந்த module-ஐத் தொடரும்) சரியா இந்த record-ஐ முன்வைக்குது:

```text
Trade ID: IRS12345
Asset Class: Derivative
Product: Interest Rate Swap
Notional: CAD 100M
Party A: Bank Of Toronto
Party B: Goldman Sachs
Maturity: 5Y
MTM: +CAD 2.4M
Exposure: CAD 2.0M
Collateral: CAD 1.8M
Custodian: XYZ
Settlement: ...
Reporting: ...
```

ஒவ்வொரு field-உம், இந்த roadmap அதை எங்க கட்டுச்சுங்கறதுக்கு எதிரா முழுசா annotate பண்ணப்பட்டது:

```text
Trade ID: IRS12345
  -> the human-readable trade identifier; the actual regulatory
     identifier reported under EMIR/Dodd-Frank is the UTI (Module
     2 §12), generated at execution, globally unique

Asset Class: Derivative / Product: Interest Rate Swap
  -> Module 1 §2's Asset Class -> Product hierarchy, refined by
     the ISDA Taxonomy (Module 2 §7) into the UPI that actually
     gets reported

Notional: CAD 100M
  -> Module 1 §8's foundational point: a REFERENCE amount for
     calculating cash flows, matching Module 8 §4a's exact IRS --
     this notional itself never settles

Party A: Bank Of Toronto / Party B: Goldman Sachs
  -> Module 13 §1's Counterparty terminology (not Buyer/Seller --
     a swap has no natural buy/sell direction), each identified
     by their own LEI (Module 4 §6, Module 13 §4) on the actual
     report, not just a name

Maturity: 5Y
  -> static economic term, Module 1 §8

MTM: +CAD 2.4M
  -> Module 10 §1's Mark-to-Market concept -- and specifically,
     this figure matches Module 11 §3's GROSS POSITIVE MTM across
     Bank Of Toronto's full netting set with Goldman Sachs (the
     IRS's own +1,500,000 plus the FX Forward's +900,000). This
     is a genuinely important real-world ambiguity: is "MTM" on
     this record describing Trade ID IRS12345 ALONE, or the whole
     NETTING SET? Module 11 §3's IT/reporting angle flagged
     exactly this -- a record needs an explicit Netting Set ID
     field to disambiguate, because in practice systems often DO
     show netting-set-level Exposure/Collateral fields attached to
     one representative Trade ID, exactly as this capstone record does

Exposure: CAD 2.0M
  -> Module 11 §3's NET Exposure after ISDA close-out netting --
     LOWER than MTM specifically because Trade 3 (the CDS, -CAD
     400,000) nets against the other two

Collateral: CAD 1.8M
  -> Module 12 §5's Required Collateral, driven by a CAD 200,000
     CSA Threshold -- NOT collateral posted "at random," a
     specific negotiated number

Custodian: XYZ
  -> Module 13 §2's Custodian role -- specifically Bank Of
     Toronto's OWN custodian, holding Bank Of Toronto's side of
     posted/received collateral (this record is Bank Of Toronto's
     own trade capture, so every field on it is Bank Of Toronto's
     view). Goldman Sachs would separately have its own custodian
     on its own books, not shown here. Logically distinct from the
     CCP (if this were centrally cleared) or the CSD (Module 13
     §2's full role table)

Settlement: ...
  -> Module 1 §7's DvP mechanics for the quarterly net cash flows
     (Module 8 §4a), or Module 3's full lifecycle if referring to
     the trade's own confirmation/clearing process

Reporting: ...
  -> Section 03 of THIS module -- an EMIR (or Dodd-Frank) report,
     dual-sided (Module 13 §6), reconciled against Goldman Sachs'
     own independently-submitted report at the Trade Repository
```

**Takeaway:** இந்த record-ல ஒவ்வொரு field-உம் — curriculum முதல் பக்கத்துல இருந்தே புரிய வைக்க நினைச்ச அதே record — இப்போ ஒரு குறிப்பிட்ட module, section, worked-number மூலத்தை பின்னாடி கொண்டிருக்கு. இந்த record-ல எதுவும் தன்னிச்சையானது இல்ல, தனிப்பட்ட உண்மையா மனப்பாடம் செய்ய வேண்டியதும் இல்ல; ஒவ்வொரு field-உம், [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) தொடங்கி [Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html) வரை உண்மையான எண்களோட இந்த roadmap கட்டின கருத்தின் உறுதியான விளைவு.

---

## 8. Regulatory Perspective — Reporting Accuracy, ISO 20022 & Enforcement

| Regulation / Body | Regulatory Reporting-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **EMIR REFIT** (2024 update) | EMIR-ஓட reporting fields-ஐ **ISO 20022 XML** message வடிவத்துக்கு தரப்படுத்தும் ஒரு குறிப்பிடத்தக்க மறுசீரமைப்பு (பழைய, குறைவான கட்டமைக்கப்பட்ட வடிவங்களை மாத்துது) — ஒவ்வொரு EU/UK derivatives desk-ஓட reporting pipeline-க்கும் ஒரு உண்மையிலயே பெரிய IT migration நிகழ்வு; [Module 8](../docs/1-capital-markets/module-08-rates.html) §1-ஓட LIBOR-to-RFR மாற்றத்தின் அளவுக்கு ஒத்தது |
| **National Competent Authorities** (அமலாக்கம்) | Regulators, தரவு விடுபடுறதுக்கு மட்டுமில்ல, reporting FAILURES-க்கே firms-க்கு தீவிரமா அபராதம் விதிக்கறாங்க — தாமதமான reports, reconcile ஆகாத breaks (Section 03), தவறான field நிரப்பல் எல்லாம் EMIR, Dodd-Frank, MiFID II regimes முழுக்க உண்மையான, கணிசமான regulatory அபராதங்களை ஈர்த்திருக்கு |
| **Firms-ஓட சொந்த control functions** | பெரும்பாலான பெரிய வங்கிகள் ஒரு அர்ப்பணிப்பு **Regulatory Reporting Control** செயல்பாட்டை நடத்துது ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §7-ஓட Product Control/IPV செயல்பாட்டுக்கு அருகிலானது, ஆனா தனியானது) — reportable trades உண்மையில, சரியா, நேரத்துக்கு report பண்ணப்பட்டனவான்னு சரிபார்ப்பதே அதன் முழு வேலை; reporting தானே இறுதித் தலைப்பாகும் வரை இந்த roadmap-க்கு தேவைப்படாத control layer |
| **IOSCO / CPMI** (ஏற்கனவே நிறுவப்பட்டது, [Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §8) | Section 06-ல உள்ள ஒவ்வொரு regime-உம் இறுதியில உள்ளூரில் அமல்படுத்தும் உலகளாவிய கொள்கைகளை தொடர்ந்து வகுக்குது — [Module 10](../docs/1-capital-markets/module-10-valuation.html)-13 முழுக்க இருந்த அதே standard-setters இங்கயும் வட்டத்தை முடிக்கறாங்க |

**Takeaway:** regulatory reporting ஒரு "submit பண்ணி மறந்துடு" process இல்ல — அது தன் சொந்த அர்ப்பணிப்பு செயல்பாடு, தன் சொந்த data-format migrations (ISO 20022), தன் சொந்த அமலாக்க risk-ஓட தொடர் control ஒழுக்கம்; [Module 10](../docs/1-capital-markets/module-10-valuation.html) §7 valuation-க்கு அறிமுகப்படுத்திய Independent Price Verification control-ஐ அப்படியே பிரதிபலிக்குது. முதல் தடவையே report-ஐ சரியா பெறுவது, அப்புறம் ஒரு reconciliation break-ஐ அல்லது regulatory அபராதத்தை சரிசெய்யறதை விட, எல்லா விதத்துலயும் மலிவு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Capstone IRS12345 record-ல Party A; dual-sided EMIR reporter |
| Goldman Sachs | Party B; தன் சொந்த பொருந்தும் (அல்லது பொருந்தாத) EMIR report-ஐ சுயாதீனமா submit பண்ணும் |
| Trade Repository | ரெண்டு counterparties-ஓட EMIR reports-ஐயும் pair செய்து reconcile பண்ணும் (Section 03) |
| National Competent Authority | MiFID II Transaction Reports-ஓட நேரடி பெறுநர் (Section 04) |
| IOSCO / CPMI | Section 06-ல உள்ள ஒவ்வொரு regime-ஐயும் தாங்கும் உலகளாவிய standard-setters |

---

## Recap Quiz — Numbers Edition

1.  Section 01-ஐ பயன்படுத்தி, Bank Of Toronto-ஓட Coca-Cola stock trade ([Module 4](../docs/1-capital-markets/module-04-equity.html) §4a) SFTR-ன் கீழ reportable-ஆ? EMIR-ன் கீழ? MiFID II Transaction Reporting-ன் கீழ? ஒவ்வொண்ணுக்கும் ஒரு வாக்கிய காரணத்தோட yes/no சொல்லுங்க.
2.  Section 03-ஓட EMIR pairing உதாரணத்துல, Bank Of Toronto UTI "ABC123"-ஐ report பண்ணி, Goldman Sachs ரெண்டு firms-உம் ஒரே trade-ன்னு நம்பறதுக்கு UTI "ABC124"-ஐ report பண்ணா, Trade Repository-ஓட pairing process பெரும்பாலும் எதை flag பண்ணும்?
3.  Section 04-ஓட ஒப்பீட்டு table-ஐ பயன்படுத்தி, ரெண்டு regimes-உம் "சந்தையில என்ன நடக்குதுன்னு அதிகம் பார்க்க" இருந்தாலும், MiFID II Transaction Reporting Coca-Cola trade-க்கு பொருந்தி EMIR பொருந்தாதது ஏன்?
4.  Section 05-ஐ பயன்படுத்தி, முற்றிலும் வேறு products-ஐ உள்ளடக்கினாலும், SFTR-ஓட report structure-க்கும் EMIR-ஓட report structure-க்கும் உண்மையிலயே பொதுவான ஒரே விஷயம் என்ன?
5.  Section 07-ஓட capstone record-ல, CAD 2.4M MTM எண் Trade ID IRS12345-ஐ மட்டும் இல்ல, NETTING SET-ஐ விவரிக்கலாம்னு module ஏன் சொல்லுது — அந்த ambiguity-ஐ தீர்க்க record-ல சேர்க்க வேண்டிய குறிப்பிட்ட field எது?
6.  Section 07-ஐ பயன்படுத்தி, "Collateral: CAD 1.8M" என்பது "Bank Of Toronto-வும் Goldman Sachs-உம் post பண்ண விரும்பியது" மட்டும் இல்லைன்னு விளக்கும் ஒற்றை module-ஓட உள்ளடக்கம் எது?
7.  Section 08-ஐ பயன்படுத்தி, ஒரு firm-ஓட ஒவ்வொரு trade-உம் இறுதியில சரியா report பண்ணப்பட்டிருந்தாலும் கூட, அந்த firm regulatory அபராதத்தை சந்திக்க ஒரு காரணத்தைப் பெயரிடுங்க.

வாழ்த்துகள் — இது Asset Class Roadmap (Modules 4-9), அதன் cross-cutting துணை ([Module 15](../docs/1-capital-markets/module-15-listed-derivatives.html)), ஐந்து cross-cutting தலைப்புகள் எல்லாத்தையும் (Modules 10-14) முடிக்குது. எல்லாத்தையும் IRS12345 record-க்கு எதிரா, இன்னொரு தடவை, எந்த உதவியும் இல்லாம சோதிக்க curriculum-ஓட சொந்த **Final End-to-End Case Study**-க்குத் தொடருங்க.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. SFTR: இல்ல — ஒரு cash equity வாங்குதல் ஒரு securities financing transaction இல்ல (repo/lending கட்டமைப்பு இல்ல, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08). EMIR: இல்ல — Coca-Cola stock ஒரு cash security, OTC derivative இல்ல ([Module 4](../docs/1-capital-markets/module-04-equity.html) §08). MiFID II Transaction Reporting: ஆமா — இது scope-ல உள்ள ஒரு financial instrument execution; asset class எதுவா இருந்தாலும் சந்தை முறைகேடு கண்காணிப்புக்கு reportable.**

Section 01-ஓட decision-matrix சட்டகம் நேரடியா பொருந்துது: ஒவ்வொரு regime-ஓட பொருந்துத்தன்மையும் சுயாதீனமா சரிபார்க்கப்படணும்; ஒரு trade ஒரே நேரத்துல ரெண்டு regimes-க்கு scope-க்கு வெளியே இருந்து மூணாவதுக்கு முழுசா scope-க்குள்ள இருக்கலாம் — சரியா Section 04-ஓட worked example இதே Coca-Cola trade-க்கு வெளிப்படையா நிரூபிக்கறது.

**2. ஒரு pairing/reconciliation BREAK — Trade Repository ரெண்டு reports-ஐயும் ஒண்ணோடொண்ணு match பண்ணவே முடியாது (UTIs பொருந்தாததால்); இது ஒரு பொருளாதார-field பொருந்தாமையை (Section 03-ஓட notional உதாரணம் போல) விட உண்மையில இன்னும் கடுமையான flag, ஏன்னா ரெண்டு firms-உம் ஒரே trade-ஐ விவரிக்குதான்னே repository-யால உறுதிப்படுத்த முடியாது.**

Section 03, pairing mechanism குறிப்பா "UTI-ல" match பண்ணுதுன்னு சொல்லுது — UTI பொருந்தாமை என்பது அடிப்படை இணைப்பு key-யே தவறு; ஒருவேளை ஒரு firm, UTI-generating counterparty உண்மையில உருவாக்கியதை ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12-ஓட விதிகள்படி) பயன்படுத்தாம, தன் சொந்த UTI-ஐ உருவாக்கியிருக்கலாம் — ஒரு எளிய field முரண்பாட்டை விட அடிப்படையான data பிரச்சனை.

**3. ஏன்னா ரெண்டு regimes-க்கும் வடிவமைப்பிலேயே முற்றிலும் வேறு SCOPES: EMIR குறிப்பா OTC derivatives-க்கு ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07), அதனால ஒரு cash equity trade நோக்கம் எதுவா இருந்தாலும் கட்டமைப்பு ரீதியா அதுக்கு வெளியே; MiFID II Transaction Reporting scope-ல உள்ள எல்லா financial instruments-க்கும், ஏன்னா சந்தை முறைகேடு derivatives-ல மட்டுமில்ல, எந்த tradable instrument-லயும் நடக்கலாம்.**

Section 04-ஓட worked example இதை துல்லியமா சொல்லுது — ரெண்டு regimes-உம் இறுதியில "சந்தையை அதிகம் பார்க்க"ன்னு சேவை செய்யுது, ஆனா EMIR-ஓட product scope (derivatives மட்டும்), MiFID II-ஓட மிகப் பரந்த instrument scope ரெண்டும் சுயாதீனமா வரையறுக்கப்பட்டவை, அவற்றை ஒரே மாதிரி பொருத்தும் ஒரு பகிரப்பட்ட அடிப்படைக் கொள்கையிலிருந்து derive ஆனவை இல்ல.

**4. ரெண்டு report structures-உம் ஒரே அடிப்படை FIELD PATTERN-ஐ பகிர்கின்றன — counterparty data (யார்), economic/loan terms (என்ன), collateral data (எதன் ஆதரவோட) — ஏன்னா ரெண்டு regimes-உம் அடிப்படையில ஒரே systemic கேள்வியின் ஒரு பதிப்பைக் கேட்குது: யார் யாருக்கு என்ன கடன்பட்டிருக்காங்க, அந்தக் கடமை எவ்வளவு நல்லா collateralize ஆகியிருக்கு.**

Section 05-ஓட takeaway இதை நேரடியா சொல்லுது: "reporting-field PATTERN... எந்த ஒரு தனி regime-ஐ விடவும் உலகளாவியது" — SFTR-உம் EMIR-உம் முற்றிலும் வேறு products-ஐ (financing transactions vs. derivatives) உள்ளடக்குது, ஆனா கிட்டத்தட்ட ஒத்த report வடிவங்களுக்கு ஒன்றிணையுது, ஏன்னா அடிப்படை regulatory கவலை (systemic exposure, என்ன collateral-ஆல ஆதரிக்கப்பட்டது) எப்படிப் பார்த்தாலும் கட்டமைப்பு ரீதியா அதே கேள்வி.

**5. ஏன்னா [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §3 ஏற்கனவே Exposure-உம் Collateral-உம் NETTING SET மட்டத்துல (ஒரே ISDA Master Agreement-ஓட கீழ உள்ள எல்லா trades) கணக்கிடப்பட்டு சட்டப்படி அர்த்தமுள்ளவைன்னு நிறுவியது, தனி trade மட்டத்துல இல்ல — "Trade ID"-ல இருந்து வேறுபட்ட ஒரு "Netting Set ID" field, MTM/Exposure/Collateral வெறும் IRS12345-ஐயா, இல்ல அது சேர்ந்த முழு netting set-ஐயான்னு ஒரு system-க்கும் (ஒரு வாசகருக்கும்) உறுதியா தெரியப்படுத்தும்.**

Section 07 இதை வெளிப்படையா சொல்லுது, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §3-ஓட IT/reporting பார்வை callout-ஐ நேரடியா மேற்கோள் காட்டி: exposure/collateral fields, ஒரு தனிப் பிரதிநிதி Trade ID-க்கு பக்கத்துல காட்டப்பட்டாலும், உண்மையான systems-ல netting-set-நிலை aggregates-உடன் அடிக்கடி இணைக்கப்படுது — ஒரு அர்ப்பணிப்பு Netting Set ID field தீர்க்கும் ambiguity சரியா அதுதான்.

**6. [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) (Collateral & Margin) — குறிப்பா Section 05-ஓட Threshold-இயக்கப்படும் Required Collateral கணக்கீடு; CAD 1.8M என்பது CAD 2,000,000 Net Exposure மேல CAD 200,000 CSA Threshold பொருத்தப்பட்டதன், வேண்டுமென்றே பேசி முடிவு செய்யப்பட்ட விளைவுன்னு அது காட்டியது, தன்னிச்சையான அல்லது ad-hoc எண் இல்ல.**

Section 07-ஓட annotation இதை நேரடியா சொல்லுது, "[Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §5-ஓட Required Collateral, CAD 200,000 CSA Threshold-ஆல இயக்கப்படுது"ன்னு பின்னாடி சுட்டிக்காட்டி — CAD 1.8M எண் Net Exposure-ல இருந்தும் Threshold-ல இருந்தும் முழுசா மறுகட்டமைக்கக்கூடியது; ரெண்டும் சட்டப்படி ஆவணப்படுத்தப்பட்ட CSA terms, ஒரு தரப்பு விருப்பப்படி ஒருதலைபட்சமா மாற்றக்கூடிய எண் இல்ல.

**7. Reporting FAILURES தரவு விடுபடுறதைப் பத்தி மட்டுமில்ல — தாமதமான reporting (T+1 deadline-ஐ தவறவிடுவது, Section 04-ஓட table), அல்லது ஆரம்பத்துல தவறா இருந்து ஒரு reconciliation break flag பண்ணப்பட்டப்புறம் மட்டுமே சரிசெய்யப்பட்ட reports (Section 03) ரெண்டுமே, trade-ஓட data பின்னாடி யாராவது சரிபார்க்கும் நேரத்துல முழுசா துல்லியமா இருந்தாலும், regulatory அபராதத்தை ஈர்க்கலாம்.**

Section 08 இதை நேரடியா சொல்லுது: regulators "தரவு விடுபடுறதுக்கு மட்டுமில்ல, reporting FAILURES-க்கே firms-க்கு அபராதம் விதிக்கறாங்க — தாமதமான reports, reconcile ஆகாத breaks, தவறான field நிரப்பல் எல்லாம் உண்மையான, கணிசமான regulatory அபராதங்களை ஈர்த்திருக்கு" — சரியான நேரமும் முதல்-தடவை துல்லியமும் சுயாதீனமா அமல்படுத்தக்கூடிய கடமைகள், இறுதியில சரியாகும் என்ற நல்ல-இருக்கற-விஷயம் மட்டுமில்ல.
