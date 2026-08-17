# Module 4 — Equity

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Example bank used throughout: **Bank Of Toronto** (fictional, for illustration only).
> **Asset Class Roadmap 1 of 6** — Equity → Fixed Income → FX → Commodities → Rates → Credit. Each asset class is studied through the same eight-part pattern below before moving to the next.

---

## 1. What Is Equity? — Theory &amp; Terminology

**Definition:** Equity represents **fractional ownership** in a company. A shareholder owns a proportional claim on the company's assets and future earnings, evidenced by shares. Unlike the Fixed Income instruments covered in Module 5, equity has **no maturity date, no fixed coupon, and no promise to return principal** — the return comes entirely from price appreciation and dividends, both of which are variable and, for dividends, discretionary rather than contractual.

| Term | Meaning |
|---|---|
| Share | One unit of ownership in a company |
| Outstanding Shares | Total number of shares currently held by all shareholders |
| Market Capitalization | Share Price × Outstanding Shares — the market's valuation of the whole company |
| Dividend | A discretionary cash (or stock) payment to shareholders, declared by the board — never guaranteed |
| Voting Rights | Common shareholders typically get one vote per share on corporate matters (board elections, major decisions) |
| Corporate Action | An event initiated by the issuer that changes the share (stock split, buyback, spin-off, rights issue) |

### Equity vs Fixed Income — the contrast that matters most

| | Equity | Fixed Income (Module 5) |
|---|---|---|
| Ownership claim | Yes — a residual claim on the company | No — a creditor claim (a loan to the issuer) |
| Maturity | None — held indefinitely | Fixed — bond matures on a set date |
| Return | Price appreciation + dividends (variable, not guaranteed) | Coupon (fixed, contractual) + principal at maturity |
| Priority in bankruptcy | **Last** — paid only after all creditors | Ahead of equity — bondholders are creditors |

**Takeaway:** every other section in this module — products, trade lifecycle, identifiers, regulation — traces back to this one fact: a share is a piece of the company itself, not a promise from it. That's the opposite of a bond, and it's why equity valuation (Module 10) looks completely different from bond valuation.

---

## 2. What Are The Products? — Cash Equity vs Equity Derivatives

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

| Product | What it is |
|---|---|
| **Common Stock** | Ordinary shares — one vote per share (typically), dividends at the board's full discretion, last claim on assets if the company is wound up |
| **Preferred Stock** | A hybrid instrument — carries a *fixed dividend rate* like a bond coupon, but the dividend is still only paid if the board declares it; ranks ahead of common stock but behind all debt in bankruptcy; usually no voting rights |
| **Equity Future** | An exchange-traded contract to buy/sell a stock or index at a future date at an agreed price today; index futures are typically cash-settled, not physically delivered |
| **Equity Option** | A contract giving the buyer the **right, not the obligation**, to buy (call) or sell (put) shares at a fixed strike price, by or at expiry, in exchange for an upfront premium |

**Takeaway:** Common and Preferred Stock sit in the same "Cash Equity" bucket because both are real, deliverable securities — the difference between them is entirely about the *economics* of the claim (fixed-ish dividend, higher priority, no vote for Preferred vs. variable dividend, lowest priority, a vote for Common). Futures and Options sit in a different bucket entirely, because neither one *is* the stock — Section 3 makes this distinction concrete.

---

## 3. What Exactly Is Being Traded? — Security vs Contract

**Definition:** For Common or Preferred Stock, you are trading an **actual security** — a real, transferable ownership claim, recorded in book-entry form at a CSD (Module 1 §7), that exists independently of any particular trade. For Equity Futures or Options, you are trading a **contract** whose value derives from that underlying security's price — the contract itself is created at execution and typically extinguished at expiry or exercise.

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

**Takeaway:** this is exactly the "Quantity vs Notional" distinction from Module 2 §3, restated for equity specifically. A cash equity trade's Quantity (shares) is a real, deliverable amount. An equity derivative's underlying share count is closer to a reference amount used to size the contract's payoff — whether it ever results in real shares moving depends entirely on whether the contract is physically settled and actually exercised (Section 5).

---

