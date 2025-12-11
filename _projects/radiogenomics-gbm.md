---
title: Radiogenomic Analysis on Glioblastoma (Lesion Segmentation and Subtype Classification)
summary: "From routine MRI to molecular insight: segment GBM lesions, distill lesion ROIs into radiomic features, and predict subtypes to bridge imaging with biology."
tags: [Medical Imaging, Segmentation, Radiomics, GBM, U-Net]
duration: 2021.09 - 2022.05
image_dir: /assets/img/project/radiogenomics-gbm
---

### Introduction

In clinic, MRI is routine while molecular assays can be costly or delayed. I asked a simple question: can we anticipate GBM molecular subtypes from what radiologists already see? I built an end‑to‑end pipeline that first teaches a network to delineate the tumor, then turns those ROIs into quantitative radiomic signatures for a lightweight classifier. Along the way, I iterated on data organization, stabilized training on imbalanced modalities, and added analyses that explain which features matter and why.

This project targets radiogenomics analysis for glioblastoma (GBM). The end-to-end pipeline includes:

1) Lesion segmentation on MRI using U‑Net variants to obtain precise ROIs (lesion Regions of Interest)  
2) Radiomics feature extraction on lesion ROIs (tumor regions) with feature engineering  
3) GBM subtype classification (e.g., MLP) with evaluation and visualization

### Objectives

- Anticipate GBM molecular subtypes from routine MRI by linking precise lesion segmentation with radiomics‑based signatures.  


### Dataset

- Source: Ivy GAP / TCGA‑GBM cohorts, with MRI series T1, T2, FLAIR, and stacked inputs (“Stack”).  
- Structure: images/masks organized per patient and series; CSV metadata for slice locations and labels.  
- ROIs: manual masks are used to supervise lesion segmentation and to define regions for radiomics extraction.  
- Radiomics config: a YAML configuration drives feature extraction (e.g., intensity/texture/shape features).

### Methods

- Typical workflow of radiogenomic studies
  
  <figure class="results-figure figure-academic">
    <img src="{{ page.image_dir | append: '/workflow-radiogenomic.png' | relative_url }}" alt="Typical workflow of radiogenomic studies" />
    <figcaption>
      Typical workflow of radiogenomic studies<sup class="ref-sup"><a href="#ref-1">[1]</a></sup>.
    </figcaption>
  </figure>

  1) Image acquisition  
  2) Image processing, including noise/artifact reduction, intensity and/or orientation standardization, coregistration of the multiparametric MRI scans  
  3) ROI definition using manual annotation or automatic segmentation  
  4) Feature extraction based on human-engineered (conventional radiomics) or deep-learning approaches  
  5) Data analysis, involving machine/deep-learning methods for feature selection, classification, and cross-validation

 - Segmentation (U‑Net family)<sup class="ref-sup"><a href="#ref-2">[2]</a></sup>  
    - Supervised learning with lesion masks; architectures based on U‑Net variants adapted for GBM MRI.  
    - Robust preprocessing and modality‑aware batching to handle sequence imbalance.  
    - Outputs include training dynamics and qualitative visualizations for each sequence.
    - Architecture :
  
    ```python
    import torch
    from torch import nn
    
    class ConvBlock(nn.Module):
        def __init__(self, in_channels: int, out_channels: int, p: float = 0.3):
            super().__init__()
            self.net = nn.Sequential(
                nn.Conv2d(in_channels, out_channels, kernel_size=3, stride=1, padding=1, padding_mode="reflect", bias=False),
                nn.BatchNorm2d(out_channels),
                nn.Dropout2d(p),
                nn.LeakyReLU(inplace=True),
                nn.Conv2d(out_channels, out_channels, kernel_size=3, stride=1, padding=1, padding_mode="reflect", bias=False),
                nn.BatchNorm2d(out_channels),
                nn.Dropout2d(p),
                nn.LeakyReLU(inplace=True),
            )
        def forward(self, x: torch.Tensor) -> torch.Tensor:
            return self.net(x)
    ```
  
    - Downsampling: strided 3×3 conv (stride=2).  
    - Upsampling: nearest‑neighbor upsample ×2 + 1×1 conv to halve channels; concatenate with encoder skip features.  
    - Depth: 64 → 128 → 256 → 512 → 1024 (encoder) with symmetric decoder.  
    - Output: 1‑channel mask with sigmoid activation.  
    - Key change: Dropout2d(p=0.3) in each ConvBlock improves generalization under limited data and reduces overfitting.
  
  - Down/Up sampling blocks and overall UNet:
  
    ```python
    import torch
    from torch import nn
    from torch.nn import functional as F
    
    class downSample(nn.Module):
        def __init__(self, channel: int):
            super().__init__()
            self.layer = nn.Sequential(
                nn.Conv2d(channel, channel, kernel_size=3, stride=2, padding=1, padding_mode="reflect", bias=False),
                nn.BatchNorm2d(channel),
                nn.LeakyReLU(inplace=True),
            )
        def forward(self, x: torch.Tensor) -> torch.Tensor:
            return self.layer(x)
    
    class upSample(nn.Module):
        def __init__(self, channel: int):
            super().__init__()
            # reduce channels after upsample to match skip features
            self.layer = nn.Conv2d(channel, channel // 2, kernel_size=1, stride=1)
        def forward(self, x: torch.Tensor, skip: torch.Tensor) -> torch.Tensor:
            x = F.interpolate(x, scale_factor=2, mode="nearest")
            x = self.layer(x)
            return torch.cat((x, skip), dim=1)
    
    class UNet(nn.Module):
        def __init__(self, in_channels: int = 1, base: int = 64, p: float = 0.3):
            super().__init__()
            # Encoder
            self.c1 = ConvBlock(in_channels, base, p)
            self.d1 = downSample(base)
            self.c2 = ConvBlock(base, base * 2, p)
            self.d2 = downSample(base * 2)
            self.c3 = ConvBlock(base * 2, base * 4, p)
            self.d3 = downSample(base * 4)
            self.c4 = ConvBlock(base * 4, base * 8, p)
            self.d4 = downSample(base * 8)
            self.c5 = ConvBlock(base * 8, base * 16, p)
            # Decoder
            self.u1 = upSample(base * 16)
            self.c6 = ConvBlock(base * 16, base * 8, p)
            self.u2 = upSample(base * 8)
            self.c7 = ConvBlock(base * 8, base * 4, p)
            self.u3 = upSample(base * 4)
            self.c8 = ConvBlock(base * 4, base * 2, p)
            self.u4 = upSample(base * 2)
            self.c9 = ConvBlock(base * 2, base, p)
            # Head
            self.head = nn.Conv2d(base, 1, kernel_size=3, stride=1, padding=1)
            self.act = nn.Sigmoid()
        def forward(self, x: torch.Tensor) -> torch.Tensor:
            L1 = self.c1(x)
            L2 = self.c2(self.d1(L1))
            L3 = self.c3(self.d2(L2))
            L4 = self.c4(self.d3(L3))
            L5 = self.c5(self.d4(L4))
            R4 = self.c6(self.u1(L5, L4))
            R3 = self.c7(self.u2(R4, L3))
            R2 = self.c8(self.u3(R3, L2))
            R1 = self.c9(self.u4(R2, L1))
            return self.act(self.head(R1))
    ```

