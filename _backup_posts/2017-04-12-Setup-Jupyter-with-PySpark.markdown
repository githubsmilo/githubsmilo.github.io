---
layout: post
title:  "Setup Jupyter with PySpark"
date:   2017-04-12
comments : true
categories:
- bigdata
---

Mac에 Jupyter + PySpark 환경을 구성하는 과정을 정리 한다. ([참고]( http://stackoverflow.com/a/39181257/1837846 ))

### 1. Install and setup Anaconda
### 2. Install and Setup Spark
1. Download Spark 2.1.0
1. Unzip it to ~/Working/spark-2.1.0-bin-hadoop2.7
1. Edit bash_profile

```shell
$ vi ~/.bash_profile
export SPARK_HOME=~/Working/spark-2.1.0-bin-hadoop2.7
export PATH=$SPARK_HOME/bin:$PATH
```

### 3. Install and setup toree

```shell
$ pip install https://dist.apache.org/repos/dist/dev/incubator/toree/0.2.0/snapshots/dev1/toree-pip/toree-0.2.0.dev1.tar.gz
$ jupyter toree install --spark_home=~/Working/spark-2.1.0-bin-hadoop2.7 --interpreters=PySpark
$ jupyter kernelspec list
```

### 4. Execute Jupyter

```shell
$ jupyter notebook
```