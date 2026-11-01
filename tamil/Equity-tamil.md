# Module 4 — Equity (Equity / பங்குகள்)

> **Audience:** Finance/Capital Markets-ல் புதிதாக வரும் Java Backend Architect / Software Architect.
> **Working context:** கனடாவில் ஒரு Capital Markets / Investment Bank backend team-ல் வேலை செய்கிறோம் என்று வைத்துக்கொள்வோம்.
> **Example bank:** **Bank Of Toronto** — கற்பனை வங்கி, விளக்கத்திற்காக மட்டும்.
> **Currencies:** முக்கியமாக **CAD** மற்றும் **USD** பயன்படுத்தப்படும்.
> **Roadmap:** Equity → Fixed Income → FX → Commodities → Rates → Credit.

---

## 1. Equity என்றால் என்ன?

**Equity = ஒரு நிறுவனத்தின் ownership-ல் ஒரு சிறிய பகுதி.**

ஒரு company 10 million shares வெளியிட்டிருக்கிறது என்று வைத்துக்கொள்வோம். நீங்கள் 10,000 shares வைத்திருந்தால், அந்த company-யின் ownership-ல் ஒரு சிறிய பகுதி உங்களிடம் இருக்கிறது.

Equity-க்கு:

- Fixed maturity date இல்லை
- Fixed coupon இல்லை
- Principal திருப்பித் தரப்படும் என்ற guarantee இல்லை
- Return இரண்டு வழிகளில் வரலாம்:
  1. Share price அதிகரிப்பதால்
  2. Dividend கிடைப்பதால்

ஆனால் dividend **guaranteed அல்ல**. Company board dividend declare செய்தால் மட்டுமே அது கிடைக்கும்.

### முக்கியமான Equity terms

| Term | எளிய விளக்கம் |
|---|---|
| Share | Company ownership-ன் ஒரு unit |
| Outstanding Shares | தற்போது investors வைத்திருக்கும் மொத்த shares |
| Market Capitalization | Share Price × Outstanding Shares |
| Dividend | Shareholders-க்கு company declare செய்யும் cash/stock payment |
| Voting Rights | Common shareholders-க்கு பொதுவாக share ஒன்றுக்கு ஒரு vote |
| Corporate Action | Stock split, buyback, rights issue, spin-off போன்ற issuer events |

### Equity vs Bond

| | Equity | Fixed Income / Bond |
|---|---|---|
| Ownership | ஆம் | இல்லை |
| Maturity | இல்லை | உள்ளது |
| Return | Price gain + dividend | Coupon + principal |
| Return certainty | Variable | Contractual |
| Bankruptcy priority | கடைசி | Equity-க்கு முன்னால் |
| Principal repayment | Guarantee இல்லை | Maturity-ல் பொதுவாக contractual repayment |

### Backend architect-க்கு முக்கியமான point

**Equity trade-ல் நாம் உண்மையான shares-ஐ trade செய்கிறோம்.**

Bond-ல் debt claim.

Option/Future போன்ற derivative-ல் underlying share/index-ன் value-ஐ reference செய்யும் **contract**-ஐ trade செய்கிறோம்.

---

## 2. Equity Products

```text
EQUITY
│
├── Cash Equity
│   ├── Common Stock
│   └── Preferred Stock
│
└── Equity Derivatives
    ├── Equity Futures
    └── Equity Options
```

### 2.1 Common Stock

Company-யின் ordinary ownership share.

உதாரணம்:

```text
Shopify Inc.

Quantity     = 1,000 shares
Share Price  = USD 150

Trade Value
= 1,000 × USD 150
= USD 150,000
```

ஒரு investor 1,000 shares வாங்குகிறார்.

### 2.2 Preferred Stock

Preferred shares-க்கு பொதுவாக common shares-ஐ விட dividend மற்றும் liquidation priority அதிகமாக இருக்கும்.

Example:

