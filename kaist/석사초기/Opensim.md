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
**Inverse Kinematics** :
![[Pasted image 20260224151100.png]]
$w_{i}: i 번째 \;마커에 \;대한 \;weight,\quad$
$x_{i}^{exp} : 실험상\; 마커 \;위치(truth),\quad$
$x_{i}(q) : 추정 \;각도(q)에 \;대한 \;마커 \;위치 (forward \;kinematics)$
