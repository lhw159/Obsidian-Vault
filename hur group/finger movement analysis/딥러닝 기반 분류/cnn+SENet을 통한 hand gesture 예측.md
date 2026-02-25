---

---

"C:\Users\leehyeonwoo\Desktop\personal\semg\upperlimb\semg손동작구분.pdf"

1d cnn
14 hand gesture
6 channel에 대한 emg 데이터
	1 Between ronator Teres (PT) and Brachioradialis (BR)
	2 Between Extensor carpi radialis brevis (ECRB) and Extensor digitorum
	3 Extensor Carpi Ulnaris (ECU)
	4 Flexor Digitorum Profundus (FDP)
	5 Between Flexor Carpi Ulnaris (FCU) and Flexor Digitorum Superficialis (FDS)
	6 Between Flexor Carpi Radialis (FCR) and Pronator Teres (PT)

-2.2- signal processing
band pass filter 5-250hz
500hz sampled
59-61 notch filter
nomarlization x=(x-x(min))/(x(max)-x(min))
window size 200, 250, 300ms
increment size 10, 50, 100ms
10 times, 5 second, 3  rest times
In this study, we propose a deep learning structure directly using raw sEMG signals to improve the hand gesture recognition and real-time control. => real time control을 위해 feature 추출x

-2.3- classifier
각 채널 별로 따로 2 conv block적용, activation: ReLU, output: 6x256xh 
(Channel-wise independent CNN)
se block, sigmoid, GAP 
2 layer of fc blocks, 20% drop out, soft max, GAP input: 6x256=1536

-2.4- Model Training Setting
categorical corss entropy
ealry stopping(40 epoch 내 차이 없으면)
10e-3 rate
128 batch

-2.5- evaluation study - structure
비교 검증을위해 LC Block(cnn처럼 convolution이지만, weight sharing 안함) 활용

-2.6- evaluation study - Dataset 
multi-channel sEMG recordings 'Nina Pro DB1'사용. 52 gesture, 100HZ


만약 
채널 수: 6
sampling rate: 100 Hz
window size: 200 ms 
increment: 100 ms 
측정 시간: 5초 
=>
✅ 한 채널 데이터: 49 × 20
✅ 전체 데이터: 49 × 20 × 6
=> 
20x6짜리 data set이 49sample로 나오면, 각 sample별 cnn 적용

discussion
SE 블록을 통해 channel-wise attention 역할을 수행하는 구조는 Conv 블록 뒤에 LC layer를 추가하여 딥러닝 층을 깊게 한 구조에 비해 파라미터 수가 7~8배 적어 모델 복잡성과 계산 비용을 줄이면서 성능은 크게 향상되었다.