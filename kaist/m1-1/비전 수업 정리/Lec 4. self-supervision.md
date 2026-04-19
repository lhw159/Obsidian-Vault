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
query, key line이 서로 다름 (비대칭)-> Model Collapse(모델 붕괴) 방지



**SwAV**
![[Pasted image 20260420022441.png]]
**SwAV** 는 "이미지끼리 직접 맞히기 게임을 하는 대신, **이미지를 몇 개의 그룹(Prototypes)으로 나누는 규칙** 을 스스로 만들고, 서로 다른 부분을 보더라도 같은 그룹임을 맞히도록 학습하는 방식



**SimSiam (Simple Siamese) Representation**
![[Pasted image 20260420022900.png|500]]
매우 단순한 버전. simCLR에서 한쪽에 predictor만 두고 한쪽은 stop grad. 


**전체 framework 비교**
![[Pasted image 20260420023026.png|500]]
