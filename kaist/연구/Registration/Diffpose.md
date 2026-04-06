### Intraoperative 2D-3D Image Registraion via Differentiable X-Ray Rendering
# Abstract

수술 도중, **rapid portable 2D intraoperative images** (수술 중 찍는 x-ray선, 2D / C-arm)를 수술 전 찍은  **high-fidelity 3D preoperative reference scan**(3D / CT) 에 맞춰봄으로서(**Registration**), **Surgical decision** (수술적 판단/ 임플란트가 어느 각도와 깊이로 박혀야 하는지, 뼈가 어느 위치로 가야하는지, 제대로 정렬이 되었는지, 도구 현 위치가 신경이나 혈관을 누르지 않는지)를 내릴 수 있다.

But, 기존의 전통적인 optimization 방식은 말도 안되게 느리고, local minimum에 민감하고, 비실용적인 landmark를 필요로 함. <= 이를 해결하기 위해 제안하는 것이 **DiffPose**

**Diffpose** (differentiable pose) : labeled data에 의존하지 않는 self-supervised 방식
환자 특화 시뮬 (**Patient-specific simulation**) => 환자마다 뼈 형상이 다르므로
미분 가능 물리 기반 렌더링 (**Differentiable physice-based rendering**) => 가상의 x-ray선을 만드는 방식(DRR, Digitially Reconstructed Radiograph), 실제 x-ray 이미지와 뼈 모델을 통해 투영한 가상 x-ray 이미지를 비교하여 3D CT의 pose 변경

work flow:
1. CNN을 통해 가상 X-ray 이미지에 대한 pose를 regress하도록 학습
2. 수술 중 CNN이 찍어주는 initial pose를 기준으로 Differentiable Renderer로 실제 x-ray에 맞춤

SE(3)공간에서 sparse diffrentiable rendring(희소 렌더링, 뼈 부분만 렌더링 하는 기법)을 통해 camera pose를 찾고, tangent space se(3)상에서 geodesic losses(회전 공간상의 포즈 사이의 실제 최단 거리), multiscale locality-sensitive losses(local 특징에 민감하게 반응하여 오차 줄여나가기)를 통해 registration

se(3) -> SE(3)라는 굽어 있는 공간을 특정 지점(보통 항등원)에서 '평평하게 펴서 만든 접평면

# Intro
Intensity-based 방식은 initial pose에 대해 제한된 범위와 민감도에 대한 한계가 있다
[52] Mathias Unberath, Cong Gao, Yicheng Hu, Max Judish, Russell H Taylor, Mehran Armand, and Robert Grupp. The impact of machine learning on 2d/3d registration for image guided interventions: A systematic review and perspective. Frontiers in Robotics and AI, 8:716007, 2021. 1

To this end, two methods for initial pose estimation are commonly deployed: **landmark-based localization** and **CNN-based pose regression.**

**landmark-based localization**
Perspective-n-Point (PnP) 방식 (x=PX)
[30] Vincent Lepetit, Francesc Moreno-Noguer, and Pascal Fua. EPnP: An accurate O(n) solution to the PnP problem. Inter national journal of computer vision, 81:155–166, 2009. 2 
[31] ShiqiLi, ChiXu, andMingXie. Arobusto(n)solutiontothe perspective-n-point problem. IEEE transactions on pattern analysis and machine intelligence, 34(7):1444–1450, 2012. 2

landmark-based localization 한계, 학습량
While landmark localizers can be learned, only a few small labeled datasets exist for training deep networks [24, 41, 50]

CNN을 통한 intraoperative x-ray에 대한 pose를 찾는 시도도 많았음

==**본 연구에선 pre-op CT 기반 rendererd synthetic x-ray선에 대한 학습을 진행함. (그럼 각도와 위치는 어떻게? 무한대라면 어떻게 처리?)**==

landmark-based는 synthetic x-ray선으로 학습해도 정확도가 떨어짐
Even when supervised training data is augmented with synthetic X-rays, landmark-based localization and pose re gression fail to achieve consistent sub-millimeter accuracy [17, 35, 56].

**intensity-based iterative**
intensity losses (e.g. MSE, SSIM, NCC)때문에 실패할 수도

==**본 연구에선 differentiable X-ray rendering을 사용해 tangent sapce에서 optimize 진행, sparse multiscale variant of loacl normalized cross correlation? <-잘 모르겠음**==

**Contribution**
self-supervised patient-specific CNN (learns through geodesic losses)을 통한 initial pose.
differentiable X-ray renderer를 통한 optimization (maximizing multiscale image similarity computed oversparse image patches)


