# YOLO11-Seg: Autonomous Concrete Crack Instance Segmentation

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=flat&logo=pytorch&logoColor=white)](https://pytorch.org/)
[![Ultralytics YOLO](https://img.shields.io/badge/Ultralytics-YOLO11-red.svg)](https://docs.ultralytics.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

*Read this in Vietnamese: [Tiếng Việt](#giới-thiệu-dự-án-tiếng-việt)*

An end-to-end research and production-ready implementation of **YOLO11 Instance Segmentation (`yolo11n-seg`)** for detecting and segmenting surface cracks on concrete structures. This project is optimized for structural health monitoring (SHM), enabling automated pavement, bridge, and building inspection.

---

## Bounding Box vs. Instance Segmentation

| Bounding Box (Object Detection) | Instance Segmentation (Our Approach) |
| :--- | :--- |
| Encloses cracks in rectangular boxes. | Identifies precise, pixel-level polygon masks. |
| Cannot measure crack width (aperture). | Enables calculation of crack area, perimeter, and average width. |
| Includes background noise in the box. | Isolates the crack geometry, facilitating skeletonization & path tracking. |

---

## Key Features
- **Exploratory Data Analysis (EDA)**: Interactive scripts to visualize raw images overlaid with ground-truth polygon masks.
- **Transfer Learning Pipeline**: Train a lightweight, fast, and accurate `yolo11n-seg` backbone.
- **Robust Evaluation**: Automated metric computation and loss plotting (`results.csv`).
- **Edge Deployment Ready**: Export trained PyTorch weights (`.pt`) to optimized ONNX formats.
- **Real-time Webcam Stream**: Test the trained model in real-time using opencv-based camera feeds.

---

## Directory Structure
```directory
crack_detect/
├── .venv/                     # Python Virtual Environment
├── images/                    # Dataset Images
│   ├── train/                 # 3,717 Training images
│   ├── val/                   # 200 Validation images
│   └── test/                  # 112 Testing images
├── labels/                    # YOLO Polygon Labels
│   ├── train/
│   ├── val/
│   └── test/
├── runs/                      # Training & Inference Logs
│   └── segment/
│       └── runs/segment/
│           └── crack_seg_train/
│               ├── weights/
│               │   ├── best.pt  # Best epoch weights
│               │   └── last.pt  # Last epoch weights
│               └── results.csv  # Training metric log
├── crack-seg.yaml             # Dataset Configuration file
├── webcam-config.yaml         # Webcam CLI prediction config
├── requirements.txt           # Python Dependency list
└── how_to_train_ultralytics_yolo_on_crack_segmentation_dataset.ipynb  # Main Notebook
```

---

## Installation & Setup

1. **Clone the repository**:
   ```bash
    git clone https://github.com/TuLeTanh/Crack-Detection.git
    cd Crack-Detection
   ```

2. **Set up a Virtual Environment**:
   ```bash
   python -m venv .venv
   # Activate on Windows:
   .venv\Scripts\activate
   # Activate on Linux/macOS:
   source .venv/bin/activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

---

## Quickstart Guide

### 1. Training & Validation (Notebook)
Open the Jupyter notebook `how_to_train_ultralytics_yolo_on_crack_segmentation_dataset.ipynb` in your IDE or Jupyter interface and run the cells sequentially.

### 2. Training (Command Line)
You can also run training directly from your terminal:
```bash
yolo segment train model=yolo11n-seg.pt data=crack-seg.yaml imgsz=640 epochs=50 batch=8 project=runs/segment/runs/segment name=crack_seg_train exist_ok=True
```

### 3. Model Export (ONNX)
Export the trained PyTorch weights to ONNX format for web/mobile deployment:
```python
from ultralytics import YOLO
model = YOLO("runs/segment/runs/segment/crack_seg_train/weights/best.pt")
model.export(format="onnx", simplify=True)
```

### 4. Real-time Webcam Testing
Run predictions on your default camera feed (camera `0`):
```bash
yolo cfg=webcam-config.yaml
```

---

## Quantitative Evaluation Results

The model was trained for **50 epochs** using the **AdamW** optimizer with a batch size of **8** at a resolution of **640x640**.

Below are the final evaluation metrics on the validation dataset:

| Task / Metric | Precision | Recall | mAP@50 | mAP@50-95 |
| :--- | :---: | :---: | :---: | :---: |
| **Bounding Box Detection (B)** | 81.90% | 74.51% | 79.92% | 61.99% |
| **Segmentation Mask (M)** | 72.51% | 65.86% | 64.85% | 22.29% |

*The high bounding box mAP@50 (79.92%) and mask mAP@50 (64.85%) demonstrate robust generalization for identifying and localizing surface cracks under varying lighting and texture conditions.*

---

## Giới thiệu Dự án (Tiếng Việt)

Dự án này cung cấp một quy trình hoàn chỉnh (end-to-end) để huấn luyện, đánh giá và triển khai mô hình **Phân đoạn thực thể (Instance Segmentation) YOLO11** (`yolo11n-seg`) nhằm tự động nhận diện và quét vết nứt trên các bề mặt bê tông (như cầu đường, tòa nhà, mặt đường).

### Điểm nổi bật:
- **Độ chính xác cao**: Sử dụng mô hình YOLO11-seg thế hệ mới giúp vẽ đường biên bao quanh vết nứt ở cấp độ pixel.
- **Thống kê chi tiết**: File notebook nghiên cứu đi kèm có biểu đồ trực quan hóa dữ liệu huấn luyện, so sánh ảnh gốc và ảnh dự đoán mặt nạ nứt.
- **Tối ưu hóa phần cứng**: Hỗ trợ xuất mô hình sang định dạng **ONNX** nhẹ hơn, chạy mượt mà trên CPU và thiết bị nhúng.
- **Webcam Real-time**: File `webcam-config.yaml` cho phép mở camera máy tính và quét vết nứt theo thời gian thực ngay lập tức.

### Cách chạy nhanh:
1. Cài đặt thư viện: `pip install -r requirements.txt`
2. Mở file notebook và chạy từng ô lệnh để xem trực quan hóa dữ liệu mẫu, biểu đồ huấn luyện và kết quả dự đoán trên tập test.
3. Chạy thử nghiệm webcam: `yolo cfg=webcam-config.yaml`

---

## References & Credits
- [Ultralytics YOLO11 Documentation](https://docs.ultralytics.com/)
- Concrete Crack Segmentation Dataset hosted by Ultralytics assets.

## License
This project is licensed under the MIT License - see the LICENSE file for details.
