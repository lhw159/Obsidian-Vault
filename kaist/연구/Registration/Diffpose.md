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

## Intro
Intensity-based 방식은 initial pose에 대해 제한된 범위와 민감도에 대한 한계가 있다
[52] Mathias Unberath, Cong Gao, Yicheng Hu, Max Judish, Russell H Taylor, Mehran Armand, and Robert Grupp. The impact of machine learning on 2d/3d registration for image guided interventions: A systematic review and perspective. Frontiers in Robotics and AI, 8:716007, 2021. 1

To this end, two methods for initial pose estimation are commonly deployed: **landmark-based localization** and **CNN-based pose regression.**

landmark-based localization
[30] Vincent Lepetit, Francesc Moreno-Noguer, and Pascal Fua. EPnP: An accurate O(n) solution to the PnP problem. Inter national journal of computer vision, 81:155–166, 2009. 2 
[31] ShiqiLi, ChiXu, andMingXie. Arobusto(n)solutiontothe perspective-n-point problem. IEEE transactions on pattern analysis and machine intelligence, 34(7):1444–1450, 2012. 2

실제 실험에서 X-RAY 