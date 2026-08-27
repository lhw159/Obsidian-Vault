# 강체 포즈 스무딩 — B-spline 이론 + so(3) 구현 정리

정합으로 얻은 프레임별 포즈 $\{P_f=[\,R_f\mid t_f\,]\}$ 를 노이즈만 걷어내고 매끄럽게 만드는
파이프라인(`scripts/smooth_poses_so3.py`)의 이론적 배경과 수식 흐름을 정리한다.

핵심 한 줄: **cubic B-spline은 "연속 곡선을 만드는 기저"일 뿐이고, 스무딩은 penalized(근사)
적합에서 나온다. 회전은 곡면(SO(3))이라 `log/exp`로 접선공간에 내려서 스플라인한다.**

---

## 1. B-spline 이론

### 1.1 정의 — 기저(basis) 함수

차수(degree) $p$, **knot 벡터** $U=\{u_0\le u_1\le\dots\le u_m\}$, **제어점(control point)**
$\{\mathbf{c}_0,\dots,\mathbf{c}_n\}$ ($m=n+p+1$) 가 주어질 때 B-spline 곡선은

$$
\mathbf{S}(u)=\sum_{i=0}^{n} N_{i,p}(u)\,\mathbf{c}_i
$$

여기서 $N_{i,p}$ 는 **Cox–de Boor 재귀**로 정의되는 basis 함수:

$$
N_{i,0}(u)=\begin{cases}1 & u_i\le u<u_{i+1}\\ 0 & \text{otherwise}\end{cases}
$$
$$
N_{i,p}(u)=\frac{u-u_i}{u_{i+p}-u_i}\,N_{i,p-1}(u)
      +\frac{u_{i+p+1}-u}{u_{i+p+1}-u_{i+1}}\,N_{i+1,p-1}(u)
$$

즉 곡선은 **basis 함수들의 가중합**이고, 가중치가 제어점이다.

### 1.2 주요 성질

- **국소 지지(local support)**: $N_{i,p}$ 는 $[u_i,u_{i+p+1})$ 에서만 0이 아님. → 제어점 하나를
  움직이면 곡선의 **국소 구간만** 변한다(전역 영향 없음). 스플라인 스무딩이 안정적인 이유.
- **단위 분할(partition of unity)**: $\sum_i N_{i,p}(u)=1$. → **affine 불변**, 곡선이 제어점들의
  **볼록껍질(convex hull)** 안에 놓임.
- **연속성**: knot이 단순(중복 없음)하면 곡선은 $C^{p-1}$ 연속. 중복도 $r$ 이면 그 knot에서 $C^{p-r}$.
- **비음성**: $N_{i,p}(u)\ge 0$.

### 1.3 cubic B-spline ($p=3$) — 왜 3차인가

- $p=3$ 이면 $C^{2}$ 연속 → **위치·속도·가속도가 매끄럽다**. 미분(각속도 등)까지 안정적으로
  뽑을 수 있어 kinematics·이벤트 검출에 적합.
- 한 파라미터 값 $u$ 에서 **동시에 0이 아닌 basis는 4개**($N_{i-3},\dots,N_i$) → 국소성이 좋음.
- 수학적 근거(2.3): 데이터에 대한 $\int (f'')^2$ 최소화 해가 정확히 **자연 3차 스플라인**이다.

### 1.4 knot 종류

- **uniform**: knot 등간격. 주기·정규 데이터에 적합.
- **clamped(끝 knot을 $p+1$ 중복)**: 곡선이 **양 끝 제어점을 통과** → 경계 제어에 유리(외삽 억제).
- **비균일(non-uniform)**: 결측·불규칙 샘플에서 실제 프레임 번호를 파라미터로 사용.

> B-spline의 "B"는 basis. **NURBS**는 여기에 가중치(rational)를 더한 일반화이며, 강체 포즈
> 스무딩엔 보통 필요 없다.

---

## 2. 보간 vs 근사(스무딩) — 스무딩은 어디서 나오나

**같은 cubic B-spline 기저라도 "어떻게 적합하느냐"에 따라 보간이 되기도, 스무딩이 되기도 한다.**

### 2.1 보간(interpolating) 스플라인
제어점을 조정해 **모든 데이터점을 통과**($\mathbf{S}(x_i)=y_i$)시킴. → 노이즈도 그대로 통과.
연속적이지만 **스무딩이 아니다**.

