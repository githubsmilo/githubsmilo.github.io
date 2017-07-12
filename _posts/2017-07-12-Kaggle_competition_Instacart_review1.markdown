---
layout: post
title:  "Kaggle competition Instacart 진행 중"
date:   2017-07-12
comments : true
categories:
- bigdata
---

요즘 Kaggle competition에 참여하느라 많은 시간을 투자하고 있다. 데이터를 가지고 이리저리 주물러서 원하는 결과를 만들어내는 분석 과정이 확실히 재미있기도 하고, Kaggle 특유의 랭킹 시스템 덕분에 순위 경쟁하는 재미도 쏠쏠하다. 작년에 서울대 빅데이터 교육을 들으면서 Kaggle을 처음 접한 이후, 올해 초 진행한 [YouTube8M](https://www.kaggle.com/c/youtube8m)에 이어 두번째로 [Instacart Market Basket Analysis](https://www.kaggle.com/c/instacart-market-basket-analysis)에 참여하고 있다.

이번 competition의 목표는 '다양한 물건에 대한 구매 이력이 있는 고객이 다음에는 어떤 물건을 구매할 것인가'에 대해 예측하는 것이다. 현업에서 수요가 굉장히 많은 분야일 것 같아서 팀원과 함께 시작했고, 종료까지 이제 한달 정도가 남은 시점에서 중간 정리를 해봤다. 참고로 현재 상위 8%(109/1393)을 달리고 있지만 테스트 데이터의 33%만 가지고 점수를 매긴거라서 크게 신경쓰진 않는다. 최근에 끝난 Mercedes-Benz competition에서는 final ranking에서 2843위가 5위로 되는 [놀라운 일](https://www.kaggle.com/c/mercedes-benz-greener-manufacturing/discussion/36103)이 벌어진 적도 있으니뭐.

* R로 할까, Python으로 할까 고민하다가 scikit-learn, pandas, numpy를 본격적으로 사용해보고 싶은 욕심과, 사용 경험이 있어서 좀더 친숙한 Python으로 시작 했다. R은 다음에 도전해 보기로... 하지만 최근에 Scala를 너무 많이 사용해서 일까, Python의 dynamic type checking에 적응이 안돼서 "대체 이 녀석은 무슨 타입이냐고!"를 자주 외치는 중. 찾아보니 Python 3.6부터 static type checking 기능이 [추가](https://medium.com/@ageitgey/learn-how-to-use-static-type-checking-in-python-3-6-in-10-minutes-12c86d72677b)됐다. pandas와 궁합이 좋을지 테스트 해보자. 그리고 Python이 call by assignment 라는 것을 잊고 있다가 디버깅을 한참 해야 했던 악몽도 겪어 보고. Scala와 너무 다르다.

* 예전에 잠시 R을 써볼 때 dplyr 패키지에 감동을 받은 적이 있는데, Python에는 비슷한 성격의 모듈이 없는게 좀 아쉽다. (검색해보면 몇개가 나오긴 하지만 개발이 중단 됐거나 버전이 너무 낮아서 섣불리 사용하기 힘듬) pandas가 처음에 접하기에는 참 좋은 녀석이긴 하지만 원하는 형태로 변형하려면 꽤나 머리를 써야 하는 경우가 많고 (R dplyr에서는 엄청 간단한데!), reference에는 기본적인 것인 내용들만 기술되어 있어서 stackoverflow를 끼고 살아야 한다. pandas 어렵다. 흑.

* Instacart competition 문제를 풀기 위해서 처음에 어떻게 접근해야 할지 팀원과 많은 논의가 있었다. 일단 나는 Kaggle discussion에서 main approach로 언급되는 방식, 즉 주어진 문제를 (user, product) tuple이 reordered 될 것인가에 대한 binary classification 문제로 변환하여 접근해보기로 했고, 팀원은 시계열 데이터 라는 것에 초점을 맞춰서 RNN으로 접근해보기로 했다. 나는 CPU가 많은 머신을, 팀원은 GPU가 달린 머신을 할당.

* Python에서 binary classification ML을 돌리려면 선택지가 딱 두개로 좁혀진다. [lightGBM](https://github.com/Microsoft/LightGBM), [xgboost](https://github.com/dmlc/xgboost). 다른 방식도 많겠지만 속도와 정확도 면에서 많은 사람들이 이것들을 추천하고 있고, xgboost의 경우 Kaggle competition 상위권 입상자들이 애용하는 방식으로 알려져 있어서 주저 없이 xgboost 선택.

* cross-validation을 위한 data split 목적으로 처음에는 scikit-learn에서 제공하는 [model_selection.train_test_split](http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)을 사용했다. 하지만 이 녀석은 무조건 random split 할 뿐이고 특정 column의 값을 기준으로 random split 하는 기능을 제공하지 않아서 어쩔 수 없이 data split을 직접 구현 했다. 나중에 여유가 생기면 scitkit-learn에 new feature로 제안해 봐야지. 그리고 [model_selection.GridSearchCV](http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html#sklearn.model_selection.GridSearchCV)도 사용했는데, 동일한 문제로 [model_selection.ParameterGrid](http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.ParameterGrid.html)를 사용하도록 변경 했다.

* evaluation metric을 어떤 것으로 해야 할지 고민 중. Kaggle에서 점수를 매기는 방식이 mean f1 score 라서 evaluation metric 또한 f1 score로 하는 것이 맞다고 생각 했지만 구현하다보니 threshold를 사전에 정해야 한다는 점이 걸린다. 일단 logloss를 사용해서 진행 중.

* Deep Learning이 아니다보니 feature engineering이 굉장히 중요하다. 고민, 또 고민. 하지만 기껏 생각한 것을 구현할 때 pandas 라는 벽에 부딪혀서 진도가 안나가는 경우가 있다. 아, pandas 어려워.

* hyperparameter tuning은 다른 사람들이 한 것을 참고 했다. 퇴근하기 전에 tuning job을 돌려놓고 가면서 조금씩 자체 tuning 중.

* 계속 업데이트 중...

