# Module 13 — Parties, LEIs & Market Infrastructure (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Cross-cutting module** — இதுவரை உள்ள ஒவ்வொரு module-க்கும் "யார் யார்" துணை. எட்டு-பாக asset-class pattern-ஐ சுத்தி அமைக்கப்படலை; அதுக்குப் பதிலா இந்த roadmap ஏற்கனவே பயன்படுத்தி வந்த ஒவ்வொரு actor பாத்திரத்தையும் பெயரிட்டு map பண்ணுது.
> **மூலம்:** English Module 13-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Buyer, Seller, Counterparty, Broker, Dealer — பாத்திரங்கள், துல்லியமா

**Definition:** இந்த ஐந்து சொற்களும் பேச்சுல தளர்வா பயன்படுத்தப்படுது, ஆனா ஒரு trade record-ல துல்லியமா வேறு வேறு அர்த்தம். [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §3 ஏற்கனவே Dealer (Principal) vs Broker (Agent)-ஐ ஒரு முழு worked example-ஓட நிறுவியது; இந்த section முழு பாத்திரத் தொகுப்பையும் ஒரே இடத்துல மறுபடி சொல்லுது.

| Role | அர்த்தம் |
|---|---|
| Buyer | ஒரு குறிப்பிட்ட trade-ல instrument/exposure-ஐ பெறும் தரப்பு — திசை-சார்ந்தது, எதிர் trade-ல புரளும் |
| Seller | Instrument/exposure-ஐ விடுக்கும் தரப்பு — Buyer-ஓட mirror |
| Counterparty | திசையைப் பொருட்படுத்தாம, "trade-ஓட மறுபக்கம்"ங்கற பொதுச் சொல் — ஒவ்வொரு Buyer-ஓட Counterparty-உம் ஒரு Seller, நேர்மாறும் |
| Broker (Agent) | ஒரு client **சார்பா** trade-ஐ execute பண்ணும், position-ஐ தன் சொந்த book-ல ஒருபோதும் எடுக்காது — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §3b-ஓட சரியான mechanic |
| Dealer (Principal) | Position-ஐ **தன் சொந்த book-ல** எடுக்கும், சிறிது நேரமே ஆனாலும் — வெறுமனே ஒரு match-ஐ எளிதாக்காம, client-ஓட உண்மையான Counterparty ஆகுது — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §3a |

```text
WHY "COUNTERPARTY" IS THE WORD THAT SURVIVES EVERY CONTEXT
========================================================================

  Coca-Cola trade (Module 4 §4a):
    Maple Leaf Pension Fund is the BUYER.
    Goldman Sachs is the SELLER.
    -> From Maple Leaf Pension Fund's perspective, Goldman Sachs
       is simply "the Counterparty" -- the label that doesn't
       depend on which direction the trade ran.

  5Y CAD IRS (Module 8 §4a):
    Bank Of Toronto pays fixed, Goldman Sachs pays floating --
    there's no "buyer" or "seller" of a swap in the equity sense
    at all, yet BOTH parties are unambiguously each other's
    COUNTERPARTY. This is exactly why Module 11's entire risk
    framework (Current Exposure, Net Exposure, CVA) is built
    around "Counterparty," never "Buyer" or "Seller" -- those
    labels don't even apply to every product in this roadmap.
```

**Takeaway:** Buyer/Seller என்பது Cash-instrument வடிவ labels ஜோடி; இயற்கையான buy/sell திசை இல்லாத swaps போன்ற products-க்கு அது உடைஞ்சுடும். Counterparty தான் இந்த roadmap-ல ஒவ்வொரு module-லயும் ஒவ்வொரு உறவையும் விவரிக்கும் அளவு பொதுவான ஒரே சொல் — அதனாலதான் [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட risk சொல்லகராதி அதையே தரமாக்கியது.

---

## 2. Market Infrastructure பாத்திரங்கள் — CCP, Custodian, CSD & Settlement Agent

**Definition:** இந்த roadmap தொடர்ந்து பயன்படுத்தி வந்த நாலு பாத்திரங்கள், பெரும்பாலும் துல்லியமா வேறுபடுத்தாமலே. நடைமுறைல அவை அடிக்கடி ஒரே நிறுவனத்துல உட்காரும், ஆனா தர்க்க ரீதியா வேறு வேறு செயல்பாடுகள்.

| Role | உண்மையில என்ன செய்யுது | ஏற்கனவே பார்த்த இடம் |
|---|---|---|
| **CCP** (Central Counterparty) | Trade-க்கு அப்புறம் ஒவ்வொரு seller-க்கும் buyer-ஆ, ஒவ்வொரு buyer-க்கும் seller-ஆ ஆகுது (novation), counterparty risk-ஐ உறிஞ்சுது ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §5) | NSCC, LCH SwapClear, ICE Clear Credit, CME Clearing, OCC |
| **CSD** (Central Securities Depository) | ஒரு security-ஓட *legal ownership*-ஐ பதிவு பண்ற ஒற்றை மைய ledger — DvP settlement இங்கதான் நடக்குது ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7) | DTC, CDS/CDSX ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7, [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §04) |
| **Custodian** | ஒரு institutional owner *சார்பா* securities, cash-ஐ வெச்சிருக்கும் ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7), [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §4-ப்படி post பண்ணின collateral-ஐயும் வெச்சிருக்கலாம் | Modules 4-12 முழுக்க குறிப்பிடப்பட்டது |
| **Settlement Agent** | உண்மையான DvP instruction-ஐ execute பண்ணும் — trade-ஓட settlement தேதில CSD வழியா securities-ஐயும் payment system வழியா cash-ஐயும் நகர்த்தும் | [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7-ஓட Fedwire உதாரணம் |
| **Tri-Party Agent** | தானியங்கி collateral தேர்வு, valuation, substitution-க்கான சிறப்பு custodian-போன்ற பாத்திரம் ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §4) | Euroclear, Clearstream |

```text
WHY THESE ROLES BLUR IN PRACTICE, BUT AREN'T THE SAME FUNCTION
========================================================================

  A single large custodian bank might simultaneously:
    - hold Maple Leaf Pension Fund's Coca-Cola shares as CUSTODIAN
    - act as SETTLEMENT AGENT, instructing DTC (the CSD) to move
      those shares on T+1
    - separately act as TRI-PARTY AGENT for a totally different
      client's derivatives collateral

  -> One institution, three DIFFERENT ROLES on three different
     relationships. A reporting system that has a single
     "Custodian" field covering all three blurs together data that
     actually serves three separate operational purposes.
```

**Takeaway:** இந்தப் பட்டியல்ல CSD தான் ஒரே *உண்மையான* மைய ledger — மற்ற எல்லாமே (Custodian, Settlement Agent, Tri-Party Agent) அந்த ஒற்றை ledger மேல உட்கார்ந்த, அல்லது அதுக்கு instructions-ஐ ஊட்டும் service layer. அதனாலதான் [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7 DvP-ஐ முழுக்க CSD-யின் அடிப்படையிலயே விவரிக்க முடிஞ்சது, ஒரு உண்மையான operational trade record-க்கு வேலையை உண்மையில யார் செய்யறாங்கன்னு காட்ட தனி Custodian, Settlement Agent fields தேவை.

---

## 3. Clearing Member — நேரடி Access vs Client Clearing

**Definition:** Centrally-cleared trade ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6) வேணும்னு விரும்பற ஒவ்வொரு firm-உம் ஒரு CCP-ஐ நேரடியா அணுக முடியாது. **Clearing Member** = ஒரு CCP-ஓட நேரடி member ஆன firm; மற்ற எல்லாரும் ஒரு Clearing Member வழியா, **client clearing**-ன்னு சொல்லப்படற உறவுல மறைமுகமா clearing-ஐ அணுகறாங்க.

```text
DIRECT CLEARING MEMBER  vs  CLIENT CLEARING
========================================================================

  Goldman Sachs: large enough to be a DIRECT Clearing Member of
    LCH SwapClear -- posts its own Initial Margin (Module 12 §2)
    directly to the CCP, faces the CCP's default waterfall
    directly (Module 11 §5)

  Maple Leaf Pension Fund: NOT a direct Clearing Member -- too
    small, doesn't meet LCH SwapClear's membership/capital
    requirements. To get a centrally-cleared IRS anyway, it uses
    Bank Of Toronto as its CLEARING MEMBER (a "client clearing"
    relationship):

    Maple Leaf Pension Fund  --trades with-->  Bank Of Toronto
                                                     |
                                        (submits trade to CCP,
                                         GUARANTEES performance)
                                                     v
                                            LCH SwapClear (CCP)

  -> Bank Of Toronto stands between Maple Leaf Pension Fund and
     the CCP -- if Maple Leaf Pension Fund fails, Bank Of Toronto
     (the Clearing Member) is on the hook to the CCP, not the
     other way around. This is a DIFFERENT counterparty risk
     relationship than the bilateral CVA math of Module 11 §4.
```

**IT/reporting பார்வை:** Client-cleared trade record-க்கு "Clearing Member"-ஐ "Counterparty"-ல இருந்து வேறுபடுத்தும் ஒரு field தேவை — Bank Of Toronto, Maple Leaf Pension Fund-ஓட Clearing Member, ஆனா பொருளாதார ரீதியா Maple Leaf Pension Fund-ஓட உண்மையான counterparty risk, novation-க்கு அப்புறம் CCP-க்கு ஓடுது; Bank Of Toronto ஒரு இடைநிலை guarantor, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட bilateral CVA கட்டமைப்பு கருதிய மாதிரி இறுதி risk-தாங்கி இல்ல.

**Takeaway:** Client clearing தான், ஒரு firm நேரடி member ஆகற அளவு பெருசா இல்லாமலே [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §5-ஓட CCP risk-mutualization நன்மைகள் எல்லாத்தையும் அணுக முடியும்ங்கறதுக்கு காரணம் — ஆனா அது CCP உறவை மாத்தாம, அதுக்குப் பக்கத்துல ஒரு புது, உண்மையிலயே தனி counterparty உறவை (client-to-Clearing-Member) அறிமுகப்படுத்துது.

---

## 4. LEI Deep Dive — ஒரு Trade, பல பாத்திரங்கள்

**Definition:** [Module 4](../docs/1-capital-markets/module-04-equity.html) §6 முதல்ல LEI-ஐ "எது" இல்ல, "யார்"ன்னு identify பண்றதா அறிமுகப்படுத்தியது. இந்த roadmap அதுக்கப்புறம் வெவ்வேறு modules முழுக்க LEI உண்மையிலயே வேறுபட்ட பாத்திரங்கள்ல நடிக்கிறதைக் காட்டியிருக்கு — Section 5-ஓட worked example அவை ஒவ்வொண்ணையும் ஒரே trade record-ல ஒரே நேரத்துல வைக்குது.

| LEI Role | எதை Identify பண்ணுது | முதல்ல பார்த்த இடம் |
|---|---|---|
| Buyer / Seller LEI | Trade-ஓட ரெண்டு பொருளாதார counterparties | [Module 4](../docs/1-capital-markets/module-04-equity.html) §6 |
| Counterparty LEI | Buyer/Seller பொருந்தாதப்போ பயன்படுத்தற, எந்த ஒரு பக்கத்துக்குமான பொது label (Section 01) | [Module 9](../docs/1-capital-markets/module-09-credit.html) §6 |
| **Reference Entity LEI** | ஒரு CDS-ஓட default risk-ஐ reference பண்ற company — trading party அல்லவே அல்ல | [Module 9](../docs/1-capital-markets/module-09-credit.html) §6 |
| **Clearing Member LEI** | ஒரு client-ஓட trade-ஐ CCP-க்கு submit பண்ற firm (Section 03) | இந்த module-க்கு புதுசு |
| **CCP LEI** | CCP தானே — ஆமா, CCPs-க்கும் தங்களோட சொந்த LEIs உண்டு, ஏன்னா அவையும் legal entities | இந்த module-க்கு புதுசு |
| **Reporting Entity LEI** | Regulatory trade report-ஐ submit பண்ற சட்டப் பொறுப்பு கொண்ட தரப்பு (Section 06) — எப்பவும் ரெண்டு trading parties-ல ஒண்ணாவே இருக்காது | இந்த module-க்கு புதுசு |
| Custodian LEI | ஒரு தரப்பு சார்பா securities/collateral-ஐ வெச்சிருக்கும் நிறுவனம் | [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §4 |

**Takeaway:** "யார்"ன்னு பதில் சொல்லும் LEI, இந்த roadmap அறிமுகப்படுத்தின ஒவ்வொரு பாத்திரத்தையும் பக்கம் பக்கமா வெச்சா, குறைந்தது ஏழு தனி உப-கேள்விகளை கொண்டிருக்குன்னு தெரியுது — "யார் trade பண்றாங்க", "யார் subject", "யார் clear பண்றாங்க", "யார் report பண்றாங்க", "யார் custody வெச்சிருக்காங்க" என்பவை ஐந்து உண்மையிலயே வேறு கேள்விகள்; அத்தனைக்கும் ஒரே 20-character identifier வடிவம்தான் பதில்.

---

## 5. Worked Example — ஒரு Trade-ல முழு Party Map

```text
5Y CAD IRS  —  CLIENT-CLEARED, FULL PARTY MAP
========================================================================

  Maple Leaf Pension Fund wants a 5Y CAD IRS, pays fixed 4.00%,
  Notional CAD 50,000,000 -- economically identical structure to
  Module 8 §4a's swap, different notional and buyer

  EXECUTION: Maple Leaf Pension Fund trades bilaterally against
    Goldman Sachs (dealer, Module 1 §3a)

  CLEARING: submitted to LCH SwapClear for novation (Module 3 §6)
    - Goldman Sachs: DIRECT Clearing Member, submits its own side
    - Maple Leaf Pension Fund: uses Bank Of Toronto as its
      CLEARING MEMBER (Section 03), which submits Maple Leaf's
      side and guarantees its performance to the CCP

  COLLATERAL: Maple Leaf Pension Fund's Initial Margin (Module 12
    §2) is held via a TRI-PARTY AGENT (Euroclear, Module 12 §4)

  ONE TRADE RECORD, SEVEN LEI FIELDS:
    Buyer LEI              : Maple Leaf Pension Fund
    Seller LEI              : Goldman Sachs
    Clearing Member LEI     : Bank Of Toronto (for Maple Leaf's side)
    CCP LEI                 : LCH SwapClear
    Reporting Entity LEI    : Bank Of Toronto (delegated reporting
                               on Maple Leaf's behalf -- Section 06)
    Custodian/Tri-Party LEI : Euroclear
    (Counterparty LEI field would duplicate Buyer/Seller here,
     since this product DOES have a clear buy/sell direction on
     the fixed-vs-floating economics -- Section 01's nuance)

  -> Seven distinct legal entities, seven distinct LEI roles, ONE
     trade. None of these seven entities appears twice in the
     same role, and none of these roles is optional to model
     correctly if this trade needs to be reconstructed later from
     its LEI fields alone.
```

**Takeaway:** இந்த ஒற்றை worked example, [Module 4](../docs/1-capital-markets/module-04-equity.html) §6-ல இருந்து இந்த roadmap கேட்ட ஒவ்வொரு "யார்" கேள்வியின் உறுதியான பலன் — ஒரு production trade record உண்மையிலயே இத்தனை தனித்தனி party-role fields தேவை; இவற்றில் ஏதாவது ரெண்டை ஒரு பொதுவான "Counterparty" field-ஆ ஒடுக்கறது, யார் உண்மையில என்ன செய்தாங்கன்னு பத்தின உண்மையான, சட்டப்படி அர்த்தமுள்ள தகவலை இழக்குது.

### கூடுதல் உதாரணம் — அதே trade, யாருக்கு என்ன கடமை? (எளிய தமிழ்ல)

| Entity | பாத்திரம் | இவங்க தோத்தா என்ன ஆகும்? |
|---|---|---|
| Maple Leaf Pension Fund | Buyer (fixed payer) | Bank Of Toronto (Clearing Member) CCP-க்கு அவங்க சார்பா பொறுப்பேற்கணும் |
| Goldman Sachs | Seller + நேரடி Clearing Member | CCP-ஓட default waterfall (அவங்க IM → Default Fund → ...) |
| Bank Of Toronto | Maple Leaf-ஓட Clearing Member + Reporting Entity | Maple Leaf default ஆனா CCP-க்கு guarantor-ஆ கட்டணும் |
| LCH SwapClear | CCP | அவங்களோட சொந்த capital + waterfall கடைசி தடுப்பு |
| Euroclear | Tri-party agent | Collateral-ஐ பாதுகாத்து வைத்திருக்கிறார்; உரிமையாளர் இல்ல |

ஒரே trade — ஆனா "பிரச்சனை வந்தா யார் கொடுக்கணும்"ங்கறது ஒவ்வொரு entity-க்கும் வேறு. இதனால தான் ஒவ்வொரு LEI பாத்திரத்தையும் தனி field-ஆ வைக்கணும்.

---

## 6. Reporting Entity vs Counterparty — உண்மையில யார் Report File பண்றது

**Definition:** [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7-ஓட OTC derivative reporting கடமை, ரெண்டு trading parties-உம் தனித்தனியா ஒரே மாதிரி reports-ஐ submit பண்ணணும்னு தானா அர்த்தப்படுத்தறதில்ல — வெவ்வேறு regimes Reporting Entity பாத்திரத்தை வெவ்வேறு விதமா ஒதுக்குது; அது அடிக்கடி ரெண்டு trading counterparties-ல ஒண்ணாவே இருக்கறதில்ல.

```text
DUAL-SIDED vs SINGLE-SIDED REPORTING
========================================================================

  EMIR (EU/UK) — DUAL-SIDED: BOTH counterparties must report their
    side of the trade to a Trade Repository, though a smaller
    counterparty can DELEGATE its reporting obligation to the
    larger one (exactly Section 05's Bank Of Toronto reporting on
    Maple Leaf Pension Fund's behalf)

  Dodd-Frank (US) — SINGLE-SIDED: only ONE party (determined by a
    defined hierarchy -- generally the more "regulated" or
    "swap dealer" party) reports the trade; the other party has no
    independent reporting obligation for that same trade at all

  -> The SAME bilateral trade between a US dealer and an EU buy-
     side client can be REPORTED ONCE under Dodd-Frank's rules but
     TWICE (or once, if delegated) under EMIR's rules for the
     EU-domiciled leg -- "Reporting Entity" isn't a fixed role,
     it's assigned differently by every regulatory regime a trade
     happens to touch.
```

**Takeaway:** Maple Leaf Pension Fund உண்மையான Buyer-ஆ இருக்கற trade-ல "Reporting Entity: Bank Of Toronto"ன்னு காட்டும் reporting field தவறில்ல — EMIR அனுமதிக்கறபடி Section 05-ஓட delegated-reporting ஏற்பாடு சரியா வேலை செய்யறது; [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) regulators உண்மையில என்ன பெறுறாங்கன்னு உள்ளடக்கும்போது இந்த வேறுபாட்டை நேரடியா எடுத்துக்கும்.

---

## 7. IT/Reporting பார்வை — Reference Data, Golden Source & LEI Lifecycle

**Definition:** இதுவரை விவாதித்த ஒவ்வொரு party/LEI field-உம் **reference data**, trade data இல்ல — அது எந்த ஒரு trade-ஐயும் சாராம இருக்கற ஒரு entity-ஐ விவரிக்குது; ஒவ்வொரு தடவையும் புதுசா உள்ளிடாம, trade capture நேரத்துல தேடிப் பிடிச்சு இணைக்கப்படுது.

```text
REFERENCE DATA vs TRADE DATA — the distinction that matters
========================================================================

  TRADE DATA (specific to THIS trade, changes every time):
    Notional, Price, Trade Date, Maturity Date

  REFERENCE DATA (describes an ENTITY, reused across every trade
  that entity is party to):
    Maple Leaf Pension Fund's LEI, registered address, credit
    rating -- the SAME values whether this is trade #1 or #10,000
    Bank Of Toronto references this year

  -> A "Golden Source" is the single authoritative system a firm
     designates to own each reference data entity -- every other
     system (trade capture, risk, collateral, reporting) is
     supposed to LOOK UP that entity's LEI from the Golden Source,
     never store its own independently-typed copy that can drift
     out of sync.
```

**LEI Lifecycle — ஒரு உண்மையிலயே operational நிஜம்:** LEI ஒரு தடவை வழங்கப்பட்டு மறக்கப்படறதில்ல — அதை வழங்கிய Local Operating Unit (LOU, [Module 2](../docs/1-capital-markets/module-02-hierarchy.html)-ஓட abbreviations glossary)-உடன் **ஆண்டுதோறும் புதுப்பிக்கப்படணும்**; புதுப்பிக்கப்படாத (lapsed) LEI, GLEIF global database-ல முழுசா செல்லாததா இல்லாம "LAPSED"ன்னு flag பண்ணப்படுது. Lapsed LEI உள்ள counterparty சட்டப்படி இன்னும் இருக்கலாம், சில jurisdictions-ல இன்னும் trade கூட பண்ணலாம்; ஆனா MiFID II-ஓட **"No LEI, No Trade"** கொள்கை (Section 08) என்றால், EU-regulated firm பொதுவா புதுப்பிக்கப்படற வரை LEI lapsed ஆன counterparty-க்கு எதிரா ஒரு புது reportable trade-ஐ execute பண்ண முடியாது — LEI lifecycle கண்காணிப்பை ஒரு உண்மையான, தொடர் operational control ஆக்குது, ஒரு தடவை onboarding checkbox இல்ல.

**Takeaway:** இந்த module பெயரிட்ட ஒவ்வொரு party பாத்திரமும் — Buyer, Clearing Member, Custodian, Reference Entity — கடைசில ஒரு Golden-Source-நிர்வகிக்கும் LEI record-க்கு resolve ஆகுது; அதன் சொந்த lifecycle (வழங்கப்பட்டது, புதுப்பிக்கப்பட்டது, lapsed ஆனது) trade-ஓட சொந்த economics-ஐப் பற்றி எதுவும் சாராம, அந்த entity-க்கு எதிரா ஒரு புது trade-ஐ சட்டப்படி execute பண்ணவே முடியுமா இல்லையாங்கறதை தனியா பாதிக்கலாம்.

---

## 8. Regulatory Perspective — GLEIF/LOUs, "No LEI, No Trade" & CPMI-IOSCO

| Regulation / Body | Parties, LEIs & Market Infrastructure-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **GLEIF** ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §8-ஓட abbreviations-ல ஏற்கனவே) | உலகளாவிய LEI அமைப்பை மேற்பார்வை பண்ணுது; ஒவ்வொரு நாட்டுலயும் firms-க்கு LEIs வழங்கற (பெரும்பாலும் தேசிய business registries அல்லது exchanges) **Local Operating Units (LOUs)**-ஐ அங்கீகரிக்குது; Section 07 விவரிக்கற LAPSED/ACTIVE நிலையையும் பராமரிக்குது |
| **MiFID II "No LEI, No Trade"** | செல்லுபடியாகும், செயலில் உள்ள LEI இல்லாத counterparty-க்காக/எதிரா ஒரு முதலீட்டு firm பொதுவா reportable trade-ஐ execute பண்ணக்கூடாதுங்கற EU விதி (தொழில்நுட்ப ரீதியா MiFIR transaction reporting தேவைகளின் பகுதி) — LEI-ஐ reporting சிறப்பிலிருந்து ஒரு உண்மையான trading முன்நிபந்தனையா மாத்துது |
| **CPMI-IOSCO** ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §8-ஓட abbreviations-ல ஏற்கனவே) | CCP, CSD risk-management standards-க்கான கூட்டு உலகளாவிய standard-setter ("Principles for Financial Market Infrastructures") — Section 02-ஓட table-ல உள்ள ஒவ்வொரு CCP-யும் (LCH SwapClear, ICE Clear Credit, NSCC, முதலியன) எந்த தேசிய regulator நேரடியா மேற்பார்வை பண்ணினாலும் பூர்த்தி செய்ய எதிர்பார்க்கப்படும் அடிப்படையை வகுக்குது |
| **EMIR / Dodd-Frank** (Reporting Entity விதிகள், Section 06) | ஒவ்வொரு regime-உம் தன் சொந்த Reporting Entity hierarchy-ஐ சுயாதீனமா வரையறுக்குது — Section 06-ல உள்ள "dual-sided vs single-sided" வேறுபாடு சட்டத்துக்குள்ள உண்மையிலயே குறிவைக்கப்பட்ட இடம் இதுதான், சந்தை வழக்கம் மட்டுமில்ல |
| **தேசிய CCP/CSD Regulators** | ஒவ்வொரு jurisdiction-ஓட markets regulator-உம் (LCH-க்கு FCA, CDCC-க்கு Bank of Canada/CIRO, US CCPs-க்கு Fed/CFTC/SEC கூட்டா — [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §10) தன் உள்நாட்டு CCPs/CSDs-ஐ CPMI-IOSCO அடிப்படையின் கீழ நேரடியா உரிமம் வழங்கி மேற்பார்வை பண்ணுது |

**Takeaway:** LEI regulation தான் இந்த முழு roadmap-லயும், ஒரு data-quality கருத்து (இந்த entity-க்கு செல்லுபடியாகும் identifier இருக்கா) ஒரு கடினமான சட்ட முன்நிபந்தனையாகவும் இருக்கற ஒரே இடம் — "No LEI, No Trade"ன்னா Section 07-ஓட Golden Source, lifecycle-monitoring கவலைகள் வெறும் நல்ல reference-data சுகாதாரம் இல்ல; ஒரு trade execute ஆகுமா ஆகாதாங்கறதுக்கான வித்தியாசம்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Maple Leaf Pension Fund | Client-cleared IRS-ல Buyer (Section 05); நேரடி Clearing Member இல்ல, Bank Of Toronto-வை சார்ந்திருக்கு |
| Goldman Sachs | Seller/dealer counterparty; LCH SwapClear-ஓட ஒரு நேரடி Clearing Member |
| Bank Of Toronto | Maple Leaf Pension Fund-ஓட Clearing Member (Section 03); delegated EMIR reporting-ன் கீழ Reporting Entity (Section 06) |
| LCH SwapClear | Novation செய்யும் CCP; தானே ஒரு LEI-ஐ சுமக்கும் (Section 04) |
| Euroclear | Maple Leaf Pension Fund-ஓட Initial Margin-ஐ வெச்சிருக்கும் tri-party agent ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §4) |
| GLEIF / LOUs | LEI வழங்கல், lifecycle-ஐ மேற்பார்வை பண்ற உலகளாவிய அமைப்பு, தேசிய வழங்குநர்கள் (Section 07-08) |

---

## Recap Quiz — Numbers Edition

1.  Section 05-ஓட 5Y CAD IRS-ல, Maple Leaf Pension Fund பின்னாடி LCH SwapClear-ஓட ஒரு நேரடி Clearing Member ஆகுற அளவு பெருசானா, trade record-ல இருந்து முழுசா மறைகிற LEI field எது? எந்த உறவை அது மாத்தும்?
2.  Section 01-ஐ பயன்படுத்தி, ஒரு plain interest rate swap-ல "Buyer" என்ற சொல் எந்த தரப்புக்காவது அர்த்தமுள்ளதா? [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட risk கட்டமைப்பு ஏன் "Counterparty"-ஐ பயன்படுத்துது?
3.  ஒரு firm-ஓட LEI GLEIF database-ல "LAPSED" status-ஐ காட்டுது. Section 07-08-ஐ பயன்படுத்தி, இந்த firm MiFID II-ன் கீழ ஒரு EU-regulated counterparty-உடன் ஒரு புது reportable trade-ஐ இன்னும் சட்டப்படி execute பண்ண முடியுமா?
4.  Section 06-ஐ பயன்படுத்தி, ஒரு US dealer-க்கும் ஒரு EU buy-side client-க்கும் இடையிலான அதே bilateral trade, Dodd-Frank-ன் கீழும் EMIR-ன் கீழும் ஒரே நேரத்துல report பண்ணப்பட முடியுமா? அவை ஒரே report-ஆ?
5.  Section 02-ஐ பயன்படுத்தி, இந்த roadmap-ல மற்றொரு பாத்திரத்தோட ஒரே நிறுவனத்துல உட்கார வாய்ப்புள்ள ஒரு institution பாத்திரத்தைப் பெயரிடுங்க; அது ஏன் அந்த ரெண்டு பாத்திரங்களையும் ஒரே செயல்பாடு ஆக்காதுன்னு விளக்குங்க.
6.  Bank Of Toronto-வுக்கும் Maple Leaf Pension Fund-க்கும் இடையிலான client-clearing உறவு "[Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §4-ஓட bilateral CVA கணக்கை விட வேறுபட்ட counterparty risk உறவு"ன்னு Section 03 ஏன் சொல்லுது — குறிப்பா என்ன மாறியது?
7.  Section 07-ஐ பயன்படுத்தி, ஒரு counterparty-ஓட LEI-ஐ Golden Source-ல இருந்து தேடாம, சுயாதீனமா தட்டச்சு செய்த தன் சொந்த நகலை store பண்ற trade capture system, அந்த நகல் உள்ளிட்ட நாளன்னிக்கு சரியா இருந்திருந்தாலும் ஒரு உண்மையான data-quality risk ஏன்?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 14](../docs/2-reporting/module-14-regulatory-reporting.html): Regulatory Reporting + IT View**-க்குப் போலாம்; roadmap-ஓட இறுதி module, இது Section 06-ஓட Reporting Entity வேறுபாட்டை நேரடியா எடுத்துக்குது.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. "Clearing Member LEI" field (தற்போது Bank Of Toronto) மறையும், Maple Leaf Pension Fund தானே LCH SwapClear-க்கு நேரடியா, தன் சொந்த Clearing Member-ஆ submit பண்றதா மாறும் — Section 03-ஓட client-clearing உறவு ஒரு நேரடி Clearing Member உறவால மாற்றப்படும், CCP-யோட Goldman Sachs-ஓட தற்போதைய உறவுக்கு கட்டமைப்புல ஒத்தது.**

Section 03-ஓட diagram சரியா இந்த மாற்றுப் பாதையைக் காட்டுது: Goldman Sachs, போதுமான அளவு பெருசா இருப்பதால், இடைநிலையை முழுசா தவிர்த்து CCP-ஐ நேரடியா எதிர்கொள்ளுது — நேரடி Clearing Member ஆகுறதுதான் Section 05-ஓட party map-ல Bank Of Toronto-ஓட guarantor பாத்திரத்தின் தேவையை நீக்கும் கட்டமைப்பு மாற்றம்.

**2. இல்ல — plain IRS-ல "Buyer" என்பது அர்த்தமுள்ளதா இல்ல, ஏன்னா fixed-vs-floating cash flows பரிமாற்றத்துல இயற்கையான buy/sell திசை இல்ல (Section 01). [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட risk கட்டமைப்பு குறிப்பா "Counterparty"-ஐ பயன்படுத்துது, ஏன்னா அது buy/sell சமச்சீரற்ற தன்மையே இல்லாத ஒரு உறவையும் விவரிக்கும் அளவு பொதுவானது; Buyer/Seller ஒரு திசைசார்ந்த trade-ஐ முன்னிறுத்துது.**

Section 01-ஓட takeaway இதை நேரடியா சொல்லுது: Buyer/Seller Cash-instrument வடிவம், swaps-க்கு உடையுது; Counterparty இந்த roadmap-ல ஒவ்வொரு module-லயும் ஒவ்வொரு உறவையும் விவரிக்கும் அளவு பொதுவான ஒரே சொல் — அதனாலதான் risk சொல்லகராதி (Current Exposure, Net Exposure, CVA) Buyer/Seller மொழியை ஒருபோதும் பயன்படுத்தறதில்ல.

**3. பொதுவா இல்ல — MiFID II-ஓட "No LEI, No Trade" கொள்கை (Section 08) என்றா, EU-regulated firm lapsed LEI உள்ள counterparty-க்கு எதிரா ஒரு புது reportable trade-ஐ அது புதுப்பிக்கப்படற வரை execute பண்ண முடியாது, firm தானே சட்டப்படி தொடர்ந்து இருந்தாலும்.**

Section 07-08 சேர்ந்து இதை நிறுவுது: lapsed LEI entity-யையே செல்லாததாக்காது, ஆனா புது MiFID II-reportable trading நடவடிக்கையை தடுக்குது — இருக்கற positions unwind ஆகாது, ஆனா புது trade execution தான் விதி கடிக்கற இடம்; LEI lifecycle கண்காணிப்பை ஒரு செயலற்ற data-quality சோதனையா இல்லாம, ஒரு செயலில் உள்ள pre-trade control ஆக்குது.

**4. ஆமா, ரெண்டு regimes-உம் அதே அடிப்படை trade-க்கு சுயாதீனமா ஒரு report-ஐ கோரலாம், அவை தானா அதே report இல்ல — Section 06 விளக்குது: EMIR dual-sided reporting-ஐ (delegate ஆகலாம்) கோருது, Dodd-Frank தன் சொந்த hierarchy-ன் கீழ single-sided reporting-ஐ கோருது; ரெண்டு jurisdictions-ஐயும் கடக்கும் ஒரு trade உண்மையிலயே தனி report submissions-ஐ, உண்மையிலயே வேறு Trade Repositories-க்கு உருவாக்கலாம், ஒவ்வொண்ணும் Reporting Entity யாருங்கறதைப் பத்தி தன் சொந்த regime-ஓட விதிகளால ஆளப்படும்.**

Section 06-ஓட takeaway இதை துல்லியமா சொல்லுது: "Reporting Entity" நிலையான பாத்திரம் இல்ல, ஒரு trade தொடும் ஒவ்வொரு regulatory regime-உம் அதை வேறுவிதமா ஒதுக்குது — ஒரு cross-border trade ஒரே ஒரு report-ஐ பெறாது, ரெண்டு தரப்பில எதன் மேலயும் அதிகார எல்லை கொண்ட regimes எத்தனை இருக்கோ அத்தனை reports வரை சாத்தியமா பெறும்.

**5. ஒரு பெரிய custodian bank, வெவ்வேறு clients-க்கு அல்லது ஒரே client-ஓட வெவ்வேறு உறவுகளுக்கு, ஒரே நேரத்துல Custodian-உம், Settlement Agent-உம், Tri-Party Agent-உம் ஆக இருக்கலாம் — ஒவ்வொரு பாத்திரமும் ஒரே legal entity-யால செய்யப்பட்டாலும், ஒரு தர்க்க ரீதியா வேறுபட்ட நோக்கத்துக்கு (assets வெச்சிருக்கறது, DvP instructions-ஐ execute பண்றது, collateral-ஐ தானா நிர்வகிக்கறது) சேவை செய்யறதால, அது இந்தப் பாத்திரங்களை ஒரே செயல்பாடு ஆக்காது.**

Section 02-ஓட diagram இதை நேரடியா சொல்லுது: "ஒரு நிறுவனம், மூன்று வேறு உறவுகள்ல மூன்று வேறு பாத்திரங்கள்" — மூணையும் உள்ளடக்கும் ஒரே பொதுவான "Custodian" field-ஓட reporting system, அவை ஒரே நிறுவனப் பெயரைப் பகிர்ந்தாலும், உண்மையிலயே தனி operational நோக்கங்களுக்கு சேவை செய்யற data-வை ஒண்ணா கலக்கும்.

**6. ஏன்னா client-clearing உறவுல, Maple Leaf Pension Fund-க்கு Bank Of Toronto-ஓட counterparty risk, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §3-4-ஓட தூய bilateral MTM-and-Net-Exposure கணக்கை விட, Clearing-Member-guarantees-the-CCP கட்டமைப்பு (Section 03) வழியா ஓடுது — Maple Leaf Pension Fund default ஆனா, Bank Of Toronto, Maple Leaf-ஓட கடமைகளுக்காக CCP-க்கு பொறுப்பு; [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §4 மாதிரியாக்கிய uncleared bilateral trade மேல CVA scenario-வை விட அடிப்படையில வேறுபட்ட risk (ஒரு client-ஓட cleared position மேல guarantor risk).**

Section 03 இதை வெளிப்படையா சொல்லுது: client clearing "ஒரு புது, உண்மையிலயே தனி counterparty உறவை... CCP உறவுக்குப் பக்கத்துல, அதை மாத்தாம" அறிமுகப்படுத்துது — [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html)-ஓட CVA formula நடுவுல CCP-யோ Clearing Member-ஓ இல்லாத bilateral trade-ஐ கருதியது; client-cleared trade novation-உம் guarantor உறவும் அடுக்கப்பட்ட முற்றிலும் வேறு risk topology-ஐ கொண்டிருக்கு.

**7. ஏன்னா reference data (Section 07) — ஒரு LEI-ஓட ACTIVE/LAPSED நிலை உட்பட — எந்த trade-ஐயும் சாராம சுயாதீனமா மாறலாம் (ஆண்டு புதுப்பிப்பு, lapse, புது LOU-ன் கீழ மறுபதிவு); தன் சொந்த நிலையான, சுயாதீனமா தட்டச்சு செய்த நகலை வெச்சிருக்கற trade capture system-க்கு அந்த மாற்றத்தைப் பின்னாடி தெரிஞ்சுக்க எந்த mechanism-உம் இல்ல; ஒவ்வொரு முறையும் Golden Source-ஓட தற்போதைய record-ஐ தேடற system மட்டுமே இன்னிக்கான உண்மை நிலையை பெறும், நகல் முதல்ல உள்ளிடப்பட்ட நேரத்தின் உறைந்த snapshot-ஐ இல்ல.**

Section 07-ஓட takeaway இதை Golden Source கட்டமைப்புக்கான மைய வாதமா ஆக்குது: உள்ளிட்ட நாளன்னிக்கான சரியான தன்மை, பின்னாடி சரியா இருக்கும்னு உத்தரவாதம் இல்ல, LEI lifecycle நிகழ்வுகள் (Section 07-08) சரியா ஒரு பழைய, உள்ளூர்ல நகல் எடுத்த reference data field அமைதியா தவறவிடும் வகை மாற்றம்.
