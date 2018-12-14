---
title: Linux相关
date: 2018-12-8 18:44:17
tags: linux
toc: true
---

linux相关的一些记录


<!--more-->


1.压缩相关

1> tar.gz
解压：
```
tar -zxvf file.tar.gz
```

压缩：
```
tar -zcvf file.tar.gz file1 file2
```

2> tar
解压：
```
tar -xvf flie.tar
```

压缩：
```
tar -cvf file.tar file1 file2
```

3> zip:
解压：
```
unzip file.zip
```

压缩：
```
zip -r file.zip file -r
```
zip [参数] [压缩后的文件名] [被压缩文件名] -r表示递归

4> tar.bz2
解压：
```
tar -jxvf file.tar.bz2
```

压缩：
```
tar -jcvf file.tar.bz2 file1 file2
```

2.centos中文乱码

```
vim /etc/locale.conf
LANG=“zh_CN.UTF-8”
```

3.ssh连接自动断开
timed out waiting for input: auto-logout
Connection closing...Socket close.

```
vim /etc/profile
#export TMOUT=300
export TMOUT=0
```

4.软连接建立

```
ln -s /root/anaconda3/envs/py36/bin/python3.6 python
```
ln –s 源文件 目标文件

5.硬链接建立

```
ln /root/anaconda3/envs/py36/bin/python3.6 python
```




