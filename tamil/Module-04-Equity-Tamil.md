# Module 4 — Equity (பேச்சுத் தமிழில்)

> **யாருக்காக?** Finance / Capital Markets புதுசா கத்துக்கற Java Software Architect-க்கு.
> **Example bank:** **Bank Of Toronto** (கற்பனை வங்கி, புரிய வைக்க மட்டும்).
> **Asset Class Roadmap 1 of 6** — Equity → Fixed Income → FX → Commodities → Rates → Credit. ஒவ்வொரு asset class-உம் அடுத்ததுக்குப் போறதுக்கு முன்னாடி கீழ இருக்கற எட்டு-பாகம் pattern வழியா படிக்கப்படுது.
> **மூலம்:** English Module 4-ஐ பேச்சுத் தமிழ்ல சொல்றது; கூடுதலா சில எளிய உதாரணங்கள் சேர்த்திருக்கேன்.

---

## 1. Equity-ன்னா என்ன? — Theory & Terminology

**Definition:** Equity = ஒரு company-ல **பகுதி உரிமை (fractional ownership)**. Shareholder-க்கு company-ஓட assets மேலயும் எதிர்கால வருமானம் மேலயும் விகிதாசார உரிமை உண்டு; அதுக்கு ஆதாரம் shares. [Module 5](../docs/1-capital-markets/module-05-fixed-income.html)-ல வர்ற Fixed Income instruments போல இல்லாம, equity-க்கு **maturity date இல்ல, fixed coupon இல்ல, அசல் திரும்பக் கொடுப்பேன்னு வாக்குறுதியும் இல்ல** — return முழுக்க price உயர்வு + dividends-ல இருந்து வரும்; ரெண்டுமே மாறக்கூடியவை, dividend தனிப்பட்ட முறையில board-ஓட விருப்பம் (contractual இல்ல).

| Term | அர்த்தம் |
|---|---|
| Share | ஒரு company-ல ஒரு unit உரிமை |
| Outstanding Shares | தற்போது எல்லா shareholders-உம் வெச்சிருக்கற மொத்த shares |
| Market Capitalization | Share Price × Outstanding Shares — மொத்த company-க்கும் market கொடுக்கற மதிப்பு |
| Dividend | Board அறிவிக்கற, shareholders-க்கு கொடுக்கற விருப்பப்படியான cash (அல்லது stock) — உத்தரவாதம் இல்ல |
| Voting Rights | Common shareholders-க்கு பொதுவா ஒரு share-க்கு ஒரு vote (board election, பெரிய முடிவுகள்) |
| Corporate Action | Share-ஐ மாற்றற issuer தொடங்கற நிகழ்வு (stock split, buyback, spin-off, rights issue) |

### Equity vs Fixed Income — மிக முக்கியமான வித்தியாசம்

| | Equity | Fixed Income ([Module 5](../docs/1-capital-markets/module-05-fixed-income.html)) |
|---|---|---|
| உரிமை | ஆமா — company மேல மீதி (residual) உரிமை | இல்ல — creditor உரிமை (issuer-க்கு கொடுத்த கடன்) |
| Maturity | இல்ல — காலவரையற்று வெச்சிருக்கலாம் | நிலையானது — bond குறிப்பிட்ட தேதில maturity ஆகும் |
| Return | Price உயர்வு + dividends (மாறக்கூடியது, உத்தரவாதம் இல்ல) | Coupon (நிலையானது, contractual) + maturity-ல அசல் |
| Bankruptcy-ல முன்னுரிமை | **கடைசி** — எல்லா creditors-க்கும் அப்புறம்தான் | Equity-க்கு முன்னாடி — bondholders creditors |

**Takeaway:** இந்த module-ஓட மற்ற எல்லா sections-உம் (products, trade lifecycle, identifiers, regulation) இந்த ஒரே உண்மைக்குத் திரும்பிப் போகும்: ஒரு share = company-ஓட ஒரு துண்டு, company கொடுத்த வாக்குறுதி இல்ல. இது bond-க்கு நேர் எதிர்; அதனாலதான் equity valuation ([Module 10](../docs/1-capital-markets/module-10-valuation.html)) bond valuation-ல இருந்து முழுசா வேறுபடுது.

> **Deep dive இருக்கு.** ஒரு share-ஓட சொந்த விலை நகர்வை Fixed Income, Rates மாதிரி equity basis points-ல விவரிக்காது — ஆனா **management/advisory fees**, **bid-ask trading spreads** இவற்றுல bps உண்மையிலயே வரும். ரெண்டு worked examples-உம் [Module 24 — Basis Points Deep Dive](../docs/1-capital-markets/module-24-basis-points-deep-dive.html) §6-ல.

---

## 2. Products என்னென்ன? — Cash Equity vs Equity Derivatives

```text
EQUITY
│
├── Cash Equity              (the actual security)
│   ├── Common Stock
│   └── Preferred Stock
│
└── Equity Derivatives       (contracts whose value derives from the security)
    ├── Equity Futures
    └── Equity Options
```

| Cash / Derivative | Product | அது என்ன |
|---|---|---|
| CASH | **Common Stock** | சாதாரண shares — (பொதுவா) ஒரு share-க்கு ஒரு vote, dividend board-ஓட முழு விருப்பம், company கலைக்கப்பட்டா assets மேல கடைசி உரிமை |
| CASH | **Preferred Stock** | கலப்பு instrument — bond coupon மாதிரி *நிலையான dividend rate* உண்டு, ஆனா board அறிவிச்சா மட்டும்தான் கொடுப்பாங்க; common-ஐ விட முன்னுரிமை, ஆனா எல்லா debt-க்கும் பின்னால்; பொதுவா voting rights இல்ல |
| DERIVATIVE | **Equity Future** | ஒரு stock அல்லது index-ஐ இன்னிக்கே ஒத்துக்கிட்ட விலைல எதிர்கால தேதில வாங்க/விற்க exchange-ல trade ஆகற contract; index futures பொதுவா cash-settled, physical delivery இல்ல |
| DERIVATIVE | **Equity Option** | Buyer-க்கு, expiry-க்குள்ள/அன்னிக்கு, fixed strike price-ல shares-ஐ வாங்க (call) அல்லது விற்க (put) **உரிமை, கடமை இல்ல** தரும் contract; முன்கூட்டியே premium கொடுக்கணும் |

