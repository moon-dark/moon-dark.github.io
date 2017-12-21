---
title: 安装TensorFlow
date: 2017-12-18 16:32:41
tags:
  - code
  - 机器学习
---

### [TensorFlow](https://www.tensorflow.org/)
### [TensorFlow 官方文档中文版](http://wiki.jikexueyuan.com/project/tensorflow-zh/)
##### An open-source software library for Machine Intelligence

### [在Ubuntu上安装TensorFlow](https://www.tensorflow.org/install/)
支持以下64位笔记本/台式机系统：
```
MacOS X 10.11 (El Capitan) or later.
Ubuntu 14.04 or later
Windows 7 or later.
```

版本选择：<br/>
只支持CPU：系统没有英伟达GPU（NVIDIA® GPU），只能安装这个版本。<br/>
支持GPU： TensorFlow程序在GPU上比CPU上执行更快。所以如果你有符合要求的NVIDIA® GPU，你应该安装这个版本。
<!-- more -->
##### 英伟达GPU需要的软件
```
CUDA® Toolkit 8.0.
The NVIDIA drivers associated with CUDA Toolkit 8.0.
cuDNN v6.0.
GPU card with CUDA Compute Capability 3.0 or higher.
The libcupti-dev library：
```
[NVIDIA's documentation](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4VZnqTJ2A)<br/>
The libcupti-dev library安装
```
$ sudo apt-get install libcupti-dev
```

##### 选择安装机制
```
virtualenv
"native" pip
Docker
Anaconda
installing from sources, which is documented in a separate guide.
```
###### 原生pip安装
需要：Python 2.7或Python 3.4+
安装pip
```
$ sudo apt-get install python-pip python-dev   # for Python 2.7
$ sudo apt-get install python3-pip python3-dev # for Python 3.n
```
查看pip版本
```
pip -V or pip3 -V
```
安装TensorFlow
```
$ pip install tensorflow      # Python 2.7; CPU support (no GPU support)
 $ pip3 install tensorflow     # Python 3.n; CPU support (no GPU support)
 $ pip install tensorflow-gpu  # Python 2.7;  GPU support
 $ pip3 install tensorflow-gpu # Python 3.n; GPU support
```
卸载TensorFlow
```
$ sudo pip uninstall tensorflow  # for Python 2.7
$ sudo pip3 uninstall tensorflow # for Python 3.n
```
