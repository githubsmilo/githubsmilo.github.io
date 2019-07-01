---
layout: post
title:  "2 years of Developing Personalized Real-Time Recommendation Service Based on Machine Learning"
date:   2019-07-24
comments : true
categories:
- python
---

머신 러닝 기반 실시간 개인화 추천 서비스를 만들기 시작한지 거의 2년이 되어 가네요. 추천 자체 보다는 기본적인 사항들 위주로 회고 내용을 간략히 정리해봤습니다. 비슷한 서비스를 만드는 분들께 조금이나마 도움이 되면 좋겠습니다.

### 머신 러닝 기반 서비스를 만들기 위한 기본 요소

크게 세 가지로 나뉩니다.
* 데이터 : 필요한 데이터를 탐색하고 사용하기 편하도록 전처리 하여 저장
* 모델링 : 서비스에서 필요한 머신 러닝 모델 구현
* 시스템 : 데이터 파이프라인 및 서빙 인프라, 모니터링 도구 개발

### 기본 요소 간 관계 및 팀 구성

서비스를 만들 때 클라이언트와 서버로 나누고 서로 API를 통해 소통하는 구조가 일반적이지만, 머신 러닝 기반 서비스의 경우 데이터, 모델링, 시스템 세 요소가 서로 매우 의존적 이기 때문에 ‘A가 데이터 하고, B가 모델링 하고, C가 시스템을 맡자’ 는 식으로 역할을 세분화 하기 애매한 경우가 많습니다. 그래서 이런 컨베이어 벨트 식이 아니라 소수의 개발자들이 각자 데이터, 모델링, 시스템, 서비스 배포까지 모두 맡아서 full stack 형태로 개발하는 방식이 좋은 것 같아요. 하지만 이런 개발자는 유니콘과 같은 존재라서 찾기 매우 힘들기 때문에 어쩔 수 없이 여러 명이 파트를 나눠 진행하게 됩니다. 이때 multi-role을 맡을 수 있는 사람이 많을수록 소통 비용이 줄어들어 개발 속도가 탄력이 붙게 됩니다. 그리고 일정 단계에 이르면 그 단계를 뛰어 넘기 위해 각 분야의 specialist가 필요해요.

분명한 점은, 제대로 된 머신 러닝 서비스를 만들기 위해서는 데이터, 모델링, 시스템을 함께 발전시켜야 한다는 것이고, 방법에 대해서는 많은 고민이 필요한 것 같아요.

### 머신 러닝 기반 추천 서비스 개발 시 중요한 점

1. iteration & learning을 얼마나 빠르게 할 수 있느냐? 하는 것입니다. 특히 추천 분야의 경우 정답이 없는게 대부분이기 때문에 내가 세운 가설(데이터 + 모델링)에 대한 feedback을 빠르게 얻을 수 있어야 다른 방식을 시도해 볼 수 있으니 매우 중요한 포인트 같아요. 이를 위해 A/B Test, MAB(Multi-Armed Bandit) 등의 시스템이 뒷받침 되어야 하구요.
2. 머신 러닝 기반 추천 서비스를 오픈 하는 것 보다 유지보수 하는게 훨씬 어렵습니다. 외부 데이터에 의존성이 있기 때문에 CACE(change anything, change everything)를 그대로 따라갑니다. 보기에는 그럴싸한 말일지 몰라도, 실제로 당해보면 이것 만큼 무시무시한 말이 없습니다.

### 실시간 추천 서비스

