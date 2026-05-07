# Intro
기존의 traditional 한 방법들은 iterative하게 optimizing 하는 방식
=> 몇가지 한계가 있음
1. 계산량이 너무 많고, 느림, 중복 계산 과다
2. objective function이 non-linear parameter에 의존해서 non-convex

**이전 registration 관련 연구 리뷰 논문들**
Several review papers have covered traditional medical image registration methods extensively ([Maintz and Viergever, 1998;Hill et al., 2001; Shams et al.,2010;Fluck et al.,2011;Sotiras et al.,2013;Oliveira and Tavares,2014;Viergever et al.,2016]).

deep-learning based
1. data set의 다양성->(outlier를 지워버리며 평탄화, gradient 평균화)-> loacal minimum을 피할 수 있는 규제 역할
2. pretrained model -> 좋은 초기값(initial estimation) ->global minimum에 갈 수 있게?
3. Forward pass inference → 반복 최적화 불필요 → 속도 ↑
- **초기**: Encoder만 사용 → 특징 추출 / 파라미터 회귀
    
- **U-Net 이후**: Encoder–Decoder → 변형 필드 직접 출력 (Supervised)
    
- **STN 이후**: Encoder–Decoder + Unsupervised → Ground truth 없이 end-to-end 학습


![[Pasted image 20260507140737.png]]
Section 2: offers a brief overview of the fundamentals of learning-based image registration. 

Section 3: explores widely-used loss functions for learning-based registration methods which resemble objective functions in traditional methods, and discusses other novel loss functions enabled by deep learning. 

Section 4: investigates network architectures developed for medical image registration, with a focus on recent developments. 

Section 5: delves into methods for estimat ing registration uncertainty in learning-based registration. 

Section 6: considers appropriate evaluation metrics for learning-based methods and examines methods for quantifying the regularity of generated deformation fields. 

Section 7: provides an enumeration of commonly used public benchmark datasets for 
medical image registration. 

Section 8: summarizes recent applications of learning-based registration in medical imaging. 

Section 9: discusses current challenges and provides future perspectives for deep learning in medical image registration.