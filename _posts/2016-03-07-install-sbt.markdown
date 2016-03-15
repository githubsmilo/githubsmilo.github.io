---
layout: post
title:  "Hello, SBT"
date:   2016-03-07
categories:
- blog
permalink: hello-sbt
---
##### 1. SBT 란?
* sbt(simple build tool) is a build tool for Scala, Java, and more. It requires Java 1.6 or later.
* [download](http://www.scala-sbt.org/download.html){:target="_blank"}
* [documentation](http://www.scala-sbt.org/documentation.html){:target="_blank"}

##### 2. SBT 설치
* Ubuntu 15.10 에서 설치 시도
* 설치 과정
{% highlight bash %}
$ echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823
$ sudo apt-get update
$ sudo apt-get install sbt
$ sbt
{% endhighlight %}
* sbt 실행 시 아래 에러가 발생하면
{% highlight bash %}
module not found: org.scala-sbt#sbt;0.13.11
{% endhighlight %}
* ssl certificates 업데이트
{% highlight bash %}
$ sudo apt-get install ssh
$ sudo update-ca-certificates -f
{% endhighlight %}
* sbt 실행해서 모듈 다운로드 시작
{% highlight bash %}
$ sbt
{% endhighlight %}

##### 3. [Hello, world](http://www.scala-sbt.org/0.13/docs/Hello.html){:target="_blank"}
{% highlight bash %}
$ mkdir hello
$ cd hello
$ echo 'object Hi { def main(args: Array[String]) = println("Hi!") }' > hw.scala
$ sbt
...
> run
...
Hi!
{% endhighlight %}