**Takeaway:** Common-உம் Preferred-உம் ஒரே "Cash Equity" bucket-ல இருக்கு, ஏன்னா ரெண்டுமே உண்மையான, deliver பண்ணக்கூடிய securities — வித்தியாசம் முழுக்க உரிமையோட *economics*-ல (Preferred: கிட்டத்தட்ட நிலையான dividend, அதிக முன்னுரிமை, vote இல்ல; Common: மாறும் dividend, கடைசி முன்னுரிமை, vote உண்டு). Futures-உம் Options-உம் முற்றிலும் வேற bucket, ஏன்னா அவை stock *இல்ல* — Section 3 இதை உறுதியா காட்டுது.

---

## 3. உண்மையில எது Trade ஆகுது? — Security vs Contract

**Definition:** Common அல்லது Preferred Stock-ல நீங்க ஒரு **உண்மையான security**-ஐ trade பண்றீங்க — எந்த ஒரு குறிப்பிட்ட trade-ஐயும் சாராம இருக்கற, CSD-ல book-entry-ஆ பதிவான ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §7) transfer பண்ணக்கூடிய ownership உரிமை. Equity Futures அல்லது Options-ல நீங்க ஒரு **contract**-ஐ trade பண்றீங்க — அதோட மதிப்பு அந்த underlying security-ஓட விலையில இருந்து derive ஆகுது; contract execution-ல உருவாகி, பொதுவா expiry அல்லது exercise-ல முடிஞ்சுடும்.

```text
SECURITY vs CONTRACT
========================================================================

Common Stock (SECURITY):
  Coca-Cola Company issued a fixed number of shares, years ago.
  Every trade just transfers ownership of EXISTING shares between
  two parties -- nothing new is created or destroyed by trading it.

Equity Option (CONTRACT):
  "AAPL 15-Jan-2027 250 Call" doesn't exist until two parties agree
  to create it (the same principle as Module 1 §8's notional, applied
  to options) -- and it disappears entirely at expiry/exercise.
  Trading it doesn't move any Apple shares; it creates or transfers
  a CLAIM on Apple shares.
```

