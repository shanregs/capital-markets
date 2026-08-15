# Module 1 — Capital Markets Fundamentals

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).

---

## 1. What is Capital Markets? — Primary vs Secondary Market

**Definition:** Capital Markets is where entities raise money by issuing securities (bonds, stocks), and where investors then trade those securities among themselves.

### Actors involved

| Actor | Role |
|---|---|
| **Government of Canada** | Issuer — creates the bond, raises cash |
| **Initial Investors** (banks/funds who buy at issuance) | First owners of the new bond |
| **Goldman Sachs** | Investor reselling the bond later (secondary market seller) |
| **Bank Of Toronto** | Investor/dealer buying the bond in the secondary market |

### Example with numbers — Primary Market (new issuance)

```text
PRIMARY MARKET  —  New Issuance
================================

   Government of Canada                         Investors
   ----------------------                       (Banks / Funds)
          |                                            |
          |  issues "Canada 5% Bond 2035"              |
          |  Total Face Value: CAD 500,000,000         |
          |-------------------------------------------->
          |     (brand-new bonds, ISIN: CA1358XXXXX)    |
          |                                            |
          <--------------------------------------------|
          |        CAD 500,000,000 cash                 |
          |                                            |

RESULT:
  - Government raises CAD 500,000,000 in cash.
  - The bond "Canada 5% Bond 2035" now exists for the first time.
```

### Coupon Rate vs. Yield — what does "5%" actually mean?

The 5% in "Canada 5% Bond 2035" is called the **coupon rate**, not APR (APR is loan/credit terminology). The coupon rate is fixed at issuance and never changes — it's a label printed on the bond. What *does* change over time is the **yield**: the effective annualized return an investor actually earns, which depends on the price they paid, not just the coupon.

```text
COUPON RATE  vs.  YIELD  (the "true APR-like" number)
========================================================

  Coupon Rate  = fixed, printed on the bond, never changes
                 "Canada 5% Bond 2035" always pays 5% of face value/year

  Yield        = the REAL annualized return, given what you actually paid
                 - Buy at par (100.00)   -> Yield ~= Coupon Rate (5.00%)
                 - Buy below par (98.50) -> Yield  >  Coupon Rate (you paid
                                             less, still get 5% + your
                                             money back at par -> better return)
                 - Buy above par (101.80)-> Yield  <  Coupon Rate
```

Yield is the number that behaves like "APR" — the honest, price-adjusted effective annual return. Coupon rate is closer to a label on the product. Full yield math (Yield to Maturity) is covered properly in Module 4/8 — for now, just don't conflate the two.

### One Issuance, Many Investors — can multiple investors buy slices of the same bond?

Yes — this is the normal case, not the exception. A single bond issuance is almost never bought by one investor. The total CAD 500,000,000 face value is one big pool that gets **split across many investors**, each taking whatever face-value slice they want (in minimum denominations, e.g. CAD 1,000 or CAD 5,000 lots). Every slice shares the exact same ISIN, coupon rate (5%), and maturity (2035) — they're fungible units of one security, the same way every share of Apple stock is identical regardless of who holds how many.

```text
ONE ISSUANCE, SPLIT ACROSS MANY INVESTORS
============================================

                         Government of Canada
                    issues ISIN CA1358XXXXX
                    Total Face Value: CAD 500,000,000
                    Coupon: 5%   Maturity: 2035
                                |
        (allocated across many investors in the SAME issuance)
                                |
        -----------------------+-----------------------
        |            |               |               |
        v            v               v               v
   Bank Of      Goldman Sachs   Maple Leaf      ... hundreds more
   Toronto                      Pension Fund     investors ...
   CAD 50M      CAD 120M        CAD 30M          CAD 300M (combined)
   face value   face value      face value       face value

  50,000,000 + 120,000,000 + 30,000,000 + 300,000,000 = CAD 500,000,000
  (adds up to the full issue size — same ISIN, same 5% coupon, same 2035
   maturity for every single investor, regardless of how much they hold)
```

