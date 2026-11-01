# Module 16 — SFTR Reporting Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> இது ஒரு **reference module**. [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §8, [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §5, [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12 ஒவ்வொண்ணும் ஒரு பரந்த கதையின் பகுதியா SFTR-ஐ சுருக்கமா தொட்டுச்சு; இந்த module SFTR-ஐ ஆழமா பார்க்கற ஒரே இடம் — அதன் legal scope, எந்த trades இதை தூண்டுது, lifecycle-event reporting model, உண்மையான regulatory sources-க்கு links.
> **மூலம்:** English Module 16-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. SFTR-ன்னா என்ன? — நோக்கமும் சட்ட அடிப்படையும்

**Definition:** SFTR = **Regulation (EU) 2015/2365 on Securities Financing Transactions** — 2008-க்கு அப்புறமான EU regulation; **Securities Financing Transactions (SFTs)** — repo, securities lending, ஒத்த collateralized-financing trades — EU-authorized Trade Repository-க்கு report பண்ணணும்னு firms-ஐ கோருது. UK தன் சொந்த onshored சமமான **UK SFTR**-ஐ நடத்துது; Brexit-க்கு அப்புறம் FCA இதை நிர்வகிக்குது.

**ஏன் இருக்கு:** 2008 நிதி நெருக்கடி (குறிப்பா 2007–2009-ல "shadow banking" அமைப்பின் மேல ஓட்டம்), எவ்வளவு collateral நிதியளிக்கப்பட்டு, மறுபயன்படுத்தப்பட்டு, re-hypothecate ஆகுதுங்கறதுல regulators-க்கு கிட்டத்தட்ட எந்தப் பார்வையும் இல்லைன்னு காட்டியது. ஒரு வங்கி ஒரே security-ஐ ஒரு repo-ல collateral-ஆ pledge பண்ணி, திரும்பப் பெற்று, வேறொரு இடத்துல மறுபடி pledge பண்ணலாம் — நம்பிக்கை உடையும்போது நோய்த்தொற்றை பெருக்கின, எந்த ஒரு regulator-ஆலும் முடிவு-முதல்-முடிவு பார்க்க முடியாத reuse சங்கிலிகள். **Financial Stability Board (FSB)** தன் நெருக்கடிக்குப் பிந்தைய workstream-ல securities financing சந்தைகளை ஒரு குறிப்பிட்ட shadow-banking risk-ஆ அடையாளம் கண்டது; SFTR அந்த FSB பரிந்துரையின் EU அமலாக்கம் — [Module 6](../docs/1-capital-markets/module-06-fx.html) §8, [Module 8](../docs/1-capital-markets/module-08-rates.html) §8, [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §01 EMIR, MiFID II-க்கு ஏற்கனவே சுட்டிக்காட்டிய அதே மூலக் காரணம்.

```text
SFTR'S PLACE AMONG THE REPORTING REGIMES
========================================================================

  MiFIR   ->  reports EXECUTED TRANSACTIONS (cash securities, some
              derivatives) — Module 3 §09

  EMIR    ->  reports OTC DERIVATIVES specifically — Module 2 §07

  SFTR    ->  reports SECURITIES FINANCING TRANSACTIONS specifically —
              repo, reverse repo, buy-sell back, securities/commodities
              lending & borrowing, margin lending — THIS MODULE

  All three often flow to the SAME Trade Repository (REGIS-TR, DTCC,
  UnaVista all accept EMIR AND SFTR reports) — but each is a legally
  distinct obligation, with its own schema, scope, and deadline.
```

**Takeaway:** SFTR "repo-க்கான EMIR"ங்கற பின்னோக்கிய சேர்க்கை இல்ல — அது சட்டப்படி தனி regulation, EMIR-ஓட ("எவ்வளவு derivatives risk இருக்கு?")-ஐ விட வேறு systemic கேள்விக்கு ("எவ்வளவு collateral re-use, financing leverage இருக்கு?") பதில் சொல்லுது; [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §5 ரெண்டு report *வடிவங்களும்* ஒத்த counterparty/economic-terms/collateral pattern-க்கு ஒன்றிணையுதுன்னு ஏற்கனவே காட்டினாலும்.

---

## 2. ஐந்து SFT வகைகள் — உண்மையில எது scope-ல, எண்களோட

**Definition:** SFTR சரியா **ஐந்து transaction வகைகளை** "Securities Financing Transactions"-ஆ வரையறுக்குது. ஒரு trade இந்த ஐந்துல ஒண்ணு இல்லைன்னா, பொருளாதார ரீதியா எவ்வளவு ஒத்திருந்தாலும் SFTR அதுக்கு பொருந்தாது.

| SFT வகை | அது என்ன | Example |
|---|---|---|
| **Repurchase transaction** (repo / reverse repo) | இப்போ ஒரு security-ஐ விற்று, பிற்காலத்துல நிலையான விலைல திரும்ப வாங்க ஒத்துக்கறது — பொருளாதார ரீதியா collateralized cash loan | கீழ Section 2a |
| **Buy-sell back / sell-buy back** | Repo-வோட அதே பொருளாதார விளைவு, ஆனா ஒரு repo contract-ஆ இல்லாம, ரெண்டு தனி spot/forward trades-ஆ கட்டமைக்கப்பட்டது | கீழ Section 2b |
| **Securities அல்லது commodities lending / borrowing** | Collateral-க்கு (cash அல்லது non-cash) எதிரா ஒரு fee-க்கு ஒரு security (அல்லது commodity)-ஐ கடனா கொடுக்கறது | கீழ Section 2c |
| **Margin lending transaction** | ஒரு broker, client-க்கு securities வாங்க/வைத்திருக்க கடன் கொடுக்கறது, அந்த securities தாங்களே collateral | கீழ Section 2d |
| *(SFT இல்ல)* | மேல உள்ள நாலு கட்டமைப்பு இல்லாம, வெறுமனே securities-ஐ collateral-ஆ **பயன்படுத்தும்** எதுவும் — உதா., CSA-ஆல ஆதரிக்கப்படும் IRS (அது EMIR + [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) collateral, SFTR இல்ல) | [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3-ஓட சரியான வேறுபாடு: repo SFTR-ன் கீழ report ஆகுது ஏன்னா அது ஒரு financing contract, derivative இல்ல |

**Takeaway:** சோதனை கட்டமைப்பு ரீதியானது, பொருளாதார ரீதியானது இல்ல. ஒரு CSA-collateralized swap-உம் ஒரு repo-வும் ரெண்டுமே ஒரே German Bund-ஐ ரெண்டு parties இடையில நகர்த்தலாம், ஆனா repo மட்டுமே SFT — swap-ஓட collateral நகர்வு [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html)-ஓட CSA/margin mechanics-ஆல ஆளப்பட்டு, (report ஆகுதுன்னா) EMIR-ன் கீழ report ஆகுது, SFTR-ன் கீழ ஒருபோதும் இல்ல.

> **Term note — "REPO", "SLEB".** இவை வெறும் trading-desk slang இல்ல — SFTR-ஓட technical standards, ஒவ்வொரு Loan Data report-ஓட **"Type of SFT"** field-ல பயன்படுத்தற அதே enumerated values:

| Product (Section 2-ஓட table) | SFTR "Type of SFT" Code |
|---|---|
| Repurchase transaction | **REPO** |
| Buy-sell back / sell-buy back | **SBSC** |
| Securities அல்லது commodities lending/borrowing | **SLEB** |
| Margin lending transaction | **MGLD** |

> அதனால ஒரு சக ஊழியர் "அது SLEB trade"ன்னு சொல்லும்போது, அது `Type of SFT = SLEB`-ஓட SFTR-ன் கீழ reportable-ன்னு அர்த்தம் — desk "securities lending" அல்லது "stock loan"ன்னு அழைக்கும் அதே transaction. Technical standards திருத்தப்படுறதால (Section 7-ஓட callout), hard-code பண்றதுக்கு முன்னாடி தற்போதைய code பட்டியலை ESMA/FCA validation rules-க்கு எதிரா (Section 9) உறுதிப்படுத்துங்க.

### 2a. Worked example — Repo & Reverse Repo (ஒரே trade, ரெண்டு label)

**முக்கிய யோசனை:** "repo"-உம் "reverse repo"-உம் ரெண்டு வேறு products இல்ல — *ஒரே transaction*, ஒவ்வொரு counterparty-ஓட சொந்தப் பக்கத்திலிருந்து விவரிக்கப்படுது. ரெண்டு பக்கமும் SFTR-ன் கீழ ஒரே UTI-ஐ report பண்ணும்; collateral-கொடுப்பவர்/cash-கடன்வாங்குபவர் vs collateral-பெறுபவர்/cash-கடன்கொடுப்பவர் எந்த party என்பது மட்டும் வேறுபடுது.

```text
REPO / REVERSE REPO — BANK OF TORONTO <-> GOLDMAN SACHS
========================================================================

  Instrument: UK Gilt 4% 2032, GBP 20,000,000 face value
  Collateral market value (dirty, incl. accrued): GBP 20,250,000
  Haircut: 2%
  Term: 7 days     Repo rate: 5.10% (GBP), actual/365

  Cash lent  = Market Value x (1 - Haircut)
             = 20,250,000 x 0.98 = GBP 19,845,000

  Interest   = 19,845,000 x 5.10% x (7/365) = GBP 19,410

  Repurchase price (Day 7) = 19,845,000 + 19,410 = GBP 19,864,410

  BANK OF TORONTO'S SIDE: sells the Gilt, receives GBP 19,845,000
  cash, agrees to repurchase -- this is a REPO for Bank Of Toronto
  (it is BORROWING cash, POSTING the Gilt as collateral)

  GOLDMAN SACHS' SIDE: buys the Gilt, pays GBP 19,845,000 cash,
  agrees to resell -- this is a REVERSE REPO for Goldman Sachs
  (it is LENDING cash, RECEIVING the Gilt as collateral)

  -> ONE trade, ONE UTI, TWO SFTR reports (Section 4's dual-sided
     obligation) -- each counterparty's own report simply reflects
     which side of the cash/collateral exchange it sat on
```

**ஏன் report பண்ணப்படுது:** repo சரியா FSB-ஓட shadow-banking workstream (Section 1) தனிச்சுட்டிக்காட்டின குறுகிய கால, collateralized funding transaction வகை — குறுகிய கால repo funding-ஐ roll over பண்ண திடீர் மறுப்பு தான் 2008-ல வங்கி balance sheets-ஐ உறைய வெச்ச mechanic என்பதால, இந்த financing நடவடிக்கையின் அளவு, விகிதத்தை அமைப்பு முழுக்க regulators பார்க்கணும்.

### 2b. Worked example — Buy-sell back / sell-buy back

**முக்கிய யோசனை:** பொருளாதார ரீதியா repo-வுக்கு ஒத்தது, ஆனா ஒரு வெளிப்படையான rate-உள்ள ஒரு repo contract-ஆ இல்லாம, **ரெண்டு தனி trades**-ஆ (ஒரு spot sale + ஒரு forward repurchase) கட்டமைக்கப்பட்டது — financing செலவு stated repo rate-ல இல்லாம, spot-க்கும் forward விலைகளுக்கும் இடையிலான வித்தியாசத்துல பதிக்கப்பட்டிருக்கு.

```text
BUY-SELL BACK — BANK OF TORONTO SELLS, MIZUHO BANK BUYS
========================================================================

  Instrument: JGB 10Y 0.4% 2036, JPY 1,000,000,000 face value
  Spot leg:     sell today at clean price 99.50
  Forward leg:  repurchase in 30 days at clean price 99.53

  Spot proceeds       = 1,000,000,000 x 99.50% = JPY 995,000,000
  Forward repurchase  = 1,000,000,000 x 99.53% = JPY 995,300,000
  Implied financing cost (30 days) = JPY 300,000

  Implied annualized repo rate
    = (300,000 / 995,000,000) x (365/30) ~ 0.37%

  -> Two trade tickets (spot sale, forward purchase), but SFTR looks
     through the two-leg STRUCTURE to the underlying ECONOMIC
     substance and reports it as ONE SFT, Type of SFT = SBSC --
     the same reporting treatment a repo gets, just a different code
```

**ஏன் report பண்ணப்படுது:** SFTR தன் ஐந்து வகைகளுக்குள்ள வேணும்னே கட்டமைப்பு-சார்பற்றது — buy-sell backs ஒரு repo contract-க்குப் பதிலா ரெண்டு trade tickets பயன்படுத்துதுங்கறதால மட்டும் report பண்ணப்படாம விடப்பட்டா, firms repo financing-ஐ பொருளாதார ரீதியா நகல் எடுத்து, regulation உருவாக்க நினைக்கற அதே வெளிப்படைத்தன்மை கடமையை (Section 1) தவிர்க்கலாம்.

### 2c. Worked example — Securities Lending / Borrowing (SLEB)

**முக்கிய யோசனை:** ஒரு தரப்பு ஒரு குறிப்பிட்ட security-ஐ *கடனா வாங்கணும்* (பொதுவா short sale அல்லது settlement fail-ஐ cover பண்ண), மற்றது collateral-க்கு எதிரா ஒரு fee-க்கு அதை *கடனா கொடுக்குது* — "loan" தானே cash கை மாறாது, collateral-ஆ மட்டும். Securities lending-ல தான் **agent lender** அதிகம் தோன்றுது: shares-ஓட beneficial owner அரிதாவே loan-ஐ நேரடியா பேசி ஆவணப்படுத்துறார்.

> **உண்மையில யார் counterparty?** Maple Leaf Pension Fund AAPL shares-ஐ சொந்தமா வெச்சிருக்கு, ஆனா அதன் custodian **Custodian Trust Co.** அதன் **agent lender**-ஆ செயல்படுது — Custodian Trust Co. தான் securities lending agreement-ல கையெழுத்து போட்டு, Bank Of Toronto-ஐ எதிர்கொண்டு, collateral-ஐ தன் சொந்த பெயர்ல வெச்சிருக்கும் அமைப்பு. Maple Leaf Pension Fund Bank Of Toronto-ஓட loan ஆவணங்கள்ல அறவே தோன்றாது; தானே ஒரு SFT இல்லாத தனி agency உறவின் கீழ, lending-fee economics-ஐ மட்டும் Custodian Trust Co.-ல இருந்து திரும்பப் பெறுது.

```text
THREE NAMES, BUT BANK OF TORONTO'S ONLY CONTRACT IS WITH THE AGENT
========================================================================

  Maple Leaf Pension Fund  ----(appoints as agent lender)---->
                                                    Custodian Trust Co.
  (beneficial owner of the AAPL shares;             (agent lender -- acts
   receives fee economics back, minus                in its OWN name)
   a small agent fee retained by Custodian Trust Co.)

  Custodian Trust Co.  <====  SECURITIES LENDING AGREEMENT  ====>  Bank Of Toronto
  (agent lender, the actual legal counterparty)                     (borrower)

  -> Bank Of Toronto's loan documentation, collateral receipt, and
     SFTR report all name CUSTODIAN TRUST CO. as counterparty --
     Maple Leaf Pension Fund is invisible to Bank Of Toronto's own
     paperwork, even though it's the shares' real economic owner
```

```text
SECURITIES LENDING / BORROWING (SLEB) — CUSTODIAN TRUST CO. LENDS
(AS AGENT FOR MAPLE LEAF PENSION FUND), BANK OF TORONTO BORROWS
========================================================================

  Security on loan: 100,000 shares of Apple Inc (AAPL) @ USD 225.00
  Market value of loan = 100,000 x 225.00 = USD 22,500,000

  Collateralization requirement: 102%
  Required collateral value = 22,500,000 x 1.02 = USD 22,950,000
  Collateral type: cash, USD

  Lending fee: 30 bps (0.30%) annualized on loan value
  Term: 30 days, actual/360

  Fee = 22,500,000 x 0.30% x (30/360) = USD 5,625

  -> Bank Of Toronto BORROWS the shares (to cover a client short
     sale) and POSTS USD 22,950,000 cash collateral TO CUSTODIAN
     TRUST CO.; Custodian Trust Co. LENDS the shares (as agent) and
     RECEIVES the collateral, collecting the USD 5,625 fee and
     passing the bulk of it back to Maple Leaf Pension Fund as
     beneficial owner
  -> Reported by both sides, Type of SFT = SLEB
```

**ஏன் report பண்ணப்படுது:** short-selling-உம் settlement-fail coverage-உம் நடைமுறைல எப்படி நிதியளிக்கப்படுதுங்கறது securities lending வழியாத்தான் — ஒரு குறிப்பிட்ட security-ஓட free float-ல எவ்வளவு loan-ல இருக்கு (எந்த collateral-க்கு எதிரா)ன்னு regulators பார்க்க வேண்டும், short-selling திறனையும், lenders தங்கள் shares-ஐ ஒரே நேரத்துல திரும்பக் கோரினா வரும் liquidity risk-ஐயும் புரிஞ்சுக்க.

**SFTR reporting பார்வை — உண்மையில யார் report பண்றது?** Section 4-ஓட delegation table ஏற்கனவே "Agent lender... பெரும்பாலும் delegation ஏற்பாட்டின் கீழ தனக்குக் கீழுள்ள lending clients சார்பா report பண்ணும்"ன்னு flag பண்ணுது. இந்த trade சரியா அந்த case: Custodian Trust Co., contract-ல சட்டப்படி இருக்கற entity-யா, பொதுவா SFTR report-ஐ submit பண்றது — ஒண்ணு அதன் counterparty-ஆ சொந்த report, அல்லது அவற்றின் குறிப்பிட்ட agency agreement-ஐப் பொறுத்து Maple Leaf Pension Fund-ஓட பக்கத்தையும் உள்ளடக்கும் delegated report. Section 4-ஓட dual-sided கடமை இன்னும் பொருந்தும், ஆனா Maple Leaf Pension Fund-ஓட பக்கத்துல report-ல எந்த legal entity-யின் பெயர் தோன்றும்ங்கறது அந்த delegation-ஐப் பொறுத்தது, shares யாருக்குச் சொந்தம்ங்கறதை மட்டும் இல்ல.

### 2d. Worked example — Margin lending

**முக்கிய யோசனை:** ஒரு broker (பெரும்பாலும் prime broker) client-ஓட சொந்த securities portfolio-ஆல ஆதரிக்கப்பட்ட கடன் தருது — repo/SLEB-ல இருந்து வேறுபட்டது, ஏன்னா *loan* தானே cash-for-securities-portfolio கடன், security-for-security அல்லது security-for-cash financing trade இல்ல.

```text
MARGIN LENDING — BANK OF TORONTO PRIME SERVICES LENDS TO
NORTHWIND CAPITAL (HEDGE FUND CLIENT)
========================================================================

  Client buys USD 10,000,000 of listed equities
  Client's own cash (initial margin, 50%): USD 5,000,000
  Margin loan from Bank Of Toronto:        USD 5,000,000

  Collateral: the full USD 10,000,000 equity portfolio
  Loan rate: SOFR + 150bps (illustrative SOFR 5.30% -> 6.80% all-in)

  Initial Loan-to-Value (LTV) = 5,000,000 / 10,000,000 = 50%

  -> Reported by Bank Of Toronto, Type of SFT = MGLD -- see
     Section 6's worked margin-call example for what happens when
     the portfolio's value moves against this loan
```

**ஏன் report பண்ணப்படுது:** margin lending மதிப்பு நகரக்கூடிய securities-ஆல ஆதரிக்கப்பட்ட leveraged credit — விழும் சந்தையில இழப்புகளை பெருக்கும் ("margin spiral") leverage-பிளஸ்-collateral-தர கலவை சரியா இது; அதனாலதான் SFTR இதை சாதாரண வங்கிக் கடனா நடத்தாம, repo, securities lending-உடன் சேர்த்து கண்காணிக்குது.

### கூடுதல் உதாரணம் — நான்கு SFT வகைகளை ஒரே பார்வைல

| | Repo (REPO) | Buy-sell back (SBSC) | Sec lending (SLEB) | Margin lending (MGLD) |
|---|---|---|---|---|
| என்ன நகருது (ஆரம்பம்) | Security ↔ Cash | Security ↔ Cash (ரெண்டு trade tickets) | Security ↔ Collateral (cash/non-cash) | Cash loan ↔ Client portfolio (collateral) |
| Financing செலவு எங்க தெரியுது | Repo rate | Spot-Forward விலை வித்தியாசம் | Lending fee (bps) | Loan rate (SOFR + spread) |
| யார் கடன் வாங்குகிறார் | Cash borrower | Cash borrower | Security borrower | Client |
| முக்கிய ஆபத்து | Collateral மதிப்பு விழுதல் | அதே | Borrower return பண்ணாதது | Portfolio மதிப்பு விழுதல் (LTV) |

---

## 3. எந்த Trades Report பண்ணப்படணும் — Scope சோதனை

**Definition:** ஒரு trade SFTR-reportable ஆக **ரெண்டு** சோதனைகளையும் கடக்கணும். ஏதாவது ஒண்ணை தவறா பெற்றா, reporting system ஒண்ணு under-report பண்ணும் (தவறவிட்ட கடமைகள், regulatory அபராதங்கள்), இல்லன்னா over-report பண்ணும் (சத்தம், வீணான reconciliation முயற்சி).

**Test 1 — Product scope:** trade Section 2-ஓட ஐந்து SFT வகைகள்ல ஒண்ணா?

**Test 2 — Counterparty/branch scope:** trade-ஓட குறைந்தது ஒரு பக்கத்துக்காவது EU (அல்லது UK SFTR-ல UK) தொடர்பு இருக்கா? SFTR-ஓட பிராந்திய scope பிடிப்பது:

| Scope-ல | Scope-க்கு வெளியே |
|---|---|
| **EU-ல நிறுவப்பட்ட** எந்த counterparty-யும் (வங்கி, investment firm, insurer, UCITS/AIF, pension fund, non-financial counterparty — EMIR-ஓட சொந்த சொற்களான "FC" அல்லது "NFC", SFTR மறுபயன்படுத்துவது) | EU/UK-க்கு வெளியே நிறுவப்பட்ட ரெண்டு counterparties, EU/UK branch எதுவும் சம்பந்தப்படாம, EU/UK-க்கு வெளியே முழுக்க trade பண்றது |
| ஒரு non-EU firm-ஓட **EU branch**, அந்த branch நுழைந்த SFTs-க்கு | ஒரு non-EU firm-ஓட non-EU branch |
| ஒரு EU firm-ஓட **non-EU branch**, அந்த branch நுழைந்த SFTs-க்கு (EU parent-ஓட கடமை உலகளவுல தன் branches-ஐ பின்தொடரும்) | — |
| ஒரு EU counterparty-ஓட (அல்லது EU branch-ஓட) **collateral reuse**, மறுபயன்படுத்தப்பட்ட security தானே எங்க இருந்தாலும் தனியா report பண்ணப்படுது | — |

**விலக்குகள்:** central banks, ESCB (European System of Central Banks), Bank for International Settlements (BIS), சில public-sector debt-management அமைப்புகள் SFTR reporting-ல இருந்து விலக்கு — EMIR-ஓட சொந்த central-bank விலக்கை ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7) பிரதிபலிக்குது.

```text
WORKED SCOPE TEST — THREE TRADES, THREE ANSWERS
========================================================================

  1) Bank Of Toronto (EU-branch-booked) repos a German Bund with
     Goldman Sachs (US entity, no EU branch involved in this trade)
     -> Test 1: YES, it's a repo (Section 02)
     -> Test 2: YES, Bank Of Toronto's EU branch is a party
     -> REPORTABLE under SFTR (Bank Of Toronto's side); Goldman
        Sachs' side is reportable too if a Goldman Sachs EU/UK
        entity or branch was the actual booking entity

  2) Bank Of Toronto's Toronto desk (no EU/UK branch anywhere in the
     chain) repos a Canadian bond with a Canadian pension fund
     -> Test 1: YES, it's a repo
     -> Test 2: NO EU/UK nexus at all
     -> NOT reportable under SFTR (Canada has no SFTR-equivalent as
        of 2026 — systems-landscape §12)

  3) Bank Of Toronto's 5Y CAD IRS with Goldman Sachs, collateralized
     under a CSA (Module 12)
     -> Test 1: NO -- an IRS is a derivative, not one of the five
        SFT types, regardless of EU nexus
     -> NOT reportable under SFTR (it's an EMIR trade instead --
        Module 14 §1's decision-matrix framing)
```

**Takeaway:** "இந்த trade-க்கு SFTR report தேவையா?" எப்பவும் *ரெண்டு* சோதனைகளையும் சுயாதீனமா சரிபார்க்கணும் — EU/UK தொடர்பு இல்லாத repo Test 1-ஐ கடந்தாலும் Test 2-ல scope-க்கு வெளியே; CSA-collateralized swap counterparties எவ்வளவு EU-கனமா இருந்தாலும் Test 1-ல தோற்குது. ஒரு reporting rules engine ஒரு ஒருங்கிணைந்த heuristic-ஆ இல்லாம, இரண்டு தனி boolean சோதனைகளா encode பண்ண வேண்டிய ரெண்டு-பகுதி விதி இதுதான்.

---

## 4. Dual-Sided Reporting & யார் Report பண்றது

**Definition:** EMIR போல ([Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §6), SFTR ஒரு **dual-sided** reporting கடமை — scope-ல உள்ள SFT-ஓட ரெண்டு counterparties-உம் தங்கள் சொந்த report-ஐ சுயாதீனமா submit பண்ணணும், ஒவ்வொண்ணும் அதே **UTI (Unique Transaction Identifier)**-ஐ சுமந்து, அதனால Trade Repository ரெண்டு submissions-ஐயும் pair செய்து reconcile பண்ணலாம் ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3-ஓட pairing mechanism, SFTR மாற்றம் இல்லாம மறுபயன்படுத்துவது).

**Delegated reporting:** ஒரு counterparty மற்றதின் சார்பா report பண்ண SFTR அனுமதிக்குது (ஒரு பெரிய வங்கிக்கும் சிறிய buy-side client-க்கும் இடையிலான பொதுவான ஏற்பாடு), மேலும் — EMIR-ஓட சொந்த small-counterparty நிவாரணத்தை பிரதிபலிச்சு — வரையறுக்கப்பட்ட அளவு வரம்புகளுக்கு கீழ உள்ள ஒரு **small Non-Financial Counterparty (NFC)**-உடன் trade பண்ற ஒரு **Financial Counterparty (FC)** *ரெண்டு பக்கத்தையும்* report பண்ற சட்டப் பொறுப்பை ஏற்குது, அதனால சிறிய counterparty தன் சொந்த reporting pipeline-ஐ கட்டவே தேவையில்ல.

| Reporting Party | பொறுப்பு |
|---|---|
| ரெண்டு counterparties-உம் (default) | ஒவ்வொண்ணும் தன் சொந்த report-ஐ சுயாதீனமா submit பண்ணும், பகிரப்பட்ட UTI-ஓட tag பண்ணி |
| பெரிய FC vs. small NFC | FC ரெண்டு பக்கத்தையும் report பண்ணும் (சட்டப்படி delegate ஆனது, வெறும் தேர்வு இல்ல) |
| CCP-cleared SFTs | Cleared leg-க்கு CCP தானே reporting பொறுப்பை ஏற்கலாம் |
| Agent lender (securities lending) | பெரும்பாலும் delegation ஏற்பாட்டின் கீழ அடிப்படை lending clients சார்பா report பண்ணும் |

**Takeaway:** dual-sided reporting தான் Trade Repository-ஓட pairing/reconciliation control-ஐ சாத்தியமாக்குது ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3) — ஆனா *delegation* விருப்பங்கள் என்றா, report-ஐ சரியா பெற சட்டப்படி பொறுப்பான entity எப்பவும் trade capture system-ஐ உண்மையில கட்டிய entity இல்ல; ஒரு counterparty-ஓட delegated reporting-ஐ சார்ந்திருக்கற எந்த firm-க்கும் ஒரு உண்மையான operational-risk புள்ளி.

---

## 5. Lifecycle Events — Trade Inception மட்டும் இல்ல

**Definition:** இது EMIR-ல இருந்து SFTR-ஓட மிக IT-பொருத்தமான வேறுபாடு. EMIR பெரும்பாலும் trade inception + அவ்வப்போதைய valuation updates-ஐ report பண்ணுது. **SFTR, ஒரு SFT-ஓட முழு வாழ்க்கை முழுக்க தனித்தனி *action types*-ஐ report பண்ணுது** — கீழே உள்ள ஒவ்வொரு நிகழ்வும் அதே UTI-ஐ reference பண்ணி, தன் சொந்த report-ஐ உருவாக்குது.

| Action Type | எதனால் தூண்டப்படுது | Example |
|---|---|---|
| **New** | Trade execution | Bank Of Toronto-ஓட overnight repo தொடங்குது |
| **Modification** | முன்பு report பண்ணின terms-ல ஏதாவது மாற்றம் | திறந்த repo-ல rate மறுபேச்சு |
| **Valuation update** | SFT-ஓட, அதன் collateral-ஓட தினசரி mark-to-market | Collateral market value இரவோடு நகர்கிறது |
| **Collateral update** | Collateral post, substitute, அல்லது திரும்ப | Bund வேறொரு bond-ஆல collateral-ஆ மாற்றப்படுது |
| **Margin update** | Margin lending SFTs-க்கு Initial/variation margin post பண்ணப்படுது | Prime brokerage margin call |
| **Reuse (rehypothecation)** | ஒரு SFT-ல பெற்ற collateral வேறொரு transaction-ல மறுபயன்படுத்தப்படுது | Goldman Sachs, Bank Of Toronto-கிட்ட பெற்ற Bund-ஐ re-pledge பண்றாங்க |
| **Termination / early termination** | SFT maturity ஆகுது அல்லது முன்கூட்டியே unwind ஆகுது | Overnight repo அடுத்த business day-ல settle ஆகி திரும்புது |
| **Error / correction** | முன்பு submit பண்ணின report-ல தவறு இருந்தது | UTI mismatch திருத்தம் ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3-ஓட pairing-break scenario) |