```text
Bank Of Toronto Preferred Share:

Shares       = 2,000,000
Par Value    = CAD 25
Dividend     = 4.50%

Annual dividend per share
= CAD 25 × 4.50%
= CAD 1.125

Total annual dividend
= 2,000,000 × CAD 1.125
= CAD 2,250,000

Quarterly
= CAD 2,250,000 / 4
= CAD 562,500
```

#### Non-cumulative என்றால்?

ஒரு quarter dividend board declare செய்யவில்லை என்றால், அந்த dividend பின்னர் automatically கிடையாது.

Cumulative preferred என்றால் skipped dividend பின்னர் payable ஆக accumulate ஆகும்.

---

## 3. Security vs Contract

இது backend system design-ல் மிகவும் முக்கியமான distinction.

### Cash Equity

**Stock = REAL SECURITY**

உதாரணம்:

Shopify shares ஏற்கனவே issue செய்யப்பட்டுள்ளன.

ஒரு investor மற்றொரு investor-க்கு shares விற்கும்போது:

```text
Investor A
    |
    |  Sell 1,000 shares
    v
Investor B
```

புதிய security உருவாகவில்லை.

Existing ownership transfer மட்டுமே நடக்கிறது.

### Equity Option

Option ஒரு contract.

உதாரணம்:

```text
AAPL Call
Strike = USD 250
Expiry = 15-Jan-2027
```

இந்த option trade செய்யப்பட்டால், அந்த நேரத்தில் option contract உருவாகிறது / transfer ஆகிறது.

அது underlying Apple shares அல்ல.

### Backend perspective

```text
Cash Equity:
Security ID
Quantity
Price
Trade Date
Settlement Date

Equity Option:
Underlying
Strike
Expiry
Option Type
Premium
Contract Quantity
```

---

## 4. Canadian Example — Cash Equity Trade

இப்போது நாம் கனடாவில் வேலை செய்கிற backend architect என்று வைத்துக்கொள்வோம்.

```text
Buyer    = Maple Leaf Pension Fund
Dealer   = Bank Of Toronto
Stock    = Royal Bank of Canada (RBC)
Currency = CAD
Exchange = TSX

Trade
BUY 10,000 RBC shares
Price = CAD 185.40

Trade Value
= 10,000 × CAD 185.40
= CAD 1,854,000
```

Trade record:

```text
Trade ID       = BOT-CA-EQ-2026-001234
Instrument     = RBC Common Share
Quantity       = 10,000
Price          = CAD 185.40
Currency       = CAD
Trade Value    = CAD 1,854,000
Venue          = TSX
Buy/Sell       = BUY
Trade Date     = T
Settlement     = T+1
```

---

## 5. Canadian Equity Trade Lifecycle

Capital Markets backend system-ல் இதை இப்படி பார்க்கலாம்:

```text
Order
  ↓
Execution
  ↓
Trade Capture
  ↓
Confirmation / Matching
  ↓
Clearing
  ↓
Settlement
  ↓
Position Update
  ↓
Valuation / P&L
  ↓
Reporting / Reconciliation
```

### Step 1 — Order

Maple Leaf Pension Fund:

```text
BUY 10,000 RBC shares
```

Bank Of Toronto OMS-க்கு order வருகிறது.

### Step 2 — Execution

Order TSX அல்லது applicable Canadian trading venue-க்கு route ஆகிறது.

Suppose:

```text
Executed Price = CAD 185.40
Quantity       = 10,000
```

### Step 3 — Trade Capture

Backend trade service:

```text
Trade ID = BOT-CA-EQ-2026-001234
```

Database:

```text
trade_id
instrument_id
side
quantity
price
currency
trade_date
settlement_date
venue
buyer
seller
```

---

## 6. Step 4 — Confirmation / Matching

Trade details buyer/seller records-க்கு match செய்யப்படுகின்றன.

Example:

```text
BUY:
10,000 RBC
CAD 185.40
T

SELL:
10,000 RBC
CAD 185.40
T
```

Quantity மற்றும் price match ஆனால்:

**MATCHED**

Mismatch example:

```text
Buyer quantity  = 10,000
Seller quantity = 9,000
```

அப்போது:

**MATCH BREAK**