## 4. Real-World Trade Examples — Buyer → Seller → Trade

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
EQUITY FUTURE  —  E-mini S&amp;P 500 Future (CME)
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
    -&gt; credited to Maple Leaf Pension Fund's margin account THAT DAY
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
    -&gt; this is the maximum possible loss for a long option buyer,
       ever — never more than the premium paid.
```

**Takeaway:** notice the buyer/seller relationship changes shape across these four examples — a bilateral dealer trade (4a), a primary issuance (4b), an exchange trade against a CCP (4c), and a bilateral option written by a dealer (4d). All four are still "Equity," but the actors and the settlement mechanics already look different before Section 5 even gets to lifecycle.

---

## 5. Trade Lifecycle — Equity-Specific Walkthrough

Applying the pattern from the Asset Class Roadmap — **Order → Execution → Trade Capture → Confirmation → Clearing → Settlement → Position → Valuation/P&amp;L → Reporting** — to the Coca-Cola common stock trade from Section 4a. This is the same nine-stage backbone as Module 3, with two extra stages made explicit (**Position** and **Valuation/P&amp;L**) that sit between Module 3's "Settlement" and "Reconciliation/Reporting" stages.

```text
COCA-COLA COMMON STOCK  —  FULL LIFECYCLE
========================================================================

  09:31:00  ORDER — Maple Leaf Pension Fund instructs Bank Of Toronto:
            "BUY 10,000 shares Coca-Cola Company (NYSE: KO)"

  09:31:04  EXECUTION @ USD 68.40 on NYSE

  09:31:04  TRADE CAPTURE — Trade ID BOT-EQ-2026-011203

  09:45:00  CONFIRMATION/MATCHING via DTCC CTM -&gt; MATCHED

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

  VALUATION / P&amp;L — each day after settlement, the position is
            marked to the current market price:
              Day of purchase:  10,000 x 68.40 = USD 684,000 (cost)
              One week later:   10,000 x 71.10 = USD 711,000 (market value)
              Unrealized P&amp;L =  711,000 - 684,000 = +USD 27,000

  REPORTING — trade reported to the relevant Trade Repository/
            regulator by T+1 (Module 3 §09); the ongoing POSITION
            is separately subject to periodic position reporting
            requirements in some jurisdictions (Module 14)
