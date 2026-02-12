# Capital Bikeshare — Demand Forecasting, Bike/Dock Allocation & Crew Clustering

This repository contains an end-to-end analytics workflow for **Capital Bikeshare operations planning**, focused on a capacity-constrained station (**“22nd & H St NW”**) and extendable to the full network.

## Problem
Some stations frequently hit capacity limits, causing missed pickups (no bikes) and missed drop-offs (no docks). This project builds tools to:
1) **Forecast next-day demand**: pickups (PU) and drop-offs (DO)  
2) **Translate forecasts into bike vs dock allocation** under capacity constraints  
3) **Cluster stations for crew deployment** (rebalancing/operations planning)

Success is measured using:
- **Prediction accuracy** (out-of-sample MSE)
- **Decision cost**:  \u03B1·missPU + \u03B2·missDO with \u03B1 = 2 and \u03B2 = 3 (operations penalty) :contentReference[oaicite:0]{index=0}

---

## Dataset & Features
- Station-level daily PU/DO counts (example analysis uses **Feb 1 – Apr 30**) :contentReference[oaicite:1]{index=1}  
- Weather enrichment: merged a **33-column DC weather feed** (Visual Crossing) with daily demand by date :contentReference[oaicite:2]{index=2}

Key observed relationships:
- Demand trends show weekday peaks and weekend troughs.
- Temperature shows mild positive relationship; precipitation/wind show weak negative; **UV index** is among the stronger correlates. :contentReference[oaicite:3]{index=3}

---

## Methods (What’s implemented)
### 1) Forecasting (Next-day PU/DO)
- Linear baseline feature ladder (temp → +precip → +wind → +uvindex → +icon) :contentReference[oaicite:4]{index=4}  
- Regularization: Lasso to shrink noisy variables and improve generalization :contentReference[oaicite:5]{index=5}  
- Multi-output modeling with PCA (4 PCs) + scaling; tuned models via randomized search :contentReference[oaicite:6]{index=6}  

### 2) Decision Support: Bike vs Dock Allocation
- Converts forecasts into an **optimal allocation** under capacity **K** and asymmetric penalties (\u03B1,\u03B2) :contentReference[oaicite:7]{index=7}  
- Model selection is scenario-specific: optimize for MSE vs operational cost depending on K and penalty weights :contentReference[oaicite:8]{index=8}  

### 3) Network Clustering for Crew Deployment
Clustering over ~880 stations (network-wide):
- k-means (k≈8), k-median (Manhattan), k-medoids (real-station depots), hierarchical (Ward) :contentReference[oaicite:9]{index=9}  
- Outputs can be used to define **rebalancing zones** and suggested depot stations :contentReference[oaicite:10]{index=10}  

---

## Results Snapshot (from report)
- Typical daily demand at the target station: ~**33±8 pickups** and **33±7 drop-offs** during the sample window :contentReference[oaicite:11]{index=11}  
- Lasso improves PU MSE vs baseline and helps interpret drivers by shrinking weak predictors :contentReference[oaicite:12]{index=12}  
- Operational cost can favor models that don’t minimize MSE (trade-off between forecast precision and cost) :contentReference[oaicite:13]{index=13}  

---
