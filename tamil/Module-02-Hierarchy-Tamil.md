# Module 2 — Asset Class → Product → Instrument → Trade (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **மூலம்:** English Module 2-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன். Diagrams-ம் numbers-ம் மூலத்தோட அப்படியே.

---

## 1. Hierarchy — Asset Class-ல இருந்து ஒரு Trade வரை

**Definition:** Reporting system-ல நீங்க பார்க்கற ஒவ்வொரு record-உம் நாலு layer hierarchy-ஓட அடில இருக்கும். மேல இருக்கற மூணு layers "*எது* trade ஆகுது"-ன்னு சொல்லும், அது மாறாது. அடில இருக்கற layer ஒரு *குறிப்பிட்ட நிகழ்வை* சொல்லும், ஒவ்வொரு தடவையும் புது record உருவாகும்.

Java-ல யோசிச்சா: மேல மூணு layers = reference data (master table), கடைசி layer = transaction table. Master-ல ஒரு தடவை look up பண்ணுவோம்; transaction table-ல தினமும் ஆயிரக்கணக்கான rows சேரும்.

```text
ASSET CLASS  ->  PRODUCT  ->  INSTRUMENT  ->  TRADE
========================================================

Asset Class:  Fixed Income
                    |
Product:       Government Bond
                    |
Instrument:    "Canada 5% Bond 2035"    (ISIN: CA1358XXXXX)
                    |
Trade:         Bank Of Toronto buys CAD 10,000,000 face value
               from Goldman Sachs @ Price 98.50, Trade Date 17-Aug-2026
```

| Layer | எந்தக் கேள்விக்குப் பதில் | Example value | ஒவ்வொரு trade-க்கும் மாறுமா? |
|---|---|---|---|
| **Asset Class** | "இது என்ன பெரிய வகை?" | Fixed Income | இல்ல — instrument-ஓட முழு வாழ்க்கைக்கும் ஒண்ணுதான் |
| **Product** | "Fixed Income-ல என்ன வகை?" | Government Bond | இல்ல |
| **Instrument / Security** | "எந்த குறிப்பிட்ட tradable பொருள்?" | Canada 5% Bond 2035 (ISIN CA1358XXXXX) | இல்ல — ஒரு instrument-ஐ ஆயிரக்கணக்கான trades reference பண்ணும் |
| **Trade** | "யார், யார்கிட்ட, எவ்வளவு, என்ன விலைக்கு, எப்போ?" | Bank Of Toronto CAD 10,000,000 @ 98.50, 17-Aug-2026 | **ஆமா** — ஒவ்வொரு தடவையும் புது record |

**Takeaway:** Asset Class, Product, Instrument = reference data — ஒரு தடவை பார்த்தா போதும், நகராது. Trade = transactional data — ஒரே Instrument-ஐ நோக்கி ஆயிரக்கணக்கான Trade records இருக்கலாம் ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html)-ல "One Issuance, Many Investors" மாதிரியே).

### கூடுதல் உதாரணம் — ஒரே Instrument, மூணு Trades

"Canada 5% Bond 2035" ஒரே ஒரு instrument. ஆனா ஒரே நாள்ல மூணு வேற வேற trades நடக்குது:

| Trade ID | Buyer | Seller | Face Value (CAD) | Price |
|---|---|---|---|---|
| T-001 | Bank Of Toronto | Goldman Sachs | 10,000,000 | 98.50 |
| T-002 | Maple Leaf Pension Fund | Bank Of Toronto | 50,000,000 | 98.55 |
| T-003 | Bank Of Toronto | Interbank dealer | 5,000,000 | 98.48 |

மூணு rows-லயும் Asset Class, Product, Instrument, ISIN — எல்லாமே ஒரே மாதிரி. Trade ID, parties, quantity, price மட்டும்தான் வேற. Database design-ல இது foreign key: `trade.instrument_id → instrument.id`.

---

## 2. Instrument Identifiers — ISIN vs CUSIP vs Ticker

**Definitions:**

-   **ISIN** (International Securities Identification Number) — 12 characters, உலகம் முழுக்க unique: 2-letter country code + 9-character national identifier + 1 check digit. Bonds, equities, இப்போ derivatives-லயும் பயன்படற de-facto global standard.
-   **CUSIP** (Committee on Uniform Securities Identification Procedures) — 9 characters, North America (US & Canada) மட்டும். North American securities-க்கு CUSIP, ISIN-க்குள்ளயே embed ஆகியிருக்கும்.
-   **Ticker** (trading symbol) — ஒரு குறிப்பிட்ட exchange-ல பயன்படற குறுகிய, படிக்கக்கூடிய code (உதா., `AAPL`). இது உலகளவுல unique *இல்ல* — ஒரே ticker வேற exchange-ல வேற பொருளை குறிக்கலாம்; முக்கியமா exchange-traded instruments-க்கு (equities) மட்டும் அர்த்தமுள்ளது. Bonds, OTC derivatives-க்கு ticker கிடையாது.

```text
IDENTIFYING THE SAME KIND OF THING, THREE DIFFERENT WAYS
=============================================================

Apple Inc. common stock (equity, exchange-traded on NASDAQ):
  Ticker : AAPL
  CUSIP  : 037833100
  ISIN   : US 037833100 5
           ^^ US (country) + 037833100 (CUSIP, embedded) + 5 (check digit)

Canada 5% Bond 2035 (government bond — NOT exchange-traded):
  Ticker : (none — bonds don't trade under exchange tickers)
  CUSIP  : 135087XX1   (illustrative)
  ISIN   : CA 135087XX1 X
           ^^ CA (country) + CUSIP-style body + check digit
```

| Identifier | Scope | பொதுவான asset classes | Example |
|---|---|---|---|
| **ISIN** | Global — உலகம் முழுக்க ஒரு security-க்கு ஒண்ணு | எல்லாம்: equities, bonds, சில derivatives (UPI வழியா) | `US0378331005` |
| **CUSIP** | North America மட்டும் | US/Canada-ல issue/trade ஆகற equities & bonds | `037833100` |
| **Ticker** | Exchange-specific, globally unique இல்ல | முக்கியமா exchange-traded equities | `AAPL` |

**Takeaway:** Reporting record-ல Ticker மட்டும் இருந்து ISIN/CUSIP இல்லன்னா, அது exchange feed-ல இருந்து வந்த equity data-ன்னு கிட்டத்தட்ட உறுதி — bond அல்லது OTC derivative-க்கு பொதுவா ISIN இருக்கும்.

---

## 3. Core Trade Fields — Trade ID, Quantity vs Notional, Price, Currency

**Definitions:**

-   **Trade ID** — Trade capture ஆன உடனே, trade பண்ற firm-ஓட trading system generate பண்ற unique reference. ஒரு transaction-ஐ அதன் முழு lifecycle-லயும் ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html)) இணைக்கற நூல்.
-   **Quantity** — equities, bonds-க்கு, வாங்கின/வித்த உண்மையான அளவு (shares, அல்லது face value). Settlement-ல இது உண்மையிலயே நகரும்.
-   **Notional** — derivatives-க்கு, periodic cash flows கணக்கிட மட்டும் பயன்படற reference principal. இது ஒருபோதும் நகராது ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) Section 8).
-   **Price** — ஒரு unit-ஓட விலை, அந்த instrument-ஓட convention-படி: per share, per 100 face value, அல்லது FX rate.
-   **Currency** — trade denominate/settle ஆகற currency.

### Worked example — Bank Of Toronto-ஓட blotter, மூணு asset classes, ஒரு நாள்

