# **==Transformer==**
encoder의 데이터를 하나의 벡터로 압축하는 과정에서 데이터 손실이 발생하는 seq2seq.
attention mechanism을 통해 위 문제를 보완.
여기서 더 나아가, attention 만으로 인코더와 디코더를 만든 구조가 바로 transformer
![[transformer_structure.png]]
위 이미지 처럼 transformer는 인코더, 디코더가 여러 층으로 이루어지며, 데이터를 한번에 입력. (rnn처럼 step에 따라 순차적으로 시퀀스를 처리하는 방식과는 다름)

==**positional encoding**==
그러면 입력 데이터의 position에 대한 정보가 필요할테고, 이를 위해 positional encoding을 활용
![[transformer_positional_encoding.png]]
$$\begin{aligned}
PE_{(pos,\ 2i)}=sin(pos/10000^{2i/d_{model}}) \\\\ PE_{(pos,2i+1)}=cos(pos/10000^{2i/d_{model}})
\end{aligned}
$$
짝수 index에는 sin 함수, 홀수 index에는 cos 함수 사용 
![[transformer_positional_encoding2.png]]

==**attention and feedforward 신경망**==
![[transformer_attention_overview.png]]
Transformer는 위 그림과 같은 구조를 지님. 왼쪽은 encoder, 오른쪽은 decoder의 구조이며, 각 구조에서 multi-head self attention, position-wise FFNN로 이루어져 있음
![[transformer_self_attention4.png]]
각 self attention의 경우 위와 같은 구조

![[transformer_self_attention.png]]
self attention이란게 결국 입력된 데이터들 끼리 attention 한다고 생각하면 될듯
![[transformer_self_attention2.png]]
위 처럼 가중치 행렬 W를 통해 입력에 대한 Query, Key, Value vector를 얻고,
![[transformer_self_attention3.png]]
위와 같이 행렬 형태(위에서 구한 Q, K, V vector를 concate)로 결합하여 아래 식을 통해 계산(attention mechanism에 나오는 그 식)
$$\text{Attention}(Q, K, V) = \text{softmax} \left( \frac{QK^T}{\sqrt{d_k}} \right)V$$
여기서$d_{k}$는 Q, K 벡터의 크기이며 $d_{k}=d_{model}$/num_head

Attention value의 경우 Query 단위로 softmax 적용

**multihead attention**
위에서 num_head로 나눈 값을 Q, K ,V 의 크기로 사용=> num_head 만큼 쪼개서 병렬로 계산을 통해 효율을 높이고자(다양한 시각에서 attention을 구하고자 하는?)
![[transformer_multihead.png]]
이렇게 구한 ateention head들을 cocate 한 후, 가중치 행렬$W^{o}$를 곱하여 multi-head attention matrix 획득
![[transformer_multihead2.png]]

**FFNN(feed forward 신경망)**
$$FFNN(x) = MAX(0, x{W_{1}} + b_{1}){W_2} + b_2$$
multi-head attetntion을 통해 구한 행렬($x$)에 $W_{1},\,W_{2}$를 곱하여 FFNN진행
![[transformer_FFNN.png]]

어텐션 => "토큰↔토큰 섞기(공간/시퀀스 방향)", 토큰 간의 상호작용
FFNN => "토큰 안에서 채널 섞기(특징 방향) + 비선형성", 토큰 내부 특징 강화


**Residual Connection and Layer Normalization**
![[transformer_residual_connection.png]]
residual term을 활용 ($H(x)=x+multi\,head\; attention(x)$, )

![[transformer_layer_normalization.png]]
위와 같이 attention value의 각 층에 대한 normalization (layer normalization)
$$\hat{x}_{i,k}=\frac{x_{i,k}-\mu_{i}}{\sqrt{\sigma^{2}_{i}+\epsilon}}$$

**Maksed multi-head attention**
decoder의 multi-head attention layer를 보면 'maked'라는 용어를 볼 수 있음. 만약 decoder의 입력 데이터를 그대로 사용한다면, (예를 들어 i want to go home이란 sequence를 넣는다면) 'want'라는 단어를 예측해야 하는 단계에서 그 이후의 입력들( to go home), 즉 미래 값을 예측에 반영해버리는 불상사 발생. 이를 방지하기 위해, 미래 값에 대한 내적값을 엄청 작은 수( ex) -99999999..)로 masking하여 softmax 적용시 0이 나오도록 처리 하는 방식
![[transformer_masking.png]]

![[transformer_masking2.png]]