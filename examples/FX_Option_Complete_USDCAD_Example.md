# FX Option — Complete USD/CAD Example

*Note: recognizable real institutions (JPMorgan Chase, Goldman Sachs, Morgan Stanley, RBC, TD, BMO, CIBC, Scotiabank, HSBC, Citigroup, etc.) are referenced for context rather than invented investment-bank names. For Canadian rates, CORRA is used where appropriate — CORRA is administered and published by the Bank of Canada and is Canada's risk-free overnight rate.*

Let's build a realistic capital-markets transaction around **Canada Bank of Toronto (CBT)**.

## 1. Business Scenario

Suppose:

- ABC Mining Corporation, a Canadian company, expects to receive **USD 10 million** on **15-Dec-2026**.
- ABC's functional currency is CAD.
- ABC is worried that USD/CAD may fall.

For example:

| | USD/CAD |
|---|---|
| Today | 1.3800 |
| December (possible) | 1.3200 |

If USD falls against CAD, ABC receives fewer Canadian dollars.

So ABC wants downside protection, but it doesn't want to lock itself into a forward rate.

Therefore: **ABC buys a USD Put / CAD Call option.**

Economically, this gives ABC the right to sell USD and receive CAD at a predetermined exchange rate.

## 2. The Participants

Our transaction:

```
                FX OPTION
                    │
       ┌────────────┴────────────┐
       │                         │
      ABC                       CBT
 Canadian Corp.          Canada Bank of Toronto
       │                         │
       │  Buys USD Put           │
       │  / CAD Call             │
       └─────────────────────────┘
```

### Roles

| Participant | Role |
|---|---|
| ABC Mining Corporation | Option buyer |
| Canada Bank of Toronto | Option seller / dealer |
| CBT FX Trading Desk | Prices and hedges the option |
| CBT Risk Management | Monitors market and counterparty risk |
| CBT Operations | Confirmation/settlement |
| CBT Regulatory Reporting | Regulatory reporting |
| External market | Provides FX prices and liquidity |

For comparison, in the real market a dealer could be a bank such as RBC, TD, BMO, CIBC, Scotiabank, JPMorgan or Goldman Sachs.

## 3. The Trade

Let's create the actual trade.

### Trade Economics

| Field | Value |
|---|---|
| Trade ID | FXO-20260905-00001 |
| Product | FX Option |
| Currency Pair | USD/CAD |
| Buyer | ABC Mining Corporation |
| Seller | Canada Bank of Toronto |
| Option Type | USD Put / CAD Call |
| USD Notional | USD 10,000,000 |
| Strike | 1.3700 CAD/USD |
| Trade Date | 05-Sep-2026 |
| Expiry Date | 15-Dec-2026 |
| Exercise | European |
| Premium | CAD 350,000 |
| Premium Payment | 07-Sep-2026 |
| Settlement | Cash |
| Buyer | ABC |
| Seller | CBT |

The key economic statement is:

> **ABC has the right to sell USD 10 million and receive CAD at 1.3700 CAD per USD on expiry.**

## 4. What Does 1.3700 Mean?

This is extremely important.

```
USD/CAD = 1.3700
```

means:

```
1 USD = 1.3700 CAD
```

Therefore:

```
USD 10,000,000 × 1.3700 = CAD 13,700,000
```

So ABC has effectively protected a minimum gross CAD value of **CAD 13.7 million**, before considering the premium.

## 5. Why Is It a USD Put?

ABC owns USD and wants to sell USD.

Therefore, ABC's right:

```
SELL USD
   ↓
RECEIVE CAD
```

So from ABC's perspective: **USD Put / CAD Call**

From CBT's perspective, the opposite position is: **USD Call / CAD Put**

This is a very useful mental model.

## 6. Premium

Unlike an FX Forward, ABC must pay CBT an option premium.

Suppose:

> Premium = CAD 350,000

ABC pays on 07-Sep-2026:

```
ABC ───────────────► CBT

       CAD 350,000
```

The premium is paid even if ABC eventually decides not to exercise the option. This is the price ABC pays for flexibility.

## 7. What Happens at Expiry?

This is where options become interesting. Let's examine three scenarios.

### Scenario A — USD/CAD falls to 1.3000

ABC is protected.

Market: `1 USD = 1.3000 CAD`

