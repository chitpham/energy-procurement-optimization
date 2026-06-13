# Energy Procurement Optimization
### Multi-Objective Linear Programming for Manufacturing Plants under Price & Supply Uncertainty

![Status](https://img.shields.io/badge/status-in%20progress-yellow)
> 📄 [View Project Brief](brief/project_brief.pdf) - methodology, model structure, and expected outputs
---
## Background

This is my personal project applying concepts from **Decision Analysis** coursework at **Aalto University** - decision trees, probability estimation, and linear optimization - to a realistic industrial energy procurement problem in the electricity market context.

---

## Overview

A manufacturing plant must procure electricity every hour to meet production demand. Electricity can be sourced from three options: a standard fixed-price contract, a RE-certified contract (Guarantee of Origin), and the spot market. No single source is optimal across all dimensions simultaneously.

This project determines the **optimal hourly portfolio mix** - how much to procure from each source - by applying a **scenario-based Multi-Objective Linear Programming (MOLP)** framework that balances three competing objectives:

1. **Minimize procurement cost**
2. **Minimize supply risk** (contract generator failure → balancing market exposure)
3. **Minimize CO2 emission** (standard contract & spot -> grid mix CO2 intensity, while 0 emission for RE-certified contract)

---

## Decision Framework

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

## Optimization Model

**Objective:**
```
min  Σₛ p(s) · [ w₁.Cost(s)  +  w₂ . SupplyRisk(s)  +  w₃ . Carbon Intensity(s) ]
```

| Term | Formula | Description |
|------|---------|-------------|
| Cost(s) | Σₜ [p₁.x₁ + p₂.x₂ + p₃(t).x₃] | Procurement cost in € |
| SupplyRisk(s) | Σₜ e.(x₁+x₂)·max[0,(p_bal(t) - p₃(t))]  | Expected balancing cost from contract generator failure |
| Carbon Intensity(s) | Σₜ ci(t).(x₁+x₃(t)).carbon_price | CO2 intesity from grid mix |

**Key assumptions:**
| Parameter | Baseline |
|-----------|----------|
| Contract failure probability π | 0.02 | 
| Standard contract cap C̄₁ | 50% of avg D(t) |
| RE contract cap C̄₂ | 20% of avg D(t) | 
| Standard contract price p₁ | E[spot] × 1.10 | 
| RE contract price p₂ | E[spot] × 1.15 |

**Weights w₁, w₂, w₃** are set by the company as priority preferences. The model is solved across multiple weight combinations to produce a **trade-off frontier**.

---
## Key Findings

- **Cost and Carbon are largely insensitive to company priorities**: Finland's grid is already clean and inexpensive, so even green-focused or risk-averse strategies cost nearly the same as cost-focused ones.
- **Supply Risk is where priorities diverge**: Green-focused profiles show the highest risk (RE contract counts toward both green score and risk exposure), while Risk-averse profiles drive risk to zero.
- **Portfolio composition shifts predictably**: Green priority roughly triples RE contract share (4%→15%); higher-stakes hours (peak demand, expensive prices) shift the mix toward contracts for price certainty.

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

## Expected Outputs

| # | Output | Description |
|---|--------|-------------|
| 01 | Scenario probability table | Calibrated joint probabilities table of 9 scenarios |
| 02 | Optimal portfolio mix | Hourly x₁ / x₂ / x₃ allocation per scenario |
| 03 | Cost vs baseline | Expected savings vs naive always-spot strategy (€/year) |
| 04 | Trade-off frontier | Cost vs green score as w₁, w₂, w₃ weights vary |
| 05 | Contract premium sensitivity | Optimal mix at 5% / 10% / 15% premium levels |
| 06 | Supply risk analysis | Impact of contract failure probability π on cost and balancing exposure |

---

## Status

- [x] Project brief & methodology defined
- [x] Data sources identified
- [x] Data collection (`00_data_collection.ipynb`)
- [x] EDA (`01_eda.ipynb`)
- [x] Decision tree (Refer to project `brief`)
- [x] Optimization model (`02_optimization.ipynb`)

