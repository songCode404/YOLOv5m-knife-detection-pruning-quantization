# YOLOv5m-knife-detection-pruning-quantization

# 🔪 Knife-Detection-YOLOv5-Optimization

실시간 감시 시스템 환경에 최적화된 저지연(Low-latency) 흉기 탐지 모델 개발 프로젝트입니다.

## 🚀 Key Features
- **Base Model**: YOLOv5m
- **Optimization**: L1-norm Unstructured Pruning (30%)
- **Model Format**: PyTorch (.pt) → ONNX (FP16)
- **Results**: 원본 모델 대비 성능(Recall) 99% 유지 및 모델 구조 경량화

## 📊 Performance Comparison
| Model | Precision | Recall | mAP50 |
| :--- | :---: | :---: | :---: |
| Baseline (v5m) | 0.811 | 0.695 | 0.700 |
| **Pruned & Tuned** | **0.821** | **0.691** | **0.699** |
| Final (ONNX) | 0.801 | 0.658 | 0.642 |

## 🛠 Future Work (v2.0)
- [ ] mAP 0.8 달성을 위한 Hard Negative Mining 수행
- [ ] Background 데이터 추가를 통한 오탐율(False Positive) 개선
