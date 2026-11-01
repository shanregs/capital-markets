# Module 12 — Collateral & Margin (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Cross-cutting module** — [Module 10](../docs/1-capital-markets/module-10-valuation.html) (Valuation), [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) (Risk & Exposure) திறந்த சங்கிலியை முடிக்குது: Trade → Valuation → Exposure → Margin → Collateral. எட்டு-பாக asset-class pattern-ஐ சுத்தி அமைக்கப்படலை.
> **மூலம்:** English Module 12-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Collateral & Margin-ன்னா என்ன? — சங்கிலியை முடிக்கிறது

**Definition:** Modules 10, 11 ஒரு சங்கிலி எண்களை — Market Value, அப்புறம் Exposure — கட்டின; அவை எல்லாம் risk-ஐ விவரிக்குது, ஆனா அதைப் பத்தி *எதுவும் செய்யறதில்ல*. Collateral = அந்த risk-ஐ cover பண்ண post பண்ணப்படற உண்மையான asset (cash அல்லது securities); Margin = எவ்வளவு collateral தேவைன்னு கணக்கிட்டு அதை கோரும் பரந்த process. இந்த module curriculum தானே வகுக்கற சங்கிலியை முடிக்குது:

```text
Trade  ->  Valuation (Module 10)  ->  Exposure (Module 11)  ->  Margin  ->  Collateral
```

| Term | அர்த்தம் |
|---|---|
| Collateral | ஒரு தரப்பு மற்ற தரப்போட exposure-ஐ cover பண்ண post பண்ற உண்மையான asset (cash அல்லது securities) |
| Margin | பரந்த process/தொகை — எவ்வளவு collateral தேவைன்னு கணக்கிடறது |
| Initial Margin (IM) | ஒரு position உங்களுக்கு எதிரா நகரறதுக்கு முன்னாடியே, Potential Future Exposure-க்கு ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §2) அளவிடப்பட்டு முன்கூட்டியே post பண்ற collateral |
| Variation Margin (VM) | *இன்னிக்கான* MTM நகர்வை ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §1) cover பண்ண அவ்வப்போது பரிமாறப்படற collateral |
| Cash Collateral | நேரடியா post பண்ணப்படற currency — valuation adjustment தேவையில்ல, அதன் மதிப்பு அதன் face தொகையேதான் |
| Securities Collateral | Cash-க்குப் பதிலா post பண்ணப்படற bond அல்லது வேறு security — Market Value-வை Collateral Value-ஆ மாத்த Haircut (Section 3) தேவை |
| Eligible Collateral | ஒரு CSA ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7) collateral-ஆ அனுமதிக்கற குறிப்பிட்ட asset வகைகளின் பட்டியல் — எல்லா security-உம் தகுதியுடையது இல்ல |
| Haircut | ஒரு security collateral-ஆ பயன்படுத்தப்படும்போது அதன் Market Value-வுக்கு பொருந்தும் சதவீத குறைப்பு — collateral தானே, அதை liquidate பண்ண முடியுமுன்னாடி மதிப்பு இழக்கும் அபாயத்துக்கு ஒரு தடுப்பு |

```text
THE FULL CHAIN, WITH REAL NUMBERS — continuing Module 11 §3's netting set
========================================================================

  Module 10: Trade 1 (IRS) MTM = +CAD 1,500,000
             Trade 2 (FX Fwd) MTM = +CAD 900,000
             Trade 3 (CDS) MTM = -CAD 400,000
             (Gross Positive MTM = CAD 2,400,000)

  Module 11 §3: NET EXPOSURE (after ISDA netting) = CAD 2,000,000

  Module 12 (this module):
             Collateral Held (Section 05 explains the exact number)
               = CAD 1,800,000
             Remaining (uncollateralized) Exposure
               = 2,000,000 - 1,800,000 = CAD 200,000

  -> Four modules, one continuous worked example: Bank Of Toronto's
     real, final risk to Goldman Sachs after EVERYTHING -- netting
     AND collateral -- is CAD 200,000, not the CAD 2,400,000 the
     raw MTM numbers alone would suggest.
```

**Takeaway:** இந்தச் சங்கிலியில ஒவ்வொரு எண்ணும் அதுக்கு முந்தையதை விடச் சிறியது, ஒவ்வொரு குறைப்பும் முற்றிலும் வேறு காரணத்துக்காக நடக்குது — netting ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §3) ஒரு சட்ட mechanism; collateral உண்மையில கை மாறும் ஒரு physical asset. MTM-ல இருந்து நேரா "வங்கி நல்லா இருக்கு"ன்னு தாவும் risk report, அந்த முடிவை உண்மையாக்கற வேலையையே மறைச்சிடுது.

### கூடுதல் உதாரணம் — தமிழ் வாழ்க்கை ஒப்புமை: வீட்டு வாடகை Advance & மாதாந்திர ஈடு

