---
layout: post
title:  "Ubuntu 15.10에 Zeppelin 0.5.6 설치"
date:   2016-04-11
comments : true
categories:
- bigdata
---

[Ubuntu 15.10에 Spark 1.6.1 설치](http://withsmilo.github.io/Ubuntu%2015.10%EC%97%90%20Spark%201.6.1%20%EC%84%A4%EC%B9%98) 글에 이어지는 내용이다. Zeppelin 0.5.6에 대한 아래 문서를 토대로 진행 하였다.

* [Quick Start - install](https://zeppelin.incubator.apache.org/docs/0.5.6-incubating/install/install.html)
* [Spark Interpreter](https://zeppelin.incubator.apache.org/docs/0.5.6-incubating/interpreter/spark.html)

### 1. [Zeppelin 0.5.6](https://zeppelin.incubator.apache.org/download.html) 다운로드

### 2. 설정 파일 수정

```shell
$ cp conf/zeppelin-env.sh.template conf/zeppelin-env.sh
$ vi conf/zeppelin-env.sh 실행 후 아래 줄 추가
export SPARK_HOME="/home/smilo/Working/BigData/spark-1.6.1-bin-hadoop2.6"
export HADOOP_CONF_DIR="/home/smilo/Working/BigData/hadoop-2.6.4/etc/hadoop"
```

### 3. Zeppelin 시작/종료

```shell
$ ./bin/zeppelin-daemon.sh start
```
```shell
$ ./bin/zeppelin-daemon.sh stop
```

### 4. Interpreter 설정 변경
[web UI](http://localhost:8080/#/interpreter)에 접속하여 interpreter 설정을 아래와 같이 변경 한다.

* master : yarn-client
* spark.cores.max : 8
* spark.executor.memory : 1024m

### 5. 테스트
Zeppelin Tutorial Notebook에서 샘플 테스트를 진행 한다. 정상적으로 동작 한다면 결과가 Notebook에 표시되며, [resource manager web UI](http://localhost:8088)에서 Zeppelin application에 대한 동작 로그를 확인 할 수 있다.

