# quaternion 기본 operation

기존의 real에 imaginary 한축을 추가하는 방식이 아닌-> 3개의 imaginary 축을 사용, 해당 imaginary에 perpendicular한 4차원 공간의 real part

2차원 공간에서 (real+image) 벡터는 회전 => 4차원 쿼터니언에선 더블 회전?

i, j, k는 각각 real에 perpendicular/ 그리고 i, j, k 각각 서로 perpendicular

quaternion q에 대해서,
$\textbf{q}=w+x\hat{i}+y\hat{j}+z\hat{k}$

$\textbf{q}=(w,\textbf{v})\in \mathbb{R}\times \mathbb{R}^3$ , where $\textbf{v} = (x,y,z)$

$\textbf{q}^{-1}=(w,-x,-y,-z)/(w^2+x^2+y^2+z^2)$

![[Pasted image 20260518225953.png|450]]
=> 
![[Pasted image 20260518230015.png|450]]

# quarternion을 통한 각속도 표현현

unit quaternion q는 $\textbf{q}=(\text{cos}\frac{\theta}{2},\hat{\textbf{v}}\, \text{sin}\frac{\theta}{2})\in \mathbb{S}^3$
$R_\text{q}\in\mathbb{SO}(3)$![[Pasted image 20260518231541.png|300]]
여기서 p는 p=(x,y,z)=(0,x,y,z) (purely imaginary quaternion).
여기서, $R_\text{q}=R_\text{-q}$ , 즉 q=-q (반대 축으로 반대로 돌리는거니.). 그러므로 mapping between $\mathbb{S}^3$(unit hyper-sphere)과 $\mathbb{SO}(3)$는 2대1 매핑 (two to one)

$R_q(p) = qpq^{-1}$전개
$$qp = (w, \vec{v})(0, \vec{p}) = (-\vec{v} \cdot \vec{p}, w\vec{p} + \vec{v} \times \vec{p})$$
$qp = (s, \vec{t})$ 라고 치환하면 $s = -\vec{v} \cdot \vec{p}$ 이고, $\vec{t} = w\vec{p} + \vec{v} \times \vec{p}$
$$(qp)q^{-1} = (s, \vec{t})(w, -\vec{v}) = (sw - \vec{t} \cdot (-\vec{v}), s(-\vec{v}) + w\vec{t} + \vec{t} \times (-\vec{v}))$$
실수부
$$sw + \vec{t} \cdot \vec{v} = -w(\vec{v} \cdot \vec{p}) + (w\vec{p} + \vec{v} \times \vec{p}) \cdot \vec{v} = -w(\vec{v} \cdot \vec{p}) + w(\vec{p} \cdot \vec{v}) + 0 = 0$$
벡터부
$$-s\vec{v} + w\vec{t} - \vec{t} \times \vec{v} = (\vec{v} \cdot \vec{p})\vec{v} + w(w\vec{p} + \vec{v} \times \vec{p}) - (w\vec{p} + \vec{v} \times \vec{p}) \times \vec{v}$$
$$= (\vec{v} \cdot \vec{p})\vec{v} + w^2\vec{p} + 2w(\vec{v} \times \vec{p}) - ((\vec{v} \cdot \vec{v})\vec{p} - (\vec{v} \cdot \vec{p})\vec{v})$$
$$= 2(\vec{v} \cdot \vec{p})\vec{v} + (w^2 - \|\vec{v}\|^2)\vec{p} + 2w(\vec{v} \times \vec{p})$$
$w = \cos\frac{\theta}{2}$ 와 $\vec{v} = \hat{u}\sin\frac{\theta}{2}$ 를 대입하고 삼각함수 배각공식을 적용
- $w^2 - \|\vec{v}\|^2 = \cos^2\frac{\theta}{2} - \sin^2\frac{\theta}{2} = \cos\theta$
- $2w\vec{v} = 2\cos\frac{\theta}{2}\sin\frac{\theta}{2}\hat{v} = \sin\theta\hat{v}$
- $2(\vec{v} \cdot \vec{p})\vec{v} = 2\sin^2\frac{\theta}{2}(\hat{v} \cdot \vec{p})\hat{v} = (1 - \cos\theta)(\hat{v} \cdot \vec{p})\hat{v}$