Backend system reconciliation / exception queue-க்கு அனுப்பலாம்.

---

## 7. Step 5 — Clearing

Canada equity infrastructure-ல் CDS Clearing and Depository Services / CDSX முக்கியமான infrastructure.

Clearing-ன் முக்கிய வேலை:

- Trades aggregate/net செய்வது
- Settlement obligations உருவாக்குவது
- Settlement risk குறைப்பது

உதாரணம்:

ஒரே நாளில் Bank Of Toronto பல RBC trades செய்திருக்கலாம்.

```text
BUY  10,000 RBC
SELL  7,000 RBC
BUY   2,000 RBC
SELL  1,000 RBC

Net position:
Buy  = 12,000
Sell = 8,000

Net = BUY 4,000 shares
```

இதனால் settlement obligations குறைக்கப்படலாம்.

---

## 8. Step 6 — Settlement

Canada cash equity settlement-ஐ simplified backend view-ல்:

```text
Trade Date = Monday
Settlement = Tuesday (T+1)
```

**DvP = Delivery versus Payment**

அதாவது:

```text
Shares delivered
      ↕
Cash paid
```

```text
Buyer:
Pays CAD 1,854,000
Receives 10,000 RBC shares

Seller:
Delivers 10,000 RBC shares
Receives CAD 1,854,000
```

### Backend events

```text
TRADE_CAPTURED
       ↓
TRADE_MATCHED
       ↓
CLEARING_COMPLETE
       ↓
SETTLEMENT_INSTRUCTION_CREATED
       ↓
SETTLED
       ↓
POSITION_UPDATED
```

---

## 9. Position

Settlement successful ஆன பிறகு:

```text
Before:
RBC Position = 50,000 shares

Trade:
BUY 10,000

After:
RBC Position = 60,000 shares
```

Position service இதை store செய்யலாம்:

```text
instrument_id
account_id
quantity
average_cost
market_value
currency
```

---

## 10. Valuation / MTM

இது Equity-யில் மிகவும் easy-ஆக புரியும்.

```text
Purchase:
10,000 shares
Price = CAD 185.40

Cost = CAD 1,854,000
```

ஒரு வாரம் கழித்து RBC share price:

```text
CAD 192.00

Market Value
= 10,000 × CAD 192
= CAD 1,920,000

Unrealized P&L
= CAD 1,920,000 - CAD 1,854,000
= CAD 66,000 profit
```

Price falls:

```text
Price = CAD 180

Market Value
= 10,000 × 180
= CAD 1,800,000

Unrealized P&L
= 1,800,000 - 1,854,000
= -CAD 54,000
```

---

## 11. CAD vs USD — Canadian Backend Example

Canada-வில் வேலை செய்தாலும் US equities நிறைய trade செய்யப்படும்.

அதனால் ஒரு backend system-ல் trade currency மற்றும் reporting/base currency வேறுபடலாம்.

Example — Canadian pension fund buys Apple:

```text
Instrument     = Apple
Quantity       = 5,000
Price          = USD 250
Trade Value    = USD 1,250,000
Trade Currency = USD
```

ஆனால் Bank Of Toronto-ன் reporting currency CAD என்று வைத்துக்கொள்வோம்.

```text
FX rate: 1 USD = 1.36 CAD

CAD equivalent
= USD 1,250,000 × 1.36
= CAD 1,700,000
```

### Important backend fields

```text
trade_currency      = USD
reporting_currency   = CAD

trade_price   = 250.00
quantity      = 5000
trade_value   = 1,250,000 USD

fx_rate           = 1.36 CAD/USD
reporting_value   = 1,700,000 CAD
```

---

## 12. CAD/USD Example — P&L with FX

Apple shares:

```text
Buy:
5,000 shares
USD 250

Cost = USD 1,250,000
```

Later:

```text
Apple Price = USD 270

Market Value
= 5,000 × 270
= USD 1,350,000

USD equity P&L
= USD 1,350,000 - USD 1,250,000
= USD 100,000
```

Now suppose current FX:

