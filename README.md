# 🏦 IMC Trading Competition – Statistical Arbitrage Strategy

## 📌 Introduction

This repository contains my submission for the IMC Trading Competition. The focus is on developing and analyzing statistical arbitrage strategies across five exotic tradables. The analysis includes empirical data exploration, modeling, and strategy simulation using both rule-based and dynamic programming techniques.

All results are supported by Monte Carlo simulations and visualized using custom-built Python tooling.

---

## 🌿 Rainforest Resin

### 📊 Overview
Briefly describe the behavior and unique characteristics of Rainforest Resin as a tradable.

### 🔍 Analysis
- Step distribution analysis
- Price path modeling
- Strategy performance comparison

### 🖼️ Figures
![Rainforest Resin PnL](images/rainforest_resin_pnl.png)

---

## 🪸 Kelp

### 📊 Overview
Insight into volatility, mean-reversion, or trend behavior observed in Kelp.

### 🔍 Analysis
- Comparison to synthetic walk models
- Positioning strategies
- Gain against baseline

### 🖼️ Figures
![Kelp Strategy Performance](images/kelp_strategy.png)

---

## 🧺 Baskets

### 📊 Overview
Description of behavior (e.g., high noise, drift, structure) and modeling approach.

### 🔍 Analysis
- Empirical vs. model step distribution
- Strategy timing
- Dynamic range adaptation

### 🖼️ Figures
![Baskets Walk Modeling](images/baskets_walks.png)

---

## 🪨 Volcanic Rocks

### 📊 Overview
Summary of volatility spikes or regime changes.

### 🔍 Analysis
- Edge-aware strategies
- Log-scaled heatmaps of gain
- Model calibration details

### 🖼️ Figures
![Volcanic Rocks DP Gain](images/volcanic_rocks_dp_gain.png)

---

## 🍰 Macarons

### 📊 Overview
Discuss stationarity or patterns observed in Macaron pricing.

### 🔍 Analysis
- Range-based triggers
- Real vs simulated comparisons
- Monte Carlo EVs

### 🖼️ Figures
![Macaron EV Heatmap](images/macarons_ev_heatmap.png)

---

## 🧠 Summary & Insights

Across all assets, we observe that hybrid strategies combining global extrema with mid-range threshold triggers outperform simple min/max trading under most simulated distributions. The results confirm that statistical modeling of price steps and dynamic optimization add significant value.

---

## 🗂️ Repository Structure