This is exactly why the secondary market example below works the way it does: Goldman Sachs, having received a CAD 120,000,000 slice at issuance, can later sell just CAD 10,000,000 of that slice to Bank Of Toronto — because every unit of face value of that ISIN is identical and interchangeable. Nobody's slice is "different" from anyone else's; only the *quantity* held differs.

**How the allocation actually happens (briefly):** government bonds are typically issued via an **auction** — investors (mainly primary dealers/banks) submit bids for how much face value they want and at what price/yield, and the government allocates the CAD 500,000,000 across accepted bids. Corporate bonds instead usually go through **syndication/book-building** — underwriting banks canvas demand from many buy-side investors and allocate the issue based on orders received. Either way, the outcome is the same: one ISIN, one coupon, many holders.

### Example with numbers — Secondary Market (trading an existing bond)

```text
SECONDARY MARKET  —  Trading an Existing Bond
==============================================

   Goldman Sachs (Seller)                    Bank Of Toronto (Buyer)
   -----------------------                   ------------------------
          |                                            |
          |  Sells CAD 10,000,000 face value of         |
          |  "Canada 5% Bond 2035" @ Price 98.50         |
          |-------------------------------------------->|
          |        Bonds (same ISIN, existing since       |
          |         primary issuance — nothing new)        |
          |                                            |
          <--------------------------------------------|
          |   Cash = CAD 9,850,000                       |
          |   (10,000,000 x 98.50 / 100)                 |
          |                                            |

RESULT:
  - Government of Canada is NOT involved and receives no cash.
  - Only ownership changes hands. This is what ~95% of trade/
    reporting data you'll work with actually represents.
```

### Why 98.50? — bond price mechanics explained

Two things trip people up here: why the price is quoted as "98.50" and divided by 100, and whether a price below 100 means the seller is losing money. Neither is what it looks like at first glance.

**Why divide by 100 — it's a quoting convention, not math you derive.**
Bond prices are quoted as "price per 100 of face value" — a rate, not a dollar amount.

```text
BOND PRICE QUOTING CONVENTION
==============================

  Quoted Price = 98.50  means:
  "For every CAD 100 of face value, this bond costs CAD 98.50 today."

To get the cash value of ANY face amount traded, scale by that same ratio:

  Market Value = Face Value Traded x (Quoted Price / 100)

Example: Face Value Traded = CAD 10,000,000
  Market Value = 10,000,000 x (98.50 / 100)
               = 10,000,000 x 0.9850
               = CAD 9,850,000
```

Why quote it "per 100" instead of a flat dollar figure? Because the same bond gets traded in wildly different face-value chunks — Goldman Sachs might trade CAD 10,000,000 face value of it, another investor might trade CAD 250,000 face value of the exact same bond. One universal "per 100" rate lets everyone use the same quoted number regardless of trade size — exactly like an FX exchange rate (EUR/USD = 1.08) or a unit price multiplied by quantity.

**Why is the price 98.50 and not 100?**
At issuance, a bond is usually priced at (or near) 100 — called "par." You pay CAD 100 and get back CAD 100 at maturity, plus coupons. After issuance, bond prices move **inversely to prevailing market interest rates**:

```text
WHY PRICE DRIFTS AWAY FROM PAR (100)
=====================================

At issuance:
  Canada 5% Bond 2035 issued at Price = 100.00 (par)
  Market interest rates at the time  ~= 5.00%
  -> A 5% coupon is exactly competitive. Fair price = 100.

Later, market interest rates rise to ~5.30%:
  -> Newly issued bonds now offer 5.30%, better than this old bond's fixed 5%.
  -> Nobody will pay 100 for a bond only paying 5% when 5.30% is available.
  -> The bond's PRICE must drop so its effective yield rises to compete.
  -> Market repricing settles around Price = 98.50.

(If rates had instead FALLEN to ~4.70%, this bond's fixed 5% would look
 attractive, and its price would rise ABOVE par, e.g. 101.80.)
```