# Relative Work
**Intensity-based 2D/3D registraion** (밝기 기반)
2D DRR과 실제 X-ray이미지와 'pxiel 단위의 명암 대조'를 통해 similarity를 측정.
maximizing similarity를 위해 새로운 DRR를 쏘는 과정을 반복(camera pose (R,t) 수정).
-> 픽셀 단위 이기에 미분 불가능 -> 가능성 있는 모든 방향에대해 DRR를 다시 쏴줘야 함->시간이 오래걸림
==.>본 연구에선 self-supervised method for differentiable registration을 사용==


**Landmark-based 2D/3D registration** (특징점 기반반)
뼈의 특정 부위를 찍어서, 해당 부위가 정확히 맞도록 추정하는 PnP 방식.
소수의 점만 비교하기에 속도는 빠르지만, 뼈의 어느 부분을 특징점을 잡을지에 대한 전문적인 지식이 필요, 피험자 마다 새롭게 마킹 해줘야함
==.> 본 연구에선 landmark 사용 x, operate directly on patient-specific image intensiteis==

**Camera pose regression**
3D Eucledean coord에서 roation은 discontinous (359$\degree$->0$\degree$)
고차원에서의 회전 표현을 통해 연속성 부여 가능 [57]
neural network, point cloud registration에선 고차원 회전 표현을 통해 불연속 문제를 해결 가능 

**Self-supervision**
CNN 기반 pose estimation. 무한대의 training data (unlimited synthetic training data)를 생성하기 위해 fast differentialbe X-ray renderer 사용


# Preliminaries
**Differentiable rendering of synthetic X-rays**
보통 X-ray 이미지를 투영할 땐 선형적인 감쇠율 (linear attenuation)을 사용한다[48] (scattering, beam hardening같은 2차 효과를 무시하고) ==good note(Diffpose) 참고==[22]

Euler angle, quaternion은 gimbal lock, 불연속 같은 문제가 있음.
고차원의 표현방법 사용-> rotation과 translation을 분리하여 표현하지 않고, tangent sapce(se(3) which is isomorphic $R^{3}\times R^{3}$, translation 벡터에 대해 axis-angle 표현하는)[38]


initial pose(self-supervised)->optimization()->
diffrentible 

# Method
$I$:  x-ray 이미지 (가상, 현실 둘 다) 
$\mathcal{E} : I \rightarrow \mathbb{R}^d\times\mathbb{R}^3)$ : x-ray를 Euclidean 파라미터화 하는 encoder network (CNN 학습 대상, 3차원(tlanslation)+3차원(rotation)=6차원, se(3) Lie )
$\mathcal{P}$ : 어느 camera pose T에서도 CT volume의 가상 x-ray선 생성하는 미분 가능 renderer (DiffDRR[22])
## 4.1. Training Pose Estimation Networks 
**Sampling synthetic X-ray poses.**
![[Pasted image 20260331191610.png|400]]
$\mathbf{T}_{iso}$ (isocenter pose): 환자의 뼈 3d 형상을 정면으로 봐라보는 방향. pertubation의 기준이 되는 방향, isocenter pose를 기준으로 랜덤하게 pertubation (normal distribution을 따라서) 을 주어 학습 데이터 형성. 
그렇게 형성한 Lie algebra내에서의 pertubation을 $\mathbb{R}^d\times\mathbb{R}^3\rightarrow SE(3)$ 공간 이동 후 ($\Delta \mathbf{T}$) 카메라 포즈 변경 $\mathbf{T}=\Delta \mathbf{T}\bullet \mathbf{T}_{iso}$

$t_{iso}$ (isocenter translation) = $(b_x\Delta x,b_y\Delta y,b_z\Delta z)/2$ : 3d 형상의 정 중앙, (b : voxel의 개수, $\Delta$ : voxel 당 mm 길이) 


**Synthetic pose regression pretraining.**
random camera pose $\mathbf{T}\in SE(3)$, 로 투영한 가상 X-ray 이미지 $I=\mathcal{P}(\mathbf{T})\bullet V$
가상 이미지로 부터 추정한 pertubation $\Delta \mathbf{\hat{T}} \overset{\Delta}{=} \mathcal{E}(I)$ 로 추정한 카메라 포즈 $\mathbf{\hat{T}}=\Delta \mathbf{\hat{T}}\bullet \mathbf{T}_{iso}$ 
$\mathbf{\hat{T}}$로 추정한 새로운 가상 이미지 $\hat{I}=\mathcal{{P}}(\mathbf{\hat{T}})\bullet V$
$I$와 $\hat{I}$, $\mathbf{T}$ 와 $\mathbf{\hat{T}}$의 비교를 통한 $\mathcal{E}$ weight 최적화

