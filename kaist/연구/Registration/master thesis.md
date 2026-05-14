# 1. Introduction
거종 골 결합증 완화 및 관절 운동성 재건 위한 발목 거골-종골 융합부 절제술(resection of the talocalcaneal coalition), 만성 발목 불안정성 해결을 위한 변형 brostrom 술식. 앞선 수술 법들의 효과를 분석하는 연구들은 한계(마커셋 사용, 고정자세
만 이용, 단일 관절 운동 영향)가 있음

해당 연구에선 두 방향 엑스선 영상 장비를 통해 위 두 수술 환자군에 대한 운동학 계산 및 수술 전후 관절 운동을 비교하여, 각 수술이 관절 운동에 미치는 영향 파악. 알고리즘 개발을 위해

1. feature based initial pose estimation
2. 골격 운동 자유도 제약 및 프레임 간 위치 변화 제약을 통한 정확성 향상, 최적화 시간 감소
3. 수동 정합 (manual registration)과의 비교를 통해 검증

발목은 일상에서 중요한 역할(가장 큰 하중 버티기)=> 발목 안정성및 구동성 확보를 위한 수술이 많음=>하지만 이런 수술의 실 영향을 파악하기 위한 기법은 부족한 상황=> marker set 기반 방식은 뼈에 직접 닿지 않는 마커로 인해 오차가 발생할 수 밖에 없고, 다른 정성적 방식은 정량적인 정보가 부족=> x-ray이미지를 통한 kinematics를 보고자 하니, 한 방향의 x-ray로는 부족=> 두 방향 x-ray 선을 이용하여 
# 2. Multi-frame feature-based 2D/3D pose estimation

==**Feature Extraction**==
![[Pasted image 20260513122352.png|500]]
CycleGAN network => 실 x-ray이미지를 drr 스타일로 변경
Mask-RCNN => drr 스타일 x-ray이미지에 뼈 부분 segmentation
DeepLabCut network => drr 스타일 x-ray 이미지에서 뼈의 characteristic point 추출, 키 포인트 이름짓기

학습과정
1. 3차원 기초 데이터 확보 (3D CT Segmentation)
먼저, 정답지(GT)의 원천이 되는 고해상도 3차원 데이터를 준비합니다.
- **대상:** 18명의 환자로부터 촬영된 골격 **CT** 영상.
- **작업:** **CT** 영상에서 뼈 부분만 정밀하게 분리(Segmentation)하여 3차원 뼈 형상(Shape)과 주요 특징점(Feature points)의 3차원 좌표 $(x, y, z)$ 를 확보합니다.
- **의의:** 이 18개의 정밀한 3D 모델이 수만 장의 가상 학습 데이터를 만들어내는 '원본' 역할을 합니다.

2.  가상 촬영 및 데이터 증강 (DRR Generation)
확보한 3D 모델을 가상 공간에 배치하고 컴퓨터로 사진을 찍습니다.
- **가상 카메라 설정:** 두 대의 가상 엑스선 촬영 장치를 배치합니다.
- **데이터 증강:** 카메라의 위치와 각도를 수없이 바꿔가며 다양한 방향에서 뼈를 촬영합니다.
- **결과물 (Input):** 실제 **X-ray** 와 유사하게 계산된 가상 영상인 **DRR** (Digitally Reconstructed Radiograph) 영상을 생성합니다.

3. 정답지 자동 생성 (Automatic GT Projection)
**DRR** 영상을 만드는 순간, 그 영상에 딱 맞는 정답지(GT)도 수학적 계산으로 함께 출력합니다.
- **2D 뼈 영역 (Mask):** 3차원 뼈 형상을 현재 카메라 각도에 맞춰 2차원 평면에 투영하여, 뼈가 있는 부분만 색칠된 마스크 영상을 얻습니다.
- **2D 특징점 (Coordinates):** 3차원 특징점 좌표 $(x, y, z)$ 에 카메라 투영 행렬을 곱해, **DRR** 이미지상의 2차원 좌표 $(u, v)$ 를 구합니다.
- **의의:** 사람이 직접 마스킹하거나 점을 찍을 필요 없이, 컴퓨터가 0.1초 만에 오차 없는 정답을 만들어냅니다.

