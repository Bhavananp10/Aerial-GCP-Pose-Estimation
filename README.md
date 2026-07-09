# Aerial GCP Pose Estimation

## Overview

This project implements an end-to-end deep learning pipeline for automatic Ground Control Point (GCP) pose estimation from aerial drone imagery.

The objective is to simultaneously perform:

1. **Keypoint Localization** – Predict the center pixel coordinates (x, y) of the GCP marker.
2. **Shape Classification** – Classify the marker into one of the following categories:
   - Cross
   - Square
   - L-Shape

The problem is formulated as a **multi-task learning** task combining regression and classification.

---

# Dataset

## Training Dataset

- Total images provided: **1000**
- Images used for training: **996**
- Four samples were excluded because they did not contain the required `verified_shape` annotation.

## Test Dataset

- Total images: **300**

## Image Resolution

- Approximately **4096 × 3068** pixels

## Marker Classes

- Cross
- Square
- L-Shape

---

# Exploratory Data Analysis (EDA)

Before training, the dataset was explored to understand its characteristics.

The following analyses were performed:

- Total number of training images
- Class distribution
- Image resolution
- Sample image visualization
- Ground truth annotation visualization
- Cropped marker visualization
- Coordinate distribution

## Observations

- Images are high-resolution aerial drone images.
- Ground Control Point (GCP) markers occupy a very small portion of each image.
- Significant background variation exists across different mining locations.
- Images contain varying lighting conditions and viewpoints.
- Marker orientation varies across the dataset.
- Class imbalance exists:
  - Cross: **177**
  - Square: **328**
  - L-Shape: **491**

---

# Network Architecture

A **multi-task deep learning model** was implemented using a pretrained **ResNet18** backbone.

## Backbone

- Pretrained ResNet18

## Regression Head

Predicts the GCP center coordinates:

- x-coordinate
- y-coordinate

## Classification Head

Predicts the GCP marker shape:

- Cross
- Square
- L-Shape

## Architecture Rationale

ResNet18 was selected because it provides an excellent balance between computational efficiency and feature extraction capability.

Using a shared backbone allows both localization and classification tasks to learn common visual features while maintaining separate prediction heads for each task.

---

# Training Strategy

## Image Preprocessing

- Images resized to **512 × 512**
- Pixel values normalized to **[0,1]**

## Dataset Split

- Training: **80%**
- Validation: **20%**

## Optimizer

- Adam

## Learning Rate

- 0.0001

## Loss Functions

### Coordinate Regression

- Mean Squared Error (MSE)

### Shape Classification

- Cross Entropy Loss

### Combined Loss

Total Loss = Regression Loss + 0.1 × Classification Loss

## Training Epochs

- 3

---

# Training Results

The model was successfully trained for three epochs.

Training Loss:

| Epoch | Loss |
|-------|------|
| 1 | 75248.39 |
| 2 | 66059.97 |
| 3 | 42551.82 |

The decreasing training loss indicates that the model successfully learned both localization and classification tasks.

---

# Challenges Encountered

Several practical challenges were encountered during implementation:

- High-resolution aerial images increased computational requirements.
- GCP markers occupy only a very small region of the full image.
- Large background variation across different projects.
- Class imbalance among marker categories.
- A few annotation entries were missing the required `verified_shape` field.

## Mitigation Strategies

- Images were resized to 512 × 512 for efficient training.
- Samples without `verified_shape` were excluded from training.
- A pretrained ResNet18 backbone was used for robust feature extraction.
- A multi-task architecture was implemented to jointly learn localization and classification.

---

# Model Weights

The trained model weights are provided as:

```
gcp_model.pth
```

If the notebook is executed again, the weights can be regenerated using the provided training pipeline.

---

# Running Inference

To reproduce the predictions:

1. Mount Google Drive and update the dataset paths.
2. Load the trained model (`gcp_model.pth`).
3. Create the test dataset and DataLoader.
4. Execute the inference section of the notebook.
5. The generated predictions will automatically be saved as:

```
predictions.json
```

---

# Output Format

The generated prediction file follows the required format:

```json
{
  "project/survey/GCP/image.JPG": {
    "mark": {
      "x": 1234.56,
      "y": 987.43
    },
    "verified_shape": "Square"
  }
}
```

---

# Submission Contents

This submission contains:

- GCP_Assignment.ipynb
- README.md
- gcp_model.pth
- predictions.json

---

# Future Improvements

Potential improvements include:

- Stronger data augmentation.
- Higher-capacity backbones such as EfficientNet or ConvNeXt.
- Heatmap-based keypoint localization for improved coordinate accuracy.
- Weighted sampling to address class imbalance.

---

# Conclusion

An end-to-end computer vision pipeline was successfully developed for aerial GCP pose estimation.

The implemented solution performs simultaneous coordinate regression and marker shape classification using a multi-task deep learning architecture.

The trained model successfully generates predictions for all images in the provided test dataset and outputs the results in the required JSON format.