## 4.2. Registration Losses (Pre-op)
![[Pasted image 20260402191249.png|600]]
==**Geodesic pose regression losses**==
Geodesic : 두 지점 간 가장 짧은 거리
$R_{A},R_{B}$사이의 각 차이
$$
\begin{aligned}
d_{\theta}(R_{A},R_{B})&=\text{arccos}(\frac{trace(R_{A}^{T}R_{B})-1}{2})\\
&=||\text{log}(R_{A}^{T}R_{B})||
\end{aligned}
$$
여기서 $R_{A}^{T}R_{B}$는 두 회전 행렬의 상대적 각 차이 (만약 둘이 같다면 Identity가 나옴)
$trace(R_{A}^{T}R_{B})=1+2cos\theta$ : trace(A)= A의 eigen value들의 합, R의 eigen value는 $1, e^{i\theta}, e^{-i\theta}$ .
$log(R_{A}^{T}R_{B})$ : logarithm map[26], $(R_{A}^{T}R_{B})$는 두 회전 행렬의 상대 회전. 하지만 행렬 형태. 우리가 아는 회전 행렬의 exponential 표현 $R=e^{\theta \hat{w}}$는 R에 대한 회전 축 w, 그에대한 회전 각도 theta의 tangent map so(3)상에서의 표현 $\theta \hat{w}$를 SO(3)로 보내는 공간 이동 기법 e(~)->SO(3). 그 반대의 lorarithm map $||log(R_{A}^{T}R_{B})||=\frac{1}{2}trace(S^{T}S)$ $,(SO(3)\times SO(3) \rightarrow \mathbb{R}^{+})$은 두 회전 행렬의 상대 회전 차를 so(3)로 보내 geodesic loss(각도차)를 구하는것