| Derivative ஒப்புமை | வாடகை ஒப்புமை |
|---|---|
| Initial Margin (ஆரம்பத்துல ஒரு தடவை) | வீடு எடுக்கும்போது கொடுக்கற **advance / deposit** — சேதம் வந்தா ஈடுகட்ட |
| Variation Margin (தினசரி) | மாதாமாதம் **மின்சாரக் கட்டணம் உண்மையான meter reading-ப்படி** கொடுக்கறது — நடப்பு நிலையை சரிக்கட்ட |
| Threshold | "சின்ன பாக்கி 500 ரூபாய் வரை பரவாயில்ல, தனியா வசூலிக்க வேண்டாம்"ன்னு ஒத்துக்கறது |
| Haircut | வீட்டுக்காரர் "நகையை அடமானமா வைக்கறீங்களா? அதன் மதிப்புல 80% தான் கணக்கு"ன்னு சொல்றது |

---

## 2. Initial Margin vs Variation Margin — Deep Dive

**Definition:** [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6 ஏற்கனவே CAD 100,000,000 IRS மேல விளக்க எண்களோட IM, VM-ஐ அறிமுகப்படுத்திச்சு; [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §7 bilateral IM-க்குப் பின்னாலுள்ள model-ஆ SIMM-ஐ அறிமுகப்படுத்தியது. இந்த section ரெண்டையும் உண்மையில எது post பண்ணப்படுது, IM-உம் VM-உம் ஏன் முற்றிலும் வேறு ரெண்டு தோல்வி முறைகளை எதிர்த்துப் பாதுகாக்குதுங்கற mechanics-ஓட இணைக்குது.

```text
IM PROTECTS AGAINST THE FUTURE; VM PROTECTS AGAINST THE PRESENT
========================================================================

  INITIAL MARGIN (IM) — Module 3 §6's worked example:
    100,000,000 x 2.00% (illustrative rate for a 5Y IRS) = CAD 2,000,000
    Posted ONCE, at trade inception, by BOTH sides independently.
    Purpose: if the counterparty defaults TOMORROW, before any VM
    has even had a chance to react, IM is the buffer that covers
    the loss during the time it takes to close out and replace the
    position (the "Margin Period of Risk," typically 10 business
    days for bilateral trades under UMR).

  VARIATION MARGIN (VM) — Module 3 §6's worked example:
    Next business day, MTM shifts CAD 180,000 in Bank Of Toronto's
    favor -> Goldman Sachs pays CAD 180,000 VM to Bank Of Toronto
    Purpose: keeps CURRENT Exposure (Module 11 §1) at effectively
    ZERO on an ongoing basis, by transferring exactly today's MTM
    movement, every business day, for the life of the trade.

  -> IM is sized off Module 11 §2's PFE (a FORWARD-LOOKING
     estimate); VM is sized off Module 10 §1's MTM (a CURRENT
     snapshot). Two completely different questions, two completely
     different collateral flows, calculated on two different
     schedules (IM once at inception plus periodic recalculation;
     VM daily).
```

**Takeaway:** ஒரு position-க்கு கணிசமான IM post பண்ணப்பட்டிருந்தும், மறுநாளே பெரிய VM call உருவாகலாம் — IM போதுமானது VM தற்போதையதுங்கறதை பத்தி எதுவும் சொல்லாது, ஏன்னா IM *எதிர்கால* default scenario-க்கு எதிரா பாதுகாக்குது, VM *இன்னிக்கான* மதிப்பை பின்தொடருது. இவை ரெண்டையும் குழப்பிக்கறது, [Module 10](../docs/1-capital-markets/module-10-valuation.html)/11-ஓட MTM-vs-Exposure குழப்பத்தின் collateral-management பதிப்பு, சங்கிலியில ஒரு படி கீழ.

---

## 3. Collateral வகைகளும் Haircuts-உம்

**Definition:** எல்லா collateral-உம் சமமில்ல. Cash-க்கு adjustment தேவையில்ல; securities-க்கு Haircut தேவை, ஏன்னா collateral post பண்ணப்படற தருணத்துக்கும் அதை liquidate பண்ண வேண்டியிருக்கலாம்ங்கற தருணத்துக்கும் (ஒரு default scenario) நடுவுல அதன் சொந்த சந்தை மதிப்பு விழலாம்.

```text
SECURITIES COLLATERAL — Canada 5% Bond 2035 posted as collateral
========================================================================

  Market Value (Module 5 §4a, Module 10 §1): CAD 9,850,000
  Haircut (illustrative, for a high-quality government bond): 2%

  Collateral Value = Market Value x (1 - Haircut)
                    = 9,850,000 x 0.98
                    = CAD 9,653,000

  -> The bond's Collateral Value (CAD 9,653,000) is LOWER than its
     Market Value (CAD 9,850,000) -- the CAD 197,000 gap is the
     buffer against the bond itself losing value before it can be
     sold to cover a default. A riskier, less liquid security (a
     corporate bond, or equity) would carry a MUCH larger haircut
     than a government bond -- Eligible Collateral schedules
     (Section 01) typically list a specific haircut percentage per
     asset type, not one flat rate for everything.
```

**Takeaway:** "Collateral Value" ஒரு உண்மையிலயே தனி field, "Market Value"-ல இருந்து — [Module 10](../docs/1-capital-markets/module-10-valuation.html) §1 Price, Market Value, Fair Value இடையில நிறுவின அதே வகை வேறுபாடு; ஒரு security-க்கு ஒரே எண்ணை மட்டும் store பண்ணி valuation, collateral ரெண்டு நோக்கத்துக்கும் மறுபயன்படுத்தும் reporting system, post பண்ணப்பட்ட security உண்மையில வழங்கும் பாதுகாப்பின் அளவை அமைதியா மிகைப்படுத்தும்.

### கூடுதல் உதாரணம் — வெவ்வேறு collateral, வெவ்வேறு haircut (எண்களோட)

| Collateral | Market Value (CAD) | Haircut | Collateral Value (CAD) = MV × (1 − Haircut) |
|---|---|---|---|
| Cash (CAD) | 5,000,000 | 0% | 5,000,000 |
| Canada 5% Bond 2035 | 9,850,000 | 2% | 9,653,000 |
| Bank Of Toronto 4.5% Bond 2030 (corporate) | 12,210,000 | 8% | 11,233,200 |
| Equity (Coca-Cola shares) | 684,000 | 15% | 581,400 |

அதே CAD 5,000,000 exposure-ஐ cover பண்ண, cash-ல 5,000,000 போதும்; equity-ல இருந்து cover பண்ண CAD 5,882,353 market value-வுள்ள shares தேவை (5,000,000 ÷ 0.85). Haircut அதிகம் = அதிக collateral தேவை.

---

## 4. Collateral நகர்வு Mechanics — Pledge, Custodian & Tri-Party

**Definition:** Collateral எங்கயாவது physically நகர வேண்டும், ரெண்டு counterparties-உம் ஒப்பந்தத்தை நம்பும் அளவுக்கு நடுநிலையான ஒருவரால வெச்சிருக்கப்படணும்.

```text
PLEDGE vs RECEIVE, AND WHO HOLDS IT
========================================================================

  PLEDGE: Bank Of Toronto POSTS (pledges) collateral TO Goldman
          Sachs, because Bank Of Toronto owes the exposure.
  RECEIVE: Goldman Sachs RECEIVES that same collateral.
          (On the netting set from Section 01, it's actually
          Goldman Sachs who owes Bank Of Toronto CAD 2,000,000 net
          -- so in THIS relationship, Goldman Sachs pledges and
          Bank Of Toronto receives.)

  CUSTODIAN vs TRI-PARTY AGENT -- two different operating models:

  Bilateral custody: Bank Of Toronto's own custodian (Module 1 §7)
    holds collateral received directly, and Bank Of Toronto's own
    Middle Office values it, applies haircuts, and tracks
    Eligible Collateral rules manually against the CSA.

  Tri-party arrangement: a neutral THIRD PARTY (e.g. Euroclear,
    Clearstream, or a custodian bank acting as tri-party agent)
    sits between Bank Of Toronto and Goldman Sachs. Both parties
    agree the Eligible Collateral schedule and haircuts UP FRONT;
    the tri-party agent then automatically selects, values,
    applies haircuts to, and even SUBSTITUTES eligible securities
    from a pool -- neither Bank Of Toronto nor Goldman Sachs has
    to manually pick and track individual securities day to day.

  COLLATERAL SUBSTITUTION: if Bank Of Toronto wants to sell a bond
    it's currently using as posted collateral (e.g. to fund
    something else), it can SUBSTITUTE a different eligible asset
    of equivalent Collateral Value -- a tri-party agent handles
    this automatically; bilateral custody requires manual
    negotiation and agreement from the receiving party first.
```

**Takeaway:** Tri-party ஏற்பாடுகள் இருக்கறதே, Section 3-ஓட haircut கணக்கு, Eligible Collateral சரிபார்ப்பு ஆகிய இயக்கச் சுமையை ரெண்டு trading counterparties-ல இருந்தும் நீக்கத்தான் — ஒரு உண்மையிலயே முக்கியமான IT/operations வேறுபாடு, ஏன்னா tri-party custody பயன்படுத்தும் firm-க்கு (tri-party agent-உடன்/இடமிருந்து ஒரு feed) bilateral collateral-ஐ உள்ளுக்குள்ளயே நிர்வகிக்கும் firm-ஐ விட அடிப்படையில வேறு system integration தேவைகள்.

---

## 5. மீதமிருக்கும் Exposure — சங்கிலியை முடிக்கிறது

**Definition:** Collateral அரிதா exposure-ஐ சரியா பூஜ்யத்துக்கு cover பண்ணும் — ஒரு **Threshold** (எந்த collateral-உம் தேவைப்படுவதற்கு முன்னாடி, ஒரு counterparty collateral இல்லாம சுமக்கச் சம்மதிக்கற exposure தொகை) ஒரு நிலையான CSA term, இந்த module-ஓட தொடக்க worked example-ஐ விளக்குறதும் அதுதான்.

```text
WHY CAD 200,000 OF EXPOSURE REMAINS UNCOLLATERALIZED
========================================================================

  Net Exposure to Goldman Sachs (Module 11 §3): CAD 2,000,000
  CSA Threshold agreed between Bank Of Toronto and Goldman Sachs:
    CAD 200,000 (the amount of exposure Bank Of Toronto has agreed
    to simply ACCEPT, uncollateralized, as the cost of not having
    to call/post tiny amounts of collateral for every small MTM move)

  Required Collateral = Net Exposure - Threshold
                       = 2,000,000 - 200,000
                       = CAD 1,800,000  <- exactly Section 01's
                                            "Collateral Held" figure

  Remaining (uncollateralized) Exposure = Net Exposure - Collateral Held
                                          = 2,000,000 - 1,800,000
                                          = CAD 200,000  <- exactly
                                            equal to the Threshold

  -> This isn't a coincidence -- collateral is called to bring
     Exposure DOWN TO the Threshold, never below it. A Minimum
     Transfer Amount (MTA) works alongside Threshold to prevent
     even that Threshold-sized gap from triggering constant tiny
     collateral movements as MTM wobbles by small amounts day to day.
```

**Takeaway:** மீதமிருக்கும் Exposure ஒரு system கோளாறோ under-collateralized தவறோ இல்ல — CSA-வில் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7) கையெழுத்து போட்டப்போ ரெண்டு counterparties-உம் ஒத்துக்கிட்ட Threshold-ஓட வேண்டுமென்றே பேசி முடிவுசெய்யப்பட்ட விளைவு. "CAD 200,000 exposure-க்கு collateral இல்ல"ன்னு flag பண்ணும் risk report, அந்த இடைவெளியை பிரச்சனையா நடத்தறதுக்கு முன்னாடி CSA-ஓட Threshold term-ஐ சரிபார்க்கணும்.

### கூடுதல் உதாரணம் — Threshold + MTA சேர்ந்து எப்படி வேலை செய்யுது

CSA terms: Threshold = CAD 200,000; Minimum Transfer Amount (MTA) = CAD 50,000.

| நாள் | Net Exposure (CAD) | Required Collateral = Exposure − Threshold | ஏற்கனவே வெச்சிருக்கறது | வித்தியாசம் | Call? (MTA 50,000-ஐ கடக்குதா) |
|---|---|---|---|---|---|
| Day 1 | 2,000,000 | 1,800,000 | 1,800,000 | 0 | இல்ல |
| Day 2 | 2,030,000 | 1,830,000 | 1,800,000 | +30,000 | இல்ல — MTA-க்கு கீழ (தினமும் சின்ன சின்ன நகர்வுகளை தவிர்க்க) |
| Day 3 | 2,090,000 | 1,890,000 | 1,800,000 | +90,000 | ஆமா — CAD 90,000 call |
| Day 4 | 1,950,000 | 1,750,000 | 1,890,000 | −140,000 | ஆமா — CAD 140,000 திருப்பிக் கொடுக்கணும் |

MTA இல்லாட்டி Day 2-ல ஒரு சின்ன 30,000 call-உம் அதன் settlement-உம் நடந்திருக்கும்; Threshold இல்லாட்டி exposure முழுசும் collateralize ஆகியிருக்கும்.

---

## 6. Cross-Asset-Class Margin Reference

**Definition:** ஒரு position IM/VM தேவையை உருவாக்குதான்னே, அது எப்படி clear ஆகுதுங்கறதை முழுக்க சார்ந்தது ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §5-ஓட bilateral-vs-CCP பிளவு), இங்க margin கேள்விக்கு குறிப்பா மறுபயன்படுத்தப்பட்டது.

