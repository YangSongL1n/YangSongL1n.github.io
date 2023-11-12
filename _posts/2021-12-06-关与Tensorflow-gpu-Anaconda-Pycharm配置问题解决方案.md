---
title: 关与Tensorflow-gpu Anaconda Pycharm配置问题解决方案
tags:
  - 人工智能
  - 开发环境
---

## 如何真真正正算配好tensorflow
TensorFlow-gpu安装时要与CUDA、CUDNN版本对应。
请参考网址：[https://tensorflow.google.cn/install/source_windows](https://tensorflow.google.cn/install/source_windows)
[参考文档](https://yangsongl1n.github.io/2021/12/06/%E5%85%B3%E4%B8%8ETensorflow-gpu-Anaconda-Pycharm%E9%85%8D%E7%BD%AE%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)
明确四个东西：
1.需要安装的tensorflow-gpu的版本。
2.要安装的tesorflow-gpu对应的cuda版本。
3.要安装的tesorflow-gpu对应的cudnn版本。
4.要安装的tesorflow-gpu对应的python版本。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6efb94930bd94552b473f64667223659.png)


准备材料 ：
Anaconda(版本不限 能用就行)
pycharm 
第一步 win +r 打开电脑cmd   输入 nvidia-smi   先查看 cuda 支持版本  或者是 
控制面版查看不赘述了
  如果没有控制面板，可以去Windows自带的微软商店下载安装，如果无法安装成功，考虑原电脑显卡驱动有问        题，考虑通过下载驱动精灵更新显卡驱动之后再进行安装
安装tensorflow-gpu时需要根据自己电脑已有的cuda版本进行命令的选择，否则会导致tensorflow，cuda不可用（命令行显示的cuda版本为此驱动可支持的最大版本的cuda，如果需要其他版本的cuda可以自行安装）。
如果想提升最大可兼容cuda版本，需要更新显卡驱动。
![在这里插入图片描述](https://img-blog.csdnimg.cn/d882a792d19b4396913b52cfdd2135e4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
记下来在你电脑上的  **CUDA Version**

## 第二步

在开始菜单里面打开Anaconda  Prompt 创建一个只个tensorflow 用的 even 
![在这里插入图片描述](https://img-blog.csdnimg.cn/6f601ab2aba942c693f2b83a47fd5edb.png)
选这个就可以 我的cuda版本在11.4  够用 cuda 10.1 然后对应的cudnn 是 7.6
![在这里插入图片描述](https://img-blog.csdnimg.cn/16a80353126c40a79d7fd6affe5e14fe.png)

## 在Anaconda中创建虚拟环境

```shell
conda create -n tensor2.1 python=3.6
```
删除环境
```shell
conda remove -n tensor2.1 --all# 删除
conda remove -n env_name --all
```
查看env

```python
conda env list
```
这里环境就配好了 3.6 的python 
![在这里插入图片描述](https://img-blog.csdnimg.cn/d98f67c0da4b441d93bb1d92c2763b42.png)

## 进到虚拟环境

```python
conda activate tensor2.1
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/081b75e6491d4fdd9f293394884eb93d.png)
## 安装指定 版本的TensorFlow
```python
pip3 install tensorflow-gpu==2.1.0
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/10eeaf4caf0f4179b383981e1ea09f69.png)![在这里插入图片描述](https://img-blog.csdnimg.cn/d12a7a99480b446b9402fe279ce30f0b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
有了然后再给他装上够用 cuda 10.1 然后对应的cudnn 是 7.6
如果已经安装好anaconda的同学，这部分执行出错，可能是channel的问题，可以参考文章[https://blog.csdn.net/weixin_29015051/article/details/112118017](https://blog.csdn.net/weixin_29015051/article/details/112118017)
二：换源
命令行执行：
```python
conda config --set show_channel_urls yes
```
在当前windos用户目录下生成生成.condarc，尽管有说安装anaconda后会自动生成，但是我查看了一下，没有，必须执行这个命令生成一下
命令行执行：
添加如下内容到该文件：
```python
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.bfsu.edu.cn/anaconda/pkgs/main
  - https://mirrors.bfsu.edu.cn/anaconda/pkgs/r
  - https://mirrors.bfsu.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.bfsu.edu.cn/anaconda/cloud
  msys2: https://mirrors.bfsu.edu.cn/anaconda/cloud
  bioconda: https://mirrors.bfsu.edu.cn/anaconda/cloud
  menpo: https://mirrors.bfsu.edu.cn/anaconda/cloud
  pytorch: https://mirrors.bfsu.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.bfsu.edu.cn/anaconda/cloud
```
更新anaconda

```python
conda upgrade --all
```
基本操作
没有指明包含chanel名称的情况下，均在main的频道中寻找指定操作的包，如果指定chanel-name，则在main频道中找不到时，会去指定的chanel-name中找。

安装包：

```powershell
conda install [-n env-name]包名[=版本号] [包名[=版本号]] [-c chanel-name]
```
例如：

```powershell
conda install cudatoolkit=11.2 cudnn -c conda-forge
```
给当前虚拟环境中安装cudatoolkits11.2和对应的cudann，在main和conda-forge频道中寻找要安装的软件包。
```powershell
conda install -n env-name cudatoolkit=11.2 cudnn -c conda-forge 
```
给虚拟环境env-name中安装cudatoolkits11.2和对应的cudann，在main和conda-forge频道中寻找要安装的软件包
卸载包：
conda remove [-n env-name] 包名 
不指定环境名，卸载当前环境的。
更新包：
```powershell
conda upgrade [-n env-name] 包名
```
不指定环境名更新当前环境的
全部更新：
```powershell
conda upgrade [-n env-name] --all

不指定环境名更新当前环境的所有包
```
搜索包：

```powershell
conda search 包名包含的字符 [-c chanel-name]
```

默认在main频道中找，如果寻找不到，则去附加的频道中查找。
虚拟开发环境管理
创建环境同时安装指定的包：

```powershell
conda create -n env_name [包名=版本 [包名=版本] [......]] [-c CHANELNAME]
```

激活环境：

```powershell
conda activate env_name
```

离开环境：

```powershell
conda deactivate
```

列出所有的虚拟环境：

```powershell
conda env list
conda info -e
```

删除虚拟环境：

```powershell
conda remove -n 环境名 --all
```

环境重命名--分两步：

```powershell
conda create -n old-env-name --clone new-env-name
conda remove -n old-env-name --all
```

更新指定环境指定包：

```powershell
conda update -n 环境名 包名
```

更新指定环境的所有包：

```powershell
conda update -n 环境名 --all
```

一个小例子，建立一个虚拟环境testenv，Python版本位3.7.3，并安装numpy 1.17 和pandas 1.10 和 scikit-learn 0.23.0
代码如下：

```powershell
conda create -n testnv python=3.7.3 numpy=1.17 scikit-learn=0.24
创建一个安装mindspore的开发环境，cuda为10.1 和对应的cudnn,使用附加的conda-forge的频道。
conda create -n mindspore python=3.7.5 cudatoolkit=10.1 cudnn -c conda-forge
```
```shell
conda config --add channels conda-forge
conda upgrade --all
conda install cudatoolkit=10.1 cudnn -c conda-forge
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2595a86d27674b459f60aebe84467d28.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
看看好没有

```python
import tensorflow as tf
tf.test.is_gpu_available()
```
成功 true
![在这里插入图片描述](https://img-blog.csdnimg.cn/c8b8f15f68ae4d588ec00574dbfa9e79.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)

## 然后再稍微配置一下pycharm
![在这里插入图片描述](https://img-blog.csdnimg.cn/f35798c58d754771885eea3085c5de2c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7dbdfa8a17a42b68544171f58269ad5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/ecf8a994775e4b93b0712ad40fbbb80c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/c95aae83fd27469881e61a5ac4b4e713.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)
要是它自己没有找到的话 你就自己再选出这个![在这里插入图片描述](https://img-blog.csdnimg.cn/936daf706d6d41f2a4b23498aaa31de5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/5666ec7bb64740be8462aa9514ed83ae.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)![在这里插入图片描述](https://img-blog.csdnimg.cn/b84fcfd0e5e04badb25fe4ddd0f18d9b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)

## 这样就好了？你在跑demo 是肯定会出现tensor 与 keras 什么不对应的

![在这里插入图片描述](https://img-blog.csdnimg.cn/54ba6804b8794d71ba6a92dd93d73ffa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcHlhbmd0aG9u,size_20,color_FFFFFF,t_70,g_se,x_16)

`
记2022年5月16日再装配tensorflow-gpu
`

![在这里插入图片描述](https://img-blog.csdnimg.cn/f557affd6d2846048fa985bb22cc1cd4.png)

<a target="_blank" href="http://mail.qq.com/cgi-bin/qm_share?t=qm_mailme&email=AHlhbmdzb25nMWluQHFxLmNvbQ" style="text-decoration:none;"><img src="http://rescdn.qqmail.com/zh_CN/htmledition/images/function/qm_open/ico_mailme_02.png"/></a>

`
记2023年11月05日再装配
`

# 都是垃圾给我去用vscode 

# vscode 配置也是一样得效果还比 python启动快 ！！！！