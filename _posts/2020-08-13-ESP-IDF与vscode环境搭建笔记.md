---
title: ESP-idf 与vscode环境搭建笔记
tags:
  - Esp32
  - 开发环境
---

 

#### 关于ESP-idf 开发 esp开发板 确实有点难度

[VScode+ESP32快速安装ESP-IDF插件_esp32插件-CSDN博客](https://blog.csdn.net/hwd00001/article/details/125994389)

笔记录下这次的环境搭建过程以免以后再出问题了自己和别人还有地方可以借鉴和参考
在我看来就是VScode里面安装一个乐鑫官方的插件然后一步步走完就好了


https://zhuanlan.zhihu.com/p/630698425

还有一个问题没有解决

就是包含文件爆红的问题

之前好像可以设置的是什么我忘记了danm

[ESP-IDF + Vscode ESP32 开发环境搭建以及开发入门 - 古月居 (guyuehome.com)](https://www.guyuehome.com/44770)

就是这个   `ESP-IDF:Add vscode configuration folder`

![image-20231111211710593](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/image-20231111211710593.png)

完成对IDF 工具链的搭建  但是我的这个为啥还是爆红

算了尼玛老子不管了

![image-20231111224715999](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/image-20231111224715999.png)

能跑就行！

我去到处爆红问题解决了

![image-20231123000434789](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/image-20231123000434789.png)

多加一句就行

 "compileCommands":"${workspaceFolder}/build/compile_commands.json",

得来全不费工夫啊

参考链接：

[手把手vscode移植ESP32 IDF(附：解决头文件报错)_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV19y4y1P7Er/?spm_id_from=333.788.recommend_more_video.3&vd_source=10f897775825a5c2734444dd7eb2464c)