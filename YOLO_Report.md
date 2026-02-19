# Adapting Object Detection Architectures for Medical Classification: A Custom YOLOv11 Study

**Abstract**
YOLO (You Only Look Once) architectures are traditionally dominant in object detection. This study explores the feasibility of adapting **YOLOv11's internal architectural blocks** (C3K2, SPPF, C2PSA) to construct a custom classifier for multi-class lung cancer diagnosis. Unlike standard transfer learning, we built a model from scratch inspired by YOLO's latest innovations. While the overall accuracy (**85.62%**) lagged behind CNN-based baselines (EfficientNet: ~95%), the model demonstrated a critical medical capability: **100% Recall (Sensitivity) for Malignant cases**, meaning it missed zero cancer cases in the test set.

---

## 1. Methodology: Deconstructing YOLO

Instead of using a pre-trained YOLO model for detection, we implemented a custom Keras model utilizing YOLOv11's specific building blocks to create a lightweight classifier.

### 1.1 Architectural Components
*   **C3K2 Blocks:** Optimized convolutional blocks for efficient feature extraction.
*   **SPPF (Spatial Pyramid Pooling - Fast):** Used to capture features at multiple scales.
*   **C2PSA (Cross-Stage Partial with Spatial Attention):** An attention mechanism to focus on relevant regions of the lung scans.
*   **Structure:** The model was dynamically built with a configurable number of "Stages" and "Base Filters" found via search.

### 1.2 Automated Optimization
We used **Optuna** to search for the optimal architecture and hyperparameters:
*   **Optimal Configuration:** 
    *   **Base Filters:** 64
    *   **Depth:** 4 Stages
    *   **Dropout:** ~0.39
*   **Fine-Tuning Strategy:** Freezing the first **40%** of layers was found to be the optimal balance between stability and plasticity.

## 2. Experimental Results

The model was evaluated on a held-out test set of 153 images.

### 2.1 Quantitative Metrics

| Metric | Score | Note |
| :--- | :--- | :--- |
| **Accuracy** | **85.62%** | Lower than ResNet/EfficientNet |
| **Precision** | 86.12% | Weighted Avg |
| **F1-Score** | 82.95% | Weighted Avg |

### 2.2 The "Sensitivity vs. Specificity" Trade-off
A deeper look at the confusion matrix reveals a fascinating behavior:

*   **Malignant Cases (Cancer):** **Precision: 0.87 | Recall: 1.00**
    *   The model identified **every single malignant case** correctly. In a medical screening context, this "High Sensitivity" is often more valuable than raw accuracy, as false negatives (missing cancer) are critical errors.
*   **Benign Cases:** **Recall: 0.10**
    *   The model struggled significantly to distinguish Benign cases, often confusing them with Normal or Malignant ones. This suggests the feature extractor, while sensitive to strong disease features (Malignancy), lacks the nuance to separate subtle Benign markers.

## 3. Conclusion
While the custom YOLOv11 classifier did not achieve state-of-the-art accuracy (85.62%), its **perfect sensitivity for cancer detection** warrants further investigation. It suggests that YOLO-like features are highly effective at flagging "abnormalities" but may require additional mechanisms to fine-grainedly classify the *type* of abnormality (Benign vs Malignant). This architecture could serve as an excellent "first-pass" screening tool to ensure no potential cancer cases are overlooked.
