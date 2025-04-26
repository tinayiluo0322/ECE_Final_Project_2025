# Effectiveness of Adversarial Patch Attacks on Imbalanced Safety Critical Settings

**Authors:**  
Xueqing (Annie) Wu, Yun-Chung (Murphy) Liu, Luopeiwen (Tina) Yi 

<img width="1198" alt="Screen Shot 2025-04-26 at 12 18 31 PM" src="https://github.com/user-attachments/assets/7d8b1ede-68bb-42de-b4ed-66fbcfb42856" />

---

## Abstract
Adversarial patch attacks can severely damage image classification models without needing access to model parameters or input data, posing major threats in real-world settings such as hazardous substance identification.  
This project investigates the effectiveness of adversarial patch attacks on both a balanced dataset (CIFAR-10) and a highly imbalanced dataset (batteries vs. non-batteries, 4.8% vs. 95.2%). We explored data imbalance mitigation techniques—data augmentation, weighted random sampling, and weighted cross-entropy loss—and evaluated attack success rates (ASR).  
Our results show that large whitebox patches (16×16 pixels) achieve the highest ASR (0.89 untargeted, 0.74 targeted) on CIFAR-10. On the imbalanced dataset, imbalance mitigation strategies boosted the minority battery class ASR from 0% to 97.75%.  
Future work will explore more extreme imbalance settings and defense strategies.

---

