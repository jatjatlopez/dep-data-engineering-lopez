# MaxiPay PH

**DEP Open Track · 2026 Cohort**  
John Lopez · [curriculum repo](https://github.com/dataengineeringpilipinas/dep-data-engineering-open-track)

---

## Problem Statement

I want to answer:

> **"For tech workers in the Philippines, how wide is the gap between nominal salary growth and real purchasing power?"**

I kept seeing posts about salary increases in the PH tech space — someone moves from ₱45k to ₱60k, or lands a remote role with a higher number on the contract. The nominal figure goes up, but groceries, rent, and transport went up too. I want to measure that difference instead of guessing.

This is not a yes/no question. I'm trying to put a number on it: if wages grew X% and inflation was Y%, what actually happened to purchasing power? That gap is what I'm calling out in this project.

**Decision I want the data to support:** When someone evaluates a job offer or asks for a raise, they can compare nominal pay against what inflation did over the same period — at least directionally, using public data.

---

## Audience

Filipino software developers, IT professionals, and tech workers — including people moving into data engineering like me.

Basically anyone in PH tech who has looked at their payslip and wondered if they're actually getting ahead.

---

## KPI or Key Metric

**Real Tech Purchasing Power Index (RTPI)**

Simple version:

```
RTPI ≈ nominal wage growth (%) − inflation (%)
```

I'll build this from indexed wage and CPI series so it's consistent across years. Positive RTPI means pay outpaced inflation. Negative means your raise didn't keep up.

I may also show the **gap in percentage points** directly (e.g. "wages rose 18% but inflation was 24%, so real purchasing power fell ~6%") because that's easier to read on a dashboard.

---

## Data Source Notes

*Week 2 deliverable — primary + fallback locked for RTPI (nominal wage growth − inflation).*

RTPI needs two series: **nominal wages** and **inflation**. Both come from the same primary portal (PSA OpenSTAT). The fallback covers wages only, using a different method if PSA occupation breakdowns are too coarse.

### Primary Source

- **Name:** PSA OpenSTAT — Occupational Wages Survey (OWS) + Consumer Price Index (CPI)
- **URL:**
  - OWS portal: [Occupational Wages Survey tables](https://openstat.psa.gov.ph/PXWeb/pxweb/en/DB/DB__1B__OWS/?tablelist=true)
  - OWS wage table (starting point): [Average Monthly Wage Rates of Benchmark Occupations by Industry: 2014–2022](https://openstat.psa.gov.ph/PXWeb/pxweb/en/DB/DB__1B__OWS/0031B3E1030.px/)
  - CPI portal: [2018-based CPI for All Income Households](https://openstat.psa.gov.ph/PXWeb/pxweb/en/DB/DB__2M__PI__CPI__2018/?tablelist=true)
  - CPI table (starting point): [CPI by Commodity Group (2018=100): Jan 2018 – Apr 2025](https://openstat.psa.gov.ph/PXWeb/pxweb/en/DB/DB__2M__PI__CPI__2018/0012M4ACP09.px/)
- **Format:** CSV or XLS exported from PX-Web (manual table selection → View table → Save as CSV)
- **Coverage:**
  - **Wages (OWS):** Biennial survey years 2014, 2016, 2018, 2020, 2022; national; establishments with 20+ workers; industries include *Information and Communications*; benchmark occupations include software-related roles (2022 press release cites software developers at ~₱70,595/month in information services)
  - **Inflation (CPI):** Monthly, January 2018–April 2025; Philippines + regional breakdown; headline index and commodity groups (2018=100 base)
- **Why it fits the problem:** Official government series for both sides of RTPI. OWS gives published nominal wage levels for IT-adjacent industries/occupations; CPI gives the inflation denominator to compute real purchasing power and year-over-year % changes.
- **Known limitations:**
  - OWS is **biennial**, not monthly — I'll interpolate or align to annual CPI for RTPI
  - No single "tech worker" row; I'll map *Information and Communications* industry + software/developer occupations as the proxy
  - OWS covers formal establishments (20+ employees); freelancers and early-stage startups may be underrepresented
  - CPI is all-households, not a tech-worker-specific basket (rent/food/transport weights differ from a young NCR professional's spend)
  - PX-Web requires manual export per table — no stable public API URL

### Fallback Source

- **Name:** Stack Overflow Annual Developer Survey (raw results)
- **URL:**
  - Survey hub: [survey.stackoverflow.co](https://survey.stackoverflow.co/)
  - Historical CSV archives: [StackExchange/Survey — packages/archive](https://github.com/StackExchange/Survey/tree/main/packages/archive)
  - Example direct download (2023): [stack-overflow-developer-survey-2023.zip](https://cdn.stackoverflow.co/files/jo7n4k8s/production/49915bfd46d0902c3564fd9a06b509d08a20488c.zip/stack-overflow-developer-survey-2023.zip)
- **Format:** ZIP → CSV (`survey_results_public.csv` + schema file per year)
- **Coverage:** Annual surveys 2015–2024+; global respondents; filter `Country` = Philippines; salary fields vary by year (`ConvertedCompYearly`, `CompTotal`, etc.); typically hundreds of PH responses per year (not thousands)
- **Why it could still work:** Self-reported developer compensation with annual granularity fills gaps if PSA occupation cuts are too broad. Useful as a **cross-check** on direction (did dev pay outpace inflation?) even if absolute levels differ from OWS.
- **Known limitations:**
  - Self-reported, voluntary — selection bias toward Stack Overflow users
  - Small and noisy Philippines sample; year-to-year swings may reflect sample size, not the market
  - Download URLs change each year — manual download required (documented in [StackExchange/Survey](https://github.com/StackExchange/Survey))
  - Does **not** replace CPI; inflation still comes from PSA
  - Not tax-adjusted; includes remote/US-dollar earners mixed with local PHP roles

### Supporting reference (not the fallback)

- **DOLE/NWPC regional minimum wage matrix** — [Latest Wage Orders PDF](https://nwpc.dole.gov.ph/wp-content/uploads/2026/05/Latest-Wage-Orders-Matrix-2025-2026-As-of-04-May-2026.pdf): floor wages by region, not occupation-specific. Useful sanity check only if both PSA and SO series look off.

### Feasibility check (verified)

| Check | Result |
|-------|--------|
| Primary URLs point to actual tables, not homepages | Yes — PX-Web `.px` table links above |
| Needed fields exist (wage + inflation) | Yes — OWS has industry/occupation wage rates; CPI has monthly index |
| Time overlap for RTPI | Yes — OWS 2014–2022 overlaps CPI 2018–2025; 2014–2017 wage years can use 2012-based CPI if needed |
| Public, downloadable | Yes — free PX-Web export; SO CSV under ODbL |
| Fallback differs from primary weakness | Yes — SO is annual + dev-specific vs PSA biennial establishment survey |

**Analysis window (tentative):** 2018–2022 for core RTPI (full CPI + latest OWS overlap); extend with SO annual series and 2024 CPI for trend context.

---

## Possible Final Dashboard

One page, nothing fancy at first. The main view:

1. **Line chart** — nominal tech wages vs real purchasing power over time (both indexed to 100 at the start of the series). The space between the lines is the story.
2. **Headline stat** — something like: *"If you earned ₱70,000 at the start of the period, inflation-adjusted that's about ₱___ today."*
3. **Year-by-year table** — nominal growth, CPI, and RTPI so you can spot which years hurt the most.

I'll add a short note on limitations (self-reported survey data, PH sample size, not tax-adjusted). This is exploratory — not financial advice.

---

## How I'll build it (DEP phases)

| Phase | Weeks | Plan |
|-------|-------|------|
| Foundations | 1–4 | This README, confirm sources, repo setup |
| Data collection | 5–6 | `scripts/ingest.py` → `data/raw/` |
| Processing | 7–12 | `scripts/transform.py`, SQLite, clean joins on year/month |
| Analysis | 13–16 | Notebook, 3–5 charts, write up what the gap actually looks like |
| Predictive (Path A) | 17–20 | Simple model — e.g. forecast RTPI direction from CPI trends |
| Deployment | 21–24 | Static dashboard on GitHub Pages |

---

## Milestones

| Milestone | Target | Status |
|-----------|--------|--------|
| M0 — Problem statement | Week 1 | Done |
| M1 — Data source locked | Week 3–4 | In progress (Week 2 notes complete) |
| M2 — Ingestion working | Week 6 | Not started |

Submit here when ready: [DEP milestone issues](https://github.com/dataengineeringpilipinas/dep-data-engineering-open-track/issues/new/choose)

---

## Repo layout

```
data/raw/          ← downloads land here
data/processed/    ← cleaned tables after transform.py
scripts/           ← ingest.py, transform.py
notebooks/         ← analysis
output/figures/    ← chart exports
dashboard/         ← GitHub Pages site (later)
```

Pipeline scripts aren't wired up yet — that's Phase 2.
