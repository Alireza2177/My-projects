# Leakage-Aware rs-fMRI Classification for Alzheimer's Disease

This repository contains a Jupyter notebook for evaluating Alzheimer's disease classification from resting-state fMRI using a leakage-aware machine learning pipeline. The main experiment compares two Group ICA based workflows:

1. A leakage-aware workflow, where Group ICA is fitted only on the training subjects inside each outer cross-validation fold.
2. A deliberately contaminated full-cohort ICA comparator, where Group ICA is fitted once on all subjects before cross-validation.

The purpose of the project is not to present the contaminated pipeline as a valid model. It is included to demonstrate how representation-learning leakage can change apparent classification performance in neuroimaging studies.

## Project Motivation

Machine learning pipelines for neuroimaging can accidentally leak information from test subjects into training, especially when unsupervised feature extraction is performed before cross-validation. This project focuses on that issue in the context of Alzheimer's disease diagnosis using resting-state functional MRI.

The notebook implements a checkpointed end-to-end pipeline for:

- loading AD/CN labels,
- scanning fMRIPrep-style subject folders,
- cleaning BOLD images,
- building a common analysis mask,
- fitting Group ICA models,
- extracting functional connectivity features,
- training a linear SVM classifier,
- comparing leakage-aware and contaminated ICA workflows,
- saving metrics, predictions, reports, and publication-style figures.

## Repository Contents

