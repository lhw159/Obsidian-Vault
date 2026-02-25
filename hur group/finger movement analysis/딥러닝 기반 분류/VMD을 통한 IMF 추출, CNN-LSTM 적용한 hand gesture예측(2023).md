"C:\Users\leehyeonwoo\Desktop\personal\semg\upperlimb\Surface EMG-based hand gesture recognition using CNN supervised by RNN.pdf"

**A surface electromyography based hand gesture recognition framework leveraging variational mode decomposition technique and deep learning classifier**
![[스크린샷 2025-06-29 182147.png]]

Variational mode decompostion(VMD)=>입력된 신호를 여러 개의 'mode'로 분해하는 방식. 각 모드는 **특정 중심 주파수를 가지는 narrow-band singal**(주파수 대역별로 깔끔하게 분해)이며 원래 신호는 이 모드들의 합 

| 방식                                 | 설명                   | 단점                     |
| :--------------------------------- | -------------------- | ---------------------- |
| FFT                                | 주파수 성분 분석 (전체 고정 기준) | 시계열 정보 손실              |
| Wavelet Transform                  | 시간-주파수 정보 동시 분석      | 파라미터 조정 필요, 중첩 가능성     |
| EMD(Empherical Mode Decomposition) | 데이터 기반 모드 분해         | 노이즈 민감, mode mixing 발생 |
| VMD                                | 수학적 최적화 기반의 모드 분해    | mode mixing 감소, 더 안정적  |
본 논문에선, EMG 시계열을 VMD로 분해 -> 여러 개의 intrinsic mode functions (IMFs)을 생성
각 IMF는 특정 주파수 대역의 특지을 포함
이 imf들을 cnn-rnn(CNN-LSTM)모델의 입력으로 활용, 정확하고 시간적으로 일관된 손 제스처 인식 수행


## ==**1. Introduction**==
#### Background
' Since the sEMG signals are typically nonlinear and non-stationary, hand-crafted features may not contain the prominent features that can differentiate the various hand gestures.(Bao et al., 2020; Rahimian et al., 2021;Chen et al., 2020) '<
semg signal은 nonlinear하고 non-stationary(시간이 지남에 따라 통계적 특성(평균, 분산)이 변함)=>hand crafted feature(사람이 직접 특정한 feature들)로는 semg의 패턴 분석이 어렵다.

'Yang et al. (2019) not only explored the correlation between the raw sEMG signals and the forces required in 3 DOF wrist movement but also demonstrated the superiority of CNN method over SVR to decode the complex wrist movements.' => svr을 통한 손목 각도 예측 및 힘 추청

'In another study, to enhance the online classifier performance, Simão et al. (2019) designed a LSTM classifier for a Ninapro DB5 dataset and attained 91% accuracy.' 
https://www.sciencedirect.com/science/article/abs/pii/S0167865519302089
온라인 추정, lstm을 활용한 hand gesture 예측, 91퍼의 정확도

In addition, the two key factors that limit the realization of hand prosthetic are **==heavy training burden on the classifier model==** and the **==low generalization capability.==**

#### Used technique
The primary reason for employing a VMD technique is that it is an adaptive <u>non-recursive</u>(EMD는 IMF 하나 추출-> 본 signal-IMF 후 IMF 다시 추출... 반복의 형태라면, VMD는 IMF를 한번에 추출) approach that can offer excellent spectral and spatial resolution for nonlinear and non-stationary sEMG signals.
VMD 기법을 사용하는 주된 이유는, 이것이 반복 피드백(recursion) 없이도 적응적으로 작동하며, 비선형·비정상적인 sEMG 신호에 대해 우수한 주파수 및 공간 해상도를 제공하기 때문이다=>**VMD는 각 모드(IMF)를 동시에 최적화**하며, 각 모드를 계산할 때 이전 모드나 반복된 출력에 의존하지 않음 수학적으로 더 안정적이고 병렬 처리에도 적합함