### 2.2 회귀(regression) B-spline
제어점 수를 **데이터보다 적게** 두고 최소제곱 적합:
$\min_{\mathbf{c}}\sum_i (y_i-\mathbf{S}(x_i))^2$. knot을 성기게 깔수록 더 매끄러움(스무딩이 knot 수로 조절됨).

### 2.3 smoothing(penalized) 스플라인 — 우리가 쓰는 방식
knot을 촘촘히 깔되, **거칠기 페널티**로 매끄러움을 제어:

$$
\boxed{\;\min_{f}\ \sum_i w_i\big(y_i-f(x_i)\big)^2 \;+\; \lambda\int \big(f''(x)\big)^2\,dx\;}
$$

- **1항 = 데이터 충실도**(점에 가까움), **2항 = 곡률 총량**(구불거림).
- 이 변분문제의 해는 **자연 3차 스플라인**(Reinsch 1967). 즉 "cubic"은 결과이지 가정이 아니다.

#### λ의 역할 (평활 저울)

| $\lambda$ | 지배항 | 결과 |
|---|---|---|
| $\lambda\to 0$ | 충실도 | 모든 점 통과 = **보간** (노이즈 유지) |
| $\lambda\to\infty$ | 페널티 | $f''\to 0$ = **직선**(최소제곱 회귀선) |
| 중간 | 균형 | 추세는 따르고 노이즈는 버림 ← **목표** |

- **자유도 관점**: $\lambda$ 를 키우면 유효 자유도(effective df)가 줄어 모델이 단순해진다.
- **λ 자동 선택 — GCV**(generalized cross-validation): 각 점을 뺐을 때의 예측오차를 근사적으로
  최소화하는 $\lambda$ 를 고름.
  $$
  \mathrm{GCV}(\lambda)=\frac{n\,\lVert y-\hat y_\lambda\rVert^2}{\big(n-\mathrm{tr}\,H_\lambda\big)^2},
  \qquad \hat y_\lambda=H_\lambda\,y
  $$
  ($H_\lambda$ = 스무더 행렬, $\mathrm{tr}\,H_\lambda$ = 유효 자유도.) 코드에서 `lam=None` 이면 이 값 사용.
- **P-spline**(Eilers & Marx 1996): $\int f''^2$ 대신 제어점의 **이산 차분 페널티**
  $\lambda\sum(\Delta^2 c_i)^2$ 를 쓰는 실용 변형. 계산이 간단하고 성질이 유사.

---

## 3. 강체 포즈는 벡터공간이 아니다 — 곡면 문제

포즈는 $SE(3)=SO(3)\times\mathbb{R}^3$. **이동 $t\in\mathbb{R}^3$ 는 벡터라 스플라인 OK**지만,
**회전 $R\in SO(3)$ 는 곡면**이라 성분을 직접 스플라인하면 안 된다.

### 3.1 쿼터니언과 이중덮개(double cover)
회전은 단위 쿼터니언 $q\in S^3$ 로 표현되는데 $q$ 와 $-q$ 가 **같은 회전**이다. 정합 결과는 프레임마다
부호가 뒤집혀 나올 수 있어, 그대로 스플라인하면 **부호 점프가 거대한 가짜 변화**로 들어간다.
→ 전처리: $\langle q_f,q_{f-1}\rangle<0$ 이면 $q_f\leftarrow-q_f$ (**부호 unwrap**).

### 3.2 단위노름 제약
$\lvert q\rvert=1$ 인데 4성분을 각각 $\mathbb{R}^4$ 에서 스플라인하면 결과가 단위구를 벗어난다.
재정규화($q/\lvert q\rvert$)는 근사일 뿐 → **정석은 접선공간에서 작업**.

### 3.3 접선공간: `log`/`exp` (리 대수 $\mathfrak{so}(3)$)
기준 회전 $R_0$ 근방에서
$$
v=\log(R_0^\top R)\in\mathbb{R}^3\ (\text{회전벡터}),\qquad R=R_0\exp(v)
$$
로 곡면을 **평평한 $\mathbb{R}^3$** 로 펴서 스플라인하고 다시 곡면으로 되돌린다.
$\log$ 는 회전각이 $\pi$ 에 접근하면 wrap하므로, $R_0$ 를 **평균회전**으로 잡아 $\lVert v\rVert$ 를 작게 유지한다
(코드가 최대각을 출력, $0.9\pi$ 초과 시 경고).