4. 네트워크 학습 (Model Training)
준비된 **[입력 영상 + 정답지]** 세트를 각 인공지능 네트워크에 입력하여 학습시킵니다.
- **Mask-RCNN 학습:** **[DRR 이미지 + 2D 뼈 마스크]** 를 입력하여, 영상에서 뼈의 영역을 스스로 찾아내도록 학습시킵니다.
- **DeepLabCut 학습:** **[DRR 이미지 + 2D 특징점 좌표]** 를 입력하여, 영상 내 특정 관절 포인트 등의 위치를 정확히 찍도록 학습시킵니다.

==**2.2.1 Statistical pose model**==
statistical mean anatomical coordinate system (smacs)
icp를 통해 뼈의 형상들이 최ㅐ한 겹치게 모은 다음, 각 뼈들의 ACS (anatomical coordinate system)을 평균내서 사용


==**2.2.2 Statistical pose model creation using principal component analysis of joint vectors**==
1. **대상 뼈와 관절 정의**
    
    - 5개의 뼈: tibia, talus, calcaneus, navicular, cuboid
        
    - 이들 사이의 4개 관절: ankle, subtalar, talonavicular, calcaneocuboid
        
2. **Joint pose vector 정의**
    
    - 각 관절에 대해 Hjoint=Hproximal−1⋅Hdistal
        
    - 즉, 근위(proximal) 뼈 좌표계에 대한 원위(distal) 뼈 좌표계의 상대 변환 행렬을 구함
        
3. **계층적 구조**
    
    - tibia를 기준으로 ankle → subtalar → talonavicular → calcaneocuboid 순으로 연결
        
    - 최종적으로 tibia 기준 전체 joint pose vector는 24×1 크기 (각 관절 변환을 벡터화하여 연결)
        
4. **데이터셋 구성**
    
    - 총 1266 프레임에 대해 pose vector를 추출
        
    - 따라서 데이터 행렬은 24×1266
        
5. **PCA 적용**
    
    - row-wise z-normalization으로 각 차원별 스케일을 맞춘 뒤 PCA 수행
        
    - 주성분 벡터 중 상위 12개만 사용하여 통계적 포즈 모델을 구성
        

👉 따라서 말씀하신 요약은 맞습니다. 즉, **4개 관절의 상대 변환을 벡터화 → tibia 기준 계층적 연결 → 24차원 pose vector → 1266 프레임 데이터 → PCA → 주성분 12개 추출**이라는 흐름

==**2.3.1 2D/3D initial pose estimation based on statistical pose model and bone features**==
- **목적**
    - Bi-plane X-ray 영상에서 발 뼈의 3차원 운동을 계산하기 위해, 뼈 모형을 영상 좌표계에 올바르게 배치하는 초기값(initial pose)을 얻는 과정

- **특징점 기반 초기 위치 설정**
    - X-ray 영상에서 추출된 2D 해부학적 특징점과 카메라 보정 정보를 이용해 3D 특징점 좌표를 복원합니다.
    - 평균 포즈 상태의 뼈 모형에서 대응되는 3D 특징점을 계산하고, 두 집합을 rigid transformation으로 맞추어 뼈 모형을 영상 좌표계에 배치합니다.

- **통계적 포즈 모델 적용**
    - 앞서 PCA로 만든 포즈 모델(12개의 주성분)을 사용해 발 전체 뼈의 포즈를 표현합니다.
    - Tibia를 중심 뼈(center bone)로 설정하고, tibia의 6자유도(회전 3 + 평행이동 3)와 포즈 모델의 12변수를 합쳐 총 18개의 변수로 발 전체 뼈의 3D 운동을 제어합니다.

- **최적화 과정**
    - 이 18개 변수를 변화시키면서 뼈 모형을 bi-plane 영상 평면에 투영합니다.
    - 투영된 뼈 영역과 실제 X-ray 영상에서 추출된 뼈 영역을 비교합니다.
    - 두 영역의 유사도를 **Dice coefficient**로 평가하고, 손실(loss)을 최소화하는 방향으로 최적화합니다.

- **결과**
    - 이렇게 하면 영상과 가장 잘 맞는 발 뼈의 3차원 초기 위치를 얻을 수 있고, 이후 multi-frame 최적화(2.3.2)로 이어집니다.


==**2.3.2 Multi-frame optimization using B-spline method**==
#### 단일 프레임 최적화의 한계

