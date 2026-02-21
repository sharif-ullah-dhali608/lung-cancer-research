# Hybrid Feature Extraction for Lung Cancer Classification: Synergizing EfficientNetB3 with SVM

**Abstract**
Deep learning models are often used as end-to-end classifiers. However, this study demonstrates that using a State-of-the-Art (SOTA) CNN, **EfficientNetB3**, purely as a **feature extractor**, combined with Principal Component Analysis (PCA) and a Support Vector Machine (SVM), outperforms traditional fine-tuning approaches. This hybrid pipeline achieved a remarkable **99.12% Test Accuracy**, enabling robust and computationally efficient lung cancer diagnosis.

---

## 1. Methodology: The Hybrid Pipeline

Instead of retraining the entire neural network, we leveraged the pre-trained knowledge of EfficientNetB3 to transform raw images into high-dimensional feature vectors, which were then refined and classified by classical algorithms.

### 1.1 Feature Extraction
*   **Backbone:** EfficientNetB3 (Pre-trained on ImageNet).
*   **Extraction Layer:** Global Average Pooling (producing 1536-dimensional vectors).
*   **Freeze Status:** The backbone was completely frozen; no gradient updates were applied to the CNN.

### 1.2 Dimensionality Reduction & Preprocessing
*   **PCA (Principal Component Analysis):** Reduced feature markers from 1536 to **512 components**. This step was critical to remove noise and the "curse of dimensionality" before feeding data to the SVM.
*   **Scaling:** Standard Scaling (Z-score normalization) was applied, which is a prerequisite for convergence in SVMs.

### 1.3 Classification
*   **Model:** Support Vector Machine (SVM).
*   **Kernel:** RBF (Radial Basis Function).
*   **Hyperparameters:** `C=10`, `gamma='scale'`.

### 1.4 Dataset Standardization & Reproducibility
*   **Trimming:** The dataset was standardized to exactly **3,800 samples** (down-sampled from 3,811+).
*   **Rationale:** Trimming the data to a round number like 3,800 prevents **decimal values** (fractions of an image) when applying percentage-based splits. For instance, a 15% split of the original 3,811 samples would result in 571.65 images, which is not possible in practice.
*   **Exact Distribution (68/17/15 ratio):**
    *   **Training:** 2,584 images (68% of 3,800)
    *   **Validation:** 646 images (17% of 3,800)
    *   **Testing:** 570 images (15% of 3,800)
*   **Consistency:** A fixed `random_state=42` was used to ensure the selection is perfectly reproducible across different environments.

## 2. Experimental Results

The model was evaluated on a **held-out test set** of **570 images** (exactly 15% of the total 3,800 dataset). A "held-out" set means these images were strictly isolated and were never shown to the model during the training or validation phases. This ensures an **unbiased evaluation**, as it tests the model's performance on completely unseen data. This is the gold standard for verifying **generalization**—the model's ability to accurately predict new cases rather than just memorizing the training data.

### 2.1 Quantitative Metrics

| Metric | Score | Note |
| :--- | :--- | :--- |
| **Test Accuracy** | **99.12%** | **Highest among all analyzed models** |
| **Validation Accuracy** | 98.76% | Consistent with Test, indicating no overfitting |
| **Precision (Malignant)** | 99% | Extremely low False Positive rate |
| **Recall (Malignant)** | 100% | **Zero False Negatives** |

### 2.2 Class-Wise Performance
The confusion matrix indicates near-perfect separation of classes:
*   **Benign:** Precision 1.00 | Recall 0.98
*   **Malignant:** Precision 0.99 | Recall 1.00
*   **Normal:** Precision 0.99 | Recall 0.99

## 3. Comparative Analysis

| Model Architecture | Accuracy | Key Strength | Weakness |
| :--- | :--- | :--- | :--- |
| **EfficientNetB3 + SVM (Hybrid)** | **99.12%** | **Best Accuracy & Efficiency** | Complexity in pipeline (3 stages) |
| **EfficientNetB3 (Fine-Tuned)** | ~95% | Strong Baseline | Computationally expensive training |
| **ResNet101** | 94.12% | Deep Architecture | Prone to overfitting on small data |
| **Vision Transformer (ViT)** | ~92% | Global Context | Requires massive data to excel |
| **Custom YOLOv11** | 85.62% | 100% Malignant Recall | Poor Benign Classification |

## 4. Conclusion
The **EfficientNetB3 + PCA + SVM** pipeline proved to be the superior strategy for this lung cancer dataset. By decoupling feature learning (CNN) from classification (SVM), we achieved state-of-the-art performance. The addition of PCA likely filtered out irrelevant imaging artifacts, allowing the SVM to find an optimal decision boundary effectively. This approach is highly recommended for medical imaging tasks where datasets are moderately sized (thousands of images) but not massive (millions).
