# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This is **not a software project**. It is a single-document, self-paced study plan (`capital_markets_self_paced_learning_path.md`) for learning Capital Markets from an IT/reporting perspective. There is no source code, build system, linter, or test suite.

## Document structure

The learning path follows one core mental model, repeated throughout the doc:

```
Asset Class → Product → Instrument/Security → Trade → Valuation → Exposure/Risk → Collateral/Margin → Clearing & Settlement → Regulatory Reporting
```

It is organized into 12 modules (Fundamentals, Asset Class/Product/Instrument/Trade hierarchy, Trade Lifecycle, Fixed Income, Equity, Derivatives, FX/Rates, Valuation, Risk & Exposure, Collateral & Margin, Parties/LEIs, Regulatory Reporting), followed by an end-to-end case study and a self-study pacing guide.

Stated priorities for the current work focus (see the "Priority for My Current Work" table near the end of the doc): Trade Lifecycle, Fixed Income + Derivatives, Valuation, and Collateral + Exposure are weighted highest.

## Content conventions

- Never use "BMO" as the example bank in explanations or examples, even though this repo lives under a `bmo` folder path. Use a fictional example bank called **"Bank Of Toronto"** instead.

## Working in this repo

- When asked to expand, quiz, or add content, edit `capital_markets_self_paced_learning_path.md` directly and preserve its existing structure (module numbering, `###` subheadings of Learn/Example/Goal, fenced `text` diagrams for conceptual flows).
- Keep explanations grounded in the IT/reporting angle the doc emphasizes (what a field means, not just its definition) rather than pure finance theory.
- No commands to run, no dependencies to install.