hybrid CNN–LSTM classifier model  (tmporal, spatial feature)
Adam optimizer 
overfitting issue in the classifier training, L2 regularization technique is used in combination
with dropout technique.
ten functional and grasping actions inspired from Ninapro database is collected from 25 intact participants from forearm flexor and extensor muscles

 
 this study identifies the **information rich IMFs** through power spectral analysis
=> Information-rich IMF
- **실제로 손 제스처를 구분할 수 있는 특징 정보가 많이 담긴 IMF**
- 즉, **Power Spectral Density(PSD)**가 높거나, 특정 제스처 간 구분에 기여도가 큰 모드

EMD는 mode-mixing issue와 lakc of solid mathematical back ground의 문제점
- **Mode-mixing**은 EMD에서 특정 모드에 다른 특성의 신호가 섞이는 문제, 간헐적 성분(갑자기 생겼다가 사라지는)으로 인해 주로 나타남
- **Solid mathematical background 부족**이란 EMD가 경험적인 방법이라서 수학적 엄밀성이 떨어진다는 뜻
=> VMD 사용

IMF추출을 위해 VMD (Wiener filtering, Hilbert transform) 사용, spatiotemporal featrue 추출을 위한 CNN-LSTM model 사용 (**VMD augmented CNN–LSTM classifier**)

##### 푸리에 변환(Fourier transform) vs 모드 분해(Mode decomposition)
푸리에: 정상 신호 대상, 주파수 영역에서 분석
$$X(\omega)=\int^{\infty}_{-\infty}x(t)e^{-j \omega t}$$

모드 분해: 비정상 신호(시간 가변 특성), 시간-주파수 영역에서 분석
-> 신호 자체에서 내재적(intrinsic) 존재하는 진동 성분을 데이터에서 직접 추출
기존 EMD 형태 =>
$$x(t)=\sum^{N}_{k=1}c_{k}(t)+r(t)$$
$c_{k}(t)$는 IMF, $r(t)$는 잔여(residue)성분
- 사전에 정의된 함수가 없습니다.
- 데이터 자체를 기반으로 반복적(EMD) 또는 최적화(VMD) 방식으로 IMF를 찾습니다.
- 따라서 데이터 적응적(data-driven)이며, 신호 특성에 따라 모드가 결정됩니다.

## ==**2. Related work**==
원서 참고
## ==**3. Method**==
실험에 사용한 emg 센서및 소프트 웨어: ADInstruments Powerlab 15T series bioamplifier and LabChart window software.  
✅ 1. **ADInstruments PowerLab 15T series bioamplifier**
	- **PowerLab**은 **ADInstruments**사에서 만든 **범용 생체 신호 측정 장치**입니다.
	- 15T 시리즈는 그중에서도 **아날로그 생체 신호(EMG, ECG, EEG 등)**를 증폭, 필터링, 디지털화하여 **컴퓨터로 전송**해주는 **바이오앰프 + DAQ 장치**예요.
	주요 특징:
	- **EMG, ECG, 혈압, 호흡 등** 다양한 생체 신호 지원
	- 내장 **고이득 증폭기, 필터, A/D 컨버터** 포함
	- **실시간 데이터 스트리밍** 가능
	- 채널 수에 따라 시리즈 구분됨 (15T는 2채널 바이오앰프 내장형)
✅ 2. **LabChart (Windows software)**
	🔹 무엇인가요?
	- **LabChart**는 ADInstruments가 함께 제공하는 **데이터 수집 및 분석 소프트웨어**입니다.
	- PowerLab에서 수집한 신호를 실시간으로 시각화하고, 저장하며, 분석할 수 있는 툴입니다.
	🔹 특징:
	- 다양한 **신호 필터링, RMS 처리, 주파수 분석** 기능 제공
	- **마킹, 이벤트 트래킹, 주기 측정** 등도 가능
	- 플러그인으로 **EMG 분석 모듈**도 포함 가능
	- 실시간 시각화 + 분석 → 논문 실험용으로 자주 사용
	
	| 항목               | 설명                  | EMG 전용 여부           |
	| ---------------- | ------------------- | ------------------- |
	| **PowerLab 15T** | 생체 신호용 증폭 + DAQ 장치  | ❌ 범용 (EMG 포함)       |
	| **LabChart**     | 실시간 생체 신호 시각화/분석 SW | ❌ 범용 (EMG 포함 기능 제공) |

