---
title: Windows和RedHat共享文件
date: 2017-06-13 00:36:08
tags: linux
---

Windows主机和VMware虚拟机中的RedHat通过FTP服务共享文件


<!--more-->


摘要： 在Windows10中，用VM虚拟机安装redhat9.0后，通过安装VMTools和各种尝试后仍然无法共享文件，也无法挂载U盘共享文件，疑是主机、虚拟机、红帽子三个版本的兼容性所致。遂另辟蹊径，通过配置RedHat的FTP服务来达到共享文件的目的。

配置环境：Windows 10、VMware Workstation 12 Pro、RedHat 9.0

一、配置允许root用户ftp登录
1.更改 /etc/vsftpd.user_list和/etc/vsftpd.ftpusers 文件，#注释掉root用户。
2.启动ftp服务：service vsftpd start
3.本地ftp登录：ftp localhost
4.输入root用户，密码，提示sucessful，登录成功。
5.ls查看当前目录，默认登录后在/root目录下。

二、更改ftp登录后的目录
1.更改 /etc/vsftpd/vsftpd.conf文件，添加
local_root=/var/ftp/share
chroot_local_user=YES
anon_root=/var/ftp/share
2.重新启动ftp服务，service vsftpd restart
3.tags: 
local_root=/var/ftp/share 更改root用户默认访问的主目录，路径可随便更改。
anon_root=/var/ftp/share 更改匿名用户默认访问的主目录。
chroot_local_user=YES 将所有用户限制在主目录。如果不做配置，用户可以向上切换到其它目录。

三、配置redhat防火墙
在rehat主菜单-系统设置-安全级别中，添加ftp服务或关闭防火墙。

四、rehat与主机网络互通
1.启动主机的VMnet8虚拟网卡，并用ipconfig/all命令查看主机相关IP地址。
2.虚拟机设置网络连接为桥接模式。
3.打开rehat主菜单-系统设置-网络。
4.设备-双击打开eth0的网卡配置（主意eth0的状态为不活跃），在常规-设置静态ip地址。地址与主机ip在同一网段，子网掩码与主机相同，默认网关为VMnet8的ip地址。
5.DNS-主NDS为VMnet8的ip地址。
6.重启网络服务，service network restart。并查看eth0状态为活跃。
7.确保主机能ping通redhat。

五、主机访问redhat中的文件
在主机地址栏中输入ftp://地址即可访问redhat中的文件。

六、redhat开机自启动ftp服务
1.查看ftp是否自启动，5为在图形界面。
chkconfig --list vsftpd 
显示如下信息(on为启动)：
vsftpd    0:off  1:off  2:off  3:off  4:off  5:off  6:off
2.ftp自启动
chkconfig --level 5 vsftpd on
3.再次查看，ftp已启动。

七、可能用到的命令
查看ftp服务是否启动：service vsftpd status

-2017.06.13
