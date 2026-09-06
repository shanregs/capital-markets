# Interest Rate Swap (IRS) — எளிய தமிழ் விளக்கம்

## 1. ABC ஏன் IRS செய்கிறது?

முதலில் ஒரு simple example பார்ப்போம்.

ABC ஒரு loan எடுத்திருக்கிறது.

அந்த loan-க்கு interest:

**CORRA + 1%**

என்று இருக்கிறது.

உதாரணமாக CORRA தற்போது 5.2% என்றால்:

```text
Loan Interest
= CORRA + 1%
= 5.2% + 1%
= 6.2%
```

பிரச்சனை என்ன?

CORRA ஒரு fixed rate இல்லை. அது market நிலைமைக்கு ஏற்ப மாறிக்கொண்டே இருக்கும்.

இன்று CORRA 5.2% இருக்கலாம்.

நாளை அது 8% ஆகலாம்.

அப்படி ஆனால் ABC-யின் loan cost:

```text
8% + 1%
= 9%
```

ஆகிவிடும்.

அதனால் ABC-க்கு ஒரு uncertainty இருக்கிறது:

> "எதிர்காலத்தில் நான் எவ்வளவு interest கட்ட வேண்டி வரும்?"

இந்த uncertainty-யை குறைக்க ABC ஒரு **Interest Rate Swap (IRS)** செய்கிறது.

IRS-ல் ABC:

```text
Pay     = 6% Fixed
Receive = CORRA
```

என்று agreement செய்கிறது.

---

# 2. Loan + IRS சேர்த்தால் என்ன நடக்கும்?

ABC-யின் Loan:

```text
Pay CORRA + 1%
```

IRS:

```text
Pay 6% Fixed

Receive CORRA
```

இரண்டையும் சேர்த்து பார்ப்போம்.

```text
LOAN

Pay CORRA + 1%

        +

IRS

Pay 6%
Receive CORRA
```

Loan-ல் ABC செலுத்தும் CORRA-வை IRS மூலம் பெறும் CORRA offset செய்கிறது.

அதனால்:

```text
CORRA + 1% + 6% - CORRA

CORRA cancel ஆகிறது.

= 7%
```

அதாவது ABC-யின் effective interest cost:

**சுமார் 7% fixed**

ஆகிவிடுகிறது.

இதுதான் IRS-ன் basic economic purpose.

---

# 3. IRS-ல் இருக்கும் இரண்டு Legs

ஒரு standard IRS-ல் பொதுவாக இரண்டு legs இருக்கும்.

### Fixed Leg

ABC:

**6% Fixed Rate pay செய்கிறது.**

### Floating Leg

ABC:

**CORRA receive செய்கிறது.**

அதை இப்படி நினைத்துக்கொள்ளலாம்:

```text
                  IRS
                   |
          +--------+--------+
          |                 |
      Fixed Leg        Floating Leg
          |                 |
       Pay 6%          Receive CORRA
```

அதாவது IRS என்பது:

> "நான் Fixed rate கொடுக்கிறேன்; அதற்கு பதிலாக Floating rate பெறுகிறேன்"

என்ற arrangement.

---

# 4. Notional என்றால் என்ன?

இப்போது IRS-க்கு ஒரு amount தேவை.

உதாரணமாக:

```text
Notional = ₹100 crore
```

இதன் அர்த்தம் ABC ₹100 crore-ஐ Bank-க்கு கொடுக்கிறது என்று இல்லை.

**Notional என்பது interest calculation செய்ய பயன்படுத்தப்படும் reference amount.**

உதாரணமாக:

```text
Notional = ₹100 crore
Fixed Rate = 6%
```

ஒரு வருடத்திற்கு:

```text
₹100 crore × 6%

= ₹6 crore
```

Fixed interest வரும்.

ஆனால் payment quarterly என்றால், actual calculation-ல் அந்த period-ன் **accrual factor** பயன்படுத்தப்படும்.

---

# 5. முதல் Quarterly Period

ABC-யின் IRS:

```text
Effective Date = 01-Sep-2026
Payment Date   = 01-Dec-2026
```

என்று வைத்துக்கொள்வோம்.

அதாவது முதல் accrual period:

```text
01-Sep-2026 → 01-Dec-2026
```

Assume:

