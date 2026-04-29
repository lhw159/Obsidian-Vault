RawImageConverter(x-ray이미지 처리)
D:\samsung\results\
  └─ s030\
      └─ right\
          └─ downhill\
              ├─ trial_01\
              │   ├─ Images\          ← 밝기 보정된 TIFF (C1, C2 쌍)
              │   ├─ Images_musica\   ← MUSICA 향상된 TIFF (C1, C2 쌍)
              │   ├─ Calibration\     ← 엔코더 txt, time.csv
              │   └─ Txfmats\         ← (빈 폴더, 후속 처리용)
              └─ trial_02\
                  └─ (동일 구조)


markerPoseGenerator


shapeinitial파일 results/s0##/left or right/ 에 넣기

GUI_BPF_GMP_Main->Initial Pose Estimation(InitialPoseEstimation 프로그램 활성화)
=> 

SetStartEnd_PeakDetect 프레임 설정(보행 시작-끝, heel strike-toe off)
5프레임 마다 뼈 맞추기 (y 누르면서 (이전 프레임 동기화))
다 맞춘 후 smoothing
per frame 6dof Optimization
->
initialposeestimation에서 load registered txf ->final smoothing

calibration폴더 내 InitialTxf, InitialTxf_Smoothed 생성
Txfmats/fem or tib 폴더 내 결과 파일
# marker pose generator
입력 파일
1. **`result_coeff_20221115.mat`** — 마커 포즈 캘리브레이션 데이터 (`result_coeff`, `regress_vec`)
2. **`exp_results.mat`** — source/detector 상대 포즈 (마커 기준)
3. **`Timestamps.mat`** — 각 채널의 이미지 타임스탬프 (`Ch0TimeStamp`, `Ch1TimeStamp`)
4. **`forSaveExperimentDataXYstage.txt`** — XY 스테이지 엔코더 데이터
5. **`forSaveExperimentDataPenTilt.txt`** — Pan/Tilt 스테이지 엔코더 데이터
6. X-ray 이미지 (`C1-xxx.tiff`, `C2-xxx.tiff`)

## 출력 파일

 출력은 **DPMat** 입니다:

- **`DPMat1.mat`** — `PMat` (3×4×N projection matrix)와 `Dist` (source-detector 거리)
- **`DPMat2.mat`** — 동일 구조

## 두 카메라 각각의 포즈?

정확히는 **두 카메라(biplane fluoroscopy)의 projection matrix**입니다.

- `detector(1)` + `source(2)` → 카메라 1 (DPMat1): source(2)에서 detector(1)으로의 projection
- `detector(2)` + `source(1)` → 카메라 2 (DPMat2): source(1)에서 detector(2)으로의 projection

각 프레임마다 엔코더 데이터로부터 마커 위치를 예측하고, 마커 위치로부터 source/detector의 4×4 변환행렬(`.M`)을 구한 뒤, 이를 3×4 projection matrix(`PMat`)로 변환하여 저장합니다. 즉, **두 X-ray 소스-디텍터 쌍 각각의 프레임별 포즈(projection geometry)**를 나타내는 것이 맞습니다.





# Optimization 원리
PSO (입자 군집 최적화, Particle Swarm Optimization), SSIM 기반
**PSO** : 새떼가 먹이를 찾아가는 행동을 모방한 알고리즘. 먼저, 무작위 방향으로 여러번 쏘아대고 (가상 이미지를 만들어 내고), 그중 가장 좋은 우

## 왜 registration을 하는가?
임상이나 수술 환경에선 고 자유도 관절 데이터가 필요하다 
하지만 마커셋 기반의 기존의 데이터 수집 방식은 고 자유도 데이터를 취득하는데 어렵다.
x-ray 기반 registration을 통해 6자유도 기반 관절 데이터를 수집하여 위 문제를 해결할 수 있다.



이번에는 원에서 살짝 벗어나, 가로로 길쭉한 **타원(Ellipse)**을 예로 들어보겠습니다. 원보다 숫자가 다양해져서 $C$와 $C^*$의 차이가 더 확연히 보일 거예요.

---

### 1. 타원 방정식과 행렬 $C$ 정의

다음과 같은 타원을 가정해 봅시다.

- **방정식:** $\frac{x^2}{4} + y^2 = 1$ (가로 반지름이 2, 세로 반지름이 1인 타원)
    
- **일반형:** $x^2 + 4y^2 - 4 = 0$
    