```text
BANK OF TORONTO'S BLOTTER — 17 AUGUST 2026
==============================================

TRADE 1 — BOND
  Trade ID   : BOT-2026-000512
  Instrument : Canada 5% Bond 2035  (ISIN CA1358XXXXX)
  Quantity   : CAD 10,000,000 face value
  Price      : 98.50  (per 100 face value)
  Currency   : CAD
  Value      : 10,000,000 x 98.50 / 100 = CAD 9,850,000

TRADE 2 — EQUITY
  Trade ID   : BOT-2026-000513
  Instrument : Apple Inc. common stock  (Ticker AAPL, ISIN US0378331005)
  Quantity   : 5,000 shares
  Price      : USD 227.15 per share
  Currency   : USD
  Value      : 5,000 x 227.15 = USD 1,135,750

TRADE 3 — OTC DERIVATIVE
  Trade ID   : BOT-2026-000514
  Instrument : 5Y CAD Interest Rate Swap vs Goldman Sachs
  Notional   : CAD 100,000,000   (reference only — NEVER exchanged)
  Price      : Bank Of Toronto pays Fixed 4.00% / receives Floating
  Currency   : CAD
  Value      : no upfront value exchanged — only periodic net cash flows
```

**Takeaway:** "Quantity"-யும் "Notional"-உம் ஒண்ணில்ல — சில systems ஒரே database column-ஐ ரெண்டுக்கும் பயன்படுத்தினாலும். Quantity நகரும் — bonds, shares settlement-ல உண்மையிலயே கை மாறும். Notional நகராது — அதுலருந்து கணக்கிட்ட cash flows மட்டும்தான் நகரும்.

---

## 4. ஒரே Hierarchy, மூணு Asset Classes — பக்கம் பக்கமா

அதே நாலு-layer mental model எல்லா இடத்துலயும் பொருந்தும்; ஆனா அடில (Trade level) உண்மையில *என்ன settle ஆகுது*ங்கறது asset class-க்கு ஏத்தபடி மாறும். அதுக்கு தனித்தனி modules வரும் ([Module 4](../docs/1-capital-markets/module-04-equity.html), [Module 5](../docs/1-capital-markets/module-05-fixed-income.html), [Module 6](../docs/1-capital-markets/module-06-fx.html)/7).

| Cash / Derivative | Asset Class | Product | Instrument (example) | Identifier | "Size" field | உண்மையில என்ன settle ஆகுது |
|---|---|---|---|---|---|---|
| CASH | Fixed Income | Government Bond | Canada 5% Bond 2035 | ISIN | Quantity (face value) | Face value + accrued interest |
| CASH | Equity | Common Stock | Apple Inc. shares | Ticker / ISIN | Quantity (shares) | Shares + cash |
| DERIVATIVE | Derivatives (OTC) | Interest Rate Swap | 5Y CAD IRS | பொதுவா ISIN இல்ல (புது rules-ல UPI) | Notional (reference மட்டும்) | Net cash-flow வித்தியாசம் மட்டும் |

**Takeaway:** Asset Class, Product, Instrument-ஐ சொல்ல முடிஞ்சா, அந்த trade-ல "settlement"-னா என்னன்னு Module 4/5/6 திறக்கறதுக்கு முன்னாடியே ஊகிக்கலாம் — bonds, equities உண்மையான பொருளை நகர்த்தும்; OTC derivatives (பெரும்பாலும்) நகர்த்தாது.

---

## 5. Full Field Reference — Trade record-ல ஒவ்வொரு field-க்கும் அர்த்தம்

Section 3-ல வந்த Trade 1 (bond)-ஐ running example-ஆ வெச்சு:

| Field | அர்த்தம் | Example value | Static (reference) ஆ, இல்ல ஒவ்வொரு trade-க்கும் புதுசா? |
|---|---|---|---|
| Asset Class | பெரிய வகை | Fixed Income | Static |
| Product | Asset class-க்குள்ள வகை | Government Bond | Static |
| Instrument / Security | குறிப்பிட்ட tradable பொருள் | Canada 5% Bond 2035 | Static |
| ISIN | Instrument-ஓட global identifier | CA1358XXXXX | Static |
| CUSIP | Instrument-ஓட North American identifier | 135087XX1 | Static |
| Ticker | Exchange trading symbol (முக்கியமா equities) | இந்த bond-க்கு n/a | Static |
| Trade ID | இந்த ஒரு transaction-ஓட unique reference | BOT-2026-000512 | **ஒவ்வொரு trade-க்கும் புதுசு** |
| Notional | Reference principal (derivatives மட்டும்) | இந்த bond-க்கு n/a | **ஒவ்வொரு trade-க்கும் புதுசு**, பொருந்தினா |
| Quantity | உண்மையில transact ஆன அளவு | CAD 10,000,000 face value | **ஒவ்வொரு trade-க்கும் புதுசு** |
| Price | ஒரு unit-ஓட விலை | 98.50 | **ஒவ்வொரு trade-க்கும் புதுசு** |
| Currency | Denomination / settlement currency | CAD | பொதுவா instrument-க்கு static, ஆனா ஒவ்வொரு trade-லயும் confirm ஆகும் |

**Takeaway:** ஒரு reporting field-ஐ debug பண்ணும்போது முதல் கேள்வி: "இது static reference-data field-ஆ (Asset Class → Instrument), இல்ல per-trade transactional field-ஆ (Trade ID → Currency)?" — இதுவே நீங்க security master-ஐ பார்க்கணுமா, trade capture system-ஐ பார்க்கணுமான்னு சொல்லிடும்.

---

## 6. முக்கிய Asset Classes & Products — Cash Instruments (ISDA classification இல்லாதவை)

**Definition:** "Cash" (அல்லது "physical") instruments = தானாகவே இருந்து, தானாகவே settle ஆகற securities — bond அல்லது share, issue ஆன உடனே ஒரு உண்மையான, deliver பண்ணக்கூடிய பொருள். இவற்றை **ISIN/CUSIP** (எந்த instrument) + **CFI code** (ISO 10962 — *Classification of Financial Instruments*, 6-character code; உதா., "இது debt instrument, straight bond, fixed rate, registered form") வெச்சு classify பண்றோம். Cash instruments-க்கு ISDA classification **கிடையாது** — ISDA taxonomy derivatives-க்கு மட்டும் (Section 7).

| Asset Class | Key Products | Example Instrument | பொதுவான Identifier | "Size"-னா என்ன | உண்மையில என்ன settle ஆகுது |
|---|---|---|---|---|---|
| **Fixed Income — Government** | Treasury Bills, Government Bonds, Sovereign Bonds | Canada 5% Bond 2035 | ISIN | Face value (Quantity) | Face value + accrued interest |
| **Fixed Income — Corporate** | Investment-Grade Bonds, High-Yield Bonds, Convertible Bonds | Bank Of Toronto 4.5% Bond 2030 | ISIN | Face value (Quantity) | Face value + accrued interest |
| **Fixed Income — Securitized** | MBS (Mortgage-Backed), ABS (Asset-Backed), CLO (Collateralized Loan Obligation) | Fannie Mae 30Y MBS Pool #12345 | ISIN / CUSIP | Face value (Quantity), காலப்போக்குல குறையும் | Face value (amortizing) + interest |
| **Money Market** | T-Bills, Commercial Paper, Certificates of Deposit, Repo | 90-Day Commercial Paper — Bank Of Toronto | ISIN / CUSIP | Face value, குறுகிய maturity (<1yr) | Maturity-ல face value (பொதுவா discount-ல issue) |
| **Equity — Common** | Common Stock, ADR/GDR (cross-listed shares) | Apple Inc. common stock | Ticker / ISIN | Shares எண்ணிக்கை (Quantity) | Shares + cash |
| **Equity — Preferred/Fund** | Preferred Stock, ETFs, Mutual Fund Units | S&P 500 ETF units | Ticker / ISIN | Units எண்ணிக்கை (Quantity) | Units + cash |
| **FX — Spot/Cash** | FX Spot | USD/CAD spot | Currency pair (ISIN இல்ல) | Notional currency amount | ரெண்டு currencies-உம், T+1/T+2 ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §6) |
| **Commodity — Physical** | Physical Gold, Physical Oil (வங்கியோட சொந்த book-ல அரிது — பொதுவா derivatives வழியாத்தான்) | 100oz Gold Bar | ISIN இல்ல — commodity code | Physical unit (oz, barrel) | Physical delivery (அரிது) அல்லது cash-settled equivalent |

