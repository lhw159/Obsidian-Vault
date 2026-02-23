**Developing RPC-Net: Leveraging High-Density Electromyography and Machine Learning for Improved Hand Position Estimation**
![[스크린샷 2025-07-04 154841.png|center|600]]

# ==**1. Introduction**==
기존의 분류 모델을 이용한 손 각도 예측 모델은 사람이 손을 control하는 것과는 많이 동떨어진, 즉 자연스럽지 못한 부분이 크다.
분류 기반이 아닌 '회기 기반(regression-based model)'으로 손각도를 예측하는 모델이 필요하다. 하지만 기존의 회기기반 모델들은 다음과 같은 문제가 있었다.
1. 손의 전체적인 kinematics를 고려하는게 아닌 손목, 손가락 단일 제어에만 집중
2. distal forearm muscle에만 집중한 모델이 많지만, 실제 절단 환자의 경우 proximal forearm muscle이 많이 남아 있음
3. 기존 모델들은 embedding하기에 너무 무거움
=> accurate hand position estimation을 위해 RPC-Net(Recursive Prosthetic Control Network),(96(6x16)emg channel on proximal forearm)

# ==**2. Materials and Methods**==
**A. Setting**
-subject
남성 7, 여성 5
right hand
forearm circumference 20~30cm

-experimetal protocol
6 손목 pose (wrist flexion, extension, abduction(내전),adduction(외전), pronation(내전), supination(외전))
8 손가락 pose (index mp(손바닥, 손가락 연결 관절) flextion-extention, index pip(두번째 관절) flexion-extention, index-middle ab-adduction, middle-ring-little finger flextion, extention)
4 thumb pose(flex, exten, abduc, adduc)
pose 당 8초, 휴식 2초, 총 450초 진행

**B. data aquistion**
-EMG data
32채널 짜리, f=2.048kHz, amplifier  MEACS -> 총 3개 사용(96채널)
emg 센서 첫번째 row는 **medial epicondyle–pisiform bone**거리의 20%지점

-Hand pose
motion capture 사용, 나중에 참고할만함

**C. Inverse Kinematics Algorithm (IKA)**
24 dof hand model -> 이해가 잘 안된다. 엄지 손가락 쪽은 2dof를 줄여도 된다고 생각. 추후에 적용해보자
나중에 이미지 정리.(fig1, fig2)
extention, radial deviation이 positive방향
phase 1: calibartion
phase 2: finger angle estimation setting, sequential quadratic programming(sqp)로 마커 위치 추정 (iterative inverse kinematics )
phase 3: thumb angle estimation setting, sqp사용

**D. Data post processing**
rectification, 5mV로 normalization, 200samples에 대해 25samples slide window로 RMS 진행 (2.048(kHz)/25=81.92Hz로 다운사이징)

**E. RPC-net**
### ✅ 보완해서 정리한 흐름

> 1. **피험자**는 450초 동안 **손의 연속적인 동작(sequence of poses)**을 수행함  
>     → 이건 단발 pose가 아니라 **연속적인 시계열 데이터**
>     

> 2. 이 동안 **두 가지 시계열 데이터가 동시에 수집됨**
>     
>     - **96채널 EMG 시계열** (2.048 kHz)
>     - **손에 부착된 마커의 위치 (100 Hz)** → 이건 이후 IKA로 변환됨
>         

> 3. **마커 데이터를 IKA로 변환**해서 →  
>     **24개 관절의 시계열 angle 데이터**로 만듦 (sampling: 100 Hz → 81.92 Hz 보간됨)
>     

> 4. 이 두 시계열 데이터를 전처리해서
>     - EMG: RMS + 슬라이딩 윈도우 → 1536차원
>     - Angle: 이전 관절 상태 history → 192차원  
>         → 이 둘을 **RPC-Net의 입력**으로 사용
>         
학습 과정에선 output label을 미래 데이터를 사용(예를 들어 시점 t의 emg데이터, angle데이터가 input이라면 미래 데이터는 output)

> 5. **RPC-Net은 sample-wise로 angle을 예측**함
>     - 즉, **한 시점의 24개 관절 angle 값을 출력**
>     - 이 출력을 **다음 시점 예측 시 angle branch의 입력**으로 사용함  
>         → 바로 이 구조가 recursive한 이유
>        
---

### ✅ 중요한 포인트 2가지 보완

#### ① 내부에서 "iteration"을 돌리는 게 아님

- RPC-Net은 내부적으로 한 번에 한 시점의 angle만 예측해
    
- **네트워크가 자체적으로 반복(iteration)을 도는 건 아니고**,  
    **시간 흐름을 따라 sliding window 방식으로 예측을 "여러 번" 수행하는 구조**야  
    → 즉, "외부 루프"를 통해 전체 시계열을 처리함
    

