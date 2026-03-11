# 🔪 Knife-Detection-YOLOv5-Optimization (v2.0)

### 실시간 감시 시스템을 위한 YOLOv5m 기반 흉기 탐지 모델 고도화 및 최적화 파이프라인

[![YOLOv5](https://img.shields.io/badge/Model-YOLOv5m-blue)](https://github.com/ultralytics/yolov5)
[![mAP](https://img.shields.io/badge/mAP@0.5-0.922-green)](https://github.com/songCode404)
[![License](https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey)](https://creativecommons.org/licenses/by-sa/4.0/)

### 📦 Model Weights
[Releases](https://github.com/songCode404/YOLOv5m-knife-detection-pruning-quantization/releases/tag/v2.0.0) 탭에서 다운로드하실 수 있습니다.
---

## 📌 0. 프로젝트 개요 (Overview)
본 프로젝트는 보안 및 감시 시스템의 실시간 대응 능력을 극대화하기 위해 수행되었습니다. 
단순히 높은 정확도를 가진 모델을 만드는 것에 그치지 않고, 제한된 컴퓨팅 자원에서도 빠르게 작동할 수 있도록 **데이터 엔지니어링**과 **모델 압축(Model Compression)** 기법을 병행하여 성능과 속도의 최적점을 달성했습니다.

---

## 🛠 1. 기술적 특징 (Technical Highlights)
- **Base Model**: YOLOv5m
- **Optimization Pipeline**: 
  - **Dataset Re-engineering**: 데이터셋 불균형 해소
  - **L1-norm 기반 Unstructured Pruning**: 모델 가중치의 30%를 제거하여 연산 효율성 확보
  - **Fine-tuning**: Pruning 후 하락한 성능을 v2 데이터셋으로 완벽 복구 (99.8% recovery)
  - **Quantization (ONNX)**: FP16 양자화를 통한 추론 부하 절감 및 배포 최적화

---

## 🔄 2. 고도화 히스토리 (Development History)

### **v1.0.0: Optimization Baseline**
* **성과**: Pruning 및 Quantization 파이프라인 구축 및 모델 경량화 가능성 확인.
* **한계**: 데이터셋 편향으로 인해 mAP가 0.7 수준에 머물며 실전 투입에 한계가 있음.

### **v2.0.0: Performance Breakthrough (Latest)**
* **문제 분석**: 검증 세트가 학습 세트보다 많은 구조(3:7) 및 배경 학습 부족 진단.
* **해결 1 (Data Re-balancing)**: 전체 데이터를 **8:2 비율로 재분할**하여 학습 풍부도 확보.
* **해결 2 (Background Mining)**: 오탐지가 잦은 환경의 **Background sample**을 추가하여 모델의 변별력 강화.
* **해결 3 (Advanced Augmentation)**: CCTV 저화질 대응을 위해 **Blur, MedianBlur** 증강 기법 도입.

---

## 📊 3. 성능 분석 (Performance Analysis)

동일한 검증 환경(v2-valid set)에서 측정한 단계별 지표 변화입니다.

| 단계 (Model Status) | Precision | Recall | **mAP@0.5** | 성과 (Remarks) |
| :--- | :---: | :---: | :---: | :--- |
| **Step 1: Baseline (v1)** | 0.811 | 0.695 | 0.700 | 초기 파이프라인 구축 |
| **Step 2: Refined (v2)** | **0.883** | **0.886** | **0.923** | **데이터 재설계로 mAP 31% 상승** |
| **Step 3: Pruned & Tuned** | **0.885** | **0.885** | **0.922** | **30% 경량화 및 성능 복구 완료** |



---

## 🔍 4. 문제점 분석 및 해결 방안 (Issue Tracking)

### **Issue 1. 가지치기 후 성능 하락 (Performance Drop)**
- **분석**: 가중치 30% 제거 직후 파라미터 소실로 인해 성능 하락 발생.
- **해결**: 고도화된 v2 데이터셋을 활용한 **Fine-tuning**으로 모델의 가중치를 최적 재배치하여 원본급 성능 복구.

### **Issue 2. 추론 환경의 제약 (Inference Optimization)**
- **분석**: 제한된 임베디드 환경(아두이노, 제슨 등)에서 YOLOv5m의 딜레이 발생.
- **해결**: **FP16 양자화** 및 **ONNX 변환**을 통해 연산 부하를 줄여 실시간 탐지 속도(1.9ms) 확보.

### **Issue 3. 오탐의 문제 (False Positive)**
- **분석**: 칼과 유사한 질감이나 형태를 가진 배경 물체에 대한 오탐지 발생.
- **해결**: **Hard Negative Mining** 기법을 적용하여 칼이 없는 배경 이미지를 대폭 보강하고 증강 기법을 고도화함.

### **Issue 4. 1.9ms도 너무 늦은 탐지 시간**
- **분석**: Yolo를 사용함에도 너무 늦은 탐지 시간 -> 더 낮출 수 있음.
- **해결**: **추가적인 경량화 및 fine-tuning** 추가적인 경량화 30% -> 40% or 50% 경량화 진행
---

## ✨ 5. 핵심 성과 (Key Insight)

1. **데이터가 모델보다 중요하다**: 모델 구조 변경 없이 데이터셋 재배치와 배경 이미지 보강만으로 **mAP 0.22(31%) 상승** 견인.
2. **효율적 경량화**: 30%의 파라미터를 0으로 만들었음에도 성능 하락을 0.1% 내로 방어하며 **희소 모델(Sparse Model)** 최적화 성공.
3. **실무형 파이프라인**: 데이터 설계부터 학습, 경량화, 양자화, 배포 포맷 변환까지 이어지는 **End-to-End AI 엔지니어링** 역량 증명.

---

## ✨ 6. 성과 사진

<table idx="1">
  <tr>
    <td align="center"><b>Knife Detection</b></td>
    <td align="center"><b>Streaming Test</b></td>
    <td align="center"><b>RC Car</b></td>
  </tr>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/d9b64080-6788-4ae4-817b-f534cc5a8fb9" width="300"></td>
    <td><img src="https://github.com/user-attachments/assets/49688dd0-e29e-4ba2-b227-7934f5c8a2fb" width="300"></td>
    <td><img src="https://github.com/user-attachments/assets/922f36bc-63f5-41d0-8c87-00877f9149f3" width="300"></td>
  </tr>
</table>


---

## 🚀 7. Future Work (v3.0)

- [ ] **TensorRT 가속**: NVIDIA GPU 환경 최적화를 통한 1ms 이하 추론 속도 도전.
- [ ] **Global Scaling**: 가위, 송곳 등 유사 흉기 클래스 확장 및 통합 탐지 모델 구축.
- [ ] **Edge Deployment**: 실제 임베디드 보드 탑재 및 실시간 스트리밍 테스트.