**Takeaway:** மேல இருக்கற ஒவ்வொரு row-வும் நீங்க கோட்பாட்டளவுல கைல வெச்சுக்கக்கூடிய பொருள் — bond certificate (இப்போ book-entry, ஆனா "ஒரு பொருள்"), ஒரு share, ஒரு gold bar. அதனாலதான் இவற்றுக்கு ISDA classification இல்ல: ISDA *ரெண்டு parties-க்கு இடையிலான contracts*-ஐ மட்டும்தான் classify பண்ணும், அதாவது derivative (Section 7) — underlying பொருளை இல்ல.

---

## 7. ISDA-ன்னா என்ன? Derivative-க்கு ஏன் "ISDA Classification" தேவை?

**Definition:** **ISDA** (International Swaps and Derivatives Association) ஒரு trade body; மக்கள் அடிக்கடி குழப்பிக்கற ரெண்டு தனி விஷயங்களை standardize பண்ணுது:

1.  **ISDA Master Agreement** — ரெண்டு counterparties ஒரே ஒரு தடவை கையெழுத்து போடற legal contract template; அதுக்கப்புறம் அவங்களுக்குள்ள நடக்கற *ஒவ்வொரு* OTC derivative trade-யையும் இது ஆளும் (netting, default, collateral terms). இது [Module 12](../docs/1-capital-markets/module-12-collateral-margin.html) (Collateral & Margin)-ல விரிவா.
2.  **ISDA Product Taxonomy** — ஒரு derivative என்ன வகைன்னு label பண்ண ஒரு standardized *classification scheme*. இதுதான் இந்த module-க்கும், regulatory reporting-க்கும் ([Module 14](../docs/2-reporting/module-14-regulatory-reporting.html)) சம்பந்தம்: Bank Of Toronto ஒரு OTC derivative trade-ஐ EMIR, MiFIR, அல்லது Dodd-Frank/CFTC rules-படி Trade Repository-க்கு report பண்ணும்போது, கட்டாய fields-ல ஒண்ணு "ISDA Taxonomy" (அல்லது அதிலிருந்து derive ஆன field) — ஒவ்வொரு firm-ஓட மில்லியன் கணக்கான trades-ஐ regulator-ஓட systems, ஒவ்வொரு contract-ஐயும் கையால படிக்காம, ஒப்பிடக்கூடிய categories-ஆ பிரிக்கற வழி இதுதான்.

ISDA Product Taxonomy **நாலு-level hierarchy** — இது Section 1-ல பார்த்த Asset Class → Product → Instrument → Trade hierarchy-ல இருந்து வேற என்பதை கவனிங்க; இது regulatory நோக்கத்துக்காக *product-ஐயே* classify பண்ணுது, நம்ம "Product" layer-ஐ விட ஒரு படி நுணுக்கமானது:

```text
ISDA PRODUCT TAXONOMY  —  four levels
=========================================

  ISDA Asset Class   ->   Base Product   ->   Sub-Product   ->   Transaction Type
  ==================================================================================

  Interest Rate      ->   IRSwap         ->   FixedFloat     ->   Swap
  Credit             ->   SingleName     ->   Corporate      ->   CreditDefaultSwap
  Foreign Exchange   ->   Forward        ->   Deliverable    ->   Forward
  Equity             ->   Swap           ->   PriceReturn    ->   Swap
  Commodity          ->   Energy         ->   Oil            ->   Swap
```

Derivatives-க்கு **அதிகாரப்பூர்வமா சரியா ஐந்து ISDA Asset Classes** இருக்கு — cash + derivative products எல்லாத்தையும் சேர்த்துப் பார்க்கும்போது வரும் திறந்த-முனை "Asset Class" values-ஐ விட ரொம்பக் குறுகிய, நிலையான பட்டியல்:

| ISDA Asset Class | எளிய தமிழ்ல அர்த்தம் | Desk-ல பொதுவான பேரு |
|---|---|---|
| **Interest Rate** | Cash flows interest rates-ஐ பொறுத்தது | "Rates" |
| **Credit** | Default/credit event-ஐ பொறுத்து payout | "Credit" |
| **Foreign Exchange** | Currency rates-ஐ பொறுத்து payout | "FX" |
| **Equity** | Stock/index விலையைப் பொறுத்து payout | "Equity Derivatives" |
| **Commodity** | Physical commodity விலையைப் பொறுத்து payout | "Commodities" |

**Takeaway:** Reporting record-ல "ISDA Taxonomy" அல்லது "ISDA Asset Class" field நிரம்பியிருந்தா, அது OTC derivative தான், cash bond அல்லது cash equity இல்ல — cash instruments-க்கு இந்த field இருக்காது, ஏன்னா ISDA அவற்றை classify பண்றதில்ல (Section 6).

---

## 8. ISDA Derivative Asset Classes & Products — Master Table

இது Section 6-ல இருக்கற cash-instrument table-ஓட derivatives பதிப்பு — ஆனா இங்க ஒவ்வொரு row-வும் ஒரு **contract**, deliver பண்ணக்கூடிய பொருள் இல்ல; அதனால "Notional", "What actually settles" columns Section 6-ல இருந்து முற்றிலும் வேறுபடுவதை கவனிங்க.

| ISDA Asset Class | Key Products (Base Product) | Example Instrument | Sub-Product / Transaction Type | உண்மையில என்ன settle ஆகுது |
|---|---|---|---|---|
| **Interest Rate** | Interest Rate Swap (IRS), Forward Rate Agreement (FRA), Swaption, Cap/Floor, Cross-Currency Swap | 5Y CAD IRS, Bank Of Toronto fixed pay / floating receive | FixedFloat / Swap | Periodic net cash flows மட்டும் ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) |
| **Credit** | Single-Name CDS, CDS Index (CDX, iTraxx), Total Return Swap (TRS) | "Northern Rail Corp"-ஓட 5Y CDS, Bank Of Toronto protection வாங்குது | SingleName-Corporate / CreditDefaultSwap | Periodic premium; credit event நடந்தா மட்டும் payout (Section 9) |
| **Foreign Exchange** | FX Forward, FX Swap, FX Option, Non-Deliverable Forward (NDF) | 3M USD/CAD Forward, Bank Of Toronto USD வாங்குது | Forward-Deliverable / Forward | Value date-ல ரெண்டு currency amounts-உம் (NDF-ல net cash) (Section 10) |
| **Equity** | Equity Option, Equity Swap, Equity Index Future, Variance Swap | Apple Inc. shares மேல Total Return Swap | Swap-PriceReturn / Swap | Share விலை/dividends-ஐ பின்தொடரும் periodic net cash flows |
| **Commodity** | Commodity Swap, Commodity Forward, Commodity Option | 1Y Brent Crude Oil Swap, notional 100,000 barrels | Energy-Oil / Swap | Commodity price index-ஐ reference பண்ற periodic net cash flows (உண்மையான barrels கிட்டத்தட்ட இல்ல) |

