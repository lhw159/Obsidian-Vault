# 1. Introduction
발목은 일상에서 중요한 역할(가장 큰 하중 버티기)=> 발목 안정성및 구동성 확보를 위한 수술이 많음=>하지만 이런 수술의 실 영향을 파악하기 위한 기법은 부족한 상황=> marker set 기반 방식은 뼈에 직접 닿지 않는 마커로 인해 오차가 발생할 수 밖에 없고, 다른 정성적 방식은 정량적인 정보가 부족=> x-ray이미지를 통한 kinematics를 보고자 하니, 한 방향의 x-ray로는 부족=> 두 방향 x-ray 선을 이용하여 
# 2. Multi-frame feature-based 2D/3D pose estimation
anatomical system 물어보기

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






# 3. Multi-frame inensity-based 2D/3D pose registration
# 4. Joint kinematic analysis of symptomatic foot during walking
# 5. Conclusion
# My thought
Intro
~~
**chapter 2**
1:17로 나눠서 하는건 모든 피험자에 대한 model에 statistical mean anatomical coordinate system을 부여하기 위함인가?
수동 pca 이후 이걸 다시 initial pose 추정에 사용? data leakage 문제가 있지 않나
