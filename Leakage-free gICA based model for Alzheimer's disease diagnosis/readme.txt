# Alzheimer’s Disease Classification Using Leakage‑Free gICA–SVM on Resting‑State fMRI

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)  
**Status:** Complete · **Journal:** Targeted to *Brain Imaging and Behavior*

---

## Overview

This repository provides a **rigorously validated, leakage‑free machine learning pipeline** for the binary classification of Alzheimer’s disease (AD) versus cognitively normal (CN) controls using resting‑state functional MRI (rs‑fMRI). The framework combines:

- **Group Independent Component Analysis (gICA)** performed strictly within each cross‑validation fold to prevent data leakage  
- **Multi‑measure functional connectivity** (static, dynamic mean/std, and partial correlation)  
- **Linear Support Vector Machine (SVM)** with Bayesian hyperparameter optimization (Optuna)  
- **Nested cross‑validation** for unbiased performance estimates  

The pipeline achieves **79.0% accuracy, 0.851 AUC, and 0.756 F1‑score** on 138 participants from the ADNI dataset, demonstrating robust discrimination while remaining fully interpretable—a critical advantage over “black‑box” deep learning approaches.

---

## Key Features

- **Leakage‑free design:** gICA decomposition is computed exclusively on training subjects in each outer fold; test subjects are projected via dual regression.  
- **Rich connectivity features:** Static functional connectivity, dynamic connectivity (sliding‑window mean and standard deviation), and regularised partial correlation.  
- **Fully reproducible preprocessing:** fMRIPrep + aCompCor denoising + MNI spatial normalisation.  
- **Nested cross‑validation:** Outer 5‑fold CV for evaluation; inner 5‑fold CV for Optuna‑driven hyperparameter tuning (Bayesian optimisation, 50 trials).  
- **Interpretable model:** Linear SVM weights allow direct mapping of discriminative brain connections to resting‑state networks.  
- **Publication‑ready outputs:** ROC curves, confusion matrices, comparison tables, and a pipeline flowchart (300 dpi TIF).

---

## Pipeline at a Glance

1. **Data ingestion** – ADNI rs‑fMRI (138 subjects, 2 mm MNI space, TR=3 s).  
2. **Preprocessing** – fMRIPrep (motion & distortion correction) → nuisance regression (aCompCor + motion parameters) → spatial smoothing (6 mm FWHM).  
3. **Outer CV loop** – 5‑fold stratified split at subject level.  
4. **Feature extraction (per fold):**  
   - **Group ICA** (CanICA, 40 components) *on training set only*  
   - **Dual regression** → subject‑specific time series  
   - **Static FC** – full‑length Pearson correlation  
   - **Dynamic FC** – sliding window (60 s) → mean & std across windows  
   - **Partial correlation** – Ledoit‑Wolf regularised  
   - Concatenation → **3 120 features** per subject  
5. **Classification (per fold):**  
   - Z‑score scaling (fit on training)  
   - Inner 5‑fold CV → Optuna tunes `C` for linear SVM  
   - Train final SVM → predict test fold  
6. **Aggregation** – Accuracy, AUC, F1‑score (mean ± SD across outer folds).

---

## Installation

### Requirements

- Python 3.10+  
- Key packages: `nilearn`, `scikit‑learn`, `optuna`, `nibabel`, `pandas`, `numpy`, `matplotlib`, `seaborn`, `tqdm`

### Setup

```bash
git clone https://github.com/yourusername/ad-classification-rsfmri.git
cd ad-classification-rsfmri
pip install -r requirements.txt