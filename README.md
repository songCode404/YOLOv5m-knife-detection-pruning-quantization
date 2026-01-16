# 🔪 Knife-Detection-YOLOv5-Optimization

### 실시간 감시 시스템을 위한 YOLOv5m 기반 흉기 탐지 모델 최적화 파이프라인

---

## 📌 0. 프로젝트 개요 (Overview)
본 프로젝트는 **보안 및 감시 시스템의 실시간 대응 능력**을 극대화하기 위해 수행되었습니다. 단순히 높은 정확도를 가진 모델을 만드는 것에 그치지 않고, 제한된 컴퓨팅 자원에서도 빠르게 작동할 수 있도록 **모델 압축(Model Compression)** 기법을 적용하여 **성능(Recall)과 추론 속도 사이의 최적점**을 찾는 데 집중하였습니다.

---

## ⚙️ 1. Installation & Setup

본 프로젝트는 Ultralytics의 YOLOv5 공식 레포지토리를 기반으로 최적화되었습니다.

```bash
# 1. YOLOv5 레포지토리 클론
git clone [https://github.com/ultralytics/yolov5](https://github.com/ultralytics/yolov5)
cd yolov5

# 2. 필수 라이브러리 설치
pip install -r requirements.txt
pip install onnx onnxruntime-gpu # 최적화 및 양자화용```

## 🛠 2. 기술적 특징 (Technical Highlights)
- **Base Model**: YOLOv5m (성능 확보를 위해 YOLOv5s 대비 파라미터가 풍부한 m 모델 채택)
- **Optimization Pipeline**: 
  - **L1-norm 기반 Unstructured Pruning**: 모델 가중치의 30%를 제거하여 연산 효율성 확보
  - **Fine-tuning**: Pruning 후 일시적으로 하락한 성능을 복구하기 위한 전략적 재학습 수행
  - **Quantization (ONNX)**: 배포 포맷 최적화 및 FP16 양자화를 통한 추론 가속화

---

## 🔄 3. 문제점 분석 및 해결 방안 (Optimization Pipeline)



### **Issue 1. 가지치기 후 성능 하락 (Performance Drop)**
- **분석**: 가중치를 30% 제거하는 강력한 가지치기 직후, 모델의 지능을 담당하는 파라미터가 소실되어 Recall 수치가 하락함.
- **해결**: 하이퍼파라미터를 정교하게 조정한 **Fine-tuning** 과정을 통해 모델이 남은 가중치를 효율적으로 재배치하도록 유도, 원본(baseline)과 유사하게 성능을 복구함.

### **Issue 2. 추론 환경의 제약 1 (Inference Optimization 1)**
- **분석**: PyTorch 기본 모델(`.pt`)은 범용적인 보안 장비나 클라우드 배포 환경에서 추론 속도를 최대로 끌어올리는 데 한계가 있음.
- **해결**: 모델을 **ONNX 포맷**으로 변환하고 **FP16(반정밀도) 양자화**를 적용하여 연산 부하를 절반으로 줄이고 다양한 하드웨어 가속기와의 호환성을 확보함.

### **Issue 3. 추론 환경의 제약 2 (Inference Optimization 2)**
- **문제점**: 아두이노를 활용한 실시간 자율주행 환경에서는 baseline 모델인 yolov5m을 그대로 넣으면 추론 시간에 딜레이가 심함
- **분석**: 제한된 환경에 맞는 경량화 및 양자화가 필요함
- **해결**: **30% pruning** 후 재학습/fine-tuing 실시 + **FP32 to FP16, INT8 양자화**를 통하여 연산 부하를 줄여 제한된 환경에서도 빠르게 탐지할 수 있도록 함.

### **Issue 4. 오탐의 문제**
- **문제점**: 흉기(칼)의 크기가 작거나, 칼과 비슷한 물체를 칼로 오탐하는 경우가 발생
- **분석**: **negative sample**을 추가적인 학습이 필요함
- **해결**: 칼과 유사한 물체가 있는 **background sample(negative sample)**을 추가하고 **Hard Case**를 추가하여 재학습이 필요함 -> 수행 예정

---

## 📊 4. 성능 분석 (Performance Analysis)



각 최적화 단계별 지표 변화를 통해 모델의 효율성을 입증하였습니다.

| 단계 (Model Status) | Precision | Recall | mAP@0.5 | 성과 (Remarks) |
| :--- | :---: | :---: | :---: | :--- |
| **Step 1: Baseline** | 0.811 | 0.695 | 0.700 | 원본 모델 기준 성능 확보 |
| **Step 2: Pruning & Tuned** | **0.821** | **0.691** | **0.699** | **정밀도 상승 및 구조 슬림화** |
| **Step 3: Final (ONNX)** | 0.801 | 0.658 | 0.642 | 배포 최적화 및 포맷 변환 완료 |

---

## ✨ 5. 핵심 성과 (Key Insight)

- **정밀도 역전 현상 발견**: Pruning 과정에서 불필요한 노이즈 가중치를 제거함으로써, 원본 모델보다 **정밀도(Precision)가 약 1.2% 향상**되는 최적화 효과를 거둠.
- **경량화 파이프라인 구축**: 학습부터 가지치기, 재학습, 양자화로 이어지는 **모델 최적화 엔지니어링 파이프라인**을 직접 설계하고 구현하여 실무적인 인공지능 배포 역량을 증명함.
  

---

## 🚀 6. Future Work (v2.0)

현재의 성과를 바탕으로 모델의 완성도를 높이기 위한 고도화를 계획하고 있습니다.

- [ ] **mAP 0.8 달성**: Hard Negative Mining 기법을 도입하여 탐지 성능 고도화
- [ ] **오탐 방지 강화**: 일상용품(스마트폰 등)을 포함한 **Background Image** 보강 학습을 통해 False Positive Rate 개선
- [ ] **TensorRT 가속**: 하드웨어 가속기(GPU) 최적화를 통한 추론 속도 1ms 이하 단축 시도