저희는 추천 목록을 미리 만들어두는 형태가 아니라 사용자 요청이 들어올 때마다 모델로부터 실시간으로 추천 목록을 생성하는 real-time serving을 하고 있습니다. 추천 모델을 Tensorflow 로만 만드는게 아니라 Scikit-learn, Gensim, Keras, Pytorch, Spark ML 등으로 다양하게 만들 수 있기 때문에 서빙 시스템에서 이것을 모두 지원 할 수 있는 시스템이 필요했고, 혼자 이 모든걸 감당 할 수 없기에 [Clipper](http://clipper.ai/) 라는 오픈 소스를 사용해서 실시간 추천 서비스를 만들어 오픈했습니다. (Clipper 버그가 좀 있어서 꾸준히 contribution 하다보니 어느새 maintainer가 된건 함정) real-time serving 방식은 장단점이 있는데요.
* 장점
	* 추천 목록 저장소가 없어도 됩니다. 유지 보수 비용 절감은 보너스.
	* 드물게 접속하는 유저에 대한 추천 목록까지 모두 만들어 둘 필요가 없습니다.
	* user action에 따른 추천 목록을 실시간으로 생성 할 수 있습니다.
* 단점
	* model inference time을 SLO(service level objective)에 맞추기 위해 모델을 튜닝해야 합니다.
	* 필요 없거나 importance가 떨어지는 feature를 버릴 수 있도록 feature engineering이 필요합니다.
	* feature 정보를 저장하고 실시간 접근 가능한 저장소가 필요합니다.

### 좋은 추천 모델을 만들기 위한 출발점

도메인 지식이 가장 중요합니다. 모든 데이터를 때려넣고 딥러닝을 돌리자! 라고 하는건 현실적으로 불가능하고, 도메인 지식을 가지고 모델 목적에 맞게 전처리 한 데이터가 정말 중요해요.

### Automatic Model Selection

새로운 모델을 생성 했을 때 현재 서빙 중인 모델과 비교해서 어떤 것이 더 좋은 모델인지 평가하는 과정을 자동화 하는 것인데요. 유의미한 test set을 생성하는게 꽤 어려웠던 기억이 나네요. 그리고 다양한 테스트를 해본 결과, 최신 데이터에 민감한 모델의 경우 새로 만든 모델이 대부분 우위에 있었습니다. 어쩌면 당연한지도 모르겠구요.

### Feedback Loop

사용자가 생성하는 로그 데이터는 현재 서빙 중인 모델에 의해 오염 될 수 밖에 없는데요. 그 데이터로 학습한 모델은 그 전 모델에 biased 되는 현상을 말합니다. 단순한 예를 들어보자면, 이전 모델이 추천한 인기 목록은 다음 모델에 의해서도 인기 목록이 될 확률이 높아져서 추천 목록의 diversity가 떨어지게 됩니다. 추천 서비스를 만들 때 필연적으로 경험하게 되는 현상이고, 추천 품질 개선을 위해 꼭 해결해야 하는 문제인 것 같아요.

### Offline Evaluation & A/B Test

추천 서비스의 경우 정답이 없는게 대부분이기 때문에 offline evaluation이 큰 의미가 없는 것 같습니다. offline evaluation에서 월등한 성능을 보여준 모델이 A/B Test에서 사용자로부터 외면 받는 충격적인 경우도 자주 경험해 봤어요. 잘못된 offline evaluation 설계가 원인이겠지만, 사용자 경험을 정확하게 시뮬레이션 한다는건 정말 어려운 것 같아요. 이런 이유로 Pinterest 에서는 offline evaluation을 sanity check 용으로 사용한다는 글을 본 기억이 나네요.

### Automated ML Pipeline / Serving System

데이터를 전처리 후 모델을 생성하고 배포하는 전 과정을 자동화 했습니다. AWS, GCP, Azure 같은 클라우드를 사용하면 클릭 몇 번으로 끝날 수 있는 일을 수많은 삽질을 거쳐 만들었네요. 요구 사항에 따라 변화해온 시스템 변천사를 한번 정리하면 좋을텐데, 회사 기밀에 묶여 있다는게 좀 아쉽습니다. 가장 많은 신경을 썼고, 지금도 꾸준히 개선하고 있는 부분입니다.

### ML 시스템의 중요성

모델링도 중요하지만 ML 시스템도 매우 중요하다는 것을 보여주는 한 장의 그림. ([출처](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf))
![](https://github.com/withsmilo/withsmilo.github.io/blob/master/_files/real_world_ml_system.png?raw=true)

### Reference

머신 러닝 기반 실시간 개인화 추천 서비스를 만들면서 참고한 논문들 입니다.
* What’s your ML Test Score? A rubric for ML production systems
	* NIPS 2016 Workshop
	* [https://www.eecs.tufts.edu/~dsculley/papers/ml_test_score.pdf](https://www.eecs.tufts.edu/~dsculley/papers/ml_test_score.pdf)
* Rules of Machine Learning: Best Practices for ML Engineering
	* NIPS 2016 Workshop
	* [https://developers.google.com/machine-learning/guides/rules-of-ml/](https://developers.google.com/machine-learning/guides/rules-of-ml/)
* Hidden Technical Debt in Machine Learning Systems
	* NIPS 2015 Workshop
	* [https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)
* Machine Learning: The High-Interest Credit Card of Technical Debt
	* NIPS 2014 Workshop
	* [https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43146.pdf](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43146.pdf)
* Deep Learning Inference in Facebook Data Centers: Characterization, Performance Optimizations and Hardware Implications
	* Nov 24, 2018
	* [https://arxiv.org/pdf/1811.09886.pdf?fbclid=IwAR01SNZtoJII2qiFCi-6e2mjbMFPwLj-R_TVDRXkmX1-xdGzmoKEUQvPguA](https://arxiv.org/pdf/1811.09886.pdf?fbclid=IwAR01SNZtoJII2qiFCi-6e2mjbMFPwLj-R_TVDRXkmX1-xdGzmoKEUQvPguA)
	* [http://sites.ieee.org/scv-cas/files/2018/10/2018Park.pdf?fbclid=IwAR3E0At2PDHwcLzYBzOphyFMHU8rzrbcsJ-SiXeF-YHD4zcmSqvP78k6PVw](http://sites.ieee.org/scv-cas/files/2018/10/2018Park.pdf?fbclid=IwAR3E0At2PDHwcLzYBzOphyFMHU8rzrbcsJ-SiXeF-YHD4zcmSqvP78k6PVw)
* TFX: A TensorFlow-Based Production-Scale Machine Learning Platform
	* KDD 2017
	* [http://stevenwhang.com/tfx_paper.pdf?fbclid=IwAR0IeNu2wXvvDtj8cy7h3oYvl941yF_sRvV_XYEhwt30mhKyZewRYltobMY](http://stevenwhang.com/tfx_paper.pdf?fbclid=IwAR0IeNu2wXvvDtj8cy7h3oYvl941yF_sRvV_XYEhwt30mhKyZewRYltobMY)

