## Discrete Wavelet Transform
DWT는 CWT의 sampled version. CWT은 정밀한 결과를 보여주긴 하나, 너무 redundant(반복적)하고 significant amount of computation time and resources를 필요로 한다. 이를 보완하기 위해 DWT사용
DWT employs two sets of functions, called **scaling functions ($\phi$) and wavelet functions** ($\psi$), 
which are associated with <u>low pass and highpass filters</u>, respectively
$$y_{high}[k]=\sum x[n]\boldsymbol{\cdot}g[2k-n]  $$
$$y_{low}[k]=\sum x[n]\boldsymbol{\cdot} h[2k-n]$$
$$g[L-1-n]=(-1)^{n}\boldsymbol{\cdot} h[n]$$
reversed low filter h[n]의 홀수 값들에 -1를 곱해 frequency를 높이는 방식. 최종적으로 x[n]은 다음과 같이 표현
$$x[n]=\sum^{\infty}_{k=-\infty}(y_{high}[k]\boldsymbol{\cdot} g[2k-n])+(y_{low}[k]\boldsymbol{\cdot} h[2k-n])$$

위 과정을 통해 본 신호 x[n]에 high, low pass filter 적용, low pass filter의 경우 주파수 대역폭을 절반 감소=>nyquist frequency(신호는 그 신호에 포함된 가장 높은 진동수의 2배에 해당하는 빈도로 일정한 간격으로 샘플링하면 원래의 신호로 복원할 수 있다는 샘플링 이론)로 인해 sampling rate($F_{s}$) 절반으로 감소 (주어진 sampling rate에 대해선 sampling rate의$\frac{1}{2}$ 만큼의 주파수 사용)=>$$\Delta t'=\frac{1}{F_{S}/2}=2\Delta t$$즉 필터 처리된 함수는 고주파 영역이 없어지고 저주파 영역이 강조되며 '느슨'해지는 효과. 이는 마치 함수를 길게 옆으로 늘린 것과 같은 현상을 보이며, 즉 scale이 2배가 된다고 봐도 된다.

만약 Discrete Meyer wavelet(central frequency: $\omega_{c}​≈0.672131\pi$ rad/sample, $f_{c}≈0.3361 Fs$)을 사용한다고 했을 때, $F_{s}=1kHz$라면

$$f_{c}​(j)≈\frac{0.3361Fs​​}{2^{j}}$$

| Level j | $f_c(j)(Hz)$ |
| ------- | ------------ |
| 0       | ≈ 336 Hz     |
| 1       | ≈ 168 Hz     |
| 2       | ≈ 84 Hz      |
| 3       | ≈ 42 Hz      |
| 4       | ≈ 21 Hz      |
위 level에 따라 주파수 기준으로 주파수 대역을 나눈 후(subbanding, **필터뱅크(저역/고역) + 다운샘플링**으로 구현) 밴드를 서로 다른 양자화/부호화 전략으로 압축한 뒤, 합성 필터뱅크로 복원하는 전체 절차


이상적인 주파수 분해 과정==>

| 레벨  | 계수  | 대역(Hz) (대략)  |
| --- | --- | ------------ |
| 1   | d1  | 250 ~ 500    |
|     | a1  | 0 ~ 250      |
| 2   | d2  | 125 ~ 250    |
|     | a2  | 0 ~ 125      |
| 3   | d3  | 62.5 ~ 125   |
|     | a3  | 0 ~ 62.5     |
| 4   | d4  | 31.25 ~ 62.5 |
|     | a4  | 0 ~ 31.25    |
| 5   | d5  | 15.6 ~ 31.25 |
|     | a5  | 0 ~ 15.6     |


정리하자면, level이 오를 때 마다 scale이 2배씩 증가=> 주파수가 $\frac{1}{2}$, sampling reate $\frac{1}{2}$, 이에 따라 sample 개수 또한 $\frac{1}{2}$. 만약 256 sample에 DWT를 3 level까지 적용한다 가정하면, d1=128, d2=64, d3=32, a3=32 samples가 되며, 이들의 coefficient를 plot 할 땐, 저주파(a3)부터 왼쪽에서 부터 쭉 나열. Sample 수는 다르지만, level이 오름에 따라 sampling rate이 줄어들므로 결국 모두 같은 시간 길이에 대한 데이터. 

DWT는 결국 '어느 타이밍'에 해당 '고유주파수'의 패턴이 잘 나타나냐. time -> time,frequncy domain (x축은 시간, y축은 frequency resonance value)
# 나중에 참고
핵심부터 정리할게요.

**1) “moving window로 FT를 계속 돌린다” = 사실상 STFT입니다.**  
제가 STFT를 자꾸 언급한 건, 문헌/이론에서 **“슬라이딩 윈도우 + FT”**를 그렇게 부르기 때문이에요. 용어만 정리한 거지, 당신이 하고 있는 것과 다른 걸 말하려던 건 아닙니다.

