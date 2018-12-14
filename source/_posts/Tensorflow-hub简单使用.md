---
title: Tensorflow-hub简单使用
date: 2018-12-01 18:13:31
tags: 机器学习
toc: true
---


利用tensorflow-hub工具，在谷歌训练模型上进行迁移学习。


<!--more-->


### 一、说明和文件

tensorflow代码仓库：https://github.com/tensorflow/tensorflow
hub代码仓库：https://github.com/tensorflow/hub
hub迁移学习官网介绍（需要梯子）：https://www.tensorflow.org/hub/tutorials/image_retraining#visualizing_the_retraining_with_tensorboard
预训练模型说明：https://github.com/tensorflow/models/tree/master/research/slim
测试图片集下载（需要梯子）：http://download.tensorflow.org/example_images/flower_photos.tgz
Inception V3在线（需要梯子）:
https://tfhub.dev/google/inaturalist/inception_v3/feature_vector/1
Inception V3模型本地下载（需要梯子）：
https://tfhub.dev/google/inaturalist/inception_v3/feature_vector/1?tf-hub-format=compressed

### 二、tensorflow-hub安装

```
source activate py36
pip install tensorflow-hub
```


### 三、训练模型

1.下载py文件retrain.py
https://github.com/tensorflow/hub/blob/master/examples/image_retraining/retrain.py

2.最简单的方式训练

```
#--image_dir指定待训练图片集路径
#默认使用在线的Inception V3模型
#其他默认参数可查看源码
python ./retrain.py --image_dir ~/flower_photos
```

3.查看py脚本完整参数

```
python ./retrain.py -h
```

4.较完整参数实例

```
#--image_dir 指定待训练图片集路径
#--saved_model_dir 保存模型路径，不要创建model文件夹
#--bottleneck_dir 保存bottleneck文件路径，不必创建bottleneck文件夹
#--how_many_training_steps 训练多少步
#--output_labels 标签文件存放路径，必须创建output文件夹
#--output_graph pb模型存放路径
#--tfhub_module 预训练模型路径，可指定在线或本地路径 
#./premodels 为本地下载解压好的v3模型存放路径
python ./retrain.py --image_dir /app/files/images/flower_photos/ --saved_model_dir ./files/flower/model/ --bottleneck_dir ./files/flower/bottleneck/ --how_many_training_steps 20 --output_labels ./files/flower/output/output_labels.txt --output_graph ./files/flower/output/retrain.pb --tfhub_module ./premodels
```

5.运行关键画面：

```
INFO:tensorflow:Looking for images in 'daisy'
INFO:tensorflow:Looking for images in 'dandelion'
INFO:tensorflow:Looking for images in 'roses'
INFO:tensorflow:Looking for images in 'sunflowers'
INFO:tensorflow:Looking for images in 'tulips'
......
INFO:tensorflow:Creating bottleneck at ./files/flower/bottleneck/tulips/3524204544_7233737b4f_m.jpg_.~premodels.txt
INFO:tensorflow:Creating bottleneck at ./files/flower/bottleneck/tulips/17159349572_c0c51599f7_n.jpg_.~premodels.txt
......
INFO:tensorflow:2018-12-03 16:24:39.526751: Step 0: Train accuracy = 49.0%
INFO:tensorflow:2018-12-03 16:24:39.527079: Step 0: Cross entropy = 1.552117
INFO:tensorflow:2018-12-03 16:24:40.128679: Step 0: Validation accuracy = 31.0% (N=100)
INFO:tensorflow:2018-12-03 16:24:41.270725: Step 10: Train accuracy = 79.0%
INFO:tensorflow:2018-12-03 16:24:41.270955: Step 10: Cross entropy = 1.187036
INFO:tensorflow:2018-12-03 16:24:41.404452: Step 10: Validation accuracy = 84.0% (N=100)
INFO:tensorflow:2018-12-03 16:24:42.413912: Step 19: Train accuracy = 98.0%
INFO:tensorflow:2018-12-03 16:24:42.414116: Step 19: Cross entropy = 0.916541
INFO:tensorflow:2018-12-03 16:24:42.515238: Step 19: Validation accuracy = 89.0% (N=100)
......
INFO:tensorflow:Final test accuracy = 92.1% (N=340)
......
```

### 四、测试模型

1.下载测试py文件label_image.py
https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/label_image/label_image.py

2.可将文件放到retrain.py同目录下
```
#--graph pb文件路径
#--labels 标签文件路径
#--input_layer 输入层
#--output_layer 输出层
#--image 测试图片
python ./label_image.py --graph ./files/flower/output/retrain.pb --labels ./files/flower/output/output_labels.txt --input_layer=Placeholder --output_layer=final_result --image /app/files/images/test/daisy.jpg
```

3.运行关键画面：

```
.....
daisy 0.44550425
roses 0.15574977
tulips 0.14537261
dandelion 0.12802716
sunflowers 0.12534623
```


