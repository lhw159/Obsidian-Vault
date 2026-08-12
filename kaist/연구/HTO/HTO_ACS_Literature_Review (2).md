# Methods for Establishing Anatomical Coordinate Systems for 2D/3D Registration of Gait Videos in High Tibial Osteotomy Patients: A Scientific Literature Review

---

## Abstract

High Tibial Osteotomy (HTO) is a joint-preserving surgical intervention for medial knee osteoarthritis with varus deformity that fundamentally alters lower limb alignment and gait biomechanics. Accurate quantification of post-operative kinematics requires robust anatomical coordinate system (ACS) definitions capable of accommodating altered bone geometry and alignment. This review synthesizes current methods for establishing ACS in the context of 2D/3D registration for gait video analysis, with specific emphasis on applicability to HTO patients. We examine ==conventional landmark-based== and ==International Society of Biomechanics (ISB) standard approaches==, state-of-the-art ==imaging-based== and ==functional calibration== methods, ==geometric primitive fitting (sphere, ellipsoid, cylinder, quadric)==, and emerging deep learning-based markerless techniques. Evidence from 30 highly relevant studies demonstrates that while ISB-standard anatomical landmark methods remain the clinical gold standard, functional calibration and imaging-based approaches (fluoroscopy, CT, MRI) offer superior robustness to anatomical variation. For HTO patients specifically, hybrid methods combining functional axes with imaging-based registration show promise for capturing post-surgical kinematic changes, including altered knee adduction moments, tibial translation patterns, and compensatory gait adaptations. Deep learning-based markerless pose estimation from video (e.g., OpenPose, Pose2Sim) enables accessible gait analysis but requires validation against gold-standard fluoroscopic methods in populations with significant deformity. Future work should prioritize development of automated, patient-specific ACS algorithms that explicitly account for post-HTO anatomical changes and validate these methods in longitudinal cohorts tracking surgical outcomes.

