# Causal Inference: Impact of HbA1c Testing on Hospital Readmission

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![CatBoost](https://img.shields.io/badge/Model-CatBoost-green)
![Status](https://img.shields.io/badge/Status-Complete-success)

## 📌 Project Overview
This project evaluates the causal effect of **HbA1c testing** on **hospital readmission rates** for diabetic patients.

Unlike traditional observational studies, this project utilizes a **semi-synthetic experimental design**. While the patient covariates and treatment assignments are real (sourced from the UCI Diabetes dataset), the outcomes are simulated using a known non-linear function. This provides a "Ground Truth" benchmark, allowing us to rigorously evaluate the performance of various Causal Meta-Learners (S-Learner, T-Learner, X-Learner, and DoubleML).

### 🎯 Key Objectives
1.  **Estimate the Average Treatment Effect (ATE):** Does testing reduce readmission risk globally?
2.  **Assess Heterogeneity (CATE):** Which patient subgroups benefit most from the test?
3.  **Benchmarking:** Compare "Black Box" ML approaches against traditional OLS regression using known ground truth.

---

## 📂 Data & Methodology

### Data Source
We utilize the **Diabetes 130-US Hospitals for Years 1999–2008 Data Set** (Strack et al., 2014).
* **Encounters:** ~100,000
* **Features:** Demographics, utilization history (inpatient/outpatient visits), and clinical codes.

### Semi-Synthetic Design
To validate model performance, we constructed a controlled environment:
* **Treatment (T):** Real. 1 if HbA1c measured, 0 otherwise (18% treated).
* **Confounders (X):** Real. Includes Age, Gender, Race, Medical Specialty, and prior Emergency visits.
* **Outcome (Y):** Simulated. A continuous "likelihood of avoiding readmission" score generated with non-linear interactions and Gaussian noise.

### Models Evaluated
All ML models utilized **CatBoost** as the base learner to handle categorical features natively.
1.  **OLS Regression:** Baseline with manual interaction terms.
2.  **S-Learner:** Single learner with Treatment as a feature.
3.  **T-Learner:** Two separate learners (Control vs. Treated).
4.  **X-Learner:** Two-stage meta-learner optimized for imbalance.
5.  **Double Machine Learning (DoubleML):** Two-stage nuisance parameter estimation.

---

## 📊 Key Findings

We discovered a distinct trade-off between **global reporting accuracy** and **individual targeting precision**.

| Metric | S-Learner | Double ML | OLS (Baseline) |
| :--- | :--- | :--- | :--- |
| **Primary Use Case** | **Targeting (Operations)** | **Reporting (Strategy)** | Comparison |
| **Estimated ATE** | 1.2% | **1.4%** | 1.6% |
| **True ATE** | 1.5% | 1.5% | 1.5% |
| **Targeting Accuracy (Rank Corr)** | **0.84** (High) | 0.77 | 0.23 (Low) |
| **Variance Explained ($R^2$)** | **0.65** | 0.47 | 0.04 |

### 💡 Executive Summary
1.  **The Intervention Works:** The treatment drives a statistically significant positive lift of approximately **1.5%** (95% CI: `[0.9%, 1.8%]`).
2.  **S-Learner is Best for Targeting:** The S-Learner successfully captured complex non-linear interactions, achieving a **0.84 Rank Correlation** with the ground truth.
3.  **High-Value Opportunity:** While the average effect is modest, targeting the top 10% of responders (identified by the S-Learner) yields a **3.3% lift**—more than double the average.

---

## 🛠️ Installation & Usage

### Prerequisites
* Python 3.8+
* Jupyter Notebook

### Installation
Clone the repo and install dependencies:
```bash
git clone [https://github.com/yourusername/causal-inference-diabetes.git](https://github.com/yourusername/causal-inference-diabetes.git)
cd causal-inference-diabetes
pip install pandas numpy catboost joblib statsmodels matplotlib seaborn