---

## 왜 “거의 정적/주파수 대역이 명확”하면 moving‑window FT(=STFT)로 충분한가? — **근거**

### (A) 고정 윈도우의 해상도 한계 (수식)

윈도우 길이를 LL (sample)라 하면

- **주파수 해상도**: Δf≈FsL\Delta f \approx \frac{F_s}{L}
    
- **시간 해상도(프레임 길이)**: Δt≈LFs\Delta t \approx \frac{L}{F_s}
    

즉, **Δf⋅Δt≈1\Delta f \cdot \Delta t \approx 1** 수준의 **고정된 트레이드오프**(Heisenberg 불확정성에 따른)만 제공합니다.  
**신호의 주요 성분이 거의 변하지 않으면** 이 고정 해상도만으로도 충분히 스펙트럼을 추적 가능하죠.

---

## 왜 “매우 비정상/주파수가 급변”하면 WT가 유리하다고 말하나? — **근거**

### (B) DWT/CWT의 **다중해상도(멀티레졸루션)**

Wavelet은 스케일 a=2ja = 2^j (DWT 기준) 별로

- **시간 해상도** ∝a\propto a (저주파 쪽은 긴 윈도우와 유사 → **주파수 해상도↑**)
    
- **주파수 해상도** ∝1/a\propto 1/a (고주파 쪽은 짧은 윈도우와 유사 → **시간 해상도↑**)
    

즉, **주파수 대역마다 다른 해상도**를 자동으로 제공합니다. 고정 윈도우 FT로는 **서로 다른 윈도우 길이를 직접 관리**해야 같은 효과를 냅니다.

### (C) **Constant‑Q 특성**

Wavelet(특히 Morlet 류)은 **대역폭이 중심 주파수에 비례(거의 일정 Q-factor)**해서 **로그 주파수 축(옥타브) 분석**에 자연스럽습니다.  
반면 STFT는 **대역폭이 절대값으로 일정(선형 주파수 축)**이라,

- 낮은 주파수(예: 5 Hz 근처)를 분해하려면 긴 윈도우가 필요 → 고주파 이벤트 시간 위치는 뭉개짐
    
- 짧은 윈도우로 고주파 이벤트는 잡아도, 저주파 분해능은 형편없어짐
    

**→ 한 윈도우로 전 대역을 동시에 잘 다루기 어렵다**는 것이 핵심 근거입니다.

### (D) 실전 예시 (문헌에 무수히 많음)

- **ECG/EEG, 지진파, 베어링 고장(충격/임펄스)**:  
    짧고 순간적인 고주파 이벤트 + 장기 저주파 추세가 같이 있음 → **WT 계수에서 이벤트가 특정 레벨에 날카롭게 뜨기 때문에** 검출/특징 추출이 쉬움.
    
- **압축/노이즈 제거**: WT 계수는 보통 **훨씬 희소(sparse)** → 임계값(thresholding) 기반 denoising, subband coding(JPEG2000) 등에 핵심.
    

---

## “우린 실시간 moving window FT를 이미 쓰고 있는데요?”

그래서 **타이밍(Localization)만큼은 이미 확보**했다고 하신 거죠. 인정합니다.  
그럼에도 WT를 고려할 “남은” 이유는 보통 아래와 같습니다.

1. **하나의 고정 윈도우로는 전 대역을 동시에 최적화하기 어렵다.**  
    (고주파 이벤트 + 저주파 패턴을 동시에 보고 싶을 때)  
    → WT는 **자동으로** 멀티레졸루션을 제공.
    
2. **노이즈 제거/압축/희소 표현**을 하고 싶다.  
    → DWT 계수에 **밴드별 thresholding / bit-allocation**이 매우 잘 맞음.
    
3. **로그/옥타브 스케일이 자연스러운 도메인(오디오, 기계진단 등)**  
    → WT의 밴드 구조가 더 해석하기 쉬움.
    
4. **온라인 WT도 가능** (lifting scheme, streaming DWT). 계산복잡도 O(N)이고 레이턴시도 제어 가능.
    

---

## 정리 (판단 가이드)

- **“윈도우 하나로 충분히 잘 된다(관심 주파수도 좁고, 이벤트도 느리다)”**  
    → **지금 하고 있는 moving-window FT(=STFT)면 충분**합니다.
    
- **“고주파의 순간 이벤트 + 저주파의 장기 패턴을 동시에, 각자 최적의 해상도로 보고 싶다”**  
    **“denoising/압축/희소 feature가 필요하다”**  
    → **WT가 실질적 이득**을 줍니다.
    

