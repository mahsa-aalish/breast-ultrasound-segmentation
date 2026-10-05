# Breast Ultrasound Segmentation

A deep learning project for **breast lesion segmentation in ultrasound images** using a U-Net-based segmentation model.

The project uses the **BUSI (Breast Ultrasound Images) dataset** and includes dataset inspection, image and mask preprocessing, U-Net training, segmentation evaluation, and an additional experiment using a custom SACE image enhancement method.

---

## Dataset

The project uses the **BUSI (Breast Ultrasound Images) dataset**, stored locally as `Dataset_BUSI_with_GT`.

The dataset contains three classes:

* Normal
* Benign
* Malignant

The dataset used in this project contains:

* **780 ultrasound images**
* **780 primary image-mask pairs**

Some images contain additional ground-truth mask annotations. When multiple masks are available, they are combined into a single mask using a pixel-wise maximum operation.

The dataset is not included in this repository because of its size. The images were originally accessed through Google Drive.

---

## Preprocessing

The preprocessing pipeline includes:

* Converting ultrasound images to grayscale
* Resizing images and masks to **128 × 128**
* Normalizing pixel values to **[0, 1]**
* Adding a single channel dimension
* Combining multiple available masks into a single ground-truth mask

After preprocessing:

```text
Images: (780, 128, 128, 1)
Masks:  (780, 128, 128, 1)
```

The data was divided into:

* Training: **546 images**
* Validation: **117 images**
* Test: **117 images**

using a fixed `random_state=42`.

---

## U-Net Model

A U-Net architecture was implemented for binary lesion segmentation.

The model uses **Batch Normalization** within the convolutional blocks and **Dropout** in the encoder blocks.

### Encoder

The encoder contains four blocks with:

```text
64 → 128 → 256 → 512 filters
```

Each convolutional block consists of two `3 × 3` convolutional layers with ReLU activation, each followed by Batch Normalization.

Max Pooling is used for downsampling, followed by Dropout with a rate of `0.3`.

### Bottleneck

The bottleneck uses:

```text
1024 filters
```

### Decoder

The decoder progressively reconstructs the spatial resolution using transposed convolutions and skip connections:

```text
512 → 256 → 128 → 64 filters
```

The final layer uses a `1 × 1` convolution with sigmoid activation to generate the binary segmentation mask.

---

## Training

The baseline U-Net was trained using:

* Optimizer: **Adam**
* Loss: **Binary Cross-Entropy**
* Batch size: **16**
* Epochs: **40**

The model was evaluated using:

* Accuracy
* Mean IoU
* Dice Coefficient
* Average IoU

For the manually calculated Dice and IoU metrics, a prediction threshold of **0.3** was used.

---

## SACE Experiment

A custom **SACE** image enhancement transformation was also investigated.

SACE was applied to the training images before training a second U-Net model.

The same training configuration was used for comparison:

* Batch size: **16**
* Epochs: **40**
* Optimizer: **Adam**
* Loss: **Binary Cross-Entropy**

---

## Results

| Experiment   | Test Accuracy |       Dice | Average IoU |
| ------------ | ------------: | ---------: | ----------: |
| U-Net        |    **96.11%** | **72.93%** |  **64.86%** |
| U-Net + SACE |    **89.91%** | **41.78%** |  **32.30%** |

In this experiment, the baseline U-Net achieved better segmentation performance than the U-Net trained with SACE-enhanced images.

---

## Technologies

* Python
* TensorFlow / Keras
* OpenCV
* NumPy
* Pandas
* Matplotlib
* Albumentations
* Scikit-learn
* Google Colab

---

## Repository Contents

The repository currently contains the project notebook:

```text
Breast_Ultrasound_Segmentation.ipynb
```

The dataset is not included because of its size.

The notebook contains the complete workflow from dataset inspection and preprocessing to U-Net training, evaluation, and the SACE experiment.
