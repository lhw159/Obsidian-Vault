# **Estimating finger joint angles by surface EMG signal using feature extraction and transformer-based deep learning model**

# Reference
**Slope sign change(SSC), Zero crossing(ZC), Hjorth time-domain parameters (HT), and other time and frequency domain feature extraction을 통해 12가지의 손가락 제스처 분류**
K. Anam, C. Avian, D.I. Swasono, A.Z. Muttaqin, H. Ismail, Estimation of Finger Joint Movement based on Electromyography Signal using Long Short-Term Memory, in: CENIM 2020 - Proceeding: International Conference on Computer Engineering, Network, and Intelligent Multimedia 2020, pp. 86–90, Nov. 2020, doi: 10.1109/CENIM51130.2020.9298023. => 고려해볼만한 feature들 (SSC, ZC, HT, FT...)+ RMS가 useful한 feature임

**convoultion-based time-domain feature extraction을 통해 53가지의 movement 구별**
(R.N. Khushaba, A.H. Al-Timemy, O.W. Samuel, E.J. Scheme, Myoelectric Control  With Fixed Convolution-Based Time-Domain Feature Extraction: Exploring the  Spatio-Temporal Interaction, IEEE Trans. Hum. Mach. Syst. (2022) 1–11, https://doi.org/10.1109/THMS.2022.3146053.) => 제스처 분류 모델 영역은 robust하다고 생각

**LSTM을 통해 손가락 각도 추정**
C. Avian, S.W. Prakosa, M. Faisal, J.S. Leu, Estimating finger joint angles on surface EMG using Manifold Learning and Long Short-Term Memory with Attention mechanism, Biomed. Signal Process. Control 71 (Jan. 2022), 103099, https://doi.org/10.1016/J.BSPC.2021.103099. =>아마 regression 모델의 초기 버전 느낌이 않을까 생각. lstm 사용 고려해볼 때 참고

**DWT와  Support Vector Regression (SVR)를 통해 12가지 movement에 대한 손가락 각도 추정(22dof)**
R. Alazrai, M.I. Daoud, A. Khalifeh, N. Alnuman, Y. Mowafi, D. Alabed, A wavelet-based approach for estimating the joint angles of the fingers and wrist using electromyography signals, Commun. Computer Inform. Sci. 1002 (2019) 31–45, https://doi.org/10.1007/978-3-030-16785-1_3/COVER. =>R-sqaure가 60%이상. regression 모델의 초창기 버전일듯. 나중에 읽기

**PCA, LSTM, MAV, RMS 등을 사용하여 8 fingers angle estimate model. R-square 87.4%**
[9] K. Anam, C. Avian, D.I. Swasono, A.Z. Muttaqin, H. Ismail, Estimation of Finger Joint Movement based on Electromyography Signal using Long Short-Term Memory, in: CENIM 2020 - Proceeding: International Conference on Computer Engineering, Network, and Intelligent Multimedia 2020, pp. 86–90, Nov. 2020, doi: 10.1109/CENIM51130.2020.9298023. =>PCA를 적용하여 dof를 줄인 연구일듯. 손가락 모델의 dof를 줄이는 방법 참고

**RMS, PCA, NMF, Gated Recurrent Unit(GRU, LSTM과 같은 RNN model 중 하나) 등을 통한 차원 축소 regression 모델. R-Square score of 0.921 from 52 movements**
Z. Ilyas, K. Anam, Widjonarko, C. Avian, A.Z. Muttaqin, M.E. Ramadhan, Evaluation of Gated-Recurrent Unit for Estimating Finger-Joint Angle using Surface Electromyography Signal, in: International Conference on Electrical Engineering, Computer Science and Informatics (EECSI), vol. 2022-October, pp. 25–28, 2022, doi: 10.23919/EECSI56542.2022.9946461. =>위 논문 상위 호완일듯. 차원 축소 어떻게 진행했는지 확인 해볼 것

**동시 비례 근전도 제어 시스템 (myoelectirc control systems, MCS)를 통한 14개 손가락 각도 예측**
K. Anam, D. I. Swasono, A. Triono, A. Z. Muttaqin, F. S. Hanggara, Random forest-based simultaneous and proportional myoelectric control system for finger movements, 2023, doi:10.1080/10255842.2023.2165068.

**EMG에 대한 time-frequency domain feature extraction의 단점**
[43]C. Spiewak, A Comprehensive Study on EMG Feature Extraction and Classifiers, Open Access J. Biomed. Eng. Biosci., 1(1) (Feb. 2018), doi:10.32474/OAJBEB.2018.01.000104. 

**8 fingers joint angles, but R-square 95%**
[23], 후속 연구 [24]

**R-Square score of 0.921**
[25]

**앞선 연구들 비교분석**
[8]
# **==1.Introduction, ,Literature rewiew==**
본 논문의 쟁점은 feature들간의 상호 작용=> **'attention'** 을 통해 주요 feature 분석

**attention**: 입력 feature들 사이의 **상호 연관성**을 자동으로 파악해 가중치를 다르게 부여하는 메커니즘
**Scaled Dot-Product Attention)**:
$$\text{Attention}(Q, K, V) = \text{softmax} \left( \frac{QK^T}{\sqrt{d_k}} \right)V
$$
- Q: Query (예: 현재 처리 중인 신호)
- K: Key (모든 입력의 대표값)
- V: Value (실제 정보값)
- $d_{k}$​: key의 차원 수