### 3.4 회전-이동 커플링
4×4의 이동성분 $t$ 는 **모델 원점(=CT origin, 뼈 중심 아님)의 위치**다. 원점이 뼈에서 멀면
작은 회전오차가 큰 $t$ 요동을 만든다. → **centroid 경로** $c=R\,m+t$ 를 스무딩한 뒤
$t=\tilde c-\tilde R\,m$ 으로 복원해 커플링을 끊는다.

---

## 4. 구현 흐름 (수식) — `smooth_poses_so3.py`

뼈별로, 정합 포즈 $\{P_f=[\,R_f\mid t_f\,]\}$ (프레임 $f$, model→world):

**① Outlier 정제**
$$
\Delta\theta_f=\lVert\log(R_{f-1}^\top R_f)\rVert,\quad \Delta d_f=\lVert c_f-c_{f-1}\rVert
$$
$>\ \mathrm{median}+k\cdot 1.4826\,\mathrm{MAD}$ 이면 제외 → inlier 집합 $I$.
(최소제곱 스플라인은 outlier에 끌리므로 **적합 전 정제**. 스무딩이 아니라 데이터 정제 단계.)

**② 회전 (so(3) 접선공간)**
- 부호 unwrap: $\langle q_f,q_{f-1}\rangle<0\Rightarrow q_f\leftarrow-q_f$
- 평균회전(기준): $\displaystyle R_0=\arg\min_R\sum_{f\in I}d(R,R_f)^2$
- 접선벡터: $v_f=\log(R_0^\top R_f)\in\mathbb{R}^3$
- 성분별 penalized 스플라인: $\displaystyle s_k=\arg\min_g\sum_{f\in I}(v_{f,k}-g(f))^2+\lambda\!\int g''^2$
- 복원: $\tilde R(f)=R_0\exp(\tilde v(f)),\ \ \tilde v=(s_1,s_2,s_3)$

**③ 이동 (centroid 디커플)**
- centroid world 경로: $c_f=R_f\,m+t_f$ ($m$=STL centroid, model 좌표)
- 성분별 스플라인: $\tilde c_k(f)$ (②와 같은 목적함수)
- 원점 복원: $\tilde t(f)=\tilde c(f)-\tilde R(f)\,m$

**④ 잔차 / 저장**
$$
\text{회전잔차}=\lVert\log(R_f^\top\tilde R_f)\rVert,\quad \text{이동잔차}=\lVert c_f-\tilde c_f\rVert
$$
$\tilde P_f=[\tilde R_f\mid\tilde t_f]\xrightarrow{\text{txf2dcmpar}}\texttt{par\_reg\_table}$ →
`..._bsplineSmoothed.mat` 저장(원본 `_manuallyAdjusted.mat` 불변).

---

## 5. 실무 체크리스트

1. **outlier 먼저 제거** — 튄 프레임이 곡선을 왜곡(§4①).
2. **부호 unwrap** — 쿼터니언 double cover(§3.1).
3. **접선공간 log/exp** — 곡면 위 정석(§3.3), $R_0$=평균으로 wrap 회피.
4. **penalized(smoothing) 적합** — 보간 아님(§2.3). λ 또는 GCV로 강도 조절.
5. **회전·이동 분리 + centroid 디커플**(§3.4).
6. **경계 처리** — 끝에서 외삽 금지, 튄 끝프레임 주의.
7. **결측 프레임** — 실제 프레임번호를 파라미터(비균일)로, 정합 구간 안에서만 신뢰.
8. **검증** — 잔차가 무편향 노이즈처럼 보이는지, 실제 이벤트를 안 지웠는지, DRR 재투영으로 X-ray와
   여전히 맞는지 확인.

---

## 6. 사용법 요약

```bash
# 스무딩 + 저장(별도 파일) + 비교 플롯
uv run python scripts/smooth_poses_so3.py --trial "$TR" --pose manual --save --show
uv run python scripts/smooth_poses_so3.py --trial "$TR" --lam 5000        # 더 매끄럽게(λ↑)

# 스무딩 결과를 진단/kinematics/GUI 에서 사용
uv run python scripts/viz_pose_trajectory.py --trial "$TR" --pose bspline --show
uv run python scripts/knee_kinematics.py     --trial "$TR" --pose bspline --fps 100
# GUI: "Load B-spline Smoothed" 버튼
```

