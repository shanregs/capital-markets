# Capital Markets — Abbreviations Glossary

[← Back to Index](../../index.md)

> Audience note: written for a software architect (Java background) new to Finance/Capital Markets.
> Companion reference to the module files — not a module itself. Use it as a lookup whenever an unfamiliar acronym shows up in a reporting field.

---

## 1. Instrument & Trade Identifiers

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **ISIN** | International Securities Identification Number | Global, 12-character identifier for a security (2-letter country code + 9-char body + check digit). See [Module 2](./module-02-hierarchy.md) §2. |
| **CUSIP** | Committee on Uniform Securities Identification Procedures | 9-character identifier for US/Canada securities; embedded inside the ISIN for North American securities. |
| **SEDOL** | Stock Exchange Daily Official List | 7-character UK-originated identifier, still used for some London-listed securities. |
| **UPI** | Unique Product Identifier | Global identifier for OTC derivative *products* (not individual trades) — increasingly replaces ad-hoc internal codes in regulatory reporting. See [Module 2](./module-02-hierarchy.md) §11. |
| **UTI** | Unique Trade Identifier | Globally unique identifier for one specific derivative *transaction*, generated at execution — the derivatives world's answer to a "Trade ID." |
| **CFI Code** | Classification of Financial Instruments (ISO 10962) | 6-character code on a security master describing instrument type (e.g. debt/equity, fixed/floating, voting rights). The cash-instrument counterpart to ISDA taxonomy for derivatives. See [Module 2](./module-02-hierarchy.md) §6. |
| **LEI** | Legal Entity Identifier | 20-character global identifier for a *legal entity* (a bank, fund, corporate) — not an instrument. Mandatory on most regulatory trade reports to identify counterparties. Covered in [Module 13](./module-13-parties-leis.md). On a Credit Default Swap specifically, LEI also identifies the *Reference Entity* — a second, distinct role beyond identifying counterparties ([Module 9](./module-09-credit.md) §6). |
| **MIC** | Market Identifier Code (ISO 10383) | 4-character code identifying a specific exchange or trading venue (e.g. `XNYS` for NYSE). |

---

## 2. Market Structure & Venues

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **OTC** | Over-the-Counter | A trade negotiated bilaterally between two parties, with no central exchange. See [Module 1](./module-01-fundamentals.md) §5. |
| **CCP** | Central Counterparty | A clearing house that steps in between two counterparties post-trade, becoming buyer to every seller and seller to every buyer, to reduce counterparty risk. Novation mechanics covered in [Module 3](./module-03-trade-lifecycle.md) §6; risk-reduction effect covered in [Module 11](./module-11-risk-exposure.md). For a Listed Derivative, novation happens instantaneously at execution rather than as a later step ([Module 7](./module-07-commodities.md) §5, [Module 15](./module-15-listed-derivatives.md) §1). |
| **LCH / SwapClear** | London Clearing House / SwapClear | LCH is a CCP group headquartered in London, part of the London Stock Exchange Group (LSEG) — one of the world's largest clearing houses. **SwapClear** is LCH's specific clearing service for interest rate swaps, novating the majority of the world's centrally-cleared IRS volume; "LCH" names the legal entity/group, "SwapClear" the product line within it. Used throughout this roadmap as the concrete, named CCP example. See [Module 3](./module-03-trade-lifecycle.md) §6, [Module 8](./module-08-rates.md) §7-9, [Systems Landscape](./systems-landscape.md) §6, §11. |
| **CSD** | Central Securities Depository | The single central ledger for a market recording ultimate legal ownership of securities (e.g. CDS in Canada, DTC in the US). See [Module 1](./module-01-fundamentals.md) §7. |
| **ICSD** | International Central Securities Depository | A CSD that settles cross-border/international securities (Euroclear, Clearstream). |
| **MTF** | Multilateral Trading Facility | A EU-regulated trading venue that isn't a formal "exchange" but still matches multiple buyers/sellers under common rules. |
| **SEF** | Swap Execution Facility | A US-regulated electronic venue for trading certain standardized OTC swaps, mandated post-2008 crisis to bring transparency to derivatives trading. |
| **SI** | Systematic Internalizer | A firm that deals on its own account when executing client orders outside a regulated exchange/MTF, above certain volume thresholds (an EU/MiFID II concept). |

---

