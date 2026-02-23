#### 25/08/01 지윤 누님 발표
human-robot interaction=>로봇과 사람과의 상호작용이 중요 (사람의 의도를 파악할 수 있어야 하고,  그에 따라 로봇이 적절한 output을 내줄 수 있어야 한다.)=>이를 위해선 좋은 성능의 human-robot interface(사람과 로봇 사이의 정보 매게체 느낌)가 전제 되어야 함.
closed loop은 양방향성의 sensing(feedback)이라고 볼 수 있다. (emg는 단방향, loop을 닫지 못하네) Interaction 의 영역은 loop이 closed된 형태가 되어야 한다(?)

HRI에선 어떤 정보를 어떻게 처리할 건지, 그리고 이 정보를 사람에게 표현할 건지가 중요
	예를 들어 바로 앞에 위험 요인이 생기면 이를 사람에게 어떻게 상기 시킬지
	ex) 진동을 주며 환기 시킨 후 청각+시각적으로 정보 전달

#### 25/08/22 형석 형님 발표
비정상 보행에 대한 적용
time-delay neural network-window를 늘리는 방식?
tcn은 병렬적 계산?
실 디바이스 적용엔 tensorRT
continual learning

#### 25/09/19 권승 형님 발표
**TRUST modeling for pHRI**
TRUST: Factor synchronizing human and robot, 사람이 로봇에게 몸을 맡기는 행위
pHRI: 물리적 관점에서의 HRI

#### 25/10/17 민석 형님 발표
RMA
![[Pasted image 20251017101801.png]]
enviromental encoder $\micro=f(com,friction,mass...)$
input(x,a) => output(z) regression model
base policy: $a_{t}=\pi(x_{t}, a_{t-1}, z_{t})$


## 25/12/05
실시간 제어를 위한 FPCA 제어

PCA를 적용할 땐 타겟한 상황에 대해서만,
time sequnce데이터 처리할때 적당한 구간 찾기,