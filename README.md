# Vauban 50 — Big Data Purchase Prediction Project

**Course:** Big Data · 2nd Semester 2026  

---

## Project Overview

Vauban 50 (V-50) is a premium backpack brand founded in Lille in 2022, selling four products exclusively online: **CorePack**, **TechFortress**, **AirLite**, and **EcoShell**.

The company hired our team as Big Data consultants with two objectives:

1. **Binary classification** — Predict whether a website visitor will place an order (`1 = order`, `0 = no order`)
2. **Multi-class classification (bonus)** — For predicted buyers, identify which specific product (`product_id` 1–4) they are most likely to purchase

The models are trained on historical session and order data (March 2022 – January 2025) and applied to a holdout dataset (February – March 2025) to generate final predictions.

---

## Folder Structure

```
BigData Github/
│
├── README.md                  ← This file
├── .gitignore                 ← Python/Jupyter ignore rules
│
├── notebook/
│   ├── BigData_Notebook.ipynb ← Main PySpark analysis notebook
│   └── README.md              ← Notebook walkthrough and usage guide
│
├── outputs/
│   ├── prediction_result.csv  ← Final predictions on holdout data
│   └── README.md              ← Column definitions and result summary
│
└── slides/
    ├── Presentation.pdf       ← Project presentation deck
    └── README.md              ← Slide structure and key takeaways
```

---

## How to Run

The notebook is designed to run on **Google Colab** with PySpark. Follow these steps:

1. **Open Google Colab** at [colab.research.google.com](https://colab.research.google.com)
2. **Upload** the notebook `BigData_Notebook.ipynb`
3. **Upload** the five Parquet datasets to your Google Drive:
   - `order_items.parquet`
   - `orders.parquet`
   - `products.parquet`
   - `website_pageviews.parquet`
   - `website_sessions.parquet`
   - `website_sessions_holdout.parquet`
   - `website_pageviews_holdout.parquet`
4. **Update** the data path in the notebook to your Drive location
5. **Run all cells** (`Runtime → Run all`)

---

## Dependencies

All dependencies are installed automatically inside the notebook. The core stack is:

| Package | Purpose |
|---|---|
| `pyspark` | Distributed data processing and ML |
| `pandas` | Local data manipulation and result export |
| `matplotlib` / `seaborn` | Visualisation |
| `holidays` | French public holiday calendar (feature engineering) |
| `scikit-learn` | Supplementary utilities |

> **Note:** No local installation is required. PySpark is installed in the first notebook cell via `!pip install -q pyspark`.

---

## Datasets

| Dataset | Training rows | Holdout rows | Columns |
|---|---|---|---|
| `products` | 4 | — | 3 |
| `orders` | 28,991 | — | 8 |
| `order_items` | 35,626 | — | 7 |
| `website_sessions` | 434,008 | 38,861 | 10 |
| `website_pageviews` | 1,052,523 | 103,278 | 4 |

Datasets are stored in Parquet format and loaded via Spark. They are **not** included in this repository due to size — obtain them from the course MyCourses portal.

---

## Key Results

### Binary Model — Purchase Prediction

Five classifiers were trained and evaluated using AUC-PR, AUC-ROC, and Accuracy on a chronological train / validation / test split:

| Model | AUC-PR (Test) | AUC-ROC (Test) | Accuracy (Test) |
|---|---|---|---|
| **GBT Classifier** ✓ | **0.1574** | 0.7774 | 92.26% |
| Random Forest | 0.1629 | 0.7807 | 92.26% |
| Linear SVM | 0.1613 | 0.7815 | 92.26% |
| Logistic Regression | 0.1603 | 0.7786 | 92.26% |
| Decision Tree | 0.1517 | 0.7857 | 92.26% |

The **GBT Classifier** (maxDepth=5, maxIter=20, stepSize=0.1) was selected as the best model based on validation AUC-PR, the most informative metric for this class-imbalanced problem (~5.9% positive rate).

### Multi-Class Model — Product Prediction (Bonus)

| Model | Validation Accuracy | Validation F1 |
|---|---|---|
| **Random Forest** ✓ | **98.02%** | **0.9720** |
| Logistic Regression | 98.02% | 0.9719 |

### Business Strategies

Two data-driven strategies were proposed based on customer profiling of the predicted buyers:

- **Strategy 1 — "The Green Scrollers" (EcoShell):** Eco-conscious, mobile-first users with a 64.66% repeat visit rate. Recommended action: Instagram/TikTok retargeting + 1-Click mobile checkout on the EcoShell landing page.
- **Strategy 2 — "The Intentful Professionals" (TechFortress/CorePack):** Brand-search-driven, desktop-dominant users with a 9.79% conversion rate. Recommended action: 100% brand search impression share protection + 3D technical diagrams on product pages.

---

## About

This project was submitted as part of the **Big Data** course at **IESEG School of Management**, Academic Year 2025–2026.
