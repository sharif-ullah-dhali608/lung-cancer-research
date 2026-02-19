
**Abstract**
The application of Vision Transformers (ViT) to medical image analysis represents a significant shift from local feature extraction to global context modeling. This paper investigates the efficacy of ViTs for the multi-class classification of lung cancer. Unlike standard transfer learning approaches that utilize fixed architectures, we propose a dynamic **Automated Architectural Search** pipeline using Optuna. We optimize critical architectural parameters—specifically projection dimensions and transformer depth—alongside training hyperparameters. Furthermore, we introduce a novel **"Freeze Percentage"** tuning strategy to empirically determine the optimal extent of transfer learning. Our optimized ViT model achieves a test accuracy of **87.84%**, offering insights into the trade-offs between model complexity and data requirements in medical diagnostics.

---

## 1. Introduction
While Convolutional Neural Networks (CNNs) have long dominated medical image analysis, they are inherently limited by their local receptive fields. Vision Transformers (ViT) overcome this by using self-attention mechanisms to capture long-range dependencies across the entire image. However, ViTs are notoriously data-hungry and sensitive to hyperparameter choices. This study addresses these challenges by automating the design process. Instead of manually selecting a ViT configuration, we allow a Bayesian optimization algorithm to "search" for the architecture that best fits our lung cancer dataset.

## 2. Methodology

### 2.1 Data Preparation
The study utilizes a Lung Cancer Dataset divided into three classes: **Benign**, **Malignant**, and **Normal**.
*   **Preprocessing:** Images were resized to **224x224** (standard ViT input resolution) and organized into batches.
*   **Augmentation:** We purposely avoided heavy augmentation during the architectural search phase to isolate the impact of model capacity changes.

### 2.2 The Tunable Vision Transformer
We constructed a flexible ViT architecture where the following components were treated as search variables rather than fixed constants:
*   **Patch Size:** Fixed at **16x16** to balance sequence length and feature granularity.
*   **Projection Dimension:** Searchable range $[256, 384, 512]$. This controls the width of the embedding space.
*   **Transformer Layers:** Searchable range $[4, 6, 8, 10]$. This controls the depth and capacity of the model.
*   **MLP Head:** A fixed Multiple Layer Perceptron acting as the classification head.

### 2.3 Three-Phase Optimization Pipeline
We utilized **Optuna** with the Tree-structured Parzen Estimator (TPE) to conduct a rigorous three-stage search:

#### **Phase 1: Hyperparameter Discovery**
We first established a baseline by optimizing learning dynamics.
*   **Optimizer:** We tested `Adam`, `Adamax`, `RMSprop`, and `AdamW` (Adam with Weight Decay).
*   **Findings:** `Adamax` proved most stable for this architecture, likely due to its robustness to gradient noise in Transformers.

#### **Phase 2: Architectural Search**
With learning parameters fixed, we searched for the optimal model structure.
*   **Result:** The search converged on a **Projection Dimension of 512** and **6 Transformer Layers**. This suggests that a wider (512 dim) but shallower (6 layers) network is preferable for this dataset, likely preventing vanishing gradients while maintaining sufficient feature expressiveness.

#### **Phase 3: Fine-Tuning Depth (Freeze Percentage)**
A critical innovation in this study is tuning the *extent* of transfer learning. Instead of binary "freeze/unfreeze" decisions, we tuned a `freeze_percent` parameter ($10\% - 80\%$ of total layers).
*   **Result:** The optimal configuration was freezing only **10%** of the layers. This indicates that for ViTs on this specific medical task, preserving pre-trained weights is less effective than allowing the majority of the network to adapt to the new domain.

## 3. Experimental Results

The final optimized model was trained in two stages (Initial Training + Fine-Tuning) and evaluated on a strictly reserved **Final Test Set**.

### 3.1 Performance Metrics

| Metric | Score |
| :--- | :--- |
| **Accuracy** | **87.84%** |
| **Loss** | 0.4979 |
| **Precision** | 87.82% |
| **Recall** | 87.62% |

### 3.2 Analysis of Results
*   **Performance Gap:** The accuracy of 87.84% is respectable but typical for ViTs trained on smaller datasets (thousands rather than millions of images). The lack of inductive bias (like translation invariance in CNNs) means the ViT has to "learn" how to see structures, which requires massive data.
*   **Training Dynamics:** The model showed rapid convergence during the initial epochs but hit a loss plateau quickly. The "10% freeze" finding confirms that the ImageNet features were not directly transferable to the subtle textures of lung scans, forcing the model to relearn significantly.

## 4. Conclusion
This work successfully demonstrates an **Automated Architectural Search** pipeline for Vision Transformers in medical imaging. By dynamically tuning the model's width and depth, we extracted optimal performance (87.84%) from the available data. While currently outperformed by specialized CNNs (like EfficientNet), this ViT pipeline offers a scalable foundation. Future work involving larger datasets or self-supervised pre-training on medical images could allow this architecture to surpass traditional methods.