```text
1 USD = 1.40 CAD

Current CAD value
= USD 1,350,000 × 1.40
= CAD 1,890,000

Original CAD cost
= USD 1,250,000 × 1.36
= CAD 1,700,000

Total CAD P&L
= CAD 1,890,000 - CAD 1,700,000
= CAD 190,000
```

இதில் இரண்டு components இருக்கின்றன:

```text
Total CAD P&L
     |
     ├── Equity price P&L
     |
     └── FX P&L
```

இதுதான் Canada-based backend system-ல் மிகவும் முக்கியமான concept.

---

## 13. Equity Future — USD Example

Suppose Canadian pension fund buys S&P 500 futures.

```text
Contracts  = 20
Index      = 5,920
Multiplier = USD 50

Notional
= 20 × 5,920 × USD 50
= USD 5,920,000
```

Next day:

```text
Index = 5,935

Variation Margin
= (5,935 - 5,920) × 50 × 20
= USD 15,000
```

Buyer receives: USD 15,000

இது daily MTM.

Cash equity போல settlement date வரை price difference மட்டும் accumulate ஆகாது; futures-ல் daily variation margin mechanism இருக்கும்.

---

## 14. Equity Option — USD Example

Canadian pension fund buys:

```text
AAPL Call
Strike       = USD 250
Expiry       = 15-Jan-2027
Contracts    = 100
Premium      = USD 18.50/share
Contract     = 100 shares

Premium
= 100 × 100 × USD 18.50
= USD 185,000
```

Expiry: AAPL = USD 270

```text
Intrinsic value
= (270 - 250) × 10,000
= USD 200,000

Net P&L
= USD 200,000 - USD 185,000
= USD 15,000
```

Expiry: AAPL = USD 240

Call is out of the money.

```text
Intrinsic Value = USD 0
```

Option expires worthless.

Maximum loss: USD 185,000

---

## 15. Equity Identifiers

ஒரு backend trade record-ல் "எது trade செய்யப்படுகிறது?" மற்றும் "யார் trade செய்கிறார்கள்?" என்பதை வேறுபடுத்த வேண்டும்.

| Identifier | Identifies | Example |
|---|---|---|
| ISIN | Instrument globally | US1912161007 |
| CUSIP | North American instrument | 191216100 |
| Ticker | Exchange-specific symbol | KO |
| SEDOL | UK/Ireland-oriented identifier | 0008373 |
| FIGI | Instrument / venue-level identifier | BBG... |
| LEI | Legal entity | Bank / Fund entity |
| UPI | OTC derivative product template | Equity TRS போன்றவற்றுக்கு |

### Simple rule

```text
ISIN / CUSIP / Ticker / SEDOL / FIGI
        ↓
WHAT is being traded?

LEI
        ↓
WHO is trading?

UPI
        ↓
WHAT OTC derivative PRODUCT is this?
```

---

## 16. Canadian Equity Backend Architecture

ஒரு Canadian investment bank backend system-ஐ simplified architecture-ஆக:

```text
                 FRONT OFFICE
                      |
                      v
                 OMS / EMS
                      |
                    FIX
                      |
                      v
              Trading Venue
             TSX / ATS etc.
                      |
                      v
              Trade Capture
                      |
              +-------+-------+
              |               |
              v               v
        Confirmation      Position
              |               |
              v               v
          Clearing         Valuation
              |               |
              v               v
             CDSX          P&L / MTM
              |
              v
          Settlement
              |
              v
       Reconciliation
              |
              v
          Reporting
```

---

## 17. Backend Microservices View

Java/Spring Boot architecture-ல் இதை services-ஆக பிரிக்கலாம்:

```text
Order Service
     ↓
Execution Service
     ↓
Trade Capture Service
     ↓
Reference Data Service
     ↓
Confirmation Service
     ↓
Clearing Service
     ↓
Settlement Service
     ↓
Position Service
     ↓
Valuation Service
     ↓
P&L Service
     ↓
Reporting Service
```

Example Trade Event:

```json
{
  "tradeId": "BOT-CA-EQ-2026-001234",
  "instrument": "RBC",
  "isin": "CA7800871021",
  "side": "BUY",
  "quantity": 10000,
  "price": 185.40,
  "tradeCurrency": "CAD",
  "tradeValue": 1854000,
  "venue": "TSX",
  "tradeDate": "2026-09-07",
  "settlementDate": "2026-09-08"
}
```

---

## 18. Equity Corporate Actions

Equity backend-ல் Corporate Actions மிகவும் முக்கியம்.

Examples:

- Stock Split
- Dividend
- Rights Issue
- Buyback
- Spin-off
- Merger

### Stock Split Example

```text
Before:
1,000 shares
Price = CAD 200
Market Value = CAD 200,000

2-for-1 split:
Shares = 2,000
Price  = CAD 100

Market value
= 2,000 × CAD 100
= CAD 200,000
```

Ownership value உடனடியாக மாறவில்லை; quantity மற்றும் price adjust ஆகின்றன.

Backend-ல்:

```text
Corporate Action Event
       ↓
Position Adjustment
       ↓
Cost Basis Adjustment
       ↓
Market Data Adjustment
       ↓
P&L / Reporting Recalculation
```

---

## 19. Equity Lending மற்றும் SFTR

Bank Of Toronto-விடம் RBC shares உள்ளன.

மற்றொரு firm short sell செய்ய RBC shares தேவைப்படுகிறது.

Bank Of Toronto: Lends RBC shares.

இது ordinary equity BUY/SELL trade அல்ல.

இது Securities Lending.

இந்த transaction-க்கு relevant regulatory framework: **SFTR**

Key distinction:

```text
Buy/Sell actual equity
        → Cash Equity

Lend/Borrow actual shares
        → Securities Financing

OTC derivative referencing equity
        → Equity Derivative / potentially EMIR scope
```

---

## 20. Exchange vs OTC

இரண்டு கேள்விகளை தனித்தனியாக பார்க்க வேண்டும்.

**Question 1** — Trade எங்கே negotiated/executed?

```text
Exchange
   OR
OTC
```

**Question 2** — Counterparty relationship எப்படி உள்ளது?

```text
Bilateral
   OR
Centrally Cleared
```

அதனால்:

```text
Exchange + Centrally Cleared
OTC + Bilateral
OTC + Centrally Cleared
```

என்று combinations இருக்கலாம்.

Central clearing செய்ததால் OTC trade exchange-traded ஆக மாறாது.

---

## 21. Canadian Equity Regulatory / Infrastructure View

| Entity | Equity-ல் role |
|---|---|
| TSX | Major Canadian equity trading venue |
| TSX Venture | Venture / smaller-company listings and trading |
| CDS / CDSX | Canadian securities clearing, depository and settlement infrastructure |
| CIRO | Canadian investment dealer / trading conduct and market integrity oversight |
| CSA | Canadian securities regulators' umbrella coordination |
| Bank of Canada | Relevant financial-market-infrastructure oversight |
| Lynx | Large-value CAD payment infrastructure |

### Important

ஒரு plain Canadian cash equity trade-ஐ IRS போன்ற OTC derivative trade போல Trade Repository-க்கு அனுப்புவது என்ற model இல்லை.

Equity trade-ன் முக்கிய infrastructure trail:

```text
Trading Venue
      ↓
Trade / Market Surveillance
      ↓
CDS / CDSX
      ↓
T+1 Settlement
```

---

## 22. Complete Canadian Equity Example

இப்போது எல்லாவற்றையும் ஒரே example-ல் பார்க்கலாம்.

Trade:

```text
Maple Leaf Pension Fund:
BUY 50,000 shares of Loblaw Companies Ltd.
Ticker = L
Venue  = TSX
Price  = CAD 178.40

Trade Value
= 50,000 × CAD 178.40
= CAD 8,920,000
```

Lifecycle:

```text
09:30 Order received
        ↓
09:31 Order routed to trading venue
        ↓
09:31 Executed @ CAD 178.40
        ↓
09:31 Trade captured
      Trade ID = BOT-CA-2026-004471
        ↓
09:45 Trade matched
        ↓
      Clearing
      CDSX / CNS
        ↓
T+1   Settlement
        ↓
      50,000 shares added to position
        ↓
      Daily MTM
        ↓
      P&L
        ↓
      Reconciliation / Reporting
```