signal pre processing
- **대상자**: 25명의 **건강한 성인(20~24세)**, 신경질환 없음
- **측정부위**: extensor Digitorum Superficialis (단일 채널), (flexor Digitorum Superficialis는 reference, wrist는 접지용)
- **제스처 수**: 10가지 hand gestures
    - → **Ninapro DB1**의 3번째 동작 세트에서 가져온 **일상적 동작(Action daily life, ADL)** 기반 제스처
- **반복 횟수**: 각 제스처당 **10번 반복**
- **측정 시간**:
    - 제스처 수행 시간: 5초
    - 휴식: 3초
- **데이터 처리**: 각 gesture 별로 한곳에 모음(mov1, mov2, mov3...=>5000samples for each gesture) => 500sample 단위로 segment -> 100개의 segment 형성

| 항목                | 수치                                |
| ----------------- | --------------------------------- |
| 피험자 수             | 25                                |
| 한 사람당 segment 수   | 10 gestures × 10 repetition = 100 |
| 전체 segment 수      | 25명 × 100 = **2500**              |
| 각 segment에 대한 VMD | 수행됨 (5 IMF 추출 → IMF2~4 사용)        |
| 모델 입력 개수          | **2500개**                         |
| 각 입력의 shape       | (500, 3) ← segment 길이 × IMF 채널 수  |

![[스크린샷 2025-07-02 014440.png|500]]
F(w): 원본 함수의 ft형태
m(w): IMF (instric mode function)
K: mode 개수
w_k: central frequency (각 mode에 해당하는 중심 주파수)
m update: 
w update: power weight 평균 주파수(에너지의 중심이 어디이 있는지)=> 에너지 중심으로 update
힐버트 변환 이해하고 m update 식 이해

# ✅ VMD(Variational Mode Decomposition) 정리

---

## 🎯 목적

신호 f(t)를 K개의 IMF(진폭-주파수 변조된 서브모드)로 분해하되,  
각 IMF는 **특정 중심 주파수 $\omega_k$에 집중된 **좁은 대역폭(bandwidth)**을 갖도록 최적화한다.

---

## 🧩 전체 구조 요약

### IMF 조건

- 복소수 해석 신호 (analytic signal)
- 중심 주파수 $\omega_k$ 근처에 에너지 집중
- 대역폭 최소화
- 모든 IMF의 합이 원래 신호와 같아야 함

---

## ✅ 1. 시간 도메인 정의: Bandwidth 최소화

$$\min_{u_k,w_k}\sum_{k}∥∂t[u^{+}_{k}(t)⋅e^{-jω_{k}t}]∥^{2}_{2}
$$
- $u_k^+(t): u_k(t)$의 힐버트 변환 (analytic signal)
- $e^{-jω_{k}t}$: baseband shift
- $\partial_t$: 시간 변화율
- 이 식의 의미:  
    ▶ 각 IMF를 복소수로 만든 뒤 주파수 중심을 0으로 옮기고,  
    ▶ 변화율(gradient)의 에너지를 측정하여 대역폭을 정량화함

---

## ✅ 2. 주파수 도메인 변환 (Fourier Transform)

$$∥∂t[u_{k}^{+}(t)e^{−jω_{k}t}]∥^2_2\ \xrightarrow{\; F\; }∫(ω−ω_{k})^{2}⋅∣\hat u_{k}(ω)∣^{2} dω
$$
- 미분 → 곱셈  $j\omega$
- 주파수 중심 이동 → $\omega - \omega_k$
- 즉, **중심 주파수와의 거리 제곱을 가중한 에너지 총합 = 대역폭**

---

## ✅ 3. 최적화 목적 함수 (with 제약조건)

최종 최적화 식 (Lagrangian 포함):

