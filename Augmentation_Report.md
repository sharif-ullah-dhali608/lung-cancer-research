# Enhancing Lung Cancer Classification through Advanced Data Balancing and Contrast Optimization

**Abstract**
Medical image datasets frequently suffer from severe class imbalance and variable image quality, hindering the performance of deep learning models. This report details a robust **Data Preprocessing and Augmentation Pipeline** designed to address these challenges for a multi-class lung cancer dataset. Unlike standard approaches that resize images early in the pipeline, we implement a **"No-Resize" augmentation strategy** to preserve high-frequency details. Furthermore, we integrate **CLAHE (Contrast Limited Adaptive Histogram Equalization)** and **Gamma Correction** to standardize image contrast before model ingestion. This pipeline successfully balances the dataset to **1,200 images per class**, creating a standardized foundation for subsequent model training.

---

## 1. Introduction
In deep learning-based medical diagnostics, the quality and quantity of training data are paramount. Our initial dataset exhibited significant class imbalance (Benign: 100 vs. Malignant: 1000 vs. Normal: 500), which biases models towards the majority class. Additionally, lighting conditions in medical scans can vary. We propose a preprocessing workflow that decouples augmentation from resizing, ensuring that synthetic samples are generated at full resolution to maximize data fidelity.

## 2. Methodology

### 2.1 The "No-Resize" Strategy
Standard pipelines often resize images to the model input size (e.g., $224 \times 224$) *before* augmentation. This can blur fine details and introduce artifacts.
*   **Our Approach:** We load and process all images in their **Original Resolution**.
*   **Benefit:** Geometric transformations (rotation, shear, zoom) utilize the maximum available pixel information, resulting in higher-quality augmented samples compared to upscaling low-resolution inputs.

### 2.2 Advanced Contrast Enhancement
To mitigate variations in scan acquisition, we applied a custom preprocessing function ($f_{prep}$) to every image:
1.  **CLAHE (Contrast Limited Adaptive Histogram Equalization):**
    *   Applied to the **L-channel** (Lightness) in the LAB color space.
    *   **Result:** Enhances local contrast without amplifying noise in homogenous regions, making tumor boundaries more distinct.
2.  **Gamma Correction ($\gamma=0.5$):**
    *   Non-linear adjustment to brighten dark regions.
    *   **Result:** Reveals details hidden in shadows without washing out bright areas.

### 2.3 Balancing via Augmentation
We employed an `ImageDataGenerator` to synthetically expand the minority classes ("Benign" and "Normal") to match the prevalent "Malignant" class.
*   **Target:** 1,200 images per class.
*   **Augmentation Parameters:**
    *   **Rotation:** $\pm 25^{\circ}$
    *   **Shift (W/H):** $15\%$
    *   **Shear:** $15\%$
    *   **Zoom:** $0.8x - 1.2x$
    *   **Flips:** Horizontal and Vertical
    *   **Brightness:** $0.7 - 1.3$
    *   **Channel Shift:** $30.0$

## 3. Implementation Results
The pipeline was executed on the raw training data.

| Class | Original Count | Generated Samples | Final Count | Status |
| :--- | :--- | :--- | :--- | :--- |
| **Benign** | 100 | +1,100 | **1,200** | Balanced |
| **Malignant** | 1,000 | +200 | **1,200** | Balanced |
| **Normal** | 500 | +700 | **1,200** | Balanced |

**Output:**
The final dataset consists of **3,600 standardized images**. All images are saved in the `Augmented_Dataset_Enhanced` directory and compressed for transfer to the training environment.

## 4. Conclusion
This preprocessing module provides a clean, balanced, and contrast-enhanced dataset. By prioritizing original resolution and local contrast (CLAHE), we ensure that downstream models (EfficientNet, ViT) receive the highest quality input features possible, potentially improving classification accuracy and robustness.
