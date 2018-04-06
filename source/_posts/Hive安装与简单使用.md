---
title: Hive安装与简单使用
date: 2018-04-04 15:08:06
tags: 大数据
---

![](Hive安装与简单使用/hive_logo_medium.jpg)


<!--more-->


版本号：版本号：均为发文前（2018-04-04）最新版
Hive版本：2.3.2
Hadoop版本：3.0.1

### 一.环境变量
/etc/profile /root/.bash_profile

```
#hive
HIVE_HOME=/home/programfiles/hive
PATH=$PATH:$HIVE_HOME/bin:$HIVE_HOME/conf
export HIVE_HOME PATH
```
### 二.安装配置

1.复制配置文

```
cp hive-env.sh.template hive-env.sh
cp hive-default.xml.template hive-site.xml
cp hive-log4j2.properties.template hive-log4j2.properties
cp hive-exec-log4j2.properties.template hive-exec-log4j2.properties
```
2.hdfs创建hive存放目录

```
hadoop fs -mkdir -p /user/hive/warehouse
hadoop fs -mkdir -p /user/hive/tmp
hadoop fs -mkdir -p /user/hive/log
```
3.配置

1>先安装mysql，创建hive元数据库。

```
create database hive character set latin1;
```
2>拷贝mysql-connector-java-5.1.32.jar(其他版本也可)至hive/lib目录

3>修改配置文件

```
#hive-site.xml
#指定元数据库为mysql
<property>
<name>javax.jdo.option.ConnectionURL</name>
<value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;characterEncoding=UTF-8&amp;useSSL=false</value>
</property>
<property>
<name>javax.jdo.option.ConnectionDriverName</name>
<value>com.mysql.jdbc.Driver</value>
</property>
<property>
<name>javax.jdo.option.ConnectionUserName</name>
<value>root</value>
</property>
<property>
<name>javax.jdo.option.ConnectionPassword</name>
<value>root</value>
</property>
#hive表，临时文件，日志存放路径
<property>
<name>hive.exec.scratchdir</name>
<value>/user/hive/tmp</value>
</property>
<property>
<name>hive.metastore.warehouse.dir</name>
<value>/user/hive/warehouse</value>
</property>
<property>
<name>hive.querylog.location</name>
<value>/user/hive/log</value>
</property>

#hive-site.xml
{system:java.io.tmpdir} 改成 /home/programfiles/hive/tmp
{system:user.name} 改成 {user.name}
```
4>初始化元数据库

```
schematool -dbType mysql -initSchema
```
5>键入hive进入hive控制台

### 三、错误详解

错误信息

```
org.apache.hadoop.hive.metastore.HiveMetaException: Failed to get schema version.
Underlying cause: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException : Communications link failure

```
解决方法（mysql中添加权限）

```
grant all on *.* to root@'%' identified by 'root';
flush privileges;
exit;
service mysqld restart
```
错误信息

```
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:For direct MetaStore DB connections, we don't support retries at the client level.)
```
解决方法

删除mysql中hive数据库，指定latin1编码重建，并重新初始化元数据库。

### 四.脚本执行

hive处理表后通过sqoop导出mysql，并按日期打印日志，并加入定时任务。
本demo中hive表名：前一天日期_word_src,前一天日期_word_count, eg：2018-04-04_word_count
			   mysql数据库名：wordcount ，表名：wordcount

1.

```
start-hive.sh
```
```
#!/bin/bash
#hive
. /etc/profile
. /root/.bash_profile
clock -s
log_time=`date '+%Y-%m-%d'`
log_file=/home/sh/logs/$log_time
test -d $log_file || mkdir -p $log_file
exec 1>>$log_file/"$log_time".log
exec 2>>$log_file/"$log_time".log
echo "=================================================="
echo "Time： `date '+%Y-%m-%d %H-%M-%S'`"
echo "Name: hive-start.sh" 
echo "=================================================="

filepath=`date -d "yesterday" +%y-%m-%d` 
yesday=`date -d "yesterday" +%y%m%d` 
word_src="$yesday"_word_src
word_count="$yesday"_word_count

hadoop fs -test -e /flumes/events/"$filepath"
if [ $? -ne 0 ]; then
    echo "Directory is not exists!"
else
	hive<<EOF
use wordcount;
drop table if exists $word_src;
create external table $word_src(ip string,word string,savedate string) row format delimited fields terminated by '\t' stored as textfile location '/flumes/events/$filepath';
drop table if exists $word_count;
create table $word_count(word string,count int,savedate string);
insert into table $word_count select word,count(*) count,savedate from $word_src group by word,savedate order by count desc;
select * from $word_count limit 10;
EOF
	if [ $? -ne 0 ]; then
		echo "Hive execute fail!"
	else 
		echo "Hive execte success!"
		mysql -uroot -proot -e "
create database if not exists wordcount character set utf8;
use wordcount;
create table if not exists wordcount(
	word varchar(100),
	count int,
	savedate varchar(50)
);
"
		sqoop export --connect jdbc:mysql://host1:3306/wordcount?characterEncoding=UTF-8 --username root -password root --table wordcount --export-dir /user/hive/warehouse/wordcount.db/$word_count --input-fields-terminated-by '\001'
		if [ $? -ne 0 ]; then
			echo "Sqoop execute fail!"
		else 
			echo "Sqoop execte success!"	
		fi	
	fi
fi
```
2.定时任务添加

1>设置文件权限

```
chmod 777 hive-start.sh
```
2>定时任务
```
#添加，每天2点0分启动
crontab -e
0 2 * * * /home/sh/hive-start.sh &
#查看
crontab -l
```
3>/etc/profile自己配置的环境变量复制到/root/.bash_profile

shell脚本中添加

```
. /etc/profile
. /root/.bash_profile
```