최종적으로
$$R_q(p) = \cos(\theta)\vec{p} + (1 - \cos(\theta))(\hat{v} \cdot \vec{p})\hat{v} + \sin(\theta)(\hat{v} \times \vec{p})$$
이 쿼터니언 회전이 **Rodrigues' rotation formula**이고, 이것의 **행렬 형태**가
$$R=I+sin(\theta)\hat{w}+(1-cos(\theta))\hat{w}^2$$
$$\begin{aligned}Rp&=p+sin(\theta)w\times p+(1-cos(\theta))(w\times (w\times p))\\
&=p+sin(\theta)w\times p+(1-cos(\theta))((w\cdot p)w-(w\cdot w)p) \\
&=cos(\theta)p+(1-cos(\theta))(w\cdot p)w+sin(\theta)(w\times p)
\end{aligned}$$


point of unit quaternion curve q(t)의 tangent vector는 tangent space $T_{q}\mathbb{S}^3$위에 놓여져 있다. 



회전 행렬 $R$ 의 미분 $\dot{R}$ 은 $R$ 에 접하는 공간에 있습니다. 이를 항등원 $I$ 로 끌어오기 위해 역행렬 $R^T$ 를 곱해줍니다.
$$\hat{\omega} = R^T \dot{R}$$
여기서 도출된 $\hat{\omega}$ 는 대각 성분이 0인 Skew-symmetric 행렬이 되며, 이것이 바로 Lie Algebra $\mathfrak{so}(3)$ 에 속하는 물리적 각속도 텐서입니다.


**단위 쿼터니언 $\mathbb{S}^3$ 의 경우:**
쿼터니언 $q$ 의 미분 $\dot{q}$ 역시 $q$ 에 접하는 공간 $T_q\mathbb{S}^3$ 에 존재합니다. 이를 항등원 $1 = (1, 0, 0, 0)$ 로 끌어오기 위해 역원 $q^{-1}$ 를 곱해줍니다.
$$(w^2 + x^2 + y^2 + z^2 = 1)$$
$$2w\dot{w} + 2x\dot{x} + 2y\dot{y} + 2z\dot{z} = 0$$
$$w\dot{w} + \vec{v} \cdot \dot{\vec{v}} = 0$$
$$\begin{aligned}q^{-1}\dot{q} &= (w, -\vec{v})(\dot{w}, \dot{\vec{v}})\\
&=(w\dot{w} - (-\vec{v}) \cdot \dot{\vec{v}}, \quad w\dot{\vec{v}} + \dot{w}(-\vec{v}) + (-\vec{v}) \times \dot{\vec{v}}) \\
&= (0,\quad w\dot{\vec{v}} + \dot{w}(-\vec{v}) + (-\vec{v}) \times \dot{\vec{v}})
\end{aligned}$$

결론적으로 
$$\omega = 2 q^{-1} \dot{q}$$
앞서 문서에서 확인하셨듯 , 이 결과물인 $\omega$ 는 실수부가 0인 순수 허수 쿼터니언이 되며 , 이는 $T_1\mathbb{S}^3 \equiv \mathbb{R}^3$ 에 속하는 3차원 각속도 벡터로 취급됩니다.


# quaternion의 exponential logarithmic maps
쿼터니언 $\textbf{q}=(w,v)=(\text{cos}\frac{\theta}{2},\hat{\textbf{v}}\, \text{sin}\frac{\theta}{2})\in \mathbb{S}^3$ 에서 v는 회전의 축과 회전 각도 정보를 내포하고 있음.

아래 이미지와 같이 exponential/logarithmic map을 통해 tangent space $T_1\mathbb{S}^3$상의 3차원 벡터 $v$와, $\mathbb{S}^3$상의 쿼터니언 포인트 q간의 1대1 매칭이 가능함  
![[Pasted image 20260518233034.png|400]]
![[Pasted image 20260519180514.png|600]]

![[Pasted image 20260519180613.png]]여기서 $\text{v}$에 대해 $|\text{v}|<\pi$ 조건을 통해 1대1 매칭이 가능하도록 함.

# geodesics
![[Pasted image 20260519191329.png|600]]
unit quaternion q1에서 q2까지 가는 회전을 $q_1^{-1}q_2$ 으로 표현. $\mathbb{S}_{3}$상 q1과 q2사이의 최단 거리를 **geodesic of $\mathbb{S}_{3}$** 라고 부르고 geodesic norm은 다음과 같이 표현
$$\text{dist}(q_1,q_2)=|\text{log}(q_1^{-1}q_2)|$$
여기서 q1, q2의 slerp (spherical linear interpolation) (두 쿼터니안 사이의 path)은
$$\begin{aligned}
\text{slerp}_{t}(q_1, q_2)&=q_1\text{exp}(t\cdot\text{log}(q_{1}^{-1}q_2))\\
&=q_1(q_{1}^{-1}q_2)^{t}
\end{aligned}$$
where 0<=t<=1