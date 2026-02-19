

**Abstract**
Lung cancer remains a leading cause of cancer-related mortality worldwide, necessitating robust automated diagnostic tools. This study presents a deep learning framework for the multi-class classification of lung cancer (Benign, Malignant, and Normal) using transfer learning. We employ the EfficientNetB3 architecture as a feature extractor, enhanced by a novel two-phase optimization strategy using Optuna. Phase one automates the search for optimal training hyperparameters, while phase two systematically determines the optimal depth for layer freezing during fine-tuning. Our experimental results on a publicly available dataset demonstrate a final test accuracy of **94.87%**, highlighting the efficacy of reducing human bias in hyperparameter selection and model adaptation.

---

## 1. Introduction
Early and accurate diagnosis of lung cancer significantly improves patient survival rates. While traditional Convolutional Neural Networks (CNNs) have shown promise, determining the optimal configuration for transfer learning—specifically, which hyperparameters to use and how many layers to retrain—remains a challenging, often manual process. This paper proposes an automated pipeline that leverages Bayesian optimization (Optuna) to configure an `EfficientNetB3`-based classifier, ensuring both high accuracy and training stability.

## 2. Methodology

### 2.1 Dataset Description
The model was trained and evaluated on a structured Lung Cancer Dataset containing three distinct classes:
*   **Benign cases**
*   **Malignant cases**
*   **Normal cases**

**Data Partitioning:**
To ensure rigorous evaluation, the original dataset was split as follows:
1.  **Training Set:** 3,508 images, subjected to augmentation to improve generalization.
2.  **Validation Set:** 151 images (50% of the original test set), used exclusively for the Optuna objective functions to guide hyperparameter tuning.
3.  **Final Test Set:** 153 images (50% of the original test set), held out completely until the final evaluation to prevent data leakage.

### 2.2 Model Architecture
We utilized **EfficientNetB3** (pre-trained on ImageNet) as the backbone due to its superior balance between parameter efficiency and accuracy. 
*   **Input Shape:** $(300, 300, 3)$
*   **Base Model:** Frozen initially effectively extracting high-level features.
*   **Classification Head:**
    *   `GlobalAveragePooling2D`: Reduces spatial dimensions.
    *   `Dropout`: Initial rate optimized via Optuna to prevent overfitting.
    *   `Dense (256 units, ReLU)`: Intermediate learning layer.
    *   `Dropout`: Secondary regularization.
    *   `Dense (3 units, Softmax)`: Final probability distribution.

### 2.3 Two-Phase Optimization Strategy
A key contribution of this work is the **Automated Two-Phase Optimization Pipeline**:

#### **Phase 1: Hyperparameter Optimization**
We utilized the Tree-structured Parzen Estimator (TPE) sampler to maximize validation accuracy over 15 trials.
*   **Search Space:**
    *   **Optimizer:** [Adam, Adamax, RMSprop]
    *   **Learning Rate:** Log-uniform distribution $[1e-5, 1e-3]$
    *   **Batch Size:** [16, 32, 64]
    *   **Dropout Rate:** $[0.2, 0.5]$
*   **Result:** The search converged on **Adam** ($lr \approx 5.87e-4$), Batch Size **32**, and Dropout **0.20**.

#### **Phase 2: Fine-Tuning Depth Search**
Unlike standard fine-tuning which arbitrarily unfreezes "the last N layers," we treated the *freeze count* as a hyperparameter.
*   **Search Space:** Freeze first $[10, 20, 40, 50, 70, 80, 90, 100]$ layers.
*   **Objective:** Maximize Validation Accuracy after fine-tuning.
*   **Result:** Freezing exactly the first **70 layers** provided the best balance, achieving a validation accuracy of **96.03%**.

## 3. Experimental Results

The final model was trained using the optimal configuration derived from Phases 1 and 2. Training utilized Early Stopping (Patience=5) to prevent overfitting.

### 3.1 Quantitative Metrics
Performance on the **Held-Out Final Test Set**:

| Metric | Performance |
| :--- | :--- |
| **Accuracy** | **94.87%** |
| **Loss** | **0.1161** |
| **Precision** | **94.87%** |
| **Recall** | **94.87%** |

### 3.2 Key Observations
*   **Optimizer Efficacy:** The `Adam` optimizer consistently outperformed `Adamax` and `RMSprop` in early trials, providing faster convergence.
*   **Impact of Freezing:** The Phase 2 search revealed that unfreezing too many layers (e.g., freezing only 10) degraded performance (Val Acc dropped to ~92%), likely due to the destruction of generic feature extractors learned from ImageNet. Freezing 70 layers preserved these essential low-level features while allowing high-level features to adapt to the medical domain.

## 4. Conclusion
This research demonstrates that identifying the optimal transfer learning strategy—specifically the depth of fine-tuning—is as critical as architectural choice. By automating this process with Optuna, we achieved a high classification accuracy of 94.87% without extensive manual trial-and-error. Future work will investigate the applicability of this two-phase methodology to Vision Transformers (ViT).
