---
layout: post
title:  "Windows 에서 GitHub Profile 만들기"
date:   2016-03-18
comments : true
categories:
- blog
---

[10분만에 Github Profile 만들기](http://1ambda.github.io/create-github-profile-in-10-minutes) 글을 참고하여 GitHub profile을 만들어본 과정을 간단히 정리한다. Mac, Ubuntu 용 가이드는 있는데, Windows 에서 진행하려니 자잘한 문제가 발생해서 살짝 고생했다.

### 1. 준비
  * [git 다운로드](https://git-scm.com/download/win) 및 설치
  * [python 2.7 다운로드](https://www.python.org/downloads) 및 설치
  * GitHub 설정
    * [SSH 키를 생성 후 GitHub에 등록](https://help.github.com/articles/generating-an-ssh-key)
    * [GitHub access token 생성](https://github.com/settings/tokens/new)
    * 새로운 GitHub repository를 생성한다. 가이드에는 꼭 `oh-my-github`으로 만들라고 하지만 재미 삼아 `my-github-profile`로 만들었다.
  * node-gyp의 경우 Windows에서 제대로 동작하지 않는다. 이를 해결하기 위해서는 [링크](https://github.com/nodejs/node-gyp#installation)를 참조하여 Windows 버전에 맞게 Visual Studio 등을 설치해야 한다.
  * [nvm-windows v1.1.0 다운로드](https://github.com/coreybutler/nvm-windows/releases) 및 설치
    * 설치 경로를 `C:\Users\테스트\AppData\Roaming\nvm` 에서 `C:\nvm` 로 변경 한다. 설치 경로에 한글이 포함 되어 있는 경우 nvm이 제대로 동작하지 않기 때문이다.
  * nodejs 5.0.0 설치 및 설정
{% highlight bash %}
$ nvm install 5.0.0 (Windows 32bit 버전을 사용 한다면 $ nvm install 5.0.0 32)
$ nvm use 5.0.0 (Windows 32bit 버전을 사용 한다면 $ nvm use 5.0.0 32)
{% endhighlight %}

### 2. viewer 설치 및 수정
{% highlight bash %}
$ git clone git@github.com:oh-my-github/viewer.git my-github-profile
$ cd my-github-profile
$ git remote add upstream git@github.com:oh-my-github/viewer.git
$ git remote remove origin
$ git remote add origin git@github.com:githubsmilo/my-github-profile
{% endhighlight %}

Viewer는 기본 설정인 `oh-my-github`에 맞게 되어 있기 때문에 `my-github-profile`을 사용하려면 조금 수정해야 한다. npm으로 이것저것 설치해서 빌드해서 테스트 해보기에는 귀찮으므로 필요한 소스만 조금 수정해서 사용하기로 한다.

* bundle.js.map 수정
{% highlight bash %}
var DEFAULT_REPOSITORY = 'oh-my-github';
=>
var DEFAULT_REPOSITORY = 'my-github-profile';
{% endhighlight %}
* bundle.js 수정
{% highlight bash %}
var n="oh-my-github";t.DEFAULT_REPOSITORY=n
=>
var n="my-github-profile";t.DEFAULT_REPOSITORY=n
{% endhighlight %}

### 3. oh-my-github 설치 및 버그 수정
{% highlight bash %}
$ npm install oh-my-github -g
{% endhighlight %}
`omg init` 시 모듈 경로를 찾지 못하는 버그가 있다. 아래와 같이 수정하자. [리포트 된 버그](https://github.com/oh-my-github/oh-my-github/issues/19) 이므로 조만간 수정 될지도?
{% highlight bash %}
C:\nvm\v5.0.0\node_modules\oh-my-github\dist\generator\file_util.js

- exports.PROJECT_DIR = projectDir;
+ exports.PROJECT_DIR = projectDir + "/node_modules/oh-my-github/";
{% endhighlight %}

### 4. oh-my-github 실행하여 프로필 데이터 생성 후 GitHub에 업로드
{% highlight bash %}
$ cd my-github-profile
$ omg init githubsmilo oh-my-github
$ omg generate [YOUR_ACCESS_TOKEN]
$ git add --all
$ git commit -m "feat: Update Profile"
$ git checkout -b gh-pages
$ git push origin HEAD
{% endhighlight %}

### 5. 확인
[http://githubsmilo.github.io/my-github-profile](http://githubsmilo.github.io/my-github-profile)로 가서 정상적으로 보이는지 확인한다.