```text
for t in time_steps:
    input = [EMG(t−0.78s to t), angle(t−0.78s to t)]
    output = predicted angle(t+1)
    store output
```

---

#### ② angle feedback은 inference(테스트 시)에서만 사용

- **학습 시**에는 angle 입력 branch에 **ground truth angle 이력**을 사용해서 학습함
    
- **테스트 시**에는 angle 입력 branch에 **이전 시점의 예측값**을 넣음  
    → 이게 recursive 구조의 핵심
    
---

###
**F. Performance Indicators**
Mean Pearson Correlation Coefficient (MPCC), (PCC between estimated and actual angle)
Mean Distance (MD), (mean of Weighted Fingertip Distance(distance btw finger tips of index, middle, thumb finger for estimated and actual angle))

**G. Comparison with other solutions**
CNN: 4 cnn+3 FC, 16x6x16 (3D array input)
RNN: 224 hiddensize and 24 output ,16x96 (2D array input)
TRF: 

**H. Valiation of RPC-Net** 
PRC-Net-B: recursive term 삭제, 즉 angle branch를 input으로 사용 x

전극의 개수 변형해보면서 실험 진행

RPC-Net-W: 기존 RPC-Net은 각 관절마다 sub network로 관절각도 예측-> 단일 network로 24개의 관절 모두 예측하는 RPC-Net-W사용(But, 각 층마다 node의 수를 5배로 증가(계산량 유지를 위해)).

### ✅ 1. 시간 흐름과 주요 지점

| 항목                       | 시간 범위                   | 설명                       |
| ------------------------ | ----------------------- | ------------------------ |
| **입력 EMG/angle 이력**      | t - 0.78s ~ t           | 예측을 위한 과거 0.78초 구간       |
| **예측 시점 (output)**       | t+0.0122s               | 1 sample (81.92Hz) 미래 예측 |
| **모델 추론 시간 (inference)** | 약 6.2ms                 | 모델이 예측을 내놓는 데 걸리는 시간     |
| **출력 도달 시점**             | t+0.0062s               | 예측이 완료되는 실제 시점           |
| **실제 출력 적용 시점**          | 바로 적용됨 (즉시 actuator 제어) |                          |

---

### ✅ 2. 주요 시간 지연 요소 및 해결 방법

|문제 지점|설명|문제|RPC-Net에서의 해결 방식|
|---|---|---|---|
|**초기 입력 불가**|t = 0.00s에선 과거 0.78초가 없음|예측 자체 불가|**t ≥ 0.78s**부터 예측 시작|
|**inference delay**|추론에 6.2ms 걸림|반응이 늦어질 수 있음|**미래(t + 12ms) 상태를 예측**하여 보상|
|**출력 시점 vs 실제 도달 시점**|출력은 t + 0.0062s에 나오지만 t + 0.0122s 상태임|6ms gap이 있음|**즉시 actuator 적용 → 예측 시점에 딱 맞게 도달**|

---

### ✅ 3. RPC-Net 시간 기반 예측 흐름도

```plaintext
시간축: ─────────────▶

t - 0.78s        t                        t + 0.0062s         t + 0.0122s
   │──────────────▶ 입력 (EMG, angle)
                               │──────────────▶ 모델 계산 (6.2ms)
                                                  │────────────▶ 예측 결과 (t+12ms의 angle)
                                                                      ↓
                                                             actuator 즉시 제어
```

- **입력**은 t까지의 이력
    
- **예측 목표**는 t+12.2ms의 상태
    
- **계산은 6.2ms 안에 끝남**
    
- 따라서 실제 동작은 t+12ms에 정확히 도달 → **delay 없음**
    

---

### ✅ 4. RPC-Net의 핵심 전략: 미래 예측으로 지연 상쇄

|기존 접근 방식|RPC-Net 방식|
|---|---|
|현재 입력 → 현재 상태 예측|현재 입력 → 미래 상태 예측|
|계산시간만큼 delay 발생|계산시간 이전에 예측값 준비|
|실시간 제어 불안정|딱 맞게 actuator 제어 가능|
|post-filter로 delay 보정|학습 구조 자체가 delay-free|

---

#### ✅ 5. 한 줄 정리

> **RPC-Net은 과거 0.78초 데이터를 사용해 12.2ms 미래를 예측하고,  
> 그 예측을 6.2ms 안에 완료해서 바로 actuator에 반영함으로써  
> 실제로는 딜레이가 없는 듯한 제어를 가능하게 한다.**

# ==**3. Result**==

## ✅ 1. 주요 성능 지표 정의