이전 연구들을 보았을 때, machine learning의 경우 feature의 complex pattern을 처리하기가 어렵고, deep learning의 경우 computational cost가 너무 높아 edge device에 적용하기 어렵다.

그래서 본 논문의 가장 큰 주제는
**"designing a signal processing method that levaraged combination of -==feature engineering== and ==deep learning=="**

**Feature engineering**
time domain (MAV, VAR, RMS), frequency domain (MNP, MNF, MDF) 사용
**Transformer model**
위 feature들 간의 관계성(relevance)를 응용하기 위해 transformer model 사용.

# **==3. Experimental setup==**
NinaPro dataset DB 5 사용 (double mayo-band(16channels), 22개의 손가락 관절 각도, 23가지 movement)
본 논문에선 Metacarpophalangeal(mcp, 손바닥과 첫번째 손가락 마디 사이의 관절)

encoder transformer model base

**data processing and filtering**
windowing의 경우, overlap 없이 그냔 data 단위로 잘라서 처리.(예를 들어 200hz sampling ratio에 200 point 단위로 처리할 경우, 1s마다 output 생성). 여러 window를 설정하여 최적의 width 탐색 (25, 50, 100, 200)

Band pass filter 15~25Hz 사용 (emg의 주 주파수 대역은 20~150hz이나, 가장 많은 feature가 담긴 부분만 사용하기 위해 bandwidth를 좁게 설정)

신호 전 처리 후 DWT를 적용 (EMD에 비해 저주파 영역에 대한 정보 처리 용이함) 하여 D4, A4 구간의 데이터를 활용 하여 feature extraction

**Feature extraction**
Time domain features: Mean Absolute Value (MAV), Variance (VAR), Root Mean Square (RMS) 
Frequency domain features: Mean Power (MNP), Mean Frequency (MNF), median frequency (MDF)
6가지의 feaure중 어떤 것이 가장 어울리는지

**Learning architecture**
Transformer (feature attention)+MLP (regression) 기반 학습 모델. FFNN term에서 2 layer의 convolution 적용(이 부분은 이상함. 그냥 FFNN적용한다 생각하면 될듯). 주요 파라미터는 논문 확인

**한 윈도 내 22개 토큰(특징들) 사이의 상호작용을 학습**
측정 데이터 (Batch size, 22, 1024)로 나온다고 생각,
batch size: total data point/data point window
features: 22
tokens: 1024(multi head number 4 x head size 256)
window 별로 Multihead attention(MHA)적용->FFNN적용(22,1024->22,3->22,1024)

Transformer block 이후, Gap 적용 후 2 layer MLP적용 (22->128->22)  

# **==4. Result and discussion==**
**windowing size**
data point=100 일 때 r-square 값(0.97)이 가장 크게 나옴
window size가 작을 수록 mse값이 낮게 나옴.
아무래도 데이터 수가 많아질 수록 평균 에러값이 낮아지는게 아닌가 하는 생각

**Feature extraction**
MDF에서 가장 높은 R-squared(0.97) 값과 상대적으로 낮은 에러값(996)이 나왔음.

**Execution time**
데이터 처리에 걸리는 시간, MNP가 가장 빠른 속도를 보여준다

