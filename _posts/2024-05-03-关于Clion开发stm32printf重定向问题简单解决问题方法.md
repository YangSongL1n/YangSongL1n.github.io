---
title: 关于Clion开发stm32printf重定向问题简单解决问题方法
tags:
  - STM32
  - Clion
---
[参考来源1](https://zhuanlan.zhihu.com/p/145801160)
这是另一种方法  
在printf 重定向的基础上加上 一句
 
setbuf(stdout,NULL);


[参考来源2](https://blog.csdn.net/weixin_53473434/article/details/132423960)
![20240503115747](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/20240503115747.png#pic_center)