98.50 isn't an arbitrary discount — it's the market's real-time answer to "given today's interest rates, what does this fixed 5% cash-flow stream have to cost so a buyer earns a fair, competitive return?" (Full mechanics — yield, duration — are covered properly in Module 4/8. For now: **price moves opposite to interest rates**.)

**Is the seller (Goldman Sachs) actually taking a loss?**
You cannot tell from 98.50 alone — it depends on what the seller originally paid, plus coupons collected along the way.

```text
IS GOLDMAN SACHS AT A LOSS?  —  depends on cost basis + coupons earned
========================================================================

Scenario: Goldman Sachs bought this bond 2 years ago AT ISSUANCE (par = 100.00)
          Face value = CAD 10,000,000
          Cost then  = CAD 10,000,000  (10,000,000 x 100.00/100)

Today, Goldman Sachs sells at 98.50:
          Proceeds now = CAD 9,850,000  (10,000,000 x 98.50/100)

Price-only comparison:
          9,850,000 - 10,000,000 = -CAD 150,000   <- looks like a loss

BUT Goldman Sachs also collected 2 years of coupons while holding it:
          Coupon = 5% x CAD 10,000,000 = CAD 500,000 / year
          2 years of coupons = CAD 1,000,000

Net result over the holding period:
          -150,000 (price)  +  1,000,000 (coupons)  =  +CAD 850,000 net gain

  -> Even though the SALE PRICE is below what they originally paid,
     Goldman Sachs still came out ahead once coupon income is included.
```

Two more nuances worth keeping:

1. The 98.50 price says nothing about the buyer's or seller's individual P&L — it's just today's fair market price. Bank Of Toronto (the buyer) isn't "getting a discount" either; it's paying the fair price for a bond that now yields less than it did at par.
2. A seller might sell below their original cost for reasons unrelated to "loss" — needing cash (liquidity), rebalancing a portfolio, or believing rates will rise even further (better to sell now than at an even lower price later). Selling "below par" is routine, not inherently a bad decision.

> **Mental model:** Price (98.50) = today's fair value per 100 face value, driven by interest rates. Gain/loss = today's price vs. *your own* cost basis, plus coupons collected. Two completely different questions.

**Takeaway:** if you see "Trade" data in a reporting system, assume secondary market unless the record explicitly says "New Issue" / "Primary" / "Syndication".

---

## 2. Buy-side vs Sell-side

### Actors involved

| Actor | Side | Role in this example |
|---|---|---|
| **Maple Leaf Pension Fund** | Buy-side | Wants to invest retiree contributions into bonds |
| **Bank Of Toronto** | Sell-side | Quotes a price, takes the other side of the trade |
| **Interbank Market** (other dealers) | Sell-side | Where Bank Of Toronto sources bonds it doesn't already hold |

### Example with numbers

```text
BUY-SIDE  <-->  SELL-SIDE  FLOW
================================

  Maple Leaf Pension Fund        Bank Of Toronto            Interbank Market
      (BUY-SIDE)                (SELL-SIDE / DEALER)         (Other Dealers)
        |                              |                              |
        | 1. "Buy CAD 50,000,000        |                              |
        |    Canada 5% Bond 2035"       |                              |
        |----------------------------->|                              |
        |                              |                              |
        |                              | 2. Sources bonds from         |
        |                              |    inventory / interbank      |
        |                              |----------------------------->|
        |                              |<-----------------------------|
        |                              |   Bought @ 98.40               |
        |                              |                              |
        | 3. Quotes price 98.50         |                              |
        |<-----------------------------|                              |
        |                              |                              |
        | 4. Fund accepts & pays         |                              |
        |    CAD 49,250,000              |                              |
        |    (50,000,000 x 98.50/100)   |                              |
        |----------------------------->|                              |
        |<-----------------------------|                              |
        |     Bonds, CAD 50,000,000       |                              |
        |     face value                 |                              |

Bank Of Toronto's spread (a preview of "valuation" — Module 8):
   Sold @ 98.50, bought @ 98.40  ->  0.10 x 50,000,000 / 100 = CAD 50,000 profit
```