- s개의 프레임을 촬영했을 때, 단일 프레임 최적화 방식은 각 프레임마다 **18개의 변수**가 필요 → 총 변수 수 = 18×s.
- 프레임 수가 늘어나면 변수 수가 선형적으로 증가.
- 프레임 간 제약이 없어 결과가 **불연속적이고 매끄럽지 않으며**, 이상치(outlier)가 포함될 수 있음.
- **smoothness**와 **2차 미분 가능성(second-order differentiability)**을 보장할 수 없음.

#### B-spline 기반 multi-frame 최적화
- **방법**: 발 뼈 운동학을 각 프레임마다 직접 계산하는 대신, **B-spline 곡선의 제어점(control point)** 을 최적화하여 전체 프레임의 운동학을 표현. 2개의 spline 활용 => translation curve, quaternion spline curve.

- **식 (2.16)**: Quaternion 기반 B-spline 곡선 정의.
    - 회전은 quaternion으로 표현되며, B-spline basis function과 제어점 quaternion을 곱해 곡선을 구성.
    - 이를 통해 stance phase 전체에서 회전이 매끄럽게 이어짐.

- **식 (2.17)**: Multi-frame 최적화 흐름을 나타내는 flow chart.
    - 입력: bi-plane X-ray 영상
    - 과정: segmentation → registration → B-spline 기반 multi-frame fitting
    - 출력: 매끄럽고 안정적인 foot bone kinematics.

#### 변수 수 비교
- **단일 프레임 최적화**:
    - 총 변수 수 = 18×s.

- **Multi-frame 최적화 (B-spline)**:
    - 제어점 13개 사용.
    - 각 제어점마다 18개의 변수를 가짐.
    - 총 변수 수 = 18×13=234.
    - 프레임 수와 무관하게 일정한 변수 수로 전체 프레임을 최적화 가능.

#### 핵심 요약
- 단일 프레임 방식은 프레임 수에 따라 변수가 폭증하고, 결과가 매끄럽지 않음.
- B-spline 기반 multi-frame 방식은 **제어점만 최적화**하여 전체 프레임을 표현 → 변수 수가 고정되고, 결과가 smooth + differentiable.
- 본 연구에서는 **13개의 제어점**을 사용하여 multi-frame 최적화를 수행했으며, 총 **234개의 변수**로 발 뼈 운동학을 계산.
- 식 (2.16)은 quaternion 기반 B-spline 곡선을 정의하고, 식 (2.17)은 최적화 과정의 흐름을 도식화하여 multi-frame 구조를 설명.

**B-spline 곡선을 통해 발 뼈 운동학을 multi-frame으로 최적화하여 변수 수를 줄이고, 매끄럽고 안정적인 결과를 얻는 방법을 제시하며, 이를 quaternion 기반 곡선(식 2.16)과 최적화 flow chart(식 2.17)로 구체화**


==**2.3.3 Bone feature based multi-frame 2D/3D registration of foot bone skeleton**==
#### 각 방법의 특성
- **Feature point 기반 2D/3D registration (단일 프레임)**
    - 장점: 계산 속도 빠름, 수렴 속도 빠름
    - 단점: 정확도가 낮음

- **Feature area 기반 2D/3D registration (단일 프레임)**
    - 장점: 중간 수준 계산 속도, 빠른 수렴, 넓은 최적화 경계
    - 단점: multi-frame 적용 시 매끄럽지 않고 outlier 발생 (프레임 간 제약 없음)

- **Multi-frame feature area 기반 2D/3D registration (B-spline 곡선 적용)**
    - 장점: 높은 정확도, 매끄러움(smoothness), 2차 미분 가능성 보장
    - 단점: 계산 속도 느림, 수렴 속도 느림

#### 제안된 최적화 절차 (순차적 적용)

1. **Feature point 기반 단일 프레임 registration**
    - 빠른 계산을 위해 먼저 수행.
    - 결과는 정확도가 낮지만 초기값으로 활용.

2. **Feature area 기반 단일 프레임 registration**
    - 1단계 결과를 초기값으로 사용.
    - 정확도를 높이고 최적화 경계를 좁힘.

3. **Multi-frame feature area 기반 registration (B-spline 적용)**
    - 2단계 결과를 초기값으로 사용.
    - 프레임 간 smoothness와 2차 미분 가능성을 확보.
    - 단일 프레임 방식의 outlier 문제 해결.
    - 초기값이 정확하므로 multi-frame 최적화의 느린 수렴 문제를 보완.