| Cash / Derivative / SFT | Product Category | IM Required? | VM Required? | Basis |
|---|---|---|---|---|
| CASH | Cash Equity / Bonds (settled DvP) | No | No | Settles outright via DvP ([Module 1](../docs/1-capital-markets/module-01-fundamentals.md) §7) — no ongoing position risk to margin against |
| DERIVATIVE | Exchange-traded Futures / Listed Options | Yes (CCP's own model) | Yes, **daily** | CCP's own initial and variation margin methodology ([Module 15](../docs/1-capital-markets/module-15-listed-derivatives.md) §5) |
| DERIVATIVE | Centrally-cleared standardized IRS / CDS Index | Yes (CCP's own model) | Yes, **daily** | Same CCP-driven mechanic as Futures ([Module 8](../docs/1-capital-markets/module-08-rates.md) §5, [Module 9](../docs/1-capital-markets/module-09-credit.md) §7) |
| DERIVATIVE | Bilateral OTC derivatives **above** UMR thresholds ([Module 11](../docs/1-capital-markets/module-11-risk-exposure.md) §8) | Yes, via **SIMM** (Section 02, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.md) §7) | Yes | Bilaterally agreed CSA ([Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §7), reused throughout this module |
| DERIVATIVE | Bilateral OTC derivatives **below** UMR thresholds | No | Yes (VM-only CSA is common even below the IM threshold) | CSA terms, but IM specifically not mandated |
| SFT | Repo / Securities Lending | N/A (uses **haircuts on the collateral itself**, Section 03's mechanic, rather than separate IM/VM) | N/A | The security/cash exchanged IS the collateral ([Module 5](../docs/1-capital-markets/module-05-fixed-income.md) §3) |

**Takeaway:** Repo, IM/VM framework-க்கே பொருந்தாதுங்கறதை கவனிங்க — [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3 ஏற்கனவே repo-வை, security தானே collateral-ஆ இருக்கற secured loan-ஆ நிறுவியது; அதனால Section 3-ஓட haircut கருத்து, ஒரு derivative மேல அடுக்கப்பட்ட தனி margin process-ஆ இல்லாம, loan structure-க்கே நேரடியா பொருந்துது.

---

## 7. IT/Reporting பார்வை — Margin Call Workflow, Static vs Daily Fields, Disputes

**Definition:** ஒரு margin call ஒரே நிகழ்வு இல்ல — அது பல-படி workflow; field வகைகளை (static CSA terms vs தினசரி கணக்கிடப்படும் எண்கள்) சரியா பெறுவதுதான் அந்த workflow-வை நம்பகமானதாக்குது.

```text
MARGIN CALL WORKFLOW — STATE BY STATE
========================================================================

  1. CALLED    — Bank Of Toronto's system computes today's Net
                 Exposure (Module 11 §3) and Threshold gap, issues
                 a formal margin call to Goldman Sachs for the
                 shortfall

  2. AGREED / DISPUTED — Goldman Sachs independently recomputes the
                 SAME exposure using its OWN valuation (Module 10
                 §7's IPV concept applies directly here) -- if the
                 two numbers match within an agreed tolerance,
                 AGREED; if not, DISPUTED, and the gap gets
                 investigated before any collateral moves

  3. SETTLED   — once agreed, collateral actually moves (Pledge/
                 Receive, Section 04) via the custodian or
                 tri-party agent, and both parties' records update
```

**Static vs daily fields:** Threshold-உம் Minimum Transfer Amount-உம் (Section 05) **static CSA reference data** — ஒரு தடவை பேசி முடிவு செய்யப்பட்டு, அரிதாவே மாற்றப்படுது. Net Exposure, Collateral Held, Remaining Exposure **தினசரி கணக்கிடப்படும் fields** — ஒவ்வொரு business day-யும் அந்த நாளின் MTM-ல இருந்து மறுகணக்கிடப்படுது. Threshold-ஐ தினசரி மறுகணக்கிட வேண்டியதா நடத்தும் (அல்லது, இன்னும் மோசமா, இன்னிக்கு கணக்கிட்ட exposure-ஐ static reference data போல நடத்தும்) collateral management system, கட்டமைப்பு ரீதியா தவறான margin calls-ஐ உருவாக்கும்.

**Disputes சாதாரணம், விதிவிலக்கு இல்ல:** ரெண்டு counterparties-உம் அதே trades-ஐ சுயாதீனமா value பண்றதாலும் (Section 7-ஓட AGREED/DISPUTED படி), [Module 10](../docs/1-capital-markets/module-10-valuation.html) §6 Level 2/3 valuations உண்மையான model judgment-ஐ உள்ளடக்குதுன்னு ஏற்கனவே நிறுவியதாலும், ரெண்டு firms-ஓட exposure எண்களுக்கு இடையில சின்ன வேறுபாடுகள் வழக்கமானவை, குறிப்பா குறைவான liquid, Model-Based positions-க்கு — ஒரு முதிர்ந்த collateral operation-க்கு, ரெண்டு தரப்பும் எப்பவும் ஒத்துக்கும்னு கருதாம, வரையறுக்கப்பட்ட dispute-resolution process உண்டு.

**Takeaway:** margin call workflow என்பது [Module 10](../docs/1-capital-markets/module-10-valuation.html)-ஓட valuation-observability கருத்துகள் (Quote-Based vs Model-Based, Fair Value Levels, IPV) அருவமான accounting கவலைகளா இருப்பதை நிறுத்தி, தங்கள் சொந்த எண்ணை சுயாதீனமா கணக்கிடற ரெண்டு counterparties இடையிலான இயக்க, அன்றாட reconciliation பிரச்சனையா மாறற இடம்.

---

## 8. Regulatory Perspective — UMR Phase-In, BCBS-IOSCO, SFTR & Basel LCR

| Regulation / Body | Collateral & Margin-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **BCBS-IOSCO Margin Framework** (UMR-ஓட parent, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §8) | 2016-க்கும் 2022-க்கும் இடையில ஆறு அலைகளாக ("Phase 1" முதல் "Phase 6" வரை) படிப்படியா அமல்படுத்தப்பட்டது; ஒவ்வொரு firm-ஓட non-cleared derivatives-ஓட **Average Aggregate Notional Amount (AANA)**-ஐ அடிப்படையா வெச்சு — மிகப்பெரிய dealers (அதிக AANA) முதல்ல scope-க்குள்ள கொண்டு வரப்பட்டாங்க, பிந்தைய phases-ல சிறிய firms படிப்படியா; இந்த module-ஓட IM தேவை (Section 02) தங்கள் பொருந்தும் phase-ன் கீழ உண்மையில scope-ல உள்ள firms-க்கு மட்டும்தான் பொருந்தும் |
| **SFTR** | ஏற்கனவே ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §08) குறிப்பா repo/securities lending-ஐ உள்ளடக்குதுன்னு நிறுவப்பட்டது — Section 6-ஓட table, repo தனி IM/VM process-க்குப் பதிலா collateral haircuts-ஐ நேரடியா பயன்படுத்துதுன்னு flag பண்ணுது; SFTR-ஓட reporting கடமை சரியா அந்த collateral-ஐயும் (என்ன post பண்ணப்பட்டது, என்ன haircut பொருந்தியது) loan-ஓட சொந்த economics-உடன் சேர்த்து உள்ளடக்குது |
| **Basel LCR (Liquidity Coverage Ratio)** — இந்த module-க்கு புதுசு | ஒரு வங்கி 30-நாள் stress scenario-வை தாங்க போதுமான High-Quality Liquid Assets (HQLA) வெச்சிருக்கணும்; collateral post பண்றது (Section 01-ஓட சங்கிலி) வங்கியோட சொந்த HQLA-வை கட்டிப்போடுது, collateral பெறுவது (அது தானே HQLA-தகுதியுடையதா இருந்தா) பெறும் வங்கியோட LCR நிலையை மேம்படுத்தலாம் — collateral management-உம் liquidity management-உம் நேரடியா இணைந்த balance-sheet கவலைகள், தனித்தனி துறைகள் இல்ல |
| **EMIR / Dodd-Frank** | ரெண்டு regimes-ஓட Trade Repository reporting-உம் ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07, [Module 10](../docs/1-capital-markets/module-10-valuation.html) §08, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §08) collateral-குறிப்பான fields-ஐ உள்ளடக்குது — post பண்ணின collateral, பெற்ற collateral, trade ஒரு bilateral CSA-க்கு உட்பட்டதா centrally cleared-ஆன்னு; trade-ஓட economics, valuation மட்டுமில்ல |
| **CSDR** (Central Securities Depositories Regulation, EU) | settlement-discipline விதிகளை (கட்டாய buy-ins, settlement fails-க்கு cash penalties) வகுக்குது; இவை EU CSDs வழியா settle ஆகற securities-collateral நகர்வுகளுக்கும் (Section 04) நேரடியா பொருந்தும், outright securities trades-க்கு மட்டுமில்ல |

**Takeaway:** UMR-ஓட படிப்படியான அமலாக்கம், Section 2-ஓட Initial Margin தேவை ஒரு குறிப்பிட்ட bilateral உறவுக்கு பொருந்துதா இல்லையாங்கறதை தீர்மானிக்கும் regulatory mechanism — Bank Of Toronto-வுடன் trade பண்ற ஒரு சிறிய firm-க்கு, எந்தத் தரப்போட AANA-வும் பொருந்தும் phase-ஓட வரம்பைக் கடக்கலைன்னா, IM கடமையே இல்லாம இருக்கலாம்; IM scope-ல இருக்கோ இல்லையோ, அதே VM mechanics (Section 5-ஓட Threshold-இயக்கப்படும் மீதமிருக்கும் Exposure-உம்) இன்னும் பொருந்தும்.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | Goldman Sachs-ல இருந்து CAD 1,800,000 collateral-ஓட net recipient (Section 05); Section 03-ஓட விளக்க உதாரணத்துல Canada 5% Bond 2035-ஐ collateral-ஆ post பண்றவர் |
| Goldman Sachs | [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §3-ஓட netting set-ஓட கீழ collateral-ஓட net poster |
| Euroclear / Clearstream | தானியங்கி collateral தேர்வு, valuation, substitution-ஐ கையாளும் விளக்க tri-party agents (Section 04) |
| ISDA | CSA (Section 01), SIMM (Section 02, [Module 11](../docs/1-capital-markets/module-11-risk-exposure.html) §7) ஆகியவற்றின் ஆசிரியர் |
| BCBS-IOSCO | படிப்படியான UMR அமலாக்கத்துக்குப் பின்னாலுள்ள கூட்டு standard-setter (Section 08) |

---

## Recap Quiz — Numbers Edition

1.  Section 05-ஓட worked example-ல, Bank Of Toronto-க்கும் Goldman Sachs-க்கும் இடையிலான CSA Threshold CAD 200,000-க்குப் பதிலா CAD 500,000 ஆக இருந்திருந்தா, Net Exposure CAD 2,000,000-லயே இருக்க, Required Collateral-உம் Remaining Exposure-உம் ஒவ்வொண்ணும் என்ன?
2.  Section 03-ஓட Haircut formula-வை பயன்படுத்தி, Canada 5% Bond 2035-க்கு 2%-க்குப் பதிலா 5% haircut (ஆபத்தான collateral schedule) இருந்தா, அதன் Collateral Value என்ன?
3.  IM-உம் VM-உம் "முற்றிலும் வேறு ரெண்டு தோல்வி முறைகளை எதிர்த்துப் பாதுகாக்குது"ன்னு Section 02 ஏன் சொல்லுது — ஒரு firm-க்கு வலுவான VM இருந்து IM சுத்தமா இல்லாட்டி குறிப்பா என்ன தப்பாப் போகலாம்?
4.  Bank Of Toronto-க்கும் Goldman Sachs-க்கும் இடையிலான ஒரு margin call DISPUTED status-க்கு போகுது. Section 07-ஐ பயன்படுத்தி, இது உடைஞ்ச system-க்கு ஆதாரமா? பெரும்பாலும் அடிப்படைக் காரணம் என்ன?
5.  Section 06-ஓட table-ஐ பயன்படுத்தி, IRS, CDS-உடன் சேர்ந்து repo-வுக்கு தனி IM/VM தேவையில்லாதது ஏன் — அதுக்குப் பதிலா எந்த mechanic-ஐ பயன்படுத்துது, அந்த mechanic முதல்ல எங்க நிறுவப்பட்டது?
6.  Section 08-ஐ பயன்படுத்தி, ஒரு bilateral உறவுக்கு ஒரு CSA-ஓட கீழ முழுசா செயல்படும் VM process இருந்து, ஆனா பூஜ்யம் Initial Margin கடமை இருக்க முடியுமா? UMR-ஓட phase-in கட்டமைப்பை வெச்சு விளக்குங்க.
7.  Tri-party ஏற்பாடு ஒரு firm-ஓட operational process-ஐ மட்டுமில்ல, SYSTEM INTEGRATION தேவைகளையும் மாத்துதுன்னு Section 04 ஏன் சொல்லுது — bilateral custody-ல இருந்து உண்மையான IT வேறுபாடு என்ன?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 13](../docs/1-capital-markets/module-13-parties-leis.html): Parties, LEIs & Market Infrastructure**-க்குப் போலாம்; இது இந்த module ஏற்கனவே பயன்படுத்தி வந்த ஒவ்வொரு actor-ஐயும் (Custodian, Tri-Party Agent, CCP, Clearing Member) பெயரிடுது.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. Required Collateral = 2,000,000 - 500,000 = CAD 1,500,000. Remaining Exposure = 2,000,000 - 1,500,000 = CAD 500,000 (புது Threshold-க்கு சரியா சமம்).**

Section 05 சரியான உறவை நிறுவுது: Required Collateral = Net Exposure மைனஸ் Threshold; collateral சரியா call பண்ணப்பட்டப்புறம் Remaining Exposure எப்பவும் Threshold-க்கே சமம் — பெரிய பேசி முடிவு செய்யப்பட்ட Threshold நேரடியா பெரிய நிரந்தர uncollateralized exposure இடைவெளியை உருவாக்குது, கட்டமைப்பால, தவறால இல்ல.

**2. Collateral Value = 9,850,000 x (1 - 0.05) = 9,850,000 x 0.95 = CAD 9,357,500.**

பெரிய haircut சதவீதம் Market Value-க்கும் Collateral Value-க்கும் இடையிலான பெரிய இடைவெளியை உருவாக்கும் — Section 03 இது, இந்த குறிப்பிட்ட security (அல்லது இந்த CSA-ஓட risk appetite)-க்கு, ஒரு default scenario-ல liquidate பண்ணப்படுவதுக்கு முன்னாடி collateral தானே மதிப்பிழக்கும் ஆபத்துக்கு எதிரா பெரிய தடுப்பு தேவைங்கற judgment-ஐ பிரதிபலிக்குதுன்னு விளக்குது.

**3. IM இல்லாம, ஒரு position-க்கு எதிரா திடீர், பெரிய இரவு நகர்வு (அடுத்த நாளின் VM call வெளியிடப்பட்டு settle ஆகறதுக்கு முன்னாடியே) ஆனா, உயிர்பிழைச்ச counterparty-க்கு முழு "Margin Period of Risk"-க்கும் uncollateralized இழப்பை விட்டுடும் — VM நேற்றைய close வரை மட்டும் பிடிக்கும், தினசரி VM சுழற்சி எதிர்வினை ஆற்றுவதை விட வேகமா நடக்கும் gap நகர்வுக்கு எதிரா அது ஒருபோதும் பாதுகாக்க முடியாது.**

Section 02-ஓட takeaway இதை துல்லியமா சொல்லுது: VM தற்போதைய exposure-ஐ நாளுக்கு நாள் பின்தொடருது, ஆனா ஒரு counterparty default எந்த நொடியிலும் நடக்கலாம், default ஆன position-ஐ மூடி/மாத்த உண்மையான நேரம் எடுக்கும் (Margin Period of Risk, விளக்கமா ~10 business days) — அந்த closeout window-ல சேரக்கூடிய இழப்புகளை cover பண்ணவே IM இருக்கு, VM எவ்வளவு விரைவா கொடுக்கப்பட்டாலும் கட்டமைப்பு ரீதியா சமாளிக்க முடியாத risk.

**4. கட்டாயம் உடைஞ்ச system-க்கு ஆதாரம் இல்ல — Section 07 வெளிப்படையா disputes "சாதாரணம், விதிவிலக்கு இல்ல"ன்னு சொல்லுது, குறிப்பா ரெண்டு counterparties-உம் உண்மையான model judgment-ஐ உள்ளடக்கிய valuations-ஐ சுயாதீனமா கணக்கிடற குறைவான liquid, Model-Based positions-க்கு ([Module 10](../docs/1-capital-markets/module-10-valuation.html) §6-ஓட Level 2/3 கருத்து). பெரும்பாலும் அடிப்படைக் காரணம் அதே trade-ஓட exposure-ஐ Bank Of Toronto-ஓடும் Goldman Sachs-ஓடும் சொந்த சுயாதீன (IPV-பாணி, [Module 10](../docs/1-capital-markets/module-10-valuation.html) §7) கணக்கீடுகளுக்கு இடையிலான valuation inputs அல்லது model assumptions வித்தியாசம்.**

Section 07-ஓட takeaway இந்த இணைப்பை நேரடியா வரையுது: margin-call dispute process தான் [Module 10](../docs/1-capital-markets/module-10-valuation.html)-ஓட valuation-observability கருத்துகள் அருவமாக இல்லாம, தங்கள் சொந்த எண்ணை சுயாதீனமா கணக்கிடற ரெண்டு firms இடையிலான இயக்க reconciliation பிரச்சனையா மாறும் இடம்.

**5. Repo, Section 03 நிறுவின அதே collateral-haircut mechanic-ஐ (Market Value x (1 - Haircut) = Collateral Value) பயன்படுத்துது, ஆனா loan-ஓட சொந்த collateral-ஆ பரிமாறப்படற security மேல நேரடியா பொருத்தி, [Module 5](../docs/1-capital-markets/module-05-fixed-income.html) §3-ஓட repo வரையறையில முதல்ல நிறுவப்பட்டது — repo-வுக்கு margin போடக்கூடிய தனி "exposure" இல்ல, ஏன்னா post பண்ணப்பட்ட security தான் கொடுத்த cash-க்கான collateral, notional மேல உட்கார்ந்திருக்கும் ஒரு derivative-ஓட ஏற்ற இறக்க MTM இல்ல.**

Section 06-ஓட takeaway இதை வெளிப்படையா சொல்லுது: repo IM/VM framework-க்கு பொருந்தாததுக்கு காரணம் security-உம் collateral-உம் ஒரே transaction, ஒரு derivative-ஓட notional-உம் அதன் post பண்ணப்பட்ட margin-உம் போல ரெண்டு தனிக் கருத்துகள் இல்ல.

**6. ஆமா — Section 08 விளக்குது: UMR-ஓட phase-in AANA (Average Aggregate Notional Amount) அடிப்படையிலானது; AANA (அல்லது அதன் counterparty-ஓடது) பொருந்தும் phase-ஓட வரம்பைக் கடக்காத firm-க்கு UMR-ன் கீழ Initial Margin கடமையே இல்ல, bilateral-ஆ ஒத்துக்கிட்ட CSA-ஓட Variation Margin terms (Section 01-02) IM regulatory ரீதியா கட்டாயமாக்கப்பட்டதா இல்லையாங்கறதைச் சாராம முற்றிலும் சுயாதீனமா இன்னும் பொருந்தலாம்.**

Section 08-ஓட takeaway இதை நேரடியா சொல்லுது: "அதே VM mechanics... IM scope-ல இருந்தாலும் இல்லாட்டியும் இன்னும் பொருந்தும்" — IM-உம் VM-உம் ஒரு தொகுப்புச் சலுகை இல்ல; VM மிகப் பழைய, மிக உலகளாவிய CSA வழக்கம், கட்டாய IM குறிப்பா அதைப் பிடிக்கும் அளவு பெரிய firms-க்கு மேல அடுக்கப்பட்ட புதிய, AANA-வரம்பு-கட்டுப்பாட்டு regulatory தேவை.

**7. Bilateral custody-ல Bank Of Toronto-ஓட சொந்த systems collateral-ஐ value பண்ணி, haircuts பொருத்தி, Eligible Collateral விதிகளை சரிபார்த்து, substitutions-ஐ process பண்ணணும் — tri-party ஏற்பாட்டுக்கு பதிலா tri-party agent-உடன் (Euroclear, Clearstream, முதலியன) ஒரு DATA FEED integration தேவை; அது Bank Of Toronto சார்பா அந்த valuation/haircut/substitution logic எல்லாத்தையும் செய்யுது.**

Section 04 இதை உறுதியா சொல்லுது: tri-party custody பயன்படுத்தும் firm ஒரு வெளி agent-ஓட systems-உடன் integrate ஆகி அதன் கணக்கீடுகளை நம்பணும்; bilateral custody அந்த valuation/haircut/Eligible-Collateral logic-ஐ உள்ளுக்குள்ளயே கட்டி பராமரிக்க கோருது — ரெண்டு வெறும் வேறு manual workflows இல்ல, ஒரே Section 03 பிரச்சனையை தீர்க்கும் உண்மையிலயே ரெண்டு வேறு technical architectures.