## Table of Contents
- [1. Introduction and Related Work](#1-introduction-and-related-work)
- [2. Methods](#2-methods)
  - [2.1 Base Experiment on Balanced CIFAR-10](#21-base-experiment-on-balanced-cifar-10)
  - [2.2 Experiments on Imbalanced Dataset](#22-experiments-on-imbalanced-dataset)
- [3. Results](#3-results)
  - [3.1 Base Experiment on Balanced CIFAR-10](#31-base-experiment-on-balanced-cifar-10)
  - [3.2 Experiments on Imbalanced Dataset](#32-experiments-on-imbalanced-dataset)
- [4. Conclusion and Future Work](#4-conclusion-and-future-work)
- [5. Code Availability](#5-code-availability)
- [6. References](#6-references)

---

## 1. Introduction and Related Work

Adversarial attacks threaten the reliability of image classification models, especially when classification errors have serious real-world consequences (e.g., misclassifying hazardous materials).  
While most adversarial attacks are designed to be imperceptible to humans, adversarial patches—introduced by Brown et al. (2017)—are large, visible patterns that force misclassification without needing knowledge of the input image.

In critical applications like automated garbage classification, detecting rare but dangerous items such as batteries is a "needle-in-a-haystack" challenge, particularly under attack scenarios.

While defense mechanisms such as randomized smoothing have been proposed, little work has addressed how patch attacks perform under severe class imbalance.

This project seeks to answer:

- **CIFAR-10 (Balanced Dataset):**
  - How does patch size affect ASR?
  - How do targeted vs. untargeted attacks compare?
  - How well do patches transfer between models?

- **Imbalanced Battery Dataset:**
  - How effective are patch attacks under extreme class imbalance?
  - What is the cost (in FPR) of achieving high TPR on rare classes?

---

## 2. Methods

### 2.1 Base Experiment on Balanced CIFAR-10

**Dataset:**  
- CIFAR-10: 60,000 images across 10 balanced classes.

**Pipeline:**
- Train ResNet-18 and VGG-16 models for 50 epochs.
- Generate adversarial patches via three pipelines:
  - **Pipeline 1:** Whitebox, Untargeted Attack
  - **Pipeline 2:** Blackbox, Untargeted Attack
  - **Pipeline 3:** Whitebox, Targeted Attack
- Evaluate ASR across patch sizes: 3×3, 5×5, 7×7, and 16×16.

**Attack Settings:**
- **Whitebox Attack:** Same model used for patch generation and classification.
- **Blackbox Attack:** Different models used (ResNet for patch, VGG for classification).
- **Targeted vs. Untargeted:** Analyze performance differences.

---

### 2.2 Experiments on Imbalanced Dataset

**Dataset:**  
- Garbage classification dataset from Kaggle.
- Two classes: Battery (4.78%) vs. Non-battery (95.22%).

**Pipeline:**
- Focus on 16×16 patches and whitebox attack.
- Two setups:
  - **Without imbalance mitigation**
  - **With imbalance mitigation:**
    - **Data Augmentation:** RandomCrop, Horizontal Flip, Rotation, ColorJitter.
    - **Weighted Random Sampling:** Balance minority and majority classes during training.
    - **Weighted Cross Entropy Loss:** Penalize misclassification of minority class more heavily.

---

## 3. Results

### 3.1 Base Experiment on Balanced CIFAR-10

- **Untargeted Whitebox Attack:**  
  - ASRs ~0.9 across patch sizes.
- **Untargeted Blackbox Attack:**  
  - ASRs much lower (~0.3 max), increases with patch size.
- **Targeted Attack:**  
  - Patch effectiveness varies by target class.
  - Classes like planes and birds showed higher ASRs.

| Patch Size | Plane | Bird | Cat | Deer | Dog | Frog | Horse | Ship | Truck | Average |
|:----------:|:-----:|:----:|:---:|:----:|:---:|:----:|:-----:|:---:|:-----:|:-------:|
| 3×3        | 53.26 | 6.76 | 17.72 | 0 | 0.18 | 0.08 | 0 | 6.78 | 0 | 8.48 |
| 5×5        | 81.48 | 77.06 | 4.48 | 0 | 1.46 | 8.44 | 0 | 22.24 | 0 | 19.77 |
| 7×7        | 54.90 | 92.30 | 100 | 74.32 | 38.98 | 77.72 | 0 | 43.44 | 0 | 50.54 |
| 16×16      | 100 | 100 | 100 | 100 | 100 | 38.56 | 0 | 82.24 | 99.42 | 74.11 |

---

### 3.2 Experiments on Imbalanced Dataset

- **Without Mitigation:**
  - Battery ASR: 0%
  - Majority class ASR much higher.
- **With Mitigation:**
  - Battery ASR improved to 99.75%.
  - ASRs for both classes became balanced.

**True Positive Rate (TPR) vs False Positive Rate (FPR):**

- Without mitigation, achieving 95–99% TPR required very high FPR.
- With mitigation, FPR decreased significantly while maintaining high TPR.
- Under adversarial attack, mitigation caused 100% FPR (all samples misclassified as battery), demonstrating high attack effectiveness.

---

## 4. Conclusion and Future Work

**Insights:**
- Larger patch sizes and whitebox attacks significantly increase ASR.
- Addressing class imbalance dramatically improves minority class robustness and susceptibility to attacks.

**Future Work:**
- Apply defense mechanisms to protect the minority class.
- Explore extreme imbalance scenarios (minority class <1%).
- Analyze settings with very few minority class samples (<25).

---

## 6. References
1. Goodfellow IJ, Shlens J, Szegedy C. *Explaining and harnessing adversarial examples.* arXiv:1412.6572 (2014).
2. Szegedy C, et al. *Intriguing properties of neural networks.* arXiv:1312.6199 (2013).
3. Brown TB, et al. *Adversarial Patch.* arXiv:1712.09665 (2017).
4. Li B, et al. *Certified adversarial robustness with additive noise.* NeurIPS (2019).
5. Lecuyer M, et al. *Certified robustness to adversarial examples with differential privacy.* IEEE S&P (2019).
6. Levine A, Feizi S. *(De)randomized smoothing for certifiable defense.* NeurIPS (2020).
7. Levine A, Feizi S. *Robustness certificates via randomized ablation.* AAAI (2020).
8. Shao R, et al. *On the adversarial robustness of vision transformers.* arXiv:2103.15670 (2021).
9. Krizhevsky A, Hinton G. *Learning multiple layers of features from tiny images.* (2009).
10. He K, et al. *Deep residual learning for image recognition.* CVPR (2016).
11. Simonyan K, Zisserman A. *Very deep convolutional networks for large-scale image recognition.* arXiv:1409.1556 (2014).

---