**Takeaway:** buy-side = deploying someone's capital (pension holders, fund investors). Sell-side = providing the market/liquidity that buy-side clients trade against.

---

## 3. Banks, Brokers, Dealers, Funds

The core question to ask of any actor: **"Does this party take the risk onto its own book, or just pass it through?"**

### 3a. Dealer (Principal) — Bank Of Toronto takes the bond onto its own book

```text
DEALER TRADE  —  Bank Of Toronto acts as PRINCIPAL
====================================================

   Client A                    Bank Of Toronto                Client B
   (Seller)                    (DEALER)                       (Buyer)
      |                              |                              |
      | Sells CAD 5,000,000 bond      |                              |
      | @ price 99.00                 |                              |
      |----------------------------->|                              |
      |<-----------------------------|                              |
      |     Cash CAD 4,950,000         |                              |
      |                              |                              |
      |                        [ Bond now sits on Bank Of Toronto's
      |                          OWN balance sheet — Bank Of Toronto
      |                          carries MARKET RISK until resold ]
      |                              |                              |
      |                              |  Later, sells to Client B      |
      |                              |  @ price 99.20                 |
      |                              |----------------------------->|
      |                              |<-----------------------------|
      |                              |     Cash CAD 4,960,000         |

  Bank Of Toronto's profit = 4,960,000 - 4,950,000 = CAD 10,000
  (Bank Of Toronto held price risk on the bond in between the two trades)
```

### 3b. Broker (Agent) — Bank Of Toronto never owns the bond

```text
BROKER TRADE  —  Bank Of Toronto acts as AGENT
=================================================

   Client A                    Bank Of Toronto                Client B
   (Seller)                    (BROKER)                       (Buyer)
      |                              |                              |
      |--[order: sell CAD 5M bond]-->|                              |
      |                              |--[matches order]------------>|
      |                              |                              |
      |<================  Bond, CAD 5,000,000  =================>|
      |<================  Cash, CAD 4,950,000  ==================|
      |      (bond & cash move DIRECTLY between the two clients;    |
      |       Bank Of Toronto's books are never touched)             |
      |                              |                              |
      |------[pays commission CAD 2,000]--------------------------->|
      |                              |<-----------------------------|
      |                              |    Commission CAD 2,000        |

  Bank Of Toronto's profit = CAD 2,000 commission ONLY.
  Bank Of Toronto held ZERO market risk on the bond itself.
```

### Fund

A **fund** (e.g., Maple Leaf Pension Fund from Section 2) pools many individuals' or institutions' money and invests it according to a mandate (e.g., "invest in investment-grade government bonds"). It is almost always a **buy-side** actor sitting on the client side of dealer/broker trades like the ones above.

---

## 4. Front Office / Middle Office / Back Office

### Actors / systems involved

| Office | Actor / System | Produces |
|---|---|---|
| Front Office | Trader, trading system | The raw trade (who, what, price, quantity) |
| Middle Office | Risk officer, valuation engine, confirmation system | Validated trade, MTM value, signed confirmation |
| Back Office | Settlement ops, custodian, regulatory reporting engine | Settled position, ledger entry, regulatory report |

### Example with numbers — one trade end-to-end

