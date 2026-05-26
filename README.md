# GaRisk
### Gallium Supply Chain Disruption & Procurement Optimization Platform

A Python-based analytics platform that models the cost impact of China's 2024 gallium export ban on a US-based chip manufacturer — and identifies the optimal sourcing and inventory strategy across alternative suppliers.

---

## Problem Statement

In December 2024, China banned all gallium exports to the United States. China controls 98% of global primary gallium production. Gallium is a critical input for GaN and GaAs compound semiconductors used in 5G base stations, EV power electronics, and defense radar systems.

US chip manufacturers dependent on Chinese gallium faced three immediate decisions:
- Where to source gallium now that China supply is zero
- How much strategic inventory to hold
- When to place orders given alternative supplier lead times of 8–16 weeks

GaRisk models these decisions quantitatively — showing the cost of doing nothing versus the cost of an optimized procurement strategy.

---

## Objective Function

**Minimize** total procurement cost over a 12-month disruption horizon by jointly optimizing:
- Sourcing allocation across alternative suppliers (Japan, Germany, Canada, Slovakia)
- Strategic inventory levels month by month
- Procurement timing given variable lead times

**Subject to:**
- Gallium supply from China = 0 (export ban in effect)
- Alternative supplier lead times: 4–14 weeks depending on source
- End market service level ≥ 95% (automotive, 5G, defense)
- Monthly procurement budget constraint
- Minimum and maximum order quantities per supplier

---

## Project Structure

```
GaRisk/
│
├── data/
│   ├── raw/                        # Downloaded source data
│   │   ├── usgs_gallium_2024.csv   # USGS MCS 2024 gallium data
│   │   ├── usgs_gallium_2025.csv   # USGS MCS 2025 gallium data
│   │   ├── un_comtrade_hs280530.csv # UN Comtrade import volumes
│   │   └── usitc_gallium_brief.csv # USITC price and trade data
│   └── processed/
│       ├── baseline_model.csv      # Pre-ban sourcing baseline
│       ├── supplier_params.csv     # Lead time, cost, capacity by supplier
│       └── demand_profile.csv      # Monthly US demand by end market
│
├── modules/
│   ├── 01_baseline/
│   │   └── baseline_model.py       # Pre-ban SC model
│   ├── 02_disruption/
│   │   └── disruption_model.py     # Cost impact of ban over 12 months
│   ├── 03_optimizer/
│   │   └── procurement_optimizer.py # PuLP LP model — optimal sourcing
│   └── 04_scenarios/
│       └── scenario_comparison.py  # No-action vs optimized comparison
│
├── dashboard/
│   └── app.py                      # Streamlit dashboard
│
├── reports/
│   ├── case_study.pdf              # 2-page consulting-style case study
│   └── powerbi_report.pbix         # Power BI executive report
│
├── notebooks/
│   └── exploratory_analysis.ipynb  # EDA and data exploration
│
├── requirements.txt
└── README.md
```

---

## Modules

### Module 1 — Baseline SC Model
Models the normal state of US gallium procurement before the ban.

**Inputs:** USGS import data, UN Comtrade volumes, historical prices
**Outputs:** Pre-ban sourcing split by country, baseline cost per kg, normal lead times, baseline inventory levels

**Key parameters:**
| Supplier | Pre-ban share | Cost ($/kg) | Lead time (weeks) |
|---|---|---|---|
| China | 19% | $240 | 6–8 |
| Japan | 24% | ~$420 | 4–6 |
| Germany | 19% | ~$430 | 6–8 |
| Canada | 17% | ~$390 | 8–12 |
| Slovakia | 5% | ~$370 | 10–14 |
| Other | 16% | Variable | Variable |

---

### Module 2 — Disruption Impact Model
Simulates what happens when China supply drops to zero from December 2024.

**Inputs:** Baseline model, monthly US demand by end market
**Outputs:** Inventory depletion curve, monthly shortage volume, cumulative cost impact over 12 months under no-action scenario

