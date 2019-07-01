---
layout: post
title:  "Ubuntu 15.10에 Spark 1.6.1 설치"
date:   2016-04-10
comments : true
categories:
- bigdata
---

[Ubuntu 15.10에 Hadoop 2.6.4 설치](http://withsmilo.github.io/bigdata/2016/04/06/setup-hadoop-2.6.4) 글에 이어지는 내용이다.

### 1. Scala 설치

```shell
$ sudo apt-get install scala
$ scala -version
Scala code runner version 2.11.6 -- Copyright 2002-2013, LAMP/EPFL
```

### 2. [Spark 1.6.1](http://spark.apache.org/downloads.html) 다운로드
이미 Hadoop/YARN을 설치한 상태 이므로 [YARN 위에서 Spark가 동작](http://spark.apache.org/docs/latest/running-on-yarn.html)하도록 설치하자. 이 경우 resource management 등을 YARN에서 담당하므로 따로 master/slave를 설정해줄 필요가 없다. (master/slave를 직접 지정하려면 Spark를 [Standalone mode로 동작](http://spark.apache.org/docs/latest/spark-standalone.html) 하도록 설정)

* [Which daemons are required while setting up spark on yarn cluster?](https://stackoverflow.com/questions/35121498/which-daemons-are-required-while-setting-up-spark-on-yarn-cluster)
* [Spark on yarn concept understanding](http://stackoverflow.com/questions/24909958/spark-on-yarn-concept-understanding)

### 3. Spark 환경 파일 설정

```shell
$ cp conf/spark-env.sh.template  conf/spark-env.sh 
$ vi conf/spark-env.sh 실행 후 아래 줄 추가
HADOOP_CONF_DIR="/home/smilo/Working/BigData/hadoop-2.6.4/etc/hadoop"
```

### 4. YARN 상에서 Spark application 실행

Spark application을 YARN cluster manager 상에서 실행시킬 때 resource management, scheduling, security 등은 YARN이 관리한다. deployment mode로는 cluster, client 두 가지가 존재한다. 아래 내용은 [cloudera](http://www.cloudera.com/documentation/enterprise/5-5-x/topics/cdh_ig_running_spark_on_yarn.html)에서 가져왔다.

Mode | YARN Client Mode | YARN Cluster Mode
-----|------------------|------------------
Driver runs in | Client | ApplicationManager
Requests resources | ApplicationManager | ApplicationManager
Starts executor processes | YARN NodeManager | YARN NodeManager
Persistent services | YARN ResourceManager and NodeManagers | YARN ResourceManager and NodeManagers
Supports Spark Shell | Yes | No

* Cluster deployment mode
  * Spark driver는 cluster host의 ApplicationManger 내부에서 실행 된다. YARN container 내에 존재하는 프로세스는 YARN resource manager에게 resource를 요청하고, application 진행을 담당한다. 해당 application을 실행한 client는 application의 생명 주기에 관여하지 않는다.
  * Cluster mode는 인터렉티브 하게 Spark를 사용하는 용도에는 맞지 않는다.
![](http://www.cloudera.com/documentation/enterprise/5-5-x/images/310x246xspark-yarn-cluster.png.pagespeed.ic.NiGsXTRPJE.png)
  * [Spark Pi](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_spark-quickstart/content/run_spark_pi.html) 예제 테스트 ([코드](https://github.com/apache/spark/blob/master/examples/src/main/scala/org/apache/spark/examples/SparkPi.scala))를 해보자. 결과는 [resource manager web UI](http://localhost:8088)에 접속해서 해당 application id 선택 후 logs 에서 stdout으로 확인 할 수 있다.

```shell
$ ./bin/spark-submit \
        --class org.apache.spark.examples.SparkPi \
        --master yarn \
        --deploy-mode cluster \
        ./lib/spark-examples-1.6.1-hadoop2.6.0.jar \
        10
```
> Pi is roughly 3.14484

* Client deployment mode
  * Spark driver는 application을 실행한 host에서 실행 된다. ApplicationManager는 YARN resource manager에게 resource(executor containers)를 요청만 한다. container가 실행된 후 client는 scheduling을 위해 그 container와 통신 한다.
![](http://www.cloudera.com/documentation/enterprise/5-5-x/images/310x246xspark-yarn-client.png.pagespeed.ic.3zz707T7n-.png)
  * 예제. Cluster mode와 비교해보면 deploy-mode만 client로 변경 하였다. 결과는 resource manager web UI 상에서는 확인 할 수 없고(이유는 잘 모르겠다), console에 출력 된다.

```shell
$ ./bin/spark-submit \
        --class org.apache.spark.examples.SparkPi \
        --master yarn \
        --deploy-mode client \
        ./lib/spark-examples-1.6.1-hadoop2.6.0.jar \
        10
```
> Pi is roughly 3.144148