## 3. Products & Derivatives

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **IRS** | Interest Rate Swap | Two parties exchange fixed vs floating interest cash flows on a notional. See [Module 1](./module-01-fundamentals.md) §8, [Module 2](./module-02-hierarchy.md) §8. |
| **FRA** | Forward Rate Agreement | A contract locking in an interest rate for a single future period — essentially a one-period IRS. |
| **CDS** | Credit Default Swap | Protection against a reference entity's default; buyer pays premium, seller pays out on a credit event. See [Module 2](./module-02-hierarchy.md) §9. |
| **TRS** | Total Return Swap | One party pays the total return (price change + income) of an asset in exchange for a funding-rate payment from the other. |
| **NDF** | Non-Deliverable Forward | An FX Forward where only the net cash difference settles (used when a currency isn't freely convertible) rather than both currencies physically exchanging. See [Module 2](./module-02-hierarchy.md) §10. |
| **ETD** | Exchange-Traded Derivative | A derivative (future, listed option) traded on a regulated exchange, contrasted with OTC. |
| **ETF** | Exchange-Traded Fund | A fund whose units trade on an exchange like a share, typically tracking an index. |
| **MBS** | Mortgage-Backed Security | A security backed by a pool of mortgage loans; principal amortizes over time. See [Module 2](./module-02-hierarchy.md) §6. |
| **ABS** | Asset-Backed Security | A security backed by a pool of non-mortgage assets (auto loans, credit card receivables, etc.). |
| **CLO** | Collateralized Loan Obligation | A securitization backed by a pool of leveraged corporate loans. |
| **CDO** | Collateralized Debt Obligation | A broader securitization structure backed by a pool of debt instruments, tranched by risk/seniority. |
| **ADR / GDR** | American / Global Depositary Receipt | A certificate representing shares of a foreign company, traded on a domestic exchange (e.g. a European company's shares traded in the US as an ADR). |
| **CTD** | Cheapest to Deliver | On a physically-settled bond future, the specific eligible bond (from a defined delivery basket) that's most economical for the short to source and deliver, chosen by the short using a published conversion factor. See [Module 15](./module-15-listed-derivatives.md) §5. |

---

## 4. Rates & Benchmarks

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **CORRA** | Canadian Overnight Repo Rate Average | Canada's risk-free overnight benchmark rate, calculated daily by the **Bank of Canada** from actual overnight repo transactions collateralized by Government of Canada securities — not from bank estimates the way CDOR/LIBOR were. Used as the floating leg on CAD swaps (see [Module 1](./module-01-fundamentals.md) §8's Underlier explainer and worked example; full CDOR-to-CORRA transition story in [Module 8](./module-08-rates.md) §1). |
| **CDOR** | Canadian Dollar Offered Rate | The legacy, survey-based CAD interbank benchmark CORRA replaced — banks self-reported what they believed they could borrow at, rather than CORRA's actual-transaction basis. **Discontinued after June 2024.** See [Module 8](./module-08-rates.md) §1. |
| **SOFR** | Secured Overnight Financing Rate | The US's risk-free overnight benchmark rate, successor to USD LIBOR. |
| **SONIA** | Sterling Overnight Index Average | The UK's risk-free overnight benchmark rate, successor to GBP LIBOR. |
| **LIBOR** | London Interbank Offered Rate | The legacy interbank benchmark rate, phased out by 2023 in favor of SOFR/SONIA/CORRA-style "risk-free rates." Still seen in legacy trade data. |
| **OIS** | Overnight Index Swap | A swap referencing a compounded overnight rate (like CORRA/SOFR) as its floating leg — now the standard IRS structure. |
| **bps** | Basis Points | 1 bps = 0.01% (one hundredth of a percent). Used to quote spreads, rates, and small price moves precisely (e.g. CDS spread "120 bps" in [Module 2](./module-02-hierarchy.md) §9). |

---

## 5. Pricing & Valuation

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **YTM** | Yield to Maturity | The total annualized return on a bond if held to maturity, factoring in price paid, coupons, and the pull-to-par gain/loss. See [Module 1](./module-01-fundamentals.md) §1. |
| **MTM** | Mark-to-Market | Revaluing a position at current market prices/rates rather than its original trade price — what Middle Office computes daily. See [Module 1](./module-01-fundamentals.md) §4. |
| **NPV** | Net Present Value | The value today of a stream of future cash flows, discounted back at an appropriate rate. Full treatment in [Module 10](./module-10-valuation.md) (Valuation); [Module 8](./module-08-rates.md) (Rates) already uses NPV-style discounting for the FRA worked example's upfront settlement ([Module 8](./module-08-rates.md) §4b). |
| **PV01 / DV01** | Price/Dollar Value of 1 Basis Point | How much a position's value changes for a 1 bps move in interest rates — a core interest rate risk sensitivity measure, first previewed in [Module 5](./module-05-fixed-income.md) §1 (bond Duration) and [Module 8](./module-08-rates.md) §1 (Rates), fully covered in [Module 10](./module-10-valuation.md). |
| **P&L** | Profit and Loss | The gain or loss on a position or trade over a period. |
| **NAV** | Net Asset Value | The per-unit value of a fund's holdings (assets minus liabilities), used to price fund units/ETF shares. |
| **IPV** | Independent Price Verification | The control process (typically Product Control, inside Middle Office) that independently re-derives a position's valuation to check it against the Front Office's own mark — scrutiny intensity scales with Fair Value Level ([Module 10](./module-10-valuation.md) §6-7). |

---

## 6. Settlement & Clearing

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **T, T+1, T+2** | Trade Date, Trade Date + n business days | Standard settlement-timing shorthand. See [Module 1](./module-01-fundamentals.md) §6 for full worked examples with real dates. |
| **DvP** | Delivery versus Payment | A settlement mechanism ensuring securities and cash move simultaneously, so neither party is left exposed holding only one leg. See [Module 1](./module-01-fundamentals.md) §7. |
| **SSI** | Standard Settlement Instructions | Pre-agreed, static instructions (custodian, account number, etc.) telling counterparties where to deliver securities/cash for a given party — reference data, not per-trade data. |
| **FOP** | Free of Payment | A securities transfer with no corresponding cash payment (the opposite of DvP) — e.g. moving collateral, or a gift/internal transfer. |

---

## 7. Risk, Collateral & Margin

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **IM** | Initial Margin | Collateral posted upfront to cover potential future losses on a derivative position before it's even marked-to-market. Covered in [Module 12](./module-12-collateral-margin.md). |
| **VM** | Variation Margin | Collateral exchanged periodically (daily, for exchange-traded and many centrally-cleared products) to cover the *current* mark-to-market gain/loss on a position. Covered in [Module 12](./module-12-collateral-margin.md); concrete daily examples already worked in [Module 4](./module-04-equity.md) §4c, [Module 7](./module-07-commodities.md) §5, [Module 8](./module-08-rates.md) §5, and [Module 15](./module-15-listed-derivatives.md) §4c. |
| **CSA** | Credit Support Annex | The legal document (an annex to the ISDA Master Agreement) that sets out collateral terms between two OTC derivative counterparties. See [Module 2](./module-02-hierarchy.md) §7, [Module 12](./module-12-collateral-margin.md). |
| **VaR** | Value at Risk | A statistical estimate of the maximum expected loss on a portfolio over a given time horizon at a given confidence level. Covered in [Module 11](./module-11-risk-exposure.md). |
| **PFE** | Potential Future Exposure | An estimate of how large counterparty credit exposure on a derivative could grow over its remaining life. Covered in [Module 11](./module-11-risk-exposure.md). |
| **CVA** | Credit Valuation Adjustment | An adjustment to a derivative's value reflecting the risk that the counterparty might default before the contract matures — a direct application of Credit's default-risk mechanics ([Module 9](./module-09-credit.md)) to Risk & Exposure ([Module 11](./module-11-risk-exposure.md)). |
| **CE** | Current Exposure | The loss a firm would suffer TODAY if its counterparty defaulted right now — equal to MAX(MTM, 0), never negative. See [Module 11](./module-11-risk-exposure.md) §1. |
| **SA-CCR** | Standardized Approach for Counterparty Credit Risk | The current Basel methodology banks use to calculate regulatory capital against counterparty credit risk on derivatives, formalizing Current Exposure/PFE/netting into a regulator-mandated formula. See [Module 11](./module-11-risk-exposure.md) §8. |
| **SIMM** | ISDA Standard Initial Margin Model | ISDA's standardized model calculating Initial Margin for non-centrally-cleared derivatives directly from a position's risk sensitivities. See [Module 11](./module-11-risk-exposure.md) §7, [Module 12](./module-12-collateral-margin.md). |
| **UMR** | Uncleared Margin Rules | The joint IOSCO/BCBS framework requiring Initial and Variation Margin on non-centrally-cleared derivatives above certain thresholds. See [Module 11](./module-11-risk-exposure.md) §8, [Module 12](./module-12-collateral-margin.md). |
| **CCAR** | Comprehensive Capital Analysis and Review | The US Federal Reserve's annual stress-testing regime for large banks, extending VaR-style portfolio risk logic to extreme hypothetical macroeconomic scenarios. See [Module 11](./module-11-risk-exposure.md) §8. |
| **Haircut** | — (not an acronym, but standard shorthand) | A percentage reduction applied to a security's Market Value when used as collateral, converting it into Collateral Value. See [Module 12](./module-12-collateral-margin.md) §3. |
| **MTA** | Minimum Transfer Amount | The smallest collateral movement a CSA allows, alongside Threshold, to prevent constant tiny transfers as MTM wobbles day to day. See [Module 12](./module-12-collateral-margin.md) §5. |
| **AANA** | Average Aggregate Notional Amount | The metric that determines which phase of UMR's six-wave rollout a firm falls into — the largest-AANA dealers were brought into scope first. See [Module 12](./module-12-collateral-margin.md) §8. |
| **HQLA** | High-Quality Liquid Assets | The pool of assets a bank must hold enough of, under Basel's LCR, to survive a 30-day stress scenario — posting collateral ties up a bank's own HQLA. See [Module 12](./module-12-collateral-margin.md) §8. |
| **LCR** | Liquidity Coverage Ratio | The Basel III/IV ratio requiring a bank to hold enough HQLA to survive a 30-day liquidity stress scenario — directly connected to collateral management ([Module 12](./module-12-collateral-margin.md) §8). |
| **CSDR** | Central Securities Depositories Regulation | The EU regime setting settlement-discipline rules (mandatory buy-ins, cash penalties for fails) that apply to securities-collateral movements settling through EU CSDs. See [Module 12](./module-12-collateral-margin.md) §8. |

---

## 8. Global Regulatory & Industry Bodies

Bodies and standards that operate above/across individual jurisdictions — every regional regime in Section 9 ultimately traces back to one of these.

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **ISDA** | International Swaps and Derivatives Association | The trade body standardizing OTC derivative documentation (Master Agreement) and product classification (ISDA Taxonomy). See [Module 2](./module-02-hierarchy.md) §7. Its **Determinations Committees** — industry panels, not a government regulator — rule on whether a CDS Credit Event has occurred, the single most consequential decision in a CDS's lifecycle ([Module 9](./module-09-credit.md) §5, §8). |
| **BCBS** | Basel Committee on Banking Supervision | The global standard-setter for bank capital/liquidity rules (the "Basel" accords) — drives IM/VM and capital requirements. |
| **IOSCO** | International Organization of Securities Commissions | The global standard-setter coordinating securities regulators across jurisdictions — the body whose principles underpin most national trade-reporting regimes. |
| **FSB** | Financial Stability Board | The G20-mandated body monitoring global financial system risk; it was the FSB that mandated OTC derivative reforms (clearing, reporting, margining) globally after the 2008 crisis, which each region below then implemented locally. |
| **CPMI** | Committee on Payments and Market Infrastructures | The global standard-setter for payment, clearing, and settlement systems (works jointly with IOSCO on CCP/CSD standards). |
| **GLEIF** | Global Legal Entity Identifier Foundation | The body overseeing the global LEI system — accredits the Local Operating Units (LOUs) that actually issue LEIs to firms in each country. |
| **LOU** | Local Operating Unit | The national entity actually accredited by GLEIF to issue and renew LEIs — an LEI's ACTIVE/LAPSED status ([Module 13](./module-13-parties-leis.md) §7) is maintained at the LOU level. |
| **IASB** | International Accounting Standards Board | The global accounting standard-setter — issues **IFRS 13**, the standard defining Fair Value and its Level 1/2/3 hierarchy ([Module 10](./module-10-valuation.md) §6). The first standard-setter in this glossary that isn't a markets/derivatives regulator at all, but an accounting one. |
| **FASB** | Financial Accounting Standards Board | The US accounting standard-setter — issues **ASC 820**, the US GAAP equivalent of IFRS 13, with the same Level 1/2/3 Fair Value concept. See [Module 10](./module-10-valuation.md) §8. |
| **FRTB** | Fundamental Review of the Trading Book | The BCBS's post-2008 overhaul of how banks calculate regulatory capital against trading-book market risk — directly penalizes banks for holding positions with less-observable, harder-to-verify (Level 2/3) valuations. See [Module 10](./module-10-valuation.md) §8. |

---

## 9. Regional Reporting Regimes & Regulators — US / UK / EU / Canada / Hong Kong

Every OTC derivative trade Bank Of Toronto books has to be reported *somewhere* under local law — but "somewhere" means a different regulator, a different rulebook, and often a different Trade Repository depending on which jurisdiction the trade (or the counterparty) touches. This section is the region-by-region map of who those regulators are.

### 9a. United States

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **CFTC** | Commodity Futures Trading Commission | The primary US regulator for OTC derivatives (swaps) — sets swap data reporting, clearing, and SEF trading rules under Dodd-Frank. |
| **SEC** | Securities and Exchange Commission | The US regulator for securities markets (equities, bonds, exchanges) and for *security-based* swaps (e.g. single-name equity/credit swaps) under Reg SBSR. |
| **Dodd-Frank Act** | Dodd-Frank Wall Street Reform and Consumer Protection Act | The 2010 US law mandating OTC derivative clearing, reporting, and SEF trading, in response to the 2008 financial crisis — the US's version of what EMIR does in the EU. |
| **Reg SBSR** | Regulation Security-Based Swap Reporting | The SEC's reporting rulebook for security-based swaps (as distinct from the CFTC's rules for other swaps) — a US-specific split not seen in most other jurisdictions. |
| **SDR** | Swap Data Repository | The US term for a Trade Repository — where CFTC-regulated swap data must be reported. |
| **FINRA** | Financial Industry Regulatory Authority | The US self-regulatory body overseeing broker-dealers. |
| **TRACE** | Trade Reporting and Compliance Engine | FINRA's post-trade price transparency system for corporate/agency bonds — a cash-bond reporting regime, separate from derivatives reporting. |
| **CAT** | Consolidated Audit Trail | The SEC-mandated system capturing order and trade lifecycle data across all US equity and options markets, for market surveillance. |