**Takeaway:** ஐந்து ISDA asset classes-லயும் [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8-ல பார்த்த pattern திரும்பத் திரும்ப வரும் — cash flows கணக்கிட மட்டும் ஒரு Notional/reference amount trade-ல இருக்கும்; net வித்தியாசம் (Credit-ல contingent payout) மட்டும்தான் நகரும். Underlying-ஓட physical settlement (உதா., உண்மையான oil barrels) OTC derivatives-ல விதிவிலக்கு, வழக்கம் இல்ல.

---

## 9. Worked Example — Credit Default Swap (CDS)

**Definition:** CDS = ஒரு company default ஆகும் risk-க்கு எதிரான insurance மாதிரியான protection. **Protection buyer**, **protection seller**-க்கு periodic premium (basis points per year-ல quote பண்ற "spread") கொடுப்பாங்க. **Reference entity** (எந்த company-க்கு எதிரா insure பண்றோமோ அது) ஒரு வரையறுக்கப்பட்ட **credit event** (default, bankruptcy, restructuring) சந்திச்சா, protection seller, buyer-க்கு loss amount கொடுப்பாங்க — இல்லன்னா seller premium-ஐ வாங்கிக்கிட்டு எதுவும் கொடுக்கமாட்டாங்க.

**CDS-ல ரெண்டு parties மட்டும்தான் — reference entity அதுல ஒரு party இல்ல.** Northern Rail Corp இந்த contract-ஓட *subject*, *party* இல்ல: அது எதுலயும் கையெழுத்து போடலை, எதுவும் கொடுக்காது, எதுவும் வாங்காது, இந்த trade இருக்கறதே அதுக்குத் தெரியாது. இணைப்பு reference மட்டும்தான் — அதோட credit behavior, அந்த ரெண்டு உண்மையான counterparties-ஓட cash flows-ஐ தூண்டற condition. வேறொருத்தர் வீட்டுக்கு எடுக்கற insurance மாதிரி: வீடு எரிஞ்சா payout கிடைக்கும், ஆனா வீட்டு உரிமையாளர் அந்த policy-ல party இல்ல.

```text
THREE NAMES, ONLY TWO PARTIES TO THE CONTRACT
========================================================================

  Bank Of Toronto  <====  CDS CONTRACT  ====>  Goldman Sachs
  (protection buyer)     (bilateral,            (protection seller)
                          just these two)

                    Northern Rail Corp
                 (reference entity -- outside the
                  contract, referenced BY it, not
                  a party TO it)

  -> Bank Of Toronto pays Goldman Sachs premium; Goldman Sachs pays
     Bank Of Toronto a loss amount IF Northern Rail Corp defaults.
     Northern Rail Corp itself pays or receives NOTHING under this
     contract, ever.
```

நடைமுறைல protection buyer-க்கு reference entity மேல வேற ஒரு exposure இருக்கும் — உதா., Bank Of Toronto, Northern Rail Corp bonds வெச்சிருக்கலாம், அல்லது நேரடியா கடன் கொடுத்திருக்கலாம் — அந்த தனி credit risk-ஐ Goldman Sachs-க்கு மாத்த CDS-ஐ பயன்படுத்தும். ஆனா CDS contract-க்கே அந்த exposure இருக்கணும்னு தேவையில்ல; அடிப்படை position எதுவும் இல்லாம, Northern Rail Corp-ஓட credit தரத்தை *ஊகிக்க* (speculate) கூட வாங்கலாம்.

```text
CREDIT DEFAULT SWAP  —  Bank Of Toronto buys protection from Goldman Sachs
================================================================================

  Reference Entity : Northern Rail Corp (a corporate borrower — fictional)
  Notional          : CAD 20,000,000  (reference only — never exchanged upfront)
  Spread            : 120 bps (1.20% per year)
  Maturity          : 5 years, quarterly premium payments
  Recovery Rate     : 40% (assumed, if default occurs)

SCENARIO A — No default this quarter (the normal case)
----------------------------------------------------------
  Quarterly premium (Bank Of Toronto pays Goldman Sachs):
    20,000,000 x 1.20% x (91 / 365) = CAD 59,836

  Bank Of Toronto  ---- pays CAD 59,836 ---->  Goldman Sachs
  (repeats every quarter for 5 years = 20 payments, unless default happens first)

SCENARIO B — Northern Rail Corp (the reference entity) defaults during the 5 years
----------------------------------------------------------
  Loss Given Default = Notional x (1 - Recovery Rate)
                      = 20,000,000 x (1 - 0.40)
                      = CAD 12,000,000

  Goldman Sachs  ---- pays CAD 12,000,000 ---->  Bank Of Toronto
  (premium payments then stop — the contract has been triggered)
```

**ISDA classification:** Asset Class = **Credit**, Base Product = **SingleName**, Sub-Product = **Corporate**, Transaction Type = **CreditDefaultSwap**. இந்த trade-க்கான Trade Repository report இந்த நாலு-level label-ஐத்தான் சுமக்கும்.

**Takeaway:** CAD 20,000,000 notional மேல உள்ள ரெண்டு cash flows-லயும் வரவே இல்ல — அது premium கணக்கிட multiplier, loss-given-default கணக்கிட base அவ்வளவுதான். Bank Of Toronto உண்மையிலயே CAD 12,000,000 பார்க்குமா இல்லையா என்பது, Northern Rail Corp default ஆகுதா இல்லையாங்கறதை முழுசா பொறுத்தது; பெரும்பாலான CDS contracts சின்ன quarterly premium மட்டும் கட்டி, முழு காலமும் ஓடி, trigger ஆகாமலே முடியும்.

### கூடுதல் உதாரணம் — 5 வருஷ CDS-ல மொத்தம் எவ்வளவு premium?

Scenario A-ல ஒரு quarter-க்கு CAD 59,836. 20 quarters-க்கு: 59,836 × 20 ≈ **CAD 1,196,720** (default ஆகாம முழுசா ஓடினா). அதே நேரம் default ஆனா payout CAD 12,000,000 — அதாவது premium-ஐ விட சுமார் 10 மடங்கு. இதுதான் insurance logic: சின்ன, உறுதியான செலவு vs. பெரிய, நிச்சயமில்லாத பாதுகாப்பு.

---

## 10. Worked Example — FX Forward

**Definition:** FX Forward = எதிர்காலத்துல நடக்கப்போற currency exchange-க்கான exchange rate-ஐ இன்னிக்கே lock பண்றது. FX Spot (T+1/T+2-ல settle, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html)) போல இல்லாம, Forward-ஓட value date வாரங்கள் அல்லது மாதங்கள் தள்ளி இருக்கலாம் — இன்னிக்கும் அன்னிக்கும் நடுவுல rate மாறுவதுனால வர்ற risk இல்லாம, தெரிஞ்ச எதிர்கால cash தேவையை hedge பண்ண இது உதவும்.

> **Complete worked example இருக்கு.** இதே trade-ஐ முழு lifecycle-ஓட — trade capture, MTM, settlement, regulatory reporting — பார்க்க [FX Forward — Complete Example](../examples/FX_Forward_Complete_Example.html) பாருங்க.

```text
FX FORWARD  —  Bank Of Toronto needs USD 10,000,000 in 3 months
========================================================================

  Today's Spot Rate (USD/CAD)     : 1.3500   (1 USD = CAD 1.3500)
  3-Month Forward Points          : +0.0045  (driven by CAD-USD interest rate gap)
  3-Month Forward Rate            : 1.3545   (1.3500 + 0.0045)

  Bank Of Toronto agrees TODAY with Goldman Sachs:
    In 3 months, Bank Of Toronto pays CAD 13,545,000
                 and receives USD 10,000,000
    (10,000,000 x 1.3545 = 13,545,000)

  -> No cash or currency moves today. Only the RATE is locked in now.

3 MONTHS LATER — value date arrives, two possible outcomes:
--------------------------------------------------------------
  If spot has moved to 1.3700 (USD got MORE expensive):
    Bank Of Toronto still only pays 13,545,000, not 13,700,000
    -> Forward SAVED Bank Of Toronto CAD 155,000 vs. buying at spot then.

  If spot has moved to 1.3400 (USD got CHEAPER):
    Bank Of Toronto still pays 13,545,000, even though spot would have
    only cost 13,400,000
    -> Forward COST Bank Of Toronto CAD 145,000 vs. buying at spot then.
       This is not "the trade going wrong" — it's the price of certainty:
       Bank Of Toronto hedged a known future need and gave up any chance
       of a better rate in exchange for eliminating the risk of a worse one.
```

**ISDA classification:** Asset Class = **Foreign Exchange**, Base Product = **Forward**, Sub-Product = **Deliverable** (ரெண்டு currencies-உம் உண்மையில பரிமாறப்படும் — **NDF**-ல ஒரு currency freely convertible இல்லாததால் cash வித்தியாசம் மட்டும் settle ஆகும்), Transaction Type = **Forward**.

**Takeaway:** Interest Rate Swap-ஓட notional-ஐ போல இல்லாம், Forward-ஓட Notional (இங்க USD 10,000,000) *கடைசில settle ஆகும்* — Forward = ஒரே ஒரு எதிர்கால தேதில ரெண்டு உண்மையான currency amounts-ஐ மாத்திக்கற வாக்குறுதி; reference number-ல இருந்து கணக்கிட்ட periodic net cash flows இல்ல.

---

