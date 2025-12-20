# Temporal Concept Drift and Feature Interaction Analysis

![Main workflow](flow_chart.png)

This repository implements a **drift-aware, explainable machine learning framework** to study how predictive relationships and feature dependencies evolve over time under **non-stationarity**.

The project is grounded in a real-world longitudinal dataset of airline customer reviews and focuses on **concept drift**, **robust feature reliance**, and **interaction dynamics**, rather than static prediction.

The work goes beyond conventional sentiment analysis or one-shot explainability by explicitly modeling **temporal instability** and **feature interdependence**.

---

## Problem Motivation

Most predictive analytics pipelines implicitly assume that:

- feature distributions remain stable  
- feature–target relationships do not change over time  
- single-model explanations are sufficient  

These assumptions break down in real systems exposed to **shocks, regime changes, and long time horizons** (e.g. crises, market disruptions, behavioral shifts).

This project addresses three core questions:

1. Which features are genuinely relied upon across equally good models?
2. How does that reliance change over time (concept drift)?
3. How do feature interactions evolve under non-stationarity?

---

## Dataset

- **Domain:** Airline customer reviews  
- **Source:** Skytrax (via Kaggle)  
- **Airlines:** British Airways, Emirates  
- **Time span:** 2015–2023  

### Size
- British Airways: ~3,700 reviews  
- Emirates: ~1,500 reviews  

### Data Modalities
- Structured sub-ratings (seat comfort, staff service, food quality, ground service, etc.)
- Free-text reviews
- Contextual metadata (traveller type, cabin class, route, aircraft, verification status)
- Temporal segmentation: **pre-COVID / during-COVID / post-COVID**

---

## Feature Engineering

### Text-Derived Features
- Aspect-based sentiment (staff, seat, food, entertainment)
- Emotion scores (joy, anger, optimism, sadness)
- Topic modeling using **BERTopic** to capture latent themes  
  (refunds, disruptions, lounges, safety)

### Structured Features
- One-hot encoded traveller and trip attributes
- Missingness indicators for structurally unavailable services  
  (e.g. Wi-Fi, entertainment on short-haul flights)

This design explicitly distinguishes **service absence** from **service quality**, avoiding a common modeling error.

---

## Modeling Framework

### Baseline Predictor
- **Random Forest regression**

Chosen for:
- non-linear capacity
- mixed data compatibility
- formal compatibility with **Model Class Reliance (MCR)**

### Evaluation Strategy
- Rolling-origin temporal validation
- Strict leakage control across time windows
- Performance degradation used as a secondary signal for concept drift

---

## Explainability and Drift Methodology

### 1. Model Class Reliance (MCR)
- Computes reliance intervals across the **Rashomon set** of near-optimal models
- Focus on **MCR⁺ (upper bound)** as a conservative measure of feature necessity
- Avoids instability and redundancy issues inherent in SHAP-only interpretations

### 2. Concept Drift Analysis

Drift is quantified using:
- Change in MCR⁺ magnitude across periods
- Feature rank volatility
- **Concept Drift Index (CDI)** as a system-level measure

Validated using:
- Kruskal–Wallis and Kolmogorov–Smirnov tests
- Out-of-period performance decay
- Retraining and re-evaluation checks

### 3. Feature Interaction Analysis
- SHAP interaction values computed on **MCR-screened features**
- Interactions validated via:
  - nested regression tests
  - out-of-sample performance impact
  - bootstrap stability checks
- Interpreted using **PDP** and **ICE** surfaces

---

## Key Findings (High Level)

### British Airways
- Abrupt, crisis-sensitive drift
- Strong reweighting toward refunds, disruptions, and ground service during COVID
- Interaction effects show **compounding penalties** when multiple service dimensions fail

### Emirates
- More stable explanatory structure
- Gradual reliance shifts anchored in sentiment-driven features
- Interaction effects reveal **reinforcing synergies** rather than collapse under stress

These results demonstrate that **prediction accuracy alone hides structural instability** in how models explain outcomes over time.
