RawImageConverter(x-ray이미지 처리)-> markerPoseGenerator (내일 확인)

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