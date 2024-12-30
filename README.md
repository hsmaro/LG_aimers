# 온라인 채널 제품 판매량 예측 AI 오프라인 해커톤

> **LG Aimers 3기**

## 배경

- 온라인 판매 채널에서 수집되는 대규모 데이터를 통하여 온라인 판매 환경에서 인사이트를 도출하고 데이터를 활용하여 정확한 판매 예측을 수행

## 주제

- 온라인 채널 제품별 판매량 예측

## 개요

- 기간
  - 23.08.01 ~ 23.08.28(예선, 온라인), 23.09.16 ~ 23.09.17(본선, 오프라인)
- 상세 설명
  - (예선) 특정 온라인 쇼핑몰의 일별 제품 판매 데이터를 바탕으로 향후 21일의 제품별 판매량을 예측하는 AI 모델 개발
  - (본선) 다양한 온라인 쇼핑몰의 일별 제품별 판매 데이터를 바탕으로 향후 약 21일의 제품별 판매량을 예측하는 AI 모델을 개발

## Metric

- Pseudo SFA(PSFA) 
![image](https://github.com/Eastha0526/LG_aimers/assets/110336043/1e04b518-17e5-4299-ab7d-fcb3ae9e930f)
- 대회에서 제공된 기준으로 1-((오차 * 제품비중의 제품별 가중 평균)의 일자별 대분류별 산술 평균)

---
## Collaborator

- 팀 : 띵지

|이름|닉네임|프로필|
|:--:|:--:|:--:|
|권경민|km0228kr|https://github.com/km0228kr|
|김동하|Eastha0526|https://github.com/Eastha0526|
|차형석|hsmaro|https://github.com/hsmaro|

---

## 주요 사항
- 진행 과정
  - 예선
    - 데이콘 사이트를 통해 온라인으로 진행된 해커톤 예선에서 상위 7%로 오프라인 진출 (총 747팀 참여)
    - 데이터의 경우 시퀀스 정보를 반영하기 위한 시계열 모델 활용
    - LSTM과 LayerNorm을 모델 활용
  - 본선
    - LG 인화원에서 1박 2일간 오프라인 해커톤을 통해 33팀 중 최종 수상팀을 결정하기 위한 해커톤 진행
    - 오프라인의 경우 각 팀당 서버 제공(p40 2대)
    - 제한된 시간(20시간) 내 최적의 모델 제작
    - 예선과 동일한 데이터 특성 + 추가된 데이터 특성 반영
    - 상품 설명 데이터에서 텍스트 데이터 추출 및 추가
    - 최종적으로 예선에 비해 성능 개선

## 담당 역할
- 데이터 전처리
  - 시퀀스 정보 반영을 위한 전처리
- 모델링
  - 다양한 시계열 모델 개발
  - RNN, LSTM, GRU, BiLSTM, BiGRU

## 주요 활용 모델
### LSTM
- Long Short-Term Memory Network
- 기존 RNN에서 발생하는 장기 의존성(long-term dependencies)를 해결하기 위한 모델
- 짧은 기간의 데이터를 이용하는 것이 아닌 더 거시적인 데이터를 활용할 때 유용
- Cell state, Forget Gate, Input Gate, Output Gate를 통한 상호작용이 핵심
  ![05-02](https://github.com/user-attachments/assets/c14bfbe7-55bc-4277-a180-18a71fd074f2)
  - Cell State
    - 전체적인 정보의 흐름을 관리하며 Gate에 의해 정보가 추가되거나 삭제된다.
  - Forget Gate
    - 과거 정보의 반영 정도를 결정
    - Sigmoid 함수를 통해 0에 가까울수록 정보를 버리고, 1에 가까울수록 정보를 보존
  - Input Gate
    - Cell State에 현재 정보의 반영 정도를 결정
  - Update
    - Forget Gate와 Input Gate를 통해 정해진 반영 정보를 바탕으로 Cell State 업데이트
  - Output Gate
    - Cell State에서 내보낼 부분을 결정

### GRU
- Gated Recurrent Unit
- LSTM의 변형 모델
- LSTM과 유사한 성능을 보이며, 비교적 계산량이 줄어든 모델
- Reset Gate
  - 과거의 정보량을 조절
- Update Gate
  - LSTM의 Forget Gate와 Input Gate를 합친 느낌
  - 과거의 정보와 현재의 정보의 비율을 조절

### BiLSTM & BiGRU
- 예측 시점 기준 이전 시점과 이후 시점의 정보를 모두 활용
- 정보를 보다 유연하게 처리하며 성능의 향상을 꾀하고자 활용

## 접근
### 모델링
- 베이스라인에서 예측에 활용한 윈도우 사이즈의 경우 90, 하지만 더 깊은 윈도우를 활용하여 예측을 한다면 더 정확한 예측이 가능할 것이라고 판단
- 예선에서 활용한 모델들을 보다 복잡하게 구성하고 주어진 환경에 맞추어 더 복잡한 모델 구축

### 메타 데이터 활용
- train 데이터와 함께 각 제품별 정보 데이터 존재
- 이 데이터의 경우 텍스트 마이닝을 활용하여 각 제품에서 필요한 정보 부분 추출
- 제품타입, 헤어타입, HCA, 700mg 등의 정보만 추출하여 조인을 통한 훈련 데이터와 결합
- 결측치 부분은 제거를 통하여 제품정보가 있는 데이터에 특정하여 예측
- 메타 데이터를 활용하기 전 모델의 경우 PSFA score의 경우 0.49, 0.5를 기록
  -> 메타 데이터 활용 후 GRU 모델과 함께 사용했을 경우 0.55 까지 향상된 것을 확인

---

## 배운점&느낀점
### 시계열 모델에 대한 이해
- 스터디를 통해 이론적으로 이해하고 있던 LSTM과 GRU모델을 반복적으로 활용하면서 모델에 대한 깊은 이해를 할 수 있었으며 활용방법 역시 익숙해질 수 있었습니다.
- 모델 제작시에 발생한 오류들을 해결하면서 일부 지식을 체감할 수 있었습니다.

### EDA의 필요성
- 최종 수상팀 (총 3팀)들의 발표를 통해 기존에 제공해준 Baseline모델을 사용하되, EDA 및 전처리에 신경을 많이 사용한 점을 알 수 있었습니다.
- 또한 중간에 제품정보 데이터를 텍스트 마이닝을 통해 활용했을 때, 성능이 상대적으로 크게 오르는 것을 보며 meta 데이터를 전처리하여 사용하는 것이 중요했다고 느꼈습니다.
- 저희는 아키텍처의 고도화와 모델들의 하이퍼파라미터 튜닝에 집중했는데 대회에서 한 번의 Epoch 당 시간이 6~8시간이 소요되어 최소한의 수준으로 튜닝을 진행했습니다.
- 멘토님의 조언을 통해 서버에 오랜 시간이 걸리는 큰 모델을 진행하고, 개인 Colab을 이용하여 작은 모델들을 실험하였고, 이 때 최소한의 고정값으로 Optimizer와 학습률, 스케줄러를 고정하여 최종적으로는 0.55의 양방향 GRU 모델을 통하여 예측에 성공하였습니다.
- 최종 수상팀들의 경우 모델 아키텍처를 고도화 하는 것 보다 모두 데이터 전처리 및 EDA에 더 신경을 썼다고 발표하였습니다.
- 실제로 1위팀의 경우 베이스라인 모델 아키텍처를 그대로 사용하였음에도 불구하고 PSFA 스코어 값을 0.6을 달성하였습니다.
- 또한 개인 도메인 지식을 바탕으로 특성공학에 집중하여 좋은 결과 및 인사이트를 제시하는 모습을 보며 EDA 및 전처리의 중요성을 다시금 느낄 수 있었습니다.

### 후기
- 1박 2일간 진행되는 시간이 제한적인 해커톤에서 팀원들간의 의견 교류를 해가면서 서로 로깅을 철저하게 하면서 진행하는 것의 중요성을 배울 수 있었습니다.
- 서로 밤새 (전날 13시 ~ 다음날 10시) 약 20시간 동안 쉬지않고 모델 학습을 하였고 최종적으로 만족할만한 모델 결과를 제작하였습니다.
- 너무 복잡하게 문제에 접근한 점이 아쉬웠으며, 때로는 간단한 단계에서 정답을 찾을 수 있다는 생각이 들었습니다.
- 상위권 팀들의 제출 횟수가 100회를 모두 채운 반면 우리의 제출 횟수가 22번인 것으로 상황에 맞는 유연한 사고의 필요성도 깨달을 수 있었습니다.
---
