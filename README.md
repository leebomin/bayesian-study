# 베이지안 통계 개인 스터디

10주 동안 베이지안 통계를 공부하며 정리한 **개인 노트와 Python 실습 코드**를 기록하는 저장소입니다.

- 각 주차 폴더에는 개념 정리(`notes.md`)와 예제를 직접 계산·시뮬레이션해 본 노트북(`.ipynb`)이 있습니다.

## 진행 현황

| 주차 | 주제 | 키워드 | 노트 | 실습 |
|---|---|---|---|---|
| 1주차 | 베이지안 추론 입문 | 빈도론 vs 베이지안, 가능도·사전분포·사후분포, 신용구간 | [notes](./week01_intro/notes.md) | [notebook](./week01_intro/week01_intro.ipynb) |
| 2주차 | 교환가능성과 우도원리 | 교환가능성, de Finetti 정리, 우도·충분성·조건부 원리, HPD 구간 | [notes](./week02_exchangeability_likelihood/notes.md) | [notebook](./week02_exchangeability_likelihood/week02_exchangeability_likelihood.ipynb) |
| 3주차 | 이항분포의 베이지안 추론 | 켤레 사전분포, 몬테카를로, 사후평균의 가중평균 해석, 베타-이항 예측분포, 두 비율 비교 | [notes](./week03_binomial_model/notes.md) | [notebook](./week03_binomial_model/week03_binomial_model.ipynb) |
| 4주차 | 진행 예정 | | | |
| 5주차 | 진행 예정 | | | |
| 6주차 | 진행 예정 | | | |
| 7주차 | 진행 예정 | | | |
| 8주차 | 진행 예정 | | | |
| 9주차 | 진행 예정 | | | |
| 10주차 | 진행 예정 | | | |

## 공부하면서 직접 확인해 본 것들

- **교환가능하지만 독립은 아니다**: 균등 사전분포 하에서 $P(X_1=\dots=X_n=1)=1/(n+1)$이 독립일 때의 $1/2^n$과 다르다는 것을 시뮬레이션으로 확인 (2주차)
- **p-value는 멈춤 규칙에 따라 달라진다**: 같은 데이터(12번 중 9번 성공)라도 이항 설계는 p≈0.073, "실패 3번까지" 음이항 설계는 p≈0.033. 반면 MLE와 사후분포는 동일 (2주차)
- **HPD 구간 3가지 구현**: 정확한 해, 그리드 탐색, 사후표본 기반 방법을 비교하고 다봉 분포에서 HPD 집합이 끊어지는 것까지 확인 (2주차)
- **베이지안 예측분산이 더 큰 이유**: θ를 고정값으로 대입하는 방식(분산 2.34)과 θ의 불확실성을 반영한 베타-이항(분산 2.85) 비교 (3주차)

## 실행 환경

```bash
pip install -r requirements.txt
jupyter notebook
```

Python 3, NumPy, SciPy, Matplotlib을 사용합니다.
