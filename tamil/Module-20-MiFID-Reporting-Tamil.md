# Module 20 — MiFID II / MiFIR Reporting Deep Dive (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> இது **[SFTR Reporting Deep Dive](../docs/2-reporting/module-16-sftr-reporting.html)**, **[UK DMO Reporting Deep Dive](../docs/2-reporting/module-17-dmo-uk-reporting.html)**, **[UPI, UTI & ANNA-DSB Deep Dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html)** குடும்பத்தை சேர்ந்த **reference module**. [Module 4](../docs/1-capital-markets/module-04-equity.html) §8, [Module 6](../docs/1-capital-markets/module-06-fx.html) §8, [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §4 ஒவ்வொண்ணும் ஒரு பரந்த கதையின் பகுதியா MiFID II/MiFIR-ஐ சுருக்கமா தொட்டுச்சு; இந்த module regime-ஐயே ஆழமா பார்க்கற ஒரே இடம் — ஒரு instrument-ஐ "MiFID eligible" ஆக்குவது எது, ஒரு முழு transaction report என்ன கொண்டிருக்கு, இந்த regime-க்கு புதுசா வர்ற கிட்டத்தட்ட எல்லாரையும் தடுமாற வைக்கற கேள்வி — **OTC-ல execute ஆன** equity trade உண்மையில எதுக்கு உட்பட்டது.
> **மூலம்:** English Module 20-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. MiFID II / MiFIR-ன்னா என்ன? — ரெண்டு சட்ட Instruments, ஒரே Regime

**Definition:** "MiFID II" என்பது **ஒண்ணா வேலை செய்யும் ரெண்டு தனி EU சட்டங்களுக்கான** பேச்சுவழக்கு சுருக்கம்:

| Instrument | வகை | எதை உள்ளடக்குது | எப்படி பொருந்துது |
|---|---|---|---|
| **MiFID II** — Directive 2014/65/EU | Directive | Investment firm அங்கீகாரம், conduct-of-business விதிகள், best execution (Section 7), market structure (RM/MTF/OTF venue வகைகள்) | ஒவ்வொரு member state-ஓட சொந்த தேசிய சட்டத்துக்குள்ள மாற்றப்படுது — நாட்டுக்கு நாடு விதிகள் சிறிது வேறுபடலாம் |
| **MiFIR** — Regulation (EU) No 600/2014 | Regulation | Transparency (Section 6), Share/Derivatives Trading Obligations (Section 7), **Transaction Reporting** (Article 26 — Section 3-5) | நேரடியா பொருந்தும், ஒவ்வொரு EU member state-லயும் ஒரே text — தேசிய transposition step இல்ல |

நடைமுறை புள்ளி: இந்த module உண்மையில பத்தின reporting, transparency கடமைகள் — ஒரு trade-capture/reporting system எதுக்கு எதிரா கட்டணுமோ அந்தப் பகுதி — **MiFIR**-ல இருக்கு, முழு regime-க்கும் அதன் இன்னும் பிரபலமான பெயரை தந்த Directive-ல இல்ல. **UK MiFIR** என்பது UK-ஓட onshored, retained-EU-law பதிப்பு, Brexit-க்கு அப்புறம், **FCA** மேற்பார்வை பண்றது; பழைய Directive-பக்க conduct விதிகள் இப்போ FCA-ஓட சொந்த Handbook-ல (COBS, MAR) இருக்கு.

**ஏன் இருக்கு:** 2008 நிதி நெருக்கடி ரெண்டு குறிப்பிட்ட இடைவெளிகளை வெளிப்படுத்தியப்புறம் MiFID II/MiFIR அசல் 2007 MiFID I-ஐ மாத்தியது: பொது வெளிப்படைத்தன்மை கிட்டத்தட்ட இல்லாத வளர்ந்து வரும் **dark/OTC equity trading**, non-equity instruments-க்கு (bonds, derivatives) எந்த வெளிப்படைத்தன்மை regime-ஏ இல்லாதது. EMIR (systemic derivatives risk), G20-ஓட 2009 Pittsburgh உறுதிமொழிகளுடன் சேர்ந்து, MiFID II/MiFIR என்பது "regulators-உம் பொதுமக்களும் உண்மையில என்ன trade ஆகுது, என்ன விலையில, யார் செய்யறாங்கன்னு பார்க்க வேண்டும்" என்பதற்கு EU-ஓட பதில் — EMIR-ஓட "எவ்வளவு risk இருக்கு" கேள்வியை விட நெருக்கடிக்குப் பிந்தைய சீர்திருத்தத்தின் வேறு கோணம்.

```text
WHERE MiFID II/MiFIR SITS AMONG THE REPORTING REGIMES
========================================================================

  EMIR    ->  reports OTC DERIVATIVE ECONOMICS to a Trade Repository —
              "how much systemic derivatives risk exists?" (Module 2 §7)

  SFTR    ->  reports SECURITIES FINANCING TRANSACTIONS to a Trade
              Repository — "how much collateral re-use/leverage
              exists?" (SFTR Deep Dive §1)

  MiFIR   ->  TWO separate obligations, THIS MODULE:
              (a) Transaction Reporting -> to the National Competent
                  Authority (NCA) directly, PRIVATE — "did anyone
                  behave abusively on this execution?" (Section 2-5)
              (b) Transparency -> to the PUBLIC market via the venue
                  or an APA — "what did this actually trade at?"
                  (Section 6-7)

  All three can fire off the SAME single trade, answering three
  genuinely different questions, to three different destinations,
  under three different legal texts (Module 14 §1's five-obligation
  framing, now with MiFIR's own internal two-way split added).
```

**Takeaway:** இந்த regime-ல குழப்பத்துக்கு மிகப்பெரிய ஒற்றை மூலம் "MiFID reporting"-ஐ ஒரே கடமையா நடத்துறது. அது குறைந்தது **ரெண்டு**, கீழ Sections 2, 3-5, 6-7-ல தனித்தனியா உள்ளடக்கப்பட்டது — அவற்றை ஒண்ணா குழப்பிக்கறது ஒரு reporting build-ல மிகப் பொதுவான வடிவமைப்பு தவறு.

---

## 2. ஒரு குடையின் கீழ மூணு தனிக் கடமைகள் — குழப்பிக்காதீங்க

