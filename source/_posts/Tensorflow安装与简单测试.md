---
title: Tensorflow安装与简单使用
date: 2018-11-27 15:27:57
tags: 机器学习
toc: true
---

![](Tensorflow安装与简单测试/tensorflow-logo.png)


<!--more-->


写在开头：
1.本文时间：2018年11月；为什么要说明时间？因为技术是不断革新的，某些观点和步骤可能
不适合之后的版本。

2.哪些系统可以安装tensorflow？
答：windows、linux、mac均可

3.tensorflow的版本有哪些？
答：有CPU和GPU版本。
CPU因支持指令集不同，可以有不同的版本。如gituhub可下载到的编译好的window版就分为
x86_64/SSE2（普通64位），AVX2（支持avx2指令集，avx2是CPU的扩展指令集）。
GPU也分N卡和A卡。
安装适合机器的版本可以提高计算能力。

4.怎么选择适合版本？
答：有支持tensorflow的显卡就用GPU版.
有支持高级指令集的CPU，就使用高级指令集的。
当然为了安装简单，也可以选择普遍适用的CPU普通版本。

5.怎么查看CPU支持的指令集
答：windows可以使用cpu-z工具，linux可以使用相关命令。

6.tensorflow安装方式有哪些？
答：可以安装编译好的版本，可以从源码编译安装。

7.怎么下载tensorflow版本
答：window可以在gituhub下载较全的GPU和CPU编译版本。
linux可以下载到普通CPU（不支持AVX2等指令）和GPU编译版本。需要特定版本需要编译tensorflow源码安装。
mac......

8.本文以CPU安装为主。

### linux中安装

version：centos7.5（6以上也适用）、anaconda3、python3.6、tensorflow1.2
date：2018/11/26

#### 一、Anaconda安装

