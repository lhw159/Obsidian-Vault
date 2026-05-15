기존의 real에 imaginary 한축을 추가하는 방식이 아닌-> 3개의 imaginary 축을 사용, 해당 imaginary에 perpendicular한 4차원 공간의 real part

2차원 공간에서 (real+image) 벡터는 회전 => 4차원 쿼터니언에선 더블 회전?

i, j, k는 각각 real에 perpendicular/ 그리고 i, j, k 각각 서로 perpendicular




회전 행렬 $R$ 의 미분 $\dot{R}$ 은 $R$ 에 접하는 공간에 있습니다. 이를 항등원 $I$ 로 끌어오기 위해 역행렬 $R^T$ 를 곱해줍니다.
$$\hat{\omega} = R^T \dot{R}$$
여기서 도출된 $\hat{\omega}$ 는 대각 성분이 0인 Skew-symmetric 행렬이 되며, 이것이 바로 Lie Algebra $\mathfrak{so}(3)$ 에 속하는 물리적 각속도 텐서입니다.


**단위 쿼터니언 $\mathbb{S}^3$ 의 경우:**
쿼터니언 $q$ 의 미분 $\dot{q}$ 역시 $q$ 에 접하는 공간 $T_q\mathbb{S}^3$ 에 존재합니다. 이를 항등원 $1 = (1, 0, 0, 0)$ 로 끌어오기 위해 역원 $q^{-1}$ 를 곱해줍니다.

$$\omega = 2 q^{-1} \dot{q}$$

앞서 문서에서 확인하셨듯 , 이 결과물인 $\omega$ 는 실수부가 0인 순수 허수 쿼터니언이 되며 , 이는 $T_1\mathbb{S}^3 \equiv \mathbb{R}^3$ 에 속하는 3차원 각속도 벡터로 취급됩니다.