옵션: `--lam`(평활강도, 미지정=GCV자동), `--fill`(빈 프레임 채워 평가),
`--jump_trans/--jump_rot`(outlier 임계 수동), `--save`(bsplineSmoothed 저장, 원본 불변).

---

## 참고문헌

- C. de Boor, *A Practical Guide to Splines*, Springer, 1978. (B-spline·Cox–de Boor)
- C. Reinsch, "Smoothing by spline functions," *Numer. Math.* 10 (1967) 177–183. (smoothing spline·λ)
- G. Wahba, *Spline Models for Observational Data*, SIAM, 1990. (penalized spline·GCV)
- P. Eilers, B. Marx, "Flexible smoothing with B-splines and penalties," *Statist. Sci.* 11 (1996). (P-spline)
- M.-J. Kim, M.-S. Kim, S. Shin, "A general construction scheme for unit quaternion curves
  with simple high order derivatives," *SIGGRAPH* 1995. (SO(3) cumulative B-spline)
- F. Park, B. Ravani, "Smooth invariant interpolation of rotations," *ACM TOG* 16 (1997). (log/exp 회전 곡선)







1. 곡선을 제어점의 식으로 쓰면
$$S(x) = \sum_j c_j , N_j(x)$$

$N_j$ = basis 함수(knot이 정한 뼈대, 고정), $c_j$ = 제어점(미지수).
데이터 프레임 $x_i$에서 곡선값 = $\sum_j c_j N_j(x_i)$. 이걸 행렬로 $S = Bc$ ($B_{ij}=N_j(x_i)$, "각 프레임에서 각 basis 값").

2. 풀 목적함수 (fit + 매끄러움)
$$\min_c \underbrace{|y - Bc|^2}{\text{데이터에 가깝게}} + \lambda \underbrace{c^\top \Omega c}{\text{매끄럽게}(\int S''^2)}$$

$y$ = 실제 pose값들, $\Omega$ = 곡률 penalty 행렬(knot/basis만으로 결정, 데이터 무관).

3. 미분해서 0 → 선형방정식
$$\boxed{,(B^\top B + \lambda,\Omega),c = B^\top y,}\quad\Rightarrow\quad c = (B^\top B + \lambda\Omega)^{-1} B^\top y$$

닫힌형(closed-form), 반복 없이 한 번에 풀려요.
$c$ = 데이터 $y$를 "매끄럽게 섞은" 값 ($B^\top y$로 데이터 반영, $\lambda\Omega$로 매끄러움 규제).

4. 직관 (λ 역할이 여기서 보임)
λ=0: $c=(B^\top B)^{-1}B^\top y$ = 순수 최소제곱 → 데이터 바짝 따라감(구불).
λ 큼: penalty가 지배 → 이웃 제어점끼리 서로 당겨 붙음(곡률 억제) → 매끈.
즉 제어점 = "데이터에 끌리는 힘"과 "이웃과 매끄럽게 있으려는 힘"의 균형점을 방정식으로 찾은 것.

5. outlier·robust와 연결
목적함수에 가중치 $w_i$를 넣으면 $|W^{1/2}(y-Bc)|^2$ → 방정식이 $(B^\top W B + \lambda\Omega)c = B^\top W y$.

**outlier에 $w_i=0$**을 주면(우리 robust 제거) → 그 프레임이 $B^\top W y$에 안 들어감 → 제어점이 outlier에 안 끌림.
이게 "robust 제거 = fit에서 빼기"의 수학적 정체예요.
한 줄 요약
제어점 = $(B^\top B + \lambda\Omega)^{-1}B^\top y$ — 데이터+매끄러움을 균형 맞춘 선형해.
raw pose 복사 ❌, fit되어 나오는 계수 ⭕.
λ로 매끄러움, **가중치 $w$**로 outlier 배제.
즉 "제어점을 어떻게 얻나?" = 위 선형방정식 한 번 풀면 나오고, 그 안에 이미 **smoothing(λ)과 outlier 제어(w)**가 다 들어있어요.