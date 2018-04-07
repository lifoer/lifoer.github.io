---
title: Centos中MySQL的安装与配置
date: 2018-04-04 8:07:24
tags: linux
---

Centos中使用yum安装和配置MySQL


<!--more-->


Centos版本：x64_6.5


1.查看系统自带mysql

```
rpm -qa|grep mysql
mysql-libs-5.1.71-1.el6.x86_64
```
2.删除自带mysql

```
rpm -e mysql-libs-5.1.71-1.el6.x86_64
```
若无法删除，则关联删除
```
rpm -e --nodeps mysql-libs-5.1.71-1.el6.x86_64
```
3.yum安装mysql

```
yum install mysql mysql-server mysql-devel -y
```
4.启动mysql服务

```
service mysqld start
```
5.查看mysql版本

```
mysql --version
```
6.yum安装mysql无默认密码，需自行配置

```
service mysqld stop;
mysqld_safe --user=mysql --skip-grant-tables --skip-networking &
```
进入mysql

```
update mysql.user set password=PASSWORD('root') where user='root';
flush privileges;
exit;
service mysqld start;
```
7.配置mysql权限

```
grant all on *.* to root@'%' identified by 'root';
flush privileges;
exit;
```
8.指定mysql编码
/etc/my.cnf

```
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
character_set_server=utf8
```



