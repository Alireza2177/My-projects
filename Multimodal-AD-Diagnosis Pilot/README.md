# Multimodal Alzheimer's Disease Classification Pipeline

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Machine Learning](https://img.shields.io/badge/ML-Scikit--learn-orange.svg)](https://scikit-learn.org/)
[![Neuroimaging](https://img.shields.io/badge/Neuroimaging-Nilearn-green.svg)](https://nilearn.github.io/)

A comprehensive end-to-end machine learning pipeline for classifying Alzheimer's Disease stages using multimodal neuroimaging data (sMRI + fMRI) and demographic features.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Results](#results)
- [Technical Details](#technical-details)
- [License](#license)

## 🧠 Overview

This project implements a sophisticated machine learning pipeline for the classification of Alzheimer's Disease (AD), Mild Cognitive Impairment (MCI), and Cognitively Normal (CN) subjects using:

- **Structural MRI** (sMRI) - Cortical thickness and subcortical volumes from Freesurfer
- **Functional MRI** (fMRI) - Resting-state functional connectivity
- **Demographic data** - Age and sex

The pipeline demonstrates advanced neuroimaging data processing, feature engineering, multimodal data integration, and model interpretation techniques.

## ✨ Features

- **🔬 Multi-modal Integration**: Combines structural, functional, and demographic data
- **📊 Advanced Feature Extraction**: Freesurfer statistical parsing + AAL atlas-based functional connectivity
- **🤖 Machine Learning Pipeline**: Automated preprocessing, PCA, multi-class classification
- **📈 Model Interpretation**: SHAP-based explainable AI
- **🎯 Comprehensive Evaluation**: Multiple performance metrics and visualization

## 🚀 Installation

### Prerequisites

- Python 3.8 or higher
- 4GB+ RAM recommended

### Quick Install

1. **Clone the repository**:
```bash
git clone https://github.com/Alireza2177/Multimodal-AD-Classification.git

cd Multimodal-AD-Classification