### 9b. United Kingdom

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **FCA** | Financial Conduct Authority | The UK's primary markets/conduct regulator — supervises trade reporting, market conduct, and most firms' day-to-day compliance. |
| **PRA** | Prudential Regulation Authority | The Bank of England unit responsible for the prudential (capital/solvency) supervision of banks and large investment firms — the UK's Basel-implementer. |
| **BoE** | Bank of England | The UK's central bank; oversees CCPs and systemic financial infrastructure alongside the PRA. |
| **UK EMIR** | UK European Market Infrastructure Regulation | EU EMIR as "onshored" into UK law after Brexit (1 Jan 2021) — same core reporting/clearing obligations, now supervised by the FCA/BoE instead of ESMA. |
| **UK MiFIR** | UK Markets in Financial Instruments Regulation | The onshored UK version of EU MiFIR, governing UK transaction reporting and market transparency post-Brexit. |

### 9c. European Union

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **EMIR** | European Market Infrastructure Regulation | The EU regulation requiring OTC derivative trades to be reported to Trade Repositories and, for standardized products, centrally cleared. |
| **EMIR REFIT** | EMIR Regulatory Fitness and Performance Programme | The 2024 overhaul standardizing EMIR's reporting fields onto the ISO 20022 XML message format — a major IT migration event for derivatives reporting pipelines. See [Module 14](../2-reporting/module-14-regulatory-reporting.md) §8. |
| **ISO 20022** | (ISO messaging standard, not an initialism) | The structured XML financial-messaging standard EMIR REFIT (and increasingly other regimes) reports are formatted in, replacing older, less structured formats. See [Module 14](../2-reporting/module-14-regulatory-reporting.md) §8. |
| **MiFID II / MiFIR** | Markets in Financial Instruments Directive / Regulation | The EU's core rulebook for market structure, transparency, and transaction reporting across most financial instruments. |
| **RTS 22** | Regulatory Technical Standard 22 | The specific MiFIR technical standard governing Transaction Reporting field content (~65 fields) — distinct from, and narrower in destination than, EMIR's own field set. See [Module 14](../2-reporting/module-14-regulatory-reporting.md) §4. |
| **ESMA** | European Securities and Markets Authority | The EU-level body that writes the technical standards under EMIR/MiFIR and coordinates the national regulators (NCAs) below. |
| **NCA** | National Competent Authority | The generic term for each EU member state's own regulator (e.g. Germany's BaFin, France's AMF) — day-to-day supervision happens at NCA level, with ESMA setting the common rulebook. |
| **SFTR** | Securities Financing Transactions Regulation | The EU regime requiring reporting of repo, securities lending, and other securities financing transactions — a sibling regime to EMIR, covering a different product set. |
| **TR** | Trade Repository | The generic EU/global term for the central registry OTC derivative trades must be reported to ([Module 1](./module-01-fundamentals.md) §5) — the EU equivalent of a US "SDR." |