## 11. Extended Hierarchy — Cash + Derivatives பக்கம் பக்கமா, ISDA Tie-In-ஓட

Section 4-ல இருந்த மூணு-row table-ஐ, Sections 6–10-ல அறிமுகமான முழு set-க்கு நீட்டியிருக்கு — தெரியாத instrument-ஐ எங்க வைக்கணும்னு தெரியாதப்போ திரும்பத் திரும்ப பார்க்க வேண்டிய ஒரே reference table.

| Cash / Derivative | Asset Class | Product | Example Instrument | Identifier | ISDA Classification (derivatives மட்டும்) | உண்மையில என்ன settle ஆகுது |
|---|---|---|---|---|---|---|
| CASH | Fixed Income | Government Bond | Canada 5% Bond 2035 | ISIN | n/a — cash instrument | Face value + accrued interest |
| CASH | Fixed Income | Corporate Bond | Bank Of Toronto 4.5% Bond 2030 | ISIN | n/a — cash instrument | Face value + accrued interest |
| CASH | Money Market | Commercial Paper | 90-Day CP — Bank Of Toronto | ISIN/CUSIP | n/a — cash instrument | Maturity-ல face value |
| CASH | Equity | Common Stock | Apple Inc. shares | Ticker/ISIN | n/a — cash instrument | Shares + cash |
| CASH | FX | FX Spot | USD/CAD spot | Currency pair | n/a — cash/spot, derivative இல்ல | ரெண்டு currencies-உம், T+1/T+2 |
| DERIVATIVE | Interest Rate Derivative | Interest Rate Swap | 5Y CAD IRS | UPI (ISIN இல்ல) | Interest Rate / IRSwap / FixedFloat / Swap | Net cash flows மட்டும் |
| DERIVATIVE | Credit Derivative | Credit Default Swap | 5Y CDS on Northern Rail Corp | UPI (ISIN இல்ல) | Credit / SingleName / Corporate / CreditDefaultSwap | Premium; default-ல contingent payout |
| DERIVATIVE | FX Derivative | FX Forward | 3M USD/CAD Forward | UPI (ISIN இல்ல) | Foreign Exchange / Forward / Deliverable / Forward | Value date-ல ரெண்டு currencies-உம் |
| DERIVATIVE | Equity Derivative | Equity Swap | TRS on Apple Inc. shares | UPI (ISIN இல்ல) | Equity / Swap / PriceReturn / Swap | விலை + dividends-ஐ பின்தொடரும் net cash flows |
| DERIVATIVE | Commodity Derivative | Commodity Swap | 1Y Brent Crude Swap | UPI (ISIN இல்ல) | Commodity / Energy / Oil / Swap | Net cash flows மட்டும் |

**Takeaway:** ஒரு reporting record-ஐ உடனே sanity-check பண்ண ஒரு வழி — ISIN இருந்தா அது கிட்டத்தட்ட cash instrument (மேல rows); UPI (Unique Product Identifier — ad-hoc OTC identifiers-ஐ மாத்தற புது global standard) + ISDA Taxonomy field இருந்தா அது derivative (கீழ rows). இந்த ஒரே வேறுபாடுதான் valuation, collateral, regulatory reporting-க்கான முற்றிலும் வேற downstream systems-க்கு உங்களை வழிநடத்தும் (Modules 8–12).

---

## 12. UPI — Unique Product Identifier: Theory & Worked Example

> **Deep dive இருக்கு:** இந்த section UPI (மற்றும் UTI)-ஐ, இனி curriculum-க்குத் தேவையான அளவுல பார்க்குது. UTI-ஓட structure, generation waterfall, ஆறு asset classes-க்கான UPI reference-data templates, ANNA-DSB உண்மையில எப்படி இயங்குது (MiFIR-க்கான OTC-derivative ISINs வழங்கற தனிப் பங்கு உட்பட), ஒவ்வொரு regime-லயும் எந்த identifier கட்டாயம் என்ற map — இவற்றுக்கு [UPI, UTI & ANNA-DSB Deep Dive](../docs/2-reporting/module-18-upi-uti-dsb-deep-dive.html) பாருங்க.

**Definition:** **UPI (Unique Product Identifier)** = OTC derivative *product*-க்கான global, standardized identifier — derivatives உலகத்துல ISIN-க்கு மிக நெருக்கமான விஷயம், ஆனா ஒரு குறிப்பிட்ட trade-ஐ இல்ல, ஒரு **product template**-ஐ identify பண்ணுது. இதை வழங்குவது **DSB (Derivatives Service Bureau)** — உலகம் முழுக்க ISIN issuance-ஐ ஒருங்கிணைக்கற அதே அமைப்பான ANNA-ஓட subsidiary — **CPMI-IOSCO** தொழில்நுட்ப வழிகாட்டுதல்படி. சுமார் 2022–2024 முதல், முக்கிய jurisdictions-ல regulators (CFTC, EMIR/UK EMIR rewrites, மற்றவை — Abbreviations Glossary §9 பாருங்க) OTC derivative trade reports-ல UPI-ஐ *கட்டாய* field ஆக்கியிருக்காங்க; firm-க்கு firm வேறுபடற internal product codes-ஓட குழப்பத்தை மாத்தி, firms-க்கு இடையில data-வை ஒப்பிட regulators-க்கு வழி செய்ய.

குழப்பம் தர்ற முக்கிய விஷயம்: **UPI product-ஐ identify பண்ணுது; trade-ஐயும் இல்ல, குறிப்பிட்ட counterparty pair-ஐயும் இல்ல.** நாலு identifier வகைகளையும் பக்கம் பக்கமா வெச்சா இது எளிதா புரியும்:

| Identifier | எதை identify பண்ணுது | எதுக்கு unique… | Example |
|---|---|---|---|
| **ISIN** | ஒரு குறிப்பிட்ட cash security | ஒரு instrument, பல trades reference பண்ணும் | `US0378331005` (Apple common stock) |
| **UPI** | ஒரு derivative *product template* (asset class + type + underlier + முக்கிய economic terms) | ஒரு product, பல trades reference பண்ணும் — வேற வேற counterparties-க்கு நடுவுலயும் | "CORRA-ஐ reference பண்ற 5Y CAD Fixed-Float IRS"-க்கு ஒரு UPI |
| **UTI** | ஒரு குறிப்பிட்ட derivative *transaction* | சரியா ஒரு trade, execution-ப்போ generate ஆகும் | Bank Of Toronto ஒரு swap execute பண்ற ஒவ்வொரு தடவையும் புது UTI |
| **Trade ID** | Firm-ஓட சொந்த internal transaction reference | சரியா ஒரு trade, ஆனா அந்த firm-ஓட systems-க்குள்ள மட்டும் அர்த்தமுள்ளது | `BOT-2026-000514` |

இந்த pattern Section 1-ஓட hierarchy-யை கிட்டத்தட்ட அப்படியே பிரதிபலிக்குது: UPI "Product/Instrument" level-ல உட்காருது (reference data, ஒரு தடவை பார்த்து தொடர்ந்து reuse), UTI-யும் Trade ID-யும் "Trade" level-ல (ஒவ்வொரு தடவையும் புது value). Cash instruments-ஓட வித்தியாசம்: ஒரு derivative-க்கு *instrument* level-ல ISIN போல "ஒரு instrument, பல trades" identifier கிடையாது — Section 7 சொன்னபடி, derivative-க்கு ரெண்டு parties-ஓட contract-ஐத் தவிர தனி இருப்பே இல்ல. அந்த structural இடைவெளியை UPI, ISIN உட்காரற இடத்துக்கு ஒரு படி மேல, *product* level-ல நிரப்புது.

```text
UPI vs UTI  —  WHERE EACH SITS IN THE HIERARCHY
====================================================================

  ASSET CLASS -> PRODUCT -> [ no instrument-level ID for OTC derivatives ] -> TRADE
                      |                                                          |
                      v                                                          v
              UPI identifies                                        UTI identifies
              the PRODUCT TEMPLATE                                  ONE TRANSACTION
              (reused across trades)                                 (new every time)
```

### UPI எப்படி build ஆகுது