Without option:

```
USD 10M × 1.3000 = CAD 13.0M
```

But ABC has the right to sell at 1.3700:

```
USD 10M × 1.3700 = CAD 13.7M
```

ABC exercises.

| | Amount |
|---|---|
| Protected value | CAD 13.700M |
| Market value | CAD 13.000M |
| **Option benefit** | **CAD 0.700M** |

But ABC paid Premium = CAD 0.350M.

Therefore approximate net economic benefit:

```
CAD 0.700M - CAD 0.350M = CAD 0.350M
```

### Scenario B — USD/CAD = 1.3700

The market equals the strike.

ABC can exercise at 1.3700 or transact in the market at 1.3700 — there is essentially no intrinsic value.

ABC would generally not exercise economically.

But Premium = CAD 350,000 has already been paid. Therefore the premium represents the cost of obtaining the protection.

### Scenario C — USD/CAD rises to 1.4500

This is where the option is superior to a forward.

Market: `1 USD = 1.4500 CAD`

ABC can sell USD in the market:

```
USD 10M × 1.4500 = CAD 14.5M
```

Why exercise at 1.3700? It wouldn't.

So: **ABC does NOT exercise.**

```
ABC sells USD in market
        ↓
receives CAD 14.5M
```

The only economic cost is the premium:

```
CAD 14.5M - CAD 0.35M = CAD 14.15M
```

This is the central advantage of an option.

## 8. Compare FX Forward vs FX Option

| | FX Forward | FX Option |
|---|---|---|
| Obligation | Yes | No |
| Right | No | Yes |
| Premium | Usually no upfront option premium | Yes |
| Protection | Yes | Yes |
| Benefit from favorable FX movement | No | Yes |
| Flexibility | Low | High |
| Complexity | Lower | Higher |

Think of it this way:

```
FORWARD
Lock the door
─────────────
You must use the agreed rate.

OPTION
Buy the key
───────────
You can use the agreed rate
if it is beneficial.
```

## 9. Now Look Inside CBT

This is where the capital-markets architecture becomes interesting.

ABC doesn't just interact with one generic "bank". Inside CBT:

```
                   ABC
                    │
                    ▼
              ┌───────────┐
              │ CBT Sales  │
              └─────┬─────┘
                    │
                    ▼
              ┌───────────┐
              │ FX Trader │
              └─────┬─────┘
                    │
         ┌──────────┼──────────┐
         ▼          ▼          ▼
      Pricing     Risk       Hedge
         │          │          │
         ▼          ▼          ▼
      Volatility  Greeks    Interbank
      FX Curve    Limits     Market
```

The trader doesn't simply say: *"Sure, I'll sell you the option."*

CBT must determine: **What is the option worth?**

## 10. What Determines FX Option Price?

The premium depends on several factors. The major ones are:

```
FX Spot
   +
Strike
   +
Time to Expiry
   +
USD Interest Rate
   +
CAD Interest Rate
   +
FX Volatility
   +
Market conventions
   ↓
Option Price
```

For example:

```
USD/CAD Spot       1.3800
Strike             1.3700
Expiry             15-Dec-2026
USD rate            X%
CAD rate            X%
Volatility           X%
       ↓
Premium
```

## 11. Why Volatility Matters

Suppose two options have exactly the same spot, strike, expiry, and interest rates. But:

- **Option A** — expected FX volatility: 5%
- **Option B** — expected FX volatility: 15%

Option B will generally be more expensive.

Why? Because larger expected movements increase the probability that the option finishes valuable.

```
Higher volatility
       ↓
Greater uncertainty
       ↓
Greater option value
       ↓
Higher premium
```

## 12. CBT's Risk Doesn't Disappear

Suppose CBT sells the option to ABC. CBT is now exposed to FX movements.

- ABC has: **Long USD Put**
- CBT has: **Short USD Put**

CBT therefore may hedge the exposure.

```
ABC
 │
 │ USD Put
 ▼
CBT
 │
 │ Hedge
 ▼
FX Market
```

CBT may trade currencies, options or other instruments with market counterparties to manage its risk.

This creates an important capital-markets concept:

> **Client trade → Dealer risk → Hedge trade**

## 13. Greeks

Now we introduce the next major concept. FX options are commonly managed using Greeks.