1.下载安装
[Anaconda-linux](https://www.anaconda.com/download/#linux)

```
#安装
sh Anaconda3-5.3.1-Linux-x86_64.sh

#测试
conda --version

#若提示 bash: conda: command not found，请设置环境变量，建议使用方法3一劳永逸
#1.设置临时环境变量，仅当前会话生效
export PATH=$PATH:~/anaconda3/bin
#2.设置当前用户环境变量
vim ~/.bash_profile
#添加..，生效..
#3.设置全局环境变量
vim /etc/profile
#添加如下
export PATH=$PATH:~/anaconda3/bin
#生效
source /etc/profile

#conda其他命令
#当前活跃环境下的所有包
conda list
#安装某个包
conda install xxx
```

2.添加Anaconda下载源。

```
#添加清华源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
#设置搜索时显示通道地址
conda config --set show_channel_urls yes
```

#### 二、python安装

安装python环境

```
#新建python3.6环境
conda create --name py36 python=3.6
#激活python3.6环境
source activate py36
#检查python版本是否为当前版本，重要
python -V
#若不同，请检查是否别名占用（Anaconda之前是否已经安装了python，并且绑定了别名）
find / -name "python"
vim ~/.bashrc
#退出python3.6环境
source deactivate
bash
```

tips：
1.可以将Anaconda创建的常用的python环境加到环境变量中，然后就不用activate进入即可使用了。

```
vim /etc/profile
export PATH=$PATH:~/anaconda3/envs/py36/bin
vim ~/.bashrc
alias python='~/anaconda3/envs/py36/bin/python3.6' 
source /etc/profile && source ~/.bashrc
```

2.python程序后台运行

```
nohup python -u /app/files/py/f1.py > /app/files/logs/py.log 2>&1 &
```

#### 三、tensorflow安装

进入Anaconda-python3.6环境

```
source acticate py36
```

若之前没有安装过tensorflow，建议选择直接安装或者下载安装包安装。
若CPU支持额外的指令集，如AVX2，可以选择源码编译，提高计算性能。

1.pip直接安装

```
#cpu版本
pip install tensorflow
```

2.安装包安装
1).[pip官网下载](https://pypi.org/project/tensorflow/#files)
2).[tensorflow-github下载](https://github.com/tensorflow/tensorflow)
3).[tensorflow官网下载](https://www.tensorflow.org/install/pip/)
4).[github搜索](https://github.com/search?utf8=%E2%9C%93&q=tensorflow-linux-wheel&ref=simplesearch)
5).[GoogleAPI查找](https://storage.googleapis.com/tensorflow/)

```
pip install tensorflow-1.12.0-cp36-cp36m-manylinux1_x86_64.whl
```

3.源码编译安装
[源码安装tensorflow](https://)

#### 四、tensorflow测试及错误详解

1.成功后，进入python环境，测试tensorflow 

```
python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
```

2.错误或警告

1).

```
ModuleNotFoundError: No module named 'tensorflow'
```

原因：
若安装无误，疑是找不到tensorflow路径

```
#执行，在anaconda安装目录搜索tensorflow
find /root/anaconda3/ -name "tensorflow*"
#结果
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow-1.12.0.dist-info
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/include/tensorflow
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/include/tensorflow/core/protobuf/tensorflow_server.pb.h
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/core/protobuf/__pycache__/tensorflow_server_pb2.cpython-36.pyc
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/core/protobuf/tensorflow_server_pb2.py
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/contrib/lite/toco/python/__pycache__/tensorflow_wrap_toco.cpython-36.pyc
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/contrib/lite/toco/python/tensorflow_wrap_toco.py
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/contrib/lite/python/interpreter_wrapper/__pycache__/tensorflow_wrap_interpreter_wrapper.cpython-36.pyc
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/contrib/lite/python/interpreter_wrapper/tensorflow_wrap_interpreter_wrapper.py
/root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorboard/_vendor/tensorflow_serving
```

发现tensorflow路径，说明tensorflow安装成功：

```
/root/anaconda3/envs/py36/lib/python3.6/site-packages/
```

查看python版本是否为anaconda激活版本，不是请修改，方法之前已提到。我的原因便是此，不再深究。

```
python -V
```

[非此可参考](http://www.cnblogs.com/yiyezhouming/p/9497697.html)

2).

```
ImportError: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.17' not found (required by /root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/python/_pywrap_tensorflow_internal.so)
```

原因：检查gcc的动态库，缺少“GLIBCXX_3.4.17”

```
#执行
strings /usr/lib64/libstdc++.so.6 | grep GLIBCXX
#结果
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_FORCE_NEW
GLIBCXX_DEBUG_MESSAGE_LENGTH
```

解决：

```
#执行
find / -name "libstdc++.so*"
#结果
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.13-gdb.pyc
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.13-gdb.py
/usr/share/gdb/auto-load/usr/lib64/libstdc++.so.6.0.13-gdb.pyo
/usr/share/gdb/auto-load/usr/lib/libstdc++.so.6.0.13-gdb.pyc
/usr/share/gdb/auto-load/usr/lib/libstdc++.so.6.0.13-gdb.py
/usr/share/gdb/auto-load/usr/lib/libstdc++.so.6.0.13-gdb.pyo
/usr/lib64/libstdc++.so.6
/usr/lib64/libstdc++.so.6.0.13
/root/anaconda3/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6
/root/anaconda3/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so
/root/anaconda3/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6.0.25
/root/anaconda3/envs/py36/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6
/root/anaconda3/envs/py36/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6.0.24
/root/anaconda3/envs/py36/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so
/root/anaconda3/envs/py36/lib/libstdc++.so.6
/root/anaconda3/envs/py36/lib/libstdc++.so.6.0.24
/root/anaconda3/envs/py36/lib/libstdc++.so
/root/anaconda3/envs/py37/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6
/root/anaconda3/envs/py37/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6.0.24
/root/anaconda3/envs/py37/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so
/root/anaconda3/envs/py37/lib/libstdc++.so.6
/root/anaconda3/envs/py37/lib/libstdc++.so.6.0.24
/root/anaconda3/envs/py37/lib/libstdc++.so
/root/anaconda3/lib/libstdc++.so.6
/root/anaconda3/lib/libstdc++.so
/root/anaconda3/lib/libstdc++.so.6.0.25
/root/anaconda3/pkgs/libstdcxx-ng-8.2.0-hdf63c60_1/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6
/root/anaconda3/pkgs/libstdcxx-ng-8.2.0-hdf63c60_1/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so
/root/anaconda3/pkgs/libstdcxx-ng-8.2.0-hdf63c60_1/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6.0.25
/root/anaconda3/pkgs/libstdcxx-ng-8.2.0-hdf63c60_1/lib/libstdc++.so.6
/root/anaconda3/pkgs/libstdcxx-ng-8.2.0-hdf63c60_1/lib/libstdc++.so
/root/anaconda3/pkgs/libstdcxx-ng-8.2.0-hdf63c60_1/lib/libstdc++.so.6.0.25
/root/anaconda3/pkgs/libstdcxx-ng-7.2.0-hdf63c60_3/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6
/root/anaconda3/pkgs/libstdcxx-ng-7.2.0-hdf63c60_3/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so.6.0.24
/root/anaconda3/pkgs/libstdcxx-ng-7.2.0-hdf63c60_3/x86_64-conda_cos6-linux-gnu/sysroot/lib/libstdc++.so
/root/anaconda3/pkgs/libstdcxx-ng-7.2.0-hdf63c60_3/lib/libstdc++.so.6
/root/anaconda3/pkgs/libstdcxx-ng-7.2.0-hdf63c60_3/lib/libstdc++.so.6.0.24
/root/anaconda3/pkgs/libstdcxx-ng-7.2.0-hdf63c60_3/lib/libstdc++.so
#发现/usr/lib64下只有libstdc++.so.6.0.13，而/root/anaconda3/lib/目录下有libstdc++.so.6.0.25
#复制文件，重建libstdc++.so.6软连接
cd /usr/lib64
cp /root/anaconda3/lib/libstdc++.so.6.0.25 ./
mkdir -p ./bak/ && cp libstdc++.so.6 "$_"
rm -f libstdc++.so.6
ln -s libstdc++.so.6.0.25 libstdc++.so.6
#再次验证
strings /usr/lib64/libstdc++.so.6 | grep GLIBCXX
#结果
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_3.4.20
GLIBCXX_3.4.21
GLIBCXX_3.4.22
GLIBCXX_3.4.23
GLIBCXX_3.4.24
GLIBCXX_3.4.25
GLIBCXX_DEBUG_MESSAGE_LENGTH
#成功
```

3).