- Radiomics Feature Extraction  
  - Extract quantitative features from lesion ROIs using the PyRadiomics toolkit<sup class="ref-sup"><a href="#ref-3">[3]</a></sup>; feature families cover intensity, texture, and shape.


- Subtype Classification  
  - Train a lightweight MLP on engineered radiomics features to predict GBM molecular subtypes.  
  - A baseline following prior work (Wang 2017) reaches ~92% accuracy on subtype prediction.

 

### Results

<figure class="results-figure figure-academic">
  <img src="{{ page.image_dir | append: '/Segmentation.gif' | relative_url }}" alt="Segmentation animation (T1 example)" />
  <figcaption>Segmentation animation: left = original MRI slice; middle = ground truth; right = model prediction.</figcaption>
</figure>

#### T1 sequence

<div class="results-grid">
  <figure class="results-figure figure-academic">
    <img src="{{ page.image_dir | append: '/segmentation-t1.png' | relative_url }}" alt="T1 segmentation examples — train / validation / test" />
    <figcaption>T1 segmentation examples — train, validation, and test</figcaption>
  </figure>
  <div class="table-wrap">
    <table class="results-table">
      <caption>T1 segmentation metrics (best epoch 17)</caption>
      <thead>
        <tr>
          <th>Metric</th>
          <th>Train</th>
          <th>Validation</th>
          <th>Test</th>
        </tr>
      </thead>
      <tbody>
        <tr><td>Accuracy</td><td>0.989330</td><td>0.987710</td><td>0.980175</td></tr>
        <tr><td>Balanced Accuracy</td><td>0.920806</td><td>0.926565</td><td>0.785334</td></tr>
        <tr><td>Precision</td><td>0.777257</td><td>0.712830</td><td>0.721325</td></tr>
        <tr><td>Sensitivity (Recall)</td><td>0.848366</td><td>0.862156</td><td>0.577671</td></tr>
        <tr><td>Specificity</td><td>0.993246</td><td>0.990973</td><td>0.992996</td></tr>
        <tr><td>F1‑score</td><td>0.811257</td><td>0.780414</td><td>0.642738</td></tr>
        <tr><td>IoU</td><td>0.682449</td><td>0.639901</td><td>0.473555</td></tr>
      </tbody>
    </table>
  </div>