**Takeaway:** இது [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §3-ல பார்த்த "Quantity vs Notional" வித்தியாசம்தான், equity-க்காக மறுபடி சொல்றது. Cash equity trade-ஓட Quantity (shares) உண்மையான, deliver பண்ணக்கூடிய அளவு. Equity derivative-ஓட underlying share count, contract-ஓட payoff-ஐ size பண்ண பயன்படற reference அளவு மாதிரி — அது உண்மையான shares நகர வைக்குமா இல்லையாங்கறது contract physically settle ஆகுதா, உண்மையில exercise ஆகுதாங்கறதை (Section 5) முழுசா பொறுத்தது.

---

## 4. நிஜ உலக Trade உதாரணங்கள் — Buyer → Seller → Trade

> **Complete worked examples இருக்கு.** கீழ உள்ள cash equity trade-க்கு [Cash Equity Trade — Complete Example](../examples/Cash_Equity_Trade_Complete_Example.html) பாருங்க. இந்த asset class-ஓட derivative பக்கத்துக்கு [Equity Total Return Swap — Complete Example](../examples/Equity_Total_Return_Swap_Complete_Example.html) — ரெண்டுமே உண்மையான எண்களோட முழு trade lifecycle-ஐ நடத்திக் காட்டுது.

### 4a. Common Stock

```text
COMMON STOCK  —  Coca-Cola Company (NYSE: KO)
========================================================================

  Buyer:  Maple Leaf Pension Fund
  Seller: Goldman Sachs
  Dealer: Bank Of Toronto (takes the trade onto its own book briefly,
          per Module 1 §3's dealer/principal mechanics)

  Trade: 10,000 shares @ USD 68.40
  Trade Value = 10,000 x 68.40 = USD 684,000
```

```text
ONE "TRADE," TWO LEGS — WHAT "DEALER" ACTUALLY MEANS HERE
========================================================================

  Leg 1:  Goldman Sachs  ---- sells 10,000 KO @ 68.40 ---->  Bank Of Toronto
  Leg 2:  Bank Of Toronto ---- sells 10,000 KO @ 68.40 ---->  Maple Leaf Pension Fund

  Both legs settle at the SAME price -- Bank Of Toronto takes no
  spread and ends the day flat (bought 10,000, sold 10,000) -- but
  it is a REAL counterparty on each leg, not a pass-through broker.
```

> Bank Of Toronto இங்க **ரெண்டு பக்கத்துக்கும்** உண்மையான counterparty — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §3-ல பார்த்த dealer/principal mechanics-படி. அதாவது மேல உள்ள ஒரே "Trade:" வரி உண்மையில **ரெண்டு தனி settlement கடமைகள்**: Bank Of Toronto, Goldman Sachs-க்கு cash-ம் Maple Leaf Pension Fund-க்கு shares-ம் கொடுக்கணும்; அதே நேரம் Goldman Sachs-ல இருந்து shares-ஐயும் Maple Leaf Pension Fund-ல இருந்து cash-ஐயும் வாங்க வேண்டியிருக்கு. Bank Of Toronto, title எடுக்காத riskless broker-ஆ இருந்திருந்தா, கடமை ஒண்ணே ஒண்ணுதான், Goldman Sachs-க்கும் Maple Leaf Pension Fund-க்கும் நேரடியா — ஆனா இந்த உதாரணத்துல நடக்கறது அது இல்ல.

### 4b. Preferred Stock

```text
PREFERRED STOCK  —  Bank Of Toronto Preferred Shares, Series A
========================================================================

  Issuer: Bank Of Toronto (primary issuance, per Module 1 §1's
          primary-market mechanics)
  Buyer:  Goldman Sachs

  Issue: 2,000,000 shares @ Par CAD 25.00, Dividend Rate 4.50%
         Non-Cumulative
  Cash Raised = 2,000,000 x 25.00 = CAD 50,000,000

  Annual Dividend / share = 25.00 x 4.50% = CAD 1.125
  Total Annual Dividend    = 2,000,000 x 1.125 = CAD 2,250,000
  Paid Quarterly           = 2,250,000 / 4 = CAD 562,500 / quarter

  "Non-Cumulative" means: if Bank Of Toronto's board skips a
  quarter's dividend, Goldman Sachs does NOT get it later -- it is
  simply gone. Contrast a bond coupon (Module 1 §1), where missing
  a payment is a DEFAULT. A preferred dividend, even on a fixed-rate
  instrument, is never a legal obligation the way a bond coupon is.
```

### 4c. Equity Future

```text
EQUITY FUTURE  —  E-mini S&P 500 Future (CME)
========================================================================

  Buyer: Maple Leaf Pension Fund
  Seller/Counterparty: CME Clearing (the CCP — see Section 7; on an
        exchange, buyer and seller are matched anonymously and each
        ends up facing the CCP, not each other, from day one)

  Trade: BUY 20 contracts @ 5,920.00 (index points)
         Multiplier: USD 50 per index point
  Notional = 20 x 50 x 5,920.00 = USD 5,920,000

  Initial Margin (illustrative, ~USD 12,000/contract):
    20 x 12,000 = USD 240,000 posted to CME Clearing

  Next business day, index moves to 5,935.00:
    Variation Margin = (5,935.00 - 5,920.00) x 50 x 20 = USD 15,000
    -> credited to Maple Leaf Pension Fund's margin account THAT DAY
       (daily mark-to-market, not a wait until maturity like an OTC
       swap's quarterly cash flow — Module 1 §8)
```

### 4d. Equity Option

```text
EQUITY OPTION  —  AAPL 15-Jan-2027 250 Call
========================================================================

  Buyer: Maple Leaf Pension Fund
  Seller (writer): Bank Of Toronto (dealer)

  Trade: BUY 100 contracts, Strike USD 250, Expiry 15-Jan-2027
         Premium: USD 18.50 / share, 100 shares / contract
  Premium Paid = 100 x 100 x 18.50 = USD 185,000 (paid T+1)

  AT EXPIRY -- Scenario A: AAPL trades at USD 270
    Intrinsic Value = (270 - 250) x 10,000 shares = USD 200,000
    Option exercised; Net Profit = 200,000 - 185,000 = USD 15,000

  AT EXPIRY -- Scenario B: AAPL trades at USD 240
    Option expires WORTHLESS (strike above market price)
    Maple Leaf Pension Fund loses the full USD 185,000 premium
    -> this is the maximum possible loss for a long option buyer,
       ever — never more than the premium paid.
```

> Section 4c-ஓட future-ல இருந்த அதே clearing உறவு: இந்த option OCC வழியா clear ஆனவுடனே, Maple Leaf Pension Fund-உம் Bank Of Toronto-வும் ஒருத்தருக்கொருத்தர் இல்ல, உண்மையில **OCC**-ஐத்தான் எதிர்கொள்றாங்க — மேல இருக்கற "Buyer"/"Seller (writer)" labels trade-ஓட அசல் இருதரப்பு economics-ஐ சொல்லுது, novation-க்கு அப்புறம் யார் யாருக்கு counterparty-exposed-ங்கறதை இல்ல.

### கூடுதல் உதாரணம் — Futures vs Options: ஒரே 10 நாள்ல யாருக்கு என்ன நடக்கும்?

Maple Leaf Pension Fund-ஓட ரெண்டு positions-ஐ ஒப்பிடுவோம்:

| | E-mini S&P 500 Future (long 20) | AAPL Call (long 100 contracts) |
|---|---|---|
| ஆரம்பத்துல கொடுத்தது | Initial Margin USD 240,000 (திரும்பக் கிடைக்கும் collateral) | Premium USD 185,000 (திரும்பாது, செலவு) |
| Index/AAPL சாதகமா நகர்ந்தா | தினமும் Variation Margin credit | Expiry-ல intrinsic value |
| பாதகமா நகர்ந்தா | தினமும் Variation Margin debit — அதிகம் போகலாம் | அதிகபட்ச இழப்பு = premium (USD 185,000) |
| நகர்வு எப்போ cash ஆகுது | தினமும் | Expiry/exercise-ல |

Futures-ல நஷ்டம் வரம்பில்லாம போகலாம்; long option-ல நஷ்டம் premium-ல நிக்கும். இதனால margin mechanics-உம் வேற.

**Takeaway:** இந்த நாலு உதாரணங்கள்ல buyer/seller உறவு எப்படி வடிவம் மாறுதுன்னு கவனிங்க — bilateral dealer trade (4a), primary issuance (4b), CCP-க்கு எதிரான exchange trade (4c), dealer எழுதின bilateral option (4d). நாலுமே "Equity" தான், ஆனா Section 5 lifecycle-க்கு வர்றதுக்கு முன்னாடியே actors-உம் settlement mechanics-உம் வேறுபடத் தொடங்கிடுது.

---

## 5. Trade Lifecycle — Equity-க்குரிய Walkthrough

Asset Class Roadmap pattern-ஐ — **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&L → Reporting** — Section 4a-ல வந்த Coca-Cola common stock trade மேல பொருத்தறோம். இது [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html)-ஓட அதே ஒன்பது-stage முதுகெலும்பு தான், ஆனா Module 3-ஓட "Settlement"-க்கும் "Reconciliation/Reporting"-க்கும் நடுவுல இருக்கற ரெண்டு கூடுதல் stages (**Position**, **Valuation/P&L**) வெளிப்படையா காட்டப்பட்டிருக்கு.

```text
COCA-COLA COMMON STOCK  —  FULL LIFECYCLE
========================================================================

  09:31:00  ORDER — Maple Leaf Pension Fund instructs Bank Of Toronto:
            "BUY 10,000 shares Coca-Cola Company (NYSE: KO)"

  09:31:04  EXECUTION @ USD 68.40 on NYSE

  09:31:04  TRADE CAPTURE — Trade ID BOT-EQ-2026-011203

  09:45:00  CONFIRMATION/MATCHING via DTCC CTM -> MATCHED

  Trade submitted to NSCC for CLEARING (multilateral netting,
  per systems-landscape §05 — this trade may net against other
  KO trades Bank Of Toronto did that day)

  Settlement Date = T+1
            DTC performs DvP: shares move in DTC's book-entry
            system; cash settles via Fedwire.

  POSITION — Maple Leaf Pension Fund's holding of KO increases by
            10,000 shares in the position-keeping system (this is
            what Module 3 §08's reconciliation checks against the
            custodian and CSD records)

  VALUATION / P&L — each day after settlement, the position is
            marked to the current market price:
              Day of purchase:  10,000 x 68.40 = USD 684,000 (cost)
              One week later:   10,000 x 71.10 = USD 711,000 (market value)
              Unrealized P&L =  711,000 - 684,000 = +USD 27,000

  REPORTING — trade reported to the relevant Trade Repository/
            regulator by T+1 (Module 3 §09); the ongoing POSITION
            is separately subject to periodic position reporting
            requirements in some jurisdictions (Module 14)
```

> **Deep dive இருக்கு.** Position உண்மையில என்ன, எல்லா asset classes-லயும் அது எப்படி கட்டப்படுது, எவ்வளவு அடிக்கடி மாறுதுங்கறதை முழுசா: [Module 25 — Position Deep Dive](../docs/1-capital-markets/module-25-position-deep-dive.html) §3.

### Derivative products-க்கு Clearing எப்படி வேறுபடுது

| Cash / Derivative | Product | CCP | "Clearing" என்ன கூடுதலா சேர்க்குது |
|---|---|---|---|
| CASH | Common/Preferred Stock | NSCC (மேல US உதாரணம்) / CDCC / LCH / Eurex, நாட்டுக்கு ஏற்ப ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §04–07) | ஒரே T+1/T+2 DvP settlement-க்கு முன்னாடி multilateral netting |
| DERIVATIVE | Equity Future | CME Clearing | Novation ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §06) + contract-ஓட வாழ்நாள் முழுக்க **தினசரி** Variation Margin — Position, Valuation/P&L ஒரு தடவை check இல்ல, ஒவ்வொரு business day-யும் ஓடும் |
| DERIVATIVE | Listed Equity Option | **OCC (Options Clearing Corporation)** | Option contract-ஓட novation; premium T+1-ல settle; exercise ஆனா, exercise-க்கு T+1 அப்புறம் *தனி* physical stock settlement |

