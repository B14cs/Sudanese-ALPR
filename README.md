# Sudanese-ALPR 🇸🇩🚗🔎

A multi-stage **Automatic License Plate Recognition (ALPR)** project tailored for **Sudanese license plates**.

The pipeline uses **YOLOv11n** for:

- **LPD** (License Plate Detection) — bounding-box detection of plates.
- **LPS** (License Plate Segmentation) — two-class segmentation of plate regions:
  - **Class 0:** State Code region (*SCode*)
  - **Class 1:** Main number/character region (*Numbers*)

OCR is performed using **Google Cloud Vision API** (optional module; you can replace it with any local OCR).

---

## Main Notebooks

- **`ALPRs.ipynb`**  
  ✅ Full pipeline: preprocessing → detection/segmentation → OCR.

- **`LP_cropping.ipynb`**  
  ✅ Crops license plates to build/prepare dataset images (useful before annotation or model training).

- **`YOLOv11_Custom_Training_(LPD).ipynb`**  
  Training notebook for **License Plate Detection (LPD)**.

- **`YOLOv11_Custom_Training_(LPS).ipynb`**  
  Training notebook for **License Plate Segmentation (LPS)**.

---

## Repository Structure

```
Sudanese-ALPR/
├─ Sudanese License Plate Dataset/
│  ├─ train/
│  │  ├─ images/
│  │  └─ labels/
│  ├─ valid/
│  │  ├─ images/
│  │  └─ labels/
│  └─ test/
│     ├─ images/
│     └─ labels/
├─ ALPRs.ipynb
├─ LP_cropping.ipynb
├─ YOLOv11_Custom_Training_(LPD).ipynb
├─ YOLOv11_Custom_Training_(LPS).ipynb
└─ README.md
```

---

## Dataset Format (YOLO)

### A) Detection labels (LPD)
Each image has a `.txt` label file with bounding boxes:
```
<class_id> <x_center> <y_center> <width> <height>
```
All values are normalized to `[0,1]`.

Example (one plate class):
- `0 0.52 0.61 0.27 0.14`

### B) Segmentation labels (LPS)
YOLO-seg format uses polygons:
```
<class_id> x1 y1 x2 y2 x3 y3 ... xn yn
```
All coordinates are normalized to `[0,1]`.

Classes for segmentation:
- `0` = SCode
- `1` = Numbers

---

## Setup

### Install dependencies
```bash
pip install ultralytics opencv-python numpy matplotlib pandas google-cloud-vision
```

Recommended: use **Google Colab** for GPU training.

---

## Google Cloud Vision OCR (Optional)

If you want OCR using Cloud Vision:

1. Enable **Vision API** in Google Cloud.
2. Create a **Service Account** and download the JSON key.
3. Set credentials:

**Linux/macOS**
```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"
```

**Windows (PowerShell)**
```powershell
setx GOOGLE_APPLICATION_CREDENTIALS "C:\path\to\key.json"
```

Then run `ALPRs.ipynb`.

> If you don’t want cloud OCR, replace this stage with a local OCR module (EasyOCR/Tesseract/custom CRNN).

---

## Training

### 1) Create a `data.yaml`
Because your split folder is named **`valid`** (not `val`), your YAML should reflect that.

**Example — Detection (1 class: license_plate)**
```yaml
path: Sudanese License Plate Dataset
train: train/images
val: valid/images
test: test/images

names:
  0: license_plate
```

**Example — Segmentation (2 classes)**
```yaml
path: Sudanese License Plate Dataset
train: train/images
val: valid/images
test: test/images

names:
  0: SCode
  1: Numbers
```

> Make sure label paths match: `train/labels`, `valid/labels`, `test/labels`.

### 2) Run training notebooks
- `YOLOv11_Custom_Training_(LPD).ipynb` → detection training
- `YOLOv11_Custom_Training_(LPS).ipynb` → segmentation training

---

## Full Pipeline (Inference)

Run:
- **`ALPRs.ipynb`**

Typical outputs:
- plate bounding boxes (LPD)
- segmentation masks (LPS: SCode/Numbers)
- OCR output per region (state code text + main region text)
- optional formatting/validation rules

---

## Dataset Preparation (Cropping)

Use:
- **`LP_cropping.ipynb`**

Workflow:
1. Provide raw vehicle images path.
2. Run cropping to export plate crops.
3. Annotate crops (CVAT or other tool).
4. Convert annotations to YOLO format.

---

## Citation

If you use this repo or dataset in academic work, consider citing:

- An Automatic License Plate Recognition System for Sudanese Vehicles Using YOLO11 (once published)

- YOLO/Ultralytics

- COCO (if used for vehicle pretraining)