#### 검증 과정
- **데이터**: 정상 피험자 5명의 발 뼈 X-ray 영상 (bi-plane fluoroscopy system으로 연속 촬영).
- **Ground truth**: 숙련된 기술자가 수행한 2D/3D registration 결과.
- **Proposed method 결과**: feature 기반 multi-frame registration으로 계산된 운동학.
- **Error 계산**:
    - Rotation error → ground truth와 approximate orientation의 quaternion 차이를 axis-angle로 변환 후 angle 크기.
    - Translation error → ground truth와 approximate position의 Euclidean distance.
    - RMS error → 각 프레임별 error를 모아 root mean square로 계산.

==**Disscusion**==
When bone shape is close to spherical symmetry, projected area variation due to orientation change of the bone decreases.

# 3. Multi-frame inensity-based 2D/3D pose registration
==**Purpose**==
기존의 frame by frame 방식은 실제 사람의 모션의 continuity와 미분가능성(속도/가속도 계산)을 보장하지 못함=> B-spline을 통한 multi-frame 최적화를 진행하여 위 2가지 특성 확보
==**Multi-frame intensity-based 3D pose estimation**==
![[Pasted image 20260514111836.png|697]]
전체 이미지 비교를 통해 loss 계산

시리얼하게 b-spline 적용=>knot을 (5개->11개로 증가시키며)

SSIM을 통해 LOSS 계산

surrogate optimization을 통해 효율화 (time consuming cost function evaluation?)
Surrogate optimization method is an optimization method that is effective on the problems with time consuming cost function evaluation [20, 21].

==**d**==
==**d**==
==**d**==
==**d**==
==**d**==
# 4. Joint kinematic analysis of symptomatic foot during walking

==**4.1.1 Talocalcaneal coalition and coalition resection**==
talus와 calcaneous가 서로 붙어버는 Talocalcaneal coalition=> 발목의 운동 제한이 생기고, 평발을 유발함=> 절제술(resection)을 통해 완화
본 연구의 bpf 기술을 통해 **발목관절, 거종골관절(subtalar), 경종골관절(tibiocalcaneal)** 의 6자유도 운동을 플로팅하고, 보행 단계별 Range of motion (ROM)을 분석

==**4.1.2 Chronic lateral ankle instability and Modified Broström Operation (MBO)**==
가장 흔한 손상 인대: 전거비인대(ATFL), 심한 경우 종비인대(CFL)도 함께 손상.=>Modified Broström Operation (MBO)를 통한 보강 술식=> 기존 연구는 정적 자세만 비교하거나, 환자/정상군 비교만 시행
본 연구에선 bpf 기술을 통해 6자유도 운동학을 수술 전/후 비교, 각 관절 운동 범위 계산

==**4.2.1 Population distribution and data acquisition of coalition patients**==
ROM의 경우 Mann-Whitney U-test를 통해 분석
# 5. Conclusion
# My thought
Intro
~~
**chapter 2**
모델 학습(feature point 추출/마스킹)은 drr을 통해 한건지 
=>맞음

1:17로 나눠서 하는건 모든 피험자에 대한 model에 statistical mean anatomical coordinate system을 부여하기 위함인가?
=> 모든 피험자에 대한 일반전인 좌표계 형성->추후 다른 피험자가 들어와도 동일 좌표계를 사용함으로서 분석에 일반화된 기준을 적용

수동 pca 이후 이걸 다시 initial pose 추정에 사용? data leakage 문제가 있지 않나?
=>이미 정합된 데이터 있었음

B-spline 방식으로 최종 최적화는 어떻게 진행하는건지=>surogate
[오차 계산법 (어떤 최적화 메트릭을 쓸지, 어떤 loss를 쓸지) 생각해봐야할듯]


**chapter 3**
무릎 관절 모션 분석 (intensity 기반 정합)
Ohnishi, T [43] et al proposed a knee joint motion study analyzed by bi-plane X-ray images and intensity-based 2D/3D registration. 

intensity 기반 다양한 metric들
Penney, Graeme P., et al suggested various methods such as [pattern intensity, normalized cross correlation, entropy difference, mutual information, gradient correlation, and gradient difference.] [18]

SSIM (structural similarity index measure) (이건 3D 형상 제작에 사용하는 SSIM과 다른거)
Wang, Zhou, et al suggested image difference scoring index named structural similarity index measure (SSIM), based on the degradation of structural information [19]
![[Pasted image 20260514105511.png|500]]
chapter 4
ct volume 얻는 과정?
CT volume data were segmented by skilled technicians to obtain the bone surface model and bone intensity model.



3D 이미지???