> **கவனிக்க வேண்டிய பெயர்க் குழப்பம்:** "OCC" அமெரிக்க capital markets-ல முற்றிலும் ரெண்டு வேற விஷயங்களை குறிக்கும். இங்க equities/derivatives context-ல இது **Options Clearing Corporation** — US-listed equity options அனைத்துக்குமான CCP. Banking/prudential context-ல ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §10) "OCC" என்பது **Office of the Comptroller of the Currency** — national banks-ஐ charter பண்ணி மேற்பார்வை செய்யும் regulator. ஒரே acronym, ஒரே நாடு, சம்பந்தமில்லாத அமைப்புகள் — எப்பவும் context-ஐ பாருங்க.

**Takeaway:** "Position", "Valuation/P&L" stages புது concepts இல்ல — அவை [Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §08 (Reconciliation), [Module 10](../docs/1-capital-markets/module-10-valuation.html) (Valuation)-ஓட முன்னோட்டம்; equity-ல இவற்றை எளிதா பார்க்க முடியுங்கறதால இங்க வெளிப்படையா காட்டியிருக்கு: ஒரு stock position பொது, தொடர்ந்து நகரும் market விலைக்கு mark ஆகி சும்மா உட்கார்ந்திருக்கும், coupon-உம் இல்ல, notional கணக்கும் இல்ல, எதையும் மறைக்க.

---

## 6. Identifiers — ISIN, CUSIP, SEDOL, Ticker, LEI, UPI, FIGI

[Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §2 ISIN, CUSIP, Ticker-ஐ அறிமுகப்படுத்திச்சு. மீதி நாலு — SEDOL, LEI, UPI, FIGI — equity-ல தங்க இடத்தைப் பிடிக்குது.

| Identifier | Identifies | Scope | Example (Coca-Cola / Apple, unless noted) |
|---|---|---|---|
| **ISIN** | The instrument | Global | `US1912161007` (Coca-Cola) |
| **CUSIP** | The instrument | North America | `191216100` (Coca-Cola) |
| **Ticker** | The instrument, on one venue | Exchange-specific | `KO` (NYSE) |
| **SEDOL** | The instrument | Historically UK/Ireland-specific (7 characters); still embedded inside most UK ISINs the same way a CUSIP is embedded in a US/CA ISIN | `0008373` (Tesco PLC on LSE, illustrative) |
| **FIGI** | The instrument — but at *multiple levels* (share class, or a specific trading venue/exchange line) | Global, open-source (Bloomberg-originated, now an industry standard) | `BBG000B9XRY4` (Apple Inc., Composite Global FIGI — commonly published reference data) |
| **LEI** | The **legal entity** itself — not an instrument at all | Global | `5493001KJTIIGC8Y1R12` (illustrative — Bank Of Toronto) |
| **UPI** | An OTC derivative *product template* ([Module 2](../docs/1-capital-markets/module-02-hierarchy.md) §12) | Global | Relevant only for an OTC equity derivative (e.g. a Total Return Swap on KO) — a listed Equity Future or Option instead gets an exchange-specific contract code, not a UPI |

```text
INSTRUMENT IDENTIFIERS  vs  ENTITY IDENTIFIER
========================================================================

  ISIN, CUSIP, Ticker, SEDOL, FIGI   ->  answer "WHAT is being traded?"
  LEI                                 ->  answers "WHO is trading it?"
  UPI                                 ->  answers "WHAT PRODUCT TEMPLATE
                                             does this OTC derivative
                                             belong to?" (Module 2 §12)

  On a single trade record for the Coca-Cola trade (Section 4a):
    Instrument  : ISIN US1912161007 / CUSIP 191216100 / Ticker KO
    Buyer LEI   : identifies Maple Leaf Pension Fund
    Seller LEI  : identifies Goldman Sachs
    Dealer LEI  : identifies Bank Of Toronto
  -> four completely different identifiers on one trade record,
     answering two completely different questions.
```

**Takeaway:** ISIN-ஓட ஒப்பிட்டா FIGI-ஓட தனித்துவம், அது ஒரு security-ஐ ஒன்றுக்கு மேற்பட்ட level-ல identify பண்ண முடியும்ங்கறது — security-க்கு உலகளவுல ஒரு "Composite" FIGI, ஒவ்வொரு exchange listing-க்கும் தனி FIGI. NYSE-லயும் ஒரு ஐரோப்பிய exchange-லயும் dual-listed ஆன stock-க்கு இது பயன்படும்; அங்க ISIN மட்டும், ஒரு trade record *எந்த* venue-ஓட விலை/liquidity-ஐ குறிக்குதுன்னு சொல்லாது.

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** இவை **ரெண்டு தனிக் கேள்விகள்**, குழப்பிக்கறது எளிது. "Exchange vs OTC" = *trade எங்கே பேரம் பேசப்பட்டது* ([Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §5). "Bilateral vs Centrally Cleared" = *execution-க்கு அப்புறம் counterparty risk எப்படி இருக்கு* ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §6) — OTC-ல பேசப்பட்ட trade-உம் அப்புறம் CCP-க்கு novate ஆனா centrally cleared ஆகலாம்.

```text
TWO AXES, NOT ONE
========================================================================

                    BILATERAL                 CENTRALLY CLEARED
                    (stays between the         (novated to a CCP,
                    two original parties)      Module 3 §06)
   ------------------------------------------------------------------
   EXCHANGE-        Effectively never for      Common/Preferred Stock,
   TRADED            equity — exchange rules     Equity Futures (CME
                     typically require            Clearing), Listed
                     clearing                     Options (OCC)
   ------------------------------------------------------------------
   OTC               Uncleared equity swaps/     A growing share of
                      options between two          equity index swaps
                      dealers, subject to           now clear via a CCP
                      bilateral collateral          (LCH, Eurex) even
                      (Uncleared Margin Rules,      though negotiated
                      Module 2 §7)                  OTC
```

| Equity product | Exchange ஆ OTC ஆ? | Bilateral ஆ Centrally Cleared ஆ? |
|---|---|---|
| Common/Preferred Stock | Exchange-traded | Centrally cleared (நாட்டுக்கு ஏற்ப NSCC/CDCC/LCH/Eurex) |
| Equity Future | Exchange-traded | Centrally cleared (CME Clearing) |
| Listed Equity Option | Exchange-traded | Centrally cleared (OCC) |
| OTC Equity Option / Total Return Swap | OTC | (பொதுவா) Bilateral — அல்லது அந்த product clearing-க்கு கட்டாயமாக்கப்பட்டிருந்தா/தேர்ந்தெடுக்கப்பட்டிருந்தா centrally cleared |

**Takeaway:** Cash equity, listed derivatives-க்கு "exchange-traded"-உம் "centrally cleared"-உம் ஒண்ணா பயணிக்கறதால, மக்கள் அவற்றை ஒரே அர்த்தம்னு நினைப்பாங்க — ஆனா Section 8-ல வர்ற regulatory reporting கடமைகள் (EMIR, SFTR) இருக்கறதே, அந்த table-ஓட கீழ்-வலது cell (bilateral OTC) தான் ஒரு trade regulator-ஓட பார்வையில இருந்து முழுசா மறைஞ்சு போக வாய்ப்பிருக்கற இடம்ங்கறதுக்காகத்தான் — [Module 1](../docs/1-capital-markets/module-01-fundamentals.html) §5 OTC products-க்கு பொதுவா முதல்ல நிறுவினது போலவே.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

Products, trade mechanics, identifiers எல்லாம் உறுதியா புரிஞ்சப்புறம் மட்டும்தான் acronyms-ஐ ஒரு உண்மையான விஷயத்தோட இணைக்கறோம்.

| Regulation / Body | Equity-ஐ குறிப்பா எப்படித் தொடுது |
|---|---|
| **MiFID II / MiFIR** (EU) | Equity trading-க்கான pre-/post-trade transparency, best-execution கடமைகள், exchange-க்கு வெளியே OTC-போல equity trading செய்யும் Systematic Internalizer regime, T+1 transaction reporting ([Module 3](../docs/1-capital-markets/module-03-trade-lifecycle.html) §09, [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §07) |
| **EMIR** | Equity derivative OTC-ஆ இருந்தா மட்டும் (உதா., KO மேல Total Return Swap) — Trade Repository-க்கு report ([Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §07). Common/Preferred Stock-க்கோ listed Futures/Options-க்கோ **பொருந்தாது**, ஏன்னா அவை OTC இல்ல |
| **SFTR** | Equity shares கடனாக கொடுக்கப்படும்/வாங்கப்படும்போது, அல்லது repo collateral-ஆ பயன்படும்போது குறிப்பா பொருந்தும் — உதா., Bank Of Toronto தன் Coca-Cola position-ஐ short-seller-க்கு கடன் கொடுக்கறது ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12) |
| **FCA** (UK) | UK equity trading-ஓட conduct regulator — UK MiFIR equity transaction reporting-ஐ நிர்வகிக்குது |
| **CIRO** (Canada) | Canadian equity dealer conduct, trading activity-ஐ மேற்பார்வை பண்ற SRO |
| **HKMA / SFC** (Hong Kong) | கவனமான வேறுபாடு: **HKMA** = Hong Kong-ஓட central-bank-equivalent (வங்கி மேற்பார்வை, monetary policy; Bank of England மாதிரி). **SFC** = Hong Kong-ஓட உண்மையான securities/equities market-conduct regulator — equity trading-க்கு FCA அல்லது SEC-க்கு நெருக்கமான ஒப்புமை |
| **DTCC** (US) | அதன் subsidiaries NSCC, DTC US cash equities-ஐ clear/settle பண்ணுது (மேல Section 5) |
| **DMO** (UK Debt Management Office) | UK government bonds (Gilts)-ஐ வெளியிடுது — equity-க்கு **சம்பந்தமில்ல**; நீங்க பட்டியலிட்டதால இங்க சேர்த்திருக்கு, முழு விளக்கம் [Module 5](../docs/1-capital-markets/module-05-fixed-income.html)-ல |

**Takeaway:** மேல உள்ள ஒவ்வொரு regulation-உம் Sections 1–7-ல ஏற்கனவே சொன்ன ஒரு *குறிப்பிட்ட mechanic*-உடன் இணையுது — MiFID: trade எங்கே/எப்படி execute ஆச்சு; EMIR: derivative OTC-ஆ இல்லையா; SFTR: derivative மட்டுமில்ல, shares-ஏ கடன் கொடுக்கப்பட்டதா அல்லது collateral-ஆ பயன்படுத்தப்பட்டதா. இவற்றில் எதுவும் "Equity" முழுமைக்கும் ஒரே மாதிரி பொருந்தாது; ஒவ்வொண்ணும் இந்த module நடந்து காட்டின ஒரு குறிப்பிட்ட பகுதிக்கு மட்டும் எல்லை வகுக்கப்பட்டிருக்கு.

---

## 9. Canadian Systems Landscape — Equity Front, Middle & Back Office-ல இருந்து Regulatory Reporting வரை

**Definition:** [systems-landscape](../docs/1-capital-markets/systems-landscape.html) §4 ஏற்கனவே Canada-ஓட பொதுவான Front/Middle/Back Office architecture-ஐயும் market infrastructure-ஐயும் (CDS/CDSX, CDCC) பெயரிட்டிருக்கு. இந்த section அந்தப் பொதுப் படத்தை **Equity**-க்கு மட்டும் குறிப்பா சுருக்குது — ஒரு Canadian equity order front office-ல இருந்து settlement வரை, மேற்பார்வை வரை, முழுசா கடக்கற உண்மையான systems.

> **Callout — illustrative vendor பெயர்கள்.** கீழ இருக்கற OMS/EMS product பெயர்கள் (Bloomberg AIM, FlexTrade, Charles River IMS, Eze OMS) ஒரு Canadian dealing desk பொதுவா பயன்படுத்தறதன் பிரதிநிதிகள் — Bank Of Toronto-ஓட உண்மையான internal system தேர்வுகள் பத்தின கூற்று இல்ல. Market infrastructure பெயர்கள் (TSX, ATSs, CDS/CDSX, CIRO, Lynx, Bank of Canada) உண்மையான, பெயரிடப்பட்ட Canadian அமைப்புகள்.

| Layer | System(s) | Role |
|---|---|---|
| **Front Office** | OMS/EMS (e.g. Bloomberg AIM, Charles River IMS, or Eze OMS — illustrative), FIX-connected smart order router | Captures the order, routes it to whichever Canadian venue offers the best price |
| **Trading Venues** | **TSX** & **TSX Venture Exchange** (TMX Group); Canadian **ATSs** — Nasdaq CXC/CX2, Cboe Canada (formerly NEO Exchange), Omega ATS | Multiple competing venues execute the trade — Canada's equity market is multi-venue, not single-exchange, so a smart order router genuinely matters |
| **Middle Office** | Trade capture/confirmation; CIRO market-surveillance feed under **UMIR** (Universal Market Integrity Rules); Murex/Calypso for any equity-derivative overlay | Confirms the match, feeds Canada's consolidated tape, surfaces trading-conduct issues to CIRO |
| **Back Office / CSD** | **CDS Clearing and Depository Services Inc.** (CDS, a TMX Group subsidiary) — **CDSX**, Continuous Net Settlement (CNS), DvP book-entry settlement | Nets and settles the trade, **T+1** |
| **Cash Leg** | **Lynx** (Payments Canada's large-value payment system, successor to the old LVTS) | Settles the CAD cash leg |
| **Oversight** | **CIRO** (SRO — dealer conduct & UMIR trade reporting/surveillance); **CSA** (national rule-setting, e.g. the OSC); **Bank of Canada** (oversees CDSX as a designated systemically important Financial Market Infrastructure) | A plain cash equity trade does **not** go to an OTC-derivatives-style Trade Repository the way an IRS or CDS does ([Module 14](../2-reporting/module-14-regulatory-reporting.md) §1) — this is CIRO/UMIR surveillance plus CDSX settlement, not TR reporting |

```text
CANADA — EQUITY TRADE, FRONT OFFICE TO REGULATOR
========================================================================

  Maple Leaf Pension Fund instructs Bank Of Toronto:
  BUY 50,000 shares Loblaw Companies Ltd. (TSX: L)
  (the same trade worked through in systems-landscape §4, §9)

  FRONT OFFICE
    Bloomberg AIM (OMS) captures the order
      -> FlexTrade (EMS) routes it via FIX 4.4
      -> smart order router selects the venue

  TRADING VENUE
    Executed on TSX @ CAD 178.40
    (could equally have routed to an ATS -- Nasdaq CXC/CX2,
    Cboe Canada, Omega ATS -- Canada is multi-venue)
    Trade Value = 50,000 x 178.40 = CAD 8,920,000

  MIDDLE OFFICE
    Trade captured, Trade ID BOT-CA-2026-004471
    Confirmed/matched via CDSX's matching module
    Execution data feeds Canada's consolidated tape and
    CIRO's UMIR-based market surveillance

  BACK OFFICE / CSD
    CDS Clearing and Depository Services Inc. -- CDSX
    Continuous Net Settlement (CNS) nets this trade against
    Bank Of Toronto's other Loblaw activity that day
    Settlement Date = T+1 = Tue 18-Aug-2026
    DvP: shares move book-entry inside CDSX;
    CAD cash leg settles via Lynx (Payments Canada)

  OVERSIGHT (NOT a Trade Repository report)
    CIRO   -- dealer conduct & UMIR trade reporting/surveillance
    CSA / OSC -- national and provincial rule-setting
    Bank of Canada -- oversees CDSX as a designated
    systemically important Financial Market Infrastructure (FMI)
```

**Takeaway:** இந்த trade எந்த இடத்துலயும் OTC-derivatives பாணி Trade Repository-ஐத் தொடலைங்கறதை கவனிங்க — plain cash equity trade-ஓட regulatory தடம் முழுக்க CIRO-ஓட UMIR surveillance, CDSX-ஓட settlement records வழியா ஓடுது; [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1-ஓட IRS உதாரணம் இதுக்கு மாறா, EMIR/Dodd-Frank-ல கூடுதலா Trade Repository-க்கும் போகணும். Product definition மட்டுமில்ல, system stack-ஏ எந்த regulatory regime உண்மையில பொருந்தும்ங்கறதை சொல்லுது.

---

## Actors — முழு Cast Reference

| Actor | இந்த module-ல Role |
|---|---|
| Maple Leaf Pension Fund | Buy-side client — Coca-Cola stock, E-mini future, AAPL call option-ஓட buyer |
| Goldman Sachs | Coca-Cola stock-ஓட seller; Bank Of Toronto-ஓட preferred share issuance-ஓட buyer |
| Bank Of Toronto | Coca-Cola trade-ல dealer; தன் சொந்த Preferred Shares-ஓட issuer; AAPL call-ல option writer |
| CME Clearing | E-mini S&P 500 future-க்கான CCP |
| OCC (Options Clearing Corporation) | Listed AAPL option-க்கான CCP |
| NSCC / DTC | US cash equity trade-க்கான clearing (netting), settlement (DvP) |
| CDS (CDSX) | Canada-ஓட central securities depository — Loblaw trade-ஐ CNS வழியா, T+1-ல clear/settle பண்ணுது (Section 9) |
| CIRO | Canadian SRO — Loblaw trade-ஓட UMIR-சார்ந்த market surveillance, dealer-conduct மேற்பார்வை (Section 9) |
| Bank of Canada | CDSX-ஐ designated systemically important Financial Market Infrastructure-ஆ மேற்பார்வை பண்ணுது (Section 9) |

---

## Recap Quiz — Numbers Edition

1.  Bank Of Toronto-ஓட Preferred Shares Series A, CAD 25.00 par மேல 4.50% non-cumulative dividend கொடுக்குது. Board தொடர்ந்து ரெண்டு quarters-ஐ skip பண்ணி, மூணாவதுல மறுபடி தொடங்கினா, Goldman Sachs skip ஆன dividends-ல இறுதில எவ்வளவு பெறும்? இவை *cumulative* preferred shares-ஆ இருந்திருந்தா பதில் எப்படி வேறுபடும்?
2.  E-mini S&P 500 future-ல (Section 4c), அடுத்த business day-ல index 5,900.00-க்கு இறங்கினா, Variation Margin தொகை என்ன, எந்த account-ல இருந்து எந்த account-க்கு நகரும்?
3.  AAPL 15-Jan-2027 250 Call-ல (Section 4d), AAPL விலை எங்க முடிஞ்சாலும் Maple Leaf Pension Fund-ஓட அதிகபட்ச இழப்பு என்ன — அந்தத் தொகை trade date-லயே fix ஆகறது ஏன், அதிகபட்ச *லாபம்* அப்படி இல்லாதது ஏன்?
4.  Listed equity option-ஓட Clearing stage OCC-க்கும், US cash equity trade-ஓட Clearing stage NSCC-க்கும் போறது ஏன் — இவை ரெண்டு தொப்பி போட்ட ஒரே அமைப்பா, முற்றிலும் தனி CCPs ஆ?
5.  Section 6 table-ஐ பயன்படுத்தி, Coca-Cola trade record-ல Bank Of Toronto-வையே identify பண்றது எந்த identifier? trade ஆகற instrument-ஐ identify பண்ற மூணு எவை?
6.  Coca-Cola shares மேல Total Return Swap, ரெண்டு dealers இடையே bilateral-ஆ பேசப்பட்டு, பின்னாடி தானாக முன்வந்து central clearing-க்கு submit ஆகுது. அதனால அது "OTC"-ல இருந்து "Exchange-Traded" ஆகி மாறுமா? Section 7-ஓட இரண்டு-axis model-ஐ வெச்சு விளக்குங்க.
7.  Bank Of Toronto தன் Coca-Cola shares-ஐ short-sell பண்ண விரும்பற வேற firm-க்கு கடன் கொடுத்தா, அந்த குறிப்பிட்ட transaction-ஓட reporting-ஐ Section 8-ல இருந்து எந்த regulation ஆளுது — MiFID, EMIR, இல்ல SFTR — மற்ற ரெண்டும் ஏன் பொருந்தாது?
8.  Section 9-ஐ வெச்சு, Bank Of Toronto-ஓட Toronto desk TSX-ல 50,000 Loblaw shares வாங்கினா, EMIR-ல ஒரு interest rate swap-க்கு நடக்கற மாதிரி, OTC-derivatives பாணி Trade Repository-க்கு ஒரு report உருவாகுமா? இல்லன்னா, அதை உண்மையில பிடிச்சு மேற்பார்வை பண்ணும் ரெண்டு Canadian mechanisms என்ன?

தயாரா இருக்கும்போது பதில் சொல்லுங்க — அப்புறம் Asset Class Roadmap-ஓட அடுத்த நிறுத்தம் **[Module 5](../docs/1-capital-markets/module-05-fixed-income.html): Fixed Income**.

---

## Answers — Recap Quiz, விளக்கத்தோட

**1. ரெண்டு skip ஆன quarters-க்கு Goldman Sachs CAD 0 பெறும் — non-cumulative-னா அவை நிரந்தரமா இழக்கப்பட்டவை, அப்புறம் கொடுக்கப்படாது. Cumulative-ஆ இருந்திருந்தா, மூணாவது quarter-ஓட வழக்கமான CAD 562,500-க்கு மேல skip ஆன ரெண்டு quarters-ஓட dividends-ஐயும் (2 x CAD 562,500 = CAD 1,125,000) பெறும் — மொத்தம் CAD 1,687,500 catch-up payment.**

Section 4b இதை நேரடியா வரையறுக்குது: "non-cumulative" = skip ஆன dividend அப்படியே போயிடும்; cumulative preferred share-ல (அல்லது தவறினா default ஆகற bond coupon-ல) கொடுக்கப்படாத தொகை சேர்ந்துட்டே இருக்கும், common shareholders-க்கு எந்த dividend-ம் மறுபடி தொடங்கறதுக்கு முன்னாடி கொடுக்கப்படணும்.

**2. Variation Margin = -USD 20,000, Maple Leaf Pension Fund-ஓட margin account-ல இருந்து counterparty பக்கத்துக்கு (CME Clearing வழியா) நகரும்.**

```text
Variation Margin = (5,900.00 - 5,920.00) x 50 x 20 = -USD 20,000
```

Maple Leaf Pension Fund future-ஐ வாங்கி (long) வெச்சிருக்கறதால, index இறங்கினதும் position மதிப்பு இழந்துச்சு — Section 4c-ஓட தினசரி mark-to-market mechanic திசை எதுவா இருந்தாலும் ஒரே மாதிரி ஓடும்; விலை position-ஐ எதிர்த்து நகர்ந்தா பணம் எதிர்த்திசைல நகரும், அவ்வளவுதான்.

**3. அதிகபட்ச இழப்பு = USD 185,000, கொடுத்த முழு premium — trade date-லயே fix, ஏன்னா அது முன்கூட்டியே கொடுத்த விலை; அதிகபட்ச *லாபம்* கோட்பாட்டளவில் வரம்பற்றது (அல்லது ரொம்பப் பெரியது) — expiry-க்குள்ள AAPL விலை எவ்வளவு உயரலாம்ங்கறதைப் பொறுத்தது, அது trade date-ல தெரியாது.**

Section 4d-ஓட Scenario B இதே முடிவைக் காட்டுது — option பயனற்று expire ஆகறது, AAPL strike-க்குக் கீழ எவ்வளவு இறங்கினாலும், இழப்பை கொடுத்த premium-ல நிறுத்துது. நிலையான, தெரிஞ்ச அதிகபட்ச இழப்பு vs திறந்த-முனை சாத்திய லாபம் என்ற இந்த சமச்சீரற்ற தன்மை, long option-ஓட வரையறுக்கும் economic அம்சம்; Section 4c-ஓட future-ல லாபம்/இழப்பு ரெண்டும் எந்தத் திசையிலயும் கோட்பாட்டளவில் வரம்பற்றது.

**4. முற்றிலும் தனி CCPs — ஒரே அமைப்பு இல்ல.**

Section 5-ஓட table-உம் disambiguation callout-உம் இதை வெளிப்படையா சொல்லுது: NSCC (DTCC-யோட subsidiary) cash equity trades-ஐ clear பண்ணுது; OCC (Options Clearing Corporation) US-listed options-க்கு மட்டுமே அர்ப்பணிக்கப்பட்ட முற்றிலும் வேற, சுதந்திரமான CCP. Option exercise ஆகும்போது ரெண்டும் கடைசில underlying shares-ஐ DTC வழியா settle பண்ணுது; ஆனா *clearing* செயல்பாடு ரெண்டு வேற அமைப்புகள்ல இருக்கு.

**5. Bank Of Toronto-வை (dealer-ஆ) அதன் LEI identify பண்ணுது. Instrument-ஐ ISIN (US1912161007), CUSIP (191216100), Ticker (KO) identify பண்ணுது.**

Section 6-ஓட diagram இதை நேரடியா சொல்லுது — LEI "யார்"-ங்கறதுக்கு பதில்; ISIN/CUSIP/Ticker (தேவைப்படும் இடத்துல SEDOL/FIGI-உம்) "எது"-ங்கறதுக்கு. Maple Leaf Pension Fund-உம் Goldman Sachs-உம் அதே trade record-ல buyer/seller-ஆ தங்கள் சொந்த தனி LEIs-ஐ சுமக்கும்.

**6. இல்ல — அது OTC-யாவே இருக்கும். Central clearing "Bilateral vs Centrally Cleared" axis-ஐ மட்டும் மாத்தும், "Exchange vs OTC" axis-ஐ இல்ல.**

Section 7-ஓட இரண்டு-axis model இதை துல்லியமா வரையுது: "trade எங்க பேசப்பட்டது" (OTC, ஏன்னா அது ஒரு bilateral dealer பேச்சுவார்த்தை) அது எப்படி உருவாச்சுங்கறதால நிரந்தரமா fix ஆனது, அதுக்கப்புறம் என்ன நடந்தாலும். Clearing-க்கு submit பண்றது Section 7 table-ல "OTC + Bilateral" cell-ல இருந்து "OTC + Centrally Cleared" cell-க்கு மட்டும்தான் நகர்த்தும் — அது ஒருபோதும் exchange-ல execute ஆனதில்லை என்பதால் "Exchange-Traded" ஆகவே ஆகாது.

**7. SFTR ஆளுது — ஏன்னா shares-ஐ நேரடியா கடன் கொடுக்கறது (அவற்றை reference பண்ற derivative இல்ல) ஒரு Securities Financing Transaction, SFTR இருக்கறதே இந்த product குடும்பத்துக்காகத்தான் ([systems-landscape](../docs/1-capital-markets/systems-landscape.html) §12). MiFID பொருந்தாது, ஏன்னா இது shares மேல MiFID-scope "executed transaction" இல்ல — விற்பனை/வாங்குதல் நடக்கலை. EMIR பொருந்தாது, ஏன்னா securities lending transaction ஒரு OTC *derivative* இல்ல — notional-ஐ reference பண்ற contract இல்ல, shares-தான் தற்காலிகமா கை மாறுது.**

Section 8-ஓட table இந்த scenario-வை SFTR உள்ளடக்குதுன்னு குறிக்குது. [Module 2](../docs/1-capital-markets/module-02-hierarchy.html)-ஓட asset-class hierarchy-ஓட இணைஞ்ச முக்கிய சோதனை: இது security-லயே trade ஆ (financing-வகை transaction — lending/repo — என்றால் SFTR; நேரடி buy/sell என்றால் சாதாரண MiFID transaction reporting) அல்லது security-ஐ reference பண்ற contract ஆ (OTC என்றால் EMIR)? Securities lending முழுக்க முதல் வகை, குறிப்பா SFTR கட்டப்பட்ட financing உப-வகை.

**8. இல்ல — plain cash equity trade Trade Repository-க்கே போகாது; TR reporting OTC-derivatives-க்கு மட்டுமான mechanism. அதுக்குப் பதிலா, trade ரெண்டு தனி Canadian mechanisms வழியா பிடிக்கப்பட்டு settle ஆகுது: (1) CIRO-ஓட UMIR-சார்ந்த market surveillance, (2) CDS-ல CDSX-ஓட Continuous Net Settlement (CNS) process.**

Section 9-ஓட table இதை நேரடியா சொல்லுது; [Module 2](../docs/1-capital-markets/module-02-hierarchy.html) §7, [Module 14](../docs/2-reporting/module-14-regulatory-reporting.html) §1-ஓட, TR reporting OTC derivatives-க்கு மட்டுமே என்ற கருத்துடன் இணையுது. Trade நடந்த venue-ல இருந்து execution data-வை CIRO conduct/surveillance நோக்கத்துக்காக எடுத்துக்கும்; CDSX trade-ஐ DvP-ஆ, T+1-ல net பண்ணி settle பண்ணும்; Bank of Canada CDSX-ஐ designated Financial Market Infrastructure-ஆ முறைமை மேற்பார்வை செய்யும்.