위 과정을 transpose matrix $\mathbf{T}\in SE(3)$ 에 적용한다면 ==**geodesic loss function**==은 다음과 같이, (exponential map 코드의 경우 Appendix A확인)
$$\mathcal{L}_{log}(\mathbf{T}_{A},\mathbf{T}_{B})=||\text{log}((\mathbf{T}_{A}^{-1}\mathbf{T}_{B})||$$
$$(\,\,\mathbf{T} = \exp(\boldsymbol{\xi})\,\,, \;\;d\,exp_{\xi}(\xi)=\mathbf{T}\cdot(J_{l}(\xi)\delta\xi)^\wedge\,\,)
$$

(추가)
왜 미분이 필요한가. 기존의 $\mathbf{T}\in SE(3)$는 덧셈에 열려있는 상태 (open, close 의미: **membership rule** 특정 연산을 했을 때, 그 결과가 여전히 그 집합에 머물면 닫혀있는 상태) 이를 평평한 tangent map (isopose에 대한 tangent map) 상에선 자유롭게 더할 수 있고, 즉 쉽게 Gradient를 통한 update가 가능. 


실거리 차이 ==**geodesic distance (double geodesic loss)**==
초점거리 $f$에 따른 각도 차이 기반 arc length:
$$d_{\theta}(R_{A},R_{B};f)=\frac{f}{2}d_{\theta}(R_{A},R_{B})$$
translation distance $d_{t}(t_{A},t_{B})=||t_{A}-t_{B}||$

둘을 결합한 geodesic distance
$$\mathcal{L}_{geo}(\mathbf{T}_{A},\mathbf{T}_{B};f)=\sqrt{d_{\theta}^{2}(R_{A},R_{B};f)+d_{t}^{2}(t_{A},t_{B})}$$==**Multiscale NCC**==
NCC(Normalized cross correlation) : Z-scoring을 한 image에 대한 similarity를 측정하는 metric 
$$
\begin{aligned}
\text{NCC}(I_{A},I_{B}) &=\frac{1}{NM}\sum^{N}_{i=1}\sum^{M}_{j=1}Z_{A}[i,j]Z_{B}[i,j] \\\\
Z&=\frac{(I-\mu(I))}{\sigma(I)}
\end{aligned}
$$
$\text{NCC} \leq 1$, 두 이미지가 같으면 최대값. Normalizing (Z-scoring)을 하기 때문에 이미지의 픽셀 밝기의 절대값은 중요하지 않음, 이미지 상 패턴의 차이가 중요.
NCC는 local과 global로 나뉘는데, 패치 단위로 차이를 비교하는게 local, 전역의 평균을 비교하는게 global. 본 연구에선 여러 스케일의 patch(multiscale patch)를 이용해 두 이미지를 비교하는 multiscale NCC $\mathcal{L}_{\text{mNCC}}$를 활용

==**Composite pretraining loss**==
$$\text{Loss}=-\mathcal{L}_{\text{mNCC}}+\lambda_{1}\mathcal{L}_{log}(\mathbf{T},\mathbf{\hat{T}})+\lambda_{2}\mathcal{L}_{geo}(\mathbf{T},\mathbf{\hat{T}}).$$



==**Sparse differentiable rendering**==
위의 loss는 CNN 학습 용. test-time optimization (실 수술 상황)에서 학습된 CNN을 통해 initial pose를 맞췄다면, 이젠 미세 조정 단계.

Sparse differnetiable rendering : 이미지의 특정 점들에 대해 patch를 설정하여 비교하는 기법? patch의 위치는 CNN의 최종 activation map 기준 (뼈가 있을)확률이 제일 높은 점들을 지정, 그 점들을 중점으로 patch 형성, patch 위치에 대한 $I$와 $\hat{I}$비교하며 미세 조정.
$$\mathcal{L}=\frac{\partial L}{\partial \boldsymbol{\xi}} = \frac{\partial L}{\partial \mathbf{I}_{synth}} \cdot \frac{\partial \mathbf{I}_{synth}}{\partial \mathbf{T}} \cdot \frac{\partial \mathbf{T}}{\partial \boldsymbol{\xi}}$$
(픽셀에 따른 loss의 변화) x (카메라 포즈 변경에 대한 픽셀의 변화) x (벡터 변화에 대한 카메라 포즈 변화)
$$\xi_{i+1}=\xi_{i}-\alpha \mathcal{L}$$
## 4.3. Test-Time Optimization (Intra-op)
위에서 언급한 Sparse differentiable rendering을 통해 실시간 미세 조정
$\mathbf{\hat{T}}$을 미세 조정 하면서 $\mathbf{\hat{I}}=\mathcal{P}(\mathbf{\hat{T}})\bullet \mathbf{V}$ 를 통해 업데이트

## 4.4. Landmark-Based Evaluation
본 연구에선 landmark 를 사용 안함. 평가요응로만 mTRE 따로 적용,
**mTRE (mean Target Registration Error)** : 미리 설정한 m 3D anatomical landmark $M \in \mathbb{R^{3\times m}}$에 대한 error, 이미 intrinsic matrix $\mathbf{K}\in\mathbb{R}^{3\times 3}$ 을 알고 있으니(카메라의 특성)을 알고 있으니 M에 대한 prospective projection은 쉽게 계산 가능. mTRE는 다음과 같이 계산
$$\text{mTRE}(\mathbf{T},\mathbf{\hat{T}})=\frac{1}{m}||\mathbf{K}([R|t]-[\hat{R}|\hat{t}])M||_{F}$$ 
## 4.4. Implementation Details
**Pretraining**

| **구분**     | **세부 내용**                                                                   |
| ---------- | --------------------------------------------------------------------------- |
| **모델 구조**  | **ResNet18** 백본 + 2개의 **FC Layer** (회전 $R^3$ , 이동 $R^3$ 각각 출력)              |
| **학습 데이터** | 실시간 생성된 **Synthetic X-ray** (총 1,000,000장)                                  |
| **학습 방식**  | 각 환자별로 처음부터 새로 학습 (**Patient-specific** )                                   |
| **최적화 도구** | **Adam** (Warm-up 포함) , 최대 학습률 $1 \times 10^{-3}$                           |
| **스케줄러**   | **Cosine learning rate scheduler**                                          |
| **하드웨어**   | **NVIDIA A6000 GPU** (배치 사이즈 8)                                             |
| **특이 사항**  | **Batch Normalization** 대신 **Group Normalization** 사용 (작은 배치 사이즈 때문)        |
| **소요 시간**  | 약 12시간 (**12 hours** )                                                      |
| **손실 함수**  | $\lambda_1 = \lambda_2 = 10^{-2}$ , **Multiscale NCC** (패치 크기 13 및 256(전체)) |

**Test-time optimization**

| **구분**       | **세부 내용**                                         |
| ------------ | ------------------------------------------------- |
| **목적**       | 초기 포즈 추정치의 정밀화 (**Refinement** )                  |
| **손실 함수**    | **Sparse Multiscale NCC** (100개 패치, 패치 크기 13)     |
| **파라미터화**    | **$\mathfrak{se}(3)$** 공간 기반 (미분 가능성 확보)          |
| **최적화 도구**   | **Adam** 오차 역전파 기반 포즈 업데이트                        |
| **학습률 (회전)** | $7.5 \times 10^{-3}$ (**Rotational components** ) |
| **학습률 (이동)** | $7.5 \times 10^0$ (**Translational components** ) |
| **반복 횟수**    | 250회 (**Iterations** )                            |
| **스케줄러**     | **Step Decay** (25회마다 0.9배 감소)                    |


# Experiments
## 5.1. Datasets
카메라 포즈(intrinsic matrix)와 평가용 expert annotation을 제공하는 2개의 공개 데이터 셋 사용 
**DeepFluoro dataset** (6 patient (for each 1CT, 24-111x-rays), pelvic)
모델 검증용.
**Ljubljana dataset** (뇌혈관 수술 받은 환자 10명, )
혈관 검증에도 모델 적용 가능한지 테스트 용 (범용성 확인). 기존 모델의 하이퍼파라미터 변경하지 않고 적용
**raw image 처리**
연구에서 사용한 diffPose renderer에 맞게 intraoperatively measure X-ray 이미지 처리
Voxel에 의해 흡수된 에너지 총량 이미지
$$I_{\mu}(p)=\log I_0 - \log I(p)$$
initial intensity는 이미지상 intensity가 max인 pixel 기준으로 설정 $I_{0} = \text{max}\,I(r)$
collimator 현상을 없애기 위해 각 모서리 50 pixels 단위로 crop

본 연구의 데이터 셋들을 보면, CT데이터와 X-RAY이미지가 다 다른날 찍은 것들 -> test set leakage 예방

## 5.2. Baseline Methods
여러 2D/3D registraion 모델들이랑 비교

**supervised pose regresser**
PoseNet, 

**Unsupervised intensity based registration methods**
xReg : PA(PosteriorAnterior) pose 에서 gradient-free optimization 루틴 사용, path-based image-gradient NCC similarity mertric 사용

DiffDRR :  (아래 참고) CNN 초기화 없이 본 연구에서 사용한 같은 LR과 image-based loss 활용해서 pose 추정

**PnP**
U-Net : 2D X-ray 이미지에서 특정 랜드마크 추출 하도록 학습, 카메라 포즈 추정.
#### DiffDRR 역할: '미분 가능한 디코더'
**DiffPose** 프레임워크 내에서 **DiffDRR** 은 다음과 같은 핵심 역할
- **학습 단계 (Pretraining):** 환자의 $3D$ **CT** 로부터 $100$ 만 장의 가상 엑스레이(**DRR**) 를 초고속으로 생성하여 **CNN** 을 교육
- **최적화 단계 (Inference):** 현재 포즈 $\hat{\mathbf{T}}$ 를 받아 실시간으로 패치 이미지(**Sparse DRR**) 를 그림. 이 렌더링 과정이 **PyTorch** 의 자동 미분(**Auto-grad**) 과 호환되기 때문에, 이미지 오차로부터 포즈 변화량까지의 '미분 길' 열림.


## 5.3. Results
**DeepFluoro dataset**
![[Pasted image 20260406154356.png|600]]
123
![[Pasted image 20260406155631.png|500]]
PnP-Regularizer : X-reg+PnP

**Ljubljana dataset.**
뇌혈관의 경우 PnP는 부족한 Data set으로 인해 제대로 학습이 안됨
![[Pasted image 20260406161110.png|600]]
DiffPose 에서 좋은 성능 보임


**Ablation Studies**
![[Pasted image 20260406160240.png|400]]
Test-time optimizer가 큰역할, 각 loss들도 큰 비중을 차지 하는 것을 알 수 있음.


# Discussion
**Limitation and future work**
현재는 뼈 전체를 하나의 rigid body로 보고 정합을 진행하지만, 추후 piecewise rigid transformation으로 확장 가능함. (여러 뼈를 구분해서 정합)
현재 학습 속도가 너무 느리지만, fine tuning을 통해 속도 높일 수 있을거라 기대

**Contribution**
본 연구는 최초로 자가 지도 (unsupervised) intraoperative 2D/3D registration 프레임 워크를 제안하고, 검증했다는 점에서 의의가 있음. 또한 뼈 정합에서 나아가 뇌 혈관 같은 미세 영역에 대해서도 적용 가능성을 보였음