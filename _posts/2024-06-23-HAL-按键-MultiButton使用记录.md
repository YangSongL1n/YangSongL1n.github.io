---
title: HAL-按键-MultiButton使用记录
tags:
  - STM32
  - Hal
  - Cubemax
---
[toc]

# &ensp;&ensp;HAL-按键-MultiButton使用记录

* MultiButton 是一个开源的轻量的按键判断的函数 如下使该函数的开源链接：

  [0x1abin/MultiButton: Button driver for embedded system (github.com)](https://github.com/0x1abin/MultiButton)

  如何使用这个库里面已经是写的很清楚了


  | 事件             | 说明                                 |
  | ---------------- | ------------------------------------ |
  | PRESS_DOWN       | 按键按下，每次按下都触发             |
  | PRESS_UP         | 按键弹起，每次松开都触发             |
  | PRESS_REPEAT     | 重复按下触发，变量repeat计数连击次数 |
  | SINGLE_CLICK     | 单击按键事件                         |
  | DOUBLE_CLICK     | 双击按键事件                         |
  | LONG_PRESS_START | 达到长按时间阈值时触发一次           |
  | LONG_PRESS_HOLD  | 长按期间一直触发                     |

  本人自己再封装了一遍两个按键

  `button.c`文件


  ```C
  #include "button.h"

  enum Button_IDs
  {
  	start_key,
  	Estop_key,
  };

  struct Button btn1;
  struct Button btn2;
  static void BTN1_PRESS_DOWN_Handler(void *btn)
  {
  	 printf("BTN1_PRESS_DOWN_Handler\r\n");
  }

  static void BTN2_PRESS_DOWN_Handler(void *btn)
  {
     printf("BTN2_PRESS_DOWN_Handler\r\n");
  }

  static uint8_t read_button_GPIO(uint8_t button_id)
  {
  	// you can share the GPIO read function with multiple Buttons
  	switch (button_id)
  	{
  	case start_key:
  		return HAL_GPIO_ReadPin(start_GPIO_Port, start_Pin);
  	case Estop_key: 
  		return HAL_GPIO_ReadPin(Estop_GPIO_Port, Estop_Pin);
  	default:
  		return 0;
  	}
  }
  // 按键注册
  void button_register()
  {  // 按键初始化
  	button_init(&btn1, read_button_GPIO, 0, start_key);
    button_init(&btn2, read_button_GPIO, 0, Estop_key);
     // 绑定到回调函数
    button_attach(&btn1, SINGLE_CLICK, BTN1_PRESS_DOWN_Handler);
  	button_attach(&btn2, SINGLE_CLICK, BTN2_PRESS_DOWN_Handler);

  	button_start(&btn1);
  	button_start(&btn2);
  }

  ```

  `button.h`文件

  ```C
  #ifndef __BUTTON_H_
  #define __BUTTON_H_

  #include "global.h"

  #include "multi_button.h"

  void button_register();

  #endif
  ```

#### 值得注意的是注意源函数里面有个类似“心跳“的东西

void button_ticks(void);

```

你得5ms执行一下

可以放while主循环里面 

```C
while (1)
 {
    button_ticks();
    HAL_Delay(5);
 }
```

也可以放在定时器中断里面

```C
  void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
  {
    //5ms
    if (htim->Instance == TIM7) //
    {
       button_ticks();
    }
  }
```

还有一点就是需要再执行主函数之前把按键注册什么函数初始化一遍

---

### 往期内容

##### 基础库

[01VScode开发stm32环境搭建](https://blog.csdn.net/xiaodududao/article/details/136256152?spm=1001.2014.3001.5501)

[02点亮一个LED](https://blog.csdn.net/xiaodududao/article/details/136317917?spm=1001.2014.3001.5501)

[03按键控制LED](https://blog.csdn.net/xiaodududao/article/details/136410063)

##### HAL+ cubemax + VSCODE + GCC

[HAL PWM 配置 占空比 频率](https://blog.csdn.net/xiaodududao/article/details/138500109)

[HAL-DMA中断空闲接受不定长数据](https://blog.csdn.net/xiaodududao/article/details/139830816)