**Delta** — Measures sensitivity to the underlying FX rate.

```
USD/CAD changes
       ↓
Option value changes
       ↓
Delta measures sensitivity
```

**Gamma** — Measures how Delta changes when USD/CAD changes.

**Vega** — Measures sensitivity to volatility. This is particularly important for FX options.

```
Volatility ↑
     ↓
Option value changes
     ↓
Vega
```

**Theta** — Measures sensitivity to passage of time.

```
Time passes
    ↓
Option approaches expiry
    ↓
Option value changes
```

**Rho** — Measures sensitivity to interest rates.

## 14. Daily MTM

Suppose the next day USD/CAD moves from 1.3800 to 1.3500.

ABC's USD put becomes more valuable. CBT's short option position becomes less valuable.

```
ABC
Long option
    ↓
MTM increases

CBT
Short option
    ↓
MTM decreases
```

This is the same fundamental MTM concept learned with IRS.

## 15. Complete FX Option Lifecycle

Now connect everything learned so far.

```
                 CLIENT REQUEST
                       │
                       ▼
                QUOTE / RFQ
                       │
                       ▼
                 PRICING
                       │
                       ▼
                TRADE CAPTURE
                       │
                       ▼
                 CONFIRMATION
                       │
                       ▼
                  ENRICHMENT
                       │
            ┌──────────┼──────────┐
            ▼          ▼          ▼
           LEI        UTI        UPI
            │
            ▼
             VALUATION
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
     Spot     Volatility Rates
       │        │        │
       └────────┼────────┘
                ▼
               MTM
                │
                ▼
              GREEKS
                │
                ▼
          RISK / EXPOSURE
                │
                ▼
          COLLATERAL / CSA
                │
                ▼
             EXPIRY
                │
          ┌─────┴─────┐
          ▼           ▼
       EXERCISE    EXPIRE
          │
          ▼
        SETTLE
          │
          ▼
     REGULATORY
      REPORTING
```

## 16. The Most Important Difference from IRS

We previously learned:

```
IRS
Fixed Rate
    ↕
Floating Rate
```

For FX Option:

```
FX Option
Right to transact
       ↓
Strike Rate
       ↓
Market FX Rate
       ↓
Exercise decision
```

IRS has contractual cash flows throughout the life of the swap. An option has something different: **the buyer has a decision.** That decision is the defining feature of an option.

## 17. Where This Fits in the Capital Markets Architecture

For regulatory-reporting purposes, the data lineage looks like this:

```
                FRONT OFFICE
                     │
                     ▼
              FX OPTION TRADE
                     │
                     ▼
              TRADE CAPTURE
                     │
                     ▼
              TRADE STORE
                     │
             ┌───────┴────────┐
             ▼                ▼
        ENRICHMENT         MARKET DATA
             │                │
       LEI / UTI / UPI    FX / Vol / Rates
             │                │
             └───────┬────────┘
                     ▼
                 VALUATION
                     │
                     ▼
                   MTM
                     │
              ┌──────┴──────┐
              ▼             ▼
             RISK        COLLATERAL
              │             │
              └──────┬──────┘
                     ▼
                SETTLEMENT
                     │
                     ▼
             REGULATORY RDP
```

This is very close to the architecture patterns encountered in regulatory-data work.

## 18. One Important Real-World Note

For these educational examples, a distinction is drawn between:

- **Fictional primary actor** — Canada Bank of Toronto (CBT)
- **Real market institutions**, which are named explicitly when useful.

Artificial names such as "Bank B" or "XYZ Investment Bank" are avoided unless deliberately creating a fictional counterparty.

Also, when Canadian market data is used, illustrative trade numbers are distinguished from actual market data. For example, the Bank of Canada publishes actual USD/CAD reference rates and CORRA data; its USD/CAD rate on September 3, 2026 was **1.3789**.

---

## Next Lesson: FRA — Forward Rate Agreement

The natural next step is:

```
FX Option → FRA → Cap → Floor → Collar → Swaption → Cross-Currency Swap → CDS
```

For FRA, CBT will be used as the primary dealer to build a complete CAD FRA transaction with:

```
Trade → Reference Rate → Fixing → Settlement Amount → Valuation → MTM → Collateral → Regulatory Reporting
```

...and then compare FRA vs IRS, because that's where the distinction becomes very clear.
