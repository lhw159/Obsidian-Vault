**Muscle-tendon forces** depend on the **muscle-tendon length**, and
**joint moments** depend on both **muscle-tendon forces and moment arms.**


**Moment arm**은 “관절각에 대한 근육 길이의 미분값”이다.
$$
Moment\: arm\:=\frac{\partial L}{\partial \theta}
$$
$$
Moment = Moment\:arm \times tendon\:force
$$
force는 항상 positive이기에 방향에 따라 moment arm이 음수일 수도(flexion이 기준일 경우 extensor muscle의 moment arm은 음수)


**tendon slack length** : 근육이 힘을 주기 시작하는 tendon의 길이 (the critical, non-stretched resting length of a tendon)
slack length 감소
=> muslce-tendon length 변경 x / fiber length 증가 / angle of peak force 감소


**Inverse Kinematics** : Joint angle 를 찾는 과정
**Inverse Dynamics** : 앞서 찾은 Joint angle에서 angle velocities, accelarations 와 GRF를 통해 joint에 걸리는 net moment, force 계산하는 과정.
![[Pasted image 20260224142243.png]]
# Scaling
generic 모델에 experimental marker set을 맞추는 작업
![[Pasted image 20260225161917.png|480x200]]
## **scale set up file**
**5 major sections to a scale setup file:**
**1. 실행 파라미터 (Execution Parameters)**
- **기본 구조**: 전체 스케일링 작업의 속성들은 `<ScaleTool>` 시작 태그와 종료 태그 내에 포함

**2. 피험자 특정 파라미터 (Subject-specific Parameters)**
- **입력 항목**: 피험자의 질량(`<mass>`), 키(`<height>`), 나이(`<age>`)를 각각 kg, mm, 세 단위로 지정

**3. 제네릭 모델 관련 파라미터 (Generic Model Parameters)**
- **모델 파일**: `<GenericModelMaker>` 태그 내의 `<model_file>`을 통해 스케일링의 대상이 되는 원본 OpenSim 모델(`.osim`)을 지정
- **마커 세트 추가**: 제네릭 모델에 마커가 포함되어 있지 않은 경우, 외부 XML 파일(`<MarkerSet>`)을 로드하여 스케일링에 필요한 마커들을 모델에 추가하거나 업데이트

**4. 스케일링 속성 (Scaling Properties)**
- **복합 방식**: 측정 기반(`measurements`)과 수동(`manualScale`) 방식을 혼합하여 사용할 수 있으며, `<scaling_order>`를 통해 적용 순서를 결정
- **측정 기반 설정**:
    - `<MeasurementSet>`으로 마커 쌍과 적용될 뼈(Body)를 지정
    - `<marker_file>`(.trc)과 `<time_range>`를 통해 거리를 계산할 실험 데이터와 평균 시간 범위를 설정
- **질량 분포 유지**: `<preserve_mass_distribution>` 설정을 통해 scaling 후에 각 부위별 질량 비율을 generic model과 동일하게 유지할지 결정
- **중간 결과물**: `<output_model_file>`이나 `<output_scale_file>`을 설정하여 마커 배치 전 단계의 모델이나 적용된 배율 값을 별도로 저장

**5. 마커 배치 속성 (Marker Placement Properties)**
- **정적 포즈 최적화**: `<MarkerPlacer>` 태그 내에서 수행되며, 모델을 피험자의 실험적 정지 자세와 최대한 일치하도록 관절 각도를 조정
- **가중치 설정**: `<IKTaskSet>`을 통해 자세 계산 시 각 마커와 좌표에 부여할 Weight을 지정, 이는 역동역학(IK) 방식과 유사하게 작동
- **마커 이동**:
    - 정적 자세가 결정되면, `<fixed>` 속성이 `false`로 설정된 마커들만 실험 데이터의 평균 위치로 모델 상에서 이동
    - `<time_range>` 내의 데이터를 평균 내어 실험적 마커 위치를 확정하며, 움직임이 있는 데이터의 경우 시간 범위를 좁게 설정
- **최종 출력**: 모든 과정이 완료된 최종 모델 파일(`.osim`)과 정적 자세 정보를 담은 모션 파일(`.mot` 등)을 생성

**Scaling result 확인**
1. In general, maximum marker errors for bony landmarks should be less than 2 cm.
2. RMS error should typically be less than 1 cm.
3. Pay close attention to errors in the bony landmark and FJC markers when assessing the quality of your scaling results.


# **Inverse Kinematics** :
![[Pasted image 20260224151100.png]]
$w_{i}: i 번째 \;마커에 \;대한 \;weight,\quad$
$x_{i}^{exp} : 실험상\; 마커 \;위치(truth),\quad$
$x_{i}(q) : 추정 \;각도(q)에 \;대한 \;마커 \;위치 (forward \;kinematics)$

**IK에서 '오차(Error)'**
이미 Scale에서 마커 위치를 잘 맞췄더라도, 실제 보행 중에는 다음과 같은 이유로 마커가 완벽하게 일치하지 않습니다.
- **Soft Tissue Artifact:** 보행 시 근육과 피부가 출렁거리면서 뼈 위에 붙은 마커가 미세하게 움직dla
- **모델의 한계:** 실제 인체 관절은 복잡하지만, 모델은 힌지(Hinge) 관절처럼 단순화되어 있어 완벽한 재현이 어려움

그래서 IK는 이 오차들을 받아들이면서 **"그나마 가장 마커 위치와 비슷한 관절 각도가 무엇인가?"를 수학적으로 계산**
- **Scaling:** 모델 뼈에 마커를 **'박아넣는'** 단계입니다.
- **IK:** 박아넣은 마커를 **'기준점'** 삼아 관절 각도(Motion)를 뽑아내는 단계입니다.


# **Inverse Dynamics :**
main setting: Inverse Kinematics result.mot
external loads: Force plate 데이터를 포함한 mot 파일+ 수정한 xml 파일
#### **① 데이터 파일 경로 수정**
XML 파일의 가장 아랫부분(혹은 별도의 ID Setup 파일)에서 이 XML이 어떤 `.mot` 파일을 참조할지 경로를 지정
```
<datafile>subject01_walk1_grf.mot</datafile> 
```
#### **② 데이터 식별자(Identifier) 일치**
`.mot` 파일을 메모장으로 열었을 때, 상단의 컬럼 이름(예: `1_ground_force_vx`)이 XML의 `<force_identifier>`와 일치
- **XML 내용:** `<force_identifier> ground_force_v </force_identifier>`
- **의미:** "컬럼 이름이 `ground_force_vx`, `vy`, `vz`로 시작하는 데이터를 가져와라"라는 뜻. 만약 본인의 데이터 컬럼명이 `Force_X`라면 XML도 `Force_`로 변경
#### **③ 적용 부위(Applied to Body) 확인**
- `<applied_to_body> calcn_r </applied_to_body>`
### (Axis)
- Force plate 좌표계** (실험실 기준: 전진이 Y일 수도 있음)
- **OpenSim 모델의 좌표계** (전진이 X) 만약 이 두 개가 다르면, 모델은 앞으로 걷는데 바닥에서 밀어주는 힘은 옆으로 작용하는 기괴한 현상이 발생합니다. 이 경우 `.mot` 파일 내부의 숫자를 수정하거나, XML에서 힘의 방향 설정을 조정해야 합니다.