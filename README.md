# 조기 당뇨병 진단을 위한 경량 의사결정트리 모델
### 깊이별 해석가능성과 정확도의 정량적 분석

> 🏆 **2026 한국정보기술학회(KIIT) 하계종합학술대회 대학생논문경진대회 동상 수상**

- **저자**: 박송언(1저자), 심준성 (부산대학교 IT응용공학과)
- **지도교수**: 김영인 (부산대학교 IT응용공학과)
- **키워드**: 해석 가능한 AI(XAI), 의사결정트리, 정확도–해석가능성 트레이드오프, 경량화 모델

---

## 1. 연구 배경

랜덤포레스트 같은 앙상블 모델은 정확도가 높지만, 내부 구조를 설명하기 어려운 블랙박스라는 한계가 있습니다.
의료처럼 판단의 근거가 중요한 분야에서는 사후 설명 기법에 의존하기보다 **모델 자체가 해석 가능해야 한다**는 주장(Rudin, 2019)에 착안했습니다.

**연구 질문: 몇 개의 질문만으로 당뇨병을 충분히 정확하게 진단할 수 있는가?**

의사결정트리의 깊이(`max_depth`)는 진단 과정에서 묻는 질문의 최대 개수에 해당합니다.
깊이를 바꿔 가며 정확도와 해석가능성 사이의 트레이드오프를 정량적으로 분석했습니다.

## 2. 데이터셋

- **출처**: [UCI Early Stage Diabetes Risk Prediction Dataset](https://archive.ics.uci.edu/dataset/529/early+stage+diabetes+risk+prediction+dataset) (실렛 당뇨병 병원 수집)
- **규모**: 520명 × 16개 특징
  - 14개 이진 증상 특징 (Yes=1, No=0)
  - 연령, 성별 (Male=1, Female=0)
- **목표 변수**: 양성 320명(61.5%), 음성 200명(38.5%)

## 3. 실험 방법

| 항목 | 내용 |
|---|---|
| 주 모델 | `DecisionTreeClassifier` (분할 기준: Gini 불순도) |
| 비교 기준선 | `RandomForestClassifier` (트리 100개) |
| 실험 변수 | `max_depth` 1 ~ 14 |
| 측정 지표 | 정확도, 표준편차, 리프 노드 수 (+ 정밀도, 재현율, F1, ROC-AUC) |
| 검증 방법 | Repeated Stratified 10-Fold 교차검증 × 5회 반복 (총 50회) |

## 4. 주요 결과

| 깊이 | 정확도 | 표준편차 | 리프 수 | RF와의 차이 |
|:---:|:---:|:---:|:---:|:---:|
| 1 | 0.8177 | 0.0446 | 2 | −0.1658 |
| **2** | **0.8554** | 0.0415 | 4 | −0.1281 |
| 3 | 0.9192 | 0.0322 | 8 | −0.0642 |
| 4 | 0.9115 | 0.0379 | 13 | −0.0719 |
| 5 | 0.9565 | 0.0252 | 20 | −0.0269 |
| 6 | 0.9623 | 0.0227 | 27 | −0.0212 |
| **7** | **0.9677** | 0.0205 | 31 | **−0.0158** |
| 8 | 0.9692 | 0.0214 | 33 | −0.0142 |

*랜덤포레스트 정확도: 98.4%*

- **깊이 2**: 질문 2개(리프 4개)만으로 정확도 **85.5%** → 초경량 1차 선별 도구로서의 가능성
- **깊이 7**: 정확도 **96.8%**, 랜덤포레스트와 **1.6%p** 차이
- **포화 현상**: 깊이 9 이후 리프 수가 34개에서 더 늘지 않고 정확도도 포화됨. 14개 이진 피처가 만드는 입력 공간이 유한하다는 데이터 구조의 한계로 해석함

**결론**: 해석가능성을 포기하고 블랙박스 앙상블 모델을 쓰더라도 얻는 성능 이득은 크지 않으며, 얕은 단일 의사결정트리로도 효과적인 조기 당뇨병 예측이 가능함을 확인했습니다.

## 5. 한계 및 향후 과제

- 소규모 단일 기관 데이터에 기반하므로, 대규모 다기관 임상 데이터를 활용한 일반화 검증이 필요합니다.

## 6. 실행 방법

**환경**: macOS, Python 3.9, scikit-learn, pandas, numpy, matplotlib, seaborn

```bash
pip install scikit-learn pandas numpy matplotlib seaborn notebook
jupyter notebook
```

노트북과 `diabetes_data_upload.csv`를 같은 폴더에 두고 실행하세요.

## 7. 파일 구성

```
├── (실험 노트북).ipynb        # 데이터 전처리, 깊이별 실험, 트리 시각화
├── diabetes_data_upload.csv   # UCI 조기 당뇨병 데이터셋
└── README.md
```

## 참고문헌

- C. Rudin, "Stop explaining black box machine learning models for high stakes decisions and use interpretable models instead", *Nature Machine Intelligence*, 1(5), 206–215, 2019.
- M. M. F. Islam et al., "Likelihood prediction of diabetes at early stage using data mining techniques", ISCMM 2019, Springer, pp. 113–125, 2019.
- L. Breiman, "Random forests", *Machine Learning*, 45(1), 5–32, 2001.
