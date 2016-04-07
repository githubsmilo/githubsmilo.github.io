---
layout: post
title:  "Ubuntu 15.10에 hadoop 2.6.4 설치"
date:   2016-04-06
comments : true
categories:
- blog
permalink: Ubuntu 15.10에 hadoop 2.6.4 설치
---

Ubuntu 15.10에서 hadoop 2.6.4를 설치하는 과정을 간단히 정리했다. PC 한대에서 가상 분산 환경을 만들 생각이기 때문에 [Pseudo-Distributed Mode](http://hadoop.apache.org/docs/r2.6.4/hadoop-project-dist/hadoop-common/SingleCluster.html#Pseudo-Distributed_Operation)로 설정한다.

* Java openJDK 7 설치

```shell
$ sudo apt-get install openjdk-7-jdk
$ sudo update-alternatives --config java
$ javac -version
javac 1.7.0_95
```
* 필요한 패키지 설치

```shell
$ sudo apt-get install ssh rsync
```

* hadoop 2.6.4 [다운로드](http://apache.tt.co.kr/hadoop/common/) 후 적당한 위치에 풀기

* etc/hadoop/hadoop-env.sh 수정

```shell
# Set Hadoop-specific environment variables here.
export JAVA_HOME="/usr/lib/jvm/java-7-openjdk-amd64"
export HADOOP_HOME="/home/smilo/Working/BigData/hadoop-2.6.4"
```

* etc/hadoop/core-site.xml 수정

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

* etc/hadoop/hdfs-site.xml 수정

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

* etc/hadoop/mapred-site.xml 수정 (YARN을 위한 설정)

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

* etc/hadoop/yarn-site.xml 수정 (YARN을 위한 설정)

```xml
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

* passphraseless ssh 설정

```bash
$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
$ cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
$ ssh localhost
```

* filesystem 포멧

```bash
$ bin/hdfs namenode -format
```

* NameNode daemon, DataNode daemon 실행

```bash
$ sbin/start-dfs.sh
```

* ResourceManager daemon, NodeManager daemon 실행

```bash
$ sbin/start-yarn.sh
```

* 동작 확인

```bash
$ bin/hdfs dfs -mkdir -p /user/<username>
$ bin/hdfs dfs -put etc/hadoop input
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.4.jar grep input output 'dfs[a-z.]+'
$ bin/hdfs dfs -cat output/*
6	dfs.audit.logger
4	dfs.class
3	dfs.server.namenode.
2	dfs.period
2	dfs.audit.log.maxfilesize
2	dfs.audit.log.maxbackupindex
1	dfsmetrics.log
1	dfsadmin
1	dfs.servers
1	dfs.replication
1	dfs.file
```

* 유용한 도구
  * namenode web UI (http://localhost:50070)
  * resource manager web UI (http://localhost:8088)
  * 현재 어떤 프로세스가 실행 중인지는 [jps](http://docs.oracle.com/javase/7/docs/technotes/tools/share/jps.html)를 실행하여 확인 가능

* NameNode daemon and DataNode daemon 중지

```bash
$ sbin/stop-dfs.sh
```

* ResourceManager daemon, NodeManager daemon 중지

```bash
$ sbin/stop-yarn.sh
```

* 문제 해결

  * 설치 중 꼬이는 경우가 간혹 발생 하였다. 그런 경우 /tmp/hadoop-\<username\> 아래에 있는 모든 데이터를 삭제 후 재부팅 하면 해결 되었다. core-site.xml에서 hadoop.tmp.dir를 지정 했으면 그 디렉토리를 삭제한다.

