# Notebook — `BigData_Notebook.ipynb`

This notebook contains the complete end-to-end PySpark pipeline for the Vauban 50 purchase prediction project. It is structured in six clearly labelled steps, from raw data ingestion through to the final holdout predictions and business strategy recommendations.

---

## Environment

| Setting | Value |
|---|---|
| Runtime | Google Colab (Python 3) |
| Distributed Engine | PySpark (local mode `local[*]`) |
| Spark Version | Installed via `pip install pyspark` |
| Data Storage | Google Drive (Parquet files) |

---

## Step-by-Step Walkthrough

### Step 1 — Data Cleaning

Loads all five source tables from Google Drive using `spark.read.parquet()`:

- `order_items`, `orders`, `products`, `website_pageviews`, `website_sessions`
- Also loads the two holdout tables: `website_pageviews_holdout`, `website_sessions_holdout`

Cleaning operations per table include:
- Casting `created_at` strings to proper `TimestampType` using `to_timestamp()` / `try_to_timestamp()`
- Null-value audits via column-wise `count(when(col.isNull()))`
- **Data leakage prevention:** Rows where `pageview_url` is `/cart`, `/shipping`, or `/billing` are removed from the pageviews table, since these URLs are proximal indicators of a completed purchase and would cause target leakage if retained as features

### Step 1.1 — Exploratory Data Analysis

Explores the raw data before modelling:

- **Financial analysis by product** — Revenue, Total Costs, and Net Profit aggregated per product using a distributed join across `order_items`, `products`, and computed shipping cost shares (€10 split equally across items per order)
- **Conversion funnel** — Tracks user drop-off across six stages: Landing → Catalog → Product Page → Add to Cart → Checkout → Purchase
- Both analyses are visualised using `matplotlib` and `seaborn`

### Step 2 — Target Variable Creation

Constructs the base modelling table by joining `website_sessions` with pageview behaviour and orders:

- **Base table** columns: `website_session_id`, `user_id`, `is_repeat_session`, `session_start_time`, `utm_source`, `utm_campaign`, `device_type`, `total_pages_viewed`, `landing_page`, `purchased_product_name`, `order_value`
- **Target variable:** `is_ordered = 1` if the session is linked to an `order_id`, else `0`
- Class distribution: approximately 5.9% positive (ordered), 94.1% negative

### Step 3 — Feature Engineering

Over 30 features are engineered, grouped into the following categories:

**Temporal features**
- `hour` — hour of session start (0–23)
- `day_of_week` — day of week (1=Sun … 7=Sat)
- `month` — month of year (1–12)
- `is_night` — binary flag for sessions starting between 23:00 and 06:00
- `is_holiday` — binary flag using the French public holiday calendar (`holidays` library)
- `is_weekend` — binary flag for Saturday/Sunday sessions

**Behavioural & engagement features**
- `duration_seconds` — total session length (max − min pageview timestamp)
- `avg_time_per_page`, `pages_per_minute`, `reading_depth`, `commitment_score`
- `page_revisit_count` — number of pages viewed more than once
- `decision_latency_seconds` — time between first product view and session end
- `decision_latency_bucket` — binned version of the above
- `cart_building_pattern` — "iterative" vs "direct" browsing
- `conversion_pressure_score` — pages viewed divided by time spent

**Product-level features**
- `has_viewed_ecoshell`, `has_viewed_techfortress`, `has_viewed_corepack`, `has_viewed_airlite` — binary flags
- `time_on_[product]` — total seconds spent on each product page
- `time_share_on_product`, `avg_price_viewed`, `time_on_checkout_process`
- `products_viewed_per_minute`, `time_on_cart_isolated`

**Historical / user-level features**
- `past_order_count`, `past_total_spent`, `past_session_count`
- `total_views_of_product_last_[30/60/90]_days` — rolling product view counts
- `total_any_product_views_[30/60/90]d` — aggregate rolling views
- `days_since_last_view_product`, `days_since_any_product_view`

**Channel & marketing features**
- `is_paid_traffic`, `is_brand_search`, `is_nonbrand_search`, `is_social_traffic`, `is_pilot_campaign`
- `channel_efficiency_score`

**Derived composite features**
- `is_impulse_buyer`, `is_viewing_premium_package`, `is_viewing_entry_level`
- `product_view_ratio`, `time_spent_ratio`, `total_product_view_ratio`, `total_time_spent_ratio`
- `relative_interest_score`, `product_engagement_index` (40% views + 60% time)
- `action_depth`, `price_deviation`, `market_price_comparison`

### Step 4 — Feature Selection & Class Balancing

- **Class balancing:** Undersampling the majority class (no-order) to produce a balanced training set. The imbalance ratio was ~16:1 (negative to positive).
- **Correlation matrix:** High-correlation feature pairs identified and redundant features removed
- **Feature importance:** A native Spark `RandomForestClassifier` with 100 trees is fitted on the balanced dataset to rank features by importance (replaces slower Scikit-learn Mutual Information approach)

### Step 5 — Binary Model Training & Selection

Five models are trained and evaluated on a **chronological** train/validation/test split:

| Split | Period |
|---|---|
| Train | March 2022 – March 2024 |
| Validation | March 2024 – August 2024 |
| Test | August 2024 – January 2025 |

Models evaluated: Logistic Regression, Random Forest, GBT Classifier, Decision Tree, Linear SVM.

Evaluation metrics: **AUC-PR** (primary, handles class imbalance), AUC-ROC, and Accuracy.

**Winner: GBT Classifier** — hyperparameter-tuned across 12 combinations (maxDepth, maxIter, stepSize). Optimal params: `maxDepth=5`, `maxIter=20`, `stepSize=0.1`.

### Step 6 — Holdout Prediction & Multi-Class Extension

- The same feature engineering pipeline is applied to the holdout session/pageview data
- The final GBT model generates binary predictions (`pred_score`) for each `user_id`
- A separate **Random Forest multi-class model** (Accuracy: 98.02%, F1: 0.9720) predicts which product will be purchased (`pred_multi_score_1` to `pred_multi_score_4`)
- Results are written to `prediction_result.csv`

### Customer Profiling & Business Strategy

Holdout predictions are joined with session metadata to profile buyer personas by product:

| Persona | Product | Repeat Rate | Mobile Usage |
|---|---|---|---|
| The Green Scrollers | EcoShell | 64.66% | 48.09% |
| The Intentful Professionals | TechFortress / CorePack | 32–36% | 22–24% |

Two actionable business strategies are proposed, each backed by the profiling data.

---

## Key Notebook File

| File | Description |
|---|---|
| `BigData_Notebook.ipynb` | Complete PySpark notebook (366 cells) |
