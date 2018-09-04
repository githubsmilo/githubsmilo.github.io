---
layout: post
title:  "10 Lessons I Wish I Had Known Before Scaling Deep Learning Solutions"
date:   2018-09-03
comments : true
categories:
- python
---

[https://towardsdatascience.com/10-lessons-i-wish-i-had-known-before-scaling-deep-learning-solutions-5d1fc01919db](https://towardsdatascience.com/10-lessons-i-wish-i-had-known-before-scaling-deep-learning-solutions-5d1fc01919db)

경험에서 나오는 insight가 가득히 담겨져 있는 글입니다. Machine learning/Deep learning solution을 도입하거나, 도입 후 고생하는 분들께 큰 도움이 될 것 같아요. 데이터 파이프라인이 메인, 추천 모델링을 서브로 맡고 있는 저로서는 매우 공감가는 글이고, 앞으로 어떻게 발전해야 할지 즐거운 고민거리를 안겨주는 글이네요.

간단히 정리해보면,

#### Lesson #1: Data Scientists Make Horrible Engineers
- data scientists who focused on experimentation
- data science engineers who implement production-ready models
- devops that focus on the automation, deployment and operationalization of data science workflows
- 추천 프로젝트가 진행됨에 따라 저희 팀도 이런 형태로 변화하고 있어요. 서로 간 소통이 매우 긴밀해야 하고, 전체적인 설계와 구현을 동시에 할 수 있는 아키텍처가 있어야 일을 빠르게 풀어나갈 수 있는 것 같아요.

#### Lesson #2: Notebooks Don’t Scale….Wait, Notebooks Do Scale Stupid
- we typically package the code in a notebook into containers that can run and scale as part of a container infrastructure.
- [https://github.com/nteract/papermill](https://github.com/nteract/papermill)
- data scientist에게 필수 아이템인 쥬피터 노트북을 production에서 어떻게 활용 할 수 있는지 항상 고민하고 있는데요. 넷플릭스에서는 그걸 papermill 이라는걸로 멋지게 풀어냈죠. 저도 꼭 시도해보고 싶어요.

#### Lesson #3: The Single Deep Learning Framework Fallacy
- we recommend implementing common aspects of data science workflows such as data cleansing, feature extraction or hyperparameter optimization using a common infrastructure that can work across different frameworks such as TensorFlow, PyTork, Caffe2 and many others.
- 다양한 모델링을 하다보면 공통적인 부분이 생기게 마련이고, 이걸 어떻게 효율적으로 관리할 수 있을지 고민하게 되더군요. 요즘 이 부분을 만드느라 고생 중이에요.

#### Lesson #4: Training is a Continuous Task
- we recommend taking the time to automate the training routines for each deep learning model you create and deploy that on an infrastructure that allows on-demand and scheduled execution.
- 저희 팀은 이 목표를 위해 continuous learning, continuous deployment, real-time serving이 가능한 infra를 만들었어요.

#### Lesson #5: Centralized Training Doesn’t Scale
- [https://github.com/uber/horovod](https://github.com/uber/horovod), a distributed training framework for TensorFlow models.
- Decentralized or federated training architectures is a model we recommend in organizations with a large number of deep learning models
- 분산 딥러닝 프레임워크를 어떤걸 쓸지 계속 고민 중이에요. 기존 파이프라인과 잘 어울리고 빠르게 적용 할 수 있는지가 포인트인 것 같아요.

#### Lesson #6: Feature Extraction can Become a Reusability Nightmare
- A feature store will persist the feature representation of specific input datasets in a way that can be used by other data science models. Key-value databases like Cassandra or DynamoDB are a great fit for the implementation of feature stores.
- 대박 공감 가는 부분 ㅋㅋ 진짜로 악몽을 꾸며 밤잠을 설치게 됩니다.

#### Lesson #7: Everyone Wants a Different Version of Your Model
- Model versioning is an important element of any data science strategy. We have found AutoML to be an incredibly effective framework for creating self-service variations of specific models.
- [https://github.com/salesforce/TransmogrifAI](https://github.com/salesforce/TransmogrifAI)
- model versioning은 매우 중요하다고 생각하지만 최적화 까지 고려하면 설계가 쉽지 않은 부분 같아요. TransmogrifAI는 처음 들어보는데, 스터디를 해봐야 겠군요.

#### Lesson #8: Cloud-Heavens, On-Premise Hells
- Building a highly scalable computation cluster using technologies such as Apache Spark or Flink is a recommended step to enable the execution of deep learning workflows on-premise.
- 회사에서 AWS를 전격 도입하자고 하면 팀장님한테 혼나겠죠?

#### Lesson #9: Regularization & Optimization are a Must
- Techniques such as model scoring and hyperparameters optimizations should be implemented from day one.
- 모델을 뿅~ 하고 만드는 것 보다 꾸준히 관리해주고 미처 생각하지 못했던 비즈니스 문제들을 해결하는게 더 어려운 것 같아요.

#### Lesson #10: Different Models Require Different Execution Patterns
- we recommend considering three execution patterns for deep learning models: on-demand(APIs), scheduled and publish-subscribe.
- Building the right infrastructure to enable deep learning workflows to execute on those three modalities can save a lot of headaches down the road.
- 요건 당연한 얘기. 모델의 성격에 따라 실행 패턴을 다르게 가져가야 합니다.