```text
TRADE LIFECYCLE ACROSS FRONT / MIDDLE / BACK OFFICE
=====================================================

Day T  (Trade Date)
--------------------
FRONT OFFICE
  Trader at Bank Of Toronto sells CAD 10,000,000 face value of
  "Canada 5% Bond 2035" to Goldman Sachs @ price 98.50
        |
        v
MIDDLE OFFICE   (same day, T)
  - Checks risk limit: max exposure to Goldman Sachs = CAD 20,000,000
    Current exposure after this trade = CAD 9,850,000  -> OK, within limit
  - Computes trade value:
        10,000,000 x 98.50 / 100 = CAD 9,850,000
  - Sends confirmation to Goldman Sachs
  - Goldman Sachs affirms: trade details match  -> CONFIRMED
        |
        v
Day T+2  (Settlement Date — standard for bonds is "T+2")
-----------------------------------------------------------
BACK OFFICE
  - Delivers CAD 10,000,000 face value of bonds via custodian/CSD
  - Receives CAD 9,850,000 cash into Bank Of Toronto's account
  - Books the entry into the general ledger
  - Reconciles Bank Of Toronto's internal record vs custodian's statement
  - Reports the trade to the regulator (e.g., under MiFID II transaction
    reporting rules), if the product/jurisdiction requires it
```

**Takeaway:** every field you see in a "reporting" table (trade ID, MTM value, settlement status, regulatory flags) was produced by exactly one of these three offices, in this order. When debugging a data issue, ask "which office produces this field?" — it tells you which upstream system to check.

---

## 5. Exchange-Traded vs OTC

### Actors involved

| Actor | Role |
|---|---|
| Investors A–G | Multiple independent participants trading on the exchange |
| Stock Exchange (e.g., NYSE) | Central, regulated venue matching all buyers/sellers |
| Bank Of Toronto, Goldman Sachs | The only two parties to a private OTC contract |
| Trade Repository | Where OTC derivative details must be reported (regulatory requirement) |

### Example — Exchange-Traded (centralized, many-to-many)

```text
EXCHANGE-TRADED  —  centralized order book
=============================================

     Investor A   Investor B   Bank Of Toronto   Investor C
          \            |              |               /
           \           |              |              /
            v           v              v             v
        +-------------------------------------------------+
        |         STOCK EXCHANGE  (e.g., NYSE)              |
        |         Central Order Book                        |
        |         Apple Inc. shares — Price: USD 227.15      |
        +-------------------------------------------------+
            ^           ^              ^             ^
           /            |              |              \
          /             |              |               \
     Investor D    Investor E     Investor F        Investor G

  - Price is PUBLIC and visible to all participants.
  - Contract is IDENTICAL for everyone (1 share = 1 share).
  - No need for bilateral reporting — the exchange itself is the record.
```

### Example — OTC (bilateral, private, custom contract)

```text
OTC  —  bilateral, privately negotiated
==========================================

  Bank Of Toronto   <====  Private Interest Rate Swap  ====>   Goldman Sachs

       Notional:            CAD 100,000,000
       Bank Of Toronto pays: Fixed 4.00%
       Goldman Sachs pays:   Floating (CORRA + spread)
       Maturity:             5 years

  - Terms are visible ONLY to these two parties.
  - No central exchange records this trade.
  - Both parties are legally REQUIRED to separately report the trade's
    details to a Trade Repository (e.g., under EMIR / MiFID II) so that
    regulators have visibility that would otherwise not exist.
```

**Takeaway:** this exchange-vs-OTC gap is *the* reason "regulatory reporting" (Module 12) exists as a discipline at all — regulators trust the exchange to be the record for exchange-traded products, but for OTC they need firms to actively report trade data.

---

## 6. Settlement Conventions — T, T+1, T+2 explained with real dates

**Definition:** **T** ("Trade Date") is the calendar day the trade is agreed. **T+n** ("Settlement Date") is n **business days** later — the day securities and cash actually change hands. "Business days" excludes weekends and the relevant market's public holidays, which is why T+2 is *not* just "add 2 to the calendar date."

Current standard conventions (as of 2026):

| Product | Standard settlement |
|---|---|
| US & Canadian equities | T+1 (moved from T+2 in May 2024) |
| Government / corporate bonds | T+1 or T+2, depending on market |
| FX spot | T+2 for most pairs (T+1 for USD/CAD) |
| OTC derivatives (e.g. Interest Rate Swaps) | No notional settles at all — only periodic net cash flows (see Section 8) |

### Example 1 — no weekend in the way