$$\mathcal{L} = \sum_k \int \alpha (\omega - \omega_k)^2 |\hat{m}_k(\omega)|^2 + \left\| \hat{F}(\omega) - \sum_k \hat{m}_k(\omega) \right\|_2^2 + \left\langle \hat{\lambda}(\omega), \hat{F} - \sum_k \hat{m}_k \right\rangle
$$
- $\hat{F}(\omega)$: 원 신호 f(t)f(t)의 푸리에 변환
    
- $\hat{m}_k(\omega)$: IMF의 스펙트럼 표현
    
- $\omega_k$: 중심 주파수
    
- $\lambda$: 재구성 제약을 위한 라그랑주 승수
    

---

## ✅ 4. ADMM 기반 반복 최적화

각 변수에 대해 반복적으로 갱신:

### (a) IMF $\hat{m}_k(\omega)$ 업데이트

편미분으로 도출한 식:
$$
\hat{m}_k^{(a+1)}(\omega) = \frac{ \hat{F}(\omega) - \sum_{i \ne k} \hat{m}_i(\omega) - \frac{1}{2} \hat{\lambda}(\omega) }{ 1 + 2\alpha(\omega - \omega_k)^2 }
$$
- 분자: 다른 IMF와 제약 제외 후 남은 성분
    
- 분모: 중심 주파수에서 멀어질수록 penalty → **bandwidth 최소화 반영**
    

---

### (b) 중심 주파수 $\omega_k$ 업데이트
$$
\omega_k^{(a+1)} = \frac{ \int \omega |\hat{m}_k(\omega)|^2 \, d\omega }{ \int |\hat{m}_k(\omega)|^2 \, d\omega }
$$
→ IMF의 스펙트럼 에너지 중심 → 자동으로 적응됨

---

### (c) 라그랑주 승수 업데이트
$$
\hat{\lambda}^{(a+1)}(\omega) = \hat{\lambda}^{(a)}(\omega) + \tau \left( \hat{F}(\omega) - \sum_k \hat{m}_k^{(a+1)}(\omega) \right)
$$
→ 전체 IMF 합이 원 신호에 가까워지도록 수렴 유도

---

## 🔁 최종 알고리즘 흐름

```plaintext
초기화: m_k = 0, ω_k = 초기값, λ = 0
repeat
    for k = 1 to K
        update m_k(ω)
        update ω_k
    end for
    update λ(ω)
until 수렴
```

---

## ✅ 핵심 요약 10줄

| 개념             | 의미                                |
| -------------- | --------------------------------- |
| 힐버트 변환         | 신호를 복소수 analytic form으로 변환        |
| baseband shift | 중심 주파수를 0으로 이동해 대역폭 측정 가능         |
| 대역폭 측정         | 미분 에너지 →  $(\omega - \omega_k)^2$ |
| 목적             | 각 IMF의 대역폭 최소화 + 신호 재구성 유지        |
| 푸리에 변환         | 시간 도메인 목적 함수를 주파수로 변환             |
| Lagrangian 구성  | 재구성 제약 포함한 최적화식 정의                |
| ADMM 적용        | 각 변수 반복 최적화 ($m_k, ω_k, λ$)       |
| IMF 업데이트       | 남은 신호와 중심 주파수 기반의 필터링 구조          |
| ω_k 업데이트       | IMF의 에너지 중심 주파수로 적응               |
| 수렴             | 각 IMF가 자기 주파수 대역에 수렴하며 신호 분해됨     |

---

# **==4. Result==**
### 🔹 전체 평균 정확도

- **CNN 단독**: 평균 정확도 약 **95.0%**
    
- **CNN–LSTM 하이브리드**: **98.04%**
    

### 🔹 주요 성능 지표 (CNN–LSTM)

| 지표              | 평균              |
| --------------- | --------------- |
| **Accuracy**    | 98.04%          |
| **Sensitivity** | ≥ 94% (모든 동작에서) |
| **Precision**   | ~97–99%         |
| **F1-score**    | > 96%           |
| **MCC**         | ≈ 98% 이상        |