### 9d. Canada

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **CSA** *(regulatory)* | Canadian Securities Administrators | The umbrella council of Canada's provincial/territorial securities regulators (OSC, AMF, BCSC, etc.) that jointly writes harmonized rules, including OTC derivatives trade-reporting rules. **Not** the same CSA as Credit Support Annex (Section 7) — the abbreviation collides, so always read it from context. |
| **OSC** | Ontario Securities Commission | Canada's largest provincial securities regulator (Canada has no single national SEC-equivalent — regulation is provincial, coordinated via the CSA above). |
| **CIRO** | Canadian Investment Regulatory Organization | Canada's self-regulatory body for investment dealers and trading activity, formed in 2023 from the merger of IIROC and the MFDA. |
| **TR Rules** | Trade Reporting Rules (e.g. Multilateral Instrument 96-101) | The CSA's harmonized OTC derivatives trade-reporting rules, requiring reporting to a designated Trade Repository — Canada's equivalent of EMIR/Dodd-Frank reporting. |
| **OSFI** | Office of the Superintendent of Financial Institutions | Canada's federal prudential regulator for banks and insurers — the Basel-implementer for Canadian banks like Bank Of Toronto. |
| **BoC** | Bank of Canada | Canada's central bank; publishes CORRA (Section 4) and oversees systemically important clearing/settlement systems. |