```text
Trade Date (T)  : Monday,   17 August 2026
T+1             : Tuesday,  18 August 2026
T+2             : Wednesday,19 August 2026   <- Settlement Date
```

### Example 2 — a weekend sits in the middle

```text
Trade Date (T)  : Thursday, 20 August 2026
T+1             : Friday,   21 August 2026
                   Saturday, 22 August 2026  <- not a business day, skipped
                   Sunday,   23 August 2026  <- not a business day, skipped
T+2             : Monday,   24 August 2026   <- Settlement Date
```

### Example 3 — a public holiday also sits in the way

```text
Trade Date (T)  : Thursday,  3 September 2026
T+1             : Friday,    4 September 2026
                   Saturday,  5 September 2026  <- weekend, skipped
                   Sunday,    6 September 2026  <- weekend, skipped
                   Monday,    7 September 2026  <- Labour Day (market holiday), skipped
T+2             : Tuesday,   8 September 2026    <- Settlement Date

Notice: T+2 landed 5 CALENDAR days after T, not 2 — because only
business days count, and this stretch had both a weekend AND a holiday.
```

**Takeaway:** if a "Settlement Date" field in a system is more than 2 calendar days after "Trade Date," that's not automatically a data bug — check whether a weekend or market holiday fell in between before assuming something's broken.

---

## 7. Custodian vs CSD — who actually holds and moves the securities

**Definitions:**

- **Custodian** — a bank that holds securities in safekeeping on behalf of a client (an investor or dealer), carries out settlement instructions, collects coupon/dividend payments on the client's behalf, and produces account statements. Example real-world custodians: BNY Mellon, State Street, Northern Trust.
- **CSD (Central Securities Depository)** — the single, central electronic ledger for a given market that records the *ultimate legal ownership* of securities in book-entry form (no paper certificates), and where final transfer of ownership actually happens on settlement date. Each market typically has one main CSD: **CDS** (Canadian Depository for Securities) in Canada, **DTC** in the US, **Euroclear / Clearstream** for international/Eurobonds.

An investor does not hold an account directly at the CSD. The chain is one layer deeper:

```text
CUSTODY CHAIN  —  who's books actually move
================================================

  Bank Of Toronto  ---(client of)--->  Custodian (e.g. BNY Mellon)
                                              |
                                    (custodian holds an account at)
                                              v
                                    CSD (e.g. CDS / DTC)  <- master ledger
                                              ^
                                    (custodian holds an account at)
                                              |
  Goldman Sachs    ---(client of)--->  Custodian (its own, or same one)

On Settlement Date (T+2):
  1. Both custodians instruct the CSD to move the bond, on their
     clients' behalf, from Goldman Sachs' custody account to
     Bank Of Toronto's custody account.
  2. The CSD updates its central book-entry record — this is the
     LEGAL moment ownership changes.
  3. Cash moves separately via the banking payment system, ideally
     at the same time (Delivery vs Payment - DvP) so neither side
     is left holding one leg without the other.
```

**Takeaway:** "Custodian" = your own bank holding your positions and dealing with settlement on your behalf. "CSD" = the one central record-of-truth ledger for a market that every custodian ultimately plugs into. When reconciling positions (Back Office, Section 4), your internal books should tie out to your custodian's statement — which itself ties out to the CSD.

---

## 8. Notional Amount & a first look at Interest Rate Swaps

**Definition:** **Notional amount** is the reference principal used only to *calculate* periodic interest cash flows on a derivative — it is never physically exchanged between the two parties. This is the key difference from a bond trade: a bond's face value is actually delivered (Section 1); a swap's notional is just a number used for arithmetic.

Revisiting the OTC swap from Section 5 — Bank Of Toronto and Goldman Sachs, Notional CAD 100,000,000, 5-year maturity, quarterly payments, Bank Of Toronto pays a **fixed** rate, Goldman Sachs pays a **floating** rate (CORRA + spread):

