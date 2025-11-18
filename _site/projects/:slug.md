title: Radiogenomics‑GBM (Lesion Segmentation and Subtype Classification)
date: 2022-05-08
summary: Radiogenomics pipeline for glioblastoma (GBM): MRI lesion segmentation, ROI-based radiomics feature extraction, and subtype classification.
tags: [Medical Imaging, Segmentation, Radiomics, GBM, Python]
links:
  - label: Code
    url: https://github.com/PinkR1ver/Radiogenomics-GBM
---

This project targets radiogenomics analysis for glioblastoma (GBM). The end-to-end pipeline includes:

1) Lesion segmentation on MRI using U‑Net variants to obtain precise ROIs  
2) Radiomics feature extraction on ROIs with feature engineering  
3) GBM subtype classification (e.g., MLP) with evaluation and visualization

Dataset organization and reproducible training/evaluation scripts are provided in the repository.

Reference: Radiogenomics‑GBM ([GitHub](https://github.com/PinkR1ver/Radiogenomics-GBM)).

### Dataset

- Source: Ivy GAP / TCGA‑GBM cohorts, with MRI series T1, T2, FLAIR, and stacked inputs (“Stack”).  
- Structure: images/masks organized per patient and series; CSV metadata for slice locations and labels (e.g., `GBM_MRI_Dataset.csv`, `tumor_details.csv`).  
- ROIs: manual masks are used to supervise lesion segmentation and to define regions for radiomics extraction.  
- Radiomics config: `Params.yaml` drives feature extraction (e.g., intensity/texture/shape features via pyradiomics).

### Methods

- Segmentation (U‑Net family)  
  - Training via `train.py` with modality and epoch args, e.g. `python train.py T1 50`.  
  - Data I/O and preprocessing in `data.py`; model in `unet.py`; training utilities in `trainHelper.py`, `utils.py`.  
  - Outputs include per‑epoch monitors and qualitative results folders for each sequence (see `result/`).

- Radiomics Feature Extraction  
  - Extract features from lesion ROIs using the radiomics pipeline (`feature_extraction.py`) configured by `Params.yaml`.  
  - Post‑processing includes normalization and PCA; repository stores both pipelines (Normalize→PCA / PCA→Normalize) in clearly separated folders.

- Subtype Classification  
  - `classification.py` trains an MLP classifier on engineered radiomics features to predict GBM subtypes.  
  - As noted in the repo, an MLP baseline following prior work (Wang 2017) reaches ~92% accuracy on subtype prediction.

- Mutual Information Analysis  
  - `mutual_information.py` computes MI between radiomics features and gene expression energy for interpretability and feature relevance analysis.

### Results

- Segmentation: visual examples and training curves are exported under `result/` (e.g., `ROC_curve/`, `monitor/`).  
- Classification: repo notes an MLP classifier achieving ~92% accuracy on subtype prediction. ROC/AUC plots are provided for model assessment.  
- Analysis: MI results highlight associations between selected radiomics features and gene expression energy, supporting biological plausibility.

### Discussion

- ROI quality strongly impacts downstream subtype classification; better lesion delineation improves radiomics stability.  
- Feature engineering (normalization/PCA order) affects classifier performance and interpretability; the repo retains both pipelines for comparison.  
- Limitations: slice‑based training and cohort size constrain generalization; modality imbalance may bias results.  
- Future directions: 3D/2.5D segmentation, multi‑sequence fusion, cross‑cohort validation, and end‑to‑end multi‑task learning (segmentation + subtype).

### Reproducibility

1) Install dependencies: `pip install -r requirements.txt`  
2) Segmentation: `python train.py [T1|T2|FLAIR|Stack] [epochs]`  
3) Classification: `python classification.py`  
4) Radiomics / MI: `python feature_extraction.py` and `python mutual_information.py`  
5) Inspect outputs under `result/` for curves, ROC, and qualitative visualizations.


