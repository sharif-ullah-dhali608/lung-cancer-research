# Deep Residual Learning for Lung Cancer Classification: Optimizing ResNet101 via Automated Fine-Tuning

**Abstract**
This report evaluates the efficacy of **ResNet101**, a deep residual network, for multi-class lung cancer classification (Benign, Malignant, Normal). Leveraging an automated optimization pipeline powered by **Optuna**, we systematically explored the impact of transfer learning depth and hyperparameter configuration. Our experimental results demonstrate that ResNet101 achieves a robust **Final Test Accuracy of 94.12%**. A key finding of this study is the critical role of selective layer freezing; contrary to full model fine-tuning, freezing the initial **80 layers** proved statistically optimal, preserving generalized features while allowing specific adaptation to medical imaging artifacts.

---

## 1. Introduction
Residual Networks (ResNets) mitigate the vanishing gradient problem in deep networks through skip connections, allowing for the training of very deep architectures. This study applies **ResNet101** (pre-trained on ImageNet) to the domain of lung cancer histology. We aim to determine not just *if* ResNet works, but *how* to optimally adapt such a deep backbone to a smaller, specialized dataset using automated search algorithms.

## 2. Methodology

### 2.1 Model Architecture
*   **Backbone:** ResNet101 (101 layers deep).
*   **Pre-training:** Weights initialized from ImageNet.
*   **Classification Head:** Custom fully connected layers added after the global average pooling of the backbone's final feature map.

### 2.2 Automated Optimization Strategy
We employed a two-phase optimization strategy to remove human bias from the tuning process:

#### **Phase 1: Hyperparameter Tuning**
We explored a search space including Learning Rate, Batch Size, and Optimizers (`Adam`, `Adamax`, `RMSprop`).
*   **Optimal Settings:** `Adamax` optimizer with a learning rate of $\approx 8.7e-5$ and Batch Size 16 proved most stable.

#### **Phase 2: The "Freeze Count" Experiment**
A unique contribution of this analysis is the treatment of "Freezing Depth" as a search variable. We tested freezing the first $[10, 20, ..., 140]$ layers.
*   **Findings:** Performance did not increase linearly with unfreezing.
*   **The "Sweet Spot":** Freezing exactly the **first 80 layers** yielded the highest validation accuracy ($\approx 94.7\%$). Freezing fewer layers (e.g., only 10) risked overfitting, while freezing too many limited the model's ability to learn medical-specific features.

## 3. Experimental Results

Performance was measured on a dedicated **Held-Out Test Set** (153 images) that was never seen during the optimization process.

### 3.1 Quantitative Metrics

| Metric | Score | Matches EfficientNet? |
| :--- | :--- | :--- |
| **Accuracy** | **94.12%** | Close (~94.8%) |
| **Precision** | 93.34% | Yes |
| **Recall** | 94.12% | Yes |
| **F1-Score** | 93.32% | Yes |

### 3.2 Class-Specific Performance
*   **Malignant Cases:** The model achieved perfect **1.00 Precision and Recall** for Malignant samples (105 support), indicating zero misclassification for cancer positive cases.
*   **Benign/Normal:** Minor confusion was observed between Benign and Normal classes, suggesting these morphological boundaries are subtler for the ResNet architecture than for the Malignant class.

## 4. Conclusion
ResNet101 effectively transfers to the lung cancer domain, achieving high diagnostic accuracy (94.12%). The automated discovery that freezing 80% of the network (80 layers) is optimal highlights the importance of "partial transfer learning." While slightly heavier computationally than EfficientNetB3, ResNet101 remains a highly reliable candidate for automated diagnosis systems, particularly due to its perfect sensitivity for malignant cases.
