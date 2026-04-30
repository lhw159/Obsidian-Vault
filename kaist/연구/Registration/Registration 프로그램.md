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


**markerPoseGenerator**
두 카메라의 world 좌표계에 대한 pose (DPMat1, DPMat2)를 생성

shapeinitial파일 results/s0##/left or right/ 에 넣기

**GUI_BPF_GMP_Main**->Initial Pose Estimation(InitialPoseEstimation 프로그램 활성화)
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