---

## 23. USD Equity from Canadian Bank

இப்போது அதே Canadian backend-ல் US equity.

```text
Buyer       = Canadian Pension Fund
Instrument  = Apple
Quantity    = 20,000
Price       = USD 250
Currency    = USD

Trade Value
= 20,000 × USD 250
= USD 5,000,000
```

Suppose reporting currency = CAD.

```text
FX: 1 USD = 1.36 CAD

CAD equivalent
= USD 5,000,000 × 1.36
= CAD 6,800,000
```

Database-ல் ideally:

```text
quantity
trade_price
trade_currency
trade_value
fx_rate
reporting_currency
reporting_value
```

என்று separate values வைத்திருக்க வேண்டும்.

Trade currency-ஐ reporting currency-யுடன் mix செய்யக்கூடாது.

---

## 24. Equity-ஐ Backend Architect ஆக எப்படி நினைவில் வைத்துக்கொள்வது?

ஒரு simple mental model:

```text
EQUITY
  |
  +-- What?
  |     Share
  |
  +-- How much?
  |     Quantity
  |
  +-- At what price?
  |     Market Price
  |
  +-- In which currency?
  |     CAD / USD
  |
  +-- Where?
  |     TSX / NYSE / Nasdaq etc.
  |
  +-- Who?
  |     Buyer / Seller / Legal Entity
  |
  +-- When?
  |     Trade Date / Settlement Date
  |
  +-- Where is it now?
  |     Position
  |
  +-- What is it worth?
  |     Market Value
  |
  +-- Profit/Loss?
  |     MTM / P&L
  |
  +-- Any issuer event?
        Corporate Action
```

---

## 25. Equity vs Equity Derivative — Final Mental Model

```text
                 EQUITY
                    |
        +-----------+-----------+
        |                       |
    CASH EQUITY             DERIVATIVE
        |                       |
    Actual Share          Contract referencing
        |                 Share / Index
        |                       |
   Common / Preferred     Future / Option / TRS
        |                       |
   Quantity matters        Contract terms matter
        |                       |
   Settlement              Margin / Premium /
   of shares               MTM / Exercise
```

---

## 26. Recap — நீங்கள் Backend-ல் கண்டிப்பாக தெரிந்திருக்க வேண்டியது

**1. Equity என்ன?**

Company ownership.

**2. Cash equity என்ன?**

Actual shares.

**3. Equity derivative என்ன?**

Share/index-ன் value-ஐ reference செய்யும் contract.

**4. Trade value எப்படி?**

```text
Quantity × Price
```

**5. Market value எப்படி?**

```text
Current Quantity × Current Market Price
```

**6. Equity P&L எப்படி?**

```text
Current Market Value - Cost
```

**7. CAD/USD ஏன் முக்கியம்?**

Canadian bank CAD reporting வைத்திருந்தாலும் US equity USD-ல் trade செய்யப்படலாம்.

```text
USD Trade Value
      ↓
FX Conversion
      ↓
CAD Reporting Value
```

**8. Position எப்போது update?**

Successful settlement / position processing பிறகு.

**9. Corporate Action ஏன் முக்கியம்?**

Share quantity, price, cost basis, dividend போன்றவை மாற்றப்படலாம்.

**10. Backend architect-க்கு முக்கியமான entities**

```text
Instrument
Trade
Order
Execution
Party
Account
Position
Price
FX Rate
Settlement
Corporate Action
P&L
Regulatory Data
```

---

## 27. One-Line Summary

Equity என்பது company ownership-ன் ஒரு பகுதி; backend system-ல் ஒரு equity trade-ஐ புரிந்து கொள்ள Instrument → Order → Execution → Trade → Clearing → T+1 Settlement → Position → CAD/USD Valuation → P&L → Corporate Actions → Reporting என்ற flow-ஐ புரிந்து கொண்டால் போதும்.
