Vivek Gopalakrishnan1,2 and Polina Golland1,2
Clinical Image-based Procedures (CLIP) 2022
# Abstract
대부분의 drr 기법은 imaging parameter에 대한 직관적인 gradient를 제공하지 못한다

Siddon's method (the most popular ray-tracing algorithm used in DRR generation)을 통해 gradient-based optimization과 deeplearning framework 가 상호 작용 되는 DRR generation (as a series of vectorized tensor operations) 개발이 목적

PyTorch 기반으로 개발했으며, CUDA, C++와 동급의 성능을 보임

# Introduction
1. 기존의 cpu기반 drr generator는 drr생성까지 1초나 걸리고, gpu기반으로 0.1초 까진 줄인 연구가 있으나 공개 implementation은 없다.
2. 낮은 수준의 CUDA, C++를 사용하기에 효율적인 미분값 사용이 어렵다=>Gradient-based update가 어렵다

deeplearning 기반 registration 기법은 있으나, 너무 많은 training set 필요 [2],[3]->현실적으로 쓰기 어려움(환자 특화 어려움)
많은 기법들이 gradient-free method (Nelder-mead method)

본 연구에선 Image geometry parameters에 대해 미분이 가능한 fast vectorized DRR renderer 제안. PyTorch 기반,

딥러닝 학습에 사용할 수 있는 differential operator 제공

# Methods
**Siddon's method**

# Discussion
