## Wavelet transform
Fouiere Transform(FT)은 전체 함수에 대한 주파수 분석. 문제는 'timing'을 알 수가 없다. 즉 Time domain에 대한 분석이 불가능하다.

위 단점을 보완하고자 나온게 Short Term Fourier Transform(STFT).
STFT는 특정 time window에 대해 FT를 적용하여 특정 시점 마다의 주파수 분석 값을 확인 가능하다. 
하지만 STFT의 경우, 시간 해상도를 높이면 주파수 해상도가 낮아지고, 주파수 해상도를 높이면 시간 해상도가 낮아지는 trade off의 문제점이 있다.

그래서 나온게 Wavelet Transform(WT)
WT는 특정 Wavelet 함수에 대해 'scaling', 'shifting' 라는 주요한 특성을 통해 TD, FD에 대한 분석을 진행한다.
(함수 종류는 다음 링크 참고 https://en.wikipedia.org/wiki/Wavelet#List_of_wavelets , https://www.continuummechanics.org/wavelets.html )

![[signalprocessing.png]]
## Continous Wavelet Trasnform
다음과 같은 식을 통해 분석
$$X_{\omega}(a,b)=\frac{1}{|a|^{1/2}}\int^{\infty}_{\infty}x(t)\psi(\frac{t-b}{a})dt$$
여기서 a는 scaling, b는 shifting 역할을 하며, 기존함수 x(t)에 대해 mother wavelet function $\psi(t)$와의 convolution (정확히는 cross correlation)을 통해 계산
20hz (t<2), 50hz (2<t<4), 100hz (4<t<6) 함수에 대해 Morlet wavelet을 적용하여 CWT를 하면 다음과 같은 결과 나옴
![[Figure_1.png]]
Morlet wavelet의 central frequency는 0.8125 Hz인 점을 감안하면 
$$f(Hz)=\frac{f_{c}}{scale\times fs}=\frac{0.8125}{scale\times 0.001}$$![[cwt_freq.png]]