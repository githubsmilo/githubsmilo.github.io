---
layout: post
title:  "Pandas, Pipelines, and Custom Transformers"
date:   2018-09-07
comments : true
categories:
- python
---
페북에서 우연히 보게된 아래 동영상. PyData Seattle 2017에서 발표한 영상이고, `Pandas, Pipelines, and Custom Transformers` 라는 주제이다.

[![](https://img.youtube.com/vi/BFaadIqWlAg/0.jpg)](https://youtu.be/BFaadIqWlAg)

예전에 Scikit-Learn Pipeline을 공부하면서 이걸 Pandas Dataframe과 붙이면 더 좋겠다고 생각만 했었는데, 이미 누군가 만들었구나. 이 둘의 만남을 처음에 제안한 것은 2014년에서 올라온 [Zac Stewart 블로그](http://zacstewart.com/2014/08/05/pipelines-of-featureunions-of-pipelines.html) 인듯?  암튼 PyData 발표자가 관련 소스를 공개했고, 발표 내용도 직관적이어서 쉽게 이해 할 수 있었다. 짝짝짝!

* main : [https://github.com/jem1031/pandas-pipelines-custom-transformers/blob/master/code/main.py](https://github.com/jem1031/pandas-pipelines-custom-transformers/blob/master/code/main.py)
* custom_transformers : [https://github.com/jem1031/pandas-pipelines-custom-transformers/blob/master/code/custom_transformers.py](https://github.com/jem1031/pandas-pipelines-custom-transformers/blob/master/code/custom_transformers.py)

구글링을 해보니 관련 블로그 포스트를 꽤 찾았다.

* [https://medium.com/bigdatarepublic/integrating-pandas-and-scikit-learn-with-pipelines-f70eb6183696](https://medium.com/bigdatarepublic/integrating-pandas-and-scikit-learn-with-pipelines-f70eb6183696)
* [https://ramhiser.com/post/2018-04-16-building-scikit-learn-pipeline-with-pandas-dataframe/](https://ramhiser.com/post/2018-04-16-building-scikit-learn-pipeline-with-pandas-dataframe/)

내용을 좀더 파보니 preprocessing 부분을 꽤 멋지게 체계화 할 수 있겠다. 회사에서 만들고 있는 추천 모델링에 곧바로 적용하자!
