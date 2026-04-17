**Detect Single Object**
![[Pasted image 20260417102446.png|400]]
만약 detection 대상 물체가 많다면?

### **Detecting Multiple Objects**
**sliding window**
![[Pasted image 20260417102546.png|400]]
각 pixel별로 최소 1x1, 최대 HxW크기의 박스를 sliding하며 detection-> 말도 안되는 양의 메모리 필요

**Region Proposal (RP)**
Object가 있을 만한 곳을 제안
Ex) Selective Search -> RGB기준 비슷한 영역 묶어서 RP, CPU, 한번에 2000개

**R-CNN**
![[Pasted image 20260417103331.png|400]]
Selective search로 찾은 이미지 영역들에 대해 CNN을 적용하여 Detection 하는 방식


**Non-Max Suppression(NMS)**
R-CNN에서 box가 겹친다면? -> score가 높은 순으로 box를 나열한 후, IoU가 일정 값 이상의 박스들은 삭제해버리는 방식 
![[Pasted image 20260417105918.png|300]]
IoU=> intersection over union, (intersection (GT,RP)/전체 uninon(GT+RP))

**Mean Average Precision (mAP)**
NMS이후, box를 score 순으로 두고 estimated box와 Ground-truth box사이 비교하며 (IoU값 설정) Precision과 Recall 검증. 모든 object를 detect 성공하면 presicion=1이 되고, 높은 score 에서 모두 탐지가 되면 recall도 1. 그럼 mAP=1. IoU 값은 낮추면 mAP는 오르더라도 신뢰도 떨어짐. IoU값 높이면 mAP는 떨어져도 신뢰도 오름.
![[Pasted image 20260417110833.png|300]]


**Fast R-CNN**
기존의 R-CNN은 너무 느리다는 단점이 있음 (2000개의 BOX에 대해 CNN을 다 적용해야하니)
Fast R-CNN에선 순서를 다르게. 우선 이미지에 FCN (fully convolutional network)를 적용하여 feature map을 형성 후, 거기에 box를 적용
![[Pasted image 20260417112620.png|400]]
RoI Pooling => box의 4 꼭지점을 일단 pixel 단위에 맞게 변경, 이후 RoI (Region of interest)에 대해 pooling(적당히 잘라 넣는)
![[Pasted image 20260417112923.png|300]]


**Faster R-CNN**
Fast R-CNN은 selective search를 통해 RP를 하는데, 이는 여전히 CPU기반이라 느림.
Faster R-CNN => 전 과정을 GPU기반으로 바꾸자 (End-to-End)
![[Pasted image 20260417123943.png|500]]
RPN (Region Proposal Network)