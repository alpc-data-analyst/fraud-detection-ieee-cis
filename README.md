<p align="center">
  <img src="assets/banner.png" alt="Catching Fraud Before It Happens — Machine Learning" width="100%">
</p>

<p align="center">
  <b>English</b> · <a href="README.es.md">Español</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/XGBoost-model-006400" alt="XGBoost">
  <img src="https://img.shields.io/badge/scikit--learn-ML-F7931E?logo=scikitlearn&logoColor=white" alt="scikit-learn">
  <img src="https://img.shields.io/badge/Explainability-SHAP-E8743B" alt="SHAP">
  <img src="https://img.shields.io/badge/ROC--AUC-0.9186-2DD4BF" alt="ROC-AUC 0.9186">
</p>

<p align="center">
  <b>Machine learning model that flags fraudulent card transactions across ~590,000 real operations.</b><br>
  Exploratory analysis, XGBoost with time-based validation and SHAP explainability — from raw data to an auditable decision.
</p>

---

## The pipeline at a glance

```mermaid
flowchart LR
    A["IEEE-CIS data<br/>~590,000 transactions"] --> B["Join<br/>transaction + device"]
    B --> C["Exploratory<br/>analysis"]
    C --> D["Feature<br/>engineering"]
    D --> E["XGBoost<br/>time-based validation"]
    E --> F["Evaluation<br/>ROC-AUC · PR-AUC"]
    F --> G["Explainability<br/>SHAP"]

    classDef model fill:#0d9488,stroke:#0d9488,color:#fff;
    classDef out fill:#1f2937,stroke:#374151,color:#fff;
    class E model;
    class G out;
```

## What it solves

Detect fraud **before the transaction clears**, balancing two opposing costs: catching as much real fraud as
possible without blocking legitimate customers (false positives). That's why the project goes beyond a single
metric and treats the **decision threshold** as a business choice.

## What's inside

| Stage | Content |
|-------|---------|
| **Data** | Join of transactions (amount, card, email, time) with device identity via `TransactionID`. |
| **Exploratory analysis** | Fraud rate by device, email domain, card type, hour of day, IP–billing distance and spending deviation. |
| **Model** | `XGBoost` with **time-based validation**: trained on the past, validated on the future. |
| **Evaluation** | ROC-AUC, **PR-AUC** (suited to class imbalance) and decision-threshold analysis. |
| **Explainability** | `SHAP`, both global (what drives the model overall) and local (why a single transaction is flagged). |

## Key technical decisions

- **Time-based validation, not random.** Shuffling transactions would leak the future and inflate the metrics.
- **No data leakage in the spending feature.** The per-card mean spend is computed **on the training window only**.
- **An honest metric for imbalance.** With ~3.5% fraud, **PR-AUC** is more informative than ROC-AUC alone.
- **The threshold as a business decision.** 0.5 vs 0.1 is compared to expose the trade-off caught fraud ↔ false positives.

## Results

**ROC-AUC: 0.9186** on the time-based validation set (the most recent 20% of transactions).

ROC curve, confusion matrix and score separation:
![Evaluation dashboard](assets/dashboard.png)

Most influential features:
![Feature importance](assets/feature_importance.png)

Local explainability — why one transaction is flagged as fraud (SHAP):
![SHAP explanation](assets/shap_local.png)

Decision threshold — catching more fraud at the cost of more false positives:
![Threshold analysis](assets/umbral.png)

## How to run

The dataset is large; running on [Google Colab](https://colab.research.google.com/github/alpc-data-analyst/fraud-detection-ieee-cis/blob/main/deteccion_fraude.ipynb) is recommended (free RAM, no local setup).

```bash
pip install opendatasets pandas numpy matplotlib seaborn scikit-learn xgboost shap
```

Open `deteccion_fraude.ipynb` and run the cells top to bottom. The first cell downloads the dataset
from Kaggle and asks for your Kaggle credentials (username and API key).

## Tech stack

`pandas` · `numpy` · `scikit-learn` · `XGBoost` · `SHAP` · `matplotlib` · `seaborn`

## Possible improvements

- Time-based cross-validation (several folds) instead of a single split.
- Probability calibration and `scale_pos_weight` for the class imbalance.
- Aggregated frequency / velocity features per card, device and IP.
- Threshold tuning driven by the real cost of each error type.
