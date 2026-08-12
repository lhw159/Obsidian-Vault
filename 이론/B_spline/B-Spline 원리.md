

λ가 하는 일 — penalized smoothing spline
스무딩 스플라인은 함수 f에 대해 이걸 최소화합니다:

$$
\min_{f};; \underbrace{\sum_i w_i,\big(y_i - f(x_i)\big)^2}{\text{데이터 충실도}} ;+; \lambda \underbrace{\int \big(f''(x)\big)^2,dx}{\text{거칠기(roughness) 페널티}}
$$

1항 = 데이터에 얼마나 가깝나 (작을수록 점을 잘 따라감)
2항 = ∫f''² = 곡률 총량 = 얼마나 구불구불한가
λ = 둘 사이 저울:
λ	지배항	결과
λ → 0	충실도	모든 점 통과 = 보간 (노이즈 그대로)
λ → ∞	페널티	f''→0 = 직선 (최대 평활, 디테일 소멸)
중간	균형	추세는 따르고 노이즈는 버림 ← 목표
핵심: ∫f''²를 최소화하는 함수가 수학적으로 자연 3차 스플라인입니다(Reinsch 1967) — 그래서 "cubic". --lam이 이 λ이고, 미지정이면 GCV(generalized cross-validation)가 예측오차를 최소화하는 λ를 자동 선택합니다.

코드 흐름 수식 정리
뼈별로, 정합 포즈 ${P_f = [,R_f \mid t_f,]}$ (프레임 $f$, model→world):

① Outlier 정제
$$
\Delta\theta_f = \lVert \log(R_{f-1}^\top R_f)\rVert,\quad \Delta d_f = \lVert c_f - c_{f-1}\rVert
$$
$;>; \text{median} + k\cdot 1.4826,\text{MAD}$ 이면 제외 → inlier 집합 $I$

② 회전 (so(3) 접선공간)

부호 unwrap: $\langle q_f, q_{f-1}\rangle < 0 \Rightarrow q_f \leftarrow -q_f$
평균회전(기준): $\displaystyle R_0 = \arg\min_R \sum_{f\in I} d(R, R_f)^2$
접선벡터(로그): $;v_f = \log(R_0^\top R_f)\in\mathbb{R}^3$ ← $R_0$ 근방이라 wrap 안전
성분별 penalized 스플라인: $\displaystyle s_k=\arg\min_g \sum_{f\in I}(v_{f,k}-g(f))^2+\lambda!\int g''^2$
복원(지수사상): $;\tilde R(f) = R_0,\exp!\big(\tilde v(f)\big),\quad \tilde v=(s_1,s_2,s_3)$
③ 이동 (centroid로 회전-이동 디커플)

centroid world 경로: $;c_f = R_f,m + t_f$ ($m$ = STL centroid, model 좌표)
성분별 스플라인: $\tilde c_k(f)$ (② 와 같은 목적함수)
원점 복원: $;\tilde t(f) = \tilde c(f) - \tilde R(f),m$
④ 잔차 / 저장
$$
\text{회전잔차}=\lVert\log(R_f^\top \tilde R_f)\rVert,\quad \text{이동잔차}=\lVert c_f-\tilde c_f\rVert
$$
$$ \tilde P_f = [\tilde R_f \mid \tilde t_f] \xrightarrow{\text{txf2dcmpar}} \texttt{par\_reg\_table} $$
저장

핵심 아이디어 세 줄:

회전은 곡면(SO(3))이라 log로 평평한 접선공간에 내려서 스플라인하고 exp로 되올림
이동은 centroid 경로로 바꿔 회전-이동 커플링을 끊고 스플라인
스무딩 세기는 오직 λ — 데이터충실도 vs 곡률의 저울. GCV가 자동으로 잡아줌
GUI는 uv로 재시작하면 새 버튼이 뜹니다. λ를 바꿔가며 --show로 잔차/곡선을 보고 적정값을 정하시면 돼요.