</div>

- Classification: an MLP on radiomic features achieves strong subtype discrimination (see repository for ROC/AUC details).

#### T2 sequence

<div class="results-grid">
  <figure class="results-figure figure-academic">
    <img src="{{ page.image_dir | append: '/segmentation-t2.png' | relative_url }}" alt="T2 segmentation examples — train / validation / test" />
    <figcaption>T2 segmentation examples — train, validation, and test</figcaption>
  </figure>
  <div class="table-wrap">
    <table class="results-table">
      <caption>T2 segmentation metrics (best epoch 15)</caption>
      <thead>
        <tr>
          <th>Metric</th>
          <th>Train</th>
          <th>Validation</th>
          <th>Test</th>
        </tr>
      </thead>
      <tbody>
        <tr><td>Accuracy</td><td>0.989264</td><td>0.989870</td><td>0.984979</td></tr>
        <tr><td>Balanced Accuracy</td><td>0.910587</td><td>0.909416</td><td>0.836919</td></tr>
        <tr><td>Precision</td><td>0.789571</td><td>0.823274</td><td>0.803875</td></tr>
        <tr><td>Sensitivity (Recall)</td><td>0.827365</td><td>0.824060</td><td>0.679117</td></tr>
        <tr><td>Specificity</td><td>0.993809</td><td>0.994771</td><td>0.994722</td></tr>
        <tr><td>F1‑score</td><td>0.808027</td><td>0.823667</td><td>0.736243</td></tr>
        <tr><td>IoU</td><td>0.677890</td><td>0.700199</td><td>0.582589</td></tr>
      </tbody>
    </table>
  </div>
</div>

#### FLAIR sequence

<div class="results-grid">
  <figure class="results-figure figure-academic">
    <img src="{{ page.image_dir | append: '/segmentation-flair.png' | relative_url }}" alt="FLAIR segmentation examples — train / validation / test" />
    <figcaption>FLAIR segmentation examples — train, validation, and test</figcaption>
  </figure>
  <div class="table-wrap">
    <table class="results-table">
      <caption>FLAIR segmentation metrics (best epoch 16)</caption>
      <thead>
        <tr>
          <th>Metric</th>
          <th>Train</th>
          <th>Validation</th>
          <th>Test</th>
        </tr>
      </thead>
      <tbody>
        <tr><td>Accuracy</td><td>0.992049</td><td>0.992683</td><td>0.987521</td></tr>
        <tr><td>Balanced Accuracy</td><td>0.927457</td><td>0.925201</td><td>0.852725</td></tr>
        <tr><td>Precision</td><td>0.789571</td><td>0.823274</td><td>0.803875</td></tr>
        <tr><td>Sensitivity (Recall)</td><td>0.859252</td><td>0.853861</td><td>0.709059</td></tr>
        <tr><td>Specificity</td><td>0.996663</td><td>0.996541</td><td>0.996391</td></tr>
        <tr><td>F1‑score</td><td>0.853110</td><td>0.862322</td><td>0.778177</td></tr>
        <tr><td>IoU</td><td>0.741113</td><td>0.759359</td><td>0.636898</td></tr>
      </tbody>
    </table>
  </div>
</div>

#### Stack sequence (multi-sequence stacked as RGB-like channels)

<div class="results-grid">
  <figure class="results-figure figure-academic">
    <img src="{{ page.image_dir | append: '/segmentation-stack.png' | relative_url }}" alt="Stack (T1/T2/FLAIR as 3 channels) — train / validation / test" />
    <figcaption>Stack segmentation examples — T1/T2/FLAIR stacked as three channels</figcaption>
  </figure>
  <div class="table-wrap">
    <table class="results-table">
      <caption>Stack segmentation metrics (best epoch 16)</caption>
      <thead>
        <tr>
          <th>Metric</th>
          <th>Train</th>
          <th>Validation</th>
          <th>Test</th>
        </tr>
      </thead>
      <tbody>
        <tr><td>Accuracy</td><td>0.993412</td><td>0.993930</td><td>0.989395</td></tr>
        <tr><td>Balanced Accuracy</td><td>0.946541</td><td>0.944896</td><td>0.797306</td></tr>
        <tr><td>Precision</td><td>0.871109</td><td>0.881431</td><td>0.858787</td></tr>
        <tr><td>Sensitivity (Recall)</td><td>0.896896</td><td>0.893092</td><td>0.785677</td></tr>
        <tr><td>Specificity</td><td>0.996186</td><td>0.996700</td><td>0.995885</td></tr>
        <tr><td>F1‑score</td><td>0.883814</td><td>0.887223</td><td>0.820607</td></tr>
        <tr><td>IoU</td><td>0.791817</td><td>0.797306</td><td>0.695788</td></tr>
      </tbody>
    </table>
  </div>
</div>

#### Across-sequence comparison (test set)

<div class="table-wrap">
<table class="results-table">
  <caption>Test-set metrics by sequence (best epoch per sequence)</caption>
  <thead>
    <tr>
      <th>Metric</th>
      <th>T1</th>
      <th>T2</th>
      <th>FLAIR</th>
      <th>Stack</th>
    </tr>
  </thead>
  <tbody>
    <tr><td>Accuracy</td><td>0.980175</td><td>0.984979</td><td>0.987521</td><td>0.989395</td></tr>
    <tr><td>Balanced Accuracy</td><td>0.785334</td><td>0.836919</td><td>0.852725</td><td>0.797306</td></tr>
    <tr><td>Precision</td><td>0.721325</td><td>0.803875</td><td>0.803875</td><td>0.858787</td></tr>
    <tr><td>Sensitivity (Recall)</td><td>0.577671</td><td>0.679117</td><td>0.709059</td><td>0.785677</td></tr>
    <tr><td>Specificity</td><td>0.992996</td><td>0.994722</td><td>0.996391</td><td>0.995885</td></tr>
    <tr><td>F1‑score</td><td>0.642738</td><td>0.736243</td><td>0.778177</td><td>0.820607</td></tr>
    <tr><td>IoU</td><td>0.473555</td><td>0.582589</td><td>0.636898</td><td>0.695788</td></tr>
  </tbody>
</table>
</div>

### Contributions

- We built and validated a GBM‑oriented, end‑to‑end radiogenomics pipeline: multi‑sequence lesion segmentation → ROI‑based radiomics extraction (PyRadiomics) → subtype classification.  

### Discussion

- Multi‑sequence fusion helps segmentation: stacking T1/T2/FLAIR as multi‑channel input improves boundaries and reduces false positives because each sequence contributes complementary contrast (T1 anatomical detail, T2 fluid/edema sensitivity, FLAIR CSF suppression highlighting peritumoral edema).  
- Limitation (subtype labeling): a patient may express mixed molecular signatures. Our current pipeline assumes single‑label subtype per patient and cannot produce multi‑label predictions. A practical path is to adopt multi‑label learning (sigmoid outputs with thresholding), uncertainty‑aware calibration, or mixture‑of‑subtypes modeling to reflect heterogeneity.  
- Concrete future directions:  
  1) 3D segmentation to derive volumetric (3D) radiomics features, potentially improving subtype discrimination over slice‑wise 2D features.  
  2) Move beyond hand‑crafted radiomics: explore end‑to‑end learning that maps images/ROIs directly to subtype, allowing the network to learn task‑specific representations.  

### Appendix

#### References

<ol class="refs">
  <li id="ref-1">Fathi Kazerooni, Anahita, et al. “Imaging Signatures of Glioblastoma Molecular Characteristics: A Radiogenomics Review.” <em>Journal of Magnetic Resonance Imaging</em>, 52(1), 2020, 54–69. DOI: <a href="https://doi.org/10.1002/jmri.26907" target="_blank" rel="noopener">10.1002/jmri.26907</a>.</li>
  <li id="ref-2">Ronneberger, O., Fischer, P., Brox, T. “U‑Net: Convolutional Networks for Biomedical Image Segmentation.” In: <em>MICCAI 2015</em>, LNCS 9351, pp. 234–241. DOI: <a href="https://doi.org/10.1007/978-3-319-24574-4_28" target="_blank" rel="noopener">10.1007/978-3-319-24574-4_28</a>.</li>
  <li id="ref-3">van Griethuysen, J. J. M., et al. “Computational Radiomics System to Decode the Radiographic Phenotype.” <em>Cancer Research</em>, 77(21), 2017, e104–e107. DOI: <a href="https://doi.org/10.1158/0008-5472.CAN-17-0339" target="_blank" rel="noopener">10.1158/0008-5472.CAN-17-0339</a>. (PyRadiomics)</li>
</ol>

#### Thesis

- [My Undergraduate Final Thesis (NUS, PDF)](https://github.com/PinkR1ver/Radiogenomics-GBM/blob/master/Doc/Final%20Thesis%20NUS.pdf)

#### Repository

- [Radiogenomics‑GBM (GitHub)](https://github.com/PinkR1ver/Radiogenomics-GBM)


