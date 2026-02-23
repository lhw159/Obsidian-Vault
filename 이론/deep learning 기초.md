## ==**Back propagation**==

## ==**Overfitting**==
using large scale of data
ragularization
drop out

## ==**Gradient Vanishing**==
ReLU
Weight initialization
batch normalization (rnn구조에선 사용 애매하며 layer normalization 사용)

## ==**API**==
**API**: Application Programming Interface
애플리케이션(스마트폰, 카카오톡 서버 등 서로 다른 프로그램)이 인터페이싱하는(요청과 응답을 주고받는) 체계, 소프트웨어를 사용하는 규칙

**Sequential API**
“층을 위에서 아래로 차례대로 쌓는” 완전연결/Conv 블록 등 **단순한 직렬 모델**에 최적.  
예) 선형 회귀, 간단한 MLP, 간단한 CNN.
**Functional API**
**분기/병합, 다중 입력·출력, Residual/Skip 연결** 같은 **비직렬 구조**가 필요할 때 표준 선택.  
예) U-Net, ResNet, 멀티모달(이미지+수치), Siamese 네트워크, 레이어 공유.
**Subclassing API**
모델의 전파 과정을 **파이썬으로 자유롭게** 짜야 할 때. **동적 길이 루프, 비표준 연산, 강화학습/메타러닝, 그래프 외부 로직** 등.

|항목|Sequential|Functional|Subclassing (`tf.keras.Model`)|
|---|---|---|---|
|구성 방식|층을 **직렬로 쌓기**|**DAG(그래프)** 로 연결|**파이썬 코드**로 `call()` 정의|
|입력/출력|단일 입력·출력(직렬)|**다중 입력/출력**, 분기·병합·스킵 연결 가능|무엇이든 가능(가장 자유로움)|
|레이어 공유|제한적|**쉬움** (한 레이어를 여러 경로에서 공유)|코드로 직접 제어|
|가독성/생산성|**가장 간단**|**표준적 유연성**|가장 복잡(연구·특수 모델)|
|시각화/요약|`plot_model`, `summary()`|`plot_model`, `summary()`|`summary()` 가능(빌드/첫 호출 후), 그래프 시각화 제한|
|직렬화(구성 저장)|`to_json()` 쉬움|`to_json()` 쉬움|**구성 직렬화 어려움** (SavedModel 권장)|
|커스텀 학습 루프|기본 제공(`fit`)|기본 제공(`fit`)|**`train_step` 오버라이드** 등 고급 제어 용이|
|동적 제어 흐름|제한적|제한적|**if/for/while 등 동적 연산 손쉬움**|

## LSTM
![[LSTM.png]]
$$(h_{t}​,c_{t}​)=LSTM(x_{t}​,h_{t−1}​,c_{t−1}​),\quad x_{t}=y_{t-1}
$$
$h_{t}$: hidden state=**바깥으로 내보내는 출력 벡터**. 보통 다음 층/어텐션/분류기로 넘어감.
$c_{t}$: cell state=내부 메모리. 다음 스텝으로만 전달되고 외부에 직접 쓰지 않을 수도 있음.

**다음 스텝은 이전 스텝의 ($h_{t},\,c_{t}$)​와 “다음 입력 벡터”로 진행**하고, 디코더라면 그 “다음 입력”은 보통 **이전 시점의 단어(정답 또는 내가 예측한 것)의 임베딩**

## ==**seq2seq**==
![[seq2seq.png]]

## ==**Attention mechanism**==
decoder의 은닉층과, encoder의 모든 은닉층 사이의 유사성을 통해 다음 단어 예측
어텐션 함수는 주어진 '쿼리(Query)'에 대해서 모든 '키(Key)'와의 유사도를 각각 구합니다. 그리고 구해낸 이 유사도를 가중치로 하여 키와 맵핑되어있는 각각의 '값(Value)'에 반영해줍니다. 그리고 유사도가 반영된 '값(Value)'을 모두 가중합하여 리턴합니다.
### **Luong, Scaled Dot-Product Attention)**:
![[attention_score_dot 1.png]]
$X$ :  input, output의 토큰에 대한 은닉표현들 
$W_{Q}​,W_{K​},W_{V}​$  :  학습되는 **선형 변환(가중치)**.
Q, K, V: 은닉표현, 가중치로 투영하여 **질의/주소/콘텐츠 표현**.
- $Q=XW_{Q},\quad K = XW_{K},\quad V = XW_{V}$ 
- 위 이미지 상에선 $K=h_{1,2,3}, \; Q=S_{t}$. S는 시점 t에서 예측해야하는 단어, h는 인코더에 들어가있는 이전 시점의 은닉 표현들
$$\text{Attention Value}=\text{Attention}(Q, K, V) = \text{softmax} \left( \frac{QK^T}{\sqrt{d_k}} \right)V=\sum_{i}softmax(h_{i}S^{T}_{t})h_{i}
$$
- Q: Query (예: 현재 처리 중인 신호)
- K: Key (모든 입력의 대표값)
- V: Value (실제 정보값)
- $d_{k}$​: key의 차원 수
$$$$
![[attentaion_dot.png]]


### 바다나우 어텐션 함수(Bahdanau Attention Function)
Luong과 다르게 $S_{t-1}$ 은닉층, 즉 decoder의 이전 스텝의 은닉층을 사용하여 attention 계산
$$e^{t}=score(s_{t-1},\,h_{i})=W^{T}_{a}tanh(W_{b}s_{t-1}+W_{c}h_{i})$$
$W_{a},\,W_{b},\,W_{c}$는 가중치 행렬
attention distribution=$softmax(e^{t})$
context vector=$he^{t}$
스텝 t의 input : context vector+t-1스텝의 output