```text
INTEREST RATE SWAP  —  one quarterly payment date, worked example
======================================================================

Notional (reference only, NEVER exchanged): CAD 100,000,000
Period: ~91 days (one quarter), day count actual/365

Bank Of Toronto's FIXED leg:
  100,000,000 x 4.00% x (91 / 365) = CAD 997,260

Goldman Sachs' FLOATING leg (CORRA averaged 3.20% + 0.05% spread = 3.25%):
  100,000,000 x 3.25% x (91 / 365) = CAD 810,068

NETTING  (only the difference actually moves, one direction):
  997,260 - 810,068 = CAD 187,192

RESULT on this payment date:
  Bank Of Toronto  ---- pays CAD 187,192 net ---->  Goldman Sachs

  - The CAD 100,000,000 notional itself never moves, on this date
    or any other — it only ever exists to compute the two legs above.
  - This repeats every quarter for 5 years, with the floating leg
    reset each period to whatever CORRA + spread then is.
```

This is deliberately just the mechanics of one cash flow, not the theory of swaps — pricing/valuing a swap (discount curves, DV01/PV01, mark-to-market) is covered properly in **Module 7: FX & Interest Rate Products**. For now, the two things worth keeping: (1) notional is a calculation input, not a cash amount that settles, and (2) in practice only one net payment moves per period, not two gross ones.

**Takeaway:** if you ever see a derivative trade record showing a huge "Notional" value (e.g. CAD 100,000,000) with no corresponding settlement of that amount anywhere in cash/custody systems, that's expected — notional isn't supposed to settle. Only the periodic net cash flows do.

---

## Actors — Full Cast Reference

| Actor | Buy-side / Sell-side | Typical role seen in this module |
|---|---|---|
| Government of Canada | Issuer (neither, technically) | Issues new bonds in the primary market |
| Maple Leaf Pension Fund | Buy-side | Invests client/member money into bonds |
| Bank Of Toronto | Sell-side | Dealer (principal) or broker (agent) depending on the trade |
| Goldman Sachs | Sell-side (in these examples, acting as a counterparty/dealer) | Counterparty on secondary market and OTC trades |
| Stock Exchange (e.g., NYSE) | Market infrastructure | Central venue for exchange-traded products |
| Trade Repository | Market infrastructure | Central registry OTC trades must be reported to |
| Custodian / CSD | Market infrastructure | Holds/settles securities on behalf of owners (Section 7 above; Module 11 goes deeper) |

---

## Recap Quiz — Numbers Edition

1. Bank Of Toronto buys a bond at 98.40 and later sells the same bond at 99.10. If the face value traded is CAD 20,000,000, what is Bank Of Toronto's profit, and was Bank Of Toronto acting as dealer or broker?
2. A pension fund sells CAD 8,000,000 face value of a bond through Bank Of Toronto acting purely as broker, at a price of 97.75, for a flat commission of CAD 1,500. How much cash does the pension fund receive, and how much does Bank Of Toronto earn?
3. A trade is agreed on Monday (T) and bonds settle T+2. If Monday is the trade date, which calendar day is the settlement date, and which "office" (front/middle/back) is active on each of those days?
4. Why doesn't a trade of Apple shares on NASDAQ need to be separately reported to a Trade Repository, while a CAD 100,000,000 Interest Rate Swap between Bank Of Toronto and Goldman Sachs does?
5. A trade is agreed on Thursday, 3 September 2026 and settles T+2. Monday, 7 September 2026 is a market holiday. What calendar date is the actual Settlement Date, and why isn't it Saturday, 5 September 2026?
6. What's the difference between what a custodian does and what a CSD does — and why does an investor's custodian need its own account at the CSD rather than the investor holding one directly?
7. On a CAD 100,000,000 notional Interest Rate Swap, Bank Of Toronto pays fixed 4.00% and Goldman Sachs pays floating 3.10% (already including spread) for a 91-day quarter (actual/365). Who pays whom, how much net cash actually moves, and does any part of the CAD 100,000,000 notional itself settle?

