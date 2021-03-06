---
layout: post
title:  "나만의 Yahoo Fantasy 서비스를 만들어 보자 (1)"
date:   2016-04-28
comments : true
categories:
- fantasy
---

Yahoo Fantasy 라는게 있다. 주로 MLB, NBA 등 미국 스포츠가 대상이며, 보통 10~15명 정도의 인원이 모여서 하나의 가상 리그를 구성하고, 실제 선수들을 대상으로 가상 드래프트를 진행하여 자신의 팀을 꾸리게 된다. 그리고 그 선수들이 올리는 성적을 토대로 순위 경쟁을 벌이는 team management 게임이다. 내가 데리고 있는 특정 선수가 실제 경기에서 안타를 치면 그게 팀 점수에 반영되기 때문에 어떤 선수들로 로스터를 구성하는지가 매우 중요하다. Yahoo 선수 목록에 등록된 선수 중에서 `(리그 인원 수 * 로스터 선수)`를 제외한 나머지 선수들은 FA 명단에 남게 되는데, 계속 데리고 있을 만큼 좋은 성적을 올려줄 선수는 아니지만 선발/팀 매치업에 따라 특정 날에 좋은 성적을 올릴 가능성이 높은 일종의 '로또 선수'가 존재하기 마련이다. Yahoo Fantasy를 즐기는 사람들의 고민은 바로 여기서 시작된다.

* 어떤 선수가 다음 날에 좋은 성적을 올려줄까?
* 그런 선수가 있다면, 다른 사람들 보다 먼저 add 할 수 없을까?

첫번째 문제는 수많은 야구 통계 데이터를 분석 하다보면 어느 정도 예측 할 수 있는데, 여기서 다루기에는 너무 방대하기 때문에 일단 두번째 문제에 주목해보자. Yahoo Fantasy의 로스터 날짜가 바뀌는 기준은 미국 서부 시간이며, 우리 나라 시간으로는 오후 4시 정각에 바뀐다. 바로 이때, 다음 날 좋은 성적을 올려주길 기대하는 일명 '로또 선수'를 줍기 위한 경쟁이 시작 된다. Yahoo Fantasy를 가볍게 즐기는 사람들이 모인 리그 라면 이런 일이 발생하지 않겠지만, 오덕후들이 모인 리그 라면 아래와 같은 상황이 펼쳐 진다. 오후 4시의 진풍경.

![](https://github.com/withsmilo/withsmilo.github.io/blob/master/_files/yahoo_fantasy_add_4pm.png?raw=true)

이쯤 되면 심각하다. 오후 4시에 맞춰 add를 하기 위해 버튼을 클릭 했는데 이미 다른 팀에 속해 있어서 실패 했다는 메세지를 몇번 보다보면 심각성을 느끼게 된다. 경쟁이 초 단위로 이뤄지는 상황이다. `저 사람들보다 먼저 add 해야 하는데 어떻게 하면 될까?`를 고민했고, 그 고민은 Yahoo Fantasy를 편리하게 즐길 수 있는 재미있는 서비스를 만들어보자는 쪽으로 발전하기 시작했다. 자, 이제부터 그 결과물을 만들어 가는 과정을 조금씩 정리해보기로 한다.
