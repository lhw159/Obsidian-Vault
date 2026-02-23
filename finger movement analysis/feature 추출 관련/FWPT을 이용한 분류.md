**Improved Classiﬁcation Scheme Using Fused  Wavelet Packet Transform Based Features for Intelligent Myoelectric Prostheses**

There exists a limitation to the number of surface electrodes used in EMG signal acquisition due to the constraint of muscle  surface area [5]
전극 개수 제한에 대한 논문?

This mirror creates virtual feedback to the brain that amputated hand is also performing [20], [21].
절단 환자의 forearm muslce activation을 위한 virtual feedback

myoelectric prothesis의 응답시간은 300ms를 max로 생각

# **==1. Introduction==**
EMG는 complex, nonlinear, nonstational 특성을 지님 -> EMG는 위에서 말했듯 시간에 따라 **성질이 급변**하고 **복잡**하기 때문에, 하나의 고정된 주파수 분석(예: FFT)으로는 충분한 정보를 얻기 어려움 -> Wavelet Packet Transform(WPT) 같은 **다중 해상도 분석 도구**(TD-FD 모두 분석)를 사용
단순 TD 분석 => gesture 분류 성능 괜찮으나, 형태나 시간 패턴이 비슷한 동작엔 취약
rms, fft => 정밀한 분석엔 약함
HD-EMG, CNN => 고정밀, 하지만 cost가 너무 높음
# **==2. Protocol for EMG Data Aquisition==**
3명의 절단 환자, 6명의 정상인
총 11개의 동작 (정상인 5~6회 반복, 절단 환자 4~5회 반복)
전극 부착 부위 (8곳): extensor carpi ulnaris, extensor digitorum, extensor carpi radialis longus, brachioradialis, ﬂexor carpi radialis, palmaris longus, triceps, and biceps

세팅 과정
In this article, 8-channel bipolar EMG data  acquisition have been utilized for the arm muscle data recording with 24-b precision of each channel. 
Sampling frequency rate : 2000 Hz
4th-order Butterworth bandpass ﬁlter of 10–500 Hz.
70% v/v alcohol has been used to clean the skin surface. 
The data have been taken in System Level Design Calibration and Testing Lab under the observation of a medical expert and a written consent has been given by each subjects.

절단 환자는 거울을 통해 근육 activate 

Nina Pro data set도 사용(8개 동작)
# **==3. Proposed Feature Extraction Scheme For EMG-PR-Based System ==**
유사한 상지 동작 분류 성능이 좋지 않음, 적은 전극 수에도 정확한 분류를 위해 정보 증강이 필요
=>firing rating (frequency domain), Motor unit recruitment (time domain)으로 구성된 EMG 
->Thus, **both time and frequency information** can be employed in order to **extract meaningful features for EMG-PR-based application** with minimal neural information loss--> **WPT** has been employed in this 
article for making the **feature extraction feasible.**

Wavelet Packet Transform(WPT): 저주파 영역에 대해서만 분해를 계속하는 기존의 DWT와는 달리 고주파 영역에 대해서도 분해 진행.
본 실험의 경우 sampling rate=2000hz에 대해 3 level($2^{3}=8 nodes$, 1000Hz에 대해 125Hz씩 구간 분해)의 WPT 진행.

Mother wavelet으로 biorthogonal wavelet (`bior3.1`) 사용 → EMG waveform과 유사한 구조 때문에 선택

emg data에 대해 200ms(400 samples, 100ms overlap)단위로 WPT 적용, 8 subbands $\times$ 15 features(subband별 15개씩 쪼갠 후 rms? 인듯)=총 120개의 feature 생성.
아래와 같은 식으로 RMS 값 추출
$$ E_{\Omega, K} =log(\sum \frac{W^{T}_{j,k,n}X}{\frac{2^{j}}{N}}) $$
여기서 $W^{T}_{j,k,n}$은 WPT을 통해 추출한 wavelet coefficient.

추출한 120개의 데이터에 대해 3 sequence 이전의 데이터와 함께 퓨전하여 새로운 feature 추출(과거 데이터와의 퓨전). $n_{i}$ (i=1~120)은 현재 데이터,  $m_{i}$ (i=1~120) 는 3 sequnce 이전의 과거 데이터,  $f_{i}$ (i=1~120)는 퓨전 데이터
$$f_{i}=\frac{n_{i}m_{i}}{\sqrt{\sum n^{2}_{i}}\ast \sqrt{\sum m^{2}_{i}}}$$
즉, 한 200ms의 segment에 대해서 120의 RMS 값 생성, 3 sequnse data와 퓨전한 $f_{i}$생성

# **==4.Performance Evaluation==**
3가지 classification algorithms: Linear Discriminant Analysis(LDA), Quadratic Discriminant Analysis (QDA), SVM 사용

cluster에 대해 분할 가능성 performance를 검증하기 위해 Davies_Bouldin(DB) 지표 사용
$$DB=\frac{1}{k}\sum^{k}_{i=0}max(Z_{ij}),\qquad where\;\; Z_{i,j}=\frac{D_{ii}+D_{jj}}{D_{ij}}$$
 $D_{ii}$ and $D_{jj}$ are the dispersions of the $i^{th}$ and $j^{th}$ clusters, respectively, and $D_{ij}$ is the distance between their mean values. DB가 낮을 수록 cluster간 거리가 멀다는 뜻이므로 sepratability 높음