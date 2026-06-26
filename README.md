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

## Likely Data Source

### Primary (government data — this is the backbone)

| Source | Link | Why I'm using it |
|--------|------|------------------|
| PSA OpenSTAT — Consumer Price Index | [openstat.psa.gov.ph](https://openstat.psa.gov.ph/) | Official PH inflation, monthly |
| PSA OpenSTAT — Occupational Wages Survey | [Labor and Employment](https://openstat.psa.gov.ph/Database/Labor-and-Employment) | Published wage data by occupation — I'll map IT / professional categories to tech workers |

### Supplement

| Source | Link | Why I'm using it |
|--------|------|------------------|
| Stack Overflow Developer Survey | [survey.stackoverflow.co](https://survey.stackoverflow.co/) | Self-reported dev salaries, filterable to Philippines — useful for comparing survey trends against PSA figures |

### Fallback

DOLE/NWPC regional minimum wage tables — if OWS doesn't have clean enough IT breakdowns for the years I need.

**Time range:** Whatever both PSA and the survey cover once I pull the files — likely mid-2010s through 2025, not a single arbitrary start year.

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
| M1 — Data source locked | Week 3–4 | Not started |
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
