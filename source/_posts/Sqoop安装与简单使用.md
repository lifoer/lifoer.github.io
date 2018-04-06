---
title: Sqoop安装与简单使用
date: 2018-04-04 16:04:35
tags: 大数据
---
![](Sqoop安装与简单使用/sqoop-logo.png)


<!--more-->


版本号：均为发文前（2018-04-04）最新版
Hive版本：2.3.2
Hadoop版本：3.0.1
Sqoop1版本：sqoop-1.4.7.bin__hadoop-2.6.0

### 一.环境变量
/etc/profile /root/.bash_profile

```
#sqoop
SQOOP_HOME=/home/programfiles/sqoop
PATH=$PATH:$SQOOP_HOME/bin
export SQOOP_HOME PATH

```
### 二.安装配置

```
#sqoop-env.sh
export HADOOP_COMMON_HOME=/home/programfiles/hadoop
export HADOOP_MAPRED_HOME=/home/programfiles/hadoop
export HIVE_HOME=/home/programfiles/hive
```
### 三.hive表至mysql表

查看sqoop命令

```
sqoop help
```
列出mysql某数据库下的表

```
sqoop list-tables --connect jdbc:mysql://host1:3306/demo?characterEncoding=UTF-8 --username root -password root
```
若有如下警告
#WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
不指定密码，手动输入

```
sqoop list-tables --connect jdbc:mysql://host1:3306/demo?characterEncoding=UTF-8 --username root -P
```
指定配置文件运行

```
sqoop --options-file ./demo.conf
#demo.conf文件添加
list-tables
--connect
jdbc:mysql://host1:3306/demo
--username
root
--password
root
```
### 四.脚本执行

sqoop将hive表导出sqoop表，并按日期打印日志
本demo中hive表名：前一天日期_word_count,eg：20180404_word_count
	           mysql数据库名：wordcount ，表名：wordcount


```
sqoop-start.sh
```
```
#!/bin/bash
clock -s
log_time=`date '+%Y-%m-%d'`
log_file=/home/sh/logs/$log_time
test -d $log_file || mkdir -p $log_file
exec 1>>$log_file/"$log_time".log
exec 2>>$log_file/"$log_time".log
echo "=================================================="
echo "Time： `date '+%Y-%m-%d %H-%M-%S'`" 
echo "Name: sqoop-start.sh"
echo "=================================================="

yesday=`date -d "yesterday" +%y%m%d` 
word_count="$yesday"_word_count

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
```


