
# Result and Discussion

This section presents the performance evaluation of various deep learning models for multi-class lung cancer classification (Benign, Malignant, and Normal cases). A total of five models were evaluated: ResNet101, Vision Transformer (ViT), YOLOv11 (Classifier), EfficientNet-B3, and our proposed Hybrid model (EfficientNet-B3 + PCA + SVM).

## Comparative Performance Analysis

The models were evaluated on a reserved test set. The metrics used for evaluation include Accuracy, Precision, Recall, F1-Score, and Specificity. 

### Performance Table

The following table summarizes the macro-averaged metrics for each model:

| Model | Accuracy (%) | Precision | Recall | F1-Score | Specificity |
| :--- | :---: | :---: | :---: | :---: | :---: |
| YOLOv11 (Classifier) | 85.62 | 0.8914 | 0.5860 | 0.6119 | 0.8715 |
| Vision Transformer (ViT) | 91.50 | 0.9325 | 0.6895 | 0.7217 | 0.9300 |
| ResNet101 | 94.12 | 0.8124 | 0.7491 | 0.7630 | 0.9750 |
| EfficientNet-B3 | 94.77 | 0.8479 | 0.8561 | 0.8513 | 0.9750 |
| **Hybrid (Proposed)** | **99.12** | **0.99** | **0.99** | **0.99** | **0.9955** |

> [!IMPORTANT]
> The **Hybrid Model** significantly outperforms all other architectures, achieving an outstanding accuracy of **99.12%**. This superiority is attributed to the combination of EfficientNet-B3's robust feature extraction, PCA's dimensionality reduction (which reduces noise), and SVM's effective classification boundary.

## Discussion

### 1. Superiority of the Hybrid Approach
The hybrid model demonstrates a substantial leap in performance compared to end-to-end deep learning models. While EfficientNet-B3 alone achieved a high accuracy of 94.77%, the integration of PCA and SVM pushed the performance to 99.12%. This suggests that for this specific medical imaging dataset, the statistical separation provided by SVM on a reduced feature space is more effective than the standard softmax classification head.

### 2. Model Robustness (Specificity)
Specificity is a critical metric in medical diagnosis to ensure that healthy (Normal) or Benign cases are not misclassified as Malignant. Our proposed **Hybrid model achieves a Specificity of 0.9955**, indicating near-perfect ability to identify negative cases. In contrast, models like YOLOv11 showed lower recall and specificity for benign cases, which are fewer in the test set.

### 3. Class-wise Performance
Analysis of the classification reports reveals that "Malignant" cases are generally better identified by all models due to higher representation in the dataset. However, the Hybrid model shows balanced performance across all three classes, maintaining high precision and recall even for the smaller "Benign" class.

### 4. Conclusion on Architecture Choice
While ViT and YOLO represent state-of-the-art architectures, the ResNet and EfficientNet families proved more adaptable to this classification task. The EfficientNet-B3 model, in particular, served as a powerful baseline, which was further enhanced by the Hybrid feature extraction pipeline.
