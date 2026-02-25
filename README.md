# AICV03 종합 프로젝트 (2026/01/28 ~ 2026/02/27)
## 😎 팀원 소개
1. 천효림(팀장)
2. 김예서
3. 이채현
4. 정일호
5. 천어진

# 🚀Project 1.
## 2D SEM을 이용한 깊이 예측(3D Metrology) AI 알고리즘 개발

본 프로젝트는 반도체 제조 공정의 핵심인 Metrology(계측) 단계를 혁신하기 위한 연구입니다. 고가의 3D 장비 없이도 2D SEM(Scanning Electron Microscope) 이미지만으로 나노 단위의 깊이(Depth Map)를 정밀하게 예측하는 AI 모델을 개발합니다.

# 🚀Project 2.
## Multimodal Transformer(MMT-S) 기반 실시간 이송장치 열화 예지보전

## 📖 프로젝트 개요

본 프로젝트는 열화상 이미지와 시계열 데이터를 Early Fusion 멀티모달 트랜스포머(MMT-S)로 통합하여, 현재(ML 진단)와 30초 후(DL 예보)의 상태를 동시 예측하는 솔루션입니다. F1 0.92의 고성능을 확보함과 동시에 INT8 양자화로 메모리를 75% 절감하여 저사양 로봇 탑재 효율을 극대화했습니다. 실시간 대시보드와 이상 부위 시각화(XAI)를 포함한 End-to-End 파이프라인을 구축하여 현장 맞춤형 예지보전 환경을 제공합니다.

## 🛠️ 개발 환경 및 기술 스택

* **하드웨어**: Google Colab (L4 GPU / CPU 환경 벤치마크)
* **프레임워크**: PyTorch
* **모델**: Multimodal Transformer - Small (MMT-S)
* **핵심 기법**:
* **Multi-task Learning**: 현재 상태 분류와 미래 수치 회귀를 동시 수행
* **INT8 Post-Training Quantization**: 모델 경량화 및 추론 가속화
* **Pre-Layer Normalization**: 학습 수렴 안정성 확보



## 🧠 핵심 모델 아키텍처: Why MMT-S?

### 1. **Pre-LN (norm_first=True) 구조 채택**:
* 기존 Post-LN 방식의 그래디언트 불안정성 문제를 해결하여 깊은 층에서도 안정적인 학습 수렴을 유도했습니다.


### 2. **Multimodal Integration**:
* 서로 다른 스케일을 가진 이미지와 센서 데이터를 Transformer 내부에서 융합하여 단일 모달리티(Single-modal) 대비 높은 통찰력을 확보했습니다.


### 3. **Multi-task Dual Head**:
* **Diagnosis Head (Classification)**: 현재 상태 4단계(Normal, Caution, Warning, Danger) 판정
* **Prognosis Head (Regression)**: 30초 후의 장비 상태 수치(0.0~3.0) 예측



## 📊 실험 결과 및 비교 분석

### 1. 정량적 지표 (Paper vs Ours)

논문 수치(SOTA)와 베이스라인을 모두 상회하는 고득점을 기록했습니다.

| 지표 (Metric) | Paper MMT | Baseline | **Ours (MMT-S)** | 분석 결과 |
| --- | --- | --- | --- | --- |
| **F1-Score (⬆)** | 0.8944 | 0.91xx | **0.9231** | **약 3.2% 성능 향상** |
| **RMSE (⬇)** | 0.2875 | 0.35xx | **0.33** | **안정적인 예보 정밀도** |

### 2. 경량화 성능 (INT8 Quantization)

CPU 환경에서도 실시간 추론이 가능한 속도를 확보했습니다.

| 지표 (Metric) | 원본 (FP32) | 양자화 (INT8) | 결과 |
| --- | --- | --- | --- |
| **추론 속도 (Latency)** | 18.53 ms | 19.78 ms | **안정적인 처리 속도 유지** |
| **초당 처리량 (FPS)** | 54.0 FPS | 50.6 FPS | **초당 50회 이상 분석 가능** |
| **성능 보존율 (F1)** | **0.9231** | **0.921x** | **정확도 손실 거의 없음** |

## 📍 성능 분석 및 한계점 (Error Analysis)

### 1. 신뢰도 기반 안전성 분석

* **Danger 미검출 0%**: 실제 Danger 상황을 Normal로 판단한 사례는 **0건**으로, 현장의 치명적 사고 방지율 100% 달성.
* **보수적 오탐 (False Alarm)**: 정상을 위험으로 판단한 30건(0.6%)은 센서 노이즈에 의한 일시적 현상이며, 이는 현장의 안전을 최우선으로 고려한 **보수적 설계**의 결과임.
* **확신 지표**: 모델 신뢰도(Confidence) 0.9 이상의 구간에서는 **정확도 99%**를 기록하여 알람의 신뢰성 입증.

### 2. 한계점 및 대응 논리

* **Caution 구간의 모호성**: Caution을 Normal로 오판한 사례(316건)가 존재하나, 이는 전조 증상의 미세한 차이에서 기인함.
* **이중 안전망(Fail-safe)**: 진단(Classification)이 틀리더라도, **30초 후 예보 수치(Regression)가 이미 위험권(2.5 이상)**을 가리키고 있어 시스템 전체의 사고 대응력은 유지됨.

## ⭐ 실시간(가정) 모니터링 대시보드 웹 구현

## 📍 향후 개선 방향 (Roadmap)

1. **ML-DL 앙상블 시스템 구축**: 현재 상태에 강한 ML(Random Forest 등)과 미래 흐름에 강한 DL(MMT-S)을 결합하여 27~30건의 미세 오차까지 상쇄 시도.
2. **Caution 데이터 집중 증강**: 경계선 상의 데이터를 SMOTE나 Augmentation으로 보강하여 미세 전조 증상 탐지 정밀도 개선.
3. **엣지 디바이스 최적화**: TensorRT 또는 ONNX 변환을 통해 FPS를 100 이상으로 끌어올려 초고속 공정 적용 검토.