UPI = ISIN மாதிரி format-ல 12-character code; ஆனா code தானே ஒரு lookup key மட்டும் — உண்மையான content, அதுக்குப் பின்னாடி இருக்கற **UPI Reference Data** record-ல: product-ஐ விவரிக்கற standardized fields (Asset Class, Instrument Type, Underlier(s), Notional Currency, Delivery Type, மற்றும் அந்த instrument type-க்கே உரிய சில economic terms). ரெண்டு derivative trades-க்கு **ஒரே UPI** வரும் — அந்த product-defining fields எல்லாமே பொருந்தினா, அப்போ மட்டும்தான். Trade-specific விவரங்கள் (notional amount, price/rate, counterparty, trade date) UPI-ஐ வரையறுக்கறதில் வேணும்னே சேர்க்கப்படலை — அதனாலதான் ஒரே UPI ஆயிரக்கணக்கான சம்பந்தமில்லாத trades-ல சட்டப்படி வரலாம்.

### Worked example — ஒரு UPI, மூணு வேற வேற Interest Rate Swap trades

Section 8-ல பார்த்த 5Y CAD Interest Rate Swap product-ஐ திரும்பப் பார்க்கலாம் — Bank Of Toronto சில மாதங்களுக்குள்ள மூணு தனி swaps execute பண்ணுதுன்னு வெச்சுக்கோங்க; எல்லாம் ஒரே product shape (5Y tenor, CAD notional, fixed-vs-CORRA-floating), ஆனா trade-specific விவரங்கள் முற்றிலும் வேற:

```text
ONE PRODUCT (ONE UPI) -- THREE UNRELATED TRADES (THREE UTIs)
========================================================================

  UPI: QZ9F4K2XR7T1   (illustrative — issued by the DSB)
  Product definition behind this UPI:
    Asset Class      : Interest Rate
    Instrument Type  : Swap, Fixed-Float
    Underlier        : CORRA
    Notional Currency: CAD
    Tenor             : 5 Years

  TRADE A                         TRADE B                         TRADE C
  Trade ID : BOT-2026-000514      Trade ID : BOT-2026-000601      Trade ID : BOT-2026-000725
  UTI      : CA1B0T20260817001    UTI      : CA1B0T20260903002    UTI      : CA1B0T20261014003
  UPI      : QZ9F4K2XR7T1  <-SAME UPI as Trade A and Trade B, same product
  Counterparty : Goldman Sachs    Counterparty : Maple Leaf Fund   Counterparty : Goldman Sachs
  Notional : CAD 100,000,000      Notional : CAD 35,000,000        Notional : CAD 250,000,000
  Fixed Rate : 4.00%              Fixed Rate : 3.85%                Fixed Rate : 4.15%
  Trade Date : 17-Aug-2026        Trade Date : 03-Sep-2026          Trade Date : 14-Oct-2026
```

**Takeaway:** மேல மூணு rows-உம் வேற வேற counterparties, notionals, rates, dates உள்ள முற்றிலும் வேற trades — ஒவ்வொண்ணுக்கும் தனி UTI, தனி internal Trade ID, Trade layer-ல எதிர்பார்க்கற மாதிரியே. ஆனா மூணுக்கும் ஒரே product shape (5Y CAD fixed-vs-CORRA swap) இருக்கறதால, எல்லாமே **அதே UPI**-ஐ report பண்ணுது. ஒரு reporting query ஆயிரக்கணக்கான தனித்தனி trades-ஐ ஒரே UPI value-ல group பண்ணா, அது data quality bug இல்ல — UPI தன் வேலையைத்தான் செய்யுது: ஒவ்வொரு firm-ஓட internal product coding-ஐயும் புரிஞ்சுக்காமலே "முழு market-ல எவ்வளவு 5Y CAD fixed-vs-CORRA swap risk இருக்கு"-ன்னு regulator aggregate பண்ண வழி செய்யுது.

---

## 13. Worked Example — Interest Rate Swap (IRS)

**Definition:** IRS = ஒரே **Notional** ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §8) மேல கணக்கிட்ட **fixed** வட்டி stream-ஐயும் **floating** வட்டி stream-ஐயும் பரிமாறிக்கற ஒப்பந்தம்; Notional கை மாறாது. Section 9-ஓட CDS (நடக்காமலே போகலாம்ங்கற contingent payout), Section 10-ஓட FX Forward (ரெண்டு உண்மையான currency amounts, கடைசில settle) போல இல்லாம, IRS-ஓட notional **ஒருபோதும் settle ஆகாது** — சின்ன net வித்தியாசம் மட்டும் நகரும், அதுவும் maturity-ல ஒரு தடவை இல்ல, **ஒவ்வொரு period-லயும்**.

> **Complete worked example இருக்கு.** [Interest Rate Swap (IRS) — Complete Example](../examples/Interest_Rate_Swap_IRS_Complete_Example.html) பாருங்க — FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS வரை இணைஞ்ச standalone examples வரிசைல இதுதான் முதல் பாடம்.

```text
INTEREST RATE SWAP  —  Bank Of Toronto pays fixed, receives floating vs CORRA
================================================================================

  Notional          : CAD 100,000,000  (reference only — never exchanged)
  Fixed Rate         : 4.00% (Bank Of Toronto pays)
  Floating Rate      : 3-month CORRA + 0.05% spread (Goldman Sachs pays)
  Term               : 5 years, quarterly payment dates (20 settlements total)

SCENARIO A — CORRA averages 3.20% this quarter (the normal case)
----------------------------------------------------------
  Fixed leg (Bank Of Toronto pays)  : 100,000,000 x 4.00% x (91/365) = CAD 997,260
  Floating leg (Goldman Sachs pays) : 100,000,000 x 3.25% x (91/365) = CAD 810,068
                                       (3.20% CORRA + 0.05% spread)

  Bank Of Toronto  ---- pays CAD 187,192 net ---->  Goldman Sachs

SCENARIO B — CORRA instead averages 4.30% this quarter (rates moved up)
----------------------------------------------------------
  Fixed leg (Bank Of Toronto pays)  : 100,000,000 x 4.00% x (91/365) = CAD 997,260
  Floating leg (Goldman Sachs pays) : 100,000,000 x 4.35% x (91/365) = CAD 1,084,521
                                       (4.30% CORRA + 0.05% spread)

  Goldman Sachs  ---- pays CAD 87,261 net ---->  Bank Of Toronto
  (the direction FLIPS — same trade, same two parties, opposite net payer,
  purely because CORRA reset higher this quarter)

  -> This repeats every quarter for 5 years — 20 separate net settlements,
     each independently determined by that period's own CORRA reset. The
     CAD 100,000,000 notional itself never moves on ANY of those 20 dates.
```

**ISDA classification:** Asset Class = **Interest Rate**, Base Product = **IRSwap**, Sub-Product = **FixedFloat**, Transaction Type = **Swap**. Section 11 table-ல பயன்படுத்தின "Interest Rate / IRSwap / FixedFloat / Swap" shorthand-ஓட முழு நாலு-level label இதுதான்.

**Takeaway:** Sections 9, 10-ஓட பக்கத்துல வெச்சுப் பார்த்தா, இந்த trade "உண்மையில என்ன settle ஆகுது"-ங்கறதுல மூணு-வழி வித்தியாசத்தை நிறைவு பண்ணுது: IRS-ஓட notional **ஒருபோதும்** settle ஆகாது (net வித்தியாசம் மட்டும், திரும்பத் திரும்ப); FX Forward-ஓட notional கடைசில **settle ஆகும்** (ரெண்டு முழு currency amounts, ஒரு தடவை, Section 10); CDS-ஓட notional **ஒருவேளை settle ஆகவே ஆகாது** (credit event நடந்தா மட்டும் contingent lump sum, Section 9). மூணு OTC derivatives, "Notional"-க்கும் "cash-ல உண்மையில நகர்றதுக்கும்" இடையில மூணு முற்றிலும் வேற உறவுகள் — ஒரே மாதிரின்னு assume பண்ணாம, reporting system ஒவ்வொரு product-க்கும் தனியா encode பண்ண வேண்டிய field-level நுணுக்கம் இதுதான்.

