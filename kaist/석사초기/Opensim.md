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