# MaxiPay PH

**Data Engineering Pilipinas Open Track · 2026 Cohort · John Lopez**

*Maximize what your paycheck actually buys after inflation.*

---

## Problem Statement

I want to answer: **"For Filipino tech and remote workers, is salary growth actually beating inflation — and does earning in USD preserve more purchasing power than local PHP jobs?"**

A raise from ₱70,000 to ₱90,000 sounds like progress. But if Philippine inflation ran 30% over the same period, real purchasing power may have fallen. Remote workers earning USD also face peso–dollar exchange rate changes. I will build a data pipeline to measure that gap using public government and survey data.

**Decision this project supports:** Should a Filipino tech worker stay in a local PHP role, pursue remote USD work, or renegotiate pay based on *real* — not nominal — salary growth?

---

## Audience

This project is for **Filipino software developers, IT professionals, and remote workers** who want to know if their paycheck is genuinely growing or quietly losing value to inflation.

---

## KPI or Key Metric

The main metric I want to track is the **Real Tech Purchasing Power Index (RTPI)** — nominal salary growth minus PH inflation, expressed in PHP purchasing power.

For remote USD earners, salary returns are adjusted for USD/PHP exchange rate changes before subtracting PH inflation.

- RTPI **above 0** → real pay grew faster than inflation  
- RTPI **below 0** → inflation eroded the raise  

---

## Likely Data Source

I will explore:

1. **PSA OpenSTAT — Consumer Price Index** ([openstat.psa.gov.ph](https://openstat.psa.gov.ph/)) — Philippine inflation (monthly CPI)
2. **PSA OpenSTAT — Occupational Wages Survey** ([Labor and Employment](https://openstat.psa.gov.ph/Database/Labor-and-Employment)) — official wage benchmarks by occupation
3. **Stack Overflow Developer Survey** ([survey.stackoverflow.co](https://survey.stackoverflow.co/)) — self-reported PH developer salaries and remote work (annual CSV)
4. **Yahoo Finance `USDPHP=X`** — USD/PHP exchange rate for remote USD earners (via Python `yfinance`)

**Fallback:** DOLE/NWPC regional minimum wage tables if occupational wage data lacks IT-specific coverage.

---

## Possible Final Dashboard

The dashboard should help Filipino tech workers quickly see:

1. A **line chart** of nominal salary vs real purchasing power (2020–present), indexed to 100 at the start date
2. A **side-by-side comparison** of local PHP vs remote USD earners, both in real PHP terms after inflation
3. A **headline number**: *"A ₱70,000 dev salary in 2020 has the buying power of ₱___ today"*
4. Which path — local PHP or remote USD — preserved more purchasing power over the analysis period

> Educational and exploratory only — not financial or career advice.

---

## Analytics Scope (24-week plan)

| Type | When | Purpose |
|------|------|---------|
| Descriptive | Weeks 13–16 | RTPI trends, remote vs local comparison |
| Predictive (Path A) | Weeks 17–20 | Forecast RTPI from CPI and forex features |
| Prescriptive | Week 24 | Dashboard supports the "is my raise real?" decision |

---

## Repo

| Milestone | Target | Status |
|-----------|--------|--------|
| M0 — Problem Statement | Week 1 | Complete |
| M1 — Data Source Identified | Week 3–4 | Pending |

Public repo for DEP milestone review. Pipeline code (`ingest.py`, `transform.py`) begins in Phase 2.

Submit milestones: [DEP Open Track — New Issue](https://github.com/dataengineeringpilipinas/dep-data-engineering-open-track/issues/new/choose)
