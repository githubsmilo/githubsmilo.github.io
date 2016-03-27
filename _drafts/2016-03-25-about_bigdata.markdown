---
layout: post
title:  "About BigData"
date:   2016-03-25
comments : true
categories:
- blog
permalink: About BigData
---

## 계속 정리 중입니다!

### 정리 필요
* https://kimws.wordpress.com/2012/04/22/%EB%B9%85%EB%8D%B0%EC%9D%B4%ED%84%B0big-data%EC%97%90-%EA%B4%80%ED%95%9C-%EC%9D%B4%EB%9F%AC%EC%A0%80%EB%9F%AC%ED%95%9C-%EC%83%9D%EA%B0%81%EB%93%A4/
* https://kimws.wordpress.com/2012/02/11/%EB%B9%85%EB%8D%B0%EC%9D%B4%ED%84%B0big-data%EC%9D%98-%EB%B6%88%ED%8E%B8%ED%95%9C-%EC%A7%84%EC%8B%A4/
* https://ko.wikipedia.org/wiki/%EC%8A%A4%EC%B9%BC%EB%9D%BC_(%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%96%B8%EC%96%B4)
* http://grouplens.org/datasets/movielens/
* http://spark.apache.org/examples.html
* http://engineering.vcnc.co.kr/2015/05/data-analysis-with-spark/
* http://blog.secmem.org/224

### 찾아볼 것들
* 순차 분석?
* Evaluating Machine Learning Models 책 찾아보기
* Neautral Networks가 뭔지?
* [실버 에그](http://www.silveregg.co.jp) 사이트 확인
* column-base or row-base db 확인
* Data warehouse가 아니라 이제는 Data lake!

### 전체 구조
![HDP2.1](http://img.deusm.com/informationweek/2014/06/1278892/Hortonworks-Meets-Spark.jpg)

### RDD (resilient distributed datasets)
* In Spark, we express our computation through operations on distributed collections that are automatically parallelized across the cluster. These collections are called resilient distributed datasets, or RDDs. RDDs are Spark’s fundamental abstraction for distributed data and computation.
* RDDs offer two types of operations: transformations and actions. Transformations construct a new RDD from a previous one. Actions, on the other hand, compute a result based on an RDD, and either return it to the driver program or save it to an external storage system (e.g., HDFS).

### HDFS (Hadoop Distributed File System)
* Spark + S3 + R3를 이용한 데이터 분석 시스템 만들기 : https://speakerdeck.com/vcnc/spark-plus-s3-plus-r3-eul-iyonghan-deiteo-bunseog-siseutem-mandeulgi

### ZooKeeper
* 코디네이션 서비스 시스템 (coordination service)
* Zookeeper provides a distributed configuration service, a synchronization service and a naming registry for distributed systems. It is used by many daemons (including YARN) to manage their peers in a multiple node setup for high availability.
* 분산된 시스템간의 정보를 어떻게 공유할것이고, 클러스터에 있는 서버들의 상태를 체크할 필요가 있으며 또한, 분산된 서버들간에 동기화를 위한 락(lock)을 처리
* http://bcho.tistory.com/1016

### Spark
* Apache Spark is a fast and general-purpose cluster computing system. It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including Spark SQL for SQL and structured data processing, MLlib for machine learning, GraphX for graph processing, and Spark Streaming.
* 문서 : http://spark.apache.org/docs/latest/
* MLlib
* If you wish to use the Python API you will also need a Python interpreter (version 2.6 or newer). Spark does not yet work with Python 3.
* Spark Application
  * At a high level, every Spark application consists of a driver program that launches various parallel operations on a cluster. The driver program contains your application’s main function and defines distributed datasets on the cluster, then applies operations to them. In the preceding examples, the driver program was the Spark shell itself, and you could just type in the operations you wanted to run. Driver programs access Spark through a SparkContext object, which represents a connection to a computing cluster. In the shell, a SparkContext is automatically created for you as the variable called sc .

### YARN
* Yet-Another-Resource-Negotiator
* YARN is the new Map Reduce daemon (MRv1) and it's primary job is to take jobs and run them in the Hadoop cluster. So it primarily farms out and manages cluster work load. 
* http://blog.cloudera.com/blog/2014/05/how-apache-hadoop-yarn-ha-works/

### Scala
* 스칼라 학교 : https://twitter.github.io/scala_school/ko/
* sbt (Simple Build Tool)
  * sbt is a build tool for Scala, Java, and more. It requires Java 1.6 or later.
  * 다운로드 : http://www.scala-sbt.org/download.html
  * 문서 : http://www.scala-sbt.org/0.13/docs/sbt-reference.pdf
* 기타
  * Scala Web Framework : Play Framework

### D3.js
![](http://cfile29.uf.tistory.com/image/1159003A5170FF5328EA35)

* [https://d3js.org](https://d3js.org)
* [https://github.com/mbostock/d3/wiki/Gallery](https://github.com/mbostock/d3/wiki/Gallery)
* D3.js is a JavaScript library for manipulating documents based on data. D3 helps you bring data to life using HTML, SVG, and CSS. D3’s emphasis on web standards gives you the full capabilities of modern browsers without tying yourself to a proprietary framework, combining powerful visualization components and a data-driven approach to DOM manipulation.
* [D3 Workshop](https://bost.ocks.org/mike/d3/workshop/#0)
  * Visualizing Data with Web Standards (HTML, SVG, CSS, javascript, CSV, JSON). D3 provides transformation, no representation.
  * Visualization requires visual encoding: mapping data to elements.
  * The name "D3" refers to the W3C Document Object Model.
* references
  * [D3.js 배우는 방법](http://mobicon.tistory.com/275)
  * [D3.js 기초 - select API와 enter() 이해하기](http://blog.nacyot.com/articles/2015-02-02-d3-selection)
  * [D3.js 시작하기](http://webframeworks.kr/getstarted/d3js/)
