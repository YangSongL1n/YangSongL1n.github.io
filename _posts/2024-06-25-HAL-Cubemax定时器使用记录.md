---
title: HAL-Cubemax定时器使用记录
tags:
  - STM32
  - Hal
  - Cubemax
---
[toc]

# &ensp;&ensp;HAL-Cubemax定时器使用记录

---
&ensp;&ensp;我们有些程序在执行或者和下位几交互的过程中可能会出现下位机硬性规定需要等待多少秒的情况然而我们的程序又必须得执行下去，这样会造成单片机大量资源出现过度浪费的情况解决这种问题的办法有很多。

####         分享一种思路

##### 1.创建一个ms(毫秒)级延时中断 

##### 2.创建计数的变量 

##### 3.在需要延时的函数中对变量阈值进行判断

##### 4.验证实例--完整使用记录代码

Cubemax创建一个ms定时器很简单这里不赘述附上参考链接：

[STM32CubeMX学习笔记（5）——基本定时器接口使用](https://leung-manwah.blog.csdn.net/article/details/112599813)

 `uint32_t time = 0;`

`extern  uint32_t time;`

```C
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)

{
 if(htim->Instance == TIM7)  // 定时器6基地址

 {

​    // 自定义应用程序

​    time++;      // 每1ms进来1次

 }

}


```

业务逻辑

```
 while (1)

 {

  /* USER CODE END WHILE */

   

  /* USER CODE BEGIN 3 */

​    if(time == 1000)  // 每1秒LED灯翻转一次

​    {

​      HAL_GPIO_TogglePin(LED_G_GPIO_Port,LED_G_Pin);

​      time = 0;

​    }

 }
```

这样就能做到每秒触发一次少占用资源了

## 问题

 `if(time == 1000)` 

这里我们是让它到达1000ms时 就触发业务函数 如若单业务逻辑比较多时 

此时到达`1000ms` 时 `if` 来不及判断 那么  这个函数永远没有执行 那么 这种情况如何处理 ？

如果是换成`if(time >= 1000)` 

又那么超过了多少个ms呢？


### 往期内容

##### 基础库

[01VScode开发stm32环境搭建](https://blog.csdn.net/xiaodududao/article/details/136256152?spm=1001.2014.3001.5501)

[02点亮一个LED](https://blog.csdn.net/xiaodududao/article/details/136317917?spm=1001.2014.3001.5501)

[03按键控制LED](https://blog.csdn.net/xiaodududao/article/details/136410063)

##### HAL+ cubemax + VSCODE + GCC

[HAL PWM 配置 占空比 频率](https://blog.csdn.net/xiaodududao/article/details/138500109)

[HAL-DMA中断空闲接受不定长数据](https://blog.csdn.net/xiaodududao/article/details/139830816)

[HAL-按键-MultiButton使用记录](https://blog.csdn.net/xiaodududao/article/details/139879692)
