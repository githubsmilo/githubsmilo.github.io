---
layout: post
title:  "Mac OS X 에 Hadoop 2.7.2 설치"
date:   2016-06-01
comments : true
categories:
- bigdata
---

Mac OS X에 Hadoop을 설치할 일이 생겨서 예전에 Ubuntu에 설치할 때 [정리해 놓은 글](http://githubsmilo.github.io/Ubuntu%2015.10%EC%97%90%20Hadoop%202.6.4%20%EC%84%A4%EC%B9%98)을 참고하여 진행해 보았다. 하지만 설치 후 실행시켜 보니 아래 warning이 발생한다.

`Unable to load native-hadoop library for your platform... using builtin-java classes where applicable`

구글링을 해보니 큰 문제가 아니라고 하지만 꺼림직 하기에 아래와 같은 삽질을 조금 해보았다.

### 1. Download [Java 1.7 JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) and install it

### 2. Install protobuf 2.5.0 via Homebrew. Refer to [link](http://stackoverflow.com/a/23760535)

```shell
$ brew tap homebrew/versions
$ brew install protobuf250
$ brew link --force --overwrite protobuf250
$ protoc --version
libprotoc 2.5.0
```

### 3. Install cmake via Homebrew

```shell
$ brew install cmake
```

### 4. Install Hadoop 2.7.2 via Homebrew

```shell
$ brew install hadoop
```

### 5. Update Hadoop's native libraries. Refer to [link](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/NativeLibraries.html)

```shell
$ wget http://apache.tt.co.kr/hadoop/common/hadoop-2.7.2/hadoop-2.7.2-src.tar.gz 
$ tar xvf hadoop-2.7.2-src.tar.gz
$ cd hadoop-2.7.2-src
$ mvn package -Pdist,native -DskipTests -Dtar
$ mv hadoop-dist/target/hadoop-2.7.2/lib /usr/local/Cellar/hadoop/2.7.2/
```

### 6. Open `hadoop-env.sh` and add below variables

```shell
# /usr/local/Cellar/hadoop/2.7.2/libexec/etc/hadoop/hadoop-env.sh

export HADOOP_HOME="/usr/local/Cellar/hadoop/2.7.2"
export HADOOP_OPTS="$HADOOP_OPTS -Djava.library.path=$HADOOP_HOME/lib/native"
```

### 7. Edit `core-site.xml`

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

### 8. Edit `hdfs-site.xml`

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

### 9. Activate `Remote Login` option. Refer to [this link](http://stackoverflow.com/a/6313937)

### 10. Set passphraseless ssh

```shell
$ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
$ cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
$ ssh localhost
```

### 11. Format filesystem

```shell
$ hdfs namenode -format
```

### 12. Execute NameNode daemon, DataNode daemon

```shell
$ /usr/local/Cellar/hadoop/2.7.2/sbin/start-dfs.sh
```

### 13. Stop NameNode daemon, DataNode daemon

```shell
$ /usr/local/Cellar/hadoop/2.7.2/sbin/stop-dfs.sh
```

