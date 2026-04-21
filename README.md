# ML Assessment — Mitali Kalburgi

## 📌 Project Overview

This repository contains the complete submission for a Machine Learning assessment covering three practical tasks — healthcare classification, customer segmentation, and retail sales forecasting — alongside a written business case analysis.

---

## ✅ Tasks Implemented

**Part A — Python Coding**

**Q1 — Healthcare Classification**
Predicts heart disease presence from clinical patient data. Covers missing value imputation, feature scaling, and a comparison of Decision Tree, Random Forest, and Gradient Boosting classifiers.

**Q2 — Customer Segmentation**
Groups retail customers into distinct behavioural segments using K-Means clustering. Optimal K is selected using the Elbow Method, and results are visualised using PCA reduced to two components.

**Q3 — Retail Sales Forecasting**
Builds a regression pipeline to predict items sold per store. Includes date feature engineering, a temporal train-test split, and a full scikit-learn Pipeline with preprocessing and model evaluation for Linear Regression and Random Forest.

**Part B — Business Case Analysis**

Written analysis covering problem formulation, EDA strategy, model evaluation design, and an end-to-end deployment plan for a promotion effectiveness problem across 50 retail stores.

---

## 🛠 Technologies Used
- Python
- Jupyter Notebook
- Pandas — data manipulation and analysis
- NumPy — numerical computations
- Matplotlib — plotting and visualisation
- Seaborn — statistical visualisation
- Scikit-learn — machine learning models and pipelines

---

## ▶️ How to Run

1. Place all CSV files in the `data/` directory.
2. Run each notebook in order.
3. Refer to `part_b/business_analysis.md` for the written analysis.

---

## 📝 Notes

- All notebooks are well-commented for clarity
- Markdown cells are included throughout to explain methodology and interpret results
- The temporal split in Q3 is intentional — a random split is not appropriate for time-ordered data

---

## 📁 Repository Structure

- `part_a/` — hands-on machine learning notebooks
  - `q1_classification.ipynb` — healthcare classification
  - `q2_unsupervised.ipynb` — customer segmentation
  - `q3_feature_engineering.ipynb` — retail sales forecasting
- `part_b/` — written business case analysis
- `data/` — dataset CSV files for all three tasks