|지표|의미|단위|
|---|---|---|
|**MPCC (Mean Pearson Correlation Coefficient)**|예측된 angle과 실제 angle 간의 상관도|%|
|**WFD (Weighted Fingertip Distance)** = MD|손끝 위치 오차 (예측 vs 실제)|mm|
|**Inference Time**|1회 예측에 걸리는 시간|ms|

---

## ✅ 2. RPC-Net 성능 요약 (Table II 기준)

|지표|결과|
|---|---|
|**MPCC 평균**|77% ~ 83%|
|**WFD 평균**|약 24 ~ 33mm|
|**Inference time**|**6.2ms** (±0.01ms)|

---

## ✅ 3. 실험 결과 핵심 요약

### 🎯 RPC-Net은 다음을 달성함:

- **높은 정확도** (MPCC > 80%)
    
- **낮은 손끝 오차** (WFD ≈ 25mm)
    
- **실시간 제어 가능한 속도** (6.2ms)
    

---

## ✅ 4. 주요 실험 결과별 분석

### ▶️ (1) Baseline 비교 실험 (Fig. 6)

- 비교 대상:
    
    - **TRF-Put** (Transformer 기반)
        
    - **RNN-Qui** (Recurrent)
        
    - **CNN-Ols** (CNN 기반)
        
    - **RPC-Net-B** (angle history 없는 버전)
        
- 📊 결과:
    
    - **MPCC**: RPC-Net > 모든 baseline (**p < 0.01**)
        
    - **WFD**: RPC-Net < 모든 baseline (**오차가 더 작음**)
        
- ✅ 해석:
    
    - 복잡한 구조 없어도, **angle 이력을 포함하는 구조가 성능을 확실히 개선**
        
    - **recursion이 실제 예측 안정성에 기여**
        

---

### ▶️ (2) EMG 입력 길이 변화 실험 (Fig. 7)

- EMG 길이: 0.1s ~ 0.78s 실험
    
- 결과: 길수록 성능 ↑  
    → 0.78초 이상은 **성능 향상 둔화**
    
- ✅ 결론: 0.78초는 성능-비용 균형점
    

---

### ▶️ (3) Network width 변화 실험 (Fig. 8)

- EMG branch width: 192 ~ 512
    
- 결과:
    
    - 넓을수록 성능은 좋지만 계산량↑
        
    - **RPC-Net-B** (non-recursive)는 성능 더 빨리 감소
        
- ✅ 결론: recursion이 좁은 네트워크에서 성능 보완 역할 함
    

---

### ▶️ (4) 전극 수 감소 실험 (Fig. 9)

- 96채널 중 일부만 선택
    
- 결과:
    
    - 일부 전극만 써도 **성능 큰 손실 없이 유지**
        
    - 예: 32채널만 써도 WFD +5mm, MPCC –10%
        
- ✅ 의미: **실제 의수 시스템에선 하드웨어 단순화 가능**
    

---

### ▶️ (5) 구조 단순화 실험 (Fig. 10)

- 구조:
    
    - **RPC-Net-W**: 1개 큰 네트워크 (폭 5배)
        
    - **RPC-Net-I**: 1개 좁은 네트워크
        
- 결과:
    
    - RPC-Net-W 성능 ≈ RPC-Net (inference time 비슷)
        
    - RPC-Net-I는 성능 급락
        
- ✅ 결론: 개별 sub-network 구조는 계산량이 크지만 정확함 →  
    **폭 넓은 단일 네트워크로 통합 가능성 확인**
    

---

## ✅ 5. 지표별 해석

|지표|RPC-Net 성능 의미|
|---|---|
|**MPCC > 80%**|관절 각도의 예측 신뢰도 매우 높음 (0.8 이상은 strong correlation)|
|**WFD ≈ 25mm**|손끝 위치 오차는 2.5cm 이내 → **일반적인 의수 제어 수준 이상**|
|**6.2ms 추론 시간**|12ms 단위 제어 loop 내에서 충분히 실시간 대응 가능|

---

## ✅ 6. 논문의 실용적 결론 요약

| 요점                      | 의미                      |
| ----------------------- | ----------------------- |
| **정확도 + 속도 + 경량성** 확보   | 실시간 제어용으로 이상적 구조        |
| **전극 수 줄여도 성능 유지**      | 실제 의수 설계 시 센서 개수 최소화 가능 |
| **단일 네트워크 구조로 통합 가능**   | 추론 속도 확보하면서 임베디드 구현 용이  |
| **미래 예측 기반으로 delay 제거** | 사람의 운동 의도에 실시간 대응 가능    |
|                         |                         |

---
## ✅ 핵심 한 줄 요약:

> **RPC-Net은 정확도, 계산 효율성, 실시간성, 경량화 모두 달성한 균형 잡힌 모델로,  
> 실제 의수 제어 시스템에 적용 가능한 수준의 구조적 완성도를 보여준다.**

