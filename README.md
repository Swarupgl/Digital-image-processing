# 🦶 Thermal Foot Ulcer Detection: DIP vs. CNN 🌡️

This repository contains a comparative study developed for a **Digital Image Processing** course. The project explores how traditional DIP techniques (Segmentation, Morphological Ops, Color Space Theory) compare against and complement Deep Learning (CNNs) in the detection of diabetic foot ulcers from thermographic images.

---

## 🎯 Project Objectives

The goal is to classify thermal foot images into two categories: **Control Group** (Healthy) and **DM Group** (Diabetic Mellitus).

We compare two distinct methodologies:

1. **Traditional DIP Approach:** Manual feature extraction using HSV thresholding and Area-Ratio calculation.
2. **Hybrid CNN Approach:** Using DIP as a "Semantic Pre-processor" to generate 5-channel feature maps for a Convolutional Neural Network.

---

## 🛠️ Digital Image Processing Pipeline

The core of this project is the **Feature Engineering** phase, where raw pixels are transformed into anatomical and thermal intelligence.

### 1. Pre-processing & ROI Extraction

* **Grayscale Conversion:** Simplifies the image to intensity values.
* **Global Thresholding:** Isolates the foot silhouette (Region of Interest) from the background.
* **Morphological Smoothing:** Uses `Closing` and `Opening` to eliminate sensor noise and fill gaps in the foot mask.

### 2. Thermal Segmentation (HSV Space)

Instead of RGB, we use the **HSV (Hue, Saturation, Value)** color space to decouple "color" (temperature) from "brightness":

* **Red Mask:** Detects the "White Hot" zones ($0-10$ and $170-180$ Hue).
* **Yellow Mask:** Detects "Hot" zones ($20-40$ Hue).
* **Combined Mask:** The union of all high-temperature pixels.

### 3. Feature Mapping (The 5-Channel Volume)

For the CNN, we do not provide raw RGB. We provide a **One-Hot Encoded Mask** ($128 \times 128 \times 5$):

* `Channel 0`: Background
* `Channel 1`: Normal Skin
* `Channel 2`: Yellow Hotspots
* `Channel 3`: Combined Heat
* `Channel 4`: Red Hotspots (Critical Zone)

---

## 🧠 Model Architecture (CNN)

The hybrid model is a lightweight CNN designed to recognize **spatial patterns** in the DIP masks:

* **Input Layer:** $128 \times 128 \times 5$ (DIP-segmented map)
* **Feature Extraction:** 2D Convolutional layers with ReLU activation.
* **Reasoning:** Global Average Pooling followed by a Dense (Fully Connected) stack.
* **Output:** Sigmoid neuron (Probability of DM).

---

## 📊 Performance Head-to-Head

| Metric | Traditional DIP (Rule-Based) | Hybrid DIP-CNN |
| --- | --- | --- |
| **Accuracy** | ~72% | **~80.1%** |
| **Precision** | ~78% | **~90.4%** |
| **Complexity** | Low / Deterministic | High / Stochastic |
| **Interpretability** | 🟢 High (Direct pixel math) | 🟡 Medium (Guided by DIP) |

### Key Finding

The **Hybrid CNN** achieved significantly higher precision ($90.4\%$). While the traditional DIP method only looks at the *amount* of heat, the CNN learns the *location and shape* of that heat, making it much more effective at identifying clinical patterns.

---

## 📂 Repository Structure

```bash
├── data/                    # Dataset (Thermal images)
├── notebooks/
│   └── thermal_analysis.ipynb # Main Kaggle/Jupyter Notebook
├── src/
│   ├── dip_utils.py          # HSV masks & segmentation functions
│   └── model.py              # CNN architecture
├── results/
│   └── val_predictions.csv   # Validation metrics
└── README.md

```

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install opencv-python tensorflow numpy pandas scikit-learn tqdm

```

### Running the Project

1. Clone the repo: `git clone https://github.com/your-username/thermal-foot-dip.git`
2. Update dataset paths in the script.
3. Run the training script:

```python
python full_pipeline.py

```

---

## 📜 Conclusion

This project demonstrates that **DIP is not obsolete in the age of AI**. By using DIP to pre-process medical images, we reduce the search space for the CNN, allowing a simpler model to achieve higher accuracy with less data.