```

### How Clearing differs for the derivative products

| Product | CCP | What "Clearing" adds |
|---|---|---|
| Common/Preferred Stock | NSCC (US example above) / CDCC / LCH / Eurex, per country (systems-landscape §04–07) | Multilateral netting before one T+1/T+2 DvP settlement |
| Equity Future | CME Clearing | Novation (Module 3 §06) plus **daily** Variation Margin for the life of the contract — Position and Valuation/P&amp;L are not a one-time check, they run every business day |
| Listed Equity Option | **OCC (Options Clearing Corporation)** | Novation of the option contract itself; premium settles T+1; if exercised, a *separate* physical stock settlement follows T+1 after exercise |

> **A naming collision worth flagging:** "OCC" means two completely different things in US capital markets. Here, in an equities/derivatives context, it's the **Options Clearing Corporation** — the CCP for all US-listed equity options. In a banking/prudential context (systems-landscape §10), "OCC" instead means the **Office of the Comptroller of the Currency** — the regulator that charters and supervises national banks. Same acronym, same country, unrelated organizations — always check context.

**Takeaway:** the "Position" and "Valuation/P&amp;L" stages aren't new concepts — they're Module 3 §08 (Reconciliation) and a preview of Module 10 (Valuation), made explicit here because equity is where they're easiest to see: a stock position just sits there being marked to a public, constantly-moving market price, with no coupon or notional math to obscure it.

---

## 6. Identifiers — ISIN, CUSIP, SEDOL, Ticker, LEI, UPI, FIGI

Module 2 §2 introduced ISIN, CUSIP, and Ticker. Equity is where the remaining four — SEDOL, LEI, UPI, FIGI — earn their place.

| Identifier | Identifies | Scope | Example (Coca-Cola / Apple, unless noted) |
|---|---|---|---|
| **ISIN** | The instrument | Global | `US1912161007` (Coca-Cola) |
| **CUSIP** | The instrument | North America | `191216100` (Coca-Cola) |
| **Ticker** | The instrument, on one venue | Exchange-specific | `KO` (NYSE) |
| **SEDOL** | The instrument | Historically UK/Ireland-specific (7 characters); still embedded inside most UK ISINs the same way a CUSIP is embedded in a US/CA ISIN | `0008373` (Tesco PLC on LSE, illustrative) |
| **FIGI** | The instrument — but at *multiple levels* (share class, or a specific trading venue/exchange line) | Global, open-source (Bloomberg-originated, now an industry standard) | `BBG000B9XRY4` (Apple Inc., Composite Global FIGI — commonly published reference data) |
| **LEI** | The **legal entity** itself — not an instrument at all | Global | `5493001KJTIIGC8Y1R12` (illustrative — Bank Of Toronto) |
| **UPI** | An OTC derivative *product template* (Module 2 §12) | Global | Relevant only for an OTC equity derivative (e.g. a Total Return Swap on KO) — a listed Equity Future or Option instead gets an exchange-specific contract code, not a UPI |

```text
INSTRUMENT IDENTIFIERS  vs  ENTITY IDENTIFIER
========================================================================

  ISIN, CUSIP, Ticker, SEDOL, FIGI   -&gt;  answer "WHAT is being traded?"
  LEI                                 -&gt;  answers "WHO is trading it?"
  UPI                                 -&gt;  answers "WHAT PRODUCT TEMPLATE
                                             does this OTC derivative
                                             belong to?" (Module 2 §12)

  On a single trade record for the Coca-Cola trade (Section 4a):
    Instrument  : ISIN US1912161007 / CUSIP 191216100 / Ticker KO
    Buyer LEI   : identifies Maple Leaf Pension Fund
    Seller LEI  : identifies Goldman Sachs
    Dealer LEI  : identifies Bank Of Toronto
  -&gt; four completely different identifiers on one trade record,
     answering two completely different questions.