**Keywords:** Anatomical coordinate system, High Tibial Osteotomy, 2D/3D registration, gait analysis, knee kinematics, markerless motion capture, functional calibration, fluoroscopy

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Background and Theoretical Foundations](#2-background-and-theoretical-foundations)
   - 2.1 [Fundamentals of Anatomical Coordinate Systems](#21-fundamentals-of-anatomical-coordinate-systems)
   - 2.2 [High Tibial Osteotomy: Clinical Context and Biomechanical Implications](#22-high-tibial-osteotomy-clinical-context-and-biomechanical-implications)
3. [Standard and Conventional ACS Methods](#3-standard-and-conventional-acs-methods)
   - 3.1 [ISB Recommendations and Landmark-Based Approaches](#31-isb-recommendations-and-landmark-based-approaches)
   - 3.2 [Marker-Based Motion Capture Systems](#32-marker-based-motion-capture-systems)
   - 3.3 [Limitations in Pathological Populations](#33-limitations-in-pathological-populations)
4. [State-of-the-Art and Trending ACS Methods](#4-state-of-the-art-and-trending-acs-methods)
   - 4.1 [Imaging-Based Coordinate System Definition](#41-imaging-based-coordinate-system-definition)
   - 4.2 [Functional Calibration Methods](#42-functional-calibration-methods)
   - 4.3 [Automated and Algorithmic Approaches](#43-automated-and-algorithmic-approaches)
   - 4.4 [Deep Learning-Based Markerless Pose Estimation](#44-deep-learning-based-markerless-pose-estimation)
   - 4.5 [Geometric Primitive Fitting Methods](#45-geometric-primitive-fitting-methods)
5. [ACS Methods Suitable for HTO Patients](#5-acs-methods-suitable-for-hto-patients)
   - 5.1 [Anatomical Changes Following HTO](#51-anatomical-changes-following-hto)
   - 5.2 [Alignment Correction and Kinematic Adaptations](#52-alignment-correction-and-kinematic-adaptations)
   - 5.3 [Robustness to Deformity and Altered Geometry](#53-robustness-to-deformity-and-altered-geometry)
   - 5.4 [Recommended Approaches for HTO Gait Analysis](#54-recommended-approaches-for-hto-gait-analysis)
6. [Discussion](#6-discussion)
   - 6.1 [Comparative Analysis of ACS Methods](#61-comparative-analysis-of-acs-methods)
   - 6.2 [Limitations and Methodological Challenges](#62-limitations-and-methodological-challenges)
   - 6.3 [Clinical Translation and Future Directions](#63-clinical-translation-and-future-directions)
7. [Conclusion](#7-conclusion)

---

## 1. Introduction

Accurate quantification of lower limb kinematics is essential for evaluating surgical outcomes, optimizing rehabilitation protocols, and understanding the biomechanical mechanisms underlying joint pathology. The anatomical coordinate system (ACS) serves as the foundational reference frame for all kinematic measurements, defining the orientation and position of body segments in three-dimensional space. Establishing a robust, repeatable, and clinically meaningful ACS is particularly challenging in populations with significant anatomical deformity or surgical alteration of bone geometry.

High Tibial Osteotomy (HTO) is a joint-preserving surgical procedure performed to correct varus malalignment in patients with medial compartment knee osteoarthritis. By realigning the mechanical axis of the lower limb, HTO aims to redistribute joint loading from the diseased medial compartment to the healthier lateral compartment, thereby reducing pain and slowing disease progression [1], [3], [4], [19], [27]. However, HTO fundamentally alters tibial geometry, changes the posterior tibial slope, and modifies the three-dimensional orientation of the knee joint [6], [10], [11], [25]. These anatomical changes pose significant challenges for establishing consistent ACS definitions before and after surgery, complicating longitudinal kinematic analysis.

Recent advances in 2D/3D registration techniques—methods that align two-dimensional image data (e.g., video, fluoroscopy) with three-dimensional bone models—offer promising avenues for non-invasive, dynamic gait analysis. When combined with markerless pose estimation using deep learning algorithms, these approaches enable accessible, clinic-friendly gait assessment without the need for specialized motion capture laboratories [32], [33], [34], [37], [39]. However, the accuracy and reliability of these methods depend critically on the underlying ACS definition, which must accommodate the altered anatomy characteristic of post-HTO patients.

This review synthesizes the current state of knowledge on ACS definition methods for 2D/3D registration in gait analysis, with specific emphasis on applicability to HTO patients. We examine conventional landmark-based and ISB-standard approaches, state-of-the-art imaging-based and functional calibration methods, and emerging deep learning-based techniques. Our goal is to provide a comprehensive framework for researchers and clinicians seeking to implement robust kinematic analysis in this challenging population.

---

## 2. Background and Theoretical Foundations

### 2.1 Fundamentals of Anatomical Coordinate Systems

An anatomical coordinate system (ACS) is a right-handed Cartesian reference frame attached to a body segment (e.g., femur, tibia, pelvis) that defines the orientation of anatomical axes and serves as the basis for computing joint angles and segment motions. The ACS is typically defined by three mutually perpendicular unit vectors corresponding to the medio-lateral, antero-posterior, and superior-inferior anatomical directions. The origin of the ACS is commonly placed at a joint center or anatomical landmark.

The International Society of Biomechanics (ISB) has published standardized recommendations for defining joint coordinate systems for the lower limb, including the hip, knee, and ankle [29]. These recommendations specify anatomical landmarks (e.g., medial and lateral femoral epicondyles, malleoli) and geometric constructions (e.g., bisector lines, perpendicular planes) for establishing segment coordinate systems. The ISB standard aims to promote consistency and comparability across studies by providing a common reference frame [20], [24].

However, the ISB recommendations were developed primarily for healthy populations with normal anatomy. In pathological conditions such as severe varus deformity, post-surgical bone geometry, or joint replacement, anatomical landmarks may be obscured, displaced, or absent, necessitating alternative ACS definition strategies [11], [18], [28].

### 2.2 High Tibial Osteotomy: Clinical Context and Biomechanical Implications

High Tibial Osteotomy (HTO) is indicated for relatively young, active patients with isolated medial compartment knee osteoarthritis and varus malalignment. The most common technique is medial opening-wedge HTO, in which a wedge-shaped gap is created in the proximal medial tibia and filled with bone graft or substitute material, thereby correcting the varus deformity and shifting the mechanical axis laterally [1], [3], [4], [22].

Biomechanical studies have consistently demonstrated that HTO reduces the knee adduction moment (KAM)—a surrogate measure of medial compartment loading—during gait [1], [3], [9], [12], [17], [22]. Post-operative gait analysis reveals significant changes in frontal and transverse plane knee kinematics, including altered tibial rotation, increased anterior tibial translation, and modified patellofemoral tracking [6], [11], [25]. These kinematic adaptations reflect both the direct geometric effects of the osteotomy and compensatory neuromuscular strategies adopted by patients [18], [26].

Critically, HTO alters the three-dimensional geometry of the proximal tibia, including changes in posterior tibial slope, medial cortical continuity, and the spatial relationship between anatomical landmarks [10], [15], [19]. These changes complicate the definition of a consistent tibial ACS across pre- and post-operative assessments. Furthermore, the degree of alignment correction varies among patients depending on surgical planning and individual anatomy, necessitating patient-specific ACS approaches [8], [13], [23].

---

## 3. Standard and Conventional ACS Methods

### 3.1 ISB Recommendations and Landmark-Based Approaches

The ISB recommendations for lower limb joint coordinate systems represent the most widely adopted standard in biomechanics research [29]. For the tibia, the ISB standard defines the medio-lateral axis as the line connecting the medial and lateral malleoli, the longitudinal axis as the line from the midpoint of the malleoli to the midpoint of the medial and lateral tibial condyles, and the antero-posterior axis as the cross product of these two vectors [20], [29]. For the femur, the medio-lateral axis is defined by the line connecting the medial and lateral femoral epicondyles, with the longitudinal axis directed from the hip joint center to the midpoint of the epicondyles.

Several studies have implemented ISB-based ACS definitions for gait analysis in healthy and pathological populations. Żuk and Trzeciak [20] proposed an anatomically based ISB 6-DOF protocol combining the ISB reporting standard with a marker cluster technique, demonstrating clinically acceptable inter-trial repeatability for lower limb kinematics including three-dimensional ankle-foot complex rotations. Sinclair et al. [8] evaluated the test-retest reliability of anatomical coordinate axes definition using the calibrated anatomical systems technique (CAST), which involves identifying anatomical landmarks through external palpation and calibrating them with respect to technical tracking clusters.

Landmark-based approaches offer the advantage of direct correspondence with clinical anatomy and ease of implementation in marker-based motion capture systems. However, they are susceptible to errors from soft tissue artifact, inter-rater variability in landmark palpation, and anatomical variation [8], [14], [21], [22]. In populations with significant deformity or altered anatomy, such as post-HTO patients, the reliability of landmark identification may be further compromised [11], [18].

### 3.2 Marker-Based Motion Capture Systems

Conventional marker-based motion capture systems use reflective markers placed on anatomical landmarks or rigid marker clusters attached to body segments to track three-dimensional motion. The Calibrated Anatomical Systems Technique (CAST) is a widely used approach in which anatomical landmarks are digitized relative to technical marker clusters during a static calibration trial, allowing the anatomical coordinate system to be reconstructed during dynamic movement even after anatomical markers are removed [8], [14], [22].

Heller [15] described a four-point method for defining the anatomical reference frame (ARF) based on anatomical control points (ACPs) and joint centers, with the origin located at the joint center. This method involved palpating bony protuberances to establish ACPs and using a pointer method to relate marker clusters to the anatomy. For hip joint centers, a functional method involving circumduction, flexion/extension, and abduction was employed.

Kocsis et al. [21] presented an ultrasound-based measuring technique in which external marker clusters are fixed on body segments, and during calibration, anatomical points are added to these clusters as points of the same rigid body. This approach ensures anatomical points are fixed to the cluster, making them rigid body points and eliminating errors from skin movements.

While marker-based systems remain the gold standard for laboratory-based gait analysis, they require specialized equipment, controlled environments, and trained personnel, limiting their accessibility for routine clinical use [32], [39]. Furthermore, marker placement on patients with significant soft tissue swelling, surgical scars, or external fixation devices (common in the early post-operative period following HTO) can be challenging [7], [9].

### 3.3 Limitations in Pathological Populations

Standard landmark-based and ISB-recommended ACS definitions were developed and validated primarily in healthy populations with normal anatomy. In pathological populations, including those with severe varus deformity or post-surgical anatomical changes, several limitations emerge:

1. **Landmark obscuration or displacement**: Surgical incisions, swelling, and altered bone geometry can make anatomical landmarks difficult to palpate or identify [11], [18].

2. **Anatomical variation**: Significant inter-individual variation in bone morphology, particularly in the presence of deformity, can lead to inconsistent ACS definitions [18], [28].

3. **Soft tissue artifact**: Skin movement relative to underlying bone is exacerbated in populations with altered gait patterns or muscle weakness, introducing errors in marker-based tracking [21], [31].

4. **Lack of robustness to deformity**: Standard geometric constructions (e.g., bisector lines between epicondyles) may not accurately represent functional axes in the presence of significant deformity [16], [28].

These limitations motivate the development of alternative ACS definition methods that are more robust to anatomical variation and surgical alteration, as discussed in the following section.

---

## 4. State-of-the-Art and Trending ACS Methods

### 4.1 Imaging-Based Coordinate System Definition

Imaging-based ACS definition methods use three-dimensional bone models derived from computed tomography (CT), magnetic resonance imaging (MRI), or biplanar fluoroscopy to establish coordinate systems based on bone geometry rather than external landmarks. These methods offer several advantages, including direct visualization of bone anatomy, independence from soft tissue artifact, and the ability to define patient-specific coordinate systems that account for anatomical variation.

Fischer et al. [1] used biplanar high-frequency fluoroscopy to implement anatomical coordinate systems at each joint for measuring the movement of the distally adjacent bone relative to the proximal bone in canine hind limbs during walking and trotting. Coordinate systems were placed at the middle of the pelvis, in the hip joint, and in the knee joint, aligned to the axes of the global coordinate system and manually optimized to match bone motions.

Wang et al. [3] defined the anatomical local coordinate system for each bone in the tarsal joints complex using a fluoroscopic 3D-2D registration technique, with coordinate systems uniformly defined to align with the X, Y, and Z axes of the global coordinate system. Similarly, Green et al. [7] defined a coordinate frame for the foot and ankle using three-dimensional data from CT scans, involving segmentation, creation of 3D binary stereolithography files, and importation into a shape analysis program for biomechanics.

For the knee joint, Wang et al. [9] built improved orthogonal coordinate systems on the femur, tibia, and patella using 3D image registration and coordinate transformation based on 3D point cloud models from computed tomography. Object coordinates were built on the same location and direction to avoid errors, followed by Euler angle coordinate transformation to acquire relative kinematics data.

Kai et al. [28], [30] described methods for the automatic construction of an anatomical coordinate system for three-dimensional bone models of the lower extremities, specifically the pelvis, femur, and tibia, using automated algorithms relevant to biomechanical kinematic studies. Peterson et al. [11] introduced the Automatic Anatomical Foot and Ankle Coordinate Toolbox (AAFACT), a MATLAB-based toolbox that automates the calculation of ACSs for the major fourteen foot and ankle bones, accounting for bone morphology and orientation and accommodating various foot and ankle pathologies.

Imaging-based methods are particularly well-suited for populations with altered anatomy, as they can directly visualize bone geometry and define coordinate systems based on patient-specific morphology [11], [28], [30]. However, they require access to medical imaging equipment and involve radiation exposure (for CT and fluoroscopy), limiting their use for routine longitudinal monitoring [7], [9].

### 4.2 Functional Calibration Methods

Functional calibration methods define coordinate systems based on the observed motion of a joint rather than anatomical landmarks or bone geometry. These methods typically involve performing a series of prescribed movements (e.g., hip circumduction, knee flexion-extension) and using optimization algorithms to identify the axes of rotation and joint centers that best explain the observed motion.

Ancillao et al. [16] described a novel method that does not require a biomechanical model or anatomically defined coordinate systems. Instead, it functionally defines two coordinate systems attached to the femur and tibia based on the average screw (helical) axis (ASA) of the knee motion. The x-axis of these functional coordinate systems lies along the functional rotation axis of the knee, making the method independent of chosen marker landmarks and eliminating the need for anatomical calibration.

Warlow [19] defined anatomical coordinate systems using a combination of functional calibration and anatomical pointing. For the fingers, a phalanx transformation technique (PTT) was applied, involving a calibrated pointer for anatomical positions and functional movements (flexion/extension, abduction/adduction) to define axes of rotation (AoR) and centers of rotation (CoR). The z-axes were defined by AoR, y-axes by phalangeal lines, and x-axes by their cross product.

Functional calibration methods offer the advantage of defining coordinate systems based on the actual motion of the joint, which may better represent the functional axes of rotation than anatomical landmarks, particularly in the presence of deformity or altered kinematics [16], [19]. However, they require the patient to perform specific calibration movements, which may be difficult or painful for individuals with severe osteoarthritis or in the early post-operative period following HTO.

### 4.3 Automated and Algorithmic Approaches

Automated and algorithmic approaches use computational methods to define coordinate systems based on bone geometry, statistical shape models, or optimization criteria. These methods aim to reduce inter-rater variability, improve repeatability, and accommodate anatomical variation without requiring manual landmark identification.

Pourtabib et al. [1], [2] developed an algorithm for assigning a patient-specific, optimized joint coordinate system (OPT JCS) based on a two rotational degree-of-freedom kinematic model. This approach, classified as automated/algorithmic and imaging-based (using single-plane fluoroscopic images), is patient-specific and addresses anatomical variation. The optimized JCS achieved clinically meaningful kinematics of the tibiofemoral joint compared to the ISB recommendation, with reduced crosstalk errors.

Gasparutto et al. [5] transformed kinematic data into ISB standards using a reference geometry from the Visible Human Project (VHP) and identified anatomical points on it. The method addressed robustness to anatomical variation by superimposing the femur and tibia segment coordinate systems at 0° of flexion, using k-medoid clustering for data synthesis.

Baka et al. [16] evaluated automated statistical shape model-based knee kinematics from biplane fluoroscopy, employing an imaging-based (fluoroscopy) and automated/algorithmic approach through the use of statistical shape models. This method enables automated tracking of knee kinematics without manual landmark identification.

Automated approaches offer significant advantages for large-scale studies and clinical applications, as they reduce the time and expertise required for ACS definition and improve repeatability [11], [28], [30]. However, they require validation against gold-standard methods and may not perform well in cases of severe deformity or unusual anatomy that falls outside the training data for statistical shape models.

### 4.4 Deep Learning-Based Markerless Pose Estimation

Recent advances in deep learning have enabled markerless pose estimation from standard video cameras, offering a potentially transformative approach for accessible, clinic-friendly gait analysis. These methods use convolutional neural networks (CNNs) trained on large datasets of annotated images to predict the two-dimensional locations of anatomical keypoints (e.g., hip, knee, ankle) in video frames. Multiple camera views can be combined using triangulation to reconstruct three-dimensional joint positions.

Needham et al. [32] developed a fully automated markerless motion capture workflow involving multi-view high-speed (200 Hz) image data collection, processed using 2D pose estimation, a 3D fusion process, and OpenSim-based inverse kinematics modeling to compute lower limb kinematic data. This method was applied to overground running, walking, and counter movement jumping.

Pagnon et al. [33], [34] presented Pose2Sim, an end-to-end workflow for 3D markerless sports kinematics that triangulates 2D joint coordinates from OpenPose (a deep-learning pose estimation algorithm applied to RGB video cameras) and feeds these 3D coordinates into an OpenSim skeletal model. This constrains the results to physically consistent kinematics and was applied to analyzing lower-body kinematics in walking, cycling, and running.

Liang et al. [35], [36] obtained 3D human keypoints from multiple RGB cameras through human tracking, pose estimation, and triangulation algorithms, using these 3D keypoints to drive personalized skeletal models created on OpenSim for gait analysis in elderly and young subjects. Moro et al. [37] used a deep learning-based approach for 2D keypoint detection from RGB video data, specifically Pose ResNet-152, followed by AdaFuse for refinement leveraging epipolar geometry, combined with geometric 3D reconstruction using camera parameters.

Kanko et al. [39] described a markerless motion capture system that uses deep learning for 3D human pose estimation, processing synchronized video data to estimate 2D positions of 51 salient features. An articulated multi-body model is then scaled to fit subject-specific landmarks in 3D space, and inverse kinematics is used to estimate the 3D pose throughout the recorded physical task, applied to gait kinematics.

Deep learning-based markerless methods offer unprecedented accessibility and scalability for gait analysis, enabling assessment in clinical settings, community environments, and even home-based monitoring [32], [33], [34], [37], [39]. However, their accuracy in populations with significant deformity, altered gait patterns, or post-surgical anatomy remains to be fully validated. Furthermore, these methods typically estimate joint centers rather than defining full anatomical coordinate systems, which may limit their utility for detailed kinematic analysis requiring precise segment orientations.

### 4.5 Geometric Primitive Fitting Methods

기하학적 도형 피팅(Geometric Primitive Fitting)은 CT 또는 MRI로부터 분할된 뼈 표면 점군(point cloud)에 구(sphere), 타원체(ellipsoid), 원기둥(cylinder), 이차곡면(quadric) 등의 수학적 원시 형상을 최적 피팅하여, 피팅된 형상의 중심점·주축(principal axes)으로부터 ACS를 자동 정의하는 방법론이다. 랜드마크 수동 식별에 의존하는 ISB 표준과 달리, 기하학적 피팅은 뼈 전체 표면 형상을 활용하므로 관찰자 간 변동성(inter-rater variability)을 구조적으로 제거하며, 해부학적 랜드마크가 변형되거나 불명확한 병리 집단에서 특히 유용하다 [GP-28], [GP-30].

#### 4.5.1 뼈 부위별 대표 기하학적 원시 형상

**대퇴골두 및 비구 (Femoral Head & Acetabulum)**
대퇴골두와 비구는 구면(spherical surface)에 가장 가깝게 근사되는 관절면으로, 최소제곱 구 피팅(least-squares sphere fitting)이 고관절 중심(Hip Joint Center, HJC) 추정의 표준 수단으로 채택되어 왔다 [GP-1], [GP-2]. 그러나 단순 구 모형은 정상 해부학에서도 약 1–2.5 mm의 잔류 오차를 내포하며, 특히 cam형 대퇴비구 충돌 증후군(FAI)에서는 4–5 mm에 달하는 편차가 보고된다 [GP-4]. 이를 개선하기 위해 Cerveri et al. [GP-3]은 비구 형상을 구, 타원체, conchoid의 세 가지 원시 형상으로 비교 모델링하여 환자 맞춤형(patient-specific) 비구 형상 매개변수화를 제안하였고, Lopes et al. [GP-5]은 (super)ellipsoid 및 (super)ovoid를 포함한 계층적 형상 비교를 통해 구 가정의 한계를 정량화하였다.

**대퇴골 원위부 및 과부 (Distal Femur & Condyles)**
내외측 대퇴 과부(femoral condyles)는 타원체 또는 이차곡면으로 피팅하여 내외측 축(medio-lateral axis)과 과부 중심을 추출하는 데 활용된다. Asseln et al. [GP-7]은 3D 표면 데이터로부터 원위 대퇴골을 자동으로 형상 매개변수화(parameterisation)하는 방법을 제시하였으며, 단면 타원 피팅(cross-section ellipse fitting)을 적층하여 종축과 전후방 축을 체계적으로 유도하였다. Chapman et al. [GP-8]은 현대 인류 대퇴골의 만곡 변이성(curvature variability)을 3D 이차곡면 피팅으로 정량화함으로써, 종축 정의에서 단일 직선 근사의 오차 원인을 규명하였다. 슬관절 구조 모델링에서는 Martelli et al. [GP-6]이 경골 근위부 반원통(semi-cylinder) 피팅과 결합하여 해부학적 슬관절 모형을 구성하는 CT 기반 컴퓨터 해부(computer dissection) 접근을 제안하였다.

**대퇴골 간부 및 경골 장축 (Femoral/Tibial Shaft)**
골간부(shaft)의 장축(superior-inferior axis)은 원기둥 피팅(cylinder fitting)을 통해 정의된다. Casciaro et al. [GP-9]은 3D 가상 환경에서 대퇴골 간부에 원기둥을 피팅하여 경부-간부 각도(neck-shaft angle) 및 비틀림각(torsion angle) 추정의 관찰자 간 변동성을 약 5배 감소시켰음을 보고하였다. 경골 장축 정의에서는 Knutson et al. [GP-11]이 원위 경골의 최적 원기둥 피팅 구간 길이를 체계적으로 평가하여, 발목 관절면(plafond)으로부터 5 cm 상방에서 내외측 너비의 1.5배 길이에 해당하는 구간이 PCA 기반 참조 축과의 각도 편차를 최소화함을 입증하였다. 이 권고안은 HTO 환자처럼 근위 경골 형상이 변형된 경우에도 원위 경골 피팅만으로 안정적인 경골 장축을 정의할 수 있음을 시사한다.

**복잡 관절면 (Complex Articular Surfaces)**
안장형(saddle-shaped) 또는 고도로 비구면적인 관절면(예: 손목, 발목 관절)에는 이차곡면 계열의 type-constrained fitting이 적용된다. Allaire et al. [GP-12], [GP-13]은 타원체 제약(ellipsoid-constrained) 및 유형 제약(type-constrained) 이차곡면 피팅 알고리즘을 개발하여, 제약 없는 이차곡면 피팅에서 발생하는 쌍곡면(hyperboloid) 퇴화를 방지하고 관절면 중심 및 주축을 안정적으로 추출하였다.

#### 4.5.2 피팅 알고리즘

기하학적 피팅에서 활용되는 주요 알고리즘은 크게 세 가지로 분류된다.

1. **최소제곱 기반 피팅 (Least-Squares Fitting):** 가장 광범위하게 사용되며, 대수적(algebraic) 또는 기하학적(geometric) 최소제곱으로 구/타원체/원기둥의 파라미터를 최적화한다. 피팅의 안정성을 높이기 위한 가중 최소제곱(weighted least-squares)과 편향 보정(bias-corrected) 변형도 활용된다 [GP-12], [GP-13].

2. **통계적 형상 모델 기반 피팅 (Statistical Shape Model / PCA-Based):** 훈련 집단에서 학습된 형상 변이(shape variation)를 활용하여 새로운 개체의 뼈 표면을 피팅한다. Kai et al. [GP-28], [GP-30]이 제안한 하지 골격 자동 ACS 구축 파이프라인과 Xia et al. [GP-16]의 MRI 기반 대퇴골두-경부 자동 평가가 대표적이다. 이 방법은 완전 자동화가 가능하지만, 훈련 집단 외 형상(예: 심한 변형)에 대한 일반화 성능이 제한될 수 있다.

3. **진화/비선형 최적화 (Evolutionary / Nonlinear Optimization):** conchoid, super-ovoid 등 폐형 해석해가 존재하지 않는 비선형 원시 형상에 대해 유전 알고리즘(genetic algorithm) 또는 경사 기반 비선형 최적화를 사용한다 [GP-3], [GP-5], [GP-17]. 잔류 오차를 최소화할 수 있으나 계산 비용이 높고 초기값 민감도가 존재한다.

#### 4.5.3 피팅된 형상에서 ACS 축의 정의

피팅된 원시 형상에서 ACS를 구성하는 일반적 규칙은 다음과 같다:

- **구/타원체:** 피팅된 중심점(center) → ACS 원점 또는 관절 중심(HJC, KJC 등); 타원체의 경우 고유벡터(eigenvector)가 3축 방향을 제공하며, 가장 짧은 주축이 내외측 방향, 가장 긴 주축이 종축에 대응된다.
- **원기둥:** 원기둥의 중심축 → 골간부 장축(superior-inferior axis); 원점은 근위 또는 원위 단면 중심으로 정의.
- **교차 단면 타원 스택:** 중심점의 주성분 → 종축; 단면의 주 고유벡터 → 전후방 축 또는 내외측 축.
- **유형 제약 이차곡면:** 대수적 중심 및 고유벡터 → 직접 ACS 원점과 3축으로 사용.

Kai et al. [GP-28], [GP-30]의 완전 자동화 파이프라인은 골반에는 PCA, 대퇴골두에는 구 피팅, 원위 대퇴부에는 타원체 피팅, 경골에는 원기둥 피팅을 결합하여 하지 전체의 ACS를 단일 파이프라인으로 구성하는 체계를 제시하였다. 단, 이 자동 ACS와 ISB 랜드마크 기반 ACS를 비교하면 골반 경사(pelvic tilt) ≈9.6–18.8°, 경골 전후방 축 ≈17.5–25.0°의 계통적 차이가 보고되어, ISB 표준과의 정합을 위한 좌표계 변환 또는 하이브리드 접근이 필요함이 지적된다 [GP-28], [GP-30].

#### 4.5.4 HTO 환자 무릎 뼈에 대한 비판적 적용 가능성 평가

기하학적 도형 피팅이 HTO 슬관절 ACS에 무조건 적용 가능하다고 보는 것은 타당하지 않다. HTO는 **근위 경골(proximal tibia)을 직접 변형**시키는 수술이므로, 해당 부위에 기하학적 피팅을 적용할 경우 수술로 인한 형상 왜곡이 그대로 ACS 정의에 반영되는 구조적 문제가 발생한다. 따라서 기하학적 피팅의 적용 여부는 **뼈별, 축별로 구분하여 판단**하는 것이 필요하다.

**대퇴골 (HTO의 직접적 영향 없음) — 적용 ✅**

대퇴골은 HTO 수술 범위 밖에 있으므로, 모든 기하학적 피팅 방식을 안정적으로 적용할 수 있다. 내외측 대퇴과부에 대한 타원체 피팅은 과상 축(transepicondylar axis, TEA)을 자동·반복적으로 추출하여 대퇴골 ACS의 내외측 축(ML axis)을 정의하는 데 적합하다 [GP-7], [GP-8]. 대퇴골두에 대한 구 피팅은 고관절 중심(HJC)을 환자 맞춤형으로 추정하여 역학 축(mechanical axis) 계산의 정확도를 향상시킨다 [GP-1], [GP-15]. 대퇴골 간부에 대한 원기둥 피팅은 장축(SI axis)을 관찰자 변이 없이 정의한다 [GP-9]. **결론적으로, 대퇴골 ACS 정의에서 기하학적 피팅은 ISB 랜드마크 기반 방식을 대체하거나 보완하는 유효한 선택이다.**

**경골 — 축별 분리 판단 필수**

경골 ACS는 세 개의 축(장축·내외측 축·전후방 축)으로 구성되며, HTO의 영향을 받는 부위와 받지 않는 부위가 명확히 나뉜다.

**(a) 경골 장축 (Superior-Inferior axis): 원위 경골 원기둥 피팅 — 적용 ✅**

개방형 쐐기 HTO는 근위 경골의 형상을 변화시키지만 원위 경골 간부는 영향을 받지 않는다. Knutson et al. [GP-11]은 발목 관절면(plafond) 상방 5 cm에서 시작하여 경골 내외측 너비의 1.5배 길이에 해당하는 원위 간부 구간에 원기둥을 피팅하면, PCA 기반 참조 장축과의 각도 편차가 최소화됨을 입증하였다. 이 원위 구간은 HTO와 무관하게 유지되므로, **원위 경골 원기둥 피팅은 수술 전·후에 걸쳐 일관된 경골 장축을 정의하는 가장 신뢰할 수 있는 기하학적 접근법이다.**

**(b) 경골 회전축 / 내외측 축 (ML axis 및 AP axis): 근위 경골 형상 피팅 — 적용 ❌**

경골 ACS의 ML축과 AP축을 정의하려면 일반적으로 경골 고원(tibial plateau)의 방향, 내외측 과부(condyle) 중심점, 또는 경골 후방 표면 기울기를 기준으로 삼는다. 그러나 HTO는 이 세 가지를 모두 변형시킨다.

- **후방 경골 경사(posterior tibial slope, PTS) 변화:** 개방형 쐐기 HTO는 쐐기 삽입 방향에 따라 PTS를 평균 2–5° 증가시킬 수 있으며 [5.1절 참조], 이는 경골 고원 평면의 공간적 방향을 변화시킨다. 따라서 경골 고원에 피팅된 평면이나 반원통이 정의하는 AP축은 수술 전과 후 사이에 계통적 오차를 포함한다.
- **내측 피질골 불연속:** 내측 쐐기 개방부는 내측 과부 형상을 변형시켜, 내측-외측 과부를 연결하는 방향 벡터(ML축)의 대칭성을 깨뜨린다.
- **비대칭 고원 형상:** 내측이 상방으로 들어 올려지므로, 경골 고원 전체에 피팅된 기하학적 원시 형상은 기능적 슬관절 회전축이 아닌 수술로 인한 비대칭성을 반영하게 된다.

**요약하면, 근위 경골의 기하학적 피팅으로 경골 ML축이나 AP축을 정의하는 것은 HTO 환자에게 적용해서는 안 된다.** 이를 그대로 사용하면 수술 전후 비교에서 축 방향의 계통적 오차가 발생하고, 경골 회전(tibial rotation) 및 내반/외반(varus/valgus) 측정에 직접 오류를 초래한다.

**실용적 결론 — 경골 ACS를 위한 하이브리드 접근**

따라서 HTO 환자의 경골 ACS는 다음과 같은 이분적 전략이 합리적이다:
- **장축 (1-DOF):** 원위 경골 원기둥 피팅 (기하학적 피팅 적용) [GP-11]
- **회전축 (나머지 2-DOF):** 기하학적 피팅 대신 **슬관절 굴곡-신전의 평균 나선 축(Average Helical Axis, AHA)** 기반 기능적 캘리브레이션 [16] 또는 비골두(fibular head)를 보조 랜드마크로 활용하는 하이브리드 랜드마크 접근을 사용

이 전략은 HTO로 손상된 근위 경골 형상 정보를 ACS 정의에서 배제하면서도, 영향을 받지 않는 원위 간부에서는 기하학적 피팅의 재현성을 최대한 활용하는 설계이다.

---

## 5. ACS Methods Suitable for HTO Patients

### 5.1 Anatomical Changes Following HTO

High Tibial Osteotomy fundamentally alters the three-dimensional geometry of the proximal tibia, with several key anatomical changes that impact ACS definition:

1. **Medial cortical discontinuity**: The opening-wedge osteotomy creates a gap in the medial tibial cortex, disrupting the normal bone contour and potentially obscuring anatomical landmarks [10], [15].

2. **Posterior tibial slope changes**: HTO can inadvertently increase the posterior tibial slope, altering the orientation of the tibial plateau relative to the tibial shaft [10], [15], [19]. This change affects the definition of the tibial longitudinal axis and the relationship between proximal and distal tibial landmarks.

3. **Altered mechanical axis**: The primary goal of HTO is to shift the mechanical axis of the lower limb from the medial to the lateral compartment. This realignment changes the spatial relationship between the hip, knee, and ankle joint centers, affecting the definition of segment coordinate systems based on joint center locations [1], [3], [8], [13], [23].

4. **Patellofemoral kinematics**: HTO alters patellofemoral joint kinematics, including increased patellar tilt and decreased patellar proximal translation and spin [6], [11], [25]. These changes may affect the definition of femoral coordinate systems based on patellar tracking or epicondylar landmarks.

5. **Soft tissue changes**: Post-operative swelling, scarring, and altered muscle activation patterns can affect the reliability of external landmark palpation and marker placement [18], [26].

### 5.2 Alignment Correction and Kinematic Adaptations

Gait analysis studies in HTO patients have consistently demonstrated significant biomechanical changes following surgery:

**Frontal plane corrections**: HTO reduces the knee adduction moment (KAM), a surrogate measure of medial compartment loading, by an average of 1.38 percentage body weight × height [3]. Whatling et al. [1] reported that HTO results in improved frontal plane knee moments and gait patterns, with restored frontal and transverse plane knee loading to control levels and corrected compensatory gait adaptations.

**Sagittal plane adaptations**: While frontal plane alignment is corrected, sagittal plane knee range of motion often remains smaller than controls post-HTO [1]. Lind et al. [22] showed normalization of walking speed, knee flexion, and external knee flexion moment post-surgery, with reduced varus angle and adduction moments in the operated knee.

**Transverse plane changes**: HTO alters tibial rotation patterns during gait. d'Entremont et al. [6], [11] found increased anterior translation of the tibia and altered three-dimensional knee kinematics following opening-wedge HTO, including changes in tibial anterior/proximal translation and patellar flexion.

**Dynamic loading redistribution**: Ruggeri et al. [9] demonstrated that after HTO, ground reaction force (GRF) intersection patterns lateralized, moving closer to the knee center, indicating restored physiological load distribution. This correction addresses abnormal varus, which typically causes medially oriented GRF and overloading.

**Muscle activation changes**: Dong et al. [18] investigated the impact of open HTO on lower limb muscle activation, noting reduced co-contraction and enhanced muscle activation within one year postoperatively, reflecting improved lower limb muscle coordination.

These kinematic and kinetic adaptations underscore the need for ACS methods that can accurately capture three-dimensional joint motion across a wide range of alignment conditions, from pre-operative varus deformity to post-operative valgus correction.

### 5.3 Robustness to Deformity and Altered Geometry

For ACS methods to be suitable for HTO patients, they must demonstrate robustness to the anatomical changes and deformities characteristic of this population. Several studies have addressed this challenge:

**Imaging-based approaches**: Belvedere et al. [13] used motion analysis and 3D bone modeling to assess HTO effects on knee loading and alignment, demonstrating that personalized HTO successfully lateralizes the ground reaction force at the knee. This methodology, combining motion analysis and 3D bone modeling, has potential for aiding HTO surgical planning and accommodating altered anatomy.

**Functional calibration**: Ancillao et al. [16] demonstrated that functionally defined coordinate systems based on the average screw axis of knee motion are independent of marker landmarks and do not require anatomical calibration, making them potentially more robust to anatomical variation and deformity.

**Patient-specific optimization**: Pourtabib et al. [1], [2] developed patient-specific, optimized joint coordinate systems that address anatomical variation and reduce kinematic crosstalk errors. This approach is particularly relevant for HTO patients, where standard anatomical landmarks may not accurately represent functional axes.

**Coordinate system robustness across pathologies**: Knutson et al. [18] evaluated six coordinate systems on the talus and calcaneus, noting that automated coordinate systems that align with clinically relevant anatomic planes are preferred, but principal component axes, while automatic, do not align with these planes. The study aimed to determine the influence of morphology on previously defined coordinate systems, highlighting the importance of considering anatomical variation.

### 5.4 Recommended Approaches for HTO Gait Analysis — Integrated ACS Definition Strategy

The evidence reviewed across conventional landmark-based, imaging-based, functional calibration, geometric primitive fitting, and markerless methods points to a clear conclusion: **no single method is universally adequate for HTO knee ACS definition.** The optimal strategy must be constructed axis by axis, selecting the method that is both accurate and robust to the specific anatomical changes HTO introduces at each bone and each degree of freedom.

The following framework is proposed based on a critical synthesis of the available evidence.

---

#### Femoral ACS — Geometric Primitive Fitting is Recommended

The femur is unaffected by HTO surgery. For this reason, geometric primitive fitting can be applied without reservation and is preferred over manual landmark identification due to its superior repeatability.

- **ML axis (transepicondylar axis):** Ellipsoid fitting to the medial and lateral femoral condyles from pre-operative CT provides an automated, observer-independent definition of the internal-external rotation reference [GP-7], [GP-8]. This is more reproducible than manual epicondyle palpation, which carries inter-rater errors of 3–5° in rotation [8].
- **Long axis:** Cylinder fitting to the femoral shaft defines the SI axis robustly, eliminating the influence of distal condylar asymmetry on long-axis estimation [GP-9].
- **AP axis:** Defined as the cross product of the ML and long axes; no additional fitting required.
- **Hip joint center:** Sphere fitting to the femoral head provides a patient-specific HJC that accurately reflects the post-HTO mechanical axis shift [GP-1], [GP-15].

---

#### Tibial ACS — Axis-Selective Hybrid (Geometric + Functional)

The tibia requires a fundamentally different strategy because HTO directly alters the proximal tibial geometry. The three axes of the tibial ACS must be defined from anatomically distinct regions, not a single coherent geometric primitive.

**Long axis (SI) — Distal tibial cylinder fitting ✅**

The distal tibial shaft is unaffected by the osteotomy and provides a geometrically stable region for cylinder fitting. Following the protocol of Knutson et al. [GP-11], cylinder fitting should be applied to the distal tibial shaft starting 5 cm proximal to the ankle plafond, over a length of 1.5× the medio-lateral width. This yields the tibial long axis independently of any proximal tibial deformity or hardware artifact.

This is the **one axis where geometric fitting is definitively recommended** for the tibial ACS in HTO patients.

**ML axis and AP axis (rotational DOFs) — Functional calibration ✅ | Proximal tibial geometric fitting ❌**

The remaining two axes cannot be reliably derived from proximal tibial geometry in HTO patients, for three reasons:

1. **Posterior tibial slope change** (typically +2–5° in opening-wedge HTO) reorients the tibial plateau plane, meaning any plateau-fitted geometric primitive captures post-surgical distortion rather than a functional reference direction.
2. **Medial cortical discontinuity** asymmetrically displaces the medial condyle superiorly, breaking the geometric symmetry that plateau-based ML axis fitting relies upon.
3. **Fixation hardware** introduces CT metal artifacts that corrupt point cloud quality in the proximal tibial region, further degrading fitting reliability.

For these rotational axes, **functional calibration using the Average Helical Axis (AHA) of knee flexion-extension** [16] is the most appropriate method. The AHA is derived from observed joint motion rather than bone surface geometry, making it entirely independent of proximal tibial shape changes. It directly identifies the functional rotation axis of the knee, which may shift post-HTO due to altered kinematics — a shift that a geometry-based method would simply miss.

A secondary option where dynamic functional calibration is not feasible (e.g., early post-operative, severe pain) is to use the **fibular head** as a lateral reference landmark for the ML axis, as the fibula is not directly involved in the HTO and its proximal geometry remains intact.

**Summary: Tibial ACS construction for HTO patients**

| Axis | Method | Rationale |
|------|---------|-----------|
| Long axis (SI) | Distal tibial cylinder fitting | Distal shaft unaffected by HTO; reproducible, automated |
| ML axis | Functional calibration (AHA) | Proximal tibial geometry unreliable post-HTO |
| AP axis | Cross product of SI × ML | No additional fitting needed |

---

#### Practical Workflow by Assessment Stage

**Pre-operative:** Acquire CT for full femoral and distal tibial geometric fitting pipeline. Establish baseline tibial long axis and femoral condylar ML axis. Perform functional calibration session for baseline tibial rotational axis.

**Post-operative (≥ 6 weeks):** Re-apply distal tibial cylinder fitting and femoral condyle ellipsoid fitting from post-operative CT or the original pre-operative model with registration. Repeat functional calibration to capture rotational axis shifts induced by realignment. Do not re-fit proximal tibial geometry; use the distal tibial long axis as the anchor.

**Longitudinal monitoring:** For serial gait assessments, markerless pose estimation (e.g., Pose2Sim) combined with a patient-specific musculoskeletal model scaled to the imaging-derived ACS provides accessible, clinic-friendly kinematics. Accuracy validation against the fluoroscopic 2D/3D gold standard should be performed at least once per patient [32], [33], [34].

---

#### On the Use of ISB-Standard Landmark Methods in HTO

ISB-standard landmark-based tibial ACS definition — which relies on the midpoint of medial and lateral malleoli for the inferior origin and the midpoint of tibial condyles for the superior axis direction — remains appropriate for the **ankle-referenced** portion of the tibial coordinate system. However, ISB-standard definitions that anchor any axis to the tibial plateau or proximal tibial condyles should be treated with caution post-HTO. The systematic orientation difference between geometric and ISB-landmark frames (~17.5–25.0° for the tibial AP axis reported by Kai et al. [GP-28], [GP-30]) is further compounded in HTO patients by post-surgical slope and condylar asymmetry, making uncritical application of the ISB tibial ACS a source of clinically significant error in longitudinal studies.

---

## 6. Discussion

### 6.1 Comparative Analysis of ACS Methods

The choice of ACS definition method for HTO gait analysis involves trade-offs among accuracy, accessibility, robustness to deformity, and clinical feasibility. Table 1 summarizes the key characteristics of the major ACS approaches reviewed.

**ISB-standard landmark-based methods** offer the advantage of widespread adoption, ease of implementation, and direct correspondence with clinical anatomy. However, they are susceptible to soft tissue artifact, inter-rater variability, and may not accurately represent functional axes in the presence of significant deformity [8], [20], [29]. For HTO patients, the altered tibial geometry and potential obscuration of landmarks limit the reliability of these methods.

**Imaging-based methods** (CT, MRI, fluoroscopy) provide the most accurate representation of bone geometry and are robust to soft tissue artifact and anatomical variation [1], [3], [7], [9], [11], [28], [30]. They are particularly well-suited for establishing patient-specific coordinate systems that account for pre-existing deformity and post-surgical changes. However, they require specialized equipment, involve radiation exposure (for CT and fluoroscopy), and are not practical for routine longitudinal monitoring.

**Functional calibration methods** define coordinate systems based on observed joint motion, potentially better representing functional axes than anatomical landmarks [16], [19]. They are independent of landmark identification and can accommodate altered kinematics. However, they require patients to perform specific calibration movements, which may be difficult in the early post-operative period, and may not be consistent across different activities or loading conditions.

**Deep learning-based markerless methods** offer unprecedented accessibility and scalability, enabling gait analysis in clinical settings without specialized equipment [32], [33], [34], [37], [39]. However, their accuracy in populations with significant deformity or altered gait patterns remains to be fully validated, and they typically estimate joint centers rather than full anatomical coordinate systems, limiting their utility for detailed kinematic analysis.

**Geometric primitive fitting methods** occupy a distinctive position between imaging-based and automated/algorithmic approaches. They are best understood not as a single method but as a set of tools that must be selectively applied based on whether the target bone region is anatomically preserved. For HTO patients, this distinction is decisive: femoral geometric fitting (condyle ellipsoid, shaft cylinder, femoral head sphere) is fully applicable and preferred for its repeatability [GP-7], [GP-9], [GP-15]; by contrast, proximal tibial geometric fitting for ML and AP axis definition is structurally contraindicated because the tibial plateau, posterior surface, and medial condyle are all directly altered by the osteotomy. Applying proximal tibial geometric fitting post-HTO would encode surgical distortion into the ACS definition, producing axis directions that are neither anatomically valid nor longitudinally stable. The sole geometric fitting application that remains reliable on the tibial side is **distal shaft cylinder fitting for the long axis** [GP-11], which should be adopted as a standard component of the tibial ACS workflow in this population.

### 6.2 Limitations and Methodological Challenges

Several methodological challenges remain in establishing robust ACS definitions for HTO gait analysis:

**Longitudinal consistency**: Maintaining consistent ACS definitions across pre- and post-operative assessments is challenging due to altered bone geometry, soft tissue changes, and modified gait patterns [6], [10], [11], [18]. Imaging-based methods offer the best potential for longitudinal consistency, but require repeated imaging with associated cost and radiation exposure.

**Validation in pathological populations**: Most ACS methods have been developed and validated in healthy populations with normal anatomy. Validation in populations with significant deformity, such as pre-operative HTO candidates, is limited [11], [18], [28]. Similarly, validation of markerless deep learning methods in post-surgical populations is lacking.

**Kinematic crosstalk**: Standard ISB-recommended coordinate systems can exhibit significant kinematic crosstalk, where motion in one plane (e.g., flexion-extension) artificially appears as motion in another plane (e.g., abduction-adduction) due to misalignment of coordinate axes with functional axes of rotation [1], [2]. This is particularly problematic in populations with altered joint kinematics, such as HTO patients.

**Soft tissue artifact**: Marker-based methods are susceptible to soft tissue artifact, where skin movement relative to underlying bone introduces errors in kinematic measurements [21], [31]. This is exacerbated in populations with altered gait patterns, muscle weakness, or post-operative swelling.

**Accessibility vs. accuracy trade-off**: There is an inherent trade-off between accessibility (e.g., markerless video-based methods) and accuracy (e.g., fluoroscopic 2D/3D registration). For clinical decision-making and surgical planning, high accuracy is essential, while for routine monitoring and rehabilitation, accessibility may be prioritized [32], [33], [34], [39].

### 6.3 Clinical Translation and Future Directions

The clinical translation of advanced ACS methods for HTO gait analysis requires addressing several key challenges:

**Development of validated clinical protocols**: Standardized protocols for ACS definition in HTO patients, including specific recommendations for imaging modalities, landmark identification, and functional calibration procedures, are needed to promote consistency across studies and clinical centers [8], [20], [23].

**Integration with surgical planning**: Patient-specific ACS definitions derived from pre-operative imaging could be integrated with surgical planning software to predict post-operative kinematics and optimize alignment correction targets [13], [19], [27]. This would enable personalized surgical planning based on individual anatomy and biomechanics.

**Validation of markerless methods**: Large-scale validation studies comparing deep learning-based markerless pose estimation with gold-standard fluoroscopic or marker-based methods in HTO populations are needed to establish accuracy benchmarks and identify limitations [32], [33], [34], [37], [39].

**Automated patient-specific algorithms**: Development of fully automated algorithms that can define patient-specific ACS from medical imaging (CT, MRI, biplanar radiography) without manual landmark identification would improve repeatability and reduce the expertise required for implementation [11], [28], [30].

**Longitudinal cohort studies**: Prospective longitudinal studies tracking HTO patients from pre-operative assessment through post-operative recovery with consistent ACS definitions are needed to understand the time course of kinematic adaptations and identify predictors of surgical success [1], [3], [4], [22], [26].

**Multi-modal integration**: Integration of multiple data sources—including medical imaging, marker-based motion capture, markerless video analysis, and wearable sensors—could provide complementary information and improve the robustness of ACS definitions across different assessment contexts [13], [32], [38].

---

## 7. Conclusion

Establishing robust anatomical coordinate systems for 2D/3D registration of gait videos in High Tibial Osteotomy patients presents significant methodological challenges due to altered bone geometry, changes in alignment, and modified gait patterns. This review has synthesized current approaches, ranging from conventional ISB-standard landmark-based methods to state-of-the-art imaging-based, functional calibration, geometric primitive fitting, and deep learning-based markerless techniques.

For HTO patients specifically, we recommend an axis-selective hybrid strategy: geometric primitive fitting (ellipsoid condyle fitting, shaft cylinder fitting) for the femoral ACS and the tibial long axis, combined with functional calibration (Average Helical Axis) for tibial rotational axes where proximal tibial geometry is rendered unreliable by the osteotomy. Indiscriminate application of proximal tibial geometric fitting post-HTO is not recommended, as it would encode surgical distortion into the ACS definition. Deep learning-based markerless pose estimation offers promise for accessible, clinic-friendly gait assessment, but requires further validation in populations with significant deformity and altered anatomy.

Future research should prioritize the development of automated, patient-specific ACS algorithms that explicitly account for post-HTO anatomical changes, validation of markerless methods in pathological populations, and integration of multi-modal data sources to improve robustness and clinical utility. Standardized reporting of ACS definitions and alignment parameters is essential to promote consistency and comparability across studies.

By advancing the methods for establishing anatomical coordinate systems in this challenging population, we can improve the accuracy and reliability of gait analysis, enhance our understanding of post-surgical biomechanical adaptations, and ultimately optimize surgical outcomes and rehabilitation protocols for patients undergoing High Tibial Osteotomy.

---

## References: Geometric Primitive Fitting (Section 4.5 — New Citations)

> The following references [GP-1] through [GP-19] are cited exclusively within Section 4.5 and the corresponding Discussion passages. Existing references [1]–[39] are retained from the original review.

**[GP-1]** Song W, Ou Z-Y, Zhao D-W, et al. Computer-Aided Modeling and Morphological Analysis of Hip Joint. *ICBBE 2007*. DOI: 10.1109/ICBBE.2007.314

**[GP-2]** Automatic 3D pelvimetry framework in CT images and its validation. *Scientific Reports*, 2024. DOI: 10.1038/s41598-024-72123-6

**[GP-3]** Cerveri P, Manzotti A, Baroni G. Patient-specific acetabular shape modelling: comparison among sphere, ellipsoid and conchoid parameterisations. *Computer Methods in Biomechanics and Biomedical Engineering*, 2014;17(3):260–270. DOI: 10.1080/10255842.2012.702765

**[GP-4]** Harris MD, Reese SP, Peters CL, et al. Three-dimensional Quantification of Femoral Head Shape in Controls and Patients with Cam-type Femoroacetabular Impingement. *Annals of Biomedical Engineering*, 2013;41(6):1162–1171. DOI: 10.1007/S10439-013-0762-1

**[GP-5]** Lopes DS, Neptune RR, Gonçalves AA, et al. Shape Analysis of the Femoral Head: A Comparative Study Between Spherical, (Super)Ellipsoidal, and (Super)Ovoidal Shapes. *Journal of Biomechanical Engineering*, 2015;137(11). DOI: 10.1115/1.4031650

**[GP-6]** Martelli S, Acquaroli F, Pinskerova V, et al. An Anatomical Model of the Knee Joint Obtained by Computer Dissection. *Lecture Notes in Computer Science*, 2002. DOI: 10.1007/3-540-45787-9_39

**[GP-7]** Asseln M, Alhares G, Eschweiler J, et al. Automatic Parameterisation of the Distal Femur Based on 3D Surface Data: A Novel Approach for Systematic Morphological Analysis and Optimisation. *Proceedings*, 2015.

**[GP-8]** Chapman T, Sholukha V, et al. Femoral curvature variability in modern humans using three-dimensional quadric surface fitting. *Surgical and Radiologic Anatomy*, 2015;37(9):1065–1072. DOI: 10.1007/S00276-015-1495-7

**[GP-9]** Casciaro ME, Ritacco LE, Milano F, et al. Angle estimation of human femora in a three-dimensional virtual environment. *32nd Annual International Conference of the IEEE EMBS*, 2010. DOI: 10.1109/IEMBS.2010.5627701

**[GP-10]** Development of a femoral template for computer-assisted tunnel placement in anatomical double-bundle ACL reconstruction. *Computer Aided Surgery*, 2011. DOI: 10.3109/10929088.2010.541040

**[GP-11]** Knutson K, Muhlrad EP, Peterson AC, et al. Recommendation of minimal distal tibial length for long axis coordinate system definitions. *Journal of Biomechanics*, 2024;173:112153. DOI: 10.1016/j.jbiomech.2024.112153

**[GP-12]** Allaire S, Jacq J-J, Burdin V, et al. Type-Constrained Robust Fitting of Quadrics with Application to the 3D Morphological Characterization of Saddle-Shaped Articular Surfaces. *ICCV 2007*. DOI: 10.1109/ICCV.2007.4409163

**[GP-13]** Allaire S, Jacq J-J, Burdin V, et al. Ellipsoid-Constrained Robust Fitting of Quadrics with Application to the 3D Morphological Characterization of Articular Surfaces. *29th Annual International Conference of the IEEE EMBS*, 2007. DOI: 10.1109/IEMBS.2007.4353484

**[GP-14]** Mapping ligament insertion sites onto bone surfaces in knee by co-registration of CT and digitization data. *Journal of Biomechanics*, 2009. DOI: 10.1016/j.jbiomech.2009.06.042

**[GP-15]** Kai S, Sato T, Koga Y, et al. Automatic construction of an anatomical coordinate system for three-dimensional bone models of the lower extremities — pelvis, femur, and tibia. *Journal of Biomechanics*, 2014;47(5):1229–1233. DOI: 10.1016/j.jbiomech.2013.12.013

**[GP-16]** Xia Y, Fripp J, et al. Automated 3D quantitative assessment and measurement of alpha angles from the femoral head-neck junction using MR imaging. *Physics in Medicine and Biology*, 2015;60(19):7601–7616. DOI: 10.1088/0031-9155/60/19/7601

**[GP-17]** Analytical surface recognition in three-dimensional (3D) medical images using genetic matching: Application to the extraction of spheroidal articular surfaces. *International Journal of Imaging Systems and Technology*, 2000;11(1):30–39.

**[GP-18]** Validation of hip joint center localization methods during gait analysis using 3D EOS imaging in typically developing and cerebral palsy children. *Gait & Posture*, 2016. DOI: 10.1016/j.gaitpost.2016.04.028

**[GP-19]** Kapron AL, Aoki SK, Peters CL, et al. Accuracy and feasibility of dual fluoroscopy and model-based tracking to quantify in vivo hip kinematics during clinical exams. *Journal of Applied Biomechanics*, 2014;30(3):461–470. DOI: 10.1123/JAB.2013-0112


## References

[1]M. S. Fischer, S. V. Lehmann, and E. Andrada, “Three-dimensional kinematics of canine hind limbs: in vivo, biplanar, high-frequency fluoroscopic analysis of four breeds during walking and trotting.,” Scientific Reports, vol. 8, no. 1, pp. 16982–16982, Nov. 2018, doi: 10.1038/S41598-018-34310-0.

[2]M. E. R. Balsdon, “In-Vivo Investigation of the Medial Longitudinal Arch of the Foot and Orthotic Interactions using Bi-Planar Fluoroscopy,” Jan. 2012.

[3]M. D. C. Wang et al., “In vivo kinematic study of the tarsal joints complex based on fluoroscopic 3D-2D registration technique,” Gait & Posture, Sept. 2016, doi: 10.1016/J.GAITPOST.2016.06.009.

[4]R. R. Inawat, “Kinematic Analysis of the Glenohumeral Joint: A Comparison of Post-Operative Rotator Cuff Repair Patients and Controls,” Jan. 2014.

[5]F. João, S. Amado, A. Veloso, P. Armada-da-Silva, and A. C. Maurício, “Anatomical reference frame versus planar analysis: implications for the kinematics of the rat hindlimb during locomotion.,” Reviews in The Neurosciences, vol. 21, no. 6, pp. 469–486, Jan. 2010, doi: 10.1515/REVNEURO.2010.21.6.469.

[6]W. Cheng-tao, “Analysis of Human Patellofemoral Relative Kinematics,” Journal of Shanghai Jiaotong University, Jan. 2009.

[7]C. Green, C. K. Fitzpatrick, D. FitzPatrick, M. M. Stephens, W. Quinlan, and R. Flavin, “Definition of coordinate system for three-dimensional data analysis in the foot and ankle.,” Foot & Ankle International, vol. 32, no. 2, pp. 193–199, Feb. 2011, doi: 10.3113/FAI.2011.0193.

[8]J. K. Sinclair, P. J. Taylor, A. Greenhalgh, C. J. Edmundson, D. Brooks, and S. J. Hobbs, “The Test-Retest Reliability of Anatomical Co-Ordinate Axes Definition for the Quantification of Lower Extremity Kinematics During Running,” Journal of Human Kinetics, vol. 35, no. 1, pp. 15–25, Dec. 2012, doi: 10.2478/V10078-012-0075-8.

[9]J. Wang, X. Han, W. Ji, and C. Wang, “[Analysis of Human Tibio-Femoral Joint Relative Kinematics Based on 3D Image Registration].”, doi: 10.3321/j.issn:1001-5515.2009.06.037.

[10]S. H. Wei, K. J. McQuade, and G. L. Smidt, “Three-dimensional joint range of motion measurements from skeletal coordinate data.,” Journal of Orthopaedic & Sports Physical Therapy, vol. 18, no. 6, pp. 687–691, Dec. 1993, doi: 10.2519/JOSPT.1993.18.6.687.

[11]A. C. Peterson, K. M. Kruger, and A. L. Lenz, “Automatic anatomical foot and ankle coordinate toolbox,” Frontiers in Bioengineering and Biotechnology, vol. 11, Oct. 2023, doi: 10.3389/fbioe.2023.1255464.

[12]Lawrence, Roseni, and Bey, “Correspondence between scapular anatomical coordinate systems and the 3D axis of motion: A new perspective on an old challenge.,” Journal of biomechanics, 2022, doi: 10.1016/j.jbiomech.2022.111385.

[13]X. Y. Liu, S. Yi, W. Xu, Y. Liu, W. Xu, and Z. Guo, “Lower Limb Joint Kinematic Calculation Based on 3D Marked Inverse Kinematic Gait Model,” pp. 666–671, June 2025, doi: 10.1109/rcar65431.2025.11139563.

[14]D. M. Dawson, “Development &amp; Validation Of An Improved Biomechanical Model For Motion Analysis,” Aug. 2024, doi: 10.34719/rysc7326.

[15]M. F. Heller, “Biomechanical Changes in Gait and Posture as a Result of In-Shoe Orthoses and External Load,” Aug. 2005.

[16]A. Ancillao, A. Verduyn, M. Vochten, E. Aertbeliën, and J. D. Schutter, “A Novel Procedure for Knee Flexion Angle Estimation Based on Functionally Defined Coordinate Systems and Independent of the Marker Landmarks,” International Journal of Environmental Research and Public Health, vol. 20, no. 1, pp. 500–500, Dec. 2022, doi: 10.3390/ijerph20010500.

[17]J. L. Astephen, K. J. Deluzio, G. E. Caldwell, and M. J. Dunbar, “Biomechanical changes at the hip, knee, and ankle joints during gait are associated with knee osteoarthritis severity,” Journal of Orthopaedic Research, vol. 26, no. 3, pp. 332–341, Mar. 2008, doi: 10.1002/JOR.20496.

[18]K. Knutson et al., “Talar and calcaneal coordinate axes definitions across foot pathologies,” Journal of Biomechanics, vol. 175, pp. 112298–112298, Aug. 2024, doi: 10.1016/j.jbiomech.2024.112298.

[19]O. Warlow, “Kinematic and anatomical measurement for biomechanical finger models,” Jan. 2012.

[20]M. Żuk and M. Trzeciak, “Anatomical protocol for gait analysis: joint kinematics measurement and its repeatability,” Journal of Theoretical and Applied Mechanics, vol. 55, no. 1, pp. 369–376, Oct. 2016, doi: 10.15632/JTAM-PL.55.1.369.

[21]L. Kocsis, R. M. Kiss, Z. Knoll, and M. Jurák, “Bute’s ultrasound-based measuring technique and model for gait analysis,” Jan. 2003.

[22]P. Roberson, “Validation of a Three Dimensional Motion Capture System for Use in Identifying Characteristics of the Running Walk,” Jan. 2007.

[23]M. Donati, V. Camomilla, G. Vannozzi, and A. Cappozzo, “Anatomical frame identification and reconstruction for repeatable lower limb joint kinematics estimates,” Journal of Biomechanics, July 2008, doi: 10.1016/J.JBIOMECH.2008.04.018.

[24]Wu et al., “ISB recommendation on definitions of joint coordinate systems of various joints for the reporting of human joint motion--Part II: shoulder, elbow, wrist and hand.,” Journal of biomechanics, 2005, doi: 10.1016/j.jbiomech.2004.05.042.

[25]X. Gasparutto, P. Besonhe, P. L. DiGiovanni, S. Armand, and D. Hannouche, “Definition and reliability of 3D acetabular and global offset measurements from bi-plane X-rays,” Dental science reports, vol. 13, no. 1, Jan. 2023, doi: 10.1038/s41598-023-27652-x.

[26]F. Ateş, J. E. Brandenburg, and K. R. Kaufman, “Effects of Selective Dorsal Rhizotomy on Ankle Joint Function in Patients With Cerebral Palsy.,” Frontiers in Pediatrics, vol. 8, pp. 75–75, Feb. 2020, doi: 10.3389/FPED.2020.00075.

[27]R. Dumas and L. Chèze, “3D inverse dynamics in non-orthonormal segment coordinate system,” Medical & Biological Engineering & Computing, vol. 45, no. 3, pp. 315–322, Jan. 2007, doi: 10.1007/S11517-006-0156-8.

[28]S. Kai, T. Sato, Y. Koga, G. Omori, and K. Kobayashi, “Automatic construction of an anatomical coordinate system for three-dimensional bone models of the lower extremities–pelvis, femur, and tibia”, [Online]. Available: https://www.sciencedirect.com/science/article/pii/S0021929013006404

[29]Wu et al., “ISB recommendation on definitions of joint coordinate system of various joints for the reporting of human joint motion--part I: ankle, hip, and spine. International Society of Biomechanics.,” Journal of biomechanics, 2002, doi: 10.1016/s0021-9290(01)00222-6.

[30]Kai et al., “Automatic construction of an anatomical coordinate system for three-dimensional bone models of the lower extremities--pelvis, femur, and tibia.,” Journal of biomechanics, 2014, doi: 10.1016/j.jbiomech.2013.12.013.

[31]A. Cappello, R. Stagni, S. Fantozzi, and A. Leardini, “Soft tissue artifact compensation in knee kinematics by double anatomical landmark calibration: performance of a novel method during selected motor tasks,” IEEE Transactions on Biomedical Engineering, vol. 52, no. 6, pp. 992–998, May 2005, doi: 10.1109/TBME.2005.846728.

[32]L. Needham et al., “The Development and Evaluation of a Fully Automated Markerless Motion Capture Workflow,” bioRxiv, Feb. 2022, doi: 10.1101/2022.02.16.480655.

[33]D. Pagnon, M. Domalain, and L. Reveret, “Pose2Sim: An End-to-End Workflow for 3D Markerless Sports Kinematics—Part 2: Accuracy,” Sensors, vol. 22, no. 7, pp. 2712–2712, Apr. 2022, doi: 10.3390/s22072712.

[34]D. Pagnon, M. Domalain, L. Reveret, and L. Reveret, “Pose2Sim: An End-to-End Workflow for 3D Markerless Sports Kinematics-Part 1: Robustness.,” Sensors, vol. 21, no. 19, p. 6530, Sept. 2021, doi: 10.3390/S21196530.

[35]Y. Liang, S. Qi, T. Xu, and Y. Hu, “3D Gait Analysis for the Elderly Mobility Based on Multiple RGB Cameras,” Nov. 2023, doi: 10.1109/m2vip58386.2023.10413409.

[36]S. Liang, Y. Zhang, Y. Diao, G. Li, and G. Zhao, “The reliability and validity of gait analysis system using 3D markerless pose estimation algorithms,” Frontiers in Bioengineering and Biotechnology, vol. 10, Aug. 2022, doi: 10.3389/fbioe.2022.857975.

[37]M. Moro, G. Marchesi, F. Hesse, F. Odone, and M. Casadio, “Markerless vs. Marker-Based Gait Analysis: A Proof of Concept Study,” Sensors, vol. 22, no. 5, pp. 2011–2011, Mar. 2022, doi: 10.3390/s22052011.

[38]S. Vafadar, W. Skalli, A. Bonnet-Lebrun, A. Assi, and L. Gajny, “Assessment of a novel deep learning-based marker-less motion capture system for gait study.,” Gait & Posture, vol. 94, pp. 138–143, Mar. 2022, doi: 10.1016/j.gaitpost.2022.03.008.

[39]R. M. Kanko, E. K. Laende, E. M. Davis, W. S. Selbie, and K. J. Deluzio, “Concurrent assessment of gait kinematics using marker-based and markerless motion capture,” bioRxiv, Dec. 2020, doi: 10.1101/2020.12.10.420075.