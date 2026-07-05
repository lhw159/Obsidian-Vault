
# Method
## Personalised model parameters from radiographic and gait analysis data
contatc point 분석을 위해, 서있는 자세 (full-weight bearing) 45도 flexion에 대한 radiograph 습득
TFA was deﬁned as the angle between the mechanical axes of the femur and tibia, while medial and lateral CP locations were identiﬁed as the points with the minimum joint space width in the corresponding knee compartments [30, 32]
![[Pasted image 20260705234639.png|400]]

## Data processing and statistical analysis

percentage of the stance phase of the motor activity cycle (보행 주기 사이클) 으로 정규화
subject body weight (BW) 사용

**wilcoxon sigend-rank test** 
각 그룹 내(HTO(surgical) vs Conservative)에서 baseline(수술 전)과 follow up(수술 후)에서 patient characertistics와 PROM(Patient‐reported outcome measures)차이 확인을 위해 사용
- **보행 주기의 피크치(Peak Values) 분석:** 보행 및 계단 활동 주기 중 발견되는 전형적인 이중 구조(Double-bump pattern)의 두 가지 최고점 시점을 식별한 뒤, 해당 피크 시점에서의 안쪽 압력(**MCF**), 바깥쪽 압력(**LCF**), 총 압력(**TCF**) 수치 자체의 전후 차이를 검증하는 기준으로 활용
  
- **압력 분산 비율(MFRatio) 분석:** 무릎 관절 전체에 가해지는 압력(**TCF**) 중 안쪽 칸이 부담하는 압력(**MCF**)의 상대적 비율($\%$)을 두 개의 피크 시점에서 각각 계산하여, 하중 재분배의 변화를 검증하는 통계적 기준으로 사용


**Mann–Whitney U‐tests**
surgical과 conservative 방식에서의 차이검증을 위해 사용
- 두 그룹이 치료를 시작하기 전(**Baseline**) 단계에서 서로 동질한 조건(나이, BMI, TFA 정렬 상태 및 초기 임상 점수 등)을 가지고 있었는지 기선 제압용 비교 기준으로 사용

- 치료가 끝난 후(**Follow-up**) 시점에도 두 그룹 간에 주관적 임상 점수(PROMs)의 수준 차이가 존재하는지 상호 비교하는 기준으로 적용


**statistical parametric mapping (SPM)**
각 motor activity (평지, 경사 걷기) 수술 전 후  MCF(medial contact force)와 LCF (lateral contac force), KCF(Knee contact force) 차이 검증을 위해 사용. HTO, conservatice 각각 그룹에 non-parametric two-tailed paired t-test (using SPM1D)
- **연속적 역학 흐름 분석:** 단일한 피크 점수가 아니라 보행 주기에 따라 실시간으로 변하는 안쪽 압력(**MCF**), 바깥쪽 압력(**LCF**), 총 압력(**TCF**) 곡선 전체를 비교하기 위해 적용

- **SPM1D 기반 분석:** MATLAB 환경에서 `SPM1D` 패키지를 이용하여 각 그룹별 전후 차이를 분석하기 위한 비모수적 양측 대응표본 t-검정 (Non-parametric two-tailed paired t-test, $\alpha=0.05$)으로 구현

- **임상적 유의성(Clinically relevant) 판정 기준:** t-test를 통해 통계적으로 유의미한 차이($p<0.05$)가 나타난 구간이 **전체 보행 주기의 최소 4% 이상 연속으로 유지되는 경우에만** 노이즈가 아닌 실제 역학적 의미가 있는 변화로 인정하겠다는 타임라인 컷오프 기준


# Result
## Patients' demographics and clinical measures
Tibiofemoral alignment (TFA) (어떻게 정의했는지 다시 보기) : Surgical에서는 차이가 두드러지나, conservative에선 차이가 없음 (p=0.973)
contact point의 경우 medial은 양쪽 다 차이 없고, lateral은 양쪽다 lateral 방향으로 편향 (contact point변화는 없다는 것?)
![[Pasted image 20260705234008.png|600]]


![[Pasted image 20260705233934.png|600]]


**Knee contact forces**





Previous modelling studies have reported 
lateralisation of KCFs after HTO during walking [5, 9], 
but no study has comprehensively analysed this 
redistribution across multiple daily activities