```

**Takeaway:** FIGI's distinguishing feature versus ISIN is that it can identify a security at more than one level — a "Composite" FIGI for the security globally, and separate FIGIs per exchange listing (useful for a dual-listed stock trading on both NYSE and, say, a European exchange, where ISIN alone doesn't tell you *which* venue's price/liquidity a given trade record refers to).

---

## 7. Exchange vs OTC vs Bilateral vs Centrally Cleared

**Definition:** these are **two separate questions**, easy to conflate. "Exchange vs OTC" asks *where the trade was negotiated* (Module 1 §5). "Bilateral vs Centrally Cleared" asks *what the counterparty risk looks like after execution* (Module 3 §6) — even an OTC-negotiated trade can end up centrally cleared if it's later novated to a CCP.

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

| Equity product | Exchange or OTC? | Bilateral or Centrally Cleared? |
|---|---|---|
| Common/Preferred Stock | Exchange-traded | Centrally cleared (NSCC/CDCC/LCH/Eurex per country) |
| Equity Future | Exchange-traded | Centrally cleared (CME Clearing) |
| Listed Equity Option | Exchange-traded | Centrally cleared (OCC) |
| OTC Equity Option / Total Return Swap | OTC | Bilateral (typically) — or centrally cleared if the specific product has been mandated/elected for clearing |

**Takeaway:** for cash equity and listed derivatives, "exchange-traded" and "centrally cleared" travel together so consistently that people often treat them as synonyms — but Section 8's regulatory reporting obligations (EMIR, SFTR) exist specifically because the bottom-right cell of that table (bilateral OTC) is where a trade can otherwise go completely unseen by a regulator, exactly as Module 1 §5 first established for OTC products generally.

---

## 8. Regulatory Perspective — MiFID, EMIR, SFTR, FCA, CIRO, HKMA, DTCC, DMO

Only now, with the products, trade mechanics, and identifiers already concrete, do the acronyms get attached to something real.

| Regulation / Body | How it touches Equity specifically |
|---|---|
| **MiFID II / MiFIR** (EU) | Pre- and post-trade transparency for equity trading, best-execution obligations, the Systematic Internalizer regime for OTC-like equity trading outside an exchange, and T+1 transaction reporting (Module 3 §09, systems-landscape §07) |
| **EMIR** | Applies only if the equity derivative is OTC (e.g. a Total Return Swap on KO) — reported to a Trade Repository (Module 2 §07, systems-landscape §04–07). Does **not** apply to Common/Preferred Stock or to listed Futures/Options, which aren't OTC |
| **SFTR** | Applies specifically when equity shares are lent or borrowed, or used as repo collateral — e.g. Bank Of Toronto lending out its Coca-Cola position to a short-seller (systems-landscape §12) |
| **FCA** (UK) | Conduct regulator for UK equity trading — administers UK MiFIR equity transaction reporting (systems-landscape §10) |
| **CIRO** (Canada) | The SRO overseeing Canadian equity dealer conduct and trading activity (systems-landscape §10) |
| **HKMA / SFC** (Hong Kong) | Worth a careful distinction: the **HKMA (Hong Kong Monetary Authority)** is Hong Kong's central-bank-equivalent — banking supervision and monetary policy, analogous to the Bank of England. The **SFC (Securities and Futures Commission)** is Hong Kong's actual securities/equities market-conduct regulator — the closer analog to the FCA or the SEC for equity trading specifically |
| **DTCC** (US) | Its subsidiaries NSCC and DTC clear and settle US cash equities (Section 5 above; systems-landscape §05) |
| **DMO** (UK Debt Management Office) | Issues UK government bonds (Gilts) — **not** an equity-relevant body; included here because you listed it, but its full treatment belongs in Module 5 (Fixed Income) |

**Takeaway:** every regulation above attaches to a *specific mechanic* already covered in Sections 1–7 — MiFID to where/how the trade executed, EMIR to whether the derivative is OTC, SFTR to whether shares themselves (not just a derivative on them) were lent or used as collateral. None of these rules apply uniformly to "Equity" as a whole; each one is scoped to a particular slice of what this module just walked through.

---

## Actors — Full Cast Reference

| Actor | Role in this module |
|---|---|
| Maple Leaf Pension Fund | Buy-side client — buyer of Coca-Cola stock, the E-mini future, and the AAPL call option |
| Goldman Sachs | Seller of the Coca-Cola stock; buyer of Bank Of Toronto's preferred share issuance |
| Bank Of Toronto | Dealer on the Coca-Cola trade; issuer of its own Preferred Shares; option writer on the AAPL call |
| CME Clearing | CCP for the E-mini S&amp;P 500 future |
| OCC (Options Clearing Corporation) | CCP for the listed AAPL option |
| NSCC / DTC | Clearing (netting) and settlement (DvP) for the US cash equity trade |

---

## Recap Quiz — Numbers Edition

1. Bank Of Toronto's Preferred Shares Series A pay a 4.50% non-cumulative dividend on CAD 25.00 par. If the board skips two consecutive quarters and then resumes in the third, how much of the skipped dividends does Goldman Sachs eventually receive — and how would the answer differ if these were *cumulative* preferred shares instead?
2. On the E-mini S&amp;P 500 future (Section 4c), if the index instead fell to 5,900.00 the next business day, what is the Variation Margin amount and which account does it move from/to?
3. On the AAPL 15-Jan-2027 250 Call (Section 4d), what is Maple Leaf Pension Fund's maximum possible loss, regardless of where AAPL's price ends up — and why is that figure fixed at trade date, unlike the maximum possible *gain*?
4. Why does a listed equity option's Clearing stage route to the OCC while a US cash equity trade's Clearing stage routes to the NSCC — are these the same organization wearing two hats, or two entirely separate CCPs?
5. Using the Section 6 table, which identifier on the Coca-Cola trade record identifies Bank Of Toronto itself, and which three identify the instrument being traded?
6. A Total Return Swap on Coca-Cola shares, negotiated bilaterally between two dealers, is later voluntarily submitted for central clearing. Does it change from "OTC" to "Exchange-Traded" as a result? Explain using Section 7's two-axis model.
7. If Bank Of Toronto lends its Coca-Cola shares to another firm that wants to short-sell them, which regulation from Section 8 governs the reporting of that specific transaction — MiFID, EMIR, or SFTR — and why do the other two not apply to it?

Answer whenever ready — then move to **Module 5: Fixed Income**, the next stop on the Asset Class Roadmap.

---

## Answers — Recap Quiz, with Explanations

**1. Goldman Sachs receives CAD 0 for the two skipped quarters — non-cumulative means they are permanently forfeited, never paid later. If cumulative, Goldman Sachs would receive both skipped quarters' dividends (2 x CAD 562,500 = CAD 1,125,000) on top of the third quarter's regular CAD 562,500, for a total CAD 1,687,500 catch-up payment.**

Section 4b defines this directly: "non-cumulative" means a skipped dividend is simply gone, unlike a cumulative preferred share (or a bond coupon, which would constitute default if missed) where unpaid amounts accrue and must eventually be paid before any dividend can resume to common shareholders.

**2. Variation Margin = -USD 20,000, moving FROM Maple Leaf Pension Fund's margin account TO the counterparty side (via CME Clearing).**

```text
Variation Margin = (5,900.00 - 5,920.00) x 50 x 20 = -USD 20,000
```

Since Maple Leaf Pension Fund is long (bought) the future and the index fell, the position lost value — the daily mark-to-market mechanic from Section 4c runs the same way regardless of direction, it just moves money the other way when price moves against the position holder.

**3. Maximum possible loss = USD 185,000, the full premium paid — fixed at trade date because it's simply the price paid upfront; the maximum possible *gain* is theoretically unlimited (or very large) because it depends on how high AAPL's price can rise before expiry, which isn't knowable at trade date.**

Section 4d's Scenario B shows this exact outcome — the option expiring worthless caps the loss at premium paid, no matter how far below the strike AAPL falls. This asymmetry (fixed, known maximum loss vs. open-ended potential gain) is the defining economic feature of being long an option, contrasted with Section 4c's future, where both gains and losses are theoretically unbounded in either direction.

**4. Two entirely separate CCPs — not the same organization.**

Section 5's table and the disambiguation callout make this explicit: NSCC (a DTCC subsidiary) clears cash equity trades; OCC (Options Clearing Corporation) is a completely different, independent CCP dedicated specifically to US-listed options. They happen to both ultimately settle the underlying shares through DTC when an option is exercised, but the *clearing* function itself sits in two different organizations.

**5. Bank Of Toronto is identified by its LEI (as the dealer). The instrument is identified by ISIN (US1912161007), CUSIP (191216100), and Ticker (KO).**

Section 6's diagram states this directly — LEI answers "who," while ISIN/CUSIP/Ticker (and, where relevant, SEDOL/FIGI) all answer "what." Maple Leaf Pension Fund and Goldman Sachs would each carry their own separate LEIs as buyer/seller on the same trade record.

**6. No — it stays OTC. Central clearing changes only the Bilateral-vs-Centrally-Cleared axis, not the Exchange-vs-OTC axis.**

Section 7's two-axis model draws this distinction precisely: "where the trade was negotiated" (OTC, since it was a bilateral dealer negotiation) is permanently fixed by how the trade originated, regardless of what happens to it afterward. Submitting it for clearing only moves it from the "OTC + Bilateral" cell to the "OTC + Centrally Cleared" cell in Section 7's table — it never becomes "Exchange-Traded," because it was never executed on an exchange.

**7. SFTR governs it — because lending the actual shares (not a derivative referencing them) is a Securities Financing Transaction, exactly the product family SFTR exists for (systems-landscape §12). MiFID doesn't apply because this isn't a MiFID-scope "executed transaction" on the shares — no sale/purchase happened. EMIR doesn't apply because a securities lending transaction is not an OTC *derivative* — there's no notional-referencing contract here, just the shares themselves changing hands temporarily.**

Section 8's table flags SFTR as covering exactly this scenario. The key distinguishing test, consistent with Module 2's asset-class hierarchy: is this a trade in the security itself (potentially SFTR, if it's a financing-type transaction like lending/repo, or ordinary MiFID transaction reporting if it's an outright buy/sell) or a contract referencing the security (potentially EMIR, if OTC)? Securities lending is squarely the former, and specifically the financing sub-category SFTR was built for.
