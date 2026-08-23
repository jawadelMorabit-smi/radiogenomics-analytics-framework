[![Readme in French](https://img.shields.io/badge/docs-fran%C3%A7ais-blue)](README.fr.md)
[![Kaggle](https://img.shields.io/badge/dataset-RSNA--MICCAI-20BEFF?logo=kaggle&logoColor=white)]
![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)

# Radiogenomics Analytics Framework

**MRI → Radiomics → Genomic Prediction (MGMT Methylation)**

An end-to-end **radiogenomics pipeline** that predicts **MGMT promoter
methylation status** in glioblastoma from multi-modal MRI — non-invasively,
without biopsy. Built on the [RSNA-MICCAI Brain Tumor Radiogenomic
Classification](https://www.kaggle.com/competitions/rsna-miccai-digital-pathology) dataset.

> **What is radiogenomics?** An interdisciplinary field studying the statistical
> associations between quantitative imaging features (**radiomics**) and the
> genomic/molecular characteristics of tumors. MGMT promoter methylation is a key
> prognostic biomarker: methylated tumors respond better to temozolomide
> chemotherapy.

---

## Table of Contents

- [Pipeline](#pipeline)
- [Methodology](#methodology)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Running the Notebook](#running-the-notebook)
- [Outputs](#outputs)
- [Medical Disclaimer](#medical-disclaimer)

---

## Pipeline

The notebook (`radiogenomics-research.ipynb`) is organized into 10 sequential
sections:

| Section | Step | Description |
|---|---|---|
| A | Setup & Installation | Environment configuration (all libraries available on Kaggle). |
| B | Data Understanding | Clinical labels, class balance inspection, MRI modality overview. |
| C | Image Preprocessing | Robust pipeline for multi-modal MRI (FLAIR / T1 / T1ce / T2). |
| D | Tumor Region Approximation | Pseudo-ROI construction (real segmentation masks are unavailable). |
| E | Radiomics Feature Extraction | Three categories of quantitative imaging descriptors. |
| F | Feature Selection | High-dimensional space reduction + LASSO, consensus feature set. |
| G | **Radiogenomic Analysis** | Statistical association testing, multi-modality fusion analysis, biological plausibility of markers. |
| H | Predictive Modeling | Logistic Regression vs Random Forest, before/after feature selection, 5-fold stratified CV (no leakage). |
| I | Evaluation | ROC curves, confusion matrices, detailed metrics on the best model. |
| J | Interpretation | Feature importance & biological relevance dashboard. |

## Methodology

- **No data leakage**: all model comparisons use 5-fold stratified
  cross-validation.
- **Honest ROI handling**: real radiomics requires tumor segmentation; since
  masks are unavailable, a documented pseudo-ROI approximation is used instead.
- **Fusion analysis**: quantifies whether combining MRI sequences improves
  prediction over single sequences.
- **Interpretable models first**: Logistic Regression as baseline, Random Forest
  for non-linear interactions — with feature importance for biological review.

## Project Structure

```text
.
├── radiogenomics-research.ipynb   # Full pipeline notebook (sections A–J)
├── Jaouad_El_Morabit.pdf          # Project report (French)
├── requirements.txt               # Python dependencies
└── .gitignore
```

## Installation

Requirements: **Python ≥ 3.10** with pip.

```bash
git clone https://github.com/jawadelMorabit-smi/radiogenomics-analytics-framework.git
cd radiogenomics-analytics-framework
pip install -r requirements.txt
jupyter notebook radiogenomics-research.ipynb
```

Dependencies: NumPy, pandas, SciPy, scikit-learn, statsmodels, OpenCV,
scikit-image, PyDICOM, Matplotlib, seaborn.

## Running the Notebook

The pipeline was developed for the **Kaggle environment**, where the RSNA-MICCAI
dataset is mounted at `/kaggle/input`. Two options:

1. **Recommended — Kaggle:** open the notebook at
   [kaggle.com/code](https://www.kaggle.com/code), attach the competition
   dataset, and *Run All*. Free GPU/CPU quota is sufficient.
2. **Locally:** download the RSNA-MICCAI data from Kaggle and update `BASE_PATH`
   in Section B to point to your local copy.

> Note: this repository contains the notebook **source without pre-computed
> outputs** — run it once against the dataset to regenerate all tables and
> figures.

## Outputs

The final section saves 10 figures covering the whole analysis:
class distribution, MRI modalities, preprocessing steps, ROI approximation,
feature selection, radiogenomic association (volcano plot), modality fusion,
model comparison, evaluation (ROC/confusion matrix), and an interpretation
dashboard.

## Medical Disclaimer

This project is a **research/educational framework**. It is not a medical
device and must not be used for clinical decision-making.

---

*Author: Jaouad El Morabit — Master BIAM 2025-2026, Biomedical Imaging.*
