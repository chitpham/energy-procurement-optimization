# Energy Procurement Optimization
### Multi-Objective Linear Programming for Manufacturing Plants under Price & Supply Uncertainty

## Overview
A manufacturing plant must procure electricity every hour to meet production demand, sourcing from three options: a standard fixed-price contract, a RE-certified contract (Guarantee of Origin), and the spot market. This project applies concepts from **Decision Analysis** coursework at **Aalto University** to determine the optimal hourly portfolio mix that balances:
1. Cost: total procurement spend
2. Supply Risk: exposure to balancing market costs if a contract generator fails to deliver
3. Carbon Cost: monetary cost of emissions (via EU ETS carbon price) from grid-mix electricity (standard contract & spot vs. zero-emission RE contract)

A **decision tree** (9 scenarios: demand level x spot price state) structures the uncertainty, and a stochastic LP solves the optimal allocation across x1 (standard contract), x2 (RE contract), and x3 (spot) for each hour - weighted across scenarios by empirical probability.

---
## Key Findings:
1. **Cost and Carbon are largely insensitive to company priorities**: As Finland's grid is already clean and inexpensive, so even green-focused or risk-averse strategies cost nearly the same as cost-focused ones.
2. **Supply Risk is where priorities diverge**: Green-focused profiles show the highest risk (RE contract counts toward both green score and risk exposure), while Risk-averse profiles drive risk to zero.
3. **Portfolio composition shifts predictably**: Green priority roughly triples RE contract share (4%->15%); higher-stakes hours (peak demand, expensive prices) shift the mix toward contracts for price certainty.
   
---

## Approach

The model combines two complementary components:

**1. Decision Tree** — structures uncertainty into 9 scenarios across 2 layers:
- Demand level (Light / Medium / Peak) 
- Spot price state (Cheap / Normal / Expensive) P33/P67 thresholds

**2. Stochastic LP** — for each scenario, optimally allocates:
- `x₁(t)`: MWh from standard contract
- `x₂(t)`: MWh from RE-certified contract
- `x₃(t)`: residual demand covered by spot market

Weighted across all 9 scenarios by their empirical probabilities → **optimal in expectation**.

---

## Data Sources

| Dataset | Source | Role | Period |
|---------|--------|------|--------|
| DAEWOO Steel Energy Consumption | [UCI ML Repository](https://archive.ics.uci.edu/dataset/851/steel+industry+energy+consumption)  | Demand profile D(t), Load_Type scenario labels | 2018 |
| Day-ahead spot prices (Finland) | [ENTSO-E Transparency Platform](https://transparency.entsoe.eu) | Spot price p₃(t) & price tier thresholds | 2018 |
| Balancing market prices | [Fingrid Open Data](https://data.fingrid.fi) | Balancing price p_bal(t) & Emission factor ci(t)| 2018 |

> **Note on data alignment:** DAEWOO (2018) and Finland market data (2019) are used as independent proxies - consumption *patterns* from DAEWOO, price *data* from Finland 2019 markets. 

---

## Project Structure (expected)

```
energy-procurement-optimization/
│
├── README.md
├── brief/
│   └── project_brief.pdf          # Full project brief deck
│
├── data/
│   ├── raw/                       # Downloaded source data
│   ├── processed/                 # Cleaned & merged datasets'
│   └── result/                    # Hourly portfolio
│
└── notebooks/
    ├── 00_data_collection.ipynb   # Download & save all raw data
    ├── 01_eda.ipynb               # Exploratory data analysis & probability estimation
    └── 02_optimization.ipynb      # MOLP model & solution & Results Analysis
```
---

## Status

- [x] Project brief & methodology defined
- [x] Data sources identified
- [x] Data collection (`00_data_collection.ipynb`)
- [x] EDA (`01_eda.ipynb`)
- [x] Decision tree (Refer to project `brief`)
- [x] Optimization model (`02_optimization.ipynb`)