Answer whenever ready — then we move to **Module 2: Asset Class → Product → Instrument → Trade**.

---

## Answers — Recap Quiz, with Explanations

**1. Profit = CAD 140,000; Bank Of Toronto acted as dealer.**

```text
Profit = (99.10 - 98.40) / 100 x 20,000,000
       = 0.70 / 100 x 20,000,000
       = CAD 140,000
```

Bank Of Toronto bought the bond first and held it before selling it on later — that's taking principal risk, the defining trait of a dealer. A broker never takes the position onto its own book, so "buy low, sell high on the same bond" only makes sense for a dealer.

**2. The pension fund receives CAD 7,818,500; Bank Of Toronto earns CAD 1,500.**

```text
Market value = 8,000,000 x 97.75 / 100 = CAD 7,820,000
Pension fund receives = 7,820,000 - 1,500 (commission) = CAD 7,818,500
Bank Of Toronto earns = CAD 1,500 commission only
```

As broker/agent, Bank Of Toronto never owns the bond, so its only income is the flat commission — it carries zero market risk on the trade itself.

**3. Settlement Date = Wednesday.**

Monday (T) → Tuesday (T+1) → Wednesday (T+2) — no weekend falls in between since all three days are weekdays.

- **Front Office** is active on T (Monday) — executes the trade.
- **Middle Office** is active on T, same day (Monday) — risk checks, valuation, confirmation/affirmation.
- **Back Office** is active on T+2 (Wednesday) — delivers bonds, receives cash, books ledger entries, reconciles, reports.

**4. The exchange itself is the public record; OTC has no such record.**

An Apple/NASDAQ trade doesn't need separate Trade Repository reporting because the exchange is already a public, regulated, central venue — regulators see every trade through the exchange's own data feed in real time. The CAD 100,000,000 IR Swap is OTC: a private, bilateral contract between exactly two parties, with no central venue recording it. Regulators would have zero visibility into it unless both counterparties are legally required to separately report the trade to a Trade Repository (e.g. under EMIR/MiFID II) — which is exactly the requirement that exists.

**5. Settlement Date = Tuesday, 8 September 2026.**

```text
T          : Thursday,  3 September 2026
T+1        : Friday,    4 September 2026
(skipped)  : Saturday,  5 Sept / Sunday, 6 Sept  - weekend
(skipped)  : Monday,    7 Sept - Labour Day, market holiday
T+2        : Tuesday,   8 September 2026
```

It isn't Saturday, 5 September, because Saturday was never a candidate at all — T+2 counts only business days forward from T. This stretch had to skip both the weekend *and* the following Monday's holiday, pushing the true T+2 a full five calendar days past the trade date.

**6. A custodian is client-facing; a CSD is the one central ledger the whole market shares.**

A **custodian** is the bank that holds an investor's or dealer's securities in safekeeping, executes settlement instructions on their behalf, and produces statements — many different custodians exist, each serving their own roster of clients. A **CSD** is the single central ledger for a market that records the ultimate legal ownership of every security issued in that market. An investor's custodian needs its own CSD account because the CSD doesn't deal directly with every individual investor — it only maintains a manageable number of large participant accounts (custodians, clearing members, major banks), each of which aggregates and nets the positions of many underlying clients beneath it. The investor's true legal ownership is only as good as this whole chain reconciling correctly, end to end.

**7. Bank Of Toronto pays Goldman Sachs CAD 224,384 net; no notional settles.**

```text
Fixed leg (Bank Of Toronto pays)  : 100,000,000 x 4.00% x (91/365) = CAD 997,260
Floating leg (Goldman Sachs pays) : 100,000,000 x 3.10% x (91/365) = CAD 772,877
Net = 997,260 - 772,877 = CAD 224,384
```

Since the fixed leg is larger, **Bank Of Toronto pays Goldman Sachs CAD 224,384 net** on this payment date. No part of the CAD 100,000,000 notional itself settles — it only ever exists as the number both legs are calculated from. Only the netted cash difference actually moves.
