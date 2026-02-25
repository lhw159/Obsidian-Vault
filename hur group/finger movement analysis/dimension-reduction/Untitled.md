**Kinematic hand synergies differ between reach-and-grasp and functional object manipulation**
## Intro
reach and grasp(rag) 뿐만 아니라 tool manipulation(tm)에 대한 synergy 분석이 필요하다.
2가지 가정
	1. reach and grasp와 tool manipulation 의 시너지 수는 같을 것
	2. 다만 시너지의 형태가 서로 다를 것이다.

## Method
여성3, 남성 4 (18~28), 오른손 잡이

first task: 단순 rag
scissors, zip tie, screwdriver, wire , harness with tied branched ends, and wire harness

	참가자는 테이블 위에 손을 평평하게 놓고 시작
	3초 후, 30cm 앞에 놓인 물체를 잡으라는 구두 지시
	각 시행은 15초 동안 진행되었으며, 5개의 다른 물체에 대해 4번씩, 총 20번 반복
	
	데이터의 경우 각 동작별로, 각 동작 시작-종료 까지의 구간을 100개로 나누어 분석(각 동작의 진행도를 기준으로 파악)

second task: tm
위 rag에 있는 도구들을 직접 사용(zip tie로 전선 묶기=>잉여 tie 자르기=>드라이버로 행거 달기...)

	시작 자세는 위와 동일
	3초 후, 전선 뭉치(wire harness) 조립 과업의 특정 단계를 시작하라는 지시
	참가자가 과제를 마치고 손을 원래 위치로 되돌리면 데이터 수집이 종료
	
	각 동작에 대해 1000분할로 분석

cyber glove 사용(200hz)
- **네 손가락 (검지, 중지, 약지, 새끼)**:
    - 끝마디(DIP) 굽힘: 1개 x 4손가락 = **4개**
    - 중간마디(PIP) 굽힘: 1개 x 4손가락 = **4개**
    - 손바닥-손가락 관절(MCP) 굽힘: 1개 x 4손가락 = **4개**
    - 손바닥-손가락 관절(MCP) 좌우 벌림: 1개 x 4손가락 = **4개**
    - **소계: 16개**
- **엄지**:
    - 끝마디(IP) 굽힘: **1개**
    - 손바닥-엄지 관절(MCP) 굽힘: **1개**
    - 손목-손바닥 관절(CMC) 벌림: **1개**
    - 손목-손바닥 관절(CMC) 회전: **1개**
    - **소계: 4개** 
- **손바닥 및 손목**
    - 손바닥 아치(Palm Arch): **1개**
    - 손목 상하 움직임(Pitch): **1개**
    - 손목 좌우 움직임(Yaw): **1개**
    - **소계: 3개**

## Data analysis
Singular Value Decomposition(SVD)를 이용한 행렬 분해
각 task에 대해 4, 5번 행위는 배제하고 데이터 처리(rag와 tm 사이에 차이가 별로 없어서)
## Result and Discussion
본 실험의 목적은 RAG와 TM의 시너지 간의 유사성을 찾는 것.

TM 시너지에 대한 재배열을 적용하여 높은 유사성을 가지는 시너지 별로 묶는 과정도 진행

scissor의 경우 사람 손의 구조가 제한되므로, 두 task의 시너지 수가 거의 동일, 재배열을 가했을때 형태 유사도도 높게 나옴
달리, scerew driver와 zip tie의 경우 시너지 수와 형태의 유사도가 낮은 수치로 나옴

=> 전반적으로 RAG 시너지 만으로는 TM 시너지를 설명하기엔 불충분 함,

RAG와 TM 시너지 사이 비교가 시사하는 바는 '고차원의 시너지'. 보통 고차원 시너지는 노이즈로 취급되지만, 두 task를 구분짓는 기준이 고차원 시너지가 될 수 있다는 점을 보여줌.