# 📈 15-Day Electricity Price Forecasting with Stacked Machine Learning Models

## 📌 Project Overview
This project focuses on hourly forecasting of French wholesale electricity spot prices over a 15‑day horizon, using a stacked ensemble of machine learning models.

Electricity prices exhibit strong seasonality, volatility clustering, and regime shifts driven by weather, fuel prices, and market conditions. The objective is to design a robust, long‑horizon forecasting pipeline that remains stable across both calm and volatile periods.

This work is intended as a learning and portfolio project, with emphasis on:
- Time-series rigor  
- Machine learning best practices  
- Domain-specific considerations for energy markets  

---

## 📊 Data

**Market:** France  
**Target:** Hourly wholesale electricity spot prices  
**Horizon:** 15 days ahead (hourly resolution)  
**Frequency:** Hourly  

### 🔗 Data Sources
**Electricity prices:**  
Public dataset from Ember  
https://ember-energy.org/data/european-wholesale-electricity-price-data/

**Exogenous features:**
- Calendar effects (hour, day of week, holidays)
- Weather variables from "France Energy and Weather Data hourly/daily" dataset in KAGGLE
  https://www.kaggle.com/datasets/ravvvvvvvvvvvv/france-energy-weather-hourly/data
- Natural gas price (GTT proxy) from ABC Bourse
   https://www.abcbourse.com/graphes/eod/GTTp

All data are treated as public and non‑proprietary.

---

## 🧩 Feature Engineering
The model uses a combination of domain-driven and statistical features designed to capture both short-term dynamics and longer-term regimes.

### Core Feature Groups
- **Lagged price features**
- **Rolling statistics**
  - Short-term (12h, 24h) rolling mean and volatility  
  - Weekly (168h) volatility and quantile-based features
- **Quantile features**
  - Weekly quantiles (e.g., 10%, 50%)  
  - Inter-quantile range (IQR) to characterize volatility regimes
- **Seasonality**
  - Fourier terms for annually, weekly and daily patterns
- **Calendar effects**
  - Day, Month, Hour of day, day of week, weekends, holidays
- **Exogenous variables**
  - Weather indicators : temperature, precipitation, cloud cover, global tilted irradiance , wind_speed_100m , wind_direction_100m
  - Natural gas price
  - Electricity Consumption in MW

These features help models adapt to changing price regimes, especially during high volatility or sharp price drops.

---

## 🤖 Modeling Approach

### Base Learners
Four individual models are trained independently:
- XGBoost  
- LightGBM  
- CatBoost  
- Ridge Regression  

Each model contributes differently:
- Tree-based models capture nonlinearities and spikes  
- Ridge provides a smooth, bias-stable baseline  

### Training Strategy
- Time-series cross-validation for all models  
- Out-of-fold (OOF) predictions used to train the meta learner  
- Strict separation between train / validation / test sets to avoid leakage

### Hyperparameter 
Tuning To improve model robustness and reduce overfitting, each base learner undergoes dedicated hyperparameter optimization: 
- **XGBoost:** Bayesian Optimization
- **LightGBM:** Random Search
- **CatBoost:** Random Search

These tuning strategies balance exploration and computational efficiency, ensuring each model operates near its optimal configuration before stacking.

### Stacking & Meta Learning
A **Ridge regression meta learner** is trained on OOF predictions plus additional context features:
- Mean and standard deviation of base model predictions  
- Hour-of-day indicator  
- Short-term volatility features  

This enables the meta learner to:
- Dynamically reweight models depending on volatility regimes  
- Reduce systematic overprediction during calm or declining periods  
- Improve robustness over long forecast horizons  

---

## 📈 Evaluation

### Metrics
- **MAE** (Mean Absolute Error)  
- **R² score**

### Indicative Results
**Meta learner:**
- R² ≈ 74%  
- MAE ≈ 6.79  

**Individual models:**
- MAE typically > 7.25  

The stacked model consistently outperforms individual learners and shows improved stability during volatile regimes.

---

## 🧠 Key Challenges Addressed
- Long-horizon (15‑day) uncertainty  
- Volatility clustering and regime changes  
- Overprediction bias in tree-based models  
- Feature redundancy and model correlation in ensembles  

---

## 🚧 Limitations & Future Work
Current limitations:
- No real-time retraining pipeline (static dataset)  
- Limited availability of high-frequency fundamental data  

Potential improvements:
- Regime-specific evaluation  
- Probabilistic forecasting  
- Automated daily retraining (if live data becomes available)  

---

## ⚠️ Disclaimer
This project is **not** a trading system and does **not** provide financial advice.  
It is intended for educational and portfolio purposes only.

---

## 🎯 Why This Project?
This project demonstrates:
- Rigorous handling of time-series data  
- Advanced ensemble and stacking techniques  
- Practical understanding of electricity market dynamics  
- Production-oriented thinking under realistic data constraints  