| Parameter      |      Value |
| -------------- | ---------: |
| Notional       | ₹100 crore |
| Fixed Rate     |         6% |
| Accrual Factor |     90/365 |

## Fixed Leg calculation

```text
Fixed Interest
= Notional × Fixed Rate × Accrual Factor

= ₹100 crore × 6% × 90/365

≈ ₹1.479 crore
```

அதனால் ABC Fixed Leg-க்கு சுமார்:

**₹1.479 crore**

கொடுக்க வேண்டும்.

---

# 6. Floating CORRA Leg

இப்போது Floating Leg பார்ப்போம்.

இந்த period-க்கு applicable compounded CORRA:

**5.20%**

என்று எடுத்துக்கொள்வோம்.

Simplified calculation:

```text
Floating Interest
= ₹100 crore × 5.20% × 90/365

≈ ₹1.282 crore
```

அதனால் ABC Floating Leg-ல் சுமார்:

**₹1.282 crore**

பெறும்.

---

# 7. Net Settlement

பொதுவாக Fixed amount மற்றும் Floating amount-ஐ இரண்டு separate payments ஆக exchange செய்யாமல், இரண்டையும் net செய்து ஒரு payment செய்வார்கள்.

இங்கே:

```text
Fixed Payment    = ₹1.479 crore
Floating Receipt = ₹1.282 crore
```

Difference:

```text
₹1.479 crore - ₹1.282 crore

= ₹0.197 crore
```

அதாவது:

**₹19.7 lakh**

ABC Bank-க்கு செலுத்தும்.

```text
ABC
 |
 | ₹19.7 lakh
 v
Bank
```

இது simplified example.

---

# 8. இதை Loan-உடன் சேர்த்து பார்ப்போம்

ABC-யின் Loan:

```text
CORRA + 1%
```

CORRA = 5.2% என்றால்:

```text
Loan Cost
= 5.2% + 1%
= 6.2%
```

IRS:

```text
Pay    6%
Receive 5.2%
```

இப்போது இரண்டையும் சேர்ப்போம்:

```text
Loan:
Pay 5.2% + 1%

IRS:
Pay 6%
Receive 5.2%

-----------------

5.2% cancel

Effective Cost ≈ 7%
```

அதாவது ABC தனது floating-rate loan exposure-ஐ:

**சுமார் 7% fixed cost**

ஆக மாற்றிக்கொண்டது.

---

# 9. CORRA 8% ஆக உயர்ந்தால்?

இப்போது market rates உயர்கிறது என்று வைத்துக்கொள்வோம்.

```text
CORRA = 8%
```

Loan:

```text
8% + 1%

= 9%
```

அதனால் Loan-ல் ABC 9% செலவு செய்கிறது.

IRS-ல்:

```text
Pay Fixed     = 6%
Receive       = 8%
```

IRS-ன் net benefit:

```text
Receive 8%
Pay 6%

= +2%
```

இப்போது Loan + IRS:

```text
Loan = -9%
IRS  = +2%

----------------
Net  = -7%
```

அதாவது ABC-யின் effective cost மீண்டும்:

**சுமார் 7%**

---

# 10. CORRA 4% ஆக குறைந்தால்?

இப்போது CORRA குறைகிறது என்று வைத்துக்கொள்வோம்.

```text
CORRA = 4%
```

Loan:

```text
4% + 1%
= 5%
```

IRS:

```text
Pay Fixed = 6%
Receive    = 4%
```

அதனால்:

```text
Loan = 5%
IRS  = 6% - 4%

Net = 5% + 2%

= 7%
```

மீண்டும்:

**சுமார் 7%**

அதுதான் IRS-ன் hedging benefit.

---

# 11. ஆனால் ஒரு Trade-off இருக்கிறது

ABC ஒரு விஷயத்தை புரிந்துகொள்ள வேண்டும்.

IRS மூலம்:

**Uncertainty குறைகிறது.**

ஆனால் market நல்ல direction-ல் சென்றால் கிடைக்கக்கூடிய benefit-ஐயும் ABC விட்டுக்கொடுக்கிறது.

உதாரணமாக CORRA மிகவும் குறைந்துவிட்டால், IRS இல்லாமல் இருந்தால் ABC குறைந்த interest கட்டியிருக்கும்.

