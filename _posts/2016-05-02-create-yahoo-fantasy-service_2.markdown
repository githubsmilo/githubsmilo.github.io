---
layout: post
title:  "나만의 Yahoo Fantasy 서비스를 만들어 보자 (2)"
date:   2016-05-02
comments : true
categories:
- fantasy
permalink: 나만의 Yahoo Fantasy 서비스를 만들어 보자 (2)
---

이전 내용은 여기서 볼 수 있다.

* [나만의 Yahoo Fantasy 서비스를 만들어 보자 (1)](http://githubsmilo.github.io/%EB%82%98%EB%A7%8C%EC%9D%98%20Yahoo%20Fantasy%20%EC%84%9C%EB%B9%84%EC%8A%A4%EB%A5%BC%20%EB%A7%8C%EB%93%A4%EC%96%B4%20%EB%B3%B4%EC%9E%90%20(1))

### 서비스 컨셉

구상하고 있는 서비스에 여러 기능을 넣을 수 있겠지만 일단 'add/drop transaction을 미리 등록해 두고, 로스터 날짜가 변경될 때 자동으로 실행하는 기능' 하나에만 집중하기로 했다.

### 이미 존재하는 서비스 일까?

우선 내가 생각하는 서비스와 비슷한 형태로 누군가 만들지 않았을까 하는 생각에 구글링을 해봤다. 판타지에 빠진 미국 덕후들이라면 분명 이런 서비스를 만들었으리라 생각했는데 왠걸, 하나도 없다. 검색어를 여러 차례 바꿔서 검색해봐도 없다. 없으면 내가 만들어야지! 원래 필요한 사람이, 급한 사람이 만드는 법이다.

### 필요한 Yahoo API가 제공될까?

야후에서 제공하는 Fantasy Sports API를 조사해 봤다. 깔끔하게 정리 되어 있는 [공식 문서](https://developer.yahoo.com/fantasysports/guide)를 살펴보니 필요한 아래 API들이 모두 제공 된다.

* Add player
* Drop player
* Add/drop players
* Get team roster (선택)

이정도만 있으면 충분히 만들 수 있다고 판단 했다. 당장 시작하자.

### 대략적인 형태 설계

이런 저런 형태의 프로토타입을 계속 시도했다. 처음에는 Python 스크립트로 짜서 서버에서 crontab으로 돌리는 형태가 가장 쉽겠다 싶어서 시도해 보았으나 GitHub 등에서 찾을 수 있는 Yahoo OAuth Python 라이브러리 중 제대로 동작하는게 없었다. 지금 OAuth 라이브러리를 직접 구현하기에는 부담이 크고, 개발 타겟이 그쪽이 아니기에 다른 방향을 찾아보기로 했다.

Yahoo 공식 문서에서 가이드 하는 OAuth는 php로 만들어져 있는데, 왜 그런지 모르겠지만 php는 정이 안가서 사용하지 않기로 했다. 계속 검색을 하다가 문득 든 생각이, 다른 사람들도 사용할 수 있게끔 웹 서비스를 제공해주면 어떨까? 괜찮겠다 싶어서 평소에 관심 있었던 분야의 공부도 할겸 node.js 쪽을 파기 시작했다. 그러던 중 [yfsapi](https://github.com/whatadewitt/yfsapi) 이라는 쓸만한 node.js module을 발견했다. Yahoo Fantasy API를 사용하기 쉽게 wrapping을 제공하는 녀석인데, OAuth 1.0도 제공하고, 테스트 결과 정상적으로 동작 한다. 다만, 내가 필요한 Yahoo API를 wrapping 해놓지 않아서 코드 기여를 좀 해야 할 것 같았다.

대략적인 설계가 완성 되고 있다. 앞이 조금씩 보이기 시작하니 즐거워진다. back-end는 node.js로 구현하고, 그 위에서 yfsapi를 사용하여 Yahoo API를 호출하여 필요한 데이터를 가져오거나 전달한다. 사용자 정보 등은 mongodb를 사용하여 저장한다. front-end는 아직 정해지지 않았지만 최대한 간단히 만들고 싶었다.

### 삽질을 시작하다

역시 삽질이 빠질 수 없다. [yfsapi](https://github.com/whatadewitt/yfsapi) node.js module을 어떻게 하면 사용할 수 있을까 둘러보다가 yfsapi에서 OAuth 1.0 대신 OAuth 2.0을 사용하는게 어떻겠냐는 [글](https://github.com/whatadewitt/yfsapi/issues/2)을 봤다. Yahoo Fantasy API에서 OAuth 2.0을 공식적으로 지원하지 않지만 https 주소로 쿼리를 날리면 성공한다는 내용이었다. 그러게? 왜 OAuth 2.0을 사용하지 않지? 내가 시도해 보기로 했다. 삽질 시작..

먼저 yfsapi에서 OAuth 1.0을 제거한 node.js module, [yfsapi-without-auth](https://github.com/githubsmilo/yfsapi-without-auth)를 만들었다. 이걸 [npm](https://www.npmjs.com/package/yahoo-fantasy-without-auth)에 처음으로 올려봤는데, 등록하고 내려받는 방법이 너무 쉬워서 깜짝 놀랐다. 이래서 npm이 엄청난 인기를 끌고 있구나 하며 감탄사가 나오더라. 곧이어 OAuth 2.0과 yfsapi-without-auth을 사용해서 Yahoo Fantasy API를 테스트 할 수 있는 [node.js sample application](https://github.com/githubsmilo/yfsapi-oauth2-test-sandbox)을 만들었다. 테스트 해보니 잘 동작한다. 신바람이 나서 [heroku](https://yfsapi-oauth2-test-sandbox.herokuapp.com/)에 올리고, 사이트에 GitHub 리본도 달았다.

### 프로토타입 테스트

올바른 방향인지 모르겠지만 어쨌거나 OAuth 2.0 + yfsapi-without-auth를 사용해서 환경을 구축했고, 곧바로 add/drop 관련 Yahoo API를 wrapping 하는 작업에 돌입했다. 일단 이 서비스가 현실성이 있는지부터 확인하는게 주요 관심사였으니 어떤 OAuth 타입을 사용할지에 대한 결정은 잠시 뒤로 미뤘다.

Yahoo Fantasy 공식 문서를 뒤져가며 별 어려움 없이 구현 완료 했고, 테스트 까지 성공 했다. 그리고 사용자가 등록한 transaction을 자동으로 실행 시키기 위해 [node-cron](https://github.com/ncb000gt/node-cron)을 사용해보기로 했다. 곧바로 당일 오후 4시에 아래와 같이 테스트를 실행하도록 설정해놓고 결과를 기다렸다. 15시 59분 59초에 Kole Calhoun을 버리고 Yasmani Grandal을 애드 하라는 내용이다.

```javascript
new CronJob('59 59 15 * * *', function() {
     yf.transactions.adddrop_players(
      'mlb.l.154',       // leagueKey
      'mlb.l.154.t.7',   // teamKey
      'mlb.p.9098',      // addPlayerKey (Yasmani Grandal)
      'mlb.p.9191',      // dropPlayerKey (Kole Calhoun)
        function(err, data) {
          if (err)
            console.log('[err]' + JSON.stringify(err));
          else
            console.log('[data]' + JSON.stringify(data));
        }
      );
}, null, true, 'Asia/Seoul');
```

결과는 대성공! 오후 4시에 맞춰 손으로 일일이 애드 버튼을 클릭하는 사람들을 제치고 가장 먼저 애드에 성공 했다. 가능성이 보인다.
![](https://github.com/githubsmilo/githubsmilo.github.io/blob/master/_files/yahoo_fantasy_add_4pm_by_app.png?raw=true)
