
## Convolutional Neutral Network
AlexNet

ResNet
residual nlock
# Attention
**기존 attention**
![[Pasted image 20260416172218.png|500]]

**image 에 attention 적용. CNN 적용 후 feature map에 대해 attention 적용**
![[Pasted image 20260416172721.png|500]]

**Attention Layer**
![[Pasted image 20260416174742.png|700]]
Nq= # of queries (위 상황에선 Nq=1)
Nx= # of input (3x3이면 9)
Dx= Dimension of input, CNN feature map의 채널 수
Dq= Dimension of query,
Dv= Dimension of value


**Self-attention**
![[Pasted image 20260416192944.png|500]]


**Multi-head Self-Attention**
![[Pasted image 20260416193022.png|500]]
multihead => Input의 화질은 낮추더라도 (split), 보는 관점의 다양성을 높여서, 각 head별 역할 분담을 하는 느낌


# How to use Attention/Transformers for Vision
1. CNN 구조(ResNet 같은) 중간에 self attention block 추가->모델 여전히 cnn

2. Local attention 사용
 ![[Pasted image 20260416193317.png|400]]

3. Standard Transformer on Pixels
![[Pasted image 20260416193431.png|400]]
너무 큰 용량, lose fine detail

4. Standard Transformer on Patches, Vision Transformer ViT
![[Pasted image 20260416193529.png|400]]
CNN적용 없이 Patch 단위의 이미지들을 펼쳐서 Transformer 적용


ViT에 계층적 구조(hierarchical)는 어떻게?
**PVT**
![[Pasted image 20260416193727.png|400]]
이미지 사이즈를 줄이고, 그만큼 채널을 늘리며 resolution 유지


**Swin Transformer** (Shifted Window Attention)
![[Pasted image 20260416194146.png|300]]
쪼갠 window 안에서만 transformer->no communication across windows
->Shifted Window Attention
![[Pasted image 20260416194319.png|300]]