ஆனால் IRS இருப்பதால் அது சுமார் 7% cost-ல் lock செய்யப்பட்டிருக்கிறது.

அதனால்:

```text
Without IRS:

CORRA குறைந்தால்
       ↓
Loan cost குறையும்

CORRA உயர்ந்தால்
       ↓
Loan cost உயரும்
```

IRS உடன்:

```text
CORRA உயர்ந்தாலும்
CORRA குறைந்தாலும்

Effective cost ≈ 7%
```

இதுதான் basic trade-off:

> **Hedging uncertainty-யை குறைக்கிறது. ஆனால் favourable market movement-ன் முழு benefit கிடைக்காமல் போகலாம்.**

---

# 12. IRS ஒவ்வொரு நாளும் ஒரே Value-ல் இருக்காது

இப்போது ஒரு முக்கியமான concept.

ABC 6% Fixed Rate pay செய்யும் IRS-ல் enter ஆகிறது.

அந்த நாளில் அதன் value zero-க்கு அருகில் இருக்கலாம்.

ஆனால் market rates தினமும் மாறுகின்றன.

உதாரணமாக சில மாதங்களுக்குப் பிறகு புதிய IRS-களுக்கான market fixed rate:

```text
7%
```

ஆகிவிட்டது என்று வைத்துக்கொள்வோம்.

ABC-யிடம் ஏற்கனவே:

```text
Pay Fixed = 6%
```

என்ற IRS இருக்கிறது.

இப்போது market-ல் மற்றவர்கள் 7% pay செய்ய வேண்டியிருக்கிறது.

ABC 6% மட்டுமே pay செய்கிறது.

அதனால் ABC-யிடம் இருக்கும் IRS valuable ஆகிறது.

---

# 13. Market Rate உயர்ந்தால் / குறைந்தால்

ABC:

```text
Pay Fixed = 6%
```

என்று வைத்துக்கொள்வோம்.

Market swap rate:

```text
6% → 7%
```

ஆக உயர்ந்தால்:

**ABC-யின் existing pay-fixed swap value அதிகரிக்கும்.**

ஏன்?

ABC 6% மட்டுமே pay செய்கிறது.

புதிய market participant 7% pay செய்ய வேண்டியிருக்கிறது.

மாறாக market rate:

```text
6% → 5%
```

ஆக குறைந்தால்:

ABC-யின் 6% fixed obligation attractive ஆக இருக்காது.

அதனால்:

```text
Market Rates ↑
       ↓
Pay-Fixed IRS value ↑


Market Rates ↓
       ↓
Pay-Fixed IRS value ↓
```

---

# 14. MTM என்றால் என்ன?

இதுதான்:

**Mark-to-Market — MTM**

ஒரு குறிப்பிட்ட நாளில் IRS-ஐ market conditions அடிப்படையில் value செய்வதுதான் MTM.

உதாரணமாக valuation system:

```text
IRS Fair Value = +₹4.5 crore
```

என்று calculate செய்தால், ABC perspective-ல் அந்த IRS-க்கு:

**₹4.5 crore positive value**

இருக்கிறது என்று அர்த்தம்.

Bank B perspective-ல் அதே position:

```text
MTM = -₹4.5 crore
```

ஆக இருக்கும்.

இது simplified example.

---

# 15. IRS MTM எப்படி Calculate செய்கிறார்கள்?

இது மிகவும் முக்கியமான concept.

Valuation engine எதிர்காலத்தில் IRS மூலம் வரப்போகும் cash flows-ஐ forecast செய்கிறது.

```text
Future Fixed Cash Flows
          +
Future Floating Cash Flows
          |
          v
    Net Cash Flows
          |
          v
      Discounting
          |
          v
         PV
          |
          v
         MTM
```

Basic concept:

```text
PV
=
Future Cash Flow × Discount Factor
```

எல்லா future cash flows-க்கும் இதை calculate செய்து sum செய்வார்கள்.

ஒரு IRS-க்கு conceptually:

```text
PV(IRS)
=
PV(Floating Leg)
-
PV(Fixed Leg)
```

எந்த party perspective-ல் பார்க்கிறோம் என்பதைப் பொறுத்து sign மாறும்.

---

# 16. Valuation Engine-க்கு என்ன Data தேவை?

