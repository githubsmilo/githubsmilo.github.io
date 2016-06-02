---
layout: post
title:  "나만의 Yahoo Fantasy 서비스를 만들어 보자 (3)"
date:   2016-05-03
comments : true
categories:
- fantasy
---

이전 내용은 여기서 볼 수 있다.

* [나만의 Yahoo Fantasy 서비스를 만들어 보자 (1)](http://githubsmilo.github.io/%EB%82%98%EB%A7%8C%EC%9D%98%20Yahoo%20Fantasy%20%EC%84%9C%EB%B9%84%EC%8A%A4%EB%A5%BC%20%EB%A7%8C%EB%93%A4%EC%96%B4%20%EB%B3%B4%EC%9E%90%20(1))
* [나만의 Yahoo Fantasy 서비스를 만들어 보자 (2)](http://githubsmilo.github.io/%EB%82%98%EB%A7%8C%EC%9D%98%20Yahoo%20Fantasy%20%EC%84%9C%EB%B9%84%EC%8A%A4%EB%A5%BC%20%EB%A7%8C%EB%93%A4%EC%96%B4%20%EB%B3%B4%EC%9E%90%20(2))

### 소스 기여

이제 OAuth 1.0을 사용하는 기존의 [yfsapi](https://github.com/whatadewitt/yfsapi) node.js module과, OAuth 2.0을 사용하는 [yfsapi-without-auth](https://github.com/githubsmilo/yfsapi-without-auth) 중에서 어떤 것을 사용할 것인지 결정해야 하는 시간이 다가왔다.

일단 yfsapi에서 OAuth 1.0을 제거하는 [pull request](https://github.com/whatadewitt/yfsapi/pull/5)를 만들어서 yfsapi의 owner는 어떻게 생각하는지 문의 했다. 그리고 이어진 일주일 간의 의견 교환. 난 OAuth 2.0 지원을 위해 yfsapi에서 인증 로직을 분리하고, yfsapi는 사용자로부터 오직 access token만 받자고 제안 했다. 하지만 access token 없이 consumer key와 consumer secret 만으로 game.meta와 같은 public data에 접근 할 수 있으면 좋겠다는 owner의 의견 때문에 pull request는 리젝 되었다. 그러면서 내 의견을 일부 반영한 소스 리팩토링을 진행 중이라고 밝힘에 따라 yfsapi-without-auth 대신 yfsapi에 소스 기여를 하기로 결정 했다. (yfsapi의 리팩토링이 끝나면 변경 사항을 yfsapi-without-auth에 반영할 예정)

쇠뿔도 단김에 빼라고, 곧바로 yfsapi에서 add/drop transactions API를 구현하여 [pull request](https://github.com/whatadewitt/yfsapi/pull/9)를 생성 했다. 이에 화들짝 놀란 owner, 좀더 광범위한 소스 리팩토링을 진행 중이라며 잠시 기다려 달라고 한다. 며칠 후 commit history를 보니 열심히 작업 중이다. 리팩토링이 끝나길 기다리자.

### front-end 고민

이쯤에서 front-end를 어떻게 구현할 것인가에 대해 고민하기 시작했다. 검색을 해보니 이미 node.js, express, mongodb를 사용하고 있다면 angularJS가 가장 편한 선택이라고 한다. 요즘에는 이것들을 묶어서 [MEAN stack](https://en.wikipedia.org/wiki/MEAN_(software_bundle)) 이라고 한다는 글도 봤다. javascript로 full stack을 구현할 수 있으니 장점이긴 하다. 좀더 검색을 해보니 [mean.io](http://mean.io), [mean.js](http://meanjs.org)와 같은 완성형 솔루션이 있길래 테스트를 해봤다. 하지만 만들려는 서비스 크기에 비해 디렉토리 구조가 복잡하고 쓸데 없는 기능들이 덕지덕지 붙어 있다는 생각이 들어 깔끔하게 포기 했다.

그렇다면 요즘 인기가 높은 reactJS는 어떨까? front-end 초보인 나에게는 View만 제공하는 reactJS 보다는 MVC 패턴을 따르는 angularJS가 더 적합하다는 지인의 의견에 reactJS는 다음 기회에 공부해보기로 하고 angularJS를 최종 선택 했다.

*계속 진행 중입니다!*

