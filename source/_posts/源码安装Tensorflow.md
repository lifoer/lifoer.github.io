---
title: 源码安装Tensorflow
date: 2018-12-06 17:57:12
tags: 机器学习
toc: true
---

源码安装可以根据机器配置安装最优版，最大化利用硬件计算能力。


<!--more-->


接上文[tensorflow安装](http://lifoer.github.io/2018/11/27/Tensorflow%E5%AE%89%E8%A3%85%E4%B8%8E%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8/)，若从未安装过，建议先使用安装包安装，后续扩展。
本文以CPU版本为主，解决AVX2等指令集支持问题。

上文环境：centos7.5、anaconda3、python3.6
date：2018/12/6

一、bazel安装

1.jdk8安装(bazel依赖jdk8)

```
tar -zxvf jdk-8u181-linux-x64.tar.gz
```

```
vim /etc/profile
```

```
#jdk1.8
export JAVA_HOME=/app/files/software/jdk1.8
export CLASS_PATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```


2.bazel安装

bazel官网：
https://github.com/bazelbuild/bazel/releases
https://docs.bazel.build/versions/master/install.html

1>yum引导
```
 yum install bazel
```

2>下载安装
```
sh bazel-0.20.0-installer-linux-x86_64.sh
```
3>查看
```
bazel version
```

二、tensorflow编译

1.安装编译依赖库

 numpy、pip、wheel

后两项若使用Anaconda安装python方式，默认已安装。

查看当前已安装库 

```
#进入python3.6环境
source activate py36
#列出当前环境已安装包
conda list
```

安装numpy

```
conda install numpy
```

3.编译并生成安装文件

1>下载最新tensorflow源码
https://github.com/tensorflow/tensorflow

2>配置编译选项

```
cd tensorflow-master/
./configure
```


```
WARNING: The following rc files are no longer being read, please transfer their contents or import their path into one of the standard rc files:
/app/files/packages/tensorflow-master/tools/bazel.rc
WARNING: --batch mode is deprecated. Please instead explicitly shut down your Bazel server using the command "bazel shutdown".
INFO: Invocation ID: bb5cea33-fbf5-4ca2-bc76-ba7eb0188adc
You have bazel 0.20.0 installed.

#python地址
Please specify the location of python. [Default is /root/anaconda3/envs/py36/bin/python]
enter

#python库地址
Found possible Python library paths:
/root/anaconda3/envs/py36/lib/python3.6/site-packages  
Please input the desired Python library path to use.Default is [/root/anaconda3/envs/py36/lib/python3.6/site-packages]
enter

#是否支持 XLA(Accelerated Linear Algebra/加速线性代数)、JIT（Just in Time/即时编译）
Do you wish to build TensorFlow with XLA JIT support? 
n
No XLA JIT support will be enabled for TensorFlow.

#是否支持 OpenCL SYCL
Do you wish to build TensorFlow with OpenCL SYCL support?
n
No OpenCL SYCL support will be enabled for TensorFlow.

#是否支持ROCm，AMD显卡
Do you wish to build TensorFlow with ROCm support?
n
No ROCm support will be enabled for TensorFlow.

#是否支持CUDA，NVIDIA显卡
Do you wish to build TensorFlow with CUDA support?
n
No CUDA support will be enabled for TensorFlow.

#是否下载clang编译器
Do you wish to download a fresh release of clang?
n
Clang will not be downloaded.

#是否支持MPI(Message-Passing-Interface/消息传递接口)
Do you wish to build TensorFlow with MPI support? 
n
No MPI support will be enabled for TensorFlow.

#使用bazel编译时可以指定优化参数，(如添加GPU支持，添加额外的CPU指令集支持)
Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native -Wno-sign-compare]
enter

#是否为Android配置
Would you like to interactively configure ./WORKSPACE for Android builds? 
n
Not configuring the WORKSPACE for Android builds.

#配置完成
Preconfigured Bazel build configs. You can use any of the below by adding "--config=<>" to your build command. See .bazelrc for more details.
	--config=mkl         	# Build with MKL support.
	--config=monolithic  	# Config for mostly static monolithic build.
	--config=gdr         	# Build with GDR support.
	--config=verbs       	# Build with libverbs support.
	--config=ngraph      	# Build with Intel nGraph support.
	--config=dynamic_kernels	# (Experimental) Build kernels into separate shared objects.
Preconfigured Bazel build configs to DISABLE default on features:
	--config=noaws       	# Disable AWS S3 filesystem support.
	--config=nogcp       	# Disable GCP support.
	--config=nohdfs      	# Disable HDFS support.
	--config=noignite    	# Disable Apacha Ignite support.
	--config=nokafka     	# Disable Apache Kafka support.
	--config=nonccl      	# Disable NVIDIA NCCL support.
Configuration finished
```

3>编译源码

默认cpu编译方式:
```
bazel build --config=opt //tensorflow/tools/pip_package:build_pip_package
#bazel build -c opt //tensorflow/tools/pip_package:build_pip_package
```

查看cpu支持的指令集

```
#执行
cat /proc/cpuinfo
#有如下结果，可以看到sse3,sse4.1,sse4.2,avx,avx2,fma等信息
...
flags: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc rep_good nopl eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase bmi1 hle avx2 smep bmi2 erms invpcid rtm rdseed adx smap xsaveopt
...
```

添加cpu支持指令集编译
```
bazel build -c opt --copt=-msse3 --copt=-msse4.1 --copt=-msse4.2 --copt=-mavx --copt=-mavx2 --copt=-mfma //tensorflow/tools/pip_package:build_pip_package
```

我安装事实证明，默认编译方式，也会根据当前CPU的指令集，默认添加支持。之前使用安装包安装的方式，运行tensorflow会有如下警告，使用默认方式安装完后也不再由此警告。故CPU安装推荐先使用默认方式。

```
Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
```

漫长的等待，成功。

```
INFO: Elapsed time: 4143.941s, Critical Path: 236.75s
INFO: 8105 processes: 8105 local.
INFO: Build completed successfully, 8836 total actions
```

4>生成安装文件

生成whl文件，只有后面路径分隔有个空格。

```
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

出现如下，成功。

```
Thu Dec 6 17:48:03 CST 2018 : === Output wheel file is in: /tmp/tensorflow_pkg
```

轮子好了，可以安装了。

```
cd /tmp/tensorflow_pkg/ && ls
tensorflow-1.12.0rc0-cp36-cp36m-linux_x86_64.whl
```