IRS-ஐ value செய்ய system-க்கு trade details மட்டும் போதாது.

உதாரணமாக:

```text
Trade ID
Notional
Fixed Rate
Floating Index
Trade Date
Effective Date
Maturity Date
Payment Frequency
Reset Frequency
Day Count Convention
Business Day Convention
Holiday Calendar
Payment Dates
Reset Dates
Market Curve
Discount Curve
CORRA Forward Curve
Discount Factors
```

அதாவது இரண்டு வகையான data முக்கியம்:

```text
Trade Data
     +
Market Data
     ↓
Valuation
     ↓
MTM
```

இதுதான் Capital Markets systems-ல் முக்கியமான relationship.

---

# 17. IRS Trade Lifecycle

இப்போது IRS ஒரு real Capital Markets system-ல் எப்படி travel செய்கிறது என்பதைப் பார்ப்போம்.

```text
Trade Capture
      ↓
Confirmation
      ↓
Enrichment
      ↓
Static Data
      ↓
Market Data
      ↓
Valuation
      ↓
MTM
      ↓
Risk / Exposure
      ↓
Collateral
      ↓
Interest Calculation
      ↓
Settlement
      ↓
Regulatory Reporting
```

ஒவ்வொரு stage-க்கும் தனித்தனி system/process இருக்கலாம்.

---

# 18. Trade Capture

ABC ஒரு IRS trade செய்கிறது.

Example:

| Field          | Value              |
| -------------- | ------------------ |
| Trade ID       | IRS-20260830-00001 |
| Product        | IRS                |
| Notional       | ₹100 crore         |
| Currency       | CAD                |
| Fixed Rate     | 6%                 |
| Floating Index | CORRA              |
| Direction      | Pay Fixed          |
| Trade Date     | 30-Aug-2026        |
| Effective Date | 01-Sep-2026        |
| Maturity Date  | 01-Sep-2029        |
| Frequency      | Quarterly          |

இந்த information trade capture system-ல் record செய்யப்படும்.

---

# 19. Confirmation

Trade execute ஆன பிறகு parties contractual terms-ஐ confirm செய்ய வேண்டும்.

Example:

| Field    | Value              |
| -------- | ------------------ |
| Party A  | ABC Corporation    |
| Party B  | Bank B             |
| Party A  | Pay Fixed 6%       |
| Party B  | Pay Floating CORRA |
| Notional | ₹100 crore         |
| Maturity | 01-Sep-2029        |

இதன் purpose:

> "இரு parties-க்கும் trade terms ஒரே மாதிரி இருக்கிறதா?"

என்பதை உறுதி செய்வது.

---

# 20. Enrichment

Trade capture-ல் இருக்கும் information மட்டும் regulatory processing-க்கு போதாது.

மேலும் சில information சேர்க்கப்படும்.

உதாரணமாக:

```text
ABC LEI
Bank LEI
UTI
UPI
Legal Entity
Jurisdiction
Trading Desk
Book
Counterparty Classification
Collateral Agreement
Clearing Status
Reporting Obligation
```

இதைத்தான் பொதுவாக:

**Trade Enrichment**

என்று சொல்லலாம்.

இங்கே ஒரு Regulatory Data Platform முக்கியமான role வகிக்கிறது.

---

# 21. Collateral

இப்போது IRS-ன் MTM:

```text
+₹4.5 crore
```

என்று வைத்துக்கொள்வோம்.

இதனால் counterparty exposure உருவாகலாம்.

அப்போது parties இடையே இருக்கும் **CSA — Credit Support Annex** terms அடிப்படையில் collateral தேவைப்படலாம்.

Conceptually:

```text
MTM
 ↓
Exposure
 ↓
CSA Terms
 ↓
Margin Calculation
 ↓
Collateral Call
```

Collateral ஆக பயன்படுத்தப்படக்கூடியவை:

```text
Cash
Government Securities
Other Eligible Assets
```

எந்த assets acceptable என்பதெல்லாம் agreement-ஐ பொறுத்தது.

---

# 22. Settlement

ஒரு quarter-க்கான calculation:

```text
Fixed Amount    = ₹1.479 crore
Floating Amount = ₹1.282 crore
```

Net:

```text
₹1.479 crore - ₹1.282 crore

= ₹19.7 lakh
```

Settlement system-க்கு ஒரு payment instruction போகும்.

Example:

| Field        | Value              |
| ------------ | ------------------ |
| Trade ID     | IRS-20260830-00001 |
| Payment Date | 01-Dec-2026        |
| Currency     | CAD                |
| Net Amount   | ₹19.7 lakh         |
| Direction    | ABC → Bank B       |
| Status       | PENDING            |

Payment successful ஆன பிறகு:

```text
Settlement Status = SETTLED
```

ஆகும்.

---

# 23. Regulatory Reporting

IRS போன்ற derivatives trades applicable regulatory regime-க்கு report செய்யப்பட வேண்டியிருக்கலாம்.

ஒரு conceptual regulatory record-ல்:

```text
UTI
UPI
Reporting Counterparty LEI
Other Counterparty LEI
Asset Class
Product Type
Notional
Notional Currency
Fixed Rate
Floating Rate Index
Trade Date
Effective Date
Maturity Date
Payment Frequency
Reset Frequency
Clearing Status
Collateralisation
Valuation Amount
Valuation Currency
Action Type
Event Type
Reporting Timestamp
```

போன்ற fields இருக்கலாம்.

முக்கியமான விஷயம்:

> எந்த fields தேவை, அவற்றின் exact values என்ன என்பவை எந்த regulatory regime/jurisdiction-க்கு report செய்கிறோம் என்பதைக் பொறுத்து மாறும்.

---

# 24. IRS ஏன் FX Forward-ஐ விட complicated?

FX Forward-ஐ எடுத்துக்கொள்வோம்.

ஒரு simple FX Forward:

```text
Future Date

USD ↔ INR
```

ஒரே future date-ல் exchange நடக்கும்.

ஆனால் IRS-ல் பல payment periods இருக்கும்.

உதாரணமாக:

```text
Period 1
Fixed ↔ Floating

Period 2
Fixed ↔ Floating

Period 3
Fixed ↔ Floating

...

Period N
Fixed ↔ Floating
```

அதனால் IRS-ல் அதிகமான calculations மற்றும் dates இருக்கும்.

உதாரணமாக:

```text
Reset Dates
Payment Dates
Accrual Periods
Day Count Convention
Business Day Adjustments
Reference Rate Observations
Compounding
Forward Curves
Discount Curves
Multiple Cash Flows
```

இதனால் IRS valuation மற்றும் lifecycle processing அதிகம் complicated ஆகிறது.

---

# 25. CORRA காரணமாக இன்னும் கொஞ்சம் Complexity

CORRA-based IRS-ல் floating rate ஒரு period-க்கு daily CORRA observations அடிப்படையில் determine செய்யப்படலாம்.

உதாரணமாக:

```text
Day 1  → CORRA
Day 2  → CORRA
Day 3  → CORRA
Day 4  → CORRA
...
Day 90 → CORRA
```

இந்த daily rates applicable methodology படி பயன்படுத்தப்பட்டு:

```text
Daily CORRA
     ↓
Compounding
     ↓
Compounded Rate
     ↓
Floating Interest
```

என்று செல்லலாம்.

அதனால்:

> CORRA-வை வெறுமனே எல்லா நாட்களுக்கும் average செய்து விட்டால் அதுதான் floating rate என்று நினைக்கக்கூடாது.

Contract-ல் குறிப்பிடப்பட்ட methodology முக்கியம்.

Compounded-in-arrears structure இருந்தால் daily CORRA observations applicable methodology படி compound செய்யப்படும்.

---

# 26. IRS-ல் நினைவில் வைத்துக்கொள்ள வேண்டிய 3 முக்கிய விஷயங்கள்

IRS-ஐ இப்போதைக்கு மூன்று விஷயங்களாக மட்டும் நினைவில் வைத்துக்கொள்ளுங்கள்.

## 1. Notional

Interest calculate செய்ய பயன்படுத்தப்படும் reference amount.

உதாரணம்:

```text
₹100 crore
```

பொதுவாக இந்த ₹100 crore-ஐ parties ஒருவருக்கொருவர் exchange செய்ய மாட்டார்கள்.

---

## 2. Two Legs

IRS-ல் இரண்டு legs:

```text
Fixed Leg
    ↕
Floating Leg
```

