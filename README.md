# Healthcare Analytics — Full Biostatistics & Machine Learning Project

A rigorous end-to-end analysis of a synthetic healthcare dataset, combining
data cleaning, exploratory analysis, statistical inference, survival analysis,
predictive modelling, and patient segmentation.

---

## Project Overview

This project demonstrates a complete healthcare analytics pipeline applied to a
synthetic dataset of 55,500 patient records. The analysis is structured across
six phases, each building on the last — from raw data ingestion through to
machine learning and unsupervised clustering.

The dataset was sourced from Kaggle and generated using Python's Faker library
to simulate real-world healthcare records. It contains patient demographics,
clinical variables, financial data, and operational metrics.

**Primary classification target:** `Test Results` (Normal / Abnormal / Inconclusive)

---

## Dataset

| Attribute | Detail |
|-----------|--------|
| Source | Kaggle — Synthetic Healthcare Dataset |
| Records | 55,500 (54,966 after deduplication) |
| Features | 15 columns |
| Target | Test Results (3-class) |
| Date Range | 2019 – 2024 |

**Columns:** Name, Age, Gender, Blood Type, Medical Condition, Date of Admission,
Doctor, Hospital, Insurance Provider, Billing Amount, Room Number, Admission Type,
Discharge Date, Medication, Test Results

---

## Project Structure

```
healthcare-dashboard/
├── data/
│   ├── raw/                        # Original unmodified CSV
│   └── cleaned/                    # Cleaned dataset with engineered features
├── notebooks/
│   └── healthcare_eda.ipynb        # Full analysis notebook (6 phases)
├── requirements.txt                # Python dependencies
└── README.md
```

---

## Tech Stack

| Category | Libraries |
|----------|-----------|
| Data Manipulation | `pandas`, `numpy` |
| Statistical Analysis | `scipy` (chi-square, Kruskal-Wallis, odds ratios) |
| Machine Learning | `scikit-learn`, `xgboost` |
| Survival Analysis | `lifelines` (Kaplan-Meier, Cox PH) |
| Explainability | `shap` |
| Dimensionality Reduction | `umap-learn` |
| Visualisation | `plotly`, `seaborn`, `matplotlib` |
| Notebook | `jupyter` |

---

## Setup & Installation

### 1. Clone the repository

```bash
git clone https://github.com/bensoncyril123/healthcare-dashboard.git
cd healthcare-dashboard
```

### 2. Create a conda environment

```bash
conda create -n healthcare python=3.12 -y
conda activate healthcare
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add the raw dataset

Download `healthcare_dataset.csv` from Kaggle and place it in:

```
data/raw/healthcare_dataset.csv
```

### 5. Launch the notebook

```bash
jupyter notebook notebooks/healthcare_eda.ipynb
```

---

## Analysis Phases

### Phase 1 — Data Cleaning & Feature Engineering
- Removed 534 duplicate records
- Parsed date columns and computed **Length of Stay** (LOS) as a derived feature
- Corrected 108 negative billing amounts (sign errors) using absolute value
- Standardised column names to snake_case
- Saved cleaned dataset to `data/cleaned/`

### Phase 2 — Exploratory Data Analysis
- **Demographics:** Age (uniform 13–89), gender (50/50 split), blood type (8 types equally distributed)
- **Clinical:** 6 conditions and 5 medications, all evenly distributed; Test Results perfectly balanced (33.3% each)
- **Correlation:** Near-zero correlations across all numeric pairs; Cramér's V confirms categorical independence
- **Financial:** Billing uniformly distributed $0–$53k; no meaningful variation across conditions or insurers
- **Operational:** LOS uniform 1–30 days; hospital and doctor workloads evenly spread

### Phase 3 — Statistical Inference
- **Chi-square tests:** No categorical variable significantly associated with Test Results (all p > 0.05)
- **Kruskal-Wallis:** Billing amount marginally differs by medical condition (p = 0.048) — borderline result
- **Odds Ratios:** Hypertension shows OR = 0.947 (p < 0.05) — statistically significant but clinically negligible

### Phase 4 — Survival Analysis
- **Kaplan-Meier curves:** All admission type curves perfectly overlapping — no difference in LOS by admission type
- **Cox Proportional Hazards:** Concordance = 0.506 (near-random); no variable meaningfully predicts LOS

### Phase 5 — Predictive Modelling (Multi-class Classification)

| Model | Macro F1 | ROC-AUC (OvR) |
|-------|----------|---------------|
| Logistic Regression | 0.330 | 0.496 |
| XGBoost | 0.370 | 0.545 |
| **Random Forest** | **0.430** | **0.629** |

Random Forest was the best performer, achieving F1 = 0.43 vs a random baseline of 0.33.
SHAP analysis revealed room_number as the top feature — an administrative variable with
no clinical meaning, confirming the model is learning noise rather than signal.

### Phase 6 — Patient Segmentation
- **Elbow method:** No clear bend in the inertia curve — no natural cluster structure
- **K-Means (k=4):** All four clusters nearly identical on every numeric variable (age ≈ 51.5, billing ≈ $25,500, LOS ≈ 15.5 days)
- **UMAP:** All four clusters heavily intermixed — no distinct patient subgroups

---

## Key Findings

The central finding across all six phases is consistent: **this synthetic dataset
contains no embedded real-world clinical relationships.** Variables were generated
independently using Faker, resulting in:

- No association between any clinical variable and Test Results
- No predictor of Length of Stay
- No natural patient subgroups
- Billing independent of condition, LOS, and admission type

This is the expected and appropriate outcome for a synthetic dataset, and the
rigorous analytical pipeline correctly identifies the absence of signal at every stage.

### What real-world data would show differently:
- Strong associations between medical condition, medication, and test outcomes
- LOS correlated with condition severity and admission type
- Billing increasing with LOS and condition complexity
- Distinct patient clusters (e.g. elderly chronic disease, young acute, elective surgical)
- Predictive models achieving F1 > 0.70 on test results

---

## Limitations

1. **Synthetic data** — no real clinical relationships; findings cannot be generalised
2. **No censoring in survival analysis** — all patients discharged, limiting KM/Cox interpretation
3. **No temporal features** — admission year/seasonality not explored
4. **Single dataset** — no external validation cohort

---

## Author

**Cyril Benson Nana Boakye**
Data Scientist & Biostatistician

---

## Acknowledgements

Dataset: [Synthetic Healthcare Dataset](https://www.kaggle.com/datasets/prasad22/healthcare-dataset) by Prasad on Kaggle.
Generated using Python's Faker library. No real patient data is used in this project.
