**self-supervised learning**: 결국 데이터에 augmentation을 주고, 그걸 복원하면서 데이터 자체 feature 뽑는 법을 학습=> 추후 필요할 때, data-label 셋을 들고와서 finetuning
![[Pasted image 20260420002246.png|400]]
**Pretext Task** : 변형을 준 input을 복원하는 학습
- **Inferring structure (구조 추론):** 흩어진 이미지 조각의 순서를 맞히는 퍼즐 게임입니다. 사물의 전체적인 형태를 알아야 풀 수 있습니다.
    
- **Transformation prediction (변형 예측):** 사진이 몇 도($0^{\circ}$, $90^{\circ}$, $180^{\circ}$, $270^{\circ}$) 회전되었는지 맞히게 합니다. 객체의 상하좌우 개념을 배우게 됩니다.
    
- **Reconstruction (복원):** 지워진 영역을 채우거나(Inpainting), 흑백을 컬러로 바꿉니다. 이미지의 세밀한 질감과 맥락을 학습합니다.
    
- **Exploiting time (시간 활용):** 주로 비디오에서 쓰입니다. 영상의 프레임 순서가 맞는지, 혹은 거꾸로 재생되는지 맞히게 하여 '움직임'과 '인과관계'를 배우게 합니다.
    
- **Multimodal (다중 모달):** 영상의 화면과 소리가 일치하는지 맞히게 합니다. 시각 정보와 청각 정보의 연결 고리를 찾습니다.
    
- **Instance classification (인스턴스 분류):** "이 사진을 아무리 뒤틀고 색을 바꿔도(Augmentation) 결국 같은 사진이다"라는 것을 인식하게 합니다. 최근 가장 강력한 성능을 내는 **Contrastive Learning** 의 핵심입니다.



context prediction
jigsaw puzzles
RotNet
Context Encoders (빈칸 채우기)
Colorization
Contrastive Learning (유사도 판단)


**SimCLR**
![[Pasted image 20260420010246.png|400]]
이미지에 대해 augmnetation을 2개 주어, 서로 positive 관계임을 학습/ 나머지 다른 이미지들은 모두 negative로 학습
![[Pasted image 20260420010346.png|400]]
단점: 이미지를 수만장식 학습할 때마다 써야하니 너무 많은 gpu의 사용

**Memory Bank**
![[Pasted image 20260420011503.png|400]]
이미지에서 추출한 feature들을 memeory bank에 저장해놓고, 학습할 때 feature만 꺼내 쓰는 구조, 그리고 새로 학습한 $x^q$ 의 feature또한 memory bank로 삽입. 
단점: old memory를 학습에 사용하기에 성능 안 떨어질 수 있음

**MoCo (Momentum encoder)**
![[Pasted image 20260420012653.png|400]]
$x^q$랑 $x^k$는 엄연히 다른거고, f_theta를 따라가는 f_psi를 통해 $x^q$와 $x^k$를 tocken화를 하고, $x^k$토큰을 queue에 넣으면서 positive 학습을 하고, 기존 queue에 다른 샘플들과는 negative 학습을하고.

queue안에 feature들을 그대로 사용하는것이기에 과거 feature와 현재 feature의 간극을 줄이는게 중요. 이를 위해서 momentum encoder를 사용 (simCLR은 학습마다 feature를 추출하는 방식이라 그냥 2개의 encoder를 사용)

![[Pasted image 20260420012831.png|500]]


**Practical Consideration**
output projection
![[Pasted image 20260420013840.png|400]]
기존 simCLR에 projection g()를 추가 => f()만으로 형성된 두 벡터를 무조건 같게(positive) 만들면 정보손실 위험이 있기에 중간에 projection 추가.

hyperparameter, data augmentation 부분 보완도 있음


**BYOL (Bootstrap Your Own Latent)**
Negative 없이 학습하자
![[Pasted image 20260420022602.png|500]]
query, key line이 서로 다름 (비대칭)-> Model Collapse(모델 붕괴, 어짜피 다 같은 (positive)니깐 굳이 학습할 필요 없이 대충 파라미터 값 내는 현상) 방지



**SwAV**
![[Pasted image 20260420022441.png]]
**SwAV** 는 "이미지끼리 직접 맞히기 게임을 하는 대신, **이미지를 몇 개의 그룹(Prototypes)으로 나누는 규칙** 을 스스로 만들고, 서로 다른 부분을 보더라도 같은 그룹임을 맞히도록 학습하는 방식



**SimSiam (Simple Siamese) Representation**
![[Pasted image 20260420022900.png|500]]
매우 단순한 버전. simCLR에서 한쪽에 predictor만 두고 한쪽은 stop grad. 


**전체 framework 비교**
![[Pasted image 20260420023026.png|500]]


**Barlow Twins**
![[Pasted image 20260420125720.png|500]]
똑같은 encoder를 통해 임베딩 벡터 $Z^A, Z^B$형성, 둘의 cross correlation을 Identity matrix 형태가 되도록 학습




**DINO**
![[Pasted image 20260420135846.png|500]]
학생과 선생의 관계. 학생의 encoder를 통해 학습을 하고, 선생은 학생의 encoder를 모방하여 정답을 배출. 

centering, sharpening (low temperature) 를 통해 collapse 방지
$$\text{Temperature}: P_i=\frac{\text{exp}(z_i /\tau)}{\sum_j \text{exp}(z_j /\tau)}$$


**BEIT**
![[Pasted image 20260427162808.png|500]]
이미지 복구형 모델. 다만 픽셀 단위 복구가 아닌 'token'을 대상으로 복구
원본이미지를 tokenizer를 통과시켜 token화, 그리고 이미지 패치에 대해 마스킹 처리 후, 마스킹 영역을 토큰을 기준으로 복구.
단순 픽셀은 정보가 풍부하지 않음(주변의 픽셀들이랑 크게 차이 안 나니깐.)=> token화를 통해 고 차원화


**SimMIM**
![[Pasted image 20260427163511.png|500]]
![[Pasted image 20260427163641.png|400]]
기존의 'pixel'단위로 학습하되, 그냥 간단한 구조로.
복잡한 decoder 대신 한층의 'One-later prediction head'사용


**MAE (Masked Autoencoder)**
![[Pasted image 20260427165208.png|500]]

![[Pasted image 20260427171003.png|500]]

'눈에 보이는 부분만 학습하여 주변부를 추정한다'
원본 이미지를 patch단위로 잘라 준 후, 대략 70~80%의 patch를 날려 버린 후(마스크 처리) 살아있는 부분만 encoder에 입력(position embedding, ViT모델 기반)-> encoder에서 나온 token들에 대해, 기존 마스크 된 부분들에 대 한 'mask token' (learnable token)을 삽입한 후, decoder (encoder 대비 가벼운 형태)에 넣어서 기존 이미지 복원

원본데이터를 많이 가리면(정보가 적으면) 모델이 학습을 제대로 못할거라는 고정관념을 깨버린 모델. tight한 상황을 모델에게 제공하여 학습을 강하게.