**Definition:** [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §4-ஓட ஒப்பீட்டு table-ஐ விரிவாக்கி, MiFID II/MiFIR உண்மையில **மூணு** கடமைகளை மூட்டை கட்டுது; scope-ல trades-ஐ execute பண்ற firm அவற்றை சுயாதீனமா நிறைவேத்தணும். ஒண்ணை சரியா செய்யறது மற்ற ரெண்டையும் திருப்திப்படுத்தாது.

| Dimension | Transaction Reporting (MiFIR Art. 26 / RTS 22) | Transparency Regime (MiFIR Arts. 3-13 / RTS 1, RTS 2) | Best Execution (MiFID II Art. 27) |
|---|---|---|---|
| **Destination** | National Competent Authority (NCA), directly or via an ARM — never public | The public market, via the trading venue itself or an APA (Approved Publication Arrangement) | The client, on request/periodic disclosure — not a regulator at all |
| **Purpose** | Market abuse detection (insider dealing, manipulation) | Price discovery — letting the whole market see what actually traded | Client protection — proving the firm got the client the best available outcome |
| **What's published** | Nothing public — full economic + party detail, restricted to the regulator | Price, volume, time — NOT counterparty identity | Execution-quality summaries (largely reduced in scope since a 2021 MiFID II "Quick Fix" amendment) |
| **Sides reporting** | Generally single-sided — the executing investment firm reports its own execution | Single publication per trade (by whichever side is designated the "reporting party" for transparency purposes) | Owed by the firm to its own client only |
| **Deadline** | T+1 (Section 9) | As close to real-time as technically possible, subject to deferrals (Section 6) | Ongoing obligation, not event-driven |
| **First seen** | [Module 4](../docs/1-capital-markets/module-04-equity.md) §8 | [Module 4](../docs/1-capital-markets/module-04-equity.md) §7's Exchange vs OTC framing | [Module 4](../docs/1-capital-markets/module-04-equity.md) §8 (Systematic Internalizer mention) |

```text
SAME TRADE, TWO DIFFERENT MiFIR OUTPUTS -- NEITHER SUBSTITUTES THE OTHER
========================================================================

  Maple Leaf Pension Fund buys SAP SE shares via Bank Of Toronto,
  executed OTC (bilaterally, off any venue's order book):

  TRANSACTION REPORT (Section 3-5)         TRANSPARENCY PUBLICATION (Section 6)
  ---------------------------------        ------------------------------------
  -> Goes to Bank Of Toronto's NCA          -> Goes PUBLIC, via an APA
     (e.g. the FCA, or BaFin)                  (Section 6)
  -> Full detail: both parties' LEIs,       -> Price, volume, timestamp ONLY --
     trader ID, algo flags, price,             no counterparty names at all
     quantity, venue code "XOFF"            -> Possibly DELAYED if the trade
  -> NEVER visible to the public               qualifies as Large In Scale
     or to Goldman Sachs, a competitor          (Section 6)
  -> Required EVERY time, no exceptions     -> Required EVERY time too --
     for size (Section 3)                      size only changes the TIMING,
                                                never whether it's required

  -> Building only ONE of these two pipelines satisfies HALF the
     regime. A system that publishes to an APA but never files a
     transaction report (or vice versa) is non-compliant either way.
```

**Takeaway:** ஒரு transaction report-உம் ஒரு transparency publication-உம் ஒரே ஆவணத்துக்கான ரெண்டு பெயர்கள் இல்ல — அவை கிட்டத்தட்ட முழுக்க வேறு fields-ஐ கொண்டிருக்கு, முற்றிலும் வேறு பெறுநர்களுக்குப் போகுது, முற்றிலும் வேறு regulatory கேள்விகளுக்கு பதில் சொல்லவே இருக்கு. Section 3 யார் ஒரு transaction report-ஐ file பண்ணணும்னு உள்ளடக்குது; Section 6-7, தனி transparency கடமையை, இந்த module மீதி கட்டும் குறிப்பிட்ட OTC-equity விதிகள் உட்பட.

---

## 3. ஒரு Instrument-ஐ "MiFID Eligible" ஆக்குவது எது? — Article 26(2) Scope Test

**Definition:** MiFIR Article 26(2), எந்த financial instruments Transaction Reporting கடமையை தூண்டுதுன்னு துல்லியமா வரையறுக்குது. இந்த மூணு சோதனைகள்ல **ஏதாவது ஒண்ணு** உண்மையா இருந்தா ஒரு instrument **MiFID eligible** (அதாவது transaction-reportable):

```text
ARTICLE 26(2) SCOPE TEST -- ANY ONE OF THREE CONDITIONS MAKES IT ELIGIBLE
========================================================================

  (a) THE INSTRUMENT ITSELF is admitted to trading, or actually
      traded, on an EU/UK trading venue (a Regulated Market, MTF,
      or OTF -- Module 1 §5's venue categories) -- OR a request for
      its admission has been made

  (b) THE INSTRUMENT'S UNDERLYING is a financial instrument that is
      admitted to trading or traded on a trading venue (covers a
      derivative referencing a venue-listed share, bond, or index
      constituent)

  (c) THE INSTRUMENT'S UNDERLYING is an INDEX OR BASKET composed of
      financial instruments that are admitted to trading or traded
      on a trading venue (covers, e.g., a derivative on the
      EURO STOXX 50)
```

**இந்த regime-க்கு புதுசா வர்றவங்க தவறவிடும் முக்கிய புள்ளி: இந்த மூணு சோதனைகள்ல ஒண்ணும் *கேள்விக்குட்பட்ட குறிப்பிட்ட trade எங்க execute ஆச்சு*ன்னு கேட்கறதில்ல.** அவை *instrument* (அல்லது அதன் underlying, அல்லது அதன் underlying-ஓட constituents) EU/UK-ல எங்கயாவது venue-admitted ஆ இருக்கான்னு மட்டுமே கேட்குது. அது உண்மையானவுடனே, **அந்த instrument-ல நடக்கற ஒவ்வொரு trade-உம் transaction-reportable — on-venue, OTC, bilateral, block, anonymous, எல்லாம்** — instrument admitted ஆ இருக்கும் வரை. இதுதான் ஒரு "OTC equity" transaction-ஐ முதல்லயே transaction-reportable ஆக்கும் ஒற்றை உண்மை (Section 7 நேரடியா இதன் மேல கட்டுது).

**FIRDS — operational mechanism.** நடைமுறைல, எந்த reporting system-உம் ஒவ்வொரு trade-க்கும் Section 3-ஓட மூணு சோதனைகளை முதல்ல இருந்து derive பண்றதில்ல. ESMA **Financial Instruments Reference Data System (FIRDS)**-ஐ நடத்துது: EU/UK trading venues தாங்கள் admit பண்ற ஒவ்வொரு instrument-க்கும் reference data-வை submit பண்ணும்; அதன் விளைவா வர்ற consolidated instrument பட்டியலை ESMA (Brexit-க்கு அப்புறம் தனியா, UK venues-க்கு FCA) வெளியிடுது. ஒரு reporting engine ஒரு trade-ஓட ISIN-ஐ FIRDS-க்கு எதிரா (நேரடியா, அல்லது ஒரு market-data vendor-ஓட FIRDS-derived feed வழியா) சரிபார்க்குது — சட்டச் சோதனையை மறுஅமலாக்காம encode பண்ற ஒரு எளிய lookup.

### Worked example — மூணு trades, மூணு வேறு eligibility பதில்கள்

```text
TRADE 1 -- BANK OF TORONTO BUYS SAP SE SHARES FOR MAPLE LEAF
PENSION FUND, EXECUTED OTC (BILATERALLY WITH GOLDMAN SACHS)
========================================================================
  Instrument: SAP SE ordinary shares
  Admitted to trading on: Deutsche Börse Xetra (an EU Regulated
    Market) -- present on FIRDS

  -> Test (a): YES, admitted on an EU trading venue
  -> MiFID ELIGIBLE, even though THIS specific trade never touched
     Xetra's order book at all -- it was negotiated bilaterally
     (Section 7 covers exactly what that OTC execution is then
     subject to)

TRADE 2 -- BANK OF TORONTO WRITES MAPLE LEAF PENSION FUND A
BESPOKE OTC EQUITY OPTION ON SAP SE SHARES
========================================================================
  Instrument: bilateral OTC Equity Option (Module 4 §4d's product
    family), Underlying: SAP SE shares

  -> Test (a): NO -- the OPTION itself is never admitted to any
     venue; it's a private bilateral contract
  -> Test (b): YES -- its UNDERLYING (SAP SE shares) IS admitted to
     trading on Xetra
  -> MiFID ELIGIBLE via Test (b) -- the derivative inherits its
     underlying's venue-admitted status, even though the option
     contract itself is entirely bespoke and bilateral

TRADE 3 -- BANK OF TORONTO'S CORPORATE DESK ARRANGES A PRIVATE
SHARE SALE IN AN UNLISTED FAMILY BUSINESS FOR A WEALTH CLIENT
========================================================================
  Instrument: ordinary shares of a private (unlisted) company

  -> Test (a): NO -- not admitted to, or traded on, any trading
     venue anywhere, and no admission request has been made
  -> Test (b) / (c): N/A -- this IS the underlying, there's nothing
     further to check
  -> NOT MiFID eligible -- no MiFIR Transaction Report is required,
     regardless of trade size or how "professionally" it's executed
```

**Takeaway:** "MiFID eligible" என்பது ஒரு தனி **execution**-ஓட பண்பு இல்ல, **instrument**-ஓட பண்பு (ஒரு தடவை, FIRDS வழியா சரிபார்க்கப்படுது). Section 4-ஓட "யார் report பண்றது"-உம் Section 5-ஓட "என்ன report பண்ணப்படுது"-உம் Section 3-ஓட சோதனை ஏற்கனவே அந்த instrument-க்கு YES-ஐ திருப்பினப்புறம் மட்டுமே பொருந்தும்.

---

## 4. யார் Report பண்ணணும், எப்படி — Investment Firms, ARMs & Venues

**Definition:** Transaction Reporting கடமை transaction-ஐ execute பண்ற **investment firm** மேல விழுது — MiFID II-ன் கீழ அங்கீகரிக்கப்பட்ட entity (ஒரு வங்கி, broker-dealer, அல்லது சொந்த கணக்குல deal பண்ணும் அல்லது client orders-ஐ execute பண்ணும் portfolio manager) — EMIR-ஓட dual-sided FC/NFC கட்டமைப்பு வேலை செய்யற மாதிரி இறுதி client அல்லது counterparty மேல இல்ல ([Module 13](../docs/1-capital-markets/module-13-parties-leis.html) §6).

**பொதுவா single-sided, ஆனா முதல் பார்வைல தோணுறபடி இல்ல.** ஒரு trade ரெண்டு MiFID investment firms இடையில (உதா., Bank Of Toronto-வும் Goldman Sachs-உம்) என்றா, **ரெண்டு பேரும்** சுயாதீனமா ஒரு transaction report-ஐ submit பண்ணுது — ஆனா இது EMIR-ஓட dual-sided, UTI-ல pair ஆகும் model *இல்ல*. ஒவ்வொரு firm-உம் தன் **சொந்த** execution-ஐ, தன் **சொந்த** book-ல, தன் **சொந்த** (ஒருவேளை வேறுபட்ட) NCA-க்கு report பண்ணுது; [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3 EMIR-க்கு விவரிச்ச மாதிரி ரெண்டு reports-ஐ ஒன்றுக்கொன்று match பண்ணும் Trade Repository pairing/reconciliation step இல்ல. பொருளாதார ரீதியா ஒரே trade-க்கான ரெண்டு firms-ஓட reports தனித்தனியா இருக்கு, ஒவ்வொண்ணும் தன்னளவுல செல்லுபடியாகும்.

| Reporting Channel | எப்படி வேலை செய்யுது |
|---|---|
| **Direct submission** | Investment firm தன் சொந்த reports-ஐ கட்டி நேரடியா தன் NCA-க்கு submit பண்ணும் |
| **ARM (Approved Reporting Mechanism)** | Firm சார்பா reports-ஐ validate, format, transmit பண்ணும் regulator-authorized மூன்றாம் தரப்பு — Trade Repository-க்கு MiFIR-உலக இணை, ஆனா அடிப்படையில வேறு report வகை, destination-க்கு |
| **Trading venue transmission** | ஒரு venue-ல உண்மையில execute ஆன trades-க்கு, சில நிபந்தனைகளின் கீழ venue தானே firm சார்பா சில order/execution விவரங்களை transmit பண்ணலாம்; firm-ஓட சொந்த reporting சுமையை குறைக்கும் (நீக்காது) |

**Takeaway:** EMIR-ஓட dual-sided pairing mental model-ஐ இங்க மறுபயன்படுத்தாதீங்க — ஒரு MiFIR transaction report ஒரு தனியார், single-firm submission, regulator பக்கத்துல counterparty-matching step இல்ல; அதனாலதான் Section 5-ஓட report தன்னளவுலயே முழுமையா இருக்கணும் ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §3-ஓட EMIR pairing செய்யற மாதிரி field-நிலை தவறை பிடிக்க reconciliation process இல்ல).

---

## 5. Transaction Report Content — RTS 22 Field Groups, Worked

**Definition:** MiFIR-ஓட technical standard **RTS 22** தோராயமா **65 reportable fields**-ஐ வரையறுக்குது, அவை எதை விவரிக்குதுங்கறதை பொறுத்து தொகுக்கப்பட்டது:

| Field Group | Contains |
|---|---|
| **Report & venue identification** | Report status, executing entity's LEI, trading venue code (or `XOFF` for OTC — Section 5's worked example uses this) |
| **Buyer/seller identification** | LEI (for a legal entity) or a national identifier (for a natural person), plus the investment decision maker and executing trader/algo IDs |
| **Instrument identification** | ISIN ([Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §2), CFI code ([Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §6) |
| **Transaction details** | Trading date/time (to the microsecond, for venue-executed trades), price, quantity, currency |
| **Flags** | Short sale indicator, waiver indicator (which pre-trade transparency waiver applied, if any — Section 6), commodity derivative position-limit flag ([Module 7](../docs/1-capital-markets/module-07-commodities.md) §8), OTC post-trade indicator |

### Worked example — Trade 1-ஓட SAP SE purchase, transaction-reported

```text
RTS 22 TRANSACTION REPORT -- BANK OF TORONTO'S SIDE
========================================================================
  Executing entity LEI:      Bank Of Toronto's LEI
  Buyer:                     Maple Leaf Pension Fund's LEI
  Seller:                    Goldman Sachs' LEI
  Investment decision maker: Maple Leaf Pension Fund's portfolio
                              manager (national ID, per RTS 22)
  Executing trader:          Bank Of Toronto desk trader ID

  Instrument (ISIN):         DE0007164600 (SAP SE)
  CFI Code:                  ESVUFR (equity, common share)

  Trading date/time:         2026-08-25T14:32:07.219Z
  Venue:                     XOFF (executed OTC -- Section 3's Trade 1)
  Price:                     EUR 190.00 per share
  Quantity:                  10,000 shares
  Trade value:                EUR 1,900,000.00

  Short sale indicator:      No
  Waiver indicator:          Not applicable (Section 6 waivers apply
                              to venue PRE-trade transparency, not to
                              an off-venue OTC execution)

  -> Submitted to Bank Of Toronto's NCA by T+1, NEVER made public
     (Section 2's comparison table)
```

**Takeaway:** `XOFF`-ஐ கவனிங்க — "எந்த trading venue-க்கும் வெளியே execute ஆனது"ன்னு குறிக்கும் நிலையான venue code — transaction report-லயே நேரடியா தோன்றுது. ஒரு OTC execution off-venue ஆ இருக்கறதால regulator-கிட்ட இருந்து மறைக்கப்படறதில்ல; அது வெளிப்படையா அப்படியே flag பண்ணப்பட்டு, ஒரு on-venue trade சுமக்கும் அதே field முழுமையோட report பண்ணப்படுது.

---

## 6. Transparency Regime — Pre-Trade & Post-Trade

**Definition:** Section 3-5-ஓட தனியார் Transaction Report-ல இருந்து முற்றிலும் தனியா, MiFIR-ஓட transparency regime (Articles 3-13, equity-போன்ற instruments-க்கு RTS 1) MiFID-eligible equities-ல சில trading நடவடிக்கை **பொதுவில** வெளியிடப்படணும்னு கோருது.

| Transparency வகை | என்ன கோருது | யாருக்கு பொருந்தும் |
|---|---|---|
| **Pre-trade transparency** | தற்போதைய உறுதியான bid/offer quotes, order-book depth-ஐ நிகழ்நேரத்துல வெளியிடுதல் | Trading venues எப்பவும்; OTC-ல deal பண்ற firm அந்த instrument-க்கு **Systematic Internaliser** என்றா மட்டும் (Section 7) |
| **Post-trade transparency** | Execute ஆன ஒவ்வொரு trade-க்கும் விலை, volume, நேரத்தை "தொழில்நுட்ப ரீதியா சாத்தியமான அளவு நிகழ்நேரத்துக்கு நெருக்கமா" வெளியிடுதல் | On-venue trades (venue தானே வழியா); OTC trades (ஒரு **APA**, Approved Publication Arrangement வழியா) |

**ஒத்திவைக்கப்பட்ட publication — operational ரீதியா முக்கியமான நுணுக்கம்.** Instrument-ஓட இயல்பான trading அளவுக்கு ஒப்பிட்டா **Large In Scale (LIS)** ஆ தகுதிபெறும் ஒரு trade, உடனடியா வெளியிடப்படாம, அதன் post-trade publication-ஐ **தாமதப்படுத்த** முடியும் — நிமிடங்களிலிருந்து நாள் முடிவு வரை (குறைந்த liquid instruments-க்கு இன்னும் நீளமா). இது ஒரு பெரிய block-ஐ execute பண்ற firm, position-ஐ இன்னும் வேலை செஞ்சுட்டிருக்கும்போதே சந்தை பாதிப்பிலிருந்து பாதுகாக்குது — அதனாலதான் ஒரு உண்மையிலயே பெரிய OTC block trade-உம் அதே share-ல ஒரு சிறிய OTC trade-உம், ஒத்த transaction-reporting கடமை இருந்தாலும், transparency-compliant-ஆ முற்றிலும் வேறு publication காலவரிசைகள்ல இருக்கலாம் (Section 2-ஓட diagram ஏற்கனவே flag பண்ணியது: அளவு *நேரத்தை* மாற்றும், *தேவையை* ஒருபோதும் இல்ல).

### Worked example — Trade 1, transparency-க்கு வெளியிடப்படுது

```text
POST-TRADE TRANSPARENCY -- TRADE 1'S SAP SE PURCHASE
========================================================================
  Illustrative LIS threshold for SAP SE's liquidity band: EUR 650,000

  Trade value: EUR 1,900,000.00  (Section 5's report)
  1,900,000 > 650,000  ->  QUALIFIES AS LARGE IN SCALE

  -> Bank Of Toronto (as the designated reporting side) publishes
     via an APA: price EUR 190.00, quantity 10,000 shares, timestamp
     -- but DEFERRED, e.g. until end of trading day, rather than
     immediately
  -> NO counterparty names published at any point -- contrast
     Section 5's transaction report, which names both LEIs privately
     to the NCA
```

**Takeaway:** அதே trade ரெண்டு முற்றிலும் வேறு கடிகாரங்கள்ல ரெண்டு outputs-ஐ உருவாக்குது — ஒரு தனியார், ஒத்திவைக்க முடியாத, T+1 transaction report (Section 5), ஒரு பொது, ஒருவேளை ஒத்திவைக்கப்படும் transparency publication (இந்த section) — எந்த deadline எந்த output-க்குன்னு கலந்துபோறது ஒரு உண்மையான, பொதுவான build தவறு.

---

## 7. OTC Equities — அவை உண்மையில எதுக்கு உட்பட்டவை

**இந்த module நேரடியா பதில் சொல்லவே இருக்கற கேள்வி இது.** "OTC-ல trade ஆனது"ன்னா "MiFID-க்கு வெளியே"ன்னு **அர்த்தமில்ல**. ஒரு MiFID-eligible equity-ல (Section 3) OTC-ல execute ஆன ஒரு trade, ஒரே நேரத்துல **நாலு தனிக் கடமைகள் வரை** உட்பட்டது:

```text
AN OTC EQUITY TRADE'S FOUR POTENTIAL OBLIGATIONS
========================================================================

  1. TRANSACTION REPORTING (Section 3-5)
     -> ALWAYS required if the instrument is MiFID eligible.
        No exceptions for size, venue, or counterparty type.

  2. POST-TRADE TRANSPARENCY (Section 6)
     -> ALWAYS required too -- publication via an APA, with
        possible LIS deferral (Section 6). Size changes WHEN,
        never WHETHER.

  3. BEST EXECUTION (MiFID II Art. 27)
     -> The firm must still take "all sufficient steps" to get the
        client the best possible result, even with no venue order
        book to benchmark against directly -- an ongoing conduct
        obligation, not a per-trade filing.

  4. THE SHARE TRADING OBLIGATION -- STO (MiFIR Art. 23)
     -> THE RULE THAT ACTUALLY RESTRICTS whether a trade can be
        purely bilateral OTC in the first place (below).
```

### 7a. Share Trading Obligation (STO) — OTC equity trading-ஐ உண்மையில கட்டுப்படுத்தும் விதி

**Definition:** MiFIR Article 23, EU Regulated Market-ல trading-க்கு admit ஆன shares பொதுவா ஒரு venue-ல (ஒரு Regulated Market, MTF, OTF, அல்லது சமமான third-country venue) **அல்லது ஒரு Systematic Internaliser வழியா (Section 7b)** trade ஆகணும்னு கோருது — முழுக்க bilateral-ஆ இல்ல. ஒரு liquid, EU-RM-admitted share-ல உண்மையான, கட்டமைக்கப்படாத bilateral OTC trading *விதிவிலக்கு*, trade இதுவா இருக்கும்போது மட்டும் அனுமதிக்கப்படுது:

-   **"Non-systematic, ad-hoc, irregular and infrequent,"** அல்லது
-   தகுதியுள்ள/தொழில்முறை counterparties இடையில, **price discovery-க்கு பங்களிக்காதது** (உதா., ஒரு portfolio transfer, ஒரு உள் reallocation, அல்லது புதுசா பேசப்படாம ஒரு venue விலையை reference பண்ணி விலையிடப்பட்ட trade).

```text
STO WORKED EXAMPLE -- CAN THIS SAP SE TRADE BE PURE BILATERAL OTC?
========================================================================
  SAP SE: admitted to trading on Deutsche Börse Xetra (Section 3),
  a liquid, actively-traded EU blue-chip

  Bank Of Toronto wants to sell 10,000 SAP SE shares to Goldman
  Sachs via a one-off negotiated bilateral trade, priced off
  Xetra's current quote, as an internal portfolio rebalancing --
  NOT part of a regular OTC trading pattern in this name

  -> Does the trade contribute to price discovery? NO -- it's
     referenced to an existing venue price, not independently
     negotiated
  -> STO carve-out APPLIES -> this specific trade CAN be executed
     bilaterally OTC

  Contrast: if Bank Of Toronto ran a REGULAR, ongoing bilateral
  OTC dealing business in SAP SE shares against client flow
  (systematic, not ad-hoc) -> the carve-out does NOT apply -> Bank
  Of Toronto must either route the flow through a venue, OR
  register as a Systematic Internaliser for SAP SE and trade it
  through that regime instead (Section 7b)
```

**"OTC equities எதுக்கு உட்பட்டவை"ன்னு இதுதான் மையம்:** உண்மையிலயே ad-hoc, விலை-உருவாக்காத bilateral trades என்ற சிறுபான்மைக்கு, மேல உள்ள Sections 1-3 முழுசா இன்னும் பொருந்தும் (transaction reporting, transparency, best execution) — ஆனா ஒரு liquid EU share-ல **வழக்கமான OTC dealing business**-ஐ ஒத்த எதுக்கும், STO அந்த நடவடிக்கையை கட்டமைக்கப்படாத bilateral OTC-யாவே விடாம, **Systematic Internaliser** regime-க்குள்ள தள்ளுது.

### 7b. Systematic Internaliser (SI) regime

**Definition:** ஒரு குறிப்பிட்ட instrument-ல **ஒழுங்கமைக்கப்பட்ட, அடிக்கடி, முறையான, கணிசமான அடிப்படையில** client orders-ஐ OTC-ல execute பண்ணி சொந்த கணக்குல deal பண்ற ஒரு firm — ESMA அவ்வப்போது அளவிட்டு வெளியிடும் அளவு வரம்புகளுக்கு மேல — அந்த instrument-க்கு **Systematic Internaliser-ஆ register ஆகணும்**. Register ஆனப்புறம், ஒரு SI அதன் "OTC" dealing-ஐ ஒரு சிறிய trading venue போல நடந்துக்கும்படி செய்யும் கடமைகளை ஏற்குது:

| SI கடமை | அர்த்தம் |
|---|---|
| **Pre-trade transparency (Section 6)** | எந்த client-ஆலும் deal பண்ணக்கூடிய, வரையறுக்கப்பட்ட **Standard Market Size (SMS)** வரை உறுதியான, executable quotes-ஐ வெளியிடணும் |
| **Quote செய்த விலையில execution** | SMS வரை client orders-ஐ பொதுவா அதன் வெளியிடப்பட்ட quote-ல execute பண்ணணும் (நியாயமான விலை improvement-க்கு உட்பட்டு) |
| **Post-trade transparency (Section 6)** | Trade-ஐ தானே ஒரு APA வழியா வெளியிடணும், அதை client-ஓட சொந்த investment firm-க்கு மட்டுமே விட்டுடாம |
| **Transaction reporting (Section 3-5)** | மாறாது — வேறு எந்த MiFID-eligible instrument-க்கும் போல, இன்னும் தேவை |

```text
SI vs NON-SI OTC DEALER -- SAME INSTRUMENT, DIFFERENT OBLIGATIONS
========================================================================

  Bank Of Toronto deals SAP SE shares OTC against client flow
  frequently enough to cross ESMA's SI threshold for this name:

  -> REGISTERED SI for SAP SE
  -> Must publish firm bid/offer quotes up to SMS (illustrative:
     SMS = 7,500 shares for this liquidity band)
  -> A client order for 5,000 shares (under SMS) -> Bank Of Toronto
     MUST deal at (or better than) its published quote
  -> A client order for 50,000 shares (well over SMS) -> SI quoting
     obligation doesn't bind at that size; negotiated bilaterally,
     LIS deferral (Section 6) likely available given the size

  A SMALLER firm dealing SAP SE OTC only occasionally, below the
  threshold:

  -> NOT an SI for SAP SE
  -> No pre-trade quote-publication obligation
  -> STILL owes transaction reporting (Section 3-5) and post-trade
     transparency (Section 6) on every trade, and its bilateral
     dealing pattern must stay within the STO's "ad-hoc, irregular,
     infrequent" carve-out (Section 7a) or it risks needing to
     become an SI (or route to a venue) itself
```

**Takeaway:** SI regime தான் MiFID II ஒவ்வொரு OTC trade-ஐயும் ஒரு முறையான exchange-க்கு கட்டாயப்படுத்தாம OTC equity சந்தைக்கு வெளிப்படைத்தன்மையை கொண்டு வர பயன்படுத்தற முறை — ஒரு firm "அவ்வப்போது bilateral dealer" (இலகுவான கடமைகள், ஆனா இதை எவ்வளவு அடிக்கடி செய்யலாம்ங்கறதுல STO-ஆல கட்டுப்படுத்தப்பட்டது) நிலையில இருந்து, ஒரு பெயரில அதன் சொந்த OTC dealing நடவடிக்கை, அந்த instrument-க்கு செயல்பாட்டு ரீதியா ஒரு trading venue-ஆவே இருக்கும் அளவு பெருசாவும் அடிக்கடியுமா மாறும்போது "Systematic Internaliser" (கனமான, venue-போன்ற pre-trade quoting கடமைகள்) நிலைக்கு கடக்குது.

### 7c. முழு முடிவு சட்டகம் — இந்த OTC equity trade compliant-ஆ?

```text
Q1. Is the instrument MiFID eligible?                 -> Section 3
    NO  -> stop, no MiFIR obligations apply at all
    YES -> continue

Q2. Is a Transaction Report being filed, T+1?          -> Section 5
    Required regardless of every answer below

Q3. Is post-trade transparency being published,
    via the venue or an APA (with LIS deferral if
    applicable)?                                       -> Section 6
    Required regardless of every answer below

Q4. Is this trade genuinely ad-hoc/non-price-forming,
    OR is it part of a regular OTC dealing pattern?     -> Section 7a
    Ad-hoc      -> STO carve-out applies, stays bilateral OTC
    Regular     -> continue to Q5

Q5. Does the firm's OTC dealing volume in this
    instrument cross the SI threshold?                  -> Section 7b
    NO  -> must route to a venue instead of continuing
           informal bilateral OTC dealing
    YES -> firm must be registered as an SI and quote
           accordingly
```

---

## 8. Worked End-to-End Example — ஒரு OTC Equity Trade, ஒவ்வொரு கடமையும் ஒண்ணா

```text
BANK OF TORONTO (REGISTERED SI FOR SAP SE), SELLS 5,000 SAP SE
SHARES OTC TO A RETAIL-SIZE CLIENT ORDER
========================================================================
  Instrument: SAP SE (ISIN DE0007164600) -- Section 3's Test (a),
              MiFID eligible

  Client:     Maple Leaf Pension Fund, order for 5,000 shares
  SI quote:   Bank Of Toronto's published bid/offer for SAP SE,
              valid up to SMS = 7,500 shares (Section 7b)
  5,000 < 7,500 -> WITHIN SMS -> Bank Of Toronto must deal at (or
              better than) its own published quote: EUR 190.05
  Trade value: 5,000 x 190.05 = EUR 950,250.00

  1) TRANSACTION REPORT (Section 5)
     -> Filed by Bank Of Toronto to its NCA, T+1, full detail,
        venue code XOFF, never public

  2) POST-TRADE TRANSPARENCY (Section 6)
     -> EUR 950,250 vs. an illustrative LIS threshold of EUR
        650,000 for this band -> 950,250 > 650,000 -> QUALIFIES
        FOR DEFERRAL -- published via APA, price/volume only, but
        not necessarily instantaneous
     -> As the registered SI, Bank Of Toronto itself is the
        publishing party (Section 7b), not the client's own firm

  3) BEST EXECUTION (Section 7)
     -> Bank Of Toronto must be able to demonstrate EUR 190.05 met
        or bettered prevailing market conditions for this size and
        instrument -- an ongoing conduct obligation, evidenced on
        request, not a per-trade filing

  4) STO / SI STATUS (Section 7a-b)
     -> Compliant BY CONSTRUCTION: trading through a registered SI
        at its published quote is one of the STO's own accepted
        routes for share trading, not a carve-out exception --
        Bank Of Toronto's SI registration is precisely what makes
        this OTC-labeled trade legally equivalent to an on-venue
        execution for STO purposes
```

**Takeaway:** ஒரு registered SI-ல ஒரு OTC equity trade ஒரு தனியார் transaction report-ஐயும், ஒரு பொது (ஒருவேளை ஒத்திவைக்கப்படும்) transparency publication-ஐயும், ஒரு உள்ளார்ந்த best-execution கடமையையும் உருவாக்கி, Share Trading Obligation-ஐயும் ஒரே நேரத்துல நிறைவேத்துது — ஒருபோதும் ஒரு முறையான exchange order book-ஐத் தொடாமலே; MiFID II உலகத்துல "OTC" என்பது ஒரு trade *எங்க* பேசப்பட்டதுன்னு விவரிக்குது, அது *regulate பண்ணப்படுதா* இல்லையான்னு ஒருபோதும் இல்ல என்பதற்கான நிரூபணம்.

### கூடுதல் உதாரணம் — அதே SAP SE trade, மூணு வேறு விற்பனையாளர் நிலைகள்

| விற்பனையாளர் நிலை | Pre-trade quote கடமை | Transaction report | Post-trade transparency | STO |
|---|---|---|---|---|
| **Registered SI** (Bank Of Toronto) | ஆமா — SMS வரை உறுதியான quotes | ஆமா, T+1 | ஆமா, SI தானே APA வழியா (LIS-ஆ இருந்தா ஒத்திவைப்பு) | SI வழியா trade பண்றது அனுமதிக்கப்பட்ட வழி |
| **அவ்வப்போது bilateral dealer** (SI இல்ல) | இல்ல | ஆமா, T+1 | ஆமா, APA வழியா | Ad-hoc, non-price-forming என்றா மட்டும் carve-out |
| **Venue-ல execute** (Xetra order book) | Venue தானே | ஆமா, T+1 (அல்லது venue transmission) | Venue தானே | தானாக compliant |

---

## 9. Reporting Deadline & Record-Keeping

**Transaction Reporting deadline:** **T+1** — EMIR, SFTR போல அதே அடுத்த-business-day-முடிவு ஒழுக்கம் ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §9).

**Transparency publication deadline:** ஒத்திவைக்கப்படாத trades-க்கு "தொழில்நுட்ப ரீதியா சாத்தியமான அளவு நிகழ்நேரத்துக்கு நெருக்கமா" — T+1-ஐ விட கணிசமா வேகமா — LIS ஒத்திவைப்பு windows-உடன் (Section 6) மட்டும் அனுமதிக்கப்பட்ட விதிவிலக்கா, அந்த windows தாங்களே ESMA/FCA technical standards-ஆல (RTS 1) நிர்ணயிக்கப்பட்டு அவ்வப்போது மறுஅளவிடப்படுது — [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12-ஓட "hard-code பண்றதுக்கு முன்னாடி தற்போதைய எண்களை உறுதிப்படுத்துங்க" எச்சரிக்கை பொருந்தும் சரியான வகை எண்.

**Order record-keeping (MiFID II Article 25):** மேல உள்ள ரெண்டு reporting கடமைகளிலிருந்தும் தனியா, investment firms orders-ன், transactions-ன் (execute ஆன trades மட்டுமில்ல — execute ஆகாத/ரத்து ஆன orders-உம்) பதிவுகளை குறைந்தபட்ச retention காலத்துக்கு (தற்போதைய standard-ன் கீழ ஐந்து ஆண்டுகள்) வெச்சிருக்கணும், அதனால ஒரு NCA ஒரு விசாரணையின்போது trading நடவடிக்கையை பின்னோக்கி மறுகட்டமைக்க முடியும், அந்த நேரத்துல முன்கூட்டியே report பண்ணப்பட்டதை மட்டும் மதிப்பாய்வு பண்ணாம.

> **Callout — இவற்றுக்கு எதிரா கட்டறதுக்கு முன்னாடி தற்போதைய வரம்புகளை உறுதிப்படுத்துங்க.** LIS bands, SMS அளவீடுகள், குறிப்பிட்ட அளவு SI வரம்புகள் எல்லாம் MiFIR-ஓட சொந்த சட்ட text-ஆல இல்ல, ESMA/FCA technical standards-ஆல நிர்ணயிக்கப்படுது (அவ்வப்போது திருத்தப்படுது). Sections 6-8-ல ஒவ்வொரு குறிப்பிட்ட எண்ணையும் விளக்கமா எடுத்துக்கோங்க; ஒரு rules engine-ல encode பண்றதுக்கு முன்னாடி தற்போதைய எண்களை Section 10-ஓட அதிகாரப்பூர்வ மூலங்களுக்கு எதிரா உறுதிப்படுத்துங்க — [SFTR Reporting Deep Dive](../docs/2-reporting/module-16-sftr-reporting.html) §7 தன் சொந்த phase-in தேதிகள், field எண்ணிக்கைகளுக்கு தரும் அதே எச்சரிக்கை.

---

## 10. Regulatory Perspective — அதிகாரப்பூர்வ மூலங்கள் & Links

**Definition:** technical standards, வரம்புகள் காலப்போக்குல திருத்தப்படுறதால (Section 9-ஓட callout) இந்த module வேணும்னே முதன்மை regulatory மூலங்களை மறுசொல்லாம சுட்டிக்காட்டுது.

| Source | What It Covers | Link |
|---|---|---|
| **EUR-Lex — Directive 2014/65/EU (MiFID II)** | The full legal text of the Directive: authorization, conduct, market structure | [eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014L0065](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014L0065) |
| **EUR-Lex — Regulation (EU) 600/2014 (MiFIR)** | The full legal text of MiFIR: transparency, trading obligations, transaction reporting | [eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014R0600](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32014R0600) |
| **ESMA** | RTS 22 (transaction reporting), RTS 1/RTS 2 (transparency), FIRDS, SI/LIS threshold calibrations, Q&As | [www.esma.europa.eu](https://www.esma.europa.eu) |
| **FCA — UK MiFIR** | UK's onshored regime, UK-specific technical standards, the COBS/MAR Handbook chapters | [www.fca.org.uk](https://www.fca.org.uk) |
| **ESMA FIRDS** | The consolidated instrument reference-data system underlying Section 3's eligibility test | [registers.esma.europa.eu/publication/searchRegister?core=esma_registers_firds](https://registers.esma.europa.eu/publication/searchRegister?core=esma_registers_firds) |

**Takeaway:** ஒரு build பணிக்கு, Directive/Regulation texts-ஐ scope-க்கும் கடமைகளுக்கும் சட்ட மூலமாவும், ESMA/FCA technical standards-ஐ (RTS 22, RTS 1, RTS 2) சரியான field பட்டியல்களுக்கும், வரம்புகளுக்கும், ஒத்திவைப்பு windows-க்கும் மூலமாவும், FIRDS-ஐ "இந்த குறிப்பிட்ட ISIN இன்னிக்கு scope-ல இருக்கா"ங்கறதுக்கு operational மூலமாவும் நடத்துங்க — [SFTR Reporting Deep Dive](../docs/2-reporting/module-16-sftr-reporting.html) §9 தன் சொந்த regime-க்கு வரையற அதே மூன்று-வழிப் பிரிவு.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Bank Of Toronto | முழுக்க executing investment firm; SAP SE-க்கு registered Systematic Internaliser (Section 7b-8) |
| Maple Leaf Pension Fund | SAP SE trades-ல buy-side client (Sections 3, 5, 8) |
| Goldman Sachs | Trade 1-ல bilateral OTC counterparty (Section 3) |
| SAP SE | Section 3-ல இருந்து MiFID-eligible worked example-ஆ முழுக்க பயன்படுத்தப்படும் venue-admitted equity |
| Deutsche Börse Xetra | SAP SE admit ஆன EU Regulated Market — ஒவ்வொரு SAP SE trade-ஐயும் MiFID eligible ஆக்கும் உண்மை (Section 3) |
| ESMA | RTS 22/RTS 1/RTS 2 technical standards-ஐ வகுக்கும்; FIRDS-ஐ நடத்தும் |
| FCA | UK-ஓட National Competent Authority; UK MiFIR-ஐயும் Systematic Internalisers-ஐயும் மேற்பார்வை பண்ணும் |
| APA (Approved Publication Arrangement) | OTC post-trade transparency data-வை பொது சந்தைக்கு வெளியிடும் (Section 6-8) |
| ARM (Approved Reporting Mechanism) | ஒரு firm சார்பா NCA-க்கு transaction reports-ஐ transmit பண்ணும் விருப்ப இடைநிலை (Section 4) |

---

## Recap Quiz — Numbers Edition

1.  Section 3-ஐ பயன்படுத்தி, Trade 2-ஓட SAP SE shares மேல bespoke bilateral OTC equity option MiFID eligible-ஆ? எந்த குறிப்பிட்ட சோதனையை அது கடக்குது? அதே instrument-க்கு Test (a) ஏன் தோற்குது?
2.  Section 2-ஐ பயன்படுத்தி, ஒரு Transaction Report-ல தோன்றி ஒரு Transparency publication-ல ஒருபோதும் தோன்றாத ஒரு field-ஐயும், ஒரு Transparency publication-ல தோன்றி ஒரு Transaction Report-ஓட பொதுப் பக்கத்துல (அடையாளம் காணக்கூடிய வடிவத்துல) ஒருபோதும் தோன்றாத ஒண்ணையும் பெயரிடுங்க.
3.  Section 7a-ஐ பயன்படுத்தி, Bank Of Toronto ஒருபோதும் Systematic Internaliser ஆ register ஆகாம, client flow-க்கு எதிரா SAP SE shares-ல தொடர்ச்சியான, வழக்கமான bilateral OTC dealing business-ஐ நடத்த முடியுமா? ஏன்/ஏன் இல்ல?
4.  Section 6-ஐ பயன்படுத்தி, Bank Of Toronto ஒரு liquidity band-ல EUR 400,000 OTC trade-ஐ execute பண்றது, அந்த band-ஓட LIS threshold EUR 650,000 — ஒத்திவைக்கப்பட்ட publication கிடைக்குமா? இந்த trade எவ்வளவு வேகமா பொதுவில வருது என்பதுக்கு நடைமுறை விளைவு என்ன?
5.  Section 8-ஓட worked example-ஐ பயன்படுத்தி, அந்த trade தூண்டும் நாலு கடமைகளையும் பெயரிடுங்க; அந்த நாலுல எது ஒரு குறிப்பிட்ட, தேதியிட்ட filing இல்லாம தொடர் conduct standard?
6.  Section 4-ஐ பயன்படுத்தி, Bank Of Toronto-வும் Goldman Sachs-உம் ஒரே MiFID-eligible trade-ஓட எதிர் பக்கங்கள்ல இருந்தா, ஒரு EMIR Trade Repository செய்யற மாதிரி ஒரு regulator அவங்க ரெண்டு transaction reports-ஐயும் ஒன்றுக்கொன்று எதிரா pair செய்து reconcile பண்றாங்களா? ஒவ்வொரு தனி report-உம் எவ்வளவு முழுமையா இருக்கணும்ங்கறதுக்கு இது ஏன் முக்கியம்?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 14](../docs/2-reporting/module-14-regulatory-reporting.html): Regulatory Reporting**-க்குத் திரும்பலாம், அல்லது **[SFTR Reporting Deep Dive](../docs/2-reporting/module-16-sftr-reporting.html)**-ஐத் தொடரலாம்.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. ஆமா, Test (b) வழியா eligible — அதன் underlying (SAP SE shares) Deutsche Börse Xetra-ல trading-க்கு admit ஆகியிருக்கு. Test (a) தோற்குது, ஏன்னா OPTION CONTRACT தானே எந்த venue-லயும் admit ஆகலை, trade ஆகலை — அது Bank Of Toronto-க்கும் Maple Leaf Pension Fund-க்கும் இடையில மட்டும் இருக்கற bespoke bilateral instrument.**

Section 3-ஓட Trade 2 இந்த வேறுபாட்டை நேரடியா சொல்லுது: Test (a) report பண்ணப்படும் instrument-ஐ (option) பத்தி கேட்குது; Test (b) அந்த instrument-ஓட underlying-ஐ (shares) பத்தி கேட்குது — option தனக்கு venue இருப்பு இல்லாட்டியும், underlying-ஓட venue-admitted நிலை வழியா eligibility-ஐ பெறுது.

**2. Buyer/seller LEI (investment decision maker-ஓட அடையாளமும்) Transaction Report-ல (Section 5) தோன்றுது, Transparency publication-ல (Section 6) ஒருபோதும் தோன்றாது — அது counterparty அடையாளத்தை முழுசா நீக்குது. மாறாக, Transparency பக்கத்தில Transaction Report-ல இல்லாத தனித்துவமான எதுவும் இல்ல — Transaction Report பொருளாதார விவரத்தின் ஒரு கடுமையான superset, பொதுவில வெளியிடப்படாம regulator-க்கு மட்டும் கட்டுப்படுத்தப்பட்டது.**

Section 2-ஓட பக்கம்-பக்கம் diagram இதை வெளிப்படையா சொல்லுது: Transparency publication "விலை, volume, timestamp மட்டும் — counterparty பெயர்கள் அறவே இல்ல"ன்னு கொண்டிருக்கு; Transaction Report "முழு விவரம்: ரெண்டு parties-ஓட LEIs, trader ID, algo flags, விலை, quantity, venue code" கொண்டிருக்கு.

**3. இல்ல — ESMA-ஓட வரம்பை தன் dealing volume கடக்கும்போது, அந்த flow-ஐ ஒரு venue வழியா route பண்ணாம, இல்லன்னா Systematic Internaliser ஆ register ஆகாம இல்ல. STO-ஓட ad-hoc/irregular/infrequent carve-out (Section 7a) உண்மையிலயே அவ்வப்போதைய, விலை-உருவாக்காத bilateral trades-ஐ மட்டுமே பாதுகாக்குது; client flow-க்கு எதிரா வழக்கமான தொடர்ச்சியான OTC dealing business சரியா carve-out விலக்கும் pattern, firm-ஐ SI regime-க்குள்ள (Section 7b) தள்ளுது.**

Section 7a-ஓட worked example அதன் "Contrast" பத்தியில இதை நேரடியா சொல்லுது: ஒரு liquid EU share-ல முறையான, தொடர்ச்சியான bilateral dealing சரியா STO கட்டமைக்கப்படாத OTC-யா இருக்க அனுமதிக்காத scenario — அது venue-routed அல்லது SI-quoted ஆகணும்.

**4. இல்ல, ஒத்திவைக்கப்பட்ட publication கிடைக்காது — LIS ஒத்திவைப்புக்கு threshold-ஐ ஒப்பிட்டு பெருசா இருக்கணும்; 400,000 < 650,000 என்றா இந்த trade Large In Scale ஆகத் தகுதிபெறாது. நடைமுறை விளைவு: இந்த trade தொழில்நுட்ப ரீதியா சாத்தியமான அளவு நிகழ்நேரத்துக்கு நெருக்கமா வெளியிடப்படணும் — ஒத்திவைப்பு இல்ல — threshold-ஐத் தாண்டி தகுதிபெற்ற Trade 1-ஓட EUR 1,900,000-க்கு (Section 6) எதிர்மறை.**

*(குறிப்பு: ஆங்கில மூலத்தில் இந்த answer-ன் முதல் வாக்கியம் "Yes, deferred publication is available" என்று தொடங்கி, பின்னாடி தன்னைத்தானே திருத்திக்கொள்கிறது; சரியான பதில் "இல்ல" என்பதே.)* Section 6-ஓட worked example ஒப்பீட்டை சரியான திசையில காட்டுது: 1,900,000 > 650,000 Trade 1-ஐ ஒத்திவைப்புக்கு தகுதியாக்கியது. ஒரு சிறிய EUR 400,000 trade அதே threshold-க்கு கீழ உட்காருது, அதனால உடனே வெளியிடப்படுது — இந்தக் கேள்வி inequality-ஐ தலைகீழா படிக்கறதை பிடிக்க வடிவமைக்கப்பட்டது.

**5. Section 7-ஓட நாலு கடமைகளும்: (1) Transaction Reporting, (2) Post-Trade Transparency, (3) Best Execution, (4) STO/SI compliance. Best Execution தான் தொடர் conduct standard — ஒரு குறிப்பிட்ட தேதியில file பண்ணப்படாம, கேட்கும்போது நிரூபிக்கப்படுது — மற்ற மூணும் இந்த குறிப்பிட்ட trade-ஓட execution-உடன் இணைந்த, தூண்டப்பட்ட, தேதியிட்ட கடமைகள்.**

Section 8-ஓட worked example நாலையும் வெளிப்படையா எண்ணிடுது; Best Execution பத்தின அதன் சொந்த "தொடர் conduct கடமை, கேட்கும்போது நிரூபிக்கப்படுது, ஒரு trade-க்கு ஒரு filing இல்ல" சட்டகம் கேள்வியின் ரெண்டாவது பாதிக்கு நேரடி எழுத்துப் பதில்.

**6. இல்ல — MiFIR transaction reports ஒருபோதும் ஒன்றுக்கொன்று எதிரா pair ஆகாது, reconcile ஆகாது; ஒரு EMIR Trade Repository ரெண்டு counterparties-ஓட reports-ஐ பகிரப்பட்ட UTI-ல pair பண்றது போல இல்ல. இது முக்கியம், ஏன்னா ஒவ்வொரு firm-ஓட report-உம் தன்னளவுலயே சரியாவும் முழுமையாவும் இருக்கணும் — Bank Of Toronto-ஓட, Goldman Sachs-ஓட சுயாதீன reports இடையிலான field-நிலை முரண்பாட்டை, Module 14 §3-ஓட EMIR pairing/reconciliation process செய்யற மாதிரி, பிடிக்கும் regulator-பக்க cross-check இல்ல.**

Section 4-ஓட takeaway இதை நேரடியா சொல்லுது: "Module 14 §3-ஓட EMIR pairing செய்யற மாதிரி field-நிலை தவறை பிடிக்க reconciliation process இல்ல" — MiFIR-ஓட ஒரு-firm-ஒரு-பக்க reporting model-ஓட வடிவமைப்பு விளைவு, EMIR-ஓட உண்மையிலயே dual-sided, UTI-ல matched model இல்ல.