**Takeaway:** "trade booked, பிறகு அவ்வப்போது valuation" report பண்ண கட்டின EMIR-பாணி reporting pipeline ஒரு SFT-ஐ கட்டமைப்பு ரீதியா under-report பண்ணும் — SFTR ஒவ்வொரு lifecycle நிலை மாற்றத்தையும் பின்தொடரும் event-driven reporting model-ஐ எதிர்பார்க்குது ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html)-ஓட lifecycle stages, ஆனா முடிவுல சுருக்கப்படாம தனித்தனியா report பண்ணப்படுது) — அதனாலதான் [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12 இதை கட்டும் யாருக்கும் நடைமுறை எச்சரிக்கையா "ஏற்கனவே இருக்கற EMIR pipeline-ஐ வெறுமனே நீட்டாதீங்க"ன்னு flag பண்ணுது.

---

## 6. Data Fields, UTI & Report Types

**Definition:** SFTR-ன் technical standards தோராயமா **155 reportable data fields**-ஐ வரையறுக்குது, **நாலு report tables**-ஆ பிரிச்சு; ஒவ்வொண்ணும் SFT-ஓட வேறு பொருளாதாரப் பரிமாணத்துக்கு:

| Report Table | கொண்டிருப்பது |
|---|---|
| **Loan data** | Counterparty identifiers (LEIs), UTI, trade/value/maturity dates, principal amount, rate/spread, SFT type |
| **Collateral data** | Collateral instrument (ISIN), collateral quantity/value, haircut ([Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §3-ஓட சரியான mechanic), collateral type (cash vs. non-cash) |
| **Margin data** | Margin lending / CCP-cleared SFTs-க்கு Initial margin, variation margin, currency |
| **Reuse data** | மறுபயன்படுத்தப்பட்ட collateral மதிப்பு, cash collateral-ஓட reinvestment, ஒரு reporting counterparty-ஓட முழு book முழுக்க aggregate (trade-by-trade இல்ல) |

ஒவ்வொரு report-உம் ஒரு **UTI**-ஐ சுமக்குது; EMIR பயன்படுத்தற அதே waterfall logic-ஆல ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §12) generate ஆகுது — UTI-generator-ஆ designate ஆன counterparty execution-ல அதை உருவாக்கும், Trade Repository ரெண்டையும் pair பண்ண ரெண்டு பக்க reports-உம் ஒரே மதிப்பை சுமக்கணும்.

**Takeaway:** loan/collateral/margin/reuse நான்கு-வழிப் பிரிவு, [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3-ஓட EMIR field groups-ஐ (counterparty/common/valuation/collateral data) போதுமான அளவு நெருக்கமா பிரதிபலிக்குது; அதனால ஒரு regime-க்குக் கட்டின reporting engine தன் data model-ஓட பெரும்பகுதியை மற்றதுக்கு மறுபயன்படுத்தலாம் — ஆனா reuse data-க்கு EMIR சமமானதே இல்ல, ஏன்னா rehypothecation reporting ஒரு SFTR-குறிப்பான கருத்து (regulation இருக்கறதற்கான Section 1-ஓட மையக் காரணம்).

### 6a. Collateral data, worked — haircut mechanic

Section 2a-ஓட repo-வை மறுபயன்படுத்தி: Bank Of Toronto Gilt-ஐ post பண்ணுது, Goldman Sachs cash கடன் கொடுக்குது.

```text
COLLATERAL DATA REPORT — DAY 1 (NEW)
========================================================================
  Collateral instrument: UK Gilt 4% 2032
  Collateral quantity (face value): GBP 20,000,000
  Collateral market value (dirty):  GBP 20,250,000
  Haircut:                          2%
  Collateral value post-haircut:    20,250,000 x 0.98 = GBP 19,845,000
  ^ this equals the cash lent BY CONSTRUCTION -- the haircut is
    sized so the discounted collateral value matches the cash loan
```

**ஏன் report பண்ணப்படுது:** haircut என்பது ஒரு குறிப்பிட்ட collateral வகைல விலை risk-ஐ ஒரு கடன் கொடுப்பவர் எவ்வளவு உறிஞ்ச தயாரா இருக்கான்னு சந்தையோட சொந்த நிகழ்நேர வாசிப்பு — அமைப்பு முழுக்க haircuts-ஐ கண்காணிக்கும் regulators, 2007-ஓட ஆரம்ப subprime அதிர்ச்சியை பரந்த funding freeze-ஆ மாத்தின அதே வகை அமைப்பு-முழுமையான haircut இறுக்கத்தை ("collateral crunch") கண்டுபிடிக்க முடியும். முழு haircut formula, அதன் reverse கணக்கீடு, margin call-ல இருந்து அது எப்படி வேறுபடுதுன்னு Section 11-ல.

### 6b. Valuation data, worked — தினசரி mark-to-market

அதே repo, இப்போ அதன் 7-நாள் காலத்தின் Day 2-ல: இரவோடு Gilt-ஓட விலை 0.5% விழுது.

```text
VALUATION UPDATE REPORT — DAY 2
========================================================================
  Collateral market value, Day 1: GBP 20,250,000
  Price move:                     -0.5%
  Collateral market value, Day 2: 20,250,000 x 0.995 = GBP 20,148,750
                                   (down GBP 101,250)

  Loan value (principal + 1 day's accrued interest):
    19,845,000 + (19,845,000 x 5.10% x 1/365)
    = 19,845,000 + 2,773 = GBP 19,847,773
```

**ஏன் report பண்ணப்படுது:** தினசரி valuation தான், விலைகள் நகரும்போது collateral coverage இன்னும் போதுமானதான்னு regulator-ஐயும் (counterparties-ஐயும்) பார்க்க வைக்குது — அது இல்லாம, உண்மையான பொருளாதார exposure நகர்ந்தாலும் repo-ஓட reported terms Day 1 எண்களிலேயே உறைஞ்சு நிக்கும்; எந்த Mark-to-Market process-க்கும் [Module 10](../docs/1-capital-markets/module-10-valuation.html) §1 flag பண்ற அதே பழைய-data risk.

> **Deep dive இருக்கு.** இந்த Day 1 → Day 2 valuation update ஒரு **position**-நிலை report, trade-நிலை இல்ல — புது trade எதுவும் நடக்கலை, நிற்கும் repo position-ஓட market value மட்டும் நகர்ந்தது. SFTR-ஓட தினசரி கடமை EMIR, MiFID-ஓட மிக வேறுபட்ட position-reporting cadences-ஓட எப்படி ஒப்பிடுதுங்கற முழு theory-க்கு [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §11 பாருங்க.

### 6c. Margin data, worked — collateral call உண்மையில எப்போ தூண்டுது

**Repo margin call.** 6b-ஐ தொடர்ந்து: தேவையான collateral மதிப்பு (Day 1-ல நிர்ணயிக்கப்பட்டது) GBP 20,250,000-லயே இருக்கு (கொடுத்த cash ÷ (1 − haircut) = 19,845,000 / 0.98). Day 2-ஓட உண்மையான collateral மதிப்பு GBP 20,148,750-க்கு விழுந்திருக்கு.

```text
MARGIN CALL — REPO COLLATERAL SHORTFALL
========================================================================
  Required collateral value: GBP 20,250,000
  Actual collateral value:   GBP 20,148,750
  Shortfall:                 GBP 101,250

  -> Goldman Sachs (cash lender) calls Bank Of Toronto for GBP
     101,250 of additional collateral (cash or more Gilts) to
     restore the required ratio -- reported as a Collateral update
     (Section 5's action types), same UTI
```

**Margin lending call.** Section 2d-ஓட Northwind Capital கடன்: portfolio மதிப்பு USD 10,000,000-ல இருந்து USD 7,500,000-க்கு (ஒரு கூர்மையான வீழ்ச்சி) விழுது, loan தொகை USD 5,000,000-லயே மாறாம.

```text
MARGIN CALL — MARGIN LENDING LTV BREACH
========================================================================
  Loan amount (fixed):        USD 5,000,000
  Portfolio value, Day 1:     USD 10,000,000  (LTV 50%)
  Portfolio value, after drop: USD 7,500,000  (LTV = 5.0M/7.5M = 66.7%)
  Maintenance LTV threshold:   65%

  66.7% > 65% -> MARGIN CALL TRIGGERED

  Collateral needed to restore LTV to 50% = Loan / 50% = 10,000,000
  Additional collateral required = 10,000,000 - 7,500,000
                                  = USD 2,500,000
```

**ஏன் report பண்ணப்படுது:** margin/collateral calls தான், விழும் asset விலைகள் firms-ஐ குறுகிய அவகாசத்துல cash திரட்ட அல்லது assets-ஐ விற்க வைக்கும் சரியான mechanism — இந்த calls எவ்வளவு அடிக்கடி, எவ்வளவு பெருசா ஓடுதுங்கற அமைப்பு-முழுமையான பார்வை, பல firms முழுக்க ஒரே நேரத்துல ஒருங்கிணைந்த விற்பனை நடக்கறதுக்கு முன்னாடியே ஒரு "margin spiral" உருவாகுறதை regulators பார்க்க உதவுது.

### 6d. Reuse data, worked — rehypothecation சங்கிலி

Section 2a-ஓட repo-வை தொடர்ந்து: Goldman Sachs, Bank Of Toronto-கிட்ட இருந்து GBP 20,250,000 Gilt collateral-ஐ பெற்றிருக்கு. அப்புறம் அந்த அதே collateral-ல ஒரு பகுதியை தன் சொந்த, தனி financing trade-ல பயன்படுத்துது.

```text
REUSE — GOLDMAN SACHS RE-PLEDGES BANK OF TORONTO'S COLLATERAL
========================================================================
  Collateral received from Bank Of Toronto: GBP 20,250,000 (Gilts)

  Goldman Sachs enters its OWN repo with Deutsche Bank, posting
  GBP 15,000,000 face value of THOSE SAME Gilts as its collateral
  to borrow additional cash

  Reuse rate (for this collateral type)
    = value reused / value received
    = 15,000,000 / 20,250,000 ~ 74.1%

  -> Goldman Sachs reports this as REUSE DATA -- an AGGREGATE figure
     across its whole book for this collateral type/currency
     (Section 6's table), NOT tied line-by-line to Bank Of Toronto's
     original repo UTI the way a Modification report would be
```

**ஏன் report பண்ணப்படுது:** Section 1-ஓட "எந்த ஒரு regulator-ஆலும் முடிவு-முதல்-முடிவு பார்க்க முடியாத reuse சங்கிலிகள்" விவரிக்கற சரியான mechanic இது — அதே GBP 20,250,000 Gilts இப்போ ஒரே நேரத்துல *ரெண்டு* தனி cash loans-க்கு (Bank Of Toronto-ஓட அசல் repo-வும் Goldman Sachs-ஓட Deutsche Bank-உடனான repo-வும்) ஆதரவா நிக்குது; reuse தனியா report பண்ணப்படாட்டா கண்ணுக்குத் தெரியாத collateral-இயக்கப்பட்ட leverage வடிவம்.

---

## 7. Reporting Deadline, Phase-In & Backloading

**Definition:** SFTR, EMIR/MiFIR மாதிரி அதே **T+1** ஒழுக்கத்தை ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §9) பின்பற்றுது — ஒரு reportable event அடுத்த business day முடிவுக்குள்ள Trade Repository-க்கு submit பண்ணப்படணும்.

**Phase-in (EU, 2020–2021):** SFTR go-live ஒரே தேதில எல்லாருக்கும் இல்லாம, counterparty வகை வாரியா படிப்படியா நடந்தது — வங்கிகள், investment firms முதல்ல (April 2020), அப்புறம் CCPs, CSDs (July 2020), பிறகு insurers, UCITS/AIFs, pension funds (October 2020), கடைசியா non-financial counterparties (January 2021). UK இதே படிப்படியான model-ஐ Brexit-க்கு அப்புறம் UK SFTR-க்கு எடுத்துச் சென்றது.

**Backloading:** ஒரு counterparty-ஓட சொந்த go-live தேதில இன்னும் நிலுவையில (maturity ஆகாம) இருந்த SFTs, go-live-க்கு அப்புறம் ஒரு வரையறுக்கப்பட்ட window-க்குள்ள **backload** பண்ணப்படணும் — பின்னோக்கி report பண்ணப்படணும் — அதனால Trade Repository முதல் நாளிலிருந்தே புதுசா execute ஆன trades மட்டுமில்லாம, உயிருள்ள exposure-ஓட முழுப் படத்தையும் வெச்சிருக்கும்.

> **Callout — இவற்றுக்கு எதிரா கட்டறதுக்கு முன்னாடி தற்போதைய தேதிகளை உறுதிப்படுத்துங்க.** சரியான வரம்புகள் (உதா., Section 4-ஓட small-NFC அளவு சோதனை), phase-in தேதிகள், field எண்ணிக்கைகள் ESMA/FCA **technical standards (RTS/ITS)**-ஆல நிர்ணயிக்கப்படுது; இவை அவ்வப்போது திருத்தப்படுது (ஒரு SFTR "refit", [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §8-ஓட EMIR REFIT-க்கு ஒத்தது). இந்த module-ஓட எண்களை கருத்து வடிவமா மட்டும் எடுத்துக்கோங்க; ஒரு rules engine-ல encode பண்றதுக்கு முன்னாடி தற்போதைய எண்களை Section 9-ஓட அதிகாரப்பூர்வ links-க்கு எதிரா உறுதிப்படுத்துங்க.

**Takeaway:** படிப்படியான go-live என்றா, ஒரு உண்மையான Trade Repository-ஓட SFTR data 2020-ல உடனே முழு சந்தை coverage-ஐ அடையலை — அந்தக் காலத்து aggregate SFTR statistics-க்கு எதிரா தன் சொந்த book-ஐ reconcile பண்ற firm, எந்த counterparty வகைகள் அப்போ report பண்ணவே கட்டாயப்படுத்தப்பட்டிருந்தன என்பதை கணக்கில எடுக்கணும்.

---

## 8. Worked Example — Bank Of Toronto-ஓட Repo, முழு Lifecycle

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12-ஓட ஒற்றை-report worked example-ஐ நீட்டி, Section 5-ஓட action types-ப்படி ஒரு *முழுமையான* SFTR lifecycle உண்மையில என்ன உருவாக்குதுன்னு காட்டுது.

```text
BANK OF TORONTO'S REPO WITH GOLDMAN SACHS — FULL SFTR REPORT TRAIL
========================================================================

  Trade: Bank Of Toronto lends EUR 50,000,000 cash overnight to
         Goldman Sachs, collateralized by Germany 3% Bund 2033
  Repo Rate: 3.15%   Trade Date: Mon 17-Aug-2026
  Overnight interest = 50,000,000 x 3.15% x (1/365) = EUR 4,315

  Report 1 — NEW (Mon 17-Aug, by Tue 18-Aug T+1)
    Both counterparties submit Loan data + Collateral data,
    same UTI, to REGIS-TR (Section 4's dual-sided obligation)

  Report 2 — VALUATION UPDATE (daily, for as long as the repo is
    open -- for an overnight repo this may be the same day it
    terminates)
    Collateral mark-to-market vs. the agreed haircut (Module 12 §3)

  Report 3 — REUSE (if triggered)
    Goldman Sachs re-pledges the received Bund into a DIFFERENT
    repo with a third counterparty before returning it -- its own
    separate report, aggregated collateral-reuse data (Section 6),
    NOT linked to the original UTI the same way a Modification is

  Report 4 — TERMINATION (Tue 18-Aug, by Wed 19-Aug T+1)
    SFT matures, cash + interest returned, Bund returned; both
    counterparties submit a Termination report closing out the UTI
```

**Takeaway:** எளிமையான சாத்தியமான SFT ஆன ஒரு plain overnight repo-வுமே குறைந்தது ஒரு New-உம் ஒரு Termination-உம் report-ஐ உருவாக்குது; Goldman Sachs collateral-ஐ திருப்பறதுக்கு முன்னாடி அதை என்ன செய்யுதுங்கறதைப் பொறுத்து ஒரு Valuation Update-உம் Reuse report-உம் கூட. பல collateral substitutions உள்ள ஒரு மாத கால term repo அந்தக் காலத்துல இன்னும் பல தனி reports-ஐ உருவாக்கும் — எல்லாமே ஒரு UTI-ஆல இணைக்கப்பட்டு (Reuse report-ஐத் தவிர, அது தனியா நிற்கும்).

**நடைமுறைல "REGIS-TR-க்கு submit" step:** Bank Of Toronto-ஓட back office raw SFTR XML message-ஐ கையால கட்டி வடிவமைக்கறதில்ல. **Cappitech** (Section 9) போன்ற ஒரு reporting-technology vendor பொதுவா வங்கியோட internal trade/collateral systems-க்கும் தேர்ந்தெடுக்கப்பட்ட Trade Repository-க்கும் நடுவுல உட்காரும் — field முழுமையை சரிபார்த்து, internal data-வை regulator-ஓட schema-வுக்கு map பண்ணி, வங்கி சார்பா REGIS-TR, DTCC, அல்லது UnaVista-க்கு உண்மையான submission-ஐயும் (எந்த rejection/resubmission சுழற்சியையும்) கையாளும்:

```text
Bank Of Toronto's Trade/Collateral Systems
        |
        v
Reporting Technology Vendor (e.g. Cappitech) -- validates, maps, formats
        |
        v
Trade Repository (REGIS-TR / DTCC / UnaVista) -- accepts, pairs UTIs
        |
        v
Regulator (ESMA / FCA) -- draws down TR data for market oversight
```

---

## 9. Regulatory Perspective — அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** technical standards, வரம்புகள் காலப்போக்குல திருத்தப்படுறதால (Section 7-ஓட callout) இந்த module வேணும்னே முதன்மை regulatory மூலங்களை மறுசொல்லாம சுட்டிக்காட்டுது. கீழ உள்ள ஒரு குறிப்பிட்ட deep link நகர்ந்திருந்தா, பெயரிடப்பட்ட தளத்தையே நேரடியா தேடுங்க — domain தானே நிலையான, அதிகாரப்பூர்வ reference.

| Source | What It Covers | Link |
|---|---|---|
| **EUR-Lex — Regulation (EU) 2015/2365** | The full legal text of SFTR itself | [eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32015R2365](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32015R2365) |
| **ESMA** | EU-level technical standards (RTS/ITS), Q&As, validation rules, guidance for SFTR | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **FCA — UK SFTR** | UK's onshored regime, UK-specific technical standards and guidance | [www.fca.org.uk](https://www.fca.org.uk) |
| **DTCC** | One of the authorized Trade Repositories accepting SFTR (and EMIR) reports | [www.dtcc.com](https://www.dtcc.com) |
| **LSEG UnaVista** | Another authorized Trade Repository for SFTR/EMIR/MiFIR reporting | [www.lseg.com](https://www.lseg.com) |
| **REGIS-TR** | The Trade Repository used in this module's worked examples | [www.regis-tr.com](https://www.regis-tr.com) |
| **Cappitech** (S&P Global) | Not a Trade Repository itself — a regulatory reporting technology platform (an ARM-style vendor) banks use to generate, validate, and submit SFTR (and EMIR/MiFIR) reports **to** a Trade Repository like DTCC, UnaVista, or REGIS-TR | [www.cappitech.com](https://www.cappitech.com) |
| **FSB (Financial Stability Board)** | The global post-crisis shadow-banking workstream SFTR implements | [www.fsb.org](https://www.fsb.org) |

**Takeaway:** ஒரு build பணிக்கு, scope-க்கும் கடமைகளுக்கும் சட்ட மூலமா EUR-Lex-ஐ (அல்லது UK SFTR-க்கு FCA சமமானதை), சரியான field பட்டியல்களுக்கும் validation விதிகளுக்கும் ESMA/FCA technical standards-ஐ, உங்க system உருவாக்க வேண்டிய உண்மையான message வடிவத்துக்கு Trade Repository-ஓட சொந்த வெளியிடப்பட்ட schema/reporting manual-ஐ நடத்துங்க — மூணு வேறு "நான் சரியா எதைக் கட்டணும்" கேள்விகளுக்கு பதில் சொல்லும் மூணு வேறு ஆவணங்கள்.

---

## 10. SFTR Report Architecture — நாலு Tables, மூணு Messages & Field Groups

**Definition:** Section 6-ஓட "Loan data / Collateral data / Margin data / Reuse data" சட்டகம் சரியான mental model, ஆனா உண்மையான wire அமைப்பை கொஞ்சம் குறைத்து மதிப்பிடுது. ESMA-ஓட technical standards ~155 fields-ஐ சரியா **நாலு எண்ணிடப்பட்ட tables**-ஆ ஒழுங்குபடுத்துது, அந்த நாலு tables வெறும் **மூணு ISO 20022 XML messages**-ல பயணிக்குது — Tables 1, 2 ஒரே message-ல ஒண்ணா:

| Table | Name | Fields | Carried In Message | Message Meaning |
|---|---|---|---|---|
| **Table 1** | Counterparty Data | 18 | `auth.052` | Securities Financing Transaction Report |
| **Table 2** | Loan & Collateral Data | 99 | `auth.052` (same message as Table 1) | Securities Financing Transaction Report |
| **Table 3** | Margin Data | 20 | `auth.070` | Margin Data Report — CCP-cleared SFTs only |
| **Table 4** | Reuse / Cash Reinvestment / Funding Sources | 18 | `auth.071` | Reused Collateral Data Report |

18 + 99 + 20 + 18 = 155 — Section 6-ஓட field எண்ணிக்கை, இப்போ table வாரியா பிரிச்சது. Hard-code பண்றதுக்கு முன்னாடி தற்போதைய எண்களை ESMA-ஓட technical standards (Section 9)-க்கு எதிரா உறுதிப்படுத்துங்க, ஏன்னா — Section 7-ஓட phase-in தேதிகள் பத்தின callout போல — field எண்ணிக்கைகள் அவ்வப்போது திருத்தப்படும் RTS/ITS-ஆல நிர்ணயிக்கப்படுது.

```text
THE SFTR MESSAGE / TABLE STRUCTURE
========================================================================

  auth.052 -- Securities Financing Transaction Report
     |
     +-- Table 1: Counterparty Data (18 fields)
     |     who is reporting, who's the other side, broker, CCP,
     |     tri-party agent, agent lender...
     |
     +-- Table 2: Loan & Collateral Data (99 fields)
           the SFT itself -- UTI, dates, rates, principal, security,
           AND its collateral -- all in one table

  auth.070 -- Margin Data Report
     |
     +-- Table 3: Margin Data (20 fields)
           ONLY for CCP-cleared SFTs -- initial/variation margin
           posted & received, separate from Table 2's collateral

  auth.071 -- Reused Collateral Data Report
     |
     +-- Table 4: Reuse / Reinvestment / Funding (18 fields)
           collateral reuse, cash reinvestment, funding sources --
           reported at counterparty/currency level, not trade-by-trade
           (Section 6d already showed why)
```

### 10a. Table 2-ஓட எட்டு field groups

Table 2 மட்டுமே 99 fields — முழு standard-ஓட பெரும்பகுதி — அதனால ESMA-ஓட சொந்த field எண்ணிடல் அதை எட்டு தர்க்கத் தொகுதிகளா பிரிக்குது:

| Group | Field Range | Covers |
|---|---|---|
| A. Transaction identity | 2.1 – 2.3 | UTI, report tracking number, event date |
| B. Transaction / clearing | 2.4 – 2.12 | Type of SFT, cleared?, CCP, trading venue, master agreement |
| C. Dates / lifecycle | 2.13 – 2.22 | Value date, maturity date, termination date, GC/DBV indicators |
| D. Rates | 2.23 – 2.36 | Fixed/floating repo rate, day count, spread, rate reset frequency |
| E. Principal / loan asset | 2.37 – 2.57 | Principal amount, ISIN, quantity, price, market value |
| F. Rebate / lending fee | 2.58 – 2.71 | Rebate rate, lending fee, outstanding margin loan |
| G. Collateral | 2.72 – 2.96 | Collateral ISIN, market value, haircut, reuse availability |
| H. Reporting mechanics | 2.97 – 2.99 | Portfolio code, Action Type, Level (trade vs. position) |

**Near leg / far leg, field அடிப்படையில.** Section 2a-ஓட repo-வை தொடர்ந்து — requirements வேலையில தொடர்ந்து சந்திக்கும் ஒரு field ஜோடி:

```text
2.13 Value Date         ->  NEAR LEG   (cash <-> Gilt exchanged today)
2.14 Maturity Date       ->  FAR LEG    (Gilt <-> cash+interest exchanged
                                         at repurchase)
```

### 10b. எண் வாரியா தெரிஞ்சிருக்க வேண்டிய Fields

எல்லா 155 fields-உம் உள்ளுணர்வை கட்டுவதுல சமமான எடையைக் கொண்டிருக்கல. இந்த module-ல கிட்டத்தட்ட ஒவ்வொரு worked example-லயும் தோன்றுபவை இவை:

| Field | # | Table | Business Meaning |
|---|---|---|---|
| UTI | 2.1 | 2 | Unique Transaction Identifier — the pairing key (Section 4) |
| Type of SFT | 2.4 | 2 | `REPO` / `SBSC` / `SLEB` / `MGLD` (Section 2's term-note table) |
| Value date | 2.13 | 2 | Near-leg settlement date |
| Maturity date | 2.14 | 2 | Far-leg / contractual end date |
| Principal amount | 2.37 | 2 | Cash lent/borrowed at inception |
| Security identifier | 2.41 | 2 | ISIN of the loaned/borrowed security |
| Market value | 2.57 | 2 | Current value of the loaned asset (Section 6b's daily MTM) |
| Collateral market value | 2.88 | 2 | Current value of the collateral (Section 11's haircut base) |
| Haircut or margin | 2.89 | 2 | The percentage buffer itself (Section 11) |
| Action Type | 2.99 | 2 | `NEWT`/`MODI`/`COLU`/... — Section 10c below |
| Portfolio code | 3.7 | 3 | CCP collateral portfolio identifier |
| Initial margin posted | 3.8 | 3 | IM posted for a CCP-cleared SFT |
| Variation margin posted | 3.10 | 3 | VM posted for a CCP-cleared SFT |
| Value of reused collateral | 4.8 | 4 | Actual (not estimated) reuse of received collateral |

### 10c. Action Type — முழு enumeration

Section 5 action types-ஐ ஏற்கனவே கருத்து ரீதியா அறிமுகப்படுத்திச்சு; இதோ உண்மையான enumerated code பட்டியல், ஒவ்வொண்ணும் எந்த table-க்கு பொருந்தும்:

| Code | Meaning | Applies To |
|---|---|---|
| `NEWT` | New transaction | Tables 2, 3, 4 |
| `MODI` | Modification | Table 2 |
| `EROR` | Cancel erroneous report | Tables 2, 3, 4 |
| `CORR` | Correct previously wrong data | Tables 2, 3, 4 |
| `POSC` | Position component | Table 2 |
| `COLU` | Collateral update | Table 2 |
| `ETRM` | Early termination | Table 2 |
| `VALU` | Valuation update | Table 2 (securities lending/borrowing) |
| `MARU` | Margin update | Table 3 only |
| `REUU` | Reuse update | Table 4 only |

**Action Type பத்தின Takeaway:** `MARU`, `REUU` ரெண்டும் வேறிடத்துல மறுபயன்படுத்தப்படும் Table-2-பாணி lifecycle events மட்டுமில்ல — அவை முறையே Tables 3, 4-க்கான *அர்ப்பணிப்பு* action types; Tables 3/4 ஒரு Table 2 report-க்குள்ள அடுக்கப்பட்ட உப-நிகழ்வுகளா இல்லாம, தங்கள் சொந்த தனி messages (`auth.070`/`auth.071`) ஆ report பண்ணப்படுறதோட ஒத்துப்போகுது.

### 10d. இந்த field எந்த table-க்கு சொந்தம்? — ஒரு முடிவு சட்டகம்

ஒரு source system-ல இருந்து ஒரு field-ஐ SFTR report-க்கு map பண்ணும்போது, இந்த ஐந்து கேள்விகளை வரிசையா கேளுங்க:

```text
Q1. Is it about the PARTIES?                     -> TABLE 1
    LEI, broker, CCP, agent lender, beneficiary

Q2. Is it about the SFT itself?                  -> TABLE 2
    UTI, type of SFT, principal, rate, dates, security

Q3. Is it about the COLLATERAL backing the SFT?  -> TABLE 2 (Group G)
    ISIN, quantity, price, market value, haircut, quality

Q4. Is it CCP MARGIN?                            -> TABLE 3
    Initial margin, variation margin, excess collateral

Q5. Is it REUSE / REINVESTMENT / FUNDING?        -> TABLE 4
    Reused collateral, reinvestment rate, funding sources
```

**Worked example — ஒரு உண்மையான repo report-ல இருந்து மூணு fields-ஐ வகைப்படுத்துங்க.** Section 8-ஓட Bank Of Toronto / Goldman Sachs Bund repo-வை பயன்படுத்தி:

-   "Bank Of Toronto-ஓட LEI" → Q1 → **Table 1**
-   "EUR 50,000,000 principal" → Q2 → **Table 2**
-   "Bund market value, haircut buffer" → Q3 → **Table 2, Group G**

### 10e. Reconciliation, worked — rate mismatch break

Section 4 ஏற்கனவே ரெண்டு counterparties-உம் அதே UTI-ஐ சுமந்து சுயாதீன reports-ஐ submit பண்றாங்கன்னு நிறுவியது; [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3-ஓட pairing mechanism இங்கயும் மாறாம பொருந்தும் — Trade Repository ஒவ்வொரு பக்கத்தோட reconcilable fields-ஐயும் ஒப்பிட்டு, ஒத்துப்போகாதவற்றை flag பண்ணுது.

Section 2a-ஓட Gilt repo-வை மறுபயன்படுத்தி, Goldman Sachs-ஓட rate field-ல ஒரு data-entry தவறு நுழைஞ்சதுன்னு வெச்சுக்கோங்க:

```text
TR RECONCILIATION -- UTI GB-RP-20260817-001
========================================================================
                          BANK OF TORONTO       GOLDMAN SACHS
  UTI                     GB-RP-20260817-001    GB-RP-20260817-001   MATCH
  Principal                GBP 19,845,000        GBP 19,845,000      MATCH
  Maturity date             2026-08-24             2026-08-24        MATCH
  Repo rate                    5.10%                 5.15%           BREAK

  -> The TR flags a RECONCILIATION BREAK on the rate field only --
     everything else pairs cleanly. This points to a data
     enrichment/mapping issue on ONE side (most likely a fat-finger
     or stale-rate-feed problem on Goldman Sachs' source system),
     NOT a dispute about the underlying trade itself, since the
     other three reconcilable fields already agree.
```

**இந்த வேறுபாடு operational ரீதியா ஏன் முக்கியம்:** ஒரு reconciliation break trade தவறுன்னு தானா அர்த்தப்படுத்தறதில்ல — ஒரு regulatory-reporting platform தானா triage பண்ண வேண்டிய சரியான வகை பிரச்சனை இது: ரெண்டு desks இடையிலான உண்மையான பொருளாதார தகராறா, இல்ல ஒரு பக்கத்தோட trade capture system-க்கும் அதன் SFTR reporting engine-க்கும் இடையிலான mapping bug-ஆ? TR-ஓட field-by-field ஒப்பீடு கேள்வியை வெளிக்கொணருது; அதைத் தீர்ப்பது downstream operations பணி.

### 10f. Conceptual auth.052 XML — field mapping-ஐ விளக்குது, உண்மையான schema இல்ல

**முக்கியம் — கல்வி நோக்கம் மட்டும்.** கீழ உள்ள XML *எந்த field எந்த report-க்கு map ஆகுது*ன்னு விளக்கும் ஒரு கற்பனை; schema-valid ESMA ISO 20022 message இல்ல. உண்மையான element பெயர்கள், namespaces, nesting ESMA-ஓட வெளியிடப்பட்ட usage guidelines, XSDs-ல (Section 9) இருந்து எடுக்கப்படணும் — இந்த tag பெயர்களை ஒரு build-க்கு copy பண்ணாதீங்க.

```text
<SecuritiesFinancingTransactionReport>          <!-- auth.052 -->

  <CounterpartyData>                             <!-- Table 1 -->
    <ReportingCounterparty>BANKOFTORONTO_LEI</ReportingCounterparty>
    <OtherCounterparty>GOLDMANSACHS_LEI</OtherCounterparty>
    <CounterpartySide>BORROWER</CounterpartySide>
  </CounterpartyData>

  <LoanAndCollateralData>                        <!-- Table 2 -->
    <UTI>GB-RP-20260817-001</UTI>
    <TypeOfSFT>REPO</TypeOfSFT>
    <ValueDate>2026-08-17</ValueDate>
    <MaturityDate>2026-08-24</MaturityDate>
    <PrincipalAmount>19845000</PrincipalAmount>
    <PrincipalCurrency>GBP</PrincipalCurrency>
    <FixedRate>5.10</FixedRate>
    <SecurityIdentifier>GB00XXXXXXX</SecurityIdentifier>
    <CollateralMarketValue>20250000</CollateralMarketValue>
    <Haircut>2.00</Haircut>
    <ActionType>NEWT</ActionType>
  </LoanAndCollateralData>

</SecuritiesFinancingTransactionReport>
```

**Takeaway.** இந்த section-ஓட நோக்கம் உண்மையான XML இல்ல — ஒரு source system-ஓட data model, நாலு tables, மூணு messages முழுக்க ஒரு field-ஐ சரியா route பண்ண ஒரு field-க்கு ஐந்து கேள்விகளுக்கு (Section 10d) மட்டும் பதில் சொல்லணும்; Trade Repository-ஓட reconciliation control (Section 10e) தான் ரெண்டு counterparties-ஓட அந்த அதே mapping logic-ஓட சுயாதீன அமலாக்கங்கள் ஒத்துப்போகுதான்னு உண்மையில சோதிக்குது.

---

## 11. Haircut Deep-Dive — Formula, Reverse Calculation & Haircut vs. Margin Call

**Definition:** haircut = collateral-ஓட market value மேல பொருந்தற **சதவீத தள்ளுபடி**, அதனால கடன் கொடுப்பவர் collateral உண்மையில மதிப்புள்ளதை விட சிறிய "நிதியளிக்கத்தக்க" மதிப்பை மட்டும் அங்கீகரிக்கிறார். Section 6a ஏற்கனவே ஒரு உயிருள்ள Collateral Data report-க்குள்ள இந்த mechanic-ஐ காட்டியது; இந்த section formula-வையே derive பண்ணுது.

```text
HAIRCUT FORMULA
========================================================================
  Haircut % = (Collateral Market Value - Cash Lent) / Collateral Market Value
```

### 11a. Worked example — haircut-ஐ derive பண்றது

Bank Of Toronto-க்கு cash தேவை; Deutsche Bank-க்கு (ஏற்கனவே Section 6d-ல ஒரு actor) German Government Bund collateral-ஐ post பண்ணுது:

```text
Collateral (German Government Bund), market value: EUR 105,000,000
Cash Deutsche Bank is willing to lend:              EUR 100,000,000

Haircut = (105,000,000 - 100,000,000) / 105,000,000
        = 5,000,000 / 105,000,000
        = 4.76%
```

இதை கட்டணமா இல்லாம, தள்ளுபடியா நினைங்க:

```text
Market value of Bund               EUR 105,000,000
        |
        v  apply 4.76% haircut
        |
Recognized collateral value        EUR 100,000,000
        |
        v
Maximum cash Deutsche Bank lends   EUR 100,000,000
```

EUR 5,000,000 இடைவெளி ஒரு **collateralization buffer**, Deutsche Bank-க்கு லாபம் இல்ல, வட்டியும் இல்ல — Section 2a-ஓட repurchase mechanic போலவே, maturity-ல cash-உம் repo வட்டியும் கிடைச்சதுக்கு ஈடா Deutsche Bank இன்னும் Bank Of Toronto-க்கு bond-ஐ திருப்பிக் கொடுக்கணும்.

### 11b. Reverse calculation — target cash தொகைக்கு collateral-ஐ அளவிடறது

Formula-வை தலைகீழா திருப்புங்க: ஒரு தேவையான haircut %-உம் target cash தொகையும் கொடுக்கப்பட்டா, எவ்வளவு collateral post பண்ணப்படணும்?

```text
REVERSE HAIRCUT FORMULA
========================================================================
  Required Collateral = Cash Amount / (1 - Haircut %)
```

Deutsche Bank 5% haircut-ஐ கோருது, Bank Of Toronto EUR 100,000,000 cash வேணும்னு வெச்சுக்கோங்க:

```text
Required Collateral = 100,000,000 / (1 - 0.05)
                     = 100,000,000 / 0.95
                     ~ EUR 105,263,158
```

**கவனிக்க வேண்டிய பொறி:** 5% haircut என்பது "cash தொகையோட 105%-ஐ post பண்ணுங்க" என்று அர்த்தமில்ல (அது EUR 105,000,000) — haircut cash தொகைக்கு எதிரா இல்லாம *collateral* மதிப்புக்கு எதிரா வரையறுக்கப்படுது. சரியான EUR 105,263,158 அப்பாவித்தனமான EUR 105,000,000 ஊகத்தை விட கொஞ்சம் அதிகம்; ஒரு system-ல collateral-sizing logic-ஐ validate பண்ணும்போது முக்கியமான வித்தியாசம்.

### 11c. Day-over-day stress — buffer உண்மையில எப்போ சோதிக்கப்படுது

11a-ஓட EUR 105m collateral / EUR 100m exposure-ஐ நீட்டி: Bund-ஓட விலை நகரும்போது buffer-க்கு என்ன ஆகுது?

| Scenario | Bund விலை நகர்வு | Collateral Value | Exposure | Buffer / Shortfall |
|---|---|---|---|---|
| Day 1 (மாறலை) | — | EUR 105,000,000 | EUR 100,000,000 | +EUR 5,000,000 buffer |
| 3% விழுது | -3% | 105,000,000 x 0.97 = EUR 101,850,000 | EUR 100,000,000 | +EUR 1,850,000 buffer |
| 5% விழுது | -5% | 105,000,000 x 0.95 = EUR 99,750,000 | EUR 100,000,000 | **-EUR 250,000 shortfall** |

**இதுதான் margin call தூண்டப்படும் தருணம்** (Section 6c-ஓட mechanic, இப்போ அதைத் தூண்டும் சரியான விலை நகர்வோடு): Bund 4.76%-ஐ — haircut தானே — கொஞ்சம் தாண்டி விழுந்தவுடனே, collateral மதிப்பு cash exposure-க்கு கீழ விழுது; buffer-ஐ மீட்க Deutsche Bank, Bank Of Toronto-ஐ கூடுதல் collateral-க்கு call பண்ணணும்.

### 11d. Haircut vs. margin call — SFTR-க்கு முக்கியமான வேறுபாடு

இந்த ரெண்டு கருத்துகளும் தொடர்ந்து குழப்பிக்கொள்ளப்படுது, ஆனா அவை வெவ்வேறு கேள்விகளுக்கு, வெவ்வேறு நேரங்கள்ல பதில் சொல்லுது:

| | Haircut | Margin Call |
|---|---|---|
| **அது என்ன** | Trade inception-ல நிர்ணயிக்கப்பட்ட ஒரு பாதுகாப்பு valuation buffer | பின்னாடி தூண்டப்படும் ஒரு திருத்த நடவடிக்கை |
| **எப்போ நிர்ணயிக்கப்படுது** | ஒரு தடவை, SFT ஒத்துக்கொள்ளப்படும்போது (Section 10b field 2.89) | Buffer ஒரு வரம்பைத் தாண்டி அரிக்கும்போதெல்லாம் |
| **எது தூண்டுது** | எதுவும் இல்ல — அது trade-ஓட ஒரு term | Collateral மதிப்பு exposure-ஐ ஒப்பிட நகர்றது (11c) |
| **SFTR reporting** | New report-ல ஒரு static field-ஆ (Table 2, Group G) | ஒரு Collateral update-ஆ, Action Type `COLU` (Section 10c) |

```text
HAIRCUT = initial/protective valuation buffer.
MARGIN CALL = the action taken when the collateral/exposure
              relationship needs to be restored.
```

**Cross-reference:** இது [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) §3, collateral-management பக்கத்துல இருந்து அறிமுகப்படுத்தற அதே haircut mechanic; ஒரு SFTR reporting engine field 2.89-ஐ fields 2.57/2.88-க்கு எதிரா validate பண்ண உண்மையில தேவைப்படற விதத்துல இந்த section formula-வை derive பண்ணுது.

**Takeaway.** haircut ஒரு தடவை நிர்ணயிக்கப்பட்டு தானா நகராது — அதைச் சுத்தி நகர்றது *collateral-ஓட market value*; அந்த drift தான், haircut தானே இல்ல, இறுதியில margin call-ஐ தூண்டுது. "haircut மாறிடுச்சு" vs "collateral மதிப்பு மாறிடுச்சு"ன்னு குழப்பிக்கறது ஒரு build-ல மோசமான field-mapping logic-க்கு பொதுவான மூலம்: field 2.89 (Haircut) Section 10b-ப்படி static; fields 2.57, 2.88 (Market value, Collateral market value) Section 6b-ப்படி தினமும் நகர்பவை.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Repo seller / cash borrower (Section 2a, 6a-c); Northwind Capital-க்கு margin lender (Section 2d, 6c); systems-landscape Bund repo-ல cash lender (Section 8); reconciliation-break உதாரணம் (Section 10e); haircut deep-dive-ல Bund-collateral repo borrower (Section 11) |
| Goldman Sachs | Reverse repo buyer / cash lender (Section 2a); Gilt/Bund collateral-ஐ பெற்று re-use (rehypothecate) பண்றது (Section 6d, Section 8); reconciliation உதாரணத்துல rate-mismatch counterparty (Section 10e) |
| Mizuho Bank | JGB buy-sell back-ல counterparty (Section 2b) |
| Maple Leaf Pension Fund | SLEB உதாரணத்துல AAPL shares-ஓட beneficial owner — loan contract-ல தானே ஒரு party இல்ல (Section 2c) |
| Custodian Trust Co. | Maple Leaf Pension Fund-ஓட agent lender — SLEB trade-ல Bank Of Toronto-ஐ எதிர்கொள்ளும் உண்மையான legal counterparty (Section 2c); Table 1-ஓட "agent lender" field-க்கான field-mapping உதாரணம் (Section 10d) |
| Northwind Capital | Bank Of Toronto Prime Services-ல இருந்து margin-ல கடன் வாங்கற hedge fund client (Section 2d, 6c) |
| Deutsche Bank | Goldman Sachs-ஓட re-pledge பண்ணின Gilt collateral-ஐ பெறும் மூன்றாம் தரப்பு counterparty (Section 6d); haircut deep-dive-ல Bank Of Toronto-ஓட Bund collateral-க்கு எதிரா cash lender (Section 11) |
| REGIS-TR | ரெண்டு counterparties-ஓட reports-ஐயும் பெறும், UTI-ல pair பண்ணும் Trade Repository; Section 10e-ல field-by-field reconciliation-ஐ நடத்துது |
| ESMA / FCA | முறையே EU SFTR, UK SFTR-க்கான technical-standard setters |
| FSB | SFTR அமல்படுத்தும் shadow-banking workstream-ஓட மூல உலகளாவிய standard-setter |

---

## Recap Quiz

1.  Section 2-ஐ பயன்படுத்தி, ஒரு CSA-collateralized interest rate swap எப்போதாவது SFTR-ன் கீழ reportable-ஆ? ஏன்/ஏன் இல்ல, ரெண்டு counterparties-உம் எங்க இருக்காங்கங்கறதைச் சாராம?
2.  Section 3-ஓட ரெண்டு-சோதனை சட்டகத்தை பயன்படுத்தி, Test 1-ஐ (product scope) கடந்து Test 2-ல (counterparty/branch scope) தோற்கும் ஒரு trade-ஐ உருவாக்குங்க. அது SFTR-reportable-ஆ?
3.  Section 4-ஐ பயன்படுத்தி, Bank Of Toronto (ஒரு பெரிய FC) வரையறுக்கப்பட்ட அளவு வரம்புகளுக்கு கீழ உள்ள ஒரு small NFC client-உடன் trade பண்ணா, NFC-ஓட பக்கத்தின் SFTR report-ஐ submit பண்ண சட்டப்படி யார் பொறுப்பு?
4.  Section 5-ஐ பயன்படுத்தி, "inception + அவ்வப்போது valuation" EMIR-பாணி reporting pipeline SFTR-க்கு அப்பாவித்தனமா மறுபயன்படுத்தப்பட்டா தவறவிடும் வாய்ப்புள்ள ரெண்டு action types-ஐ பெயரிடுங்க.
5.  Section 6-ஐ பயன்படுத்தி, நாலு SFTR report tables-ல நேரடி EMIR சமமானது இல்லாதது எது, ஏன் — Section 1-ல சொன்ன regulation-ஓட நோக்கத்தோட உங்க பதிலை இணையுங்க.
6.  Section 8-ஓட worked example-ஐ பயன்படுத்தி, Goldman Sachs தான் பெறும் Bund-ஐ ஒருபோதும் re-use பண்ணலைன்னா, நாலு-report தடத்துல எந்த report உருவாகவே உருவாகாது?
7.  Section 9-ஐ பயன்படுத்தி, SFTR reportable fields-ஓட சரியான தற்போதைய பட்டியல் உங்களுக்கு reporting engine கட்ட தேவைன்னா, எந்த வகை மூலத்தை பார்க்கணும் — EUR-Lex regulation text-ஐயா, ESMA-ஓட technical standards-ஐயா — ஏன்?
8.  Section 2a-ஐ பயன்படுத்தி, ஒரு "repo"-உம் "reverse repo"-வும் எப்போதாவது பொருளாதார ரீதியா வேறு trades-ஆ, இல்ல எப்பவும் எதிர் பக்கங்கள்ல இருந்து பார்க்கப்பட்ட ஒரே trade-ஆ? Bank Of Toronto / Goldman Sachs Gilt repo-வோட ரெண்டு பக்கமும் உண்மையில எந்த SFTR "Type of SFT" code-ஐ report பண்ணுது?
9.  Section 2c-ஐ பயன்படுத்தி, SFTR code `SLEB` எதைக் குறிக்குது? இந்த module-ல எந்த குறிப்பிட்ட transaction அந்த code-ஓட report பண்ணப்படும்?
10. Section 6c-ஓட margin lending உதாரணத்தை பயன்படுத்தி, Northwind Capital-ஓட portfolio USD 7,500,000-க்குப் பதிலா USD 8,500,000-க்கு விழுந்திருந்தா, stated 65% maintenance LTV வரம்பின் கீழ margin call தூண்டுமா? LTV கணக்கீட்டைக் காட்டுங்க.
11. Section 6d-ஐ பயன்படுத்தி, Bank Of Toronto-ஓட Gilt collateral-ஐ Goldman Sachs re-use பண்றது, Bank Of Toronto-ஓட அசல் repo UTI-உடன் நேரடியா இணைக்கப்படாம, ஒரு aggregate எண்ணா report பண்ணப்படுறது ஏன்?
12. Section 10-ஐ பயன்படுத்தி, Tables 1, 2 ரெண்டும் `auth.052` message-ல பயணிக்க, Tables 3, 4 ஒவ்வொண்ணும் தனி message பெறுவது ஏன்?
13. Section 10d-ஓட முடிவு சட்டகத்தை பயன்படுத்தி, ஒரு "agent lender" field எந்த table-க்கு சொந்தம், ஏன் — Section 2c-ஓட Custodian Trust Co. உதாரணத்தோட உங்க பதிலை இணையுங்க.
14. Section 10e-ஓட reconciliation உதாரணத்தை பயன்படுத்தி, rate-மட்டும் break உண்மையான trade தகராறையா, data-mapping பிரச்சனையையா குறிக்க அதிக வாய்ப்பு? ஏன்?
15. Section 11b-ஓட reverse formula-வை பயன்படுத்தி, Deutsche Bank 8% haircut-ஐ கோரி, Bank Of Toronto EUR 50,000,000 cash கடன் வாங்க விரும்பினா, Bank Of Toronto எவ்வளவு collateral post பண்ணணும்?

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. இல்ல — IRS ஒரு derivative, ஐந்து SFT வகைகள்ல (repo, buy-sell back, securities/commodities lending, margin lending) ஒண்ணு இல்ல; அதனால counterparty இடம் அல்லது எப்படி collateralize ஆகியிருக்குன்னு எதுவா இருந்தாலும் Test 1-ல தோற்குது.**

Section 2 சோதனை கட்டமைப்பு ரீதியானது, பொருளாதார ரீதியானது இல்லைன்னு சொல்லுது: CSA-collateralized swap-உம் repo-வும் ஒரே security-ஐ collateral-ஆ நகர்த்தலாம், ஆனா repo மட்டுமே வரையறுக்கப்பட்ட ஐந்து SFT கட்டமைப்புகள்ல ஒண்ணு. Swap-ஓட collateral [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html)-ஓட CSA mechanics-ஆல ஆளப்பட்டு, (இருந்தா) EMIR-ன் கீழ report ஆகுது, SFTR-ன் கீழ ஒருபோதும் இல்ல — [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §8-ல இருந்து நேரடியா மறுசொல்லப்பட்டது.

**2. உதாரணம்: சங்கிலியில எங்கயும் EU அல்லது UK branch இல்லாத ரெண்டு US entities இடையிலான ஒரு repo (Section 3-ஓட Worked Scope Test #2). அது Test 1-ஐ கடக்குது (அது ஒரு repo) ஆனா Test 2-ல தோற்குது (EU/UK தொடர்பு அறவே இல்ல) — SFTR-reportable இல்ல.**

Section 3-ஓட சட்டகம் ரெண்டு சோதனைகளும் சுயாதீனமா சரிபார்க்கப்படணும்னு வெளிப்படையா சொல்லுது; பிராந்திய தொடர்பு எதுவும் இல்லாத, product-scope-ல உள்ள trade சரியா, product வகையை மட்டும் சரிபார்க்கும் ஒரு rules engine-ஐ தடுமாறச் செய்யும் case.

**3. FC (Bank Of Toronto) ரெண்டு பக்கத்தையும் report பண்ண சட்டப்படி பொறுப்பு — இந்த delegated-reporting நிவாரணம் EMIR-ஓட சொந்த small-counterparty சலுகையை பிரதிபலிக்குது, அதனால சிறிய NFC-க்கு தன் சொந்த reporting pipeline தேவையில்ல.**

Section 4-ஓட table இதை "பெரிய FC vs. small NFC"-க்கு கீழ நேரடியா சொல்லுது — பொறுப்பு சட்டத்தால ஒதுக்கப்படுது, ரெண்டு firms இடையிலான தன்னார்வ ஒப்பந்தத்தால இல்ல.

**4. Reuse (rehypothecation) reporting-உம் Collateral update reporting-உம் — trade inception + அவ்வப்போது MTM updates-ஐ சுற்றி கட்டிய EMIR-பாணி pipeline-ல இயல்பான சமமானது இல்ல, ஏன்னா EMIR-க்கு rehypothecation-reporting கருத்தே இல்ல (Section 1).**

Section 5-ஓட takeaway இதை நேரடியா சொல்லுது; [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12 சுயாதீனமா அதே எச்சரிக்கையை flag பண்ணுது: SFTR reporting கட்டும்போது "ஏற்கனவே இருக்கற EMIR pipeline-ஐ வெறுமனே நீட்டாதீங்க".

**5. Reuse data-க்கு EMIR சமமானது இல்ல — ஏன்னா rehypothecation/collateral-reuse வெளிப்படைத்தன்மைதான் SFTR மூட உருவாக்கப்பட்ட குறிப்பிட்ட systemic-risk இடைவெளி (Section 1-ஓட FSB shadow-banking காரணம்), EMIR-ஓட OTC-derivatives-risk mandate ஒருபோதும் உள்ளடக்க வேண்டியதில்லாத கவலை.**

Section 6-ஓட takeaway இந்த இணைப்பை வெளிப்படையா வரையுது: Loan/Collateral/Margin data எல்லாம் EMIR-ஓட சொந்த field groups-ஐ நெருக்கமா பிரதிபலிக்குது, ஆனா Reuse data தான் ஒரே உண்மையிலயே புது வகை — நேரடியா Section 1-ஓட "எந்த ஒரு regulator-ஆலும் முடிவு-முதல்-முடிவு பார்க்க முடியாத reuse சங்கிலிகள்" மூலக் காரணத்துக்குத் திரும்புது.

**6. Report 3 (REUSE) — New, Valuation Update, Termination reports மூணும் Goldman Sachs collateral-ஐ என்ன செய்தாலும் உருவாகும், ஏன்னா அவை SFT-ஐயே பின்தொடருது, அதுக்கப்புறம் collateral-க்கு என்ன நடக்குதுங்கறதை இல்ல.**

Section 8-ஓட worked example Report 3-ஐ வெளிப்படையா "(தூண்டப்பட்டா)"ன்னு குறிக்குது — SFT-ஓட சொந்த lifecycle events-ஐ இல்ல, collateral-ஓட counterparty-ஓட downstream நடத்தையை சார்ந்த நாலு-report தடத்துல ஒரே report.

**7. ESMA-ஓட (அல்லது UK SFTR-க்கு FCA-ஓட) technical standards (RTS/ITS) — EUR-Lex regulation text சட்ட scope-ஐயும் கடமைகளையும் நிறுவுது, ஆனா field-by-field reportable data பட்டியல் technical standards-ல இருக்கு; அவை அடிப்படை regulation-ஐ விட சுயாதீனமா, அடிக்கடி திருத்தப்படுது.**

Section 9-ஓட takeaway இந்த மூன்று-வழிப் பிரிவை நேரடியா சொல்லுது: scope/கடமைகளுக்கு regulation சட்ட மூலம், சரியான field பட்டியல்களுக்கும் validation விதிகளுக்கும் technical standards, உண்மையான message வடிவத்துக்கு Trade Repository-ஓட சொந்த reporting manual — மூணு வேறு build கேள்விகளுக்கு மூணு வேறு ஆவணங்கள்.

**8. எப்பவும் எதிர் பக்கங்கள்ல இருந்து பார்க்கப்பட்ட ஒரே trade — "repo"-உம் "reverse repo"-வும் வேறு products இல்ல. Bank Of Toronto-வும் Goldman Sachs-உம் ரெண்டும் ஒரே UTI-க்கு `Type of SFT = REPO`-ஐ report பண்ணுது; collateral-கொடுப்பவர்/cash-கடன்வாங்குபவர் vs. collateral-பெறுபவர்/cash-கடன்கொடுப்பவர் பாத்திரங்கள் மட்டும் அவர்களின் ரெண்டு reports-க்கு இடையில வேறுபடுது.**

Section 2a இதை நேரடியா சொல்லுது: GBP 19,845,000 cash, GBP 20,250,000 collateral, 5.10% rate ரெண்டு பக்கத்துலயும் ஒரே எண்கள் — "repo" vs. "reverse repo" label ஒரு குறிப்பிட்ட counterparty அதே transaction-ஓட எந்த முனையில உட்கார்ந்ததுன்னு மட்டும் விவரிக்குது.

**9. `SLEB` = Securities Lending and Borrowing (பேச்சுவழக்குல "sec lending" அல்லது "stock loan"-னும்). Section 2c-ஓட Custodian Trust Co. (Maple Leaf Pension Fund-ஓட agent-ஆ) → Bank Of Toronto AAPL share loan (USD 22,500,000 market value, USD 22,950,000 collateral, USD 5,625 fee) `Type of SFT = SLEB`-ஓட report பண்ணப்படுது.**

Section 2-ஓட முக்கிய table-க்கு உடனே பிறகான term-note table, `SLEB`-ஐ இந்த product குடும்பத்துக்கான உண்மையான SFTR "Type of SFT" enumeration மதிப்பா பட்டியலிடுது — அதே code Bank Of Toronto-ஓட (borrower), Custodian Trust Co.-ஓட (agent lender) ரெண்டு reports-லயும் தோன்றுது; Maple Leaf Pension Fund-ஓட பொருளாதார ஆர்வம் Section 2c விவரிக்கும் தனி agency உறவின் கீழ கடத்தப்படுது.

**10. ஆமா — LTV = 5,000,000 / 8,500,000 = 58.8%, 65% maintenance வரம்புக்கு கீழ, அதனால margin call தூண்டாது.**

Section 6c-ஓட worked formula (Loan / Portfolio Value = LTV) நேரடியா பொருந்துது: 58.8% 65% வரம்புக்கு கீழ இருக்கு, அதை மீறின உண்மையான worked example-ஓட 66.7%-ஐ (USD 7,500,000 portfolio-ல இருந்து) போல இல்ல. வரம்பு ஒரு படிப்படியான scale இல்ல, கடினமான cutoff என்பதை இது காட்டுது — அதுக்கு கீழ நிற்கும் portfolio வீழ்ச்சி, Section 6c-ஓட stated விதியின் கீழ எதையும் தூண்டாது.

**11. ஏன்னா reuse, reusing counterparty-ஓட முழு book முழுக்க அந்த collateral type/currency-க்கு aggregate எண்ணா report பண்ணப்படுது — trade-by-trade இல்ல — ஏன்னா re-pledge பண்ணப்படும் collateral-ஓட குறிப்பிட்ட units, Goldman Sachs-ஓட பொது collateral pool-க்குள்ள திரும்பிட்டா, அவை எந்த அசல் SFT-ல இருந்து வந்தன என்பதன் அடிப்படையில பிரிக்கப்படுறதில்ல.**

Section 6-ஓட report-table வரையறை ஏற்கனவே reuse data "ஒரு reporting counterparty-ஓட முழு book முழுக்க aggregate (trade-by-trade இல்ல)"ன்னு சொல்லுது; Section 6d-ஓட takeaway ஏன் அது முக்கியம்னு விளக்குது: ஒரு collateral *வகை* அமைப்பு முழுக்க reuse சங்கிலிகள் வழியா எவ்வளவு சுழலுதுங்கறதுல பார்வை தான் முழு நோக்கம், ஒரு குறிப்பிட்ட மூல trade-ஓட UTI-க்கு துல்லியமான இணைப்பு இல்ல.

**12. ஏன்னா Table 3 (CCP margin) SFT centrally cleared ஆகும்போது மட்டும் பொருந்தும், Table 4 (reuse/reinvestment/funding) ஒரு counterparty-ஓட முழு book முழுக்க aggregate-ஆ report பண்ணப்படுது (Section 6d) — ரெண்டுல ஏதாவது ஒண்ணை ஒவ்வொரு Table 1+2 submission-க்குள்ளயும் மூட்டை கட்டினா, பெரும்பாலான trades-க்கு பொருந்தாத fields-ஐ (Table 3) அல்லது per-UTI கட்டமைப்புக்கே பொருந்தாத fields-ஐ (Table 4) report பண்ணுவதுன்னு அர்த்தம்.**

Section 10-ஓட table/message பிரிவு காட்டுது: Tables 1, 2 ரெண்டும் SFT-ஐயும் அதன் parties-ஐயும் விவரிச்சு, ஒவ்வொரு lifecycle நிலையிலும் ஒண்ணா submit ஆகுது — ஆனா Table 3 CCP-cleared SFTs துணைக்கணத்துக்கு மட்டும் இருக்கு, Table 4 (Section 6d-ஓட takeaway படி) ஒரு தனி UTI-யோடேயே இணைக்கப்படலை. ESMA ஒவ்வொரு `auth.052` report-ஐயும் பெரும்பாலும் பொருந்தாத fields-ஆல பேடிங் பண்ணாம, அவற்றை தங்கள் சொந்த messages-ஆ (`auth.070`, `auth.071`) பிரிச்சு பொருத்தமானப்போ மட்டும் submit பண்ணுது.

**13. Table 1 — "agent lender" என்பது SFT-ல யார் சம்பந்தப்பட்டிருக்காங்கங்கறதைப் பத்தின கேள்வி; அதை Section 10d-ஓட Q1 நேரடியா Table 1-ஓட Counterparty Data-க்கு route பண்ணுது.**

Section 2c-ஓட Custodian Trust Co. (Maple Leaf Pension Fund-ஓட agent lender) உறுதியான உதாரணம்: Bank Of Toronto-ஓட Table 1-ல தோன்றுவது Maple Leaf Pension Fund-ஓட LEI இல்ல, Custodian Trust Co.-ஓட LEI — சரியா Section 10b, Table 1-ஓட 18 fields-ல பட்டியலிடும் "agent lender" field.

**14. ஒரு data-mapping பிரச்சனை, உண்மையான trade தகராறு இல்ல — Section 10e-ஓட உதாரணம் UTI, Principal, Maturity மூணும் ரெண்டு counterparties-ஓட reports-க்கு இடையில சுத்தமா ஒத்துப்போகுது, rate field மட்டும் உடையுதுன்னு காட்டுது.**

அடிப்படை trade தானே உண்மையிலயே சர்ச்சைக்குரியதா இருந்தா, ஒரு field மட்டுமில்ல, அதிக fields ஒத்துப்போகாது. மற்ற எல்லாம் pair ஆக ஒரே field உடையறது, ஒரு பக்கத்துல source-system enrichment/mapping bug-ஓட classic அடையாளம் — சரியா Section 10e-ஓட takeaway "trade பிரச்சனை" vs "mapping பிரச்சனை"க்கு இடையில வரையற operational வேறுபாடு.

**15. EUR 54,347,826 — Section 11b-ஓட reverse formula-வை பயன்படுத்தி, Required Collateral = 50,000,000 / (1 − 0.08) = 50,000,000 / 0.92 ≈ EUR 54,347,826.**

Section 11b-ஓட "கவனிக்க வேண்டிய பொறி" குறிப்புப்படி, EUR 54,000,000 (cash தொகையோட 108%) என்ற அப்பாவித்தனமான ஊகம் trade-ஐ under-collateralize பண்ணும், ஏன்னா haircut cash தொகைக்கு எதிரா இல்ல, collateral-ஓட மதிப்புக்கு எதிரா வரையறுக்கப்படுது — Section 11b-ஓட சொந்த EUR 100,000,000 / 5% worked example-ஓட அதே reverse-formula mechanic, இந்தக் கேள்வியோட சொந்த எண்களோட.