### கூடுதல்: மூணு derivatives-ஐ ஒரே பார்வைல

| Derivative | Notional settle ஆகுமா? | எப்போ cash நகரும்? | யார் யாருக்கு? |
|---|---|---|---|
| IRS | ஒருபோதும் இல்ல | ஒவ்வொரு period-லயும் (net) | Fixed vs floating-ல யார் அதிகமோ அவங்க கொடுப்பாங்க; direction புரளலாம் |
| FX Forward | ஆமா, கடைசில | Value date-ல ஒரு தடவை (ரெண்டு currencies-உம்) | ரெண்டு பக்கமும் பரிமாற்றம் |
| CDS | ஒருவேளை | Premium தவணை தவணையா; payout default ஆனா மட்டும் | Buyer premium; seller loss amount |

---

## Recap Quiz — Numbers Edition

1.  மேல Trade 1-ஐ வெச்சு, Bank Of Toronto அதே Price 98.50-க்கு CAD 25,000,000 face value வாங்கியிருந்தா, புது market value என்ன? Original trade-ஓட ஒப்பிட்டா record-ல எந்த fields மாறியிருக்கும்?
2.  ஒரு reporting record-ல CUSIP இருக்கு, Ticker இல்ல. அது bond-ஆ, exchange-listed equity-ஆ இருக்கறதுக்கு வாய்ப்பு அதிகம்? ஏன்?
3.  CAD 100,000,000 Interest Rate Swap-உம், CAD 100,000,000 bond purchase-உம் ரெண்டுமே report-ல "CAD 100,000,000"-ன்னு பெரிசா காட்டலாம். இந்த ரெண்டு "CAD 100,000,000"-உம் அடிப்படைல ஏன் வேற வேற அர்த்தம்?
4.  Asset Class → Product → Instrument → Trade hierarchy-ல எந்த ஒரு layer Bank Of Toronto transact பண்ற ஒவ்வொரு தடவையும் புது record பெறும்? எந்த மூணு layers பல trades-க்கு ஒரே மாதிரி இருக்கும்?
5.  Apple Inc. common stock-க்கு Ticker `AAPL`, CUSIP `037833100`, ISIN `US0378331005`. NASDAQ-க்கு பதிலா ஒரு ஐரோப்பிய exchange-ல நடந்த Apple trade-ஐ பார்க்கும்போது, இந்த மூணுல எது தொடர்ந்து வேலை செய்யும், எது செய்யாம போக வாய்ப்பு?
6.  Northern Rail Corp CDS-ல (Section 9), recovery rate 40%-க்கு பதிலா 25%-ஆ இருந்து default ஆனா, Goldman Sachs-ஓட payout என்ன? *குறைவான* recovery rate ஏன் *அதிகமான* payout-ஐ குறிக்குது?
7.  Cash bond-க்கு reporting system-ல "ISDA Taxonomy" field ஏன் நிரம்பவே நிரம்பாது, ஆனா Interest Rate Swap-க்கு எப்பவும் நிரம்புது?
8.  ISDA Product Taxonomy-ஓட நாலு levels-ஐ வரிசைப்படி சொல்லுங்க. "Credit" எந்த level-ல உட்காரும்? "CreditDefaultSwap" எந்த level-ல?
9.  FX Forward உதாரணத்துல (Section 10), Bank Of Toronto USD 10,000,000-ஐ 3 மாசத்துல *விக்க* (வாங்க இல்ல) வேண்டியிருந்து, அதே 1.3545 forward rate-ஐ lock பண்ணிச்சுன்னு வெச்சுக்கோங்க. Spot பிறகு 1.3700-க்கு நகர்ந்தா, காத்திருந்து spot-ல விக்கறதோட ஒப்பிட்டா இது Bank Of Toronto-க்கு நல்லதா கெட்டதா? இது original உதாரணத்தோட mirror image ஆக எப்படி?
10. Section 12 worked example-ல Trade A-வும் Trade C-யும் ஒரே UPI, ஒரே counterparty (Goldman Sachs), ரெண்டும் CAD-denominated — ஆனா அவை தெளிவா ரெண்டு தனி trades. அவை தனின்னு நிரூபிக்கற ரெண்டு identifiers என்ன? UPI-ஐ share பண்றதால அவை "ஒரே trade" ஆகாதது ஏன்?
11. Section 13-ஓட Scenario B-ஐ பயன்படுத்தி, CORRA இந்த quarter-ல சரியா 3.95% சராசரியா இருந்தா (+0.05% spread = 4.00% floating, fixed rate-க்கு சரியா சமம்), net settlement என்ன? அப்போ அந்த payment date-ல எதுவுமே நடக்காதுன்னு அர்த்தமா?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் **[Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html): Trade Lifecycle**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. புது market value = CAD 24,625,000. Quantity-யும் Value-உம் மாறின; Price, Currency, Instrument, எல்லா reference-data fields-உம் அப்படியே.**

```text
Market Value = 25,000,000 x 98.50 / 100 = CAD 24,625,000
```

Quantity (Trade field) value-வை நேரடியா scale பண்ணுது. Price (98.50) மாறல, ஏன்னா அது அந்த நேரத்துல instrument-க்கான market விலை, ஒவ்வொரு buyer எவ்வளவு வாங்கறாங்கங்கறதைச் சார்ந்தது இல்ல. Trade layer-க்கு மேல இருக்கற எல்லாமே — Asset Class, Product, Instrument, ISIN — அதே, ஏன்னா அது இன்னும் அதே bond.

**2. பெரும்பாலும் bond.**

Tickers exchange-traded instruments-க்கு — நடைமுறைல முக்கியமா equities-க்கு — இருக்கும். Ticker இல்லாம CUSIP மட்டும் இருக்கறது bond record-ஓட classic அடையாளம்: bonds-க்கு identifiers (CUSIP/ISIN) உண்டு, ஆனா shares மாதிரி exchange ticker-ஓட trade ஆகாது.

**3. ஒண்ணு Quantity (உண்மையில settle ஆகறது), இன்னொண்ணு Notional (கணக்கிட மட்டும் பயன்படற reference number).**

CAD 100,000,000 bond purchase-னா, CAD 100,000,000 face value bonds உண்மையிலயே deliver ஆகி, settlement-ல கிட்டத்தட்ட அதே அளவு cash (price கொஞ்சம் மேல கீழ) கை மாறும் — உண்மையான securities, உண்மையான cash. CAD 100,000,000 swap notional ஒருபோதும் நகராது; fixed, floating legs-க்கு பெருக்க ஒரு number இருக்கணும்னு மட்டும்தான் அது இருக்கு (Section 3, [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) Section 8). காட்டற எண் ஒண்ணு, settlement நிஜம் முற்றிலும் வேற.

**4. Trade தான் ஒவ்வொரு தடவையும் புதுசா இருக்கற ஒரே layer. Asset Class, Product, Instrument பல trades-ல அப்படியே இருக்கும்.**

"Canada 5% Bond 2035" மாதிரி ஒரு instrument-க்கு அடில அதன் வாழ்நாள்ல ஆயிரக்கணக்கான Trade records இருக்கலாம் — ஒவ்வொண்ணும் அதே Asset Class (Fixed Income), Product (Government Bond), Instrument (அந்த ஒரு bond)-க்கே திரும்பிச் சுட்டும். யார் transact பண்ணாங்க, எவ்வளவு, என்ன விலை, எப்போன்னு Trade layer மட்டும்தான் பிடிக்குது.

**5. ISIN-உம் CUSIP-உம் எல்லா இடத்துலயும் வேலை செய்யும்; Ticker செய்யாம போக வாய்ப்பு.**

ISIN வடிவமைப்பிலேயே உலகளவுல unique; எந்த exchange-ல trade ஆனாலும் security-கூடவே பயணிக்கும். CUSIP-உம் exchange venue-ஆல பாதிக்கப்படாது (listing-ஐ இல்ல, security-ஐ identify பண்ணுது). ஆனா Ticker exchange-specific — ஐரோப்பிய exchange-ல (உதா., Frankfurt Xetra) Apple-ஓட ticker `AAPL` இல்ல, `APC` மாதிரி ஏதாவது காட்டப்படும்; ஏன்னா tickers ஒவ்வொரு listing venue-க்கும் assign ஆகும், ISIN/CUSIP மாதிரி global இல்ல.