### 9e. Hong Kong

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **SFC** | Securities and Futures Commission | Hong Kong's primary securities and derivatives markets regulator. |
| **HKMA** | Hong Kong Monetary Authority | Hong Kong's central banking institution — jointly administers the OTC derivatives reporting regime with the SFC (for banks and licensed corporations respectively) and oversees systemic infrastructure. |
| **SFO** | Securities and Futures Ordinance | The primary Hong Kong statute under which the OTC derivatives reporting, clearing, and licensing regime is established. |
| **HKTR** | Hong Kong Trade Repository | The trade repository operated by the HKMA where OTC derivative transactions booked in/through Hong Kong must be reported. |

**Takeaway:** the pattern repeats everywhere even though the acronyms don't — every region has (1) a markets/conduct regulator, (2) a prudential/central-bank-linked supervisor, (3) a specific law or rulebook mandating OTC derivative reporting, and (4) a Trade Repository the data actually lands in. Once you can name those four things for one jurisdiction, spotting the equivalent in another is mostly a vocabulary exercise — the underlying reporting obligation ([Module 14](../2-reporting/module-14-regulatory-reporting.md)) is structurally the same everywhere.

---

## 10. Organizational Shorthand

| Abbreviation | Full Form | Meaning |
|---|---|---|
| **FO / MO / BO** | Front Office / Middle Office / Back Office | The three-stage internal pipeline a trade passes through — execution, risk/valuation/confirmation, then settlement/reporting. See [Module 1](./module-01-fundamentals.md) §4. |
| **KYC** | Know Your Customer | The due-diligence process for verifying a counterparty's identity before trading with them — feeds into LEI/reference-data setup. |
| **AML** | Anti-Money Laundering | Regulatory controls preventing the financial system from being used to launder illicit funds. |

---

**How to use this glossary:** if a reporting field or conversation uses an acronym you don't recognize, check here first — most entries link back to the module section where the underlying concept is taught with worked numbers, not just defined.

---

[← Back to Index](../../index.md)