- **포인트 코닉 행렬 $C$:**
    
    $$C = \begin{bmatrix} 1 & 0 & 0 \\ 0 & 4 & 0 \\ 0 & 0 & -4 \end{bmatrix}$$
    

---

### 2. 듀얼 코닉 $C^*$ 계산 ($C^{-1}$)

이제 역행렬을 구해서 이 타원에 접하는 **'선들의 모임'**을 정의해 봅시다.

- __$C^_ = C^{-1} = \begin{bmatrix} 1 & 0 & 0 \ 0 & 1/4 & 0 \ 0 & 0 & -1/4 \end{bmatrix}$_*
    

---

### 3. 접선 구하기 ($\mathbf{l} = C\mathbf{x}$)

타원의 오른쪽 끝점인 **$\mathbf{x} = (2, 0)$**에서의 접선을 구해볼까요?

1. **점의 동차 좌표:** $\mathbf{x} = [2, 0, 1]^T$
    
2. **접선 계산:**
    
    $$\mathbf{l} = C\mathbf{x} = \begin{bmatrix} 1 & 0 & 0 \\ 0 & 4 & 0 \\ 0 & 0 & -4 \end{bmatrix} \begin{bmatrix} 2 \\ 0 \\ 1 \end{bmatrix} = \begin{bmatrix} 2 \\ 0 \\ -4 \end{bmatrix}$$
    
3. **직선 방정식:** $2x + 0y - 4 = 0 \rightarrow \mathbf{x = 2}$
    

- 타원의 오른쪽 끝에서 수직으로 내려오는 접선이 정확히 계산되었습니다!
    

---

### 4. 듀얼 코닉 검증 ($\mathbf{l}^T C^* \mathbf{l} = 0$)

방금 구한 접선 $\mathbf{l} = [2, 0, -4]^T$가 듀얼 코닉 $C^*$의 멤버인지 확인해 봅시다.

$$\mathbf{l}^T C^* \mathbf{l} = \begin{bmatrix} 2 & 0 & -4 \end{bmatrix} \begin{bmatrix} 1 & 0 & 0 \\ 0 & 1/4 & 0 \\ 0 & 0 & -1/4 \end{bmatrix} \begin{bmatrix} 2 \\ 0 \\ -4 \end{bmatrix}$$

$$= (2 \cdot 1 \cdot 2) + 0 + (-4 \cdot -1/4 \cdot -4)$$

$$= 4 + 0 - 4 = 0$$

**완벽합니다!** 듀얼 코닉은 이 타원에 접하는 모든 직선(예: $x=2, x=-2, y=1, y=-1$ 등)을 대입했을 때 항상 0이 나오도록 설계된 행렬인 것입니다.

---

### 5. 만약 곡선 위에 없는 선을 넣으면?

만약 타원과 전혀 상관없는 선, 예를 들어 **$y=2$** ($\mathbf{l} = [0, 1, -2]^T$)를 넣어볼까요?

$$\begin{bmatrix} 0 & 1 & -2 \end{bmatrix} \begin{bmatrix} 1 & 0 & 0 \\ 0 & 1/4 & 0 \\ 0 & 0 & -1/4 \end{bmatrix} \begin{bmatrix} 0 \\ 1 \\ -2 \end{bmatrix} = (0) + (1 \cdot \frac{1}{4} \cdot 1) + (-2 \cdot -\frac{1}{4} \cdot -2) = \frac{1}{4} - 1 = -\frac{3}{4} \neq 0$$

- 결과가 0이 아니죠? 즉, 이 직선은 타원에 접하지 않는다는 것을 듀얼 코닉이 판별해 준 것입니다.
    

---

### 💡 정리하자면

- **Point Conic ($C$):** "이 **점**이 곡선의 테두리인가?"를 판별.
    
- **Dual Conic ($C^*$):** "이 **직선**이 곡선의 접선인가?"를 판별.
    

이 대칭성 덕분에 우리는 점을 직접 다루기 어려운 상황(예: 이미지에서 곡선의 경계선만 검출된 경우)에서도 **직선들의 방정식만 가지고 역으로 곡선의 행렬을 복원**할 수 있습니다.

이제 이 개념을 바탕으로, 아까 이미지에서 보셨던 **"아핀 변환을 해도 무한대 직선($\mathbf{l}_\infty$)은 변하지 않는다"**는 증명을 이 $C^*$ 관점에서 다시 뜯어볼까요? 아니면 **포물선(Parabola)**이나 **쌍곡선(Hyperbola)** 같은 다른 곡선으로 넘어가 볼까요?