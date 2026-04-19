![[Pasted image 20260419001938.png]]
### **Semantic Segmentation**
**FCN**
![[Pasted image 20260419001957.png|500]]
이미지 자체를 압축하고 (down sampling) 다시 복구 (up sampling)

**Deep Lab**
![[Pasted image 20260419002220.png|400]]
layer 중간에 dilated conv(kenal filter에 간격 존재, 해상도를 유지하며 넓은 영역에 대한 feature 추출, 다만 계산량 증가)

### Instance Segmentation
detection+segmentation
Top down 방식 (Mask R-CNN, PANet)(detec->seg), 
bottom up 방식 ()??

**Mask R-CNN**
![[Pasted image 20260419004454.png|400]]
Detection 을 하고, box regressor가 추정한 box에 안에서 FCN을 통해 segmentation
![[Pasted image 20260419005148.png|400]]
detection box는 pixel에 맞게 정수배로 안 떨어지는 경우가 많으므로, RoIAlign을 통해 feaeture map 형성(box를 feature map 크기에 맞게 분할, 한 칸 당 임의 4개의 점을 설정, 4개의 각 점 마다 근처 4개의 기존 feature map의 pixel의 feature를 bi interpolation하여 임의 점 점수 설정, MAX pooling)


### Panoptic Segmentation
![[Pasted image 20260419010930.png|500]]
DETR의 구조를 닮은 Panopic segmenation. detected box들을 embedding 하여 transformer 적용

### Mask classification
기존 per pixel (모든 pixel에 대해 K class중 어디에 해당하는지를 검증해야하는 방식) 보다 더 유연한 mask 기반 방식. MASK R-CNN 처럼 별도의 boxing 필요 없이 1 stage end-to-end segmentation 방식. things, stuff 구분없이 전역 segmenation 가능

#### Maskformer
![[Pasted image 20260419022107.png]]
![[Pasted image 20260419194859.png|400]]
##### 1. **Pixel-level module** (좌측 하단: "눈")

이 모듈은 이미지의 공간적 세부 정보(고해상도 특징)를 담당합니다.

- **Backbone:** 입력 이미지에서 기본적인 시각 정보(선, 질감, 색상 등)를 뽑아냅니다.
    
- **Pixel decoder:** Backbone에서 나온 특징을 점진적으로 **업샘플링(Upsampling)** 하여, 원래 이미지와 유사한 고해상도 피처 맵을 만듭니다.
    
- **결과물($\epsilon_{pixel}$):** 이미지의 모든 픽셀이 각각 어떤 특징을 갖는지 나타내는 **"픽셀 단위 임베딩"**이 생성됩니다. 이 정보는 나중에 마스크의 테두리를 정교하게 따는 데 쓰입니다.
    

###### 2. **Transformer module** (좌측 상단: "뇌")

이 모듈은 모델이 찾아야 할 물체의 추상적인 개념(의미적 정보)을 담당합니다.

- **N queries:** 모델이 미리 준비해둔 $N$개의 **Object Queries**입니다. 이 쿼리들은 학습을 통해 "이미지 어딘가에 있는 물체 하나를 담당해"라는 임무를 부여받습니다.
    
- **Transformer decoder:** 쿼리들이 이미지의 전역적인 정보를 훑으며 물체에 대한 정보를 학습합니다.
    
- **결과물:** 각 쿼리는 최종적으로 "내가 찾은 물체는 이런 모양이야"라는 **"마스크 임베딩($\epsilon_{mask}$)"**을 출력합니다.
    

###### 3. **Interaction & Segmentation** (우측: "조합")

여기가 바로 **MaskFormer의 핵심**입니다. 추상적인 쿼리와 구체적인 픽셀이 만나는 지점입니다.

- **Interaction ($\otimes$):** 우측 하단의 곱하기($\otimes$) 기호가 보이시나요? 여기서 **"마스크 임베딩($\epsilon_{mask}$)"**과 **"픽셀 단위 임베딩($\epsilon_{pixel}$)"**을 내적(Dot Product) 합니다.
    - "내가 찾은 물체 특징($\epsilon_{mask}$)과 이 픽셀의 특징($\epsilon_{pixel}$)이 얼마나 일치하는가?"를 계산하는 것입니다.

- **Mask predictions:** 결과적으로 픽셀마다 마스크 임베딩과의 유사도가 계산되어, $N$개의 **Binary Mask**가 튀어나옵니다.
    
- **Classification:** 위쪽의 MLP를 거쳐 각 쿼리가 찾은 물체가 "이건 소야(Cow)", "이건 하늘이야(Sky)"라고 클래스를 예측합니다.



#### Mask2former
![[Pasted image 20260419022542.png|400]]
기존의 maskformer에서 query가 전체 이미지가 아닌 masking 영역만 보도록 강제하는 모델



### SAM
**SAM**
![[Pasted image 20260419024704.png|600]]
image embedding=> 입력한 이미지 인풋
prompt tokens=> 유저가 설정한 box, text, point
output tocken => DETR에서 Query tocken이라고 생각


**SAM2**
![[Pasted image 20260419025235.png|600]]
Memory Bank: 이전 프레임에서 얻은 정보(객체의 특징, 사용자의 프롬프트 등)를 저장해두는 창고
Memory Attention: 현재 프레임을 처리할 때, 메모리 뱅크에 있는 과거 정보를 참조하여 "아까 그 객체가 지금 어디쯤 있겠구나"를 파악
Memory Encoder: 현재 프레임의 예측 결과(마스크)를 다시 메모리 뱅크에 저장할 수 있는 형태로 변환



## Open Vocabulary Semantic Segmentation
![[Pasted image 20260419031916.png|400]]
Clip의 (text 와 image의 관계 학습한 대규모 데이터 셋) text embedding을 semantic segmentation과 결합을 해보자

**Zeg-Former (Two stage Approach)**
![[Pasted image 20260419032931.png|600]]
위 파란 과정은 기존 모델이 학습할 때 본 사물들 (Seen)에 대해서 segmentation, 아래 과정은 이미지에 masking을 적용 후, 그 mask과 clip의 연관성을 통해 segmentation(unseen 도 가능)

단점: 1.픽셀단위가 아닌 이미지 단위로 진행하기에 local detail 학습이 어려움, 2.mask는 clip을 신경 쓰지 않고 형셩되기에 관계학습에서 간극 발생. 


**Mast clip**
![[Pasted image 20260419223118.png|400]]
maskformer를 통해 masking=> mask 부분 crop해서 clip과의 관계
![[Pasted image 20260419223031.png|400]]
mask prompt (P)=> 배경 부분을 지워버리는 역할


## CAT-Seg (Cost aggregation)
![[Pasted image 20260419040403.png|600]]
CLIP을 backbone으로 활용하야 image embedding 후 tex embed와의 유사도를 통해 cost volume 형성 -> cost aggregation(선형적으로)
**cost aggregation**
![[Pasted image 20260419040529.png|300]]
