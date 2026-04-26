# SiamDT++: Infrared Drone Tracking via Frequency Domain Perception
Official implementation of "SiamDT++: Infrared Drone Tracking via Frequency Domain Perception". A robust SOTA tracker for tiny IR targets using FFT spectral gating and Swin-CA-BiFPN.

[![Pytorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat&logo=pytorch&logoColor=white)](https://pytorch.org/)
[![Paper](https://img.shields.io/badge/Paper-PDF-red.svg)](#) 
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Abstract

Tracking Low, Slow, and Small (LSS) Unmanned Aerial Vehicles (UAVs) in the infrared (IR) domain is highly challenging, primarily due to **feature sparsity**, **thermal crossover**, and **complex background clutter**.

To overcome the issues of feature dispersion and inadequate background suppression in existing Siamese networks, we propose **SiamDT++**. This architecture integrates multi-scale frequency-domain perception and a dynamic focusing mechanism:
1. **Swin-CA-BiFPN**: Bidirectionally fuses shallow textures and deep semantics to enhance the perception of point-like targets.
2. **FDCA (Frequency Domain Context Attention)**: Utilizes spectral gating to suppress low-frequency clutter and enhance high-frequency edges, resolving feature degradation under thermal crossover.
3. **Dynamic Focusing Prediction Head**: Reconstructs the loss function (Focal Loss + Wise-IoU) to achieve pixel-level regression precision.

## Technical Highlights

### 1. Swin-CA-BiFPN: Geometry-Aware Feature Extraction
To address the feature dispersion issue where tiny targets (<5 pixels) are easily lost in deep networks, we designed a hybrid architecture:
* **Bidirectional Feature Pyramid (BiFPN)**: Transfers shallow high-resolution textures to deep layers via a bottom-up pathway, preserving spatial indices.
* **Coordinate Attention (CA)**: Decomposes attention into two 1D encoding processes, forcing the network to focus on the precise localization of targets.

### 2. FDCA: Frequency Domain Global Context Interaction
When target and background temperatures converge (thermal crossover), spatial features often fail. The FDCA module processes this in the frequency domain via Fourier Transform:
$$X_{u,v}=\sum_{h=0}^{H-1}\sum_{w=0}^{W-1}x_{h,w}e^{-j2\pi(\frac{hu}{H}+\frac{wv}{W})}$$
By utilizing a learnable spectral weight $W_{gate}$ to filter out background noise, the Signal-to-Noise Ratio (SNR) is significantly improved.

### 3. Dynamic Focusing Loss System
To solve the extreme imbalance between positive and negative samples:
* **Classification**: Introduces **Focal Loss** to make the model focus on feature-sparse hard samples.
* **Regression**: Adopts **Wise-IoU (WIoU)** to dynamically allocate gradient gains based on anchor box quality, preventing the model from being misled by annotation noise.

---

## Architecture

![Overall Framework](./assets/framework.png)
*Figure 1: Overall Framework of the SiamDT++ Algorithm*

---

## Experimental Results

### 1. Quantitative Results
We evaluated our model on the highly challenging **Anti-UAV410** dataset using One-Pass Evaluation (OPE):

| Algorithm | Backbone | Precision | Success Rate |
| :--- | :--- | :--- | :--- |
| SiamFC | AlexNet | 0.4798 | 0.3516 |
| SiamRPN++ | ResNet-50 | 0.5960 | 0.4578 | 
| ARTrack | Transformer | 0.6257 | 0.4856 |
| TransUSF | Transformer | 0.6959 | 0.5582 |
| SiamDT (Baseline) | Swin-Tiny | 0.6885 | 0.5420 |
| **SiamDT++ (Ours)** | **Swin-CA-BiFPN** | **0.7050** | **0.6035** |

> **Conclusion**: Compared to the baseline SiamDT, the success rate achieves a significant leap of **6.15%**. Furthermore, on a real-world ground/air background dataset, the precision of SiamDT++ reaches an impressive **90.35%**.

### 2. Ablation Study
Validating the effectiveness of each module on Anti-UAV410:

| Model | Precision | Success Rate | Improvement Focus |
| :--- | :--- | :--- | :--- |
| SiamDT (Baseline) | 0.6885 | 0.5420 | - |
| + Swin-CA-BiFPN | 0.6935 | 0.5785 | Alleviates feature dispersion |
| + FDCA | 0.7030 | 0.5945 | Suppresses frequency domain clutter |
| **SiamDT++ (Ours)** | **0.7050** | **0.6035** | Dynamic weight allocation |

---

## Qualitative Visualization

![Tracking Results](./assets/1.png)
![Tracking Results](./assets/2.png)
![Tracking Results](./assets/3.png)
![Tracking Results](./assets/4.png)
![Tracking Results](./assets/5.png)
*Tracking performance comparison under complex terrain, thermal crossover, and bright clutter interference.*

---

## Getting Started

*Code is being cleaned up and will be released soon!*
