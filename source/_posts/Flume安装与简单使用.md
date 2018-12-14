---
title: Flume安装与简单实用
date: 2018-04-04 10:43:37
tags: 大数据
---

![](Flume安装与简单使用/flume-logo.png)


<!--more-->


### 一.Flume启动

1.命令

```
#启动命令
#${FLUNE_HOME}:flume目录 -n:指定agent名称 -f:指定配置文件 -Dflume***打印日志到控制台
${FLUME_HOME}/bin/flume-ng agent -n a1 -f ${FLUME_HOME}/conf/demo.conf - Dflume.root.logger=INFO,console
```

2.脚本

1>启动

```
flume-start.sh
```
```
#!/bin/bash
#flume start
clock -s
name=org.apache.flume.node.Application
count=`ps aux|grep ${name}|grep -v grep|wc -l`
FLUME_HOME=/home/programfiles/flume
#-Dflume.root.logger=INFO,console
while [ $count -eq 0 ]
do
        #-Dflume.root.logger=INFO,console
        nohup ${FLUME_HOME}/bin/flume-ng agent -n a1 -f ${FLUME_HOME}/conf/demo.conf &
        sleep 60
        count=`ps aux|grep ${name}|grep -v grep|wc -l`
done
echo "Flume start success! count="$count
```
2>关闭

```
flume-stop.sh
```
```
#!/bin/bash
#flume stop
clock -s
name=org.apache.flume.node.Application
count=`ps aux|grep ${name}|grep -v grep|wc -l`
while [ $count -gt 0 ]
do
	ids=`ps aux|grep ${name}|grep -v grep|awk '{print $2}'`
	for id in $ids
	do
		kill -9 $id
	done
	sleep 60
	count=`ps aux|grep ${name}|grep -v grep|wc -l`
done
echo "Flume stop! count="$count
```
### 二.Flume指定Sink为HDFS

版本号：均为发文前（2018-04-04）最新版
Flume：1.8.0
Hadoop：3.0.1

1.配置conf文件

```
#指定agent，soruce，sink，channel名称
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#指定输入源为avro类型
a1.sources.r1.type = avro
#指定监听地址，0.0.0.0不做限制
a1.sources.r1.bind =0.0.0.0
#指定监听端口号
a1.sources.r1.port = 33333

#指定输出hdfs系统
a1.sinks.k1.type = hdfs
#采用本地时间戳，不配置日期参数无效
a1.sinks.k1.hdfs.useLocalTimeStamp=true
#hdfs路径
a1.sinks.k1.hdfs.path=hdfs://host1:9000/flume/events/%y-%m-%d
#文件类型
a1.sinks.k1.hdfs.fileType=DataStream
#序列化类型
a1.sinks.k1.hdfs.writeFormat=TEXT
#文件滚动大小,字节,0为不指定
a1.sinks.k1.hdfs.rollSize=0
#日志文件滚动时间，秒
a1.sinks.k1.hdfs.rollInterval=0
#日志文件滚动条数，条
a1.sinks.k1.hdfs.rollCount=1000
#日志文件超时未写入滚动，秒
a1.sinks.k1.hdfs.idleTimeout=600
#日志文件前缀
a1.sinks.k1.hdfs.filePrefix=%Y-%m-%d
#日志文件后缀
a1.sinks.k1.hdfs.fileSuffix=.log
#每条日志换行，flase不换
a1.sinks.k1.serializer.appendNewline = false

#设置通道为内存
a1.channels.c1.type = memory
#设置内存最大存储日志条数
a1.channels.c1.capacity = 10000
#设置channel每次提交日志数量
a1.channels.c1.transactionCapacity = 1000

#为source和sink绑定channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

```
2.缺少jar包导致的错误，需导入flume/lib目录

```
[ERROR - org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:146)] Failed to start agent because dependencies were not found in classpath. Error follows.
java.lang.NoClassDefFoundError: org/apache/hadoop/io/SequenceFile$CompressionType

缺少：hadoop-common-3.0.1.jar
来源：hadoop/share/hadoop/common

[ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:447)] process failed
java.lang.NoClassDefFoundError: com/ctc/wstx/io/InputBootstrapper

缺少：woodstox-core-5.0.3.jar
来源：hadoop/share/hadoop/common/lib

[ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:447)] process failed
java.lang.NoClassDefFoundError: org/codehaus/stax2/XMLInputFactory2

缺少：stax2-api-3.1.4.jar
来源：hadoop/share/hadoop/common/lib

[ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:447)] process failed
java.lang.NoClassDefFoundError: org/apache/commons/configuration2/Configuration

缺少：commons-configuration2-2.1.1.jar
来源：hadoop/share/hadoop/common/lib

[ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:447)] process failed
java.lang.NoClassDefFoundError: org/apache/hadoop/util/PlatformName

缺少：hadoop-auth-3.0.1.jar 
来源：hadoop/share/hadoop/common/lib


[ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:447)] process failed
java.lang.NoClassDefFoundError: org/apache/htrace/core/Tracer$Builder

缺少：htrace-core4-4.1.0-incubating.jar 
来源：hadoop/share/hadoop/common/lib

[WARN - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:443)] HDFS IO error
org.apache.hadoop.fs.UnsupportedFileSystemException: No FileSystem for scheme "hdfs"

缺少：hadoop-hdfs-3.0.1.jar 
	  hadoop-hdfs-client-3.0.1.jar 
来源：hadoop/share/hadoop/hdfs

others：
查询：http://grepcode.com/search
```




