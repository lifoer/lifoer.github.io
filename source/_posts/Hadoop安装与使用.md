---
title: Hadoop安装与使用
date: 2018-04-04 08:50:52
tags: 大数据
---

![](Haddop安装与使用/hadoop-logo.jpg)


<!--more-->


版本：3.0.1

### 一.环境变量
/etc/profile /root/.bash_profile

```
#jdk
JAVA_HOME=/home/programfiles/jdk1.8
CLASSPATH=.:$JAVA_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:.
export JAVA_HOME CLASSPATH PATH
#hadoop
HADOOP_HOME=/home/programfiles/hadoop
PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export HADOOP_HOME PATH
```
### 二.伪分布式配置

1.配置文件
```
#hadoop-evn.xml
export JAVA_HOME=/home/programfiles/jdk1.8
export HADOOP_HOME=/home/programfiles/hadoop
export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop

#core-site.xml
<configuration> 
<!--namenode的地址-->
<property>
<name>fs.defaultFS</name>
<value>hdfs://host1:9000</value>
</property> 
<!--指定hadoop运行时产生文件的存放目录-->
<property>
<name>hadoop.tmp.dir</name>
<value>/home/programfiles/hadoop/tmp</value>
</property>
</configuration>

#hdfs-site.xml
<configuration> 
<!--指定hdfs保存数据副本的数量，包括自己，默认值是3.如果是伪分布模式，此值是1-->
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<!--设置hdfs的操作权限，false：任何用户都可以在hdfs上操作文件-->
<property>
<name>dfs.permissions</name>
<value>false</value>
</property>
</configuration>

#mapred-site.xml
<configuration>
<!--指定mapreduce运行在yarn上-->
<property> 
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
<!-- 如果不配置mapreduce.admin.user.env和yarn.app.mapreduce.am.env这两个参数mapreduce运行时有可能报错。 -->
<property> 
<name>mapreduce.admin.user.env</name>
<value>HADOOP_MAPRED_HOME=$HADOOP_COMMON_HOME</value>
</property>
<property> 
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=$HADOOP_COMMON_HOME</value>
</property>
</configuration>

#yarn-site.xml
<configuration>
<!--ResouceManager的地址-->
<property> 
<name>yarn.resourcemanager.hostname</name>
<value>host1</value>
</property>
<!--指定reducer取数据的方式是mapreduce_shuffle-->
<property> 
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property> 
</configuration>
```
 2.命令
```
#hadoop版本
hadoop version
#namenode格式化
hadoop namenode -format
#cluster端口号:8088
#dfshealth端口号:9870(3.0之前默认50070)
```
3.启动伪分布式
```
sh start-all.sh
```
启动成功jps查看有5个进程
```
ResourceManager
SecondaryNameNode
NameNode
NodeManager
DataNode
```
关闭伪分布式
```
sh stop-all.sh
```

### 三.错误详解

错误信息
```
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [host1]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
2018-03-30 21:13:00,556 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting resourcemanager
ERROR: Attempting to operate on yarn resourcemanager as root
ERROR: but there is no YARN_RESOURCEMANAGER_USER defined. Aborting operation.
Starting nodemanagers
ERROR: Attempting to operate on yarn nodemanager as root
ERROR: but there is no YARN_NODEMANAGER_USER defined. Aborting operation.
```
解决方式
```
#start-dfs.sh stop-dfs.sh添加
HDFS_DATANODE_USER=root
HDFS_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root

#start-yarn.sh stop-yarn.sh添加
YARN_RESOURCEMANAGER_USER=root
HDFS_DATANODE_SECURE_USER=yarn
YARN_NODEMANAGER_USER=root
```
警告信息
```
WARNING: HADOOP_SECURE_DN_USER has been replaced by HDFS_DATANODE_SECURE_USER. Using value of HADOOP_SECURE_DN_USER.
```
解决方式
```
HADOOP_SECURE_DN_USER改为HDFS_DATANODE_SECURE_USER
```
警告信息
```
WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```
解决方式
```
#log4j.properties添加去掉警告信息 
log4j.logger.org.apache.hadoop.util.NativeCodeLoader=ERROR 
```
错误信息（MapReduce过程报错）
```
Container exited with a non-zero exit code 1. Error file: prelaunch.err.
Last 4096 bytes of prelaunch.err :
Last 4096 bytes of stderr :
错误: 找不到或无法加载主类 org.apache.hadoop.mapreduce.v2.app.MRAppMaster
```
解决方式
```
#mapred-site.xml添加
<property> 
<name>mapreduce.admin.user.env</name>
<value>HADOOP_MAPRED_HOME=$HADOOP_COMMON_HOME</value>
</property>
<property> 
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=$HADOOP_COMMON_HOME</value>
</property>
</configuration>
```
警告信息
```
WARN hdfs.DataStreamer: Caught exception
java.lang.InterruptedException
```
解决方式
```
可以忽略^-^
参见：https://issues.apache.org/jira/browse/HDFS-10429
```

错误信息
```
dfshealth浏览器中操作文件报错
Permission denied: user=dr.who, access=WRITE, inode="/":root:supergroup:drwxr-xr-x
```
解决方式
```
#hdfs-site.xml
<property>
<name>dfs.permissions</name>
<value>false</value>
</property>
</configuration>
```
错误信息（MapReduce过程报错）
```
Container [pid=102334,containerID=container_1522540774647_0019_01_000006] is running beyond virtual memory limits. Current usage: 135.6 MB of 1 GB physical memory used; 2.5 GB of 2.1 GB virtual memory used. Killing container.
```
解决方式
```
#修改hadoop物理内存的大小,默认1G
#mapred-site.xml
<property>
<name>mapreduce.map.memory.mb</name>
<value>2048</value>
</property>
<property>
<name>mapreduce.reduce.memory.mb</name>
<value>2048</value>
</property>
或修改hadoop虚拟内存与hadoop物理内存的比率值，默认2.1
#yarn-site.xml
<property>
<name>yarn.nodemanager.vmem-pmem-ratio</name>
<value>5</value>
</property>
```