**Cost components modeled:**
- Shortage cost — lost revenue from unmet chip demand
- Expedite cost — premium paid for urgent alternative sourcing
- Holding cost — cost of carrying strategic stockpile

---

### Module 3 — Procurement Optimizer
Linear programming model that finds the optimal sourcing and inventory strategy.

**Decision variables:**
- Q_j,t — quantity ordered from supplier j in month t
- I_t — inventory level at end of month t
- S_t — shortage quantity in month t

**Tool:** PuLP (Python LP library)

**Outputs:** Optimal order quantities per supplier per month, optimal inventory trajectory, minimum achievable total cost

---

### Module 4 — Scenario Comparison
Compares three procurement strategies side by side.

| Scenario | Description |
|---|---|
| No action | Company continues as before, runs out of gallium |
| Partial diversification | Company sources 50% from alternatives |
| Full optimization | LP-optimized sourcing and inventory strategy |

**Output:** Cost comparison table and chart across all three scenarios

---

## Data Sources

| Source | URL | What it provides |
|---|---|---|
| USGS MCS 2025 | pubs.usgs.gov/periodicals/mcs2025/mcs2025-gallium.pdf | Import volumes, prices, production by country |
| USGS MCS 2026 | pubs.usgs.gov/periodicals/mcs2026/mcs2026-gallium.pdf | Post-ban price impact, 2025 import data |
| USITC Briefing | usitc.gov/publications/332/executive_briefings/ebot_germanium_and_gallium.pdf | Price and trade flow impact of export controls |
| UN Comtrade | comtrade.un.org (HS code 280530) | Monthly import volumes by country 2018–2024 |
| DOE TRACE-Ga | energy.gov/cmei | Domestic recovery capacity and timeline |
| Wesselkaemper et al. 2025 | escholarship.org/uc/item/2j64q0xq | Material flow analysis, cost benchmarks |

---

## Key Findings

*(To be completed after analysis)*

- Baseline monthly gallium cost under normal conditions: $X
- Cost impact of doing nothing over 12 months: $X
- Cost of optimized procurement strategy: $X
- Savings from optimization vs no-action: $X (X%)
- Recommended sourcing split: X% Japan, X% Germany, X% Canada, X% Slovakia
- Recommended strategic stockpile: X weeks of supply

---

## Dashboard

Built with Streamlit. Three tabs:

**Tab 1 — Baseline:** Pre-ban SC network map, sourcing split by country, normal cost structure

**Tab 2 — Disruption:** Inventory depletion curve, monthly shortage volume, cumulative cost under no-action

**Tab 3 — Optimizer:** Optimal sourcing split, monthly order quantities, cost comparison across scenarios

Run locally:
```bash
pip install -r requirements.txt
streamlit run dashboard/app.py
```

---

## Power BI Report

Four pages:

- Page 1: Supply chain overview and gallium dependency
- Page 2: Disruption cost impact — month by month
- Page 3: Optimization results — sourcing split and savings
- Page 4: Strategic recommendations

---

## Installation

```bash
git clone https://github.com/yourusername/garisk
cd garisk
pip install -r requirements.txt
```

**Requirements:**
```
pandas
numpy
pulp
plotly
streamlit
matplotlib
scipy
jupyter
```

---

## Citation

If you use this project please cite the following key sources:

- Wesselkaemper et al. (2025). Enhancing supply resilience for critical materials: case study of gallium supply in the United States. *Resources, Conservation and Recycling*, 222, 108436.
- USGS (2025). Mineral Commodity Summaries 2025 — Gallium. US Geological Survey.
- USITC (2023). Germanium and Gallium Export Controls. Executive Briefing on Trade.

---

## Author

Built as a supply chain analytics portfolio project.
Targeting SC analyst, procurement, and operations roles.

---

*Data current as of 2025. China's gallium export ban on the US was enacted December 2024 and temporarily lifted November 2025 for one year.*