```text
.
+-- Leakage-aware model.ipynb   # Main notebook
`-- README.md                   # Project documentation
```

The dataset is not included because ADNI data are access-controlled and cannot be redistributed.

## Expected Data Layout

The notebook expects a local project directory containing a label CSV and fMRIPrep-style subject folders:

```text
ROOT_DIR/
+-- full dataset.csv
+-- sub-XXXXXXXX/
|   `-- ses-*/
|       `-- func/
|           +-- *_task-rest_space-MNI152NLin2009cAsym_res-2_desc-preproc_bold.nii.gz
|           +-- *_task-rest_space-MNI152NLin2009cAsym_res-2_desc-brain_mask.nii.gz
|           `-- *_task-rest_desc-confounds_timeseries.tsv
`-- ...
```

The label CSV should contain at least:

| Column | Description |
| --- | --- |
| `Subject ID` | Subject folder identifier, for example `sub-002S6007` |
| `label` | Diagnostic label; expected values are `CN` and `AD` |

Optional columns used in manifests include `Sex`, `Scan_Age`, `Phase`, `fMRI_Description`, and `fMRI_Imaging_Protocol`.

## Main Configuration

Before running the notebook, update the path constants near the top of the main code cell:

```python
ROOT_DIR = Path(r"H:\Master's Thesis\pipeline")
LABEL_CSV = ROOT_DIR / "full dataset.csv"
OUT_DIR = ROOT_DIR / "analysis_outputs_checkpointed_relaxed"
```

Important modeling parameters in the current notebook are:

| Parameter | Value |
| --- | --- |
| Diagnostic task | AD vs CN |
| Session selection rule | Earliest available session |
| Outer CV | 5-fold stratified CV |
| Inner CV | 5-fold stratified CV |
| Hyperparameter tuning | Optuna, 50 trials per outer fold |
| ICA components | 40 |
| Connectivity edges per matrix | 780 |
| Feature blocks | Static FC, dynamic FC mean, dynamic FC std, partial FC |
| Total features per subject | 3120 |
| Dynamic FC window | 20 volumes |
| Dynamic FC step | 10 volumes |
| Classifier | Linear SVM with balanced class weights |
| Scaling | `StandardScaler` inside the scikit-learn pipeline |
| Positive class | AD |

## Method Summary

### Preprocessing and Cleaning

The notebook assumes that fMRI preprocessing has already been performed externally, for example with fMRIPrep. It then applies post-fMRIPrep cleaning with Nilearn.

The preferred cleaning strategy uses:

- motion parameters,
- white matter and CSF signals,
- cosine regressors,
- non-steady-state outlier regressors when available,
- band-pass filtering from 0.01 Hz to 0.10 Hz,
- detrending,
- `zscore_sample` standardization,
- subject-specific functional masks.

The notebook also includes fallback cleaning strategies so long runs do not fail completely when subject-level mask or affine issues occur. The exact strategy used for each subject is saved in the generated manifest files.

### Feature Extraction

For each fitted ICA model, the notebook extracts ICA time series and computes four feature blocks:

- static Pearson functional connectivity,
- mean dynamic functional connectivity,
- standard deviation of dynamic functional connectivity,
- partial correlation connectivity using Ledoit-Wolf shrinkage.

Only the upper-triangular connectivity edges are retained.

### Leakage-Aware Model

In the leakage-aware workflow:

1. The outer cross-validation split is created.
2. Group ICA is fitted only on the training subjects of that fold.
3. Training and test subjects are transformed using the training-derived ICA maps.
4. Connectivity features are extracted.
5. SVM `C` is tuned using inner cross-validation on the training fold only.
6. The final fold model is evaluated on the held-out test fold.

This workflow prevents test subjects from contributing to ICA spatial map estimation.

### Full-Cohort ICA Comparator

In the contaminated comparator:

1. Group ICA is fitted once using the full cohort before cross-validation.
2. Features are extracted for all subjects from that full-cohort ICA model.
3. The SVM is still evaluated with cross-validation.

This comparator is intentionally leaky because test subjects contribute to the unsupervised ICA representation. It should not be interpreted as a valid estimate of generalization.

## Current Notebook Results

The executed notebook output reports 137 valid subjects:

| Group | N |
| --- | ---: |
| CN | 69 |
| AD | 68 |

Summary metrics from pooled out-of-fold predictions:

| Model | Accuracy | Balanced Accuracy | F1 | AUC |
| --- | ---: | ---: | ---: | ---: |
| Leakage-aware ICA | 0.825 | 0.824 | 0.815 | 0.891 |
| Full-cohort ICA comparator | 0.803 | 0.803 | 0.791 | 0.876 |

Confusion matrices use label order `[CN=0, AD=1]` and format `[[TN, FP], [FN, TP]]`.

| Model | Confusion Matrix |
| --- | --- |
| Leakage-aware ICA | `[[60, 9], [15, 53]]` |
| Full-cohort ICA comparator | `[[59, 10], [17, 51]]` |

These results reflect the run stored in the notebook outputs. Reruns may differ slightly depending on software versions, data availability, and random seeds.

## Generated Outputs

The notebook writes intermediate and final outputs under:

```text
analysis_outputs_checkpointed_relaxed/
```

Important output files include:

| File | Purpose |
| --- | --- |
| `manifests/subject_file_manifest_all.csv` | All scanned subjects and exclusion reasons |
| `manifests/subject_file_manifest_valid.csv` | Subjects with required BOLD, mask, and confounds files |
| `manifests/mask_resampling_manifest.csv` | Mask resampling status |
| `manifests/denoising_checkpoint.csv` | Per-subject denoising checkpoint |
| `manifests/cleaned_bold_manifest_all.csv` | Cleaning strategy and status for all subjects |
| `manifests/cleaned_bold_manifest_valid.csv` | Subjects with usable common-grid cleaned BOLD images |
| `manifests/outer_fold_assignments.csv` | Cross-validation fold assignment |
| `results/fold_metrics.csv` | Fold-level model metrics |
| `results/summary_metrics.csv` | Pooled and bootstrapped summary metrics |
| `results/paired_differences.csv` | Paired differences between workflows |
| `results/out_of_fold_predictions.csv` | Subject-level out-of-fold predictions and scores |
| `results/results_arrays.npz` | Saved NumPy arrays for labels, predictions, scores, and confusion matrices |
| `results/paper_details.json` | Machine-readable experiment configuration |
| `results/paper_ready_report.txt` | Text report for manuscript drafting |

The visualization cell also creates:

- `Figure_2_primary_leakage_aware_performance`
- `Figure_3_paired_comparator_differences`
- `Supplementary_Figure_S1_outer_fold_metrics`
- `Supplementary_Figure_S2_model_comparison`

## Installation

Create a Python environment and install the required packages:

```bash
pip install numpy pandas nibabel nilearn scikit-learn optuna matplotlib seaborn jupyter
```

Recommended Python version: Python 3.9 or newer.

## How to Run

1. Clone the repository.
2. Place or mount your local fMRIPrep-derived data in the expected structure.
3. Open `Leakage-aware model.ipynb`.
4. Update `ROOT_DIR`, `LABEL_CSV`, and `OUT_DIR`.
5. Run the main pipeline cell.
6. Check the generated manifest files before interpreting model results.
7. Run the visualization cell after `summary_metrics.csv`, `paired_differences.csv`, `fold_metrics.csv`, and `out_of_fold_predictions.csv` have been created.

## Reproducibility Notes

The notebook uses `RANDOM_STATE = 42` for cross-validation, ICA, and Optuna sampling where applicable. However, exact reproducibility can still depend on:

- Nilearn and scikit-learn versions,
- operating system and BLAS backend,
- availability and ordering of subject files,
- fMRIPrep version and preprocessing settings,
- local CPU behavior in numerical routines.

For publication use, record the complete software environment and keep the generated `paper_details.json` file with the results.

## Limitations and Responsible Use

- This repository does not provide clinical diagnostic software.
- ADNI data are not included and must be obtained according to ADNI access policies.
- The full-cohort ICA comparator is intentionally contaminated and should not be reported as a valid generalization model.
- The checkpointed version uses fallback cleaning strategies. This improves robustness but should be described transparently in any manuscript or report.
- Potential dataset confounds, including acquisition phase, scanner/site effects, age, and sex imbalance, should be examined before making scientific claims.

## Suggested Citation

If this repository supports a manuscript or thesis, cite the related work describing the final validated pipeline. Until then, please cite this repository as an implementation of a leakage-aware rs-fMRI classification experiment for AD vs CN classification.