错误：

```
ImportError: /lib64/libc.so.6: version `GLIBC_2.16' not found (required by /root/anaconda3/envs/py36/lib/python3.6/site-packages/tensorflow/python/_pywrap_tensorflow_internal.so)
```

原因，缺少GLIBC_2.16：继续画瓢

```
#执行
strings /lib64/libc.so.6 | grep GLIBC
#结果
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_PRIVATE
```

解决（需要升级glibc库到2.16以上版本，注意glibc是linux的基础库，几乎所有的库都会依赖于glibc，破坏libc.so.6的软连接会导致几乎所有系统指令无法使用[万一删除请按照末尾方法恢复]）：

glibc版本太高可能需要升级gcc，我直接用yum安装的gcc是4.4.7，无法进行太高版本的编译，而升级gcc又是一个漫长的过程，只专注眼前即可，所以选择较低版本2.17。

```
wget http://mirrors.ustc.edu.cn/gnu/libc/glibc-2.17.tar.gz
tar -zxvf glibc-2.17.tar.gz
mkdir glibc-2.17/build && cd glibc-2.17/build
#configure参数是升级关键，强烈建议不要修改
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
make && make install
#查看是否升级成功
strings /lib64/libc.so.6 | grep GLIBC
```

注意：

```
#如果没有gcc，需要先安装gcc
#我的centos安装时只选择了（）,没有自带gcc。
yum install gcc
```

```
#破坏libc.so.6请执行
LD_PRELOAD=/lib64/libc-2.17.so ln -sf /lib64/libc-2.17.so /lib64/libc.so.6
```


4）.
警告：

```
#Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
#警告提示你的CPU支持AVX2、FMA指令集，而tensorflow版本不支持。
```

解决方法有如下三种：

```
#1.安装AVX2指令集的tensorflow
#2.代码中加入，将tensorflowr日志显示级别调整为2，即不显示这个警告，默认为1
import os
os.environ["TF_CPP_MIN_LOG_LEVEL"] = '2'
#3.可以选择不处理
```


五、tensorflow卸载

```
pip uninstall tensorflow
```


### window中安装

version：window7、python3.6、tensorflow1.1
date：2018/11/06

1.exe安装Anaconda
[Anaconda-windows](https://www.anaconda.com/download/#windows)

2.测试是否安装成功

```
conda --version
```

3.安装python3.6环境

```
conda --versionconda create --name python36 python=3.6
activate python36
```

4.根据CPU（分x86_64和AVX2版本）或GPU下载tensorflow

[tensorflow-windows](https://github.com/fo40225/tensorflow-windows-wheel)

5.pip安装，我下载了CPU的AVX2版本

```
pip install tensorflow-1.11.0-cp36-cp36m-win_amd64.whl
```

6.测试

```
#进入python
pyhon
#键入测试
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
```

7.可能发生的错误或警告

1）

```
pip install --upgrade --ignore-installed tensorflow
#python3.7下pip直接安装tensorflow
#could not find a version that satisfies the requirement tensorflow
#由于当时pip库里还没有适合python3.7的tensorflow版本，后续可能不会有此问题；若有此，请从github地址下载安装
```

2）

```
#Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2
#警告提示你的CPU支持AVX2指令集，而tensorflow版本不支持。使用pip install --upgrade --ignore-installed tensorflow会默认安装x86_64版本
#这个警告也可以选择不处理
#卸载tensorflow，下载安装AVX2版本
pip uninstall tensorflow
pip list
```