உதாரணம்:

```text
Pay Fixed 6%
Receive CORRA
```

---

## 3. Cash Flows

IRS-ல் future-ல் பல cash flows வரும்.

Valuation engine:

```text
Future Cash Flows
       ↓
Discounting
       ↓
Present Value
       ↓
MTM
```

என்று calculate செய்கிறது.

---

# 27. IRS vs FRA vs Cap vs Floor

இந்த products-க்கு இடையிலான difference மிகவும் முக்கியம்.

| Product  | Simple Meaning                                                   |
| -------- | ---------------------------------------------------------------- |
| FRA      | ஒரு future interest-rate period-க்கு rate lock செய்கிறது         |
| IRS      | பல periods-ல் Fixed மற்றும் Floating rates exchange செய்கிறது    |
| Cap      | Interest rate ஒரு குறிப்பிட்ட level-க்கு மேல் போகாமல் protection |
| Floor    | Interest rate ஒரு குறிப்பிட்ட level-க்கு கீழே போகாமல் protection |
| Collar   | Cap + Floor combination                                          |
| Swaption | எதிர்காலத்தில் IRS enter செய்யும் option                         |

Simple-ஆக நினைத்துக்கொள்ள:

```text
FRA
→ One future rate period

IRS
→ Many future rate periods

CAP
→ Maximum rate protection

FLOOR
→ Minimum rate protection

SWAPTION
→ Option to enter an IRS
```

---

# 28. Capital Markets-ல் Derivatives-ஐ எப்படி நினைத்துக்கொள்ளலாம்?

இப்போது ஒரு பெரிய picture கிடைக்கும்.

Derivatives-ன் முக்கியமான purpose:

**Risk-ஐ ஒரு form-லிருந்து இன்னொரு form-க்கு மாற்றுவது.**

## Forward

```text
Future Price Uncertainty
          ↓
       Forward
          ↓
     Locked Price
```

அதாவது எதிர்கால price என்ன ஆகும் என்ற uncertainty-யை ஒரு fixed price-ஆக lock செய்கிறோம்.

---

## Option

```text
Future Price Uncertainty
          ↓
        Option
          ↓
Protection + Flexibility
```

Option-ல் protection கிடைக்கும்.

ஆனால் market favourable ஆக இருந்தால் அந்த benefit-ஐ பயன்படுத்தும் flexibility இருக்கும்.

---

## IRS

```text
Floating Interest Rate Risk
          ↓
          IRS
          ↓
Fixed Interest Exposure
```

அதாவது floating interest-rate uncertainty-யை fixed-rate exposure-ஆக மாற்றுகிறோம்.

---

## FX Forward

```text
Future FX Uncertainty
          ↓
      FX Forward
          ↓
     Locked FX Rate
```

எதிர்காலத்தில் USD/INR அல்லது USD/CAD exchange rate என்ன ஆகும் என்ற uncertainty-யை lock செய்கிறோம்.

---

## CDS

```text
Credit / Default Risk
          ↓
          CDS
          ↓
   Credit Protection
```

ஒரு borrower default செய்யும் risk-க்கு எதிராக protection பெற CDS பயன்படுத்தப்படுகிறது.

---

# 29. ஒரு Simple Mental Model

Derivatives-ஐ புரிந்துகொள்ள இந்த ஒரு idea மிகவும் useful:

> **"நமக்கு பிடிக்காத risk-ஐ வேறு ஒரு financial contract மூலம் மாற்றிக்கொள்வது."**

உதாரணமாக:

```text
Floating Interest Risk
        ↓
       IRS
        ↓
Fixed Interest Exposure
```

```text
Future FX Risk
        ↓
    FX Forward
        ↓
    Fixed FX Rate
```

```text
Future Price Risk
        ↓
      Option
        ↓
Protection + Flexibility
```

அதனால் Capital Markets-ல் நீங்கள் ஒரு derivative-ஐ பார்க்கும்போது முதலில் இந்த கேள்வியைக் கேளுங்கள்:

**"இந்த derivative எந்த risk-ஐ மாற்றுவதற்காக பயன்படுத்தப்படுகிறது?"**

IRS-க்கு பதில்:

### **Floating Interest Rate Risk-ஐ manage செய்ய.**
