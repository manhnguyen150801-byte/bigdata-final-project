# Slides — `Presentation.pdf`

This folder contains the official project presentation submitted as part of the Big Data course deliverables for Group BD26.

---

## File

| File | Format | Description |
|---|---|---|
| `Presentation.pdf` | PDF | Full project slide deck |

---

## Presentation Structure

The deck is organised into three sections as required by the assignment brief, targeting different audiences within Vauban 50.

### Section 1 — Executive Summary (max. 2 slides)

Targeted at **senior management**.

Covers the overall project setup, the highest-level conclusions, and the top proposed actions. Written to be skimmable and decision-ready — no technical jargon.

### Section 2 — Business Section (max. 4 slides)

Targeted at the **business development team** (Clémence's team).

Covers:
- **Conversion funnel analysis** — Where users drop off between landing and purchase, with stage-by-stage counts and conversion percentages
- **Financial & production performance** — Revenue, costs, and net profit broken down by product (CorePack, TechFortress, AirLite, EcoShell)
- **Marketing channel analysis** — Which UTM sources and campaigns drive the most traffic and which are the most cost-efficient (using CPC data provided by V-50)
- **Landing page experiment results** — Performance comparison of `/home` vs `/lander-1` through `/lander-5` on conversion rate, with a recommendation on which lander to scale
- **Two proposed business strategies**, each grounded in the customer profiling data:
  - *Strategy 1 — "The Eco-Mobile Retargeting Surge"*: Instagram/TikTok ads + 1-Click mobile checkout for EcoShell's high-repeat, mobile-first buyers (64.66% repeat rate, 48.09% mobile share)
  - *Strategy 2 — "The Search-to-Success Premium Funnel"*: 100% brand search impression share + technical product page enhancements for desktop-dominant, brand-loyal TechFortress/CorePack buyers (9.79% conversion rate)

### Section 3 — Technical Section (max. 4 slides)

Targeted at the **data science team** (Santi's team).

Covers:
- **Data pipeline** — Overview of the five source tables, data cleaning steps, and data leakage prevention (removal of `/cart`, `/shipping`, `/billing` pageviews)
- **Feature engineering** — The 30+ engineered features, grouped by category (temporal, behavioural, product-level, historical, channel)
- **Model selection** — Comparison table of all five binary classifiers (Logistic Regression, Random Forest, GBT, Decision Tree, Linear SVM) with AUC-PR, AUC-ROC, and Accuracy on test set
- **GBT hyperparameter tuning** — Grid search results across 12 combinations, with final optimal parameters
- **Multi-class extension** — Random Forest product classifier achieving 98.02% validation accuracy

### Appendix (max. 5 slides)

Additional technical detail including feature importance charts, correlation matrix, threshold-tuning analysis, and persona visualisations.

---

## Key Visual Takeaways

- The overall website-to-purchase conversion rate is below 10% — the funnel leaks heavily at the product discovery stage
- EcoShell buyers are the most deliberate (highest repeat visit rate) and most mobile-dependent, requiring a mobile-first engagement approach
- TechFortress and CorePack buyers arrive through direct brand search and respond to technical product information
- GBT was chosen as the binary model due to its best validation AUC-PR score in an imbalanced dataset context
