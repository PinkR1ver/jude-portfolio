---
title: Radiogenomic Analysis on Glioblastoma (Lesion Segmentation and Subtype Classification)
summary: "From routine MRI to molecular insight: segment GBM lesions, distill lesion ROIs into radiomic fingerprints, and predict subtypes to bridge imaging with biology."
tags: [Medical Imaging, Segmentation, Radiomics, GBM, U-Net]
duration: 2021.09 - 2022.05
image_dir: /assets/img/project/radiogenomics-gbm
---

This project targets radiogenomics analysis for glioblastoma (GBM). The end-to-end pipeline includes:

1) Lesion segmentation on MRI using U‑Net variants to obtain precise ROIs (lesion Regions of Interest)  
2) Radiomics feature extraction on lesion ROIs (tumor regions) with feature engineering  
3) GBM subtype classification (e.g., MLP) with evaluation and visualization

### Objective & Contributions

- Objective: anticipate GBM molecular subtypes from routine MRI by linking precise lesion segmentation with radiomics‑based signatures.  
- Contributions: a robust end‑to‑end pipeline (segmentation → radiomics → subtype), careful lesion ROI definition that stabilizes radiomic features, and interpretability via mutual information between features and gene expression.

In clinic, MRI is routine while molecular assays can be costly or delayed. I asked a simple question: can we anticipate GBM molecular subtypes from what radiologists already see? I built an end‑to‑end pipeline that first teaches a network to delineate the tumor, then turns those ROIs into quantitative radiomic signatures for a lightweight classifier. Along the way, I iterated on data organization, stabilized training on imbalanced modalities, and added analyses that explain which features matter and why.

### Dataset

- Source: Ivy GAP / TCGA‑GBM cohorts, with MRI series T1, T2, FLAIR, and stacked inputs (“Stack”).  
- Structure: images/masks organized per patient and series; CSV metadata for slice locations and labels.  
- ROIs: manual masks are used to supervise lesion segmentation and to define regions for radiomics extraction.  
- Radiomics config: a YAML configuration drives feature extraction (e.g., intensity/texture/shape features).

### Methods

- Typical workflow of radiogenomic studies

  ![Typical workflow of radiogenomic studies]({{ page.image_dir | append: '/workflow-radiogenomic.png' | relative_url }})

  1) Image acquisition  
  2) Image processing, including noise/artifact reduction, intensity and/or orientation standardization, coregistration of the multiparametric MRI scans  
  3) ROI definition using manual annotation or automatic segmentation  
  4) Feature extraction based on human-engineered (conventional radiomics) or deep-learning approaches  
  5) Data analysis, involving machine/deep-learning methods for feature selection, classification, and cross-validation

- Segmentation (U‑Net family)  
  - Supervised learning with lesion masks; architectures based on U‑Net variants adapted for GBM MRI.  
  - Robust preprocessing and modality‑aware batching to handle sequence imbalance.  
  - Outputs include training dynamics and qualitative visualizations for each sequence.

- Radiomics Feature Extraction  
  - Extract quantitative features from lesion ROIs; feature families cover intensity, texture, and shape.  
  - Post‑processing includes normalization and PCA; we compare both ordering strategies (Normalize→PCA vs. PCA→Normalize).

- Subtype Classification  
  - Train a lightweight MLP on engineered radiomics features to predict GBM molecular subtypes.  
  - A baseline following prior work (Wang 2017) reaches ~92% accuracy on subtype prediction.

- Mutual Information Analysis  
  - Compute mutual information between radiomics features and gene expression energy to assess relevance and interpretability.

### Results

- Segmentation: qualitative examples and training curves demonstrate stable convergence across sequences.  
- Classification: repo notes an MLP classifier achieving ~92% accuracy on subtype prediction. ROC/AUC plots are provided for model assessment.  
- Analysis: MI results highlight associations between selected radiomics features and gene expression energy, supporting biological plausibility.

### Discussion

- ROI quality strongly impacts downstream subtype classification; better lesion delineation improves radiomics stability.  
- Feature engineering (normalization/PCA order) affects classifier performance and interpretability; the repo retains both pipelines for comparison.  
- Limitations: slice‑based training and cohort size constrain generalization; modality imbalance may bias results.  
- Future directions: 3D/2.5D segmentation, multi‑sequence fusion, cross‑cohort validation, and end‑to‑end multi‑task learning (segmentation + subtype).

### Repository

Radiogenomics‑GBM (GitHub): https://github.com/PinkR1ver/Radiogenomics-GBM