**6. Payout = CAD 15,000,000. குறைவான recovery rate = default-ல creditors குறைவாத் திரும்பப் பெறறாங்க, அதனால CDS பெரிய loss-ஐ cover பண்ணணும்.**

```text
Loss Given Default = Notional x (1 - Recovery Rate)
                    = 20,000,000 x (1 - 0.25)
                    = CAD 15,000,000
```

Recovery Rate = default-க்கு அப்புறம் (உதா., default ஆன company-ஓட மீதி assets-ஐ விற்று) creditors ஒரு dollar-க்கு எத்தனை cents திரும்பப் பெறறாங்க. Recovery Rate-உம் CDS payout-உம் எதிர்த்திசைல நகரும்: 40% recovery-ல payout CAD 12,000,000; recovery 25%-க்கு விழுந்தா creditors தங்க பணத்துல இன்னும் குறைவா திரும்பப் பெறறாங்க, அதனால protection seller (Goldman Sachs) பெரிய இடைவெளியை — CAD 15,000,000 — ஈடுகட்டணும்.

**7. ISDA Taxonomy OTC derivative *contracts*-ஐ classify பண்ணுது; cash bond ரெண்டு parties-க்கு இடையிலான contract இல்ல — அது தனித்து நிற்கற security.**

Bond, அதை இப்போ யார் வெச்சிருக்காங்கங்கறதைச் சாராம தனியா இருக்கும்; அதை வாங்கறது/விக்கறது ஏற்கனவே இருக்கற ஒரு பொருளோட ownership-ஐ மாத்தறது மட்டும் (Section 6). Interest Rate Swap, ரெண்டு counterparties ஒப்புக்கிட்டதாலதான் இருக்கு — அந்த bilateral contract-க்கு வெளியே அதுக்கு இருப்பே இல்ல; இதைத்தான் ISDA taxonomy தொழில்துறை முழுக்க ஒரே மாதிரி classify பண்ண உருவாக்கப்பட்டது (Section 7). Cash instruments CFI codes, ISIN/CUSIP-ஐ பயன்படுத்துது; அவை ISDA-ஓட scope-ல இருந்ததே இல்ல.

**8. ISDA Asset Class → Base Product → Sub-Product → Transaction Type. "Credit" முதல் level-ல (Asset Class); "CreditDefaultSwap" கடைசி level-ல (Transaction Type).**

Section 9 உதாரணம்: Asset Class = Credit, Base Product = SingleName, Sub-Product = Corporate, Transaction Type = CreditDefaultSwap. ஒவ்வொரு level-லயும் taxonomy இன்னும் குறிப்பிட்டதாகுது — "Credit" மட்டும் CDS-ஐயும் Total Return Swap-ஐயும் பிரிச்சுக் காட்டாது, ஆனா நாலாவது level-ல "CreditDefaultSwap" தெளிவா அடையாளம் காட்டிடும்.

**9. Bank Of Toronto-க்கு கெட்ட விளைவு (opportunity cost) — original உதாரணத்தோட mirror image, ஏன்னா இப்போ Bank Of Toronto USD-ஐ வாங்கற பக்கம் இல்ல, விக்கற பக்கம்.**

```text
Bank Of Toronto sells USD 10,000,000 forward at locked rate 1.3545:
  Receives CAD 13,545,000 in 3 months, regardless of where spot lands.

If spot later moves to 1.3700 (USD got MORE expensive):
  Selling USD at that HIGHER spot rate would fetch MORE CAD:
  10,000,000 x 1.3700 = CAD 13,700,000 (spot) vs CAD 13,545,000 (forward)
  -> Forward COST Bank Of Toronto CAD 155,000 vs. waiting and selling at spot.
```

Section 10-ல Bank Of Toronto (USD *வாங்குபவர்)* spot, locked forward rate-ஐ விட உயர்ந்தப்போ நன்மை அடைஞ்சது. இங்க USD *விற்பவரா* இருப்பதால், spot locked rate-ஐ விட உயர்ந்தா பாதிப்பு — காத்திருந்து உயர்ந்த spot-ல வித்திருந்தா அதிக CAD கிடைச்சிருக்கும். ஒரு rate நகர்வு forward-க்கு "உதவுதா" "பாதிக்குதா"ங்கறது, currency pair-ல நீங்க எந்தப் பக்கத்தை lock பண்ணீங்கன்னு பொறுத்தது — buyer, seller ரெண்டு பேரும் ஒரே rate நகர்வுக்கு சரியா எதிர் exposure-ஐ சந்திக்கறாங்க.

**10. அவற்றின் UTI-யும் Trade ID-யும் வேற. UPI-ஐ share பண்றது ரெண்டு trades-க்கும் ஒரே *product shape* மட்டும்தான்னு சொல்லுது — counterparty, size, rate, date பத்தி எதுவும் இல்ல, அவை UPI-ஓட வரையறையில சேராது.**

```text
Trade A: UTI CA1B0T20260817001, Trade ID BOT-2026-000514, Notional CAD 100,000,000, Rate 4.00%, 17-Aug-2026
Trade C: UTI CA1B0T20261014003, Trade ID BOT-2026-000725, Notional CAD 250,000,000, Rate 4.15%, 14-Oct-2026
```

UPI, product-defining fields-ல இருந்து மட்டும் கட்டப்படுது (Asset Class, Instrument Type, Underlier, Notional Currency, Tenor) — Section 12 அந்த fields எவை எனப் பட்டியலிடுது; counterparty/notional/rate/date அவற்றில் இல்ல. இது வேணும்னே அப்படி: "எல்லா 5Y CAD fixed-vs-CORRA swaps-ஐயும்" market முழுக்க ஒண்ணா group பண்ண regulator-க்கு உதவறதுதான் UPI-ஓட வேலை; trade-specific details விலக்கப்பட்டாதான் அது வேலை செய்யும். UTI-யும் (internal-ஆ Trade ID-யும்) UPI வேணும்னே விட்ட trade-specific அடையாளத்தை சுமக்கத்தான் இருக்கு — ஆயிரம் மற்ற transactions அதே product template-ஐ share பண்ணினாலும், ஒவ்வொரு உண்மையான transaction-க்கும் புது ஒண்ணு கிடைக்கும்.

**11. Net settlement = CAD 0 — ஆனா அந்த தேதில எதுவுமே நடக்காதுன்னு அர்த்தம் இல்ல.**

```text
Fixed leg (Bank Of Toronto pays)  : 100,000,000 x 4.00% x (91/365) = CAD 997,260
Floating leg (Goldman Sachs pays) : 100,000,000 x 4.00% x (91/365) = CAD 997,260
                                     (3.95% CORRA + 0.05% spread)
Net = 997,260 - 997,260 = CAD 0
```

ரெண்டு legs-உம் அந்த quarter-ஓட CORRA reset-ல இருந்து தனித்தனியா கணக்கிடப்படும் — CORRA இந்த quarter-ல எங்க இறங்கிச்சுங்கற தற்செயலால, netting step இந்தத் தடவை பூஜ்யம் தருது, அவ்வளவுதான். Trade நிக்கவோ, terminate ஆகவோ, processing-ஐ skip பண்ணவோ இல்ல; அடுத்த quarter-ஓட settlement, CORRA அடுத்து என்ன செய்யுதோ அதிலிருந்து முழுசா தனியா கணக்கிடப்படும். Net cash உண்மையிலயே நகரும்போது மட்டும் entry book பண்ற reporting system, இந்த period-ஓட settlement-ஐ பதிவு பண்ணாம சத்தமில்லாம தவறவிட்டுடும் — net பூஜ்யமா இருந்தாலும் ரெண்டு legs-ஐயும் net-ஐயும் எப்பவும் கணக்கிட்டு report பண்றதுதான் சரியான நடத்தை.
