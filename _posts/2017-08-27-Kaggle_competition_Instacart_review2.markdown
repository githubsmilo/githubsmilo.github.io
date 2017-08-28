---
layout: post
title:  "Kaggle competition Instacart Review"
date:   2017-08-27
comments : true
categories:
- bigdata
---

[Instacart Kaggle competition](https://www.kaggle.com/c/instacart-market-basket-analysis)이 8월 15일에 끝났는데 그동안 일이 밀려서 이제야 정리 해본다. 막판까지 치열한 경쟁을 펼치며 너무 재미있었고, 덕분에 많은 것을 배울 수 있었던 competition 이었다. 최종 성적만 본다면 Public 6%(166/2623),  Private 14%(354/2623) 이다. 내가 만든 모델이 overfit 되어 있다는 것은 알고 있었지만 이렇게 많이 되어 있을 줄이야! 덕분에 메달권에서도 멀어져서 조금 아쉽다. 어쨌든 느낀 점을 정리해보자.

* Data schema를 살펴보면 마치 DB에서 퍼온 것 처럼 깔끔하게 정리되어 있다. competition을 진행하면서 data cleaning이 잘 되어 있다는 의견을 많이 봤고, 덕분에 cross validation 결과가 최종 evaluation과 비슷했다.
![](https://github.com/withsmilo/withsmilo.github.io/blob/master/_files/kaggle_instacart__data_schema.png?raw=true)

* 많은 사람들이 그랬듯이 나도 (user, product) tuple이 reorder 될 것인지에 대한 single-class classification으로 시작했다. 대략적인 과정은 아래와 같고, 포인트가 되는 부분은 feature engineering, threshold 등이다.
  1. prior set으로 features 생성
  2. training set으로 model 생성
  3. test set에 적용하여 (user, product) 튜플에 대한 probability 얻음
  4. 임의의 threshold 보다 큰 (user, product) 튜플은 reordered 라고 판단

* 최종 형태에 이르기까지 수많은 모델링 삽질이 있었지만 차차 얘기하기로 하고 일단 최종 형태만 올려본다.
  * Reorder model #1 : (user, product) tuple이 reorder 될 것인지에 대한 classification model. 가장 많은 시간을 feature engineering에 쏟았으며, hyperparameter tuning도 꽤 많이 진행한 모델이다.
  * Reorder model #2 : Reorder model #1에서 생성한 probability의 mean, std를 기존 feature에 추가하여 다시 돌린다. ML에서 Stacking 이라고 불리는 기법이지만 아주 약간의 성능 향상만 얻을 수 있었다.
  * None model : 임의의 order에 reorder product가 하나도 없는 경우를 None 이라고 competition에서 정의하는데, 이것을 prediction 하는 classification model. Reorder model의 feature 일부를 사용하여 쉽게 만들 수 있다.
![](https://github.com/withsmilo/withsmilo.github.io/blob/master/_files/kaggle_instacart__final_modeling.png?raw=true)

* competition이 막바지로 달려가던 7월말 무렵까지 threshold를 정하는 부분에 많은 시간을 투자했다. 이 threshold에 따라 하나의 order에 담기는 product 종류가 결정되기 때문에 basket size를 의미한다고 생각했고, 이것을 예측하는 Basket size model을 만들고 있었다. 먼저 Linear regression model로 만들어보니 evaluation이 너무 좋지 않았고, Basket size 0부터 130 까지에 대한 classification model도 시도해봤으나 역시나 결과는 좋지 않았다. 여러 개의 threshold를 적용해보기도 하고.. 암튼 수많은 삽질의 연속. 그런데 갑자기 내 Public LB score가 급격하게 내려가는 상황이 발생했다. 깜짝 놀라 Discussion을 찾아보다가 알게된 바로 이것, [F1-Score Expectation Maximization](https://www.kaggle.com/c/instacart-market-basket-analysis/discussion/37221)! 간단히 말해 각 order에 대한 F1 score를 최대화 하는 thresold를 최적화 하는 방법이다. 우아..... 이런게 있다니, 내 고민을 한방에 해결! 이에 대한 다양한 반응들.

> There will be a huge shake-up in public-board！

> OMG... maybe I can't defend 1st place any more (당시 1등)

* 이런 것도 해봤다. 최종적으로 훈련된 Reorder model #2의 tree leaves에 담긴 정보를 feature로 뽑아서 다시 Reorder model을 돌리는 방법이다. Facebook에서 이 방법을 사용하여 accuracy를 3% 정도 향상 시켰다는 논문을 보고 시도해 봤는데, 64GB 라는 메모리 한계로 인해 별다른 성능 향상을 얻지 못했고, 훈련 시키는 것도 너무 오래 걸려서 최종적으로는 사용하지 않았다.

* 이런 것도 해봤다. 동료가 RNN을 이용해서 만든 basket size prediction을 threshold에 대체해보니 기존 방식보다 더 나쁜 결과를 얻었다. RNN의 모델링과 성능 문제라는 내부 결론을 냈지만 RNN을 좀더 개선하지 못한게 아쉽다.

* competition 종료가 다가오면서 치열한 순위 결쟁을 벌이고 있는 와중에 또 하나의 대형 사건이 터졌다. 종료 일주일 전 [56등이 자신의 코드를 공개](https://www.kaggle.com/c/instacart-market-basket-analysis/discussion/37697)해 버린 것! approach가 아니라 코드까지 모두 공개한 것은 바람직하지 않다는 의견과, 다른 사람들이 배울 수 있는 기회를 열었다는 점에서 긍정적으로 보는 의견 등 갑론을박이 매우 치열한 가운데, 이 사람의 코드가 순식간에 competition의 baseline이 되었다. 높은 LB score를 얻으려면 이 사람의 코드를 분석하여 무조건 내 것으로 만들어야 하는 풍경이 펼쳐지면서 일주일 간의 HELL이 열렸다. 와우, 나에게 이건 배우기에 더 없이 좋은 기회! 이 사람의 코드를 분석을 하면서 정말 많은 것을 배웠다. pandas, numpy 사용법 부터 새로 정리했고, cross-validation 정의하는 법, 여러 모델들을 어떻게 하면 ensemble 할까에 대한 고민 등. 단기간에 소스 리팩토링을 몇번이나 했는지 모르겠네.

* 막판 시간에 쫓겨 cross-validation 체크를 제대로 하지 못한게 아쉽다. 위 그림과 같이 여러 개의 모델이 섞이는 형태가 되면서 cross-validation을 하기 힘들어지기도 했고, 계속 새로운 feature 및 모델을 구현하느라 시간이 없었다. 가장 아쉬운 부분.

* 그렇게 competition이 끝났고 [상위권의 솔루션이 공개](https://www.kaggle.com/c/instacart-market-basket-analysis/discussion/38130)되기 시작했다. 해커톤 하듯이 일주일을 빡세게 달려서 상위권까지 올라간 사람도 있고, 아래와 같이 할말을 잃게 하는 놀라운 솔루션도 있다. 골리앗과 다윗 처럼 전혀 다른 스타일로 각각 문제를 공략한 것을 보면서 많은 것을 배웠다. 'Special feature를 뽑을 자신이 있으면 ML로 하라, feature 뽑는게 싫으면 DL로 하라, 다만 두 방향 모두 끝에는 고수들이 기다리고 있다.'
  * [2nd place](https://www.kaggle.com/c/instacart-market-basket-analysis/discussion/38143) : 오직 28개의 feature만 사용해서 None model + Reorder model로 문제를 풀었다. 난 100개가 넘는 feature를 사용해서 이정도 밖에 안되는데 겨우 28개라니! feature engineering의 중요성을 다시 한번 깨닫게 해주는 이 분.
  * [3rd place](https://www.kaggle.com/c/instacart-market-basket-analysis/discussion/38097) : 오직 Deep Learning만으로 feature를 자동 생성했다. first-level에 8개의 DL model을, second-level에 GBM 한개와 Feedforward NN 한개를 배치하여 문제를 풀다니 놀라움 그 자체.

* GPU 머신이 competition 말미에 수중에 들어왔다. CPU로 돌릴 때보다 몇배씩 빠른 성능을 체험해보니 GPU 머신 없이는 competition에 참여하면 안되겠구나 하는 생각이 들었다. 다만, CPU로 돌릴 때와 비교해보면 결과의 accuracy가 조금 낮은 편이다. 구글링을 해보니 floating 연산 차이라고 하니 모델을 개발할 때에는 GPU로, 최종 릴리즈 시에는 CPU로 돌리는게 가장 좋다.

* 이번 competition에서 scikit-learn, pandas, numpy에 대한 수련을 제대로 했다. 다음 competition에서는 이것 때문에 헤매는 일이